---
title: "Clean Up"
date: 2026-11-25
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

When you finish the workshop, delete everything in the order below so nothing keeps billing. Steps 1–6 follow the clean-up guide in the project repository (`infra/README.md`, section 7, rewritten in pull request #186 for task SH-10). Step 7 and the extra items in steps 5 and 6 cover resources created in other sections of this workshop.

The commands are for stage `dev` in `ap-southeast-1`, run from the repository root in Bash (on Windows use Git Bash or WSL). Change `STAGE` if you deployed another stage.

```bash
STAGE=dev
export AWS_REGION=ap-southeast-1
```

{{% notice warning %}}
**Steps 3, 5 and 6 cannot be undone.** Step 3 deletes every file in the bucket (models and donated images). Step 5 deletes the DynamoDB table `askfirst-$STAGE` with all user data, and its PITR backups go with it. Step 6 deletes the bot token parameter and all logs. Back up anything you want to keep first.
{{% /notice %}}

#### Step 1: Remove the Telegram webhook

Stop Telegram from sending updates to the Function URL before the function is deleted:

```bash
python scripts/set_webhook.py --delete   # reads TELEGRAM_BOT_TOKEN from the environment or .env
```

Check: the script prints a successful `deleteWebhook` result, and the bot's `getWebhookInfo` shows an empty `url`.

#### Step 2: Remove provisioned concurrency (only if you turned it on)

The template does **not** enable provisioned concurrency. If you added it yourself, remove it:

```bash
for f in webhook worker ai; do
  aws lambda list-provisioned-concurrency-configs --function-name askfirst-$STAGE-$f \
    --query 'ProvisionedConcurrencyConfigs[].FunctionArn' --output text 2>/dev/null
done
# For every line printed, delete the config on that alias, for example:
aws lambda delete-provisioned-concurrency-config --function-name askfirst-$STAGE-ai --qualifier live
```

Check: running the loop again prints nothing.

#### Step 3: Empty the S3 bucket

CloudFormation cannot delete a bucket that still has objects. The stack's bucket does not have versioning, but if you turned it on you must also delete old versions:

```bash
BUCKET=$(aws ssm get-parameter --name "/askfirst/$STAGE/s3/bucket_name" --query Parameter.Value --output text)
aws s3 rm "s3://$BUCKET" --recursive
# Only if versioning was enabled: delete every version and delete marker
aws s3api delete-objects --bucket "$BUCKET" --delete "$(aws s3api list-object-versions --bucket "$BUCKET" \
  --query '{Objects: [Versions, DeleteMarkers][][].{Key: Key, VersionId: VersionId}}' --output json)"
```

Check: `aws s3api list-object-versions --bucket "$BUCKET" --max-items 1` shows no `Versions` or `DeleteMarkers`. The bucket itself is deleted in step 5.

#### Step 4: Delete the ECR images

The repository `askfirst-ai` belongs to the stack `askfirst-github-oidc` and has `EmptyOnDelete: true`, so deleting the stack deletes the images too:

```bash
aws cloudformation delete-stack --stack-name askfirst-github-oidc
aws cloudformation wait stack-delete-complete --stack-name askfirst-github-oidc
```

Check: `aws ecr describe-repositories --repository-names askfirst-ai` returns `RepositoryNotFoundException`. If another stack in the account shares the GitHub OIDC provider, review this stack before deleting it.

#### Step 5: Delete the SAM stack

If you gave the `ai` function access to Bedrock in another account (`infra/README.md`, section 8.4), remove the inline policy you added by hand to its role first. CloudFormation did not create that policy:

```bash
aws iam delete-role-policy --role-name <AI_LAMBDA_ROLE> --policy-name assume-bedrock
```

Then delete the stack:

```bash
sam delete --stack-name askfirst-$STAGE
```

Answer `y` when SAM asks to delete the stack and its artifacts. This removes the Lambda functions `webhook`, `worker`, `worker-dlq` and `ai`, the DynamoDB table, both SQS queues, the bucket, the alarm, the SSM parameters created by the stack, and this stack's deploy artifacts in the `aws-sam-cli-managed-default` bucket.

Check: `aws cloudformation describe-stacks --stack-name askfirst-$STAGE` reports `does not exist`.

#### Step 6: Delete what the stack does not manage

```bash
# Log groups created by Lambda
for g in $(aws logs describe-log-groups --log-group-name-prefix "/aws/lambda/askfirst-$STAGE-" \
           --query 'logGroups[].logGroupName' --output text); do
  aws logs delete-log-group --log-group-name "$g"
done
# SSM parameters created by hand (bot token, webhook secret, policy_sha256...)
for n in $(aws ssm get-parameters-by-path --path "/askfirst/$STAGE" --recursive \
           --query 'Parameters[].Name' --output text); do
  aws ssm delete-parameter --name "$n"
done
```

Also check these:

- **Resources created by hand outside the stack**, for example an `ai` function deployed manually before `AiImageUri` was passed, or an ECR repository other than `askfirst-ai`. `sam delete` does not remove them. Find them with the final check below and delete them by hand (`aws lambda delete-function --function-name <name>`, `aws ecr delete-repository --repository-name <name> --force`). A manually created role for that function must be deleted too: remove its policies, then `aws iam delete-role --role-name <name>`.
- **SNS topic for the alarm** (only if you created it in 5.7.5):

  ```bash
  TOPIC_ARN=$(aws sns list-topics --query "Topics[?ends_with(TopicArn, ':askfirst-$STAGE-alarms')].TopicArn" --output text)
  aws sns delete-topic --topic-arn "$TOPIC_ARN"
  ```

- **AWS Budgets** (task BE-01) are free, so you can keep them. To delete one: `aws budgets describe-budgets --account-id <ACCOUNT_ID>` to get the name, then `aws budgets delete-budget --account-id <ACCOUNT_ID> --budget-name <name>`.
- **Bucket `aws-sam-cli-managed-default`** is created by `resolve_s3 = true` and shared by every SAM stack in the Region. If no SAM stack is left, empty that bucket and delete the stack `aws-sam-cli-managed-default`.
- **Bedrock Guardrail** (`ap-southeast-2`): only if you created it with `ai/scripts/create_guardrail.py`, delete it with `aws bedrock delete-guardrail --region ap-southeast-2 --guardrail-identifier <id>`. Run it with the profile of the account that owns the Guardrail.

#### Step 7: Clean up outside the team account

These items are not covered by PR #186.

- **Cross-account Bedrock role** (only if you followed `infra/README.md`, section 8.4). Run these with a profile for the account that holds Bedrock, not the team account:

  ```bash
  aws iam delete-role-policy --role-name askfirst-bedrock-from-team --policy-name bedrock --profile <BEDROCK_PROFILE>
  aws iam delete-role --role-name askfirst-bedrock-from-team --profile <BEDROCK_PROFILE>
  ```

- **GitHub repository variable** that points to the deleted OIDC role:

  ```bash
  gh variable delete AWS_AI_IMAGE_ROLE_ARN
  ```

- **Telegram bot token** (optional). If the bot will not be used again, revoke its token in BotFather (`/revoke`). Do this yourself in Telegram; never paste the token into a terminal log, a chat or this report.

#### Final check: no `askfirst-$STAGE` resources left

```bash
aws cloudformation describe-stacks --stack-name askfirst-$STAGE 2>&1 | grep -q 'does not exist' && echo "stack: deleted"
aws lambda list-functions --query "Functions[?starts_with(FunctionName, 'askfirst-$STAGE')].FunctionName" --output text
aws dynamodb list-tables --query "TableNames[?starts_with(@, 'askfirst-$STAGE')]" --output text
aws sqs list-queues --queue-name-prefix "askfirst-$STAGE" --query QueueUrls --output text
aws s3 ls | grep "askfirst-$STAGE"
aws logs describe-log-groups --log-group-name-prefix "/aws/lambda/askfirst-$STAGE-" --query 'logGroups[].logGroupName' --output text
aws ssm get-parameters-by-path --path "/askfirst/$STAGE" --recursive --query 'Parameters[].Name' --output text
```

The first command prints `stack: deleted`. The other six must print nothing (or `None`).

{{% notice note %}}
Look at **Billing and Cost Management → Bills** one or two days later. Usage from the last days before clean-up can still appear, but no new charges for `askfirst` should be added.
{{% /notice %}}

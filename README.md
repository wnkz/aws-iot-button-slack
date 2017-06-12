# aws-iot-button-slack

### Requirements

* An AWS account (duh?)
* An AWS IoT Button
* `awscli` properly configured
* A working [IoT Device Certificate][create-device-certificate]

## Quick Start

1. Create an [Incoming webhook][slack-incoming-webhooks] and encrypt the Webhook URL with KMS

  ```sh
  aws kms encrypt --key-id alias/mykey --plaintext "hooks.slack.com/services/XXXXXXXXX/XXXXXXXXX/xxxxxxxxxxxxxxxxxxxxxxxx" --output text --query CiphertextBlob
  ```

2. Gather required parameters

  * `CertificateARN`: The Amazon Resource Name (ARN) of the existing AWS IoT certificate.
  * `IoTButtonDSN`: The device serial number (DSN) of the AWS IoT Button. This can be found on the back of the button. The DSN must match the pattern of 'G030 XXXX XXXX XXXX'.
  * `KMSEncryptedHookUrl`: The KMS encrypted Slack incoming WebHook url from step 1.
  * `SlackChannel`: The Slack channel you want to post to (eg. #general @john)

3. Package and Deploy the Cloudformation template using `awscli`

  ```sh
  aws cloudformation package --template-file cfn.yml --output-template-file out.yml --s3-bucket mybucket
  ```

  ```sh
  aws cloudformation deploy --stack-name aws-iot-button-slack \
                            --capabilities CAPABILITY_IAM \
                            --template-file out.yml \
                            --parameter-overrides CertificateARN="${CertificateARN}" \
                                                  IoTButtonDSN="${IoTButtonDSN}" \
                                                  SlackChannel="${SlackChannel}" \
                                                  KMSEncryptedHookUrl="${KMSEncryptedHookUrl}"
  ```

[create-device-certificate]: https://docs.aws.amazon.com/iot/latest/developerguide/create-device-certificate.html
[slack-incoming-webhooks]: https://my.slack.com/apps/A0F7XDUAZ-incoming-webhooks

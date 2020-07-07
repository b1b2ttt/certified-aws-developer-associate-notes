# CLI: Command Line Interface

Add user credentials locally using this command:
- $ `aws configure`

If you are using multiple AWS accounts, you can add custom profiles with seperate credentials using this command:
- $ `aws configure --profile {my-other-aws-account}`
- if you you'd like to execute commands on a specific profile:
  - example: `aws s3 ls --profile {my-other-aws-account}`
- if you don't specify the aws profile, the commands will be executed to your **default** profile

#### AWS CLI on EC2
* IAM roles can be attached to EC2 instances
* IAM roles can come with a policy authorizing exactly what the EC2 instance should be able to do. This is the best practice.
* EC2 Instances can then use these profiles automatically without any additional configurations

#### AWS CLI Dry Runs
- Some AWS CLI commands(not all) cantain a `--dry-run` option to simulate API calls


#### CLI STS Decode Errors
- When you run API calls and they fail, you can get a long, encoded error message code 
- This error can be decoded using STS 
- run the command: `aws sts decode-authorization-message --encoded-message {encoded_message_code}`
- your IAM user must have the correct permissions to use this command by adding the `STS` service to your policy

#### AWS EC2 Instance Metadata
- It allows AWS EC2 instances to ”learn about themselves” without using an IAM Role for that purpose.
- The URL is http://169.254.169.254/latest/meta-data
- You can retrieve the IAM Role name from the metadata, but you CANNOT retrieve the IAM Policy.
- Metadata = Info about the EC2 instance
- Userdata = launch script of the EC2 instance

#### MFA(multi-factor authentication) with CLI
- To use MFA with the CLI, you must create a temporary session
- To do so, you must run the STS GetSessionToken API call
- aws sts get-session-token --serial-number arn-of-the-mfa-device --token-code code-from-token --duration-seconds 3600
- then configure a new profile `aws configure --profile mfa`

#### AWS CLI Credenyisld Provider Chain

1. Command line options – --region, --output, and --profile

2. Environment variables – AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, and AWS_SESSION_TOKEN

3. CLI credentials file –aws configure
~/.aws/credentials on Linux / Mac & C:\Users\user\.aws\credentials on Windows

4.CLI configuration file – aws configure
~/.aws/config on Linux / macOS & C:\Users\USERNAME\.aws\config on Windows

5.Container credentials – for ECS tasks

6.Instance profile credentials – for EC2 Instance Profiles



docker run --rm -p 4566:4566 floci/floci:latest

After starting floci in a different terminal start typing the commands

The AWS Command Line Interface (CLI) is **a unified tool used to manage your AWS services from a terminal**. It uses a standard syntax: `aws <service> <operation> [options]`. [[1](https://github.com/aws/aws-cli), [2](https://www.geeksforgeeks.org/devops/how-to-use-aws-ec2-ebs-and-s3-services-using-aws-cli/), [3](https://www.theknowledgeacademy.com/blog/what-is-aws-cli/), [4](https://www.youtube.com/watch?v=21-t7Rkokmo&t=76)]**🛠️ Core & Configuration Commands** Before running service-specific commands, you must configure your environment. [[1](https://www.youtube.com/watch?v=9oYd5KQM8AQ), [2](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html), [3](https://www.archerimagine.com/articles/aws/aws-cli-and-sdk-installation-tutorial.html)]  
• **`aws configure`**: Set up your Access Key, Secret Key, Region, and Output format.  
• **`aws --version`**: Verify your installation and check the current version.  
• **`aws help`**: Access built-in documentation for any service or command.  
• **`aws sts get-caller-identity`**: Verify which IAM user or role you are currently using. [[1](https://docs.aws.amazon.com/cli/latest/reference/sts/), [2](https://docs.aws.amazon.com/cli/v1/userguide/cli-chap-configure.html), [3](https://www.pump.co/guides/check-aws-cli-version), [4](https://www.geeksforgeeks.org/devops/how-to-use-aws-ec2-ebs-and-s3-services-using-aws-cli/), [5](https://github.com/aws/aws-cli), [6](https://www.youtube.com/watch?v=9oYd5KQM8AQ)]**📦 Amazon S3 (Storage) [[1](https://notes.kodekloud.com/docs/AWS-Certified-Developer-Associate/AWS-Fundamentals/S3-Basics/page)]**High-level commands for managing buckets and files. [[1](https://docs.aws.amazon.com/cli/latest/reference/s3/), [2](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)]  
• **`aws s3 ls`**: List all buckets or the contents of a specific bucket.  
• **`aws s3 mb s3://bucket-name`**: Create (Make) a new S3 bucket.  
• **`aws s3 rb s3://bucket-name --force`**: Remove a bucket and all its contents.  
• **`aws s3 cp local-file s3://bucket-name`**: Upload a file from your computer to S3.  
• **`aws s3 sync . s3://bucket-name`**: Synchronize a local directory with an S3 bucket. [[1](https://awsfundamentals.com/blog/aws-s3-ls), [2](https://docs.aws.amazon.com/cli/latest/reference/s3/), [3](https://www.geeksforgeeks.org/devops/how-to-use-aws-ec2-ebs-and-s3-services-using-aws-cli/), [4](https://sumanta9090.medium.com/the-ultimate-aws-cli-cheatsheet-for-developers-92af2221eb14), [5](https://www.datacamp.com/tutorial/aws-s3-sync)]**💻 Amazon EC2 (Compute) [[1](https://www.nakivo.com/blog/how-to-manage-aws-ec2-instances-via-aws-command-line-interface/), [2](https://docs.aws.amazon.com/cli/latest/reference/configservice/), [3](https://www.linkedin.com/learning/aws-for-developers-cli-tips)]**Commands for managing virtual servers and instances. [[1](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html), [2](https://www.geeksforgeeks.org/devops/how-to-use-aws-ec2-ebs-and-s3-services-using-aws-cli/)]  
• **`aws ec2 describe-instances`**: View details of all your EC2 instances.  
• **`aws ec2 start-instances --instance-ids <id>`**: Power on a stopped instance.  
• **`aws ec2 stop-instances --instance-ids <id>`**: Shutdown a running instance.  
• **`aws ec2 terminate-instances --instance-ids <id>`**: Permanently delete an instance.  
• **`aws ec2 describe-regions`**: List all available AWS regions. [[1](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html), [2](https://www.geeksforgeeks.org/devops/how-to-use-aws-ec2-ebs-and-s3-services-using-aws-cli/), [3](https://repost.aws/articles/ARkmirs-YtQUmKMA-jt2lhkA/availability-zone-az-migration-instance-upgrade-guide), [4](https://fedoramagazine.org/ssh-key-aws-regions/)]**👤 IAM (Identity & Access)**Manage users, groups, and permissions. [[1](https://docs.aws.amazon.com/cli/v1/userguide/cli_code_examples.html), [2](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)]  
• **`aws iam list-users`**: List all IAM users in your account.  
• **`aws iam create-user --user-name <name>`**: Add a new IAM user.  
• **`aws iam list-access-keys --user-name <name>`**: View access keys for a user. [[1](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html), [2](https://www.edureka.co/blog/aws-cli/)]**⚙️ Global Options [[1](https://docs.aws.amazon.com/cli/latest/reference/ssm/list-commands.html)]**These can be added to almost any command to modify behavior. [[1](https://docs.aws.amazon.com/cli/latest/reference/ssm/list-commands.html), [2](https://docs.aws.amazon.com/cli/v1/userguide/cli-configure-options.html)]  
• **`--profile <name>`**: Use a specific set of credentials from your config file.  
• **`--region <name>`**: Override the default region for a single command.  
• **`--output json|table|text`**: Change how the data is displayed in your terminal.  
• **`--query 'JSONPath'`**: Filter the output to show only specific fields.

## More Commands

### Login into main user

aws configure --profile kcbc
# aws-shared-bucket-example
Example CloudFormation stack for sharing a bucket to another AWS account for uploads.

## Walk-through
For the sake of the example, we have two accounts:

* Account A: Our account
* Account B: Another AWS account

In this example, we will consider the bucket owner's (Account A) full access to any file uploaded to the bucket as a requirement. To meet that requirement, we add [a condition to the bucket policy](cfn-template.yaml#L31) that requires Account B users to use the [canned ACL](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl) `bucket-owner-full-control` for any PUT.

To create this CloudFormation stack, use the [cfn-template.yaml template](cfn-template.yaml).

Then, for an Account B user to upload a file, they'll need to specify the canned ACL. E.g. using [`aws-cli`'s `cp` command](https://docs.aws.amazon.com/cli/latest/reference/s3/cp.html):

    aws s3 cp foo.txt s3://proj1-bucket-wkdngena98ww/foo.txt --acl bucket-owner-full-control

If the canned ACL is not provided, the user will receive an error.

## Caveats
* There must be an Account B policy that provides access to its users to PUT files in the bucket.
* As always, if there's an explicit DENY in an IAM policy or ACLs, that will overwrite the bucket policy we set. Refer to [AWS documentation](https://aws.amazon.com/blogs/security/iam-policies-and-bucket-policies-and-acls-oh-my-controlling-access-to-s3-resources/) for more details.
* This bucket policy will expose your bucket to PUTs by any user that is able to assume a role in Account B.


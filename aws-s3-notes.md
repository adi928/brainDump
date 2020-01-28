# AWS Access Controls

By identifying a number of different misconfigurations we discovered that we could suddenly control, monitor and break high end websites due to weak configurations of the bucket and object ACLs. - [1](https://labs.detectify.com/2017/07/13/a-deep-dive-into-aws-s3-access-controls-taking-full-control-over-your-assets/?utm_source=blog&utm_campaign=s3_buckets)

## Identification of Buckets

NoSuchBucket - Your search for the bucket should end there.
AllAccessDisabled - Buckets are completely dead.

- Depends on how the bucket is accessed. Request directly goes to
	- S3
	- CloudFront
	- S3 Static Website
- DNS entry for the domain might give S3 bucket name
- Google for the domain to see the history of the domain with a bucket name

## Permission and Predefined Groups

- AuthenticatedUsers - Anyone who has a valid AWS credential is included in this group (Most likely reason)
- AllUsers - Literally Anyone

## Standard Policy Permissions

- READ/READ_ACP - Can be different for each object, regardless of the setting on S3 bucket
	- `aws s3api get-object --bucket test-bucket --key read.txt read.txt`
	- `aws s3api get-object-acl --bucket test-bucket --key read-acp.txt`
- WRITE/WRITE_ACP - Write can be checked by adding new file. Never modify.
	- `aws s3api put-bucket-acl --bucket test-bucket --grant-full-control emailaddress=frans@example.com && echo "success"`
		- To take over the owner by overwriting the ACPs.

## Scenarios

- If bucket is on company's subdomain. Test for:
	- BUCKET/Object READ
	- BUCKET READ_ACP
	- BUCKET WRITE - Using invalid MD5 checksum hack [2](https://gist.github.com/adi928/3aecc60ec6bc0cecb67c61dadb0da564)
	- BUCKET WRITE_ACP - Very dangerous to test as you can disrupt an existing owner.
	- Possible Vulnerabilities might include stored XSS, RCE or Information Disclosure.
- If assets from buckets are used by the company:
	- BUCKET READ-ACP
	- BUCKET WRITE (By invalid-MD5 hack) [2](https://gist.github.com/adi928/3aecc60ec6bc0cecb67c61dadb0da564)
	- BUCKET WRITE-ACP
	- OBJECT WRITE-ACP
	- Same vulners as above.


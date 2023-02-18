# AWS

## Connect to a bucket

- If we found a key or buckets during enumeration, we need to check them and see if we can connect to them.

- `sudo apt install awscli` install aws cli
- `aws configure --profile targetname` create a profile for our target
- `aws s3 ls s3://target --profile targetname` Access to a bucket

## Resources

- [AWScli Documentation](https://aws.amazon.com/fr/cli/)
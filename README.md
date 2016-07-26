# S3 Easy Deploy

Node JS module and cli command for easily deploying to AWS S3.

## Installation

`npm install s3-easy-deploy`

## How it works

Simply uploads the entire contents of a dirctory to an S3 bucket. 
MD5 hash is used to determine if a file has changed so that only changed files are uploaded.
Also sets ACL to public read on all uploaded files.

## Usage

### Within node script

```
var s3EasyDeploy = require('s3-easy-deploy');

// with callback
s3EasyDeploy.deploy({
  profile: 'aws-profile',
  publicRoot: './release',
  bucket: 'magic-bucket-name'
}, function(error, result) {
  // done!
});

// with promise
s3EasyDeploy.deploy({
  profile: 'aws-profile',
  publicRoot: './release',
  bucket: 'magic-bucket-name'
}).then(function(result) {
  // done!
}, function(error) {
  // error!
});
```

#### `.deploy(...)` options

* `accessKeyId`: AWS access key id. Must be specified with `secretAccessKey`.
* `secretAccessKey`: AWS secret access key. Must be specified with `accessKeyId`.
* `profile`: The [AWS profile name](http://docs.aws.amazon.com/java-sdk/latest/developer-guide/setup-credentials.html) 
specified in ~/.aws/credentials. This can be specified instead of `accessKeyId` and `secretAccessKey`
* `region`: The S3 region to deploy to. Defaults to "us-east-1"
* `publicRoot`: The path to the directory you want to deploy to s3
* `bucket`: The s3 bucket name to deploy to
* `cloudFrontDistributionId`: (optional) The CloudFront distribution id to invalidate.
* `concurrentRequests`: The number of uploads to process concurrently. Defaults to 10.


### With comman `s3-easy-deploy`

`s3-easy-deploy --help`:

```
  Usage: s3-easy-deploy [options]

  Options:

    -h, --help                                  output usage information
    -V, --version                               output the version number
    -c, --config <configFile>                   The AWS accessKeyId
    --access-key-id <accessKeyId>               The AWS accessKeyId
    --secret-access-key <secretAccessKey>       The AWS secretAccessKey
    --profile <profile>                         The AWS profile saved in ~/.aws/credentials
    --region <region>                           The S3 region. Defaults to us-east-1
    --public-root <publicRoot>                  The path of the folder to deploy
    --bucket <bucket>                           The S3 bucket name
    --concurrent-requests <concurrentRequests>  The number of uploads to send at the same time. Defaults to 10
```

Example usage:
`s3-easy-deploy --access-key-id kjch84hg9shd --secret-access-key v3049g0jdge --public-root ./public --bucket magic-bucket-name`
or specify a config file
`s3-easy-deploy deploy-config.json`
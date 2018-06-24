# S3 Easy Deploy

Node JS module and cli command for easily deploying to AWS S3.

## Installation

`npm install s3-easy-deploy`

## How it works

Simply uploads the entire contents of a dirctory to an S3 bucket.
MD5 hash is used to determine if a file has changed so that only changed files are uploaded.
Also sets ACL to public read on all uploaded files.

## AWS Credentials

As per AWS docs, you can set your AWS credentials with environment variables
[`AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/loading-node-credentials-environment.html)
or [`AWS_PROFILE`](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/loading-node-credentials-shared.html).


## Usage

### In your `package.json`

Simply add the following to your `package.json` file and run `npm run deploy` to deploy.

```
{
  ...
  "scfripts": {
    ...
    "deploy": "s3-easy-deploy --public-root ./dist --bucket magic-bucket-name"
  }
}
```

### Within node script

```
var s3EasyDeploy = require('s3-easy-deploy');

// with callback
s3EasyDeploy.deploy({
  publicRoot: './release',
  bucket: 'magic-bucket-name',
  acl: 'private'
}, function(error, result) {
  // done!
});

// with promise
s3EasyDeploy.deploy({
  publicRoot: './release',
  bucket: 'magic-bucket-name',
  acl: 'private'
}).then(function(result) {
  // done!
}, function(error) {
  // error!
});

// with additional headers and metadata
s3EasyDeploy.deploy({
  publicRoot: './release',
  bucket: 'magic-bucket-name',
  acl: 'private',
  putObjectParams: [
      {
          match: /^[^\/]+\.(js|css|html)$/,
          tags: { ContentEncoding: 'gzip' }
      }
  ],
  metadata: [
      {
          match: /^[^\/]+\.(js|css|html)$/,
          tags: { 'Build-Version': '1.0.1', 'Build-Timestamp': 442527840000 }
      }
  ]
}).then(function(result) {
  // done!
}, function(error) {
  // error!
});
```

#### `.deploy(...)` options

* `region`: The S3 region to deploy to. Defaults to "us-east-1"
* `publicRoot`: The path to the directory you want to deploy to s3
* `bucket`: The s3 bucket name to deploy to
* `acl`: (optional) [Canned s3 policy](http://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html) to use (e.g. 'private', 'public-read'). Defaults to "public-read".
* `cloudFrontId`: (optional) The CloudFront distribution id to invalidate.
* `concurrentRequests`: The number of uploads to process concurrently. Defaults to 10.
* `putObjectParams`: (optional) Additional params to pass to [S3 putObject](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#putObject-property) call (e.g. headers to set on uploaded files) ([{ match: RegExp, tags: { key: value } }])
* `metadata`: (optional) Additional metadata to set ([{ match: RegExp, tags: { key: value } }])


### With command `s3-easy-deploy`

`s3-easy-deploy --help`:

```
  Usage: s3-easy-deploy [options]

  Options:

    -h, --help                                  output usage information
    -V, --version                               output the version number
    -c, --config <configFile>                   A config file
    --region <region>                           The S3 region. Defaults to us-east-1
    --public-root <publicRoot>                  The path of the folder to deploy
    --bucket <bucket>                           The S3 bucket name
    --acl <acl>                                 The ACL policy. Defaults to public-read
    --cloud-front-id <cloudFrontDistributionId> The CloudFront distribution id
    --concurrent-requests <concurrentRequests>  The number of uploads to send at the same time. Defaults to 10
```

Example usage:
`s3-easy-deploy --public-root ./public --bucket magic-bucket-name --acl public-read`
or specify a config file
`s3-easy-deploy deploy-config.json`

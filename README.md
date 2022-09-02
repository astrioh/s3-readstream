# s3-readstream
AWS S3 Read Stream made easy

Simple wrapper around [AWS S3 getObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html) call to grab a range of bytes allowing smooth and stable streaming!
* Simple interface for streaming any size file from AWS S3
* Easily speed-up, and slow down, the streaming at any point
* All of the functionaly you love with NodeJS Readable streams
* Drop in replacement for `AWS.S3.getObject().createReadStream()`



```
npm install s3-readstream
```

You can instantiate the `S3ReadStream` class like below:

```js
import {S3ReadStream} from 's3-readstream';
// Pass in your AWS S3 credentials
const s3 = new AWS.S3({
  accessKeyId: s3Env.accessKey,
  secretAccessKey: s3Env.secret
});
// Check the headobject like normal to get the length of the file
s3.headObject(bucketParams, (error, data) => {
    const options = {
        parameters: bucketParams,
        s3,
        maxLength: data.ContentLength,
        byteRange: 1024 * 1024 * 5 // 5 MiB
    };
    // Instantiate the S3ReadStream in place of s3.getObject().createReadStream()
    const stream = new S3ReadStream(options);
});
```
To adjust the speed of the stream:
```js
// You can adjust the speed at any point during the stream
stream.adjustByteRange(1024 * 1024 * 10); // 10 MiB
```

You can alse use this `S3ReadStream` like any other [NodeJS Readable stream](https://nodejs.org/api/stream.html#readable-streams), setting an event listener is exactly the same:
```js
stream.on('data', (chunk) => {
  console.log(`read: ${chunk.toString()}`);
});
stream.on('end', () => {
  console.log('end');
});
```
Use this with `zlib` to handle zipped files!
```js
import {createGunzip} from 'zlib';

const gzip = createGunzip();
// pipe into gzip to unzip files as you stream!
stream.pipe(gzip)
```

You can test this stream in an [example HD video app](https://github.com/about14sheep/awsstreaming) and read a [blog on its origins](https://dev.to/about14sheep/streaming-data-from-aws-s3-using-nodejs-stream-api-and-typescript-3dj0).

# vue-image-crop-upload

A mobile component used for cropping and uploading avatar;

## Install
#### npm
```shell
$ npm install vue-mobile-avatar-upload
```


## Usage
#### Props
| Name              | Type               | Default             | Description                                         |
| ----------------| ---------------- | ---------------| ------------------------------------------|
| action             | String            |  ''                | Server path, If empty, will not be uploaded    |
| maxSize       | Number   | 6000     | the maximum of image's size.    |
| language             | String            | en            | language,'zh'(chinese),'en'(english)   |

#### Events
| Name              | Description                                         |
| ----------------| ------------------------------------------|
| onBegin   | image begin upload    |
| onSuccess | upload success, params( response )    |
| onError    | upload fail, params( status )    |

# file_upload
## install package
```npm
$ npm install --save multer
```
## form
```html
<form action="/profile" method="post" enctype="multipart/form-data">
  <input type="file" name="avatar" />
</form>
<!--Don't forget that enctype="multipart/form-data" is importent in file uploading-->
```
```js
var express = require('express')
var multer  = require('multer')
var upload = multer({ dest: 'uploads/' })
 
var app = express()
 
app.post('/profile', upload.single('avatar'), function (req, res, next) {
  // req.file is the `avatar` file
  // req.body will hold the text fields, if there were any
})
 
app.post('/photos/upload', upload.array('photos', 12), function (req, res, next) {
  // req.files is array of `photos` files
  // req.body will contain the text fields, if there were any
})
 
var cpUpload = upload.fields([{ name: 'avatar', maxCount: 1 }, { name: 'gallery', maxCount: 8 }])
app.post('/cool-profile', cpUpload, function (req, res, next) {
  // req.files is an object (String -> Array) where fieldname is the key, and the value is array of files
  //
  // e.g.
  //  req.files['avatar'][0] -> File
  //  req.files['gallery'] -> Array
  //
  // req.body will contain the text fields, if there were any
})
```
::: success
In case you need to handle a text-only multipart form, you should use the .none() method:
:::
```js
var express = require('express')
var app = express()
var multer  = require('multer')
var upload = multer()
 
app.post('/profile', upload.none(), function (req, res, next) {
  // req.body contains the text fields
})
```
## File information
Each file contains the following information:

| Key |	Description |	Note |
| -- | -- | -- |
| fieldname	| Field name specified in the form	 | |
| originalname |	Name of the file on the user's computer	| |
| encoding |	Encoding type of the file	| |
| mimetype |	Mime type of the file	| |
| size |	Size of the file in bytes	| |
| destination |	The folder to which the file has been saved	| DiskStorage |
| filename |	The name of the file within the destination	| DiskStorage |
| path |	The full path to the uploaded file	| DiskStorage |
| buffer |	A Buffer of the entire file	| MemoryStorage |


## options that can be passed to Multer.

| Key |	Description |
| --- | --- |
| dest or storage | 	Where to store the files |
| fileFilter |	Function to control which files are accepted |
| limits |	Limits of the uploaded data |
| preservePath |	Keep the full path of files instead of just the base name |


## fileFilter
Set this to a function to control which files should be uploaded and which should be skipped. The function should look like this:
```js
function fileFilter (req, file, cb) {
 
  // The function should call `cb` with a boolean
  // to indicate if the file should be accepted
 
  // To reject this file pass `false`, like so:
  cb(null, false)
 
  // To accept the file pass `true`, like so:
  cb(null, true)
 
  // You can always pass an error if something goes wrong:
  cb(new Error('I don\'t have a clue!'))
 
}
```

## Error handling
When encountering an error, Multer will delegate the error to Express. You can display a nice error page using the standard express way.

If you want to catch errors specifically from Multer, you can call the middleware function by yourself. Also, if you want to catch only the Multer errors, you can use the MulterError class that is attached to the multer object itself (e.g. err instanceof multer.MulterError).
```js
var multer = require('multer')
var upload = multer().single('avatar')
 
app.post('/profile', function (req, res) {
  upload(req, res, function (err) {
    if (err instanceof multer.MulterError) {
      // A Multer error occurred when uploading.
    } else if (err) {
      // An unknown error occurred when uploading.
    }
 
    // Everything went fine.
  })
})

```

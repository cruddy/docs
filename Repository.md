Repository is responsible for performing actual CRUD operations. It retrieves data, creates, updated and deletes models.

## Uploading files

Default repository implementation can upload files using `FileUploader`. Note that repository doesn't care of file content, it just uploads it to specified folder and returns filename to be stored in database.

You need to define files to be uploaded in entity's [[schema|Schema]]:

```php
public function files($repo)
{
    $repo->uploads('images');
}
```

You can specify folder to where files are uploaded:

```php
$repo->uploads('images')->to('upload/images');
```

By default, `FileUploader` generates random names for files, but you can tell him to keep original names:

```php
$repo->uploads('images')->keepNames();
```

If file is exists, random name will be forced.

### What is saved to the database?

`FileUploader` will move `UploadedFile` to specified directory and return its filename relatively to the root folder. Default root folder is `public`. So, if root folder is `public` and path is `upload/images`, file with the name of `my-image.jpg` will be saved to `public/upload/images/my-image.jpg` and database value will be `upload/images/my-image.jpg'. You can then easily show this image:

```html
<img src="{{ asset($model->image) }}">
```
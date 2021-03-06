# FileRun API Client PHP Library

This library is an example on how to use the [FileRun] API from your own PHP application. It uses the OAuth2 "resource owner credentials flow", where the credentials of the user are stored in your application.

For more information, please read the FileRun API [documentation].

### Installation

```sh
composer require filerun/api-client:dev-master
```

### Usage


```php
$FileRun = new FileRun\API\Client(array(
	'url' => 'http://WWW.YOUR-SITE.COM/FILERUN',
	'client_id' => 'GET-THIS-FROM-THE-FILERUN-CONTROL-PANEL',
	'client_secret' => 'GET-THIS-FROM-THE-FILERUN-CONTROL-PANEL',
	'username' => 'admin', //the FileRun username
	'password' => 'admin', //the FileRun password
	'scope' => ['profile', 'list', 'upload', 'download', 'weblink', 'delete', 'share'] //list of "permissions" needed to complete the operations
));
$rs = $FileRun->connect();
if (!$rs) {exit('Failed to connect');}
$FileRun->debug = true;//set this to troubleshoot possible errors
```
This will authenticate your program and allow you to call the API:

#### Getting user info

```php
$userInfo = $FileRun->getUserInfo();
if (!$userInfo) {
	exit('Failed to get user info: '.$FileRun->getError());
}
echo 'Hello '.$userInfo['name'].'!<br>';
```

#### Uploading a file

```php
$data = 'This is the file contents. This is some unique data: '.time().'-'.rand();
$rs = $FileRun->uploadFile(['path' => '/ROOT/HOME/MyUploadedFile.txt'], $data);
if ($rs && $rs['success']) {
	echo 'File successfully uploaded with the following contents:<br>';
	echo '<div style="border:1px solid silver;background-color:whitesmoke;padding:5px;">'.$data.'</div>';
} else {
	exit('Failed to upload file: '.$FileRun->getError());
}
```

#### List folder contents

```php
$rs = $FileRun->getFileList(['path' => '/ROOT/HOME']);
if ($rs && $rs['success']) {
	echo 'Here is the list of files and folders inside your home folder:<br>';
	echo '<div style="border:1px solid silver;background-color:whitesmoke;padding:5px;max-height:200px;overflow:auto"><pre>';
	print_r($rs);
	echo '</pre></div>';
} else {
	exit('Failed to retrieve list of files: '.$FileRun->getError());
}
```

#### Search for files

```php
$rs = $FileRun->searchFiles(['path' => '/ROOT/HOME', 'keyword' => 'MyUploaded']);
if ($rs && $rs['success']) {
	echo 'Search results for keyword "MyUploaded":<br>';
	echo '<div style="border:1px solid silver;background-color:whitesmoke;padding:5px;max-height:200px;overflow:auto"><pre>';
	print_r($rs);
	echo '</pre></div>';
} else {
	exit('Failed to retrieve search result: '.$FileRun->getError());
}
```

#### Download a file

```php
$rs = $FileRun->downloadFile(['path' => '/ROOT/HOME/MyUploadedFile.txt']);
if ($rs) {
	echo 'File successfully downloaded with the following contents:<br>';
	echo '<div style="border:1px solid silver;background-color:whitesmoke;padding:5px;">'.$rs.'</div>';
} else {
	exit('Failed to download file: '.$FileRun->getError());
}
```

#### Create a weblink

```php
$rs = $FileRun->getWebLink(['path' => '/ROOT/HOME/MyUploadedFile.txt']);
if ($rs && $rs['success']) {
	echo 'WebLink generated: <a href="'.$rs['data']['linkInfo']['url'].'" target="_blank">'.$rs['data']['linkInfo']['url'].'</a>';
	echo '<br>';
} else {
	exit('Failed to get weblink: '.$FileRun->getError());
}
```

#### Sharing a folder

```php
$rs = $FileRun->shareFolder([
	'path' => '/ROOT/HOME/MyFolder',
	'uid' => 123, //share with user ID 123
	//'gid' => 456, //share with group ID 456
	'anonymous' => 0,   //set to 1 for anonymous share
	'upload' => 1,      //set to 0 to disable uploads
	'download' => 1,    //set to 0 to disable downloads
	'comment' => 1,     //set to 0 to prevent users from posting comments
	'read_comments' => 1, //set to 0 to prevent users from reading comments
	'alter' => 0,       //set to 1 to allow users to rename/delete/move/etc files
	'share' => 0,       //set to 1 to allow users to create web links inside the share
	'alias' => 'My share' //If you want the folder to be shared with a different name
]);
if ($rs && $rs['success']) {
	echo 'Folder successfully shared.';
} else {
	exit('Failed to share folder: '.$FileRun->getError());
}
```

#### Unsharing a folder

```php
$rs = $FileRun->unShareFolder([
	'path' => '/ROOT/HOME/MyFolder',
	'uid' => 123, //unshare with user ID 123
	//'gid' => 456, //unshare with group ID 456
]);
if ($rs && $rs['success']) {
	echo 'Folder successfully unshared.';
} else {
	exit('Failed to unshare folder: '.$FileRun->getError());
}
```


#### Delete a file

```php
$rs = $FileRun->deleteFile(['path' => '/ROOT/HOME/MyUploadedFile.txt']);
if ($rs && $rs['success']) {
	echo 'File successfully moved to trash.';
} else {
	exit('Failed to move file to trash: '.$FileRun->getError());
}
```

[FileRun]:http://www.filerun.com
[documentation]:http://docs.filerun.com/index.php/API
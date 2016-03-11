# Upload helper based on Codeigniter's helper

#Installation:
Copy both Security.php, Upload.php to helpers folder

#Usage:
Setup your route. 
```
//routes.php
 Router::any('', 'Controllers\Main@index');
 Router::any('upload','Controllers\Main@upload');//Main is my controller
```
Create a view with a form

```
// upload_view.php
<?php echo \Helpers\Form::open(array('method'=>'post','files'=>'','action'=>'upload'));?>
    Archivo <input type="file" name="thefile">
    <input type="submit" value="upload">
<?php echo \Helpers\Form::close(); 
```
Load the view previously created 
```
// controller Main.php
   public function index() {
        View::renderTemplate('header');
        View::render('upload_view');
        View::renderTemplate('footer');
    }
```

Inside the controller create a function the name should be the same as the route defined
```
// controller Main.php
    public function upload() {
        $config['upload_path'] = 'uploads';
        $config['allowed_types'] = '*';
        $upload = new \Helpers\Upload($config);
        if ($upload->do_upload('archivo')) {
            print_r($upload->data());
        } else {
            print_r($upload->error_msg);
        }
    }
```

The code  ``` $config['upload_path'] = 'uploads'; ``` define the upload path where the file will upload
you must CREATE THIS FOLDER in this case its path will be on the root.
For more preferences like allowed_types, upload_path you can find the codeigniter's helper documentation here
https://ellislab.com/codeigniter/user-guide/libraries/file_uploading.html

##Upload to a database table
The function fileToDb allows you to upload a file to a table column
-Setup a route: Router::any('up','Controllers\Main@up');
-Create a database table with 3  predefined column names file_type, file_content, file_size, you can
add extra columns if you wish to add extra info, this can be passed through 
the 3rd parameter as an array. array(column=>value,column=>value)
As an example i have this table: 
```
    CREATE TABLE `somefiles` (
            `id` INT(11) NOT NULL AUTO_INCREMENT,
            `file_content` MEDIUMBLOB NULL,
            `file_type` VARCHAR(100) NULL DEFAULT NULL,
            `file_size` INT(100) NULL DEFAULT NULL,
            PRIMARY KEY (`id`)
    )
    ENGINE=InnoDB
    AUTO_INCREMENT=1;
```
-Create a function should be the same name as your route
```
    public function up() {
        $config['upload_path'] = 'uploads';
        $config['allowed_types'] = '*';
        $upload = new \Helpers\Upload($config);
        if ($upload->do_upload('archivo')) {
            $tableData = array(); // empty array predefined to store the file
            $upload->fileToDb($upload->data(), 'somefiles', $tableData);
        } else {
            print_r($upload->error_msg);
        }
    }
```
To the fileToDb function you should pass in this order the file data with the data()
method, the table name, and the last parameter you can pass to the table, 
in this case is empty since i don't pass any extra data.

##Download a file from database table

The function dbFileDownload  allows you to download a file stored in a db table
- Create a route: Router::any('down','Controllers\Main@download');
- Create a funtion: 
```
    public function download() {
         $download = new \Helpers\Upload();
         $download->dbFileDownload('somefiles', array('id'=>4));
         
    }
```
The first parameter is the table name, the second is an array where you can searh
by column in this case you know the id, the search should give only 1 file
in case of failure it will return FALSE.

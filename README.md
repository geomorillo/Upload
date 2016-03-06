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
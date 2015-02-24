#CSV Datasource for reading your CSV files
*Originally based on work of Siegfried Hirsch (Siegfried)*

**Updated by Rick Mills for CakePHP 2.x**

Licensed under The MIT License
Redistributions of files must retain the above copyright notice.

#Usage:


Copy the data source file to /app/Model/Datasource/CsvSource.php

Setup your database connection in /app/Config/database.php:



	class DATABASE_CONFIG {

	  public $csvFileConfig = array(
	          'datasource' => 'CsvSource',
	          'path' => "/path/to/your/file.csv",
	          'header_row' => 7 // Optional - It use this row as header and start to read data from next row... previous rows will be ignored
	  );

	  public $default = array(
	          'driver' => 'mysql',
	          'persistent' => false,
	          'host' => 'localhost',
	          'login' => 'user',
	          'password' => 'password',
	          'database' => 'test_database_name',
	          'prefix' => ''
	  );
	}


Setup your model
	
	class MyModel extends AppModel {

	  public $name = 'MyModel';
	  public $useDbConfig = 'csvFileConfig';
	  public $useTable = false;

	}


Now you can retrive the csv data from controller

	class ExampleController extends AppController {

	  public $name = 'Example';
	  public $uses = array('MyModel');

	  function index() {

	    //retrive rows with the id bethween 11 and 20
	    $csvData = $this->MyModel->find('all', array('page' => 2, 'limit' => 10));

	    $this->set('csvData', $csvData);
	  }

	}

##Limitations

It's seemingly not currently possible to access global constants such as APP_DIR inside the database.php file, making it pretty annoying to reference uploaded CSV files. To get around this I found the simplest way was to upload the CSV via a form and save it into /app/tmp/your_file.csv

Then in your database.php config file, inside the constructor override the filepath of the csv file like so:

	public function __construct() {

		$this->app_path = dirname(dirname( __FILE__));
		$this->csvFileConfig['path'] = $this->app_path . $this->csvFileConfig['path'];

		$config = Configure::read('db.config');

		$this->default = $this->$config;

	}
	
This will essentially append your app/tmp path to the begining of the path parameter. Hopefully there is another/better way of doing this that someone will point out, but it does work. Just make sure you don't call your model until afte you've got your CSV file uploaded.
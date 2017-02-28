# Basic

*   Typical Directory Structure
        root
        ├─ThinkPHP
        │ ├─Common
        │ ├─Conf
        │ ├─Lang
        │ ├─Library
        │ │ ├─Think
        │ │ ├─Behavior
        │ │ ├─Org
        │ │ ├─Vendor
        │ │ ├─...
        │ ├─Mode
        │ ├─Tpl
        │ ├─LICENSE.txt
        │ ├─logo.png
        │ ├─index.php
        ├─Application
        │ ├─Common
        │ │ ├─Common
        │ │ ├─Conf
        │ ├─Module
        │ │ ├─Conf
        │ │ ├─Common
        │ │ ├─Controller
        │ │ ├─Model
        │ │ ├─Logic(Optional)
        │ │ ├─Service(Optional)
        │ │ ├─...
        │ │ ├─View
        │ ├─Runtime
        │ │ ├─Cache
        │ │ ├─Data
        │ │ ├─Logs
        │ │ ├─Temp
        ├─www
        │ ├─Public
        │ ├─index.php
*   Entrance File
        define('APP_PATH','../Application'); // Define 'Application' directory.
        define('RUNTIME_PATH','../Runtime/'); // Define runtime directory.
        require '../ThinkPHP/ThinkPHP.php'; // Load framework entrance file.
*   Add New Module
        define('BIND_MODULE','New_Module_Name'); // Automatically generate new module directory structure.
*   Debug Mode
        define('APP_DEBUG',true); // Enable debug mode.
*   Config
        return array(
            'Parameter'=>'Value',
            ...
        );
        // Public config file in 'ThinkPHP/Common/Conf/config.php'.
        define('CONF_EXT','.ini'); // Define config file format.
*   Controller
        Controller file name:'ThinkPHP/Module/Controller/IndexController.class.php'
*   URL Request
        http://serverName/index.php/Module/Controller/Operation
        Default module: Home, default controller: Index, default operation: index.
*   View
        Default template: ThinkPHP/Module/View/Controller/Operation.html
    Sample template:
        <html>
        <head>
        <title>hello {$name}</title>
        </head>
        <body>
            hello, {$name}!
        </body>
        </html>
    Sample rendering operation:
        <?php
        namespace Home\Controller;
        use Think\Controller;
        class IndexController extends Controller {
            public function hello($name='thinkphp'){
                $this->assign('name',$name);
                $this->display();
            }
        }
*   Read data
    Database connnection in config file in the module:
        'DB_TYPE'=>'mysql',
        'DB_HOST'=>'127.0.0.1',
        'DB_NAME'=>'thinkphp',
        'DB_USER'=>'',
        'DB_PWD'=>'',
        'DB_PORT'=>3306,
        'DB_PREFIX'=>'think_',
        'DB_CHARSET'=>'utf8',
    

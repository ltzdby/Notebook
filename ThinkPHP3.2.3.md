# ThinkPHP3.2.3 Study Notes

## Basic

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
        ?>

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

## CURD

*   C
    
    Sample ThinkPHP\Home\Controller\FormController.class.php:
        
        <?
        namespace Home\Controller;
        use Think\Controller;
        class FormController extends Controller{
            public function insert(){
                $Form=D('Form');
                if($Form->create()) {
                    $result=$Form->add();
                    if($result) {
                        $this->success('Success!');
                    }else{
                        $this->error('Error!');
                    }
                }else{
                    $this->error($Form->getError());
                }
            }
        }
        
        // If the primary key is A_I, 'add' method will return the value of that primary key. If not, the return value shows the number of data inserted. If the return value is 'false', it means an error.

*   Model Class
    
    Define: ModelName+Model.class.php // Capitalize the first letter of ModelName.
    
    Sample Home\Model\FormModel.class.php:
        
        <?php
        namespace Home\Model;
        use Think\Model;
        class FormModel extends Model {
            // Automatic validate
            protected $_validate =array(
                array('title','require','Title required!'),
                );
            // Automatic complete
            protected $_auto=array(
                array('create_time','time',1,'function'),
                );
        }
    
    // If we use function 'D' instantiate model class, we need a data model class normally. 'Create' method will execute automatic validate and complete (if defined). If validation failed, we can get information from method 'getError' of model. If succeed, it shows that object is created but just stored in memory until method 'add' is called.
    
    If data is trusted, we can use 'add' directly:
    
        $Form=D('Form');
        $data['title']='ThinkPHP';
        $data['content']='Content';
        $Form->add($data);
        
    Or:
    
        $Form=D('Form');
        $Form->title='ThinkPHP';
        $Form->content='content';
        $Form->add();
    
*   R
    
        public function read($id=0){
            $Form=M('Form');
            // Read data
            $data=$Form->find($id);
            if($data) {
                $this->assign('data',$data);
            }else{
                $this->error('Error!');
            }
            $this->display();
        }
        
    Return value of method 'find':
    
        array (
            'id'=>5,
            'title'=>'title',
            'content'=>'content',
            'status'=>1,
        )
        
    Then we can output data in a model:
    
        <table>
         <tr>
            <td>id:</td>
            <td>{$data.id}</td>
         </tr>
         <tr>
            <td>title:</td>
            <td>{$data.title}</td>
         </tr>
         <tr>
            <td>content:</td>
            <td>{$data.content}</td>
         </tr>
        </table>
        
    Another method 'getField':
    
        $Form=M("Form");
        $title=$Form->where('id=3')->getField('title');
        

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
        define('CONF_EXT','.ini'); 
        // Define config file format.

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

*   Database connnection in config file in the module:

        'DB_TYPE'=>'mysql',
        'DB_HOST'=>'127.0.0.1',
        'DB_NAME'=>'thinkphp',
        'DB_USER'=>'',
        'DB_PWD'=>'',
        'DB_PORT'=>3306,
        'DB_PREFIX'=>'think_',
        'DB_CHARSET'=>'utf8',

## CURD

*   Create
    
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
    
*   Read
    
    Get data set:
    
        namespace Home\Controller;
        use Think\Controller;
        class IndexController extends Controller {
            public function index(){
                $Data=M('Data'); // Instantiate 'Data' data model.
                $result=$Data->find(1);
                $this->assign('result',$result);
                $this->display();
            }
        }
        
    A method to get single data:
    
        public function read($id=0){
            $Form=M('Form');
            // Read data
            $data=$Form->find($id);
            if($data) {
                $this->assign('data',$data); // Assign values for template variable.
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
        
*   Update
    
    Two methods in 'FormController' class:
    
        public function edit($id=0){
            $Form=M('Form');
            $this->assign('vo',$Form->find($id));
            $this->display();
        }
        public function update(){
            $Form=D('Form');
            if($From->create()) {
                $result=$Form->save();
                if($result) {
                    $this->success('Success!');
                }else{
                    $this->error('Error!');
                }
            }else{
                $this->error($Form->getError());
            }
        }
    
    Update without using form:
    
        $Form=M('Form');
        $data['id']=5;
        $data['title']='ThinkPHP';
        $data['content']='ThinkPHP3.2.3';
        $form->where('id=5')->save($data);
    
    Or:
    
        $Form=M('Form');
        $Form->title='ThinkPHP';
        $Form->content='ThinkPHP3.2.3';
        $Form->where('id=5')->save();
    
    The return value of 'save' is the number of rows affected. If it is 'false', an error occurred.
    
    Sometimes we only need to modify a field:
    
        $Form=M('Form');
        $Form->where('id=5')->setField('title','ThinkPHP');
    
    Or:
    
        $User=M('User');
        $User->where('id=5')->setInc('score',3); // score+=3
        $User->where('id=5')->setInc('score'); // score++
        $User->where('id=5')->setDec('score',5); // score-=5
        $User->where('id=5')->setDec('score'); // score--
    
*   Delete
    
    Delete a single row:
    
        $Form=M('Form');
        $Form->delete(5);
    
    'Delete' method can also delete more rows:
    
        $User=M("User"); // Instantiate 'User' object.
        $User->where('id=5')->delete();
        $User->delete('1,2,5');
        $User->where('status=0')->delete();
    
    The return value of 'delete' is the number of rows deleted. If it is 'false', it means an error occurred.
    
## Query Language

### Ways of query

*   Use string as conditions
    
        $User=M("User"); // Instantiate "User" object.
        $User->where('type=1 AND status=1')->select();
    
    This will generate SQL statement:
    
        SELECT * FROM think_user WHERE type=1 AND status=1
    
*   Use array as conditions
    
    This is the most commonly used way:
    
        $User=M("User");
        $condition['name']='thinkphp';
        $condition['status']=1;
        $User->where($condition)->select();
    
    This will generate SQL statement:
    
        SELECT * FROM think_user Where `name`='thinkphp' AND status=1
    
    The default logical relationship is 'AND', but it can be modified:
    
        $User=M("User");
        $condition['name']='thinkphp';
        $condition['account']='thinkphp';
        $condition['_logic']='OR';
        $User->where($condition)->select();
    
    This will generate SQL statement:
    
        SELECT * FROM think_user WHERE `name`='thinkphp' OR `account`='thinkphp'
    
*   Use object as conditions
    
    Use 'stdClass' as an example:
    
        $User=M("User");
        $condition= new stdClass();
        $condition->name='thinkphp';
        $condition->status=1;
        $User->where($condition)->select();
    
    This will generate SQL statement:
    
        SELECT * FROM think_user WHERE `name`='thinkphp' AND status=1
    
    Use array can be more effective.
    
*   Expression query
    
    Format:
    
        $map['field']=array('expression','condition');
    
    Table of expression supported:
    
        Expression        | Meaning
        EQ                | =
        NEQ               | <>
        GT                | >
        EGT               | >=
        LT                | <
        ELT               | <=
        LIKE              | Fuzzy query
        [NOT] BETWEEN     | (NOT) Interval query
        [NOT] IN          | (NOT) In query
        EXP               | Expression query, support SQL grammar
    
    
    For example:
    
        $map['id']=array('eq',100);
    
    equals:
    
        $map['id']=100;
    
    If 'DB_LIKE_FIELDS' parameter sets, some fields will be in fuzzy query automatically. If
    
        'DB_LIKE_FIELDS'=>'title|content'
    
    is set, use
    
        $map['title']='thinkphp';
    
    will be like this:
    
        title like '%thinkphp%'
    
    'EXP' condition will not be processed as string, so the condition following can use any grammar SQL supported, including functions and fields. Query expressions can be used not only as query conditions, but also updating data, such as:
    
        $User=M("User");
        $data['name']='ThinkPHP';
        $data['score']=array('exp','score+1');
        $User->where('id=5')->save($data);
    
### Fast query

*   Use same condition on different fields
    
        $User=M("User");
        $map['name|title']='thinkphp';
        $User->where($map)->select();
    
    Query condition will be:
    
        name='thinkphp' OR title='thinkphp'
    
*   Different conditions on different fields
    
        $User=M("User");
        $map['status&title']=array('1','thinkphp','_multi'=>true);
        $User->where($map)->select();
    
    `'_multi'=>true` must be in the end of the array, then the query condition will be:
    
        status=1 AND title='thinkphp'
    
    
    It also support more fields, like:
    
        $map['status&score&title']=array('1',array('gt','0'),'thinkphp','_multi'=>true);
    
    Query condition will be:
    
        status=1 AND score>0 AND title='thinkphp'
    
*   Interval query
    
    Example:
    
        $map['id']=array(array('gt',1),array('lt',10));
    
    Query condition:
    
        (`id`>1) AND (`id`<10)
    
    Example:
    
        $map['id']=array(array('gt',3),array('lt',10),'or');
    
    Query condition:
    
        (`id`>3) OR (`id`<10)
    
    Example:
    
        $map['name']=array(array('like','%a%'),array('like','%b%'),array('like','%c%'),'ThinkPHP','or');
    
    Query condition:
    
        (`name` LIKE '%a%') OR (`name` LIKE '%b%') OR (`name` LIKE '%c%') OR (`name`='ThinkPHP')
    
    
### Combination query

*   String mode
    
    Example:
    
        $User=M("User");
        $map['id']=array('neq',1);
        $map['name']='ok';
        $map['_string']='status=1 AND score>10';
        $User->where($map)->select();
    
    Query condition:
    
        (`id`!=1) AND (`name`='ok') AND (status=1 AND score>10)
    
*   Request string mode
    
    Example:
    
        $map['id']=array('gt','100');
        $map['_query']='status=1&score=100&_logic=or';
    
    Query condition:
    
        `id`>100 AND (`status`='1' OR `score`=100')
    
*   Complex mode
    
    Example:
    
        $where['name']=array('like','%thinkphp%');
        $where['title']=array('like','%thinkphp%');
        $where['_logic']='or';
        $map['_complex']=$where;
        $map['id']=array('gt',1);
    
    Query condition:
    
        (id>1) AND ((name like '%thinkphp%') OR (title like '%thinkphp%'))
    
### Statistical query

ThinkPHP has many internal methods, including:

    Method  | Meaning
    Count   | Count the number, parameter is the field to be counted (Optional)
    Max     | Get the maximum number, parameter is the field name (Required)
    Min     | Get the minimum number, parameter is the field name (Required)
    Avg     | Get the average value, parameter is the field name (Required)
    Sum     | Get the sum, parameter is the field name (Required)

Example:

    $User=M("User");
    $userCount=$User->count();
    $userCount=$User->count("id");
    $maxScore=$User->max('score');
    $minScore=$User->where('score>0')->min('score');
    $avgScore=$User->avg('score');
    $sumScore=$User->sum('score');

### Dynamic query

Method list:

    Method      | Meaning                                     | Example
    getBy       | Query according to value of field           | getByName, getByEmail
    getFieldBy  | Query according to field and return a value | getFieldByName

*   getBy:
    
        $user=$User->getByName('123');
        $user=$User->getByEmail('123@456.com');
        $user=$User->getByAddress('America');
        // Query according to an attribute.
    
*   getFieldBy:
    
        $userId=$User->getFieldByName('123','id');
        // Get 'id' according to 'Name'.
    
### Subquery

*   Use 'select' method
    
    When the parameter of 'select' is 'false', it means only form SQL but does not execute query. For example:
    
        $subQuery=$model->field('id,name')->table('tablename')->group('field')->where($where)->order('status')->select(false);
    
*   Use 'buildSql' method
    
    'buildSql' only form SQL statement but not execute.
    
        $subQuery=$model->field('id,name')->table('tablename')->group('field')->where($where)->order('status')->buildSql();
    
*   Execute subquery
    
        $model->table($subQuery.' a')->where()->order()->select();
    

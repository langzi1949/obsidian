
当前php中最受欢迎的框架就是Laravel，逻辑相对很好理解， 它就是一个MVC的接口，这个是以前就掌握的开发模式。

# 1.流程

使用Laravel开发，我们有两种方式来组织前-后端的逻辑

1. SPA单页面应用

浏览器访问 ->  后端`Laravel`路由(routes目录下) -> 找到`views`中的blade模板文件 -> 返回HTML外壳 
-> 浏览器接到后端返回的HTML后 -> 前端加载`vue应用` -> `vue router`进行渲染组件  -> 后续的逻辑就跟我们之前开发前端一致。

2. 融合在一起的php页面

浏览器访问 ->  后端`Laravel`路由(routes目录下) -> 找到`views`中的blade模板文件 -> 直接返回的就是HTML文件
-> 后续的页面可能会发生路由变化，同样的会进行浏览器发起请求，然后又走一遍这个流程


# 2. 路由

路由文件存放在`/routes` 目录里面，正常的开发会有`web.php`以及`api.php`  一个是专门用于浏览器网页的路由，另外一个是api接口专用的路由。
在terminal中可以通过命令` php artisan route:list` 来查看当前有多少的路由

## 2.1 web.php

1. 基本路由
```php
// 闭包路由（直接定义处理逻辑）
Route::get('/user', function () {
    return 'Hello, User!';
});
// POST路由，提交数据
Route::post('/user', [UserController::class, 'store']);

// 控制器路由（调用控制器方法）
Route::get('/user', [UserController::class, 'index']);

// 允许 GET 和 POST
Route::match(['get', 'post'], '/user', [UserController::class, 'handle']);

// 允许所有 HTTP 方法
Route::any('/user', [UserController::class, 'handleAny']);
```

2. 参数路由

```php
// 接收 id 参数
Route::get('/user/{id}', function ($id) {
    return "User ID: {$id}";
});

// 多个参数（顺序与回调参数一致）
Route::get('/user/{id}/post/{postId}', function ($id, $postId) {
    return "User: {$id}, Post: {$postId}";
});

// 可选参数
Route::get('/user/{name?}', function ($name = 'Guest') {
    return "Hello, {$name}";
});

// 参数校验
// id 必须是数字
Route::get('/user/{id}', function ($id) {})
    ->where('id', '[0-9]+');

// 多个参数约束
Route::get('/user/{id}/post/{postId}', function ($id, $postId) {})
    ->where(['id' => '[0-9]+', 'postId' => '[a-z0-9]+']);
```

3. 按照路由类型分类
```php
// 返回视图
Route::get('/hello', function () {
    return view('hello'); // 返回视图
});
// 通过controller来返回
// 基础写法
Route::get('/user', [UserController::class, 'index']);
// Laravel 8+ 支持的字符串语法（需配置命名空间）
Route::get('/user', 'UserController@index');
// 一键生成CRUD对应的所有路由
// 生成 7 个 RESTful 路由（index, create, store, show, edit, update, destroy）
Route::resource('posts', PostController::class);

// 只生成指定方法的路由
Route::resource('posts', PostController::class)->only(['index', 'show']);

// 排除指定方法的路由
Route::resource('posts', PostController::class)->except(['create', 'edit']);

// 可以按照controller来进行
Route::prefix('student')->controller(StudentController::class)->group(function() {
	Route::get('/', 'index');
	Route::get('/{id}')
});

// 路由组
// 带前缀（访问路径为 /admin/...）
Route::prefix('admin')->group(function () {
    Route::get('users', [AdminUserController::class, 'index']);
    Route::get('posts', [AdminPostController::class, 'index']);
});

// 带中间件（如认证）
Route::middleware('auth')->group(function () {
    Route::get('/profile', [ProfileController::class, 'show']);
});

// 命名空间（指定控制器所在命名空间）
Route::namespace('Admin')->group(function () {
    Route::get('dashboard', 'DashboardController@index');
});
```

4. 命名路由

```php
Route::get('/user/{id}', [UserController::class, 'show'])
    ->name('user.show');

// 使用命名路由生成 URL
$url = route('user.show', ['id' => 1]);

// 重定向到命名路由
return redirect()->route('user.show', ['id' => 1]);
```

5. 404

```php
// 类似404
Route::fallback(function () {
    return view('404');
});
```


# 3.  视图view

php的view信息都在`/resources/views`这个目录下面，所有的文件都是`xxxx.blade.php`，这个就是视图模板文件。
必须是固定的格式`xxxx.blade.php`这个风格的就是一个blade模板文件，Laravel才知道怎么处理

有两种方式创建添加一个view
1. 第一种，直接手动添加创建一个view文件就行，比如`student-detail.blade.php`文件  这个命名风格在view中就是正确的
2. 第二种，用命令行的方式

```bash
php artisan make:view student-detail
```

如果是最简单的view的方式话，可以在路由中直接添加view路由
- 常用静态页面，比如帮助页、隐私政策页等
```php
Route::view('student-detail', 'student-detail');
```

## 3.1 给view传递参数

```php
// 1️⃣ 使用get方法
Route::get('student-detail', function() {
	$name = 'zmg';
	$email = 'zmglove@126.com'
	//return view('student-detail')->with('name', $name)->with('email', $email);
	 return view('student-detail',compact('name','email'));
});

// 2️⃣ 使用路由方法
$name = 'zmg';
$email = 'zmglove@126.com';
Route::view('student-detail', 'student-detail', ['name'=>$name, 'email'=>$email]);
```

> [!tips]
> `compact`函数是内置的函数，将变量名与其值打包成关联数组。 将多个变量，最终组成map

## 3.2 注释

在blade文件中可以使用传统的html的注释方式，也可以使用专用的注释方式

```php
// 原始的方式，F12会看到这个注释信息
<!-- 这种方式也行-->

// 这种方式也行，但是在前端最终的html页面中，看不到这个注释信息，即使F12检查
 {{--
	这个也是一段注释
--}}
```
## 3.3  blade模板指令

这些指令主要是模板里面的一些逻辑处理，比如循环，if 等
必要重要的是for以及一些条件处理，所有的指令都是`@`开头的 `@end`结尾的

```php
@if ($it < 4) 
// 写html的处理逻辑等
@endif

@for ($i = 0; $i < count($arr); $i++) 
// 处理循环的逻辑
@endfor
```

其他相关的指令查看[https://laravel.com/docs/12.x/blade#blade-directives](https://laravel.com/docs/12.x/blade#blade-directives)

## 3.4 子视图

子视图就是将一些公共的html代码判断进行复用，通过`@include` 导入进来，就相当于把代码直接复制进来，最后组成一个大的HTML

子视图一般会有专门的目录存放，目录命名为：`sub-views`或者`partials`  ，我看社区好像都是放在`partials`下，然后子视图文件命名都是以`_`下划线开头，这样别人一看到就知道是一个子视图。
要明白，子视图就是相当于嵌在php中的而已，达到复用的目的。
其中，子视图可以传递参数

```php
<!-- 传递参数到子视图 -->
@include('partials.footer', ['year' => 2025, 'siteName' => '我的博客'])
```

## 3.5 layout

1. 通过**blade template**的方式来使用

这个跟vue类似，会有一个整体的布局layout，在php中通过`@extend`继承的方式，然后在`@section` 填充占位符(yield)的方式，将代码插到对应的`layout`中的yield占位符的地方。

```php
<!-- resources/views/layouts/app.blade.php -->
<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

```php
<!-- resources/views/child.blade.php -->

@extends('layouts.app')

@section('title', 'Page Title')

@section('sidebar')
    @@parent   <!-- 这个就是要保留parent的内容，这边只是追加-->

    <p>This is appended to the master sidebar.</p>
@endsection

@section('content')
    <p>This is my body content.</p>
@endsection
```

## 3.6 添加view缓存和清空view缓存

```bash
# 添加缓存
php artisan view:cache
# 清空缓存
php artisan view:clear
```

# 4. Controller

## 4.1 通过命令创建

其实手动自己创建也没有问题，但是通过命令会生成一些模板代码
```bash
php artisan make:controller StudentController
```

然后在controller中写相关的代码逻辑，最后可以返回view出去

```php title=路由
Route::get('student', [StudentController::class, 'index']);
```

## 4.2 invokable controller

这个是一个简化的controller，就是controller中可能只有一个方法，那么我们就可以简化
1. 首先通过命令来创建一个invokable controller

```shell
php artisan make:controller TestInvokableController --invokable
```

2. 会自动生产一个controller，在`__invoke`中写逻辑即可

```php
class TestInvokableController extends Controller
{
    /**
     * Handle the incoming request.
     */
    public function __invoke(Request $request)
    {
        //
        return view('student-detail',['id'=>800]);
    }
}
```

3. 路由中进行简化配置

```php
// 不用写具体的方法了，直接一个类即可，系统会自动找这个类的__invoke方法
Route::get('student', TestInvokableController::class);
```

## 4.3 Controller Middleware

这个相当于是拦截器，但是是在某个controller对方法进行拦截处理，可以统一处理请求验证、权限校验、日志记录以及限流等通用操作

### 4.3.1 注册中间件

```bash
php artisan make:middleware CheckAge
```
会自动生成一个代码文件，结构如下
```php title=app/Http/Middleware/CheckAge.php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckAge
{
    // $next 是“下一个中间件”或“控制器方法”的回调
    public function handle(Request $request, Closure $next)
    {
        // 请求处理前的逻辑（如验证年龄）
        if ($request->age < 18) {
            return redirect('/underage'); // 不满足条件则跳转
        }

        // 继续执行下一个中间件或控制器
        return $next($request);
    }
}
```

### 4.3.2 应用controller middleware

1. **对controller中的所有方法生效**
直接写在构造函数中即可

```php
// app/Http/Controllers/StudentController.php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class StudentController extends Controller
{
    public function __construct()
    {
        // 应用 auth 中间件（所有方法都需要登录）
        $this->middleware('auth');
        
        // 应用自定义的 CheckAge 中间件
        $this->middleware(\App\Http\Middleware\CheckAge::class);
    }

    public function index()
    {
        // 只有通过中间件验证后才会执行
        return view('students.index');
    }
}
```

2. 对指定方法生效（排除或者包含）

```php
public function __construct()
{
    // 仅对 index 和 show 方法生效
    $this->middleware('auth')->only(['index', 'show']);
    
    // 对除 destroy 外的所有方法生效
    $this->middleware('CheckAge')->except('destroy');
}
```

## 4.4 resource controller

它的主要主要作用就是预定义了CRUD的7个方法，极大的简化了路由和controller的编写

### 4.4.1 创建resource controller

```bash
php artisan make:controller PostController --resource
```

### 4.4.2 注册资源路由

一行代码直接包含7个请求
```php
// 生成针对 posts 资源的所有路由
Route::resource('posts', PostController::class);
```

### 4.4.3 其他高级玩法
1. 限制路由资源

```php
// 只生成 index 和 show 路由
Route::resource('posts', PostController::class)->only(['index', 'show']);

// 排除 create 和 edit 路由（适合纯 API 场景，无需表单页面）
Route::resource('posts', PostController::class)->except(['create', 'edit']);
```

2. API路由资源

```php
// 生成 5 个适合 API 的路由（无 create/edit）
Route::apiResource('posts', PostController::class);
```

# 5. Migration

就是数据库的迁移文件，保证团队的成员拿到代码后，database的初始化是相同的，以及后续所有的DDL、init DML 语句等都要写到migration文件中。
## 5.1 migrations目录

如果要将migration的文件写入到数据库的话，
```shell
# 这个命令会将database下的migration里面的文件DDL都执行完毕
php artisan migrate
```

创建一个migration文件 文件名称为`create_`开头的，这样会创建一些模板代码
```shell
php artisan make:migrate create_students_table
```

> [!danger]
> 我们会注意到使用命令行的话，文件名会有一些命名规范，常见的如下：
> - **创建新表**   create_students_table
> - **向表中添加字段**   add_columnname_to_students
> - **从表中删除字段**  remove_columnname_from_students
> - **添加索引**  add_index_to_students
> - ***删除索引* remove_index_from_students
> - **重新命名**  rename_xxx_to_students

## 5.2 rollback

执行`php artisan migrate:rollback` 就会回滚==**最近一个batch的文件**==
具体的migration文件都存在数据库的`migrations`表中，里面记录了batch id

如果要回滚最近两个批次的SQL，可以添加参数
```shell
php artisan migrate:rollback --step=2
```

## 5.3 其他命令

直接`php artisan list`  看一下相关`migrate`的命令，有相关的解释， 比如：
- `migate:reset`  回滚所有迁移文件，将数据库恢复到初识
- `migrate:refresh` 先回滚所有迁移， 然后再次执行所有迁移。相当于清空数据库，然后再重建

## 5.4 factory和seeder

1. 先创建一个Factory

```shell
php artisan make:factory StudentFactory --model=Student
```

参数有：
- `--model`  指定model
- `--seeder`  同时创建一个新的seeder文件

2. 在Factory文件中定义数据

factory的作用就是定义如何为Eloquent模型生成假数据。它是一个蓝图，相当于图纸，规定了每个字段应该是什么类型数据
特点： **factory不会执行任何数据库的操作，他只是一个数据生成器而已，它可以被单独调用，也可以在seeder中使用**

```php
class StudentFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->name(),
            'email' => fake()->unique()->safeEmail(),
            'card_no' => fake()->unique()->numberBetween(0,100000),
            'user_id' => 1,
        ];
    }
}
```

3. 在对应的Model中要移入HasFactory trait

```php title=Student
class Student extends Model
{
    use HasFactory;
}
```

4. seeder的使用

seeder就是执行数据的填充的。它是一个脚本，包含调用工厂或者其他逻辑来向数据库中插入数据的代码。
-  **如何创建一个seeder?**

```shell
 php artisan  make:seeder StudentSeeder
```

```php title=StudentSeeder
class StudentSeeder extends Seeder
{
    /**
     * 运行数据库填充。
     *
     * @return void
     */
    public function run()
    {
        // 示例：使用 Eloquent 模型工厂填充数据
        Student::factory()->count(10)->create();
        
        // 示例：或者使用 DB facade 填充数据  但是还是推荐使用上面的factory来执行
        DB::table('新表名')->insert([
            'column1' => 'value1',
            'column2' => 'value2',
        ]);
    }
}
```

-  **如何执行？**
	- 在`seeder`文件中的`run()`方法中编写代码，通常是使用工厂来创建模型实例，当然也可以不用工厂，直接来创建也可以

```shell
# 这个命令会执行DatabaseSeeder中的run方法
php artisan db:seed
```

<span class='r'>如果只想针对某个表单独进行初始化呢？</span>

1. 方法1 ： 使用`--class`参数
	1. 这个是最常用的方法，可以在`db:seed`命令后加上`--class`参数，来指定运行特定的seeder类

```shell
php artisan db:seed --class=StudentSeeder
```

2. 方法2： 临时修改`DatabaseSeeder`的`run()` 最后执行`db:seed`即可，推荐还是使用第一种，一目了然，而且不会破坏原有的代码

> [!danger]
> 在phpstorm里面你如果使用`User::factory(10)`的时候，点击factory跳转到方法的时候，发现找不到这个方法，那是因为PHP的魔术方法导致的，找不到静态方法后，会去找当前类或者父类中是否有`__callStatic()`方法，是在这个里面进行处理的

# 6. Model

不用我说了，这个就是Java中的Repo或者DAO层

## 6.1 创建一个Model

```shell
php artisan make:model Student
```

以上命令会创建一个`Student` model，这个命令后面可以添加参数，用于创建更多的文件
- `-m`或者`--migration`   同时创建一个迁移文件
- `-c` 或者 `--controller`  同时创建一个controller
- `-r` 或者 `--resource`  同时创建一个resource  controller
- `-f` 或者 `--factory`  同时创建一个model Factory，用于生成假数据
- `-s` 或者 `--seed`  用于生成seeder文件，向DB中插入数据
- `-a` 或者`--all`  这个是终极快捷方式，一下子创建所有相关文件


## 6.2 简单的使用

Laravel中的Eloquent模型名称如果和表中的名字不一致，需要显式的在model中指定表名。
在默认情况下，Eloquent会将模型的类型转为复数形式作为表名，比如 `User` model  => `users` 

就是正常的CRUD，到时候直接看代码即可。 [https://laravel.com/docs/12.x/eloquent-mutators](https://laravel.com/docs/12.x/eloquent-mutators)

>[!tips]
> 如果phpstorm针对Eloquent生成的魔术方法没有提示，可以使用插件`Laravel idea`来解决
> 在工具栏中找到`Laravel` -> 

# 7. Query

## 7.1 Raw SQL

使用`DB::select(SQL语句)`这种写原生SQL方式来进行处理。返回的就是最终的结果，如果需要连接池的话，找AI去问问，此处不赘述
写原生的SQL相对灵活，同时保证了SQL的性能，但是比较费时间，既然用到了Laravel。应该就用最推荐的方式来进行SQL的查询

## 7.2 query builder

其实有点有点类似Java中的sql的 wrapper方式， 有很多复杂的查询，需要查看文档

```php
 // 查询所有记录
$users = DB::table('users')->get();

// 查询单条记录
$user = DB::table('users')->where('id', 1)->first();

// 根据条件查询
$activeUsers = DB::table('users')
	->where('status', 'active')
	->where('age', '>=', 18)
	->get();

// 使用orWhere
$users = DB::table('users')
	->where('status', 'active')
	->orWhere('vip_level', '>=', 3)
	->get();
```

## 7.2 Eloquent ORM的方式

通过Eloquent ORM的方式，就是用`Model`的方法去执行最终的SQL

```php 

 public function create()
    {
        $student = new Student();
        $student->name = 'New Student1';
        $student->email = 'newStudent1@gmail.com';
        $student->card_no = '1234567890';
        $student->user_id = 1;
        $student->save();
        return $student;
    }
public function show($id)
{
// return Student::findOrFail($id);

	$stu = Student::where('id', $id)->get();
	return $stu;
}
```

## 7.3  query scope

在Laravel开发中，有时候一个sql语句，始终都需要加上特定的where条件，比如delete = 0  这种方式，有没有什么方法，可以自动处理呢？我们正常写代码的时候，可以不用考虑这个，但是我作为一个Java程序员，我觉得这个方式不太好，但是PHP就是这么野

1. 定义本地作用域
	1. 只在当前Model中生效，方法名必须要以`scope`为前缀，参数为查询构造器`$query`

```php
class User extends Model
{
    // 定义本地作用域：查询状态为激活的用户
    public function scopeActive(Builder $query)
    {
        return $query->where('status', 'active');
    }

    // 定义带参数的作用域：查询指定年龄以上的用户
    public function scopeAgeGreaterThan(Builder $query, $age)
    {
        return $query->where('age', '>', $age);
    }
}
```

	2. 使用本地作用域

```php
// 使用无参数作用域
$activeUsers = User::active()->get();

// 使用带参数作用域
$adults = User::ageGreaterThan(18)->get();

// 组合多个作用域
$activeAdults = User::active()->ageGreaterThan(18)->orderBy('name')->get();
```

2. 全局作用域
	1. 创建一个实现Scope接口的类

```php
<?php
namespace App\Scopes;

use Illuminate\Database\Eloquent\Scope;
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;

class ActiveScope implements Scope
{
    // 应用全局查询条件
    public function apply(Builder $builder, Model $model)
    {
        $builder->where('status', 'active');
    }
}

```

	2. 在model中注册全局作用域

```php
// app/Models/User.php
use App\Scopes\ActiveScope;

protected static function booted()
{
    // 注册全局作用域
    static::addGlobalScope(new ActiveScope);
}
```

之后对应模型所有的查询都会自动带上相关的条件

	2. 临时忽略全局作用域

有时候我们需要展示忽略全局作用域

```php
// 移除指定全局作用域
$allUsers = User::withoutGlobalScope(ActiveScope::class)->get();

// 移除所有全局作用域
$allUsers = User::withoutGlobalScopes()->get();
```

## 7.4 逻辑删除

1. 先要在数据库中添加逻辑删除的字段`deleted_at`字段

```shell
php artisan make:migration add_deleted_at_to_users_table
```
然后在migration文件中添加
```php
$table->softDeletes();
```

2. 在model中启用逻辑删除，这样调用`delete`其实也是逻辑删除
```php title=model
use SoftDeletes;
```

3. 所有的查询默认过滤已删除的记录  `deleted_at is  null`
4. 如果需要查询已经删除的记录怎么办？

```php
// 查询所有用户（包括已删除的）
$allUsers = User::withTrashed()->get();

// 仅查询已删除的用户
$deletedUsers = User::onlyTrashed()->get();
```


# 其他注意事项

## 1. Laravel IDE 插件的使用

- 使用`Helper code parameters` 这个给一些代码没有提示的，生成一些代码，方便phpstorm能够识别
- 还有很多命令行的指令，也可以直接用`Laravel IDE`插件来可视化操作

## 2. 设置时区

有时候数据库插入的时区不对或者代码运行的时候使用时间函数等，需要调整时区
- 在`php.ini`中添加 `date.timezone=Asia/Bangkok`
- 在项目根目录`config/app.php`中修改`'timezone' => 'Asia/Bangkok',`

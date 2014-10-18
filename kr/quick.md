# Laravel Quickstart

- [Installation](#installation)
- [Routing](#routing)
- [Creating A View](#creating-a-view)
- [Creating A Migration](#creating-a-migration)
- [Eloquent ORM](#eloquent-orm)
- [Displaying Data](#displaying-data)

<a name="installation"></a>
## Installation 설치

### Via Laravel Installer 라라벨 인스톨러로 설치하기

First, download the [Laravel installer PHAR archive](http://laravel.com/laravel.phar).먼저  [Laravel installer PHAR archive](http://laravel.com/laravel.phar)를 다운받아라. For convenience, rename the file to `laravel` and move it to `/usr/local/bin`. 편하게 사용하려면 그 파일을 `laravel`로 바꾸고 `/user/local/bin`으로 이동한다. Once installed, the simple `laravel new` command will create a fresh Laravel installation in the directory you specify. 일단 설치하면 간단하게 `laravel new`명령어로 설치하고자 하는 디렉토리에 새로운 라라벨을 설치할 수 있다. For instance, `laravel new blog` would create a directory named `blog` containing a fresh Laravel installation with all dependencies installed. 예를 들면 `laravel new blog`라고 하면 `blog`라는 디렉토리에 모든 의존패키지들이 설치된 새로운 라라벨이 프로젝트가 생긴다. This method of installation is much faster than installing via Composer. 이 방법은 컴포저를 사용하는것보다 훨씬 빠르다.

### Via Composer 컴포저로 설치하기

The Laravel framework utilizes [Composer](http://getcomposer.org) for installation and dependency management.라라벨 프레임워크는 설치와 의존성 관리를 위해서 Composer를 사용한다. If you haven't already, start by [installing Composer](http://getcomposer.org/doc/00-intro.md). 아직 설치하지 않았다면 Composer를 설치해라.

Now you can install Laravel by issuing the following command from your terminal: 터미널에서 다음 명령어를 입력해서 라라벨을 설치할 수 있다.:
	composer create-project laravel/laravel=4.1.* your-project-name --prefer-dist

This command will download and install a fresh copy of Laravel in a new `your-project-name` folder within your current directory. 이 명령은 현재 디렉토리에 `your-project-name` 폴더로 라라벨을 다운받아 설치하는것이다.

If you prefer, you can alternatively download a copy of the [Laravel repository from Github](https://github.com/laravel/laravel/archive/master.zip) manually. 원한다면 깃헙에 라라벨 저장소에서 수동으로 다운받아 설치 할 수 있다. Next run the `composer install` command in the root of your manually created project directory. 다음번에 수동으로 생성된 프로젝트 디렉토리 루트에서 `composer install`명령을 실행시켜라. This command will download and install the framework's dependencies. 이명령은 프레임워크의 의존성을 다운로드하여 설치할것이다. 

### Permissions 권한

After installing Laravel, you may need to grant the web server write permissions to the `app/storage` directories.라라벨 설치 이후에 웹서버의 `app/storage`에 쓰기 권한을 부여하여야 한다. See the [Installation](/docs/installation) documentation for more details on configuration. 설정에대한 더 자세한 사항은 [Installation](/docs/installation)을 참고하라.

### Serving Laravel

Typically, you may use a web server such as Apache or Nginx to serve your Laravel applications. 보통 아파치나 엔진엑스같은 웹서버에서 라라벨 어플리케이션들을 사용할것이다.If you are on PHP 5.4+ and would like to use PHP's built-in development server, you may use the `serve` Artisan command: PHP5.4이상이라면 `serve`라는 artisan 명령어를 사용하면 PHP의 내장된 개발 서버를 사용 할 수 있다.:

	php artisan serve

<a name="directories"></a>
### Directory Structure 디렉토리 구조

After installing the framework, take a glance around the project to familiarize yourself with the directory structure.프레임워크 설치후 디렉토리 구조를 간단히 보자. The `app` directory contains folders such as `views`, `controllers`, and `models`.`app`디렉토리에는 `views`,`controllers`, `models`와 같은 폴더들이 있다. Most of your application's code will reside somewhere in this directory. 너의 어플리케이션 코드의 대부분은 이 디렉토리에 있을것이다.You may also wish to explore the `app/config` directory and the configuration options that are available to you.`app/config`디렉토리에서 적용할 수 있는 설정 옵션들을 찾아봐라.

<a name="routing"></a>
## Routing 라우팅

To get started, let's create our first route. 시작으로 첫번째 라우트를 만들자. In Laravel, the simplest route is a route to a Closure. 라라벨에서 가장 간단한 라우트는 클로저를 사용한 라우트이다.Pop open the `app/routes.php` file and add the following route to the bottom of the file:`app/routes.php`파일을 열어서 다음 라우트를 파일의 하단부에 추가해라:

	Route::get('users', function()
	{
		return 'Users!';
	});

Now, if you hit the `/users` route in your web browser, you should see `Users!` displayed as the response.지금 웹브라우저에서 `/users`라고 치면 `users!`가 출력될것이다. Great! You've just created your first route. 대단하다! 너의 첫번째 라우트를 만든것이다.

Routes can also be attached to controller classes. For example: 라우트들은 controller classes에 지정될 수 도 있다.:

	Route::get('users', 'UserController@getIndex');

This route informs the framework that requests to the `/users` route should call the `getIndex` method on the `UserController` class. 이 라우트는 프레임워크에 `/users`로 리퀘스트하면 `userController`에 `getIndex`메소드를 call하라고 알려주는 것입니다. For more information on controller routing, check out the [controller documentation](/docs/controllers).컨트롤러 라우팅에대한 더 자세한 정보는 [controller documentation](/docs/controllers)를 확인해라.

<a name="creating-a-view"></a>
## Creating A View 뷰 만들기

Next, we'll create a simple view to display our user data. user data를 보여주는 간단한 view를 만들어 보자. Views live in the `app/views` directory and contain the HTML of your application. `app/views`에 application의 HTML을 가지고 있는 view들이 있다. We're going to place two new views in this directory: `layout.blade.php` and `users.blade.php`. 우리는 이 디렉토리에 2개의 새로운 view들인 `layout.blade.php`와 `users.blade.php`를 만들것이다. First, let's create our `layout.blade.php` file: 먼저 layout.blade.php file을 만들자:

	<html>
		<body>
			<h1>Laravel Quickstart</h1>

			@yield('content')
		</body>
	</html>

Next, we'll create our `users.blade.php` view: 다음으로 users.blade.php view를 만들자:

	@extends('layout')

	@section('content')
		Users!
	@stop

Some of this syntax probably looks quite strange to you.  아마도 이 문법이 아주 낯설것이다. That's because we're using Laravel's templating system: Blade. 우리가 라라벨의 templating 시스템인 블레이드를 사용하기 때문이다. Blade is very fast, because it is simply a handful of regular expressions that are run against your templates to compile them to pure PHP. 블레이드는 순순한 PHP 검파일에비해 간단하게 정규표현을 사용하는것이므로 매우 빠르다. Blade provides powerful functionality like template inheritance, as well as some syntax sugar on typical PHP control structures such as `if` and `for`. 블레이드는 `if`와 `for`와 같은 전형적인 PHP 제어구조뿐 아니라 템플릿의 상속같은 강력한 기능을 제공한다. Check out the [Blade documentation](/docs/templates) for more details.자세한 내용은 [Blade documentation](/docs/templates)를 확인하라.


Now that we have our views, let's return it from our `/users` route. Instead of returning `Users!` from the route, return the view instead: 보기를 보자. `/users`라우트로 가면 문자열`users!`를 돌려주는 대신에 view를 준다:

	Route::get('users', function()
	{
		return View::make('users');
	});

Wonderful! Now you have setup a simple view that extends a layout. 대단해! 지금 당신은 레이아웃을 확장해서 간단한 뷰를 설정했다. Next, let's start working on our database layer.다음으로 데이터베이스 레이어 작업을 해보자.  

<a name="creating-a-migration"></a>
## Creating A Migration 마이그레이션 만들기

To create a table to hold our data, we'll use the Laravel migration system. 데이터를 저장하려는 테이블을 만들기위해 우리는 라라벨 마이그레이션 시스템을 사용할것이다. Migrations let you expressively define modifications to your database, and easily share them with the rest of your team. 마이그레이션은 데이터베이스 수정 사항들을 정의해서 다른팀원들과 쉽게 그 내용을 공유할 수 있게 해준다.

First, let's configure a database connection. 먼저 데이터베이스 커넥션을 설정하자. You may configure all of your database connections from the `app/config/database.php` file. `app/config/database.php`파일에서 모든 데이터베이스 커넥션 설정을 할 수 있다.  By default, Laravel is configured to use MySQL, and you will need to supply connection credentials within the database configuration file. 라라벨은 기본적으로 MySQL을 사용하도록 설정되어있는데 데이터베이스 설정 파일에서 연결 정보를 제공해야 한다. If you wish, you may change the `driver` option to `sqlite` and it will use the SQLite database included in the `app/database` directory.바란다면 `driver`를 `sqlite`로 변경해서 `app/database`디렉토리에 있는 SQLite 데이터베이스를 사용할 수 있을것이다.

Next, to create the migration, we'll use the [Artisan CLI](/docs/artisan). 다음으로 마이그레이션을 만들기위해  [Artisan CLI](/docs/artisan)을 사용할것이다. From the root of your project, run the following from your terminal: 프로젝트 루트 터미널에서 다음을 실행해라:

	php artisan migrate:make create_users_table

Next, find the generated migration file in the `app/database/migrations` folder. 다음으로 `app/database/migrations`폴더에서 생성된 마이그레이션 파일을 찾아라. This file contains a class with two methods: `up` and `down`.  이 파일은 `up`과 `down`이라는 두개의 메소드를 가진 클래스가 있다. In the `up` method, you should make the desired changes to your database tables, and in the `down` method you simply reverse them. `up`메소드로 데이터베이스 테이블들을 의도한데로 수정할 수 있고 `down`메소드로 쉽게 수정한것들을 되돌릴 수 있다. 

Let's define a migration that looks like this: 다음과같이 마이그레이션을 정의해보자:

	public function up()
	{
		Schema::create('users', function($table)
		{
			$table->increments('id');
			$table->string('email')->unique();
			$table->string('name');
			$table->timestamps();
		});
	}

	public function down()
	{
		Schema::drop('users');
	}

Next, we can run our migrations from our terminal using the `migrate` command. 다음으로 터미널에서 `migrate`명령어를 사용해서 마이그레이션을 실행할 수 있다. Simply execute this command from the root of your project: 프로젝트 루트에서 다음과같은 명령어로 간단히 실행할 수 있다:

	php artisan migrate

If you wish to rollback a migration, you may issue the `migrate:rollback` command. 마이그레이션을 롤백하고싶다면 `migrate:rollback`명령어를 사용하자. Now that we have a database table, let's start pulling some data! 지금 데이터베이스 테이블이 생겼다. 이제 데이터를 넣어보자!

<a name="eloquent-orm"></a>
## Eloquent ORM 엘로퀀트 ORM

Laravel ships with a superb ORM: Eloquent. 라라벨은 엘로퀀트라는 훌륭한 ORM을 가지고 있다. If you have used the Ruby on Rails framework, you will find Eloquent familiar, as it follows the ActiveRecord ORM style of database interaction. 루비온레일즈를 사용한적이 있다면 엘로퀀트가 친숙할텐데 액티브레코드 ORM 스타일을 따르고 있기 때문이다.

First, let's define a model. 먼저 모델을 정의한다. An Eloquent model can be used to query an associated database table, as well as represent a given row within that table. 엘로퀀트 모델은 지정된 테이블 행뿐만 아니라 연관된 데이터베이스 테이블에 쿼리를 사용할 수 있다.  Don't worry, it will all make sense soon! 걱정하지마라 곧 이해가 될거다. Models are typically stored in the `app/models` directory. 모델들은 보통 `app/models`디렉토리에 저장된다. Let's define a `User.php` model in that directory like so: 그 디렉토리에 다음과 같이 `user.php`모델을 정의하자:

	class User extends Eloquent {}

Note that we do not have to tell Eloquent which table to use. 엘로퀀트에 어떤테이블을 사용할지 알릴필요가 없다는것에 주목하자. Eloquent has a variety of conventions, one of which is to use the plural form of the model name as the model's database table. Convenient! 엘로퀀트는 다양한 규약이 있고 그중에 하나는 모델의 데이터베이스 테이블 이름으로 복수형을 사용한다는것이다. 편리하게도!

Using your preferred database administration tool, insert a few rows into your `users` table, and we'll use Eloquent to retrieve them and pass them to our view. 선호하는 데이터베이스 관리도구로 `users`테이블에 몇개행을 넣고 엘로퀀트를 사용해서 다시 호출하여 뷰로 보내보자.

Now let's modify our `/users` route to look like this: `/user`라우트를 다음과같이 수정해보자:

	Route::get('users', function()
	{
		$users = User::all();

		return View::make('users')->with('users', $users);
	});

Let's walk through this route.  이 라우트로 실행해보자. First, the `all` method on the `User` model will retrieve all of the rows in the `users` table. 먼저  `user`모델에서 `all`메소드는 `user`테이블에 모든 행들을 가져온다. Next, we're passing these records to the view via the `with` method. 다음으로 이 레코드들을 `with`메소드로 뷰로 보낸다. The `with` method accepts a key and a value, and is used to make a piece of data available to a view. `with`메소드는 뷰에서 사용할 수 있는 키와 값을 받아들인다. 

Awesome. Now we're ready to display the users in our view! 대단해. 뷰에 사용자들을 보일 준비가 됐다.

<a name="displaying-data"></a>
## Displaying Data 데이터 보여주기
 
Now that we have made the `users` available to our view, we can display them like so: 뷰에서 사용할 수 있는 `users`를 다음과 같이 보여줄 수 있다:

	@extends('layout')

	@section('content')
		@foreach($users as $user)
			<p>{{ $user->name }}</p>
		@endforeach
	@stop

You may be wondering where to find our `echo` statements. `echo`문장으로는 이상해보일것이다. When using Blade, you may echo data by surrounding it with double curly braces. 블레이드를 사용할때 이중괄호로 감싸서 데이터들을 echo 처럼 보여줄 수 있다. It's a cinch. 식은죽먹기다. Now, you should be able to hit the `/users` route and see the names of your users displayed in the response. `/users`라우트를 통해서 사용자의 이름을 보여줄 수 있다. 

This is just the beginning. 이것은 시작에 불과하다. In this tutorial, you've seen the very basics of Laravel, but there are so many more exciting things to learn. 이 튜토리얼에서 라라벨의 아주 기본적이지만 훨씬 재미있는 배울것들이 있다. Keep reading through the documentation and dig deeper into the powerful features available to you in [Eloquent](/docs/eloquent) and [Blade](/docs/templates).  이 문서를 계속 읽어서 [Eloquent](/docs/eloquent) 와 [Blade](/docs/templates)의 강력한 기능들을 더깊게 체험해봐라.  Or, maybe you're more interested in [Queues](/docs/queues) and [Unit Testing](/docs/testing). 혹은  [Queues](/docs/queues) 와 [Unit Testing](/docs/testing)가 더 흥미로울수도 있다. Then again, maybe you want to flex your architecture muscles with the [IoC Container](/docs/ioc).  [IoC Container](/docs/ioc)로 유연한 아키텍저를 원할지도 모른다. The choice is yours! 선택은 당신의 몫이다!

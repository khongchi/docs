# Laravel Quickstart

- [Installation](#installation)
- [Routing](#routing)
- [Creating A View](#creating-a-view)
- [Creating A Migration](#creating-a-migration)
- [Eloquent ORM](#eloquent-orm)
- [Displaying Data](#displaying-data)

<a name="installation"></a>
## Installation

The Laravel framework utilizes [Composer](http://getcomposer.org) for installation and dependency management. 라라벨 프레임워크는 설치와 의존성 관리를 위해서 Composer를 사용한다. If you haven't already, start by [installing Composer](http://getcomposer.org/doc/00-intro.md).아직 설치하지 않았다면 Composer를 설치해라.

Now you can install Laravel by issuing the following command from your terminal:  터미널에서 다음 명령어를 입혁해서 라라벨을 설치할 수 있다.

	composer create-project laravel/laravel=4.0.* your-project-name --prefer-dist

This command will download and install a fresh copy of Laravel in a new `your-project-name` folder within your current directory.  이 명령은 현재 디렉토리에 `your-project-name` 폴더로 라라벨을 다운받아 설치하는것이다.

If you prefer, you can alternatively download a copy of the [Laravel repository from Github](https://github.com/laravel/laravel/archive/master.zip) manually. 원한다면 깃헙에 라라벨 저장소에서 수동으로 다운받아 설치 할 수 있다. Next run the `composer install` command in the root of your manually created project directory.  다음번에 수동으로 생성된 프로젝트 디렉토리 루트에서 `composer install`명령을 실행시켜라. This command will download and install the framework's dependencies. 이명령은 프레임워크의 의존성을 다운로드하여 설치할것이다. 

<a name="permissions"></a>
### Permissions

After installing Laravel, you may need to grant the web server write permissions to the `app/storage` directories. See the [Installation](/docs/installation) documentation for more details on configuration. 라라벨 설치 이후에 웹서버의 `app/storage`에 쓰기 권한을 부여하여야 한다. 설정에대한 더 자세한 사항은 [Installation](/docs/installation)을 참고하라.

<a name="directories"></a>
### Directory Structure

After installing the framework, take a glance around the project to familiarize yourself with the directory structure. 프레임워크 설치후 디렉토리 구조를 간단히 보자. The `app` directory contains folders such as `views`, `controllers`, and `models`. `app`디렉토리에는 `views`,`controllers`, `models`와 같은 폴더들이 있다. Most of your application's code will reside somewhere in this directory. 너의 어플리케이션 코드의 대부분은 이 디렉토리에 있을것이다.    You may also wish to explore the `app/config` directory and the configuration options that are available to you. `app/config`디렉토리와 적용할 수 있는 설정 옵션들을 찾아봐라.

<a name="routing"></a>
## Routing

To get started, let's create our first route. 시작으로 첫번째 라우트를 만들자. In Laravel, the simplest route is a route to a Closure. 라라벨에서 가장 간단한 라우는 클로저를 사용한 라우트이다. Pop open the `app/routes.php` file and add the following route to the bottom of the file: `app/routes.php`파일을 열어서 다음 라웉를 파일의 하단부에 추가해라.

	Route::get('users', function()
	{
		return 'Users!';
	});

Now, if you hit the `/users` route in your web browser, you should see `Users!` displayed as the response. 지금 웹브라우저에서 `/users`라고 치면 `users!`가 출력될것이다. Great! You've just created your first route. 대단하다! 너의 첫번째 라우트를 만든것이다.

Routes can also be attached to controller classes. For example:  라우트들은 controller classes에 attach될 수 도 있다. 

	Route::get('users', 'UserController@getIndex');

This route informs the framework that requests to the `/users` route should call the `getIndex` method on the `UserController` class. 이 라우트는 프레임워크에 `/users`로 리퀘스트하면 `userController`에 `getIndex`메소드를 call하라고 알려주는 것입니다. For more information on controller routing, check out the [controller documentation](/docs/controllers).controller routing에대한 더 자세한 정보는 [controller documentation](/docs/controllers)를 확인해라.

<a name="creating-a-view"></a>
## Creating A View

Next, we'll create a simple view to display our user data. user data를 보여주는 간단한 view를 만들어 보자. Views live in the `app/views` directory and contain the HTML of your application. `app/views`에 application의 HTML을 가지고 있는 view들이 있다.  We're going to place two new views in this directory: `layout.blade.php` and `users.blade.php`.  우리는 이 디렉토리에 2개의 새로운 view들인 `layout.blade.php`와 `users.blade.php`를 만들것이다.  First, let's create our `layout.blade.php` file: 먼저 layout.blade.php file을 만들자.

	<html>
		<body>
			<h1>Laravel Quickstart</h1>

			@yield('content')
		</body>
	</html>

Next, we'll create our `users.blade.php` view: 다음으로 users.blade.php view를 만들자

	@extends('layout')

	@section('content')
		Users!
	@stop

Some of this syntax probably looks quite strange to you. 아마도 이 문법이 아주 낯설것이다. That's because we're using Laravel's templating system: Blade. 우리가 라라벨의 templating syste인 블레이드를 사용하기 때문이다. Blade is very fast, because it is simply a handful of regular expressions that are run against your templates to compile them to pure PHP. 블레이드는 PHP만으로 컴파일되는것에비해 간단학게 정규표현식들을 매우 빠르다. Blade provides powerful functionality like template inheritance, as well as some syntax sugar on typical PHP control structures such as `if` and `for`. Check out the [Blade documentation](/docs/templates) for more details.

Now that we have our views, let's return it from our `/users` route. Instead of returning `Users!` from the route, return the view instead:

	Route::get('users', function()
	{
		return View::make('users');
	});

Wonderful! Now you have setup a simple view that extends a layout. Next, let's start working on our database layer.

<a name="creating-a-migration"></a>
## Creating A Migration

To create a table to hold our data, we'll use the Laravel migration system. Migrations let you expressively define modifications to your database, and easily share them with the rest of your team.

First, let's configure a database connection. You may configure all of your database connections from the `app/config/database.php` file. By default, Laravel is configured to use MySQL, and you will need to supply connection credentials within the database configuration file. If you wish, you may change the `driver` option to `sqlite` and it will use the SQLite database included in the `app/database` directory.

Next, to create the migration, we'll use the [Artisan CLI](/docs/artisan). From the root of your project, run the following from your terminal:

	php artisan migrate:make create_users_table

Next, find the generated migration file in the `app/database/migrations` folder. This file contains a class with two methods: `up` and `down`. In the `up` method, you should make the desired changes to your database tables, and in the `down` method you simply reverse them.

Let's define a migration that looks like this:

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

Next, we can run our migrations from our terminal using the `migrate` command. Simply execute this command from the root of your project:

	php artisan migrate

If you wish to rollback a migration, you may issue the `migrate:rollback` command. Now that we have a database table, let's start pulling some data!

<a name="eloquent-orm"></a>
## Eloquent ORM

Laravel ships with a superb ORM: Eloquent. If you have used the Ruby on Rails framework, you will find Eloquent familiar, as it follows the ActiveRecord ORM style of database interaction.

First, let's define a model. An Eloquent model can be used to query an associated database table, as well as represent a given row within that table. Don't worry, it will all make sense soon! Models are typically stored in the `app/models` directory. Let's define a `User.php` model in that directory like so:

	class User extends Eloquent {}

Note that we do not have to tell Eloquent which table to use. Eloquent has a variety of conventions, one of which is to use the plural form of the model name as the model's database table. Convenient!

Using your preferred database administration tool, insert a few rows into your `users` table, and we'll use Eloquent to retrieve them and pass them to our view.

Now let's modify our `/users` route to look like this:

	Route::get('users', function()
	{
		$users = User::all();

		return View::make('users')->with('users', $users);
	});

Let's walk through this route. First, the `all` method on the `User` model will retrieve all of the rows in the `users` table. Next, we're passing these records to the view via the `with` method. The `with` method accepts a key and a value, and is used to make a piece of data available to a view.

Awesome. Now we're ready to display the users in our view!

<a name="displaying-data"></a>
## Displaying Data

Now that we have made the `users` available to our view. We can display them like so:

	@extends('layout')

	@section('content')
		@foreach($users as $user)
			<p>{{ $user->name }}</p>
		@endforeach
	@stop

You may be wondering where to find our `echo` statements. When using Blade, you may echo data by surrounding it with double curly braces. It's a cinch. Now, you should be able to hit the `/users` route and see the names of your users displayed in the response.

This is just the beginning. In this tutorial, you've seen the very basics of Laravel, but there are so many more exciting things to learn. Keep reading through the documentation and dig deeper into the powerful features available to you in [Eloquent](/docs/eloquent) and [Blade](/docs/templates). Or, maybe you're more interested in [Queues](/docs/queues) and [Unit Testing](/docs/testing). Then again, maybe you want to flex your architecture muscles with the [IoC Container](/docs/ioc). The choice is yours!

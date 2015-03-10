# Controllers 컨트롤러

- [Basic Controllers 기본 컨트롤러](#basic-controllers)
- [Controller Filters 컨트롤러 필터](#controller-filters)
- [RESTful Controllers RESTful 컨트롤러](#restful-controllers)
- [Resource Controllers 리소스 컨트롤러](#resource-controllers)
- [Handling Missing Methods 찾을 수 없는 메소드 처리](#handling-missing-methods)

<a name="basic-controllers"></a>
## Basic Controllers 기본 컨트롤러

Instead of defining all of your route-level logic in a single `routes.php` file, you may wish to organize this behavior using Controller classes. 모든 라우팅 로직을 하나의 `routes.php` 파일에 정의하는 대신 컨트롤러 클래스를 통해서 구성할 수도 있습니다. Controllers can group related route logic into a class, as well as take advantage of more advanced framework features such as automatic [dependency injection](/docs/ioc). 컨트롤러는 관련있는 라우팅 로직을 클래스에 정리하여 그룹핑하는 동시에 자동 [의존성 주입](/docs/ioc)등 프레임 워크의 고급 기능을 사용하는 이점이 있습니다.

Controllers are typically stored in the `app/controllers` directory, and this directory is registered in the `classmap` option of your `composer.json` file by default. 컨트롤러는 일반적으로 `app/controllers` 디렉토리에 위치 시키며, 이 디렉토리는 기본적으로 `composer.json` 파일의`classmap` 옵션에 등록되어 있습니다.

Here is an example of a basic controller class:
기본적인 컨트롤러 클래스의 예제 입니다: 

	class UserController extends BaseController {

		/**
		 * Show the profile for the given user.
		 */
		public function showProfile($id)
		{
			$user = User::find($id);

			return View::make('user.profile', array('user' => $user));
		}

	}

All controllers should extend the `BaseController` class. 모든 컨트롤러들은 `BaseController` 클래스를 상속해야 합니다. The `BaseController` is also stored in the `app/controllers` directory, and may be used as a place to put shared controller logic. `BaseController` 또한 `app/controllers` 디렉토리에 있으며, 모든 컨트롤러 에서 공유하고자 하는 로직을 추가하는 데도 사용할 수 있습니다. The `BaseController` extends the framework's `Controller` class. `BaseController` 는 프레임워크의 `Controller` 클래스를 상속 받고 있습니다. Now, We can route to this controller action like so: 이 컨트롤러를 사용해서 다음과 같이 라우팅 경로를 지정할 수 있습니다:

	Route::get('user/{id}', 'UserController@showProfile');

If you choose to nest or organize your controller using PHP namespaces, simply use the fully qualified class name when defining the route: 만약 컨트롤러가 자체적인 네임스페이스를 사용하고 있다면 다음과 같이 전체 네임스페이스를 포함한 전체 경로를 사용하면 됩니다.

	Route::get('foo', 'Namespace\FooController@method');

You may also specify names on controller routes:
또한 컨트롤러의 라우팅에 이름을 부여할 수도 있습니다.

	Route::get('foo', array('uses' => 'FooController@method',
											'as' => 'name'));

To generate a URL to a controller action, you may use the `URL::action` method: 컨트롤러의 액션에 해당하는 URL 생성하려면 `URL::action` 메소드를 사용합니다: 

	$url = URL::action('FooController@method');

You may access the name of the controller action being run using the `currentRouteAction` method: 현재 실행중인 컨트롤러 액션의 이름을 얻고자 한다면 `currentRouteAction` 메소드를 사용하면 됩니다:

	$action = Route::currentRouteAction();

<a name="controller-filters"></a>
## Controller Filters 컨트롤러 필터

[Filters](/docs/routing#route-filters) may be specified on controller routes similar to "regular" routes: 컨트롤러 라우트에 "일반적인" 라우팅과 같이 [필터](/docs/routing#route-filters)를 지정할 수 있습니다:

	Route::get('profile', array('before' => 'auth',
				'uses' => 'UserController@showProfile'));

However, you may also specify filters from within your controller:
그리고, 컨트롤러 내부에 필터를 지정할 수도 있습니다.

	class UserController extends BaseController {

		/**
		 * Instantiate a new UserController instance.
		 */
		public function __construct()
		{
			$this->beforeFilter('auth', array('except' => 'getLogin'));

			$this->beforeFilter('csrf', array('on' => 'post'));

			$this->afterFilter('log', array('only' =>
								array('fooAction', 'barAction')));
		}

	}

You may also specify controller filters inline using a Closure:
클로저를 사용해 인라인 컨트롤러 필터를 지정할 수도 있습니다. 

	class UserController extends BaseController {

		/**
		 * Instantiate a new UserController instance.
		 */
		public function __construct()
		{
			$this->beforeFilter(function()
			{
				//
			});
		}

	}

<a name="restful-controllers"></a>
## RESTful Controllers RESTful 컨트롤러

Laravel allows you to easily define a single route to handle every action in a controller using simple, REST naming conventions. 라라벨에서는 REST풀한 라우팅 룰을 처리하는 컨트롤러 액션을 정의하기 위한 손쉬운 방법을 제공합니다. First, define the route using the `Route::controller` method:
먼저 `Route::controller` 메소드를 사용하여 경로를 지정합니다.

**Defining A RESTful Controller REST풀 컨트롤러 정의하기 **

	Route::controller('users', 'UserController');

The `controller` method accepts two arguments. `controller` 메소드는 두개의 인자를 넘겨 받도록 되어 있습니다. The first is the base URI the controller handles, while the second is the class name of the controller. 첫번째 인자는 컨트롤러로 제어할 URI이고, 두번째는 컨트롤러의 클래스명을 의미합니다. Next, just add methods to your controller, prefixed with the HTTP verb they respond to: 이어서 해당하는 HTTP 메소드 이름을 접두어로 (get, post..) 사용하는 형태로 컨트롤러의 메소드를 추가합니다: 

	class UserController extends BaseController {

		public function getIndex()
		{
			//
		}

		public function postProfile()
		{
			//
		}

	}

The `index` methods will respond to the root URI handled by the controller, which, in this case, is `users`. 위의 경우에 컨트롤러의 `index` 메소드는 `users` URI에 대한 루트 주소에 대한 결과를 반환합니다. 

If your controller action contains multiple words, you may access the action using "dash" syntax in the URI. 만약 컨트롤러의 메소드가 여러개의 단어로 구성되어 진 형태라면 “-“을 통해서 접속할 수 있는 URI를 제공하게 됩니다. For example, the following controller action on our `UserController` would respond to the `users/admin-profile` URI: 예를들어 `UserController`에 다음과 같은 액션이 정의되었다면 URI는 `users/admin-profile` 과 같이 구성됩니다. 

	public function getAdminProfile() {}

<a name="resource-controllers"></a>
## Resource Controllers 리소스 컨트롤러

Resource controllers make it easier to build RESTful controllers around resources. 리소스 컨트롤러는 리소스에 대한 RESTful 컨트롤러를 손쉽게 구성할 수 있게 해줍니다. For example, you may wish to create a controller that manages "photos" stored by your application. 예를 들어 여러분은 어플리케이션에서 “photos” 를 관리하는 컨트롤러를 생성할 수 있습니다. Using the `controller:make` command via the Artisan CLI and the `Route::resource` method, we can quickly create such a controller. `controller:make` Artisan 커맨드라인 명령어와 `Route::resource` 메소드를 사용하면 손쉽게 컨트롤러를 생성할 수 있습니다. 

To create the controller via the command line, execute the following command:
커맨드라인에서 컨트롤러를 생성하기 위한 명령어는 다음과 같습니다. : 

	php artisan controller:make PhotoController

Now we can register a resourceful route to the controller:
이제 생성된 컨트롤러에 리소스풀 라우트를 등록하면 됩니다. 

	Route::resource('photo', 'PhotoController');

This single route declaration creates multiple routes to handle a variety of RESTful actions on the photo resource. 한번의 선언만으로 photo 를 구성하는 RESTful 한 액션에 대한 다양한 라우트를 설정할 수 있습니다. Likewise, the generated controller will already have stubbed methods for each of these actions with notes informing you which URIs and verbs they handle. 직접 구성한것과 마찬가지로, 생성 된 컨트롤러는 이미의 URI와 동사가 처리를 알려주는 메모와 함께 각각의 액션에 대한 방법을 구성 것입니다.

**Actions Handled By Resource Controller**
**리소스풀 컨트롤러에 의해서 구성된 액션들**

Verb      | Path                        | Action       | Route Name
----------|-----------------------------|--------------|---------------------
GET       | /resource                   | index        | resource.index
GET       | /resource/create            | create       | resource.create
POST      | /resource                   | store        | resource.store
GET       | /resource/{resource}        | show         | resource.show
GET       | /resource/{resource}/edit   | edit         | resource.edit
PUT/PATCH | /resource/{resource}        | update       | resource.update
DELETE    | /resource/{resource}        | destroy      | resource.destroy

Sometimes you may only need to handle a subset of the resource actions:
어쩌면 몇개의 액션만 필요할 수도 있습니다. 

	php artisan controller:make PhotoController --only=index,show

	php artisan controller:make PhotoController --except=index

And, you may also specify a subset of actions to handle on the route:
그리고 마찬가지로 몇개의 액션만 라우트에 등록할 수도 있습니다. 

	Route::resource('photo', 'PhotoController',
					array('only' => array('index', 'show')));

	Route::resource('photo', 'PhotoController',
					array('except' => array('create', 'store', 'update', 'delete')));

<a name="handling-missing-methods"></a>
## Handling Missing Methods 찾을 수 없는 메소드의 처리

A catch-all method may be defined which will be called when no other matching method is found on a given controller. 컨트롤러에서 일치하는 메소드를 찾을 수 없는 경우에 호출되는 메소드가 존재합니다.  The method should be named `missingMethod`, and receives the parameter array for the request as its only argument: `missingMethod`라고 불리는 이 메소드는 요청에 대한 파라미터가 있는 경우 이를 배열의 형태로 넘겨 받습니다. 

**Defining A Catch-All Method 메소드 정의 방법**


	public function missingMethod($parameters)
	{
		//
	}

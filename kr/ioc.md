# IoC Container Ioc 컨테이너

- [Introduction 소개](#introduction)
- [Basic Usage 기본 사용법](#basic-usage)
- [Automatic Resolution 자동 레졸루션](#automatic-resolution)
- [Practical Usage 실제 사용법](#practical-usage)
- [Service Providers 서비스 프로바이더](#service-providers)
- [Container Events 컨테이너 이벤트](#container-events)

<a name="introduction"></a>
## Introduction 소개

The Laravel inversion of control container is a powerful tool for managing class dependencies. 라라벨의 IOC 컨테이너(inversio of control - 제어 역전)는 클래스의 의존성을 관리하는 강력한 도구 입니다. Dependency injection is a method of removing hard-coded class dependencies. 의존성 주입(DI-Dependency Injection)은 하드코딩된 클래스간의 의존성을 제거해줍니다. Instead, the dependencies are injected at run-time, allowing for greater flexibility as dependency implementations may be swapped easily. 이를 대신해서 클래스간의 의존성은 어플리케이션의 런타임시에 의존 객체가 삽입되어 강략한 유연함을 제공하게 되고, 이로 인해서 클래스간의 의존성 구현을 손쉽게 변경할 수 있게 됩니다.

Understanding the Laravel IoC container is essential to building a powerful, large application, as well as for contributing to the Laravel core itself. 라라벨의 코어 시스템과 동일하게 IoC 컨테이너를 이해하는 것은 더 큰 규모의 라라벨 어플리케이션을 작성하는데 핵심적인 부분입니다.

<a name="basic-usage"></a>
## Basic Usage 기본 사용법

There are two ways the IoC container can resolve dependencies: IoC 컨테이너를 사용하여 의존성을 푸는 방법은 두가지의 방식이 있습니다. via Closure callbacks or automatic resolution. 바로 클로저 콜백을 사용하는 방식과 자동 레졸루션을 사용하는 방식입니다. First, we'll explore Closure callbacks. 첫번째로 클로저 콜백방식을 살펴보겠습니다. First, a "type" may be bound into the container: 다음은 클로저 콜백으로 컨테이너에 바인딩 하는 방법 입니다. 

**Binding A Type Into The Container 클로저 콜백 컨테이너 바인딩**

	App::bind('foo', function($app)
	{
		return new FooBar;
	});

**Resolving A Type From The Container 바인딩한 객체를 획득하는 방법**

	$value = App::make('foo');

When the `App::make` method is called, the Closure callback is executed and the result is returned. `App::make` 메소드가 실행되면, 바인딩 해둔 클로저 콜백에 의해서 실행된 결과가 리턴됩니다.

Sometimes, you may wish to bind something into the container that should only be resolved once, and the same instance should be returned on subsequent calls into the container: 
위의 예제는 매번 새로운 FooBar 인스턴스를 리턴하는데, 때로는 컨테이너에 바인딩 된후 서브 시퀀스가 호출 할 때 동일한 인스턴스가 리턴되는 것을 원할 수도 있습니다:

**Binding A "Shared" Type Into The Container**

	App::singleton('foo', function()
	{
		return new FooBar;
	});

You may also bind an existing object instance into the container using the `instance` method:
또는 이미 존재하는 객체의 인스턴스를 `instance` 메소드를 사용하여 컨테이너에 바인딩 할 수도 있습니다:

**Binding An Existing Instance Into The Container 이미 존재하는 인스턴스를 컨테이너에 바인딩**

	$foo = new Foo;

	App::instance('foo', $foo);

<a name="automatic-resolution"></a>
## Automatic Resolution 자동 레졸루션 

The IoC container is powerful enough to resolve classes without any configuration at all in many scenarios. 바인딩을 따로 하지 않더라도 IoC 컨테이너는 클래스 의존성을 자동으로 해결 해 줍니다. For example: 예를 들면:

**Resolving A Class 자동으로 의존성 해결 **

	class FooBar {

		public function __construct(Baz $baz)
		{
			$this->baz = $baz;
		}

	}

	$fooBar = App::make('FooBar');

Note that even though we did not register the FooBar class in the container, the container will still be able to resolve the class, even injecting the `Baz` dependency automatically!
컨테이너에 `FooBar` 클래스를 등록 하지 않았더라도, 컨테이너가 자동으로 알아서 객체 인스턴스를 돌려주며, 심지어 내부의 `Baz` 클래스까지도 자동으로 의존성을 해결해 줍니다. 

When a type is not bound in the container, it will use PHP's Reflection facilities to inspect the class and read the constructor's type-hints. 컨테이너에 실제 어떠한 클래스 타입인지 바인딩 되어 있지 않더라도, PHP 고유의 리플렉션 기능을 사용하여 클래스를 점검하고 생성자의 타입을 읽어들입니다. Using this information, the container can automatically build an instance of the class. 이 정보를 통해서 컨테이너는 자동으로 클래스의 인스턴스를 생성할 수 있습니다.


However, in some cases, a class may depend on an interface implementation, not a "concrete type". When this is the case, the `App::bind` method must be used to inform the container which interface implementation to inject:
때때로 의존성을 관리하려는 객체의 형태가 인터페이스의 형태로 실제적이지 않을 수도 있습니다. 이경우 `App::bind` 메소드는 어떠한 인터페이스의 구현(implementation)이 삽입되는지 알려줍니다. 다음의 예를 보면 : 

**Binding An Interface To An Implementation 인터페이스에 실제 구현된 객체를 바인딩**

	App::bind('UserRepositoryInterface', 'DbUserRepository');

Now consider the following controller:
컨트롤러에서 사용된 형태:

	class UserController extends BaseController {

		public function __construct(UserRepositoryInterface $users)
		{
			$this->users = $users;
		}

	}

Since we have bound the `UserRepositoryInterface` to a concrete type, the `DbUserRepository` will automatically be injected into this controller when it is created. `UserRepositoryInterface`가 `DbUserRepository` 의 형태로 바인딩 되었기 때문에 `UserController`가 생성될때 자동으로 `DbUserRepository`가 삽입됩니다.

<a name="practical-usage"></a>
## Practical Usage 실제 사용법

Laravel provides several opportunities to use the IoC container to increase the flexibility and testability of your application. 라라벨은 IoC 컨테이너를 통해서 어플리케이션이 보다 유연해지고 테스트가 가능하도록 만듭니다. One primary example is when resolving controllers. 예를 들자면 컨트롤러의 경우가 그러합니다. All controllers are resolved through the IoC container, meaning you can type-hint dependencies in a controller constructor, and they will automatically be injected. 모든 컨트롤러들는 IoC 컨테이너를 통해서 의존성을 해결하는데, 이것은 컨트롤러 생성자 안에서 의존성 유형을 알아 내고 이러한 의존성이 자동으로 삽입됨을 의미합니다. 

**Type-Hinting Controller Dependencies 유형을 알게하는 컨트롤러 의존성**

	class OrderController extends BaseController {

		public function __construct(OrderRepository $orders)
		{
			$this->orders = $orders;
		}

		public function getIndex()
		{
			$all = $this->orders->all();

			return View::make('orders', compact('all'));
		}

	}

In this example, the `OrderRepository` class will automatically be injected into the controller. 이 예제에서 `OrderRepository` 객체는 자동으로 컨트롤러 안에 주입됩니다. This means that when [unit testing](/docs/testing) a "mock" `OrderRepository` may be bound into the container and injected into the controller, allowing for painless stubbing of database layer interaction. 이것은 [유닛 테스트](/docs/testing)가 수행될때 모의 `OrderRepository`가 컨테이너에 바인딩되어, 컨트롤러에 삽입되게 되어지고 어렵지 않게 데이타베이스 레이어를 고려하지 않도록 할 수 있습니다. (스터빙)

(번역주 : 스터빙 - 하위 모듈의 상세한 구조에도 불구하고 그 입출력 조건을 모의하기 위한 모듈로, 톱 다운으로 행하는 설계나 테스트에 있어서 어느 추상화 레벨에서의 기술(記述)을 위해 불필요한 하위 모듈 내부의 기술을 없애기 위해 사용하는 것)

[Filters](/docs/routing#route-filters), [composers](/docs/responses#view-composers), and [event handlers](/docs/events#using-classes-as-listeners) may also be resolved out of the IoC container. [필터](/docs/routing#route-filters), [컴포저](/docs/responses#view-composers), 그리고 [이벤트 핸들러](/docs/events#using-classes-as-listeners) 또한 IoC 컨트롤에 의해서 의존성 해결을 할 수 있습니다. When registering them, simply give the name of the class that should be used: 그것들을 등록할 때, 다음과 같은 형식으로 사용되어 집니다.

**Other Examples Of IoC Usage IoC 의 다른 사용 예제 **

	Route::filter('foo', 'FooFilter');

	View::composer('foo', 'FooComposer');

	Event::listen('foo', 'FooHandler');

<a name="service-providers"></a>
## Service Providers 서비스 프로바이더 

Service providers are a great way to group related IoC registrations in a single location. 서비스 프로바이더는 관계된 IoC 등록을 한곳에서 처리할 수 있게 합니다. Think of them as a way to bootstrap components in your application. 응용 프로그램에서 구성 요소를 부트스트랩하는 방법으로 생각하면됩니다. Within a service provider, you might register a custom authentication driver, register your application's repository classes with the IoC container, or even setup a custom Artisan command. 서비스 프로바이더를 통해서 사용자 정의된 인증 드라이버를 등록하거나, 어플리케이션에서 사용하는 레파지토리 클래스를 등록하거나 또는 사용자 정의된 아티즌 명령어를 등록할 수도 있습니다. 

In fact, most of the core Laravel components include service providers. 실제로 Laravel 코어 컴포넌트의 많은 부분은 서비스 프로바이더를 포함하고 있습니다. All of the registered service providers for your application are listed in the `providers` array of the `app/config/app.php` configuration file. 어플리케이션에 등록된 서비스 프로바이더들은 `app/config/app.php` 설정 파일의 `Providers` 배열에서 확인할 수 있습니다.

To create a service provider, simply extend the `Illuminate\Support\ServiceProvider` class and define a `register` method: 
새롭게 간단한 서비스 프로바이더를 생성하기 위해서는 `Illuminate\Support\ServiceProvider` 클래스를 상속하고 `register` 메소드를 정의 하면 됩니다 : 

**Defining A Service Provider 서비스 프로바이더 정의하기 **

	use Illuminate\Support\ServiceProvider;

	class FooServiceProvider extends ServiceProvider {

		public function register()
		{
			$this->app->bind('foo', function()
			{
				return new Foo;
			});
		}

	}

Note that in the `register` method, the application IoC container is available to you via the `$this->app` property. `register` 메소드 안에서 어플리케이션 IoC 컨테이너는 `$this->app`을 통해서 사용이 가능합니다. Once you have created a provider and are ready to register it with your application, simply add it to the `providers` array in your `app` configuration file. 일단 당신이 프로바이더를 생성하고 응용 프로그램에 등록 할 준비가 되었다면 간단하게 `app` 설정 파일의 `Providers` 배열에 추가하면 됩니다. 

You may also register a service provider at run-time using the `App::register` method:
다른 방법으로는 런타임에서 `App::register` 메소드를 사용하여 서비스 프로바이더를 등록할 수 있습니다. 

**Registering A Service Provider At Run-Time 런타임에 서비스 프로바이더를 등록하기 **

	App::register('FooServiceProvider');

<a name="container-events"></a>
## Container Events 컨테이너 이벤트

The container fires an event each time it resolves an object. 컨테이너는 객체의 의존성 해결을 수행할 때 마다 이벤트를 발생시킵니다. You may listen to this event using the `resolving` method: `resolving` 메소드를 사용하여 이 이벤트들에 대응할 수 있습니다. 

**Registering A Resolving Listener 의존성 해결 리스터 등록 **

	App::resolvingAny(function($object)
	{
		//
	});

	App::resolving('foo', function($foo)
	{
		//
	});

Note that the object that was resolved will be passed to the callback. 의존객체가 주입되어 의존성이 해결될 때 마다 해당 콜백이 수행될 것입니다. 
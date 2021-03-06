# Service Container 서비스 컨테이너

- [Introduction 소개](#introduction)
- [Basic Usage 기본 사용법](#basic-usage)
- [Binding Interfaces To Implementations 인터페이스를 구현체에 바인딩 하는 법](#binding-interfaces-to-implementations)
- [Contextual Binding 상황에 맞는 바인딩](#contextual-binding)
- [Tagging 태깅](#tagging)
- [Practical Applications 실제 어플리케이션](#practical-applications)
- [Container Events 컨테이너 이벤트](#container-events)

<a name="introduction"></a>
## Introduction 소개

The Laravel service container is a powerful tool for managing class dependencies. 라라벨의 서비스 컨테이너는 클래스의 의존성을 관리하는 강력한 도구 입니다. Dependency injection is a fancy word that essentially means this: 의존성 주입이라는 멋진 말의 의미는 다음과 같습니다: class dependencies are "injected" into the class via the constructor or, in some cases, "setter" methods. 클래스간의 의존성은 클래스 생성될 때 또는 경우에 따라 "setter" 메소드에 의해서 "주입" 된다는 의미입니다.  

Let's look at a simple example:
간단한 예제를 들어 봅시다. 

	<?php namespace App\Handlers\Commands;

	use App\User;
	use App\Commands\PurchasePodcast;
	use Illuminate\Contracts\Mail\Mailer;

	class PurchasePodcastHandler {

		/**
		 * The mailer implementation.
		 */
		protected $mailer;

		/**
		 * Create a new instance.
		 *
		 * @param  Mailer  $mailer
		 * @return void
		 */
		public function __construct(Mailer $mailer)
		{
			$this->mailer = $mailer;
		}

		/**
		 * Purchase a podcast.
		 *
		 * @param  PurchasePodcastCommand  $command
		 * @return void
		 */
		public function handle(PurchasePodcastCommand $command)
		{
			//
		}

	}

In this example, the `PurchasePodcast` command handler needs to send e-mails when a podcast is purchased. 위의 예제에서 `PurchasePodcast` 명령어는 podcast 가 주문이 되면 이메일을 보내야 할 필요가 있습니다. So, we will **inject** a service that is able to send e-mails. 그래서 우리는 이메일을 보내기 위한 서비스를 **주입** 시킬것입니다. Since the service is injected, we are able to easily swap it out with another implementation. 서비스가 주입되었기 때문에 원하는 경우 쉽게 다른 구현체로 바꿀 수 있습니다. We are also able to easily "mock", or create a dummy implementation of the mailer when testing our application. 또한 어플리케이션을 테스팅 할 때 손쉽게 "목킹" 할수 있고 메일러의 더미 구현체를 만들수 있습니다. 

A deep understanding of the Laravel service container is essential to building a powerful, large application, as well as for contributing to the Laravel core itself.
라라벨 서비스 컨테이너를 깊이 이해하는 것은 강력하고 큰 애플리케이션을 구축 할 때나 라라벨 코어에 공헌하기 위해서  중요한 부분입니다.

<a name="basic-usage"></a>
## Basic Usage 기본 사용법

### Binding 바인딩

Almost all of your service container bindings will be registered within [service providers](/docs/5.0/providers), so all of these examples will demonstrate using the container in that context. However, if you need an instance of the container elsewhere in your application, such as a factory, you may type-hint the `Illuminate\Contracts\Container\Container` contract and an instance of the container will be injected for you. Alternatively, you may use the `App` facade to access the container.

#### Registering A Basic Resolver

Within a service provider, you always have access to the container via the `$this->app` instance variable.

There are several ways the service container can register dependencies, including Closure callbacks and binding interfaces to implementations. First, we'll explore Closure callbacks. A Closure resolver is registered in the container with a key (typically the class name) and a Closure that returns some value:

	$this->app->bind('FooBar', function($app)
	{
		return new FooBar($app['SomethingElse']);
	});

#### Registering A Singleton

Sometimes, you may wish to bind something into the container that should only be resolved once, and the same instance should be returned on subsequent calls into the container:

	$this->app->singleton('FooBar', function($app)
	{
		return new FooBar($app['SomethingElse']);
	});

#### Binding An Existing Instance Into The Container

You may also bind an existing object instance into the container using the `instance` method. The given instance will always be returned on subsequent calls into the container:

	$fooBar = new FooBar(new SomethingElse);

	$this->app->instance('FooBar', $fooBar);

### Resolving

There are several ways to resolve something out of the container. First, you may use the `make` method:

	$fooBar = $this->app->make('FooBar');

Secondly, you may use "array access" on the container, since it implements PHP's `ArrayAccess` interface:

	$fooBar = $this->app['FooBar'];

Lastly, but most importantly, you may simply "type-hint" the dependency in the constructor of a class that is resolved by the container, including controllers, event listeners, queue jobs, filters, and more. The container will automatically inject the dependencies:

	<?php namespace App\Http\Controllers;

	use Illuminate\Routing\Controller;
	use App\Users\Repository as UserRepository;

	class UserController extends Controller {

		/**
		 * The user repository instance.
		 */
		protected $users;

		/**
		 * Create a new controller instance.
		 *
		 * @param  UserRepository  $users
		 * @return void
		 */
		public function __construct(UserRepository $users)
		{
			$this->users = $users;
		}

		/**
		 * Show the user with the given ID.
		 *
		 * @param  int  $id
		 * @return Response
		 */
		public function show($id)
		{
			//
		}

	}

<a name="binding-interfaces-to-implementations"></a>
## Binding Interfaces To Implementations

### Injecting Concrete Dependencies

A very powerful features of the service container is its ability to bind an interface to a given implementation. For example, perhaps our application integrates with the [Pusher](https://pusher.com) web service for sending and receiving real-time events. If we are using Pusher's PHP SDK, we could inject an instance of the Pusher client into a class:

	<?php namespace App\Handlers\Commands;

	use App\Commands\CreateOrder;
	use Pusher\Client as PusherClient;

	class CreateOrderHandler {

		/**
		 * The Pusher SDK client instance.
		 */
		protected $pusher;

		/**
		 * Create a new order handler instance.
		 *
		 * @param  PusherClient  $pusher
		 * @return void
		 */
		public function __construct(PusherClient $pusher)
		{
			$this->pusher = $pusher;
		}

		/**
		 * Execute the given command.
		 *
		 * @param  CreateOrder  $command
		 * @return void
		 */
		public function execute(CreateOrder $command)
		{
			//
		}

	}

In this example, it is good that we are injecting the class dependencies; however, we are tightly coupled to the Pusher SDK. If the Pusher SDK methods change or we decide to switch to a new event service entirely, we will need to change our `CreateOrderHandler` code.

### Program To An Interface

In order to "insulate" the `CreateOrderHandler` against changes to event pushing, we could define an `EventPusher` interface and a `PusherEventPusher` implementation:

	<?php namespace App\Contracts;

	interface EventPusher {

		/**
		 * Push a new event to all clients.
		 *
		 * @param  string  $event
		 * @param  array  $data
		 * @return void
		 */
		public function push($event, array $data);

	}

Once we have coded our `PusherEventPusher` implementation of this interface, we can register it with the service container like so:

	$this->app->bind('App\Contracts\EventPusher', 'App\Services\PusherEventPusher');

This tells the container that it should inject the `PusherEventPusher` when a class needs an implementation of `EventPusher`. Now we can type-hint the `EventPusher` interface in our constructor:

		/**
		 * Create a new order handler instance.
		 *
		 * @param  EventPusher  $pusher
		 * @return void
		 */
		public function __construct(EventPusher $pusher)
		{
			$this->pusher = $pusher;
		}

<a name="contextual-binding"></a>
## Contextual Binding

Sometimes you may have two classes that utilize the same interface, but you wish to inject different implementations into each class. For example, when our system receives a new Order, we may want to send an event via [PubNub](http://www.pubnub.com/) rather than Pusher. Laravel provides a simple, fluent interface for defining this behavior:

	$this->app->when('App\Handlers\Commands\CreateOrderHandler')
	          ->needs('App\Contracts\EventPusher')
	          ->give('App\Services\PubNubEventPusher');

<a name="tagging"></a>
## Tagging

Occasionally, you may need to resolve all of a certain "category" of binding. For example, perhaps you are building a report aggregator that receives an array of many different `Report` interface implementations. After registering the `Report` implementations, you can assign them a tag using the `tag` method:

	$this->app->bind('SpeedReport', function()
	{
		//
	});

	$this->app->bind('MemoryReport', function()
	{
		//
	});

	$this->app->tag(['SpeedReport', 'MemoryReport'], 'reports');

Once the services have been tagged, you may easily resolve them all via the `tagged` method:

	$this->app->bind('ReportAggregator', function($app)
	{
		return new ReportAggregator($app->tagged('reports'));
	});

<a name="practical-applications"></a>
## Practical Applications

Laravel provides several opportunities to use the service container to increase the flexibility and testability of your application. One primary example is when resolving controllers. All controllers are resolved through the service container, meaning you can type-hint dependencies in a controller constructor, and they will automatically be injected.

	<?php namespace App\Http\Controllers;

	use Illuminate\Routing\Controller;
	use App\Repositories\OrderRepository;

	class OrdersController extends Controller {

		/**
		 * The order repository instance.
		 */
		protected $orders;

		/**
		 * Create a controller instance.
		 *
		 * @param  OrderRepository  $orders
		 * @return void
		 */
		public function __construct(OrderRepository $orders)
		{
			$this->orders = $orders;
		}

		/**
		 * Show all of the orders.
		 *
		 * @return Response
		 */
		public function index()
		{
			$orders = $this->orders->all();

			return view('orders', ['orders' => $orders]);
		}

	}

In this example, the `OrderRepository` class will automatically be injected into the controller. This means that a "mock" `OrderRepository` may be bound into the container when [unit testing](/docs/5.0/testing), allowing for painless stubbing of database layer interaction.

#### Other Examples Of Container Usage

Of course, as mentioned above, controllers are not the only classes Laravel resolves via the service container. You may also type-hint dependencies on route Closures, filters, queue jobs, event listeners, and more. For examples of using the service container in these contexts, please refer to their documentation.

<a name="container-events"></a>
## Container Events 컨테이너 이벤트

#### Registering A Resolving Listener 의존성 해결 리스너 등록하기

The container fires an event each time it resolves an object. 컨테이너는 객체의 의존성 해결을 수행할 때 마다 이벤트를 발생시킵니다. You may listen to this event using the `resolving` method: `resolving` 메소드를 사용하여 이 이벤트들에 대응할 수 있습니다. 

	$this->app->resolving(function($object, $app)
	{
		// Called when container resolves object of any type...
	});

	$this->app->resolving(function(FooBar $fooBar, $app)
	{
		// Called when container resolves objects of type "FooBar"...
	});

The object being resolved will be passed to the callback. 의존성이 해결된 객체가 콜백에 전달됩니다. 

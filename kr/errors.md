# Errors & Logging 에러와 로깅

- [Configuration 설정](#configuration)
- [Handling Errors 에러 처리](#handling-errors)
- [HTTP Exceptions HTTP 예외](#http-exceptions)
- [Handling 404 Errors 404 에러 처리](#handling-404-errors)
- [Logging 로깅](#logging)

<a name="configuration"></a>
## Configuration 설정

The logging handler for your application is registered in the `app/start/global.php` [start file](/docs/lifecycle#start-files). By default, the logger is configured to use a single log file; however, you may customize this behavior as needed. Since Laravel uses the popular [Monolog](https://github.com/Seldaek/monolog) logging library, you can take advantage of the variety of handlers that Monolog offers. 로그 핸들러는 `app/start/global.php` [start file](/docs/lifecycle#start-files)에 등록되어 있습니다. 기본적으로이 로거는 하나의 로그 파일을 사용하도록 설정되어 있습니다; 그러나 필요한 경우이 동작을 변경할 수 있습니다. Laravel은 인기있는 [Monolog](https://github.com/Seldaek/monolog) 로그 라이브러리를 사용하고 있기 때문에, 이 라이브러리가 제공하는 다양한 핸들러를 사용할 수있는 장점이 있습니다.

For example, if you wish to use daily log files instead of a single, large file, you can make the following change to your start file: 예를 들어, 큰 하나의 파일에 로그를 남기는 대신, 일별 로그 파일을 사용하고 싶다면 start 파일을 다음과 같이 수정합니다.

	$logFile = 'laravel.log';

	Log::useDailyFiles(storage_path().'/logs/'.$logFile);
	
### Error Detail 에러 상세

By default, error detail is enabled for your application. This means that when an error occurs you will be shown an error page with a detailed stack trace and error message. You may turn off error details by setting the `debug` option in your `app/config/app.php` file to `false`. **It is strongly recommended that you turn off error detail in a production environment.** 기본적으로 응용 프로그램 오류 정보를 출력하도록 설정되어 있습니다. 이것은 오류 발생시 오류 페이지에서 스택 추적과 오류 메시지를 표시하는 것입니다. 에러 상세를 보지 않으려면 `app/config/app.php` 의 `debug` 옵션을 `false` 로 설정하십시오.

> **Note:** It is strongly recommended that you turn off error detail in a production environment. 실제 운영 환경에서는 에러 상세 표시를 해제하기를 강력히 권합니다.

<a name="handling-errors"></a>
## Handling Errors 에러 처리

By default, the `app/start/global.php` file contains an error handler for all exceptions: 기본적으로 `app/start/global.php` 파일에서 모든 예외 처리를 행하고 있습니다:

	App::error(function(Exception $exception)
	{
		Log::error($exception);
	});

This is the most basic error handler. However, you may specify more handlers if needed. Handlers are called based on the type-hint of the Exception they handle. For example, you may create a handler that only handles `RuntimeException` instances: 이것은 가장 기본적인 오류 처리입니다. 그러나 필요시 더 많은 핸들러를 지정할 수 있습니다. 처리기는 처리하는 예외 유형 힌트를 바탕으로 불려갑니다. 예를 들어, `RuntimeException` 만을 다루는 처리기를 만들고 싶다면 :

	App::error(function(RuntimeException $exception)
	{
		// Handle the exception...
	});

If an exception handler returns a response, that response will be sent to the browser and no other error handlers will be called: 만약 예외 처리기가 응답을 반환하면, 그것은 브라우저에 전송되고 다른 처리기가 호출되지 않습니다.

	App::error(function(InvalidUserException $exception)
	{
		Log::error($exception);

		return 'Sorry! Something is wrong with this account!';
	});

To listen for PHP fatal errors, you may use the `App::fatal` method: PHP의 fatal 오류를 수신하려면 `App::fatal` 메소드를 사용합니다.

	App::fatal(function($exception)
	{
		//
	});

If you have several exception handlers, they should be defined from most generic to most specific. So, for example, a handler that handles all exceptions of type `Exception` should be defined before a custom exception type such as `Illuminate\Encryption\DecryptException`. 여러 예외 처리기를 사용하는 경우 가장 일반적인것에서 가장 구체적인 순서로 정의되어야 합니다. 예를 들어, 모든 예외를 처리하는 유형 `Exception` 예외 처리기는 `Illuminate\Encryption\DecryptException`와 같은 사용자 지정 예외 이전에 정의해야 합니다.

### Where To Place Error Handlers 에러 핸들러 위치

There is no default "home" for error handler registrations. Laravel offers you freedom in this area. One option is to define the handlers in your `start/global.php` file. In general, this is a convenient location to place any "bootstrapping" code. If that file is getting crowded, you could create an `app/errors.php` file, and `require` that file from your `start/global.php` script. A third option is to create a [service provider](/docs/ioc#service-providers) that registers the handlers. Again, there is no single "correct" answer. Choose a location that you are comfortable with. 에러 핸들러를 위한 기본 설치 장소는 없습니다. Laravel은 여러분이 자유롭게 정할 수 있도록 해주고 있습니다. 한 가지 방법은 `start/global.php` 파일에 핸들러를 선언하는 것입니다. 일반적으로 이 파일은 "초기 설정" 코드를 설치하는 데 편리한 장소라고 말할 수 있겠지요. 이 파일이 너무 복잡해지면, `app/errors.php` 파일을 생성하고 `start/global.php` 스크립트에서 `require` 할 수 있습니다. 세 번째 방법은 [service provider](/docs/ioc#service-providers)에서 핸들러를 등록하는 방법입니다. 어쨌든, "올바른"하나의 대답은 존재하지 않습니다. 여러분이 편안하게 생각하는 위치에 설정하세요.

<a name="http-exceptions"></a>
## HTTP Exceptions HTTP 예외

Some exceptions describe HTTP error codes from the server. For example, this may be a "page not found" error (404), an "unauthorized error" (401) or even a developer generated 500 error. In order to return such a response, use the following: 어떤 예외들은 서버에서 발생하는 HTTP 에러 코드를 나타냅니다. 예를 들어, "페이지를 찾을 수 없습니다." 에러(404)나, 인증 오류 (401), 500 오류 같은 것입니다. 이 같은 응답을 반환하려면 다음과 같이 수행하십시오:

	App::abort(404);

Optionally, you may provide a response: 선택적으로, 응답을 제공할 수 있습니다.

	App::abort(403, 'Unauthorized action.');

This method may be used at any time during the request's lifecycle. 이 메소드는 요청의 수명주기 동안이면 언제든지 사용할 수 있습니다.

<a name="handling-404-errors"></a>
## Handling 404 Errors 404 에러 처리

You may register an error handler that handles all "404 Not Found" errors in your application, allowing you to easily return custom 404 error pages: 모든 "404 찾을 수 없음" 에러를 처리하는 에러 핸들러를 등록할 수 있고, 이 핸들러는 커스텀 404 페이지를 쉽게 반환 할 수 있게 해준다.

	App::missing(function($exception)
	{
		return Response::view('errors.missing', array(), 404);
	});

<a name="logging"></a>
## Logging 로깅

The Laravel logging facilities provide a simple layer on top of the powerful [Monolog](http://github.com/seldaek/monolog). By default, Laravel is configured to create daily log files for your application, and these files are stored in `app/storage/logs`. You may write information to these logs like so: Laravel 로그 기능은 강력한 [Monolog](http://github.com/seldaek/monolog)의 간단한 상위 레이어를 제공합니다. Laravel은 기본적으로 일별 로그 파일을 생성하도록 설정되어 있고, 로그 파일들은 `app/storage/logs` 에 저장됩니다. 다음과 같이 하여 로그에 정보를 입력할 수 있습니다:

	Log::info('This is some useful information.');

	Log::warning('Something could be going wrong.');

	Log::error('Something is really going wrong.');

The logger provides the seven logging levels defined in [RFC 5424](http://tools.ietf.org/html/rfc5424): **debug**, **info**, **notice**, **warning**, **error**, **critical**, and **alert**. [RFC 5424](http://tools.ietf.org/html/rfc5424)에 정의되어 있는 7가지 로깅 레벨(**debug**, **info**, **notice**, **warning**, **error**, **critical**, and **alert**)을 지원합니다.

An array of contextual data may also be passed to the log methods: 컨텍스트 데이터 배열도 로그 메소드에 전달할 수 있습니다.

	Log::info('Log message', array('context' => 'Other helpful information'));

Monolog has a variety of additional handlers you may use for logging. If needed, you may access the underlying Monolog instance being used by Laravel: Monolog에는 로그에 사용할 수있는 기타 다양한 핸들러가 준비되어 있습니다. 필요하다면 Laravel가 내부에서 사용하는 Monolog 인스턴스에 액세스 할 수 있습니다.

	$monolog = Log::getMonolog();

You may also register an event to catch all messages passed to the log: 로그에 전달되는 모든 메시지를 잡기 위해 이벤트를 등록 할 수 있습니다.

#### Registering A Log Listener 로그 리스너 등록하기

	Log::listen(function($level, $message, $context)
	{
		//
	});

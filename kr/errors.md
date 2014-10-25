# Errors & Logging 에러와 로깅

- [Error Detail 에러 상세](#error-detail)
- [Handling Errors 에러 처리](#handling-errors)
- [HTTP Exceptions HTTP 예외](#http-exceptions)
- [Handling 404 Errors 404 에러 처리](#handling-404-errors)
- [Logging 로깅](#logging)

<a name="error-detail 오류 상세"></a>
## Error Detail 오류 상세

By default, error detail is enabled for your application. This means that when an error occurs you will be shown an error page with a detailed stack trace and error message. You may turn off error details by setting the `debug` option in your `app/config/app.php` file to `false`. **It is strongly recommended that you turn off error detail in a production environment.** 기본적으로 응용 프로그램 오류 정보를 출력하도록 설정되어 있습니다. 이것은 오류 발생시 오류 페이지에서 스택 추적과 오류 메시지를 표시하는 것입니다. 에러 상세를 보지 않으려면 `app/config/app.php` 의 `debug` 옵션을 `false` 로 설정하십시오. **특히 실제 운영 환경에서는 에러 상세 표시를 해제하기를 강력히 권합니다.**

<a name="handling-errors"></a>
## Handling Errors 에러 처

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

<a name="http-exceptions"></a>
## HTTP Exceptions HTTP 예외

Exceptions in respect to HTTP, refer to errors that may occur during a client request. This may be a page not found error (404), an unauthorized error (401) or even a generated 500 error. In order to return such a response, use the following: HTTP 예외는 클라이언트의 요청을 처리 중 발생한 오류를 알려줍니다. 페이지를 찾을 수 없는 에러(404)나, 인증 오류 (401), 500 오류 같은 것입니다. 이 같은 응답을 반환하려면 다음과 같이 수행하십시오:

	App::abort(404, 'Page not found');

The first argument, is the HTTP status code, with the following being a custom message you'd like to show with the error. 첫 번째 인수는 HTTP 상태 코드입니다. 그 다음 오류로 표시 할 사용자 지정 메시지를 지정합니다.

In order to raise a 401 Unauthorized exception, just do the following: 401 인증 오류가 발생 싶다면 다음과 같이하면됩니다:

	App::abort(401, 'You are not authorized.');

These exceptions can be executed at any time during the request's lifecycle. 이러한 예외들은 요청의 수명주기 중 언제든지 발생시킬 수 있습니다.

<a name="handling-404-errors"></a>
## Handling 404 Errors 404 에러 처

You may register an error handler that handles all "404 Not Found" errors in your application, allowing you to return custom 404 error pages: 응용 프로그램에서 모든 "404 찾을 수 없음" 에러를 처리하기 위해 사용자 지정 404 페이지를 반환하는 핸들러를 등록 할 수 있습니다.

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

**Registering A Log Listener 로그 리스너 등록하**

	Log::listen(function($level, $message, $context)
	{
		//
	});

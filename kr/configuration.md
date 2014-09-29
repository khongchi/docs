# Configuration 설정

- [Introduction 소개](#introduction)
- [Environment Configuration 구동 환경 설정](#environment-configuration)
- [Provider Configuration 프로바이더 설정](#provider-configuration)
- [Protecting Sensitive Configuration 중요 설정 보호](#protecting-sensitive-configuration)
- [Maintenance Mode 점검(공사중) 모드](#maintenance-mode)

<a name="introduction"></a>
## Introduction 소개

All of the configuration files for the Laravel framework are stored in the `app/config` directory. 라라벨의 모든 설정 파일은 `app/config` 폴더에 위치하고 있습니다. Each option in every file is documented, so feel free to look through the files and get familiar with the options available to you. 모든 파일에는 개별 옵션에 대한 설명이 들어 있으니 해당 옵션에 대해 이해하는데 도움을 받을 수 있습니다. 

Sometimes you may need to access configuration values at run-time. 경우에 따라서 실행중에 설정값에 접근할 필요가 있을 수도 있습니다. You may do so using the `Config` class: 이런 경우에는 `Config` 클래스를 활용할 수 있습니다.

#### Accessing A Configuration Value 환경설정값 엑세스

	Config::get('app.timezone');

You may also specify a default value to return if the configuration option does not exist: 만약 설정값이 존재하지 않는 경우에는 기본값을 미리 지정할 수 있습니다.

	$timezone = Config::get('app.timezone', 'UTC');

#### Setting A Configuration Value 환경설정값 셋팅하기

Notice that “dot” style syntax may be used to access values in the various files. “점(마침표)” 스타일의 문법은 다양한 파일 설정에 액세스 할 수 있다는 것을 의미합니다. You may also set configuration values at run-time: 또한 실행중에 설정값을 설정할 수도 있습니다. 

	Config::set('database.default', 'sqlite');

Configuration values that are set at run-time are only set for the current request, and will not be carried over to subsequent requests. 실행중에 세팅된 설정값은 현재의 요청이 수행되는 동안에만 적용되며, 그 다음 요청에까지 적용되지는 않습니다.

<a name="environment-configuration"></a>
## Environment Configuration 구동환경 설정

It is often helpful to have different configuration values based on the environment the application is running in. 프로그램이 실행되는  구동환경에 따라 다른 설정값을 사용할 수 있으면 많은 이점이 있습니다. For example, you may wish to use a different cache driver on your local development machine than on the production server. 예를 들어, 실제 제품 서버와 로컬 개발 머신에 각각 다른 캐시 드라이버를 사용하려는 경우입니다. It is easy to accomplish this using environment based configuration. 이러한 경우 구동환경 설정을 통해 이를 손쉽게 해결 할 수 있습니다.

Simply create a folder within the `config` directory that matches your environment name, such as `local`. `local` 과 같은 구동환경의 이름과 같은 디렉토리를 `config` 디렉토리 안에 생성합니다. Next, create the configuration files you wish to override and specify the options for that environment. 그 다음, 재정의하길 원하는 설정값에 대한 파일을 만들고 필요한 옵션을 설정합니다. For example, to override the cache driver for the local environment, you would create a `cache.php` file in `app/config/local` with the following content:  예를 들어, 로컬 환경에서만 사용되도록 캐시 드라이버를 재정의하고 싶다면 `app/config/local` 폴더에 `cache.php` 파일을 생성하고 다음과 같은 내용을 작성합니다:

	<?php

	return array(

		'driver' => 'file',

	);

> **Note주의:** Do not use ‘testing’ as an environment name. This is reserved for unit testing. ‘testing’ 이라는 이름은 구동환경 이름으로 설정하지 마십시오. 이것은 유닛 테스트를 위한 예약어입니다.

Notice that you do not have to specify _every_ option that is in the base configuration file, but only the options you wish to override. 기본 설정 파일의 모든 옵션을 새롭게 재정의할 필요없이, 재정의 하고 싶은 부분만 설정하면 됩니다. The environment configuration files will “cascade” over the base files. 환경 설정 파일은 기본 파일을 바탕으로 “캐스케이드” 방식으로 구성됩니다.

Next, we need to instruct the framework how to determine which environment it is running in. 다음으로 우리는 어플리케이션이 어떤 구동환경에서 실행되고 있는지 프레임워크에게 알려 줄 필요가 있습니다. The default environment is always `production`. 기본 구동환경은 `production` 입니다. However, you may setup other environments within the `bootstrap/start.php` file at the root of your installation. 하지만 최상의 폴더에 위치한 `bootstrap/start.php` 파일에 다른 구동환경을 설정할 수도 있습니다. In this file you will find an `$app->detectEnvironment` call. 이 파일에서 `$app->detectEnvironment` 메소드가 호출되는 것을 확인할 수 있습니다. The array passed to this method is used to determine the current environment. 이 메소드에 전달되는 배열이 현재의 구동환경을 결정하는데 사용됩니다. You may add other environments and machine names to the array as needed. 필요한 경우 배열값에 다른 구동환경과 머신의 이름을 추가할 수 있습니다. 

    <?php

    $env = $app->detectEnvironment(array(

        'local' => array('your-machine-name'),

    ));

In this example, ‘local’ is the name of the environment and ‘your-machine-name’ is the hostname of your server. 이 예제에서 ‘local’은 구동환경의 이름이 되고 ‘your-machine-name’ 은 서버의 호스트 이름을 말합니다. On Linux and Mac, you may determine your hostname using the `hostname` terminal command. Linux나 Mac이라면 `hostname` 터미널 명령어를 통해서 현재의 호스트 이름을 확인할 수 있습니다.

(역자주 :윈도우7에서도 시작 - 실행 터미널에서 hostname 명령어를 입력하면 해당 컴퓨터의 호스트 이름을 확인할 수 있습니다.)

You may also pass a `Closure` to the `detectEnvironment` method, allowing you to implement your own environment detection:
또한, 자신만의 구동환경을 결정하는 `클로저`를 `detectEnvironment` 메서드에 전달 할 수도 있습니다:

	$env = $app->detectEnvironment(function()
	{
		return $_SERVER['MY_LARAVEL_ENV'];
	});

#### Accessing The Current Application Environment 현재의 어플리케이션 구동환경에 엑세스

You may access the current application environment via the `environment` method:
`environment` 메소드를 통해서 현재의 어플리케이션 구동환경에 액세스 할 수도 있습니다:

	$environment = App::environment();

You may also pass arguments to the `environment` method to check if the environment matches a given value: 
또는 `environment` 메소드에 매개변수를 넘겨주어 해당 구동환경을 확인하는 형태로 사용할 수도 있습니다:

	if (App::environment('local'))
	{
		// The environment is local
	}

	if (App::environment('local', 'staging'))
	{
		// The environment is either local OR staging...
	}

<a name="provider-configuration"></a>
### Provider Configuration 프로바이더 설정

When using environment configuration, you may want to "append" environment [service providers](/docs/ioc#service-providers) to your primary `app` configuration file. 구동환경에 의해 환경설정을 구분하여 사용하는 경우 기본 `app` 설정 파일에 서비스 프로바이더를 추가"append" 하고 싶을 수도 있습니다. However, if you try this, you will notice the environment `app` providers are overriding the providers in your primary `app` configuration file. 하지만 이 경우 기본 `app` 설정 파일이 구동환경당 설정되어 있는 `app` 설정 파일 내용을 덮어씌워 버린다는 점에 유의하십시오. To force the providers to be appended, use the `append_config` helper method in your environment `app` configuration file: 서비스 프로바이더를 강제로 추가 하고자 할 경우에는 append_config 헬퍼 메소드를 다음과 같이 구동환경의 `app` 설정 파일에서 사용하면 됩니다.

	'providers' => append_config(array(
		'LocalOnlyServiceProvider',
	))

<a name="protecting-sensitive-configuration"></a>
## Protecting Sensitive Configuration 중요 설정 보호

For "real" applications, it is advisable to keep all of your sensitive configuration out of your configuration files. 개발용이 아닌 "실제" 어플리케이션에서는 중요한 설정 파일은 모두 분리하는 것이 바람직한 방법입니다. Things such as database passwords, Stripe API keys, and encryption keys should be kept out of your configuration files whenever possible. 가능하다면 데이타베이스 접속 암호, Stripe API 키 그리고 암호화 키는 설정 파일에서 분리하는 것이 좋습니다. So, where should we place them? 그렇다면 어디에 위치 시키는 것이 좋을까요? Thankfully, Laravel provides a very simple solution to protecting these types of configuration items using "dot" files. 다행스럽게도, 라라벨에서는 "점" 파일을 사용하여 이러한 중요한 설정 항목을 보호할 수 있는 간단한 해결책을 제공합니다.

First, [configure your application](/docs/configuration#environment-configuration) to recognize your machine as being in the `local` environment. 첫번째로, `local` 구동환경에 있는 것 처럼 컴퓨터를 인식할 수 있도록 [어플리케이션 설정](/docs/configuration#environment-configuration)을 구성합니다. Next, create a `.env.local.php` file within the root of your project, which is usually the same directory that contains your `composer.json` file. 다음으로 `composer.json` 파일이 위치한 최상위 디렉토리에 `.env.local.php` 파일을 생성합니다. The `.env.local.php` should return an array of key-value pairs, much like a typical Laravel configuration file: `.env.local.php` 파일은 일반적인 라라벨 구성 파일과 같은 형태로 키-값 형태의 배열을 반환해야합니다 :

	<?php

	return array(

		'TEST_STRIPE_KEY' => 'super-secret-sauce',

	);

All of the key-value pairs returned by this file will automatically be available via the `$_ENV` and `$_SERVER` PHP "superglobals". You may now reference these globals from within your configuration files:
이 파일에서 반환된 모든 키-값 형태의 배열은 자동으로 `$_ENV` 변수와 PHP 시스템전역변수인 `$_SERVER`을 통해서 엑세스 할 수 있습니다. 이제 이 설정 파일로 부터 다음과 같이 전역변수를 통해서 값을 가져올 수 있습니다. 

	'key' => $_ENV['TEST_STRIPE_KEY']

Be sure to add the `.env.local.php` file to your `.gitignore` file. `.env.local.php` 파일은 `.gitignore` 파일에 추가해 놓는것이 좋습니다. This will allow other developers on your team to create their own local environment configuration, as well as hide your sensitive configuration items from source control. 이렇게 하면 팀 구성원들이 자신의 로컬 환경 설정을 만들 수 있으며, 동시에 소스 제어에서 주요 설정 항목들을 숨길 수 있습니다.

Now, On your production server, create a `.env.php` file in your project root that contains the corresponding values for your production environment. Like the `.env.local.php` file, the production `.env.php` file should never be included in source control. 그 다음으로 실제운영 서버에서 `.env.php` 파일을 프로젝트 최상위 디렉토리에 생성하고 구동환경에 적합한 설정값을 지정하십시오. `.env.local.php` 파일과 마찬가지로 `env.php` 파일이 버전관리 대상에서 제외되도록 하십시오. 

> **Note 주의:** You may create a file for each environment supported by your application. 어플리케이션이 지원하는 각각의 구동환경에 따라서 설정 파일을 생성할 수 있습니다. For example, the `development` environment will load the `.env.development.php` file if it exists. 예를 들면 `development` 이란 이름의 구동환경에서는 `.env.development.php` 파일이 존재하면 해당 파일을 로드 합니다. 

<a name="maintenance-mode"></a>
## Maintenance Mode 점검 모드

When your application is in maintenance mode, a custom view will be displayed for all routes into your application. 어플리케이션이 점검 모드일 경우 응용 프로그램에서 지정한 모든 라우팅이 특정한 뷰로 보여지게 됩니다. This makes it easy to “disable” your application while it is updating. 따라서 어플리케이션을 업데이트 할 때 손쉽게 “사용 정지” 할 수 있습니다. A call to the `App::down` method is already present in your `app/start/global.php` file.
`app/start/global.php` 파일에 `App:down` 메소드가 준비되어 있습니다. The response from this method will be sent to users when your application is in maintenance mode. 어플리케이션이 점검 모드일 때 이 메소드의 응답이 사용자에게 보여집니다.

To enable maintenance mode, simply execute the `down` Artisan command:
점검 모드를 활성화 하려면 아티즌 명령어 `down`을 실행시키면 됩니다:


	php artisan down

To disable maintenance mode, use the `up` command:
점검 모드를 해제하려면 아티즌 명령어 `up`을 실행시키면 됩니다:

	php artisan up

To show a custom view when your application is in maintenance mode, you may add something like the following to your application’s `app/start/global.php` file:
어플리케이션이 점검 모드 일 때 특정 화면을 보여주기 위해 `app/start/global.php` 에 다음과 같이 지정할 수도 있습니다:

	App::down(function()
	{
		return Response::view('maintenance', array(), 503);
	});

If the Closure passed to the `down` method returns `NULL`, maintenance mode will be ignored for that request. 만약 `down` 메소드로 넘겨준 클로저가 `NULL`값을 반환한다면 점검 모드는 무시됩니다. 

### Maintenance Mode & Queues 점검 모드 & 큐

While your application is in maintenance mode, no [queue jobs](/docs/queues) will be handled. 어플리케이션이 점검 모드인 동안에는 [큐 작업](/docs/queues)은 동작하지 않습니다. The jobs will continue to be handled as normal once the application is out of maintenance mode. 점검 모드가 해제 된 어플리케이션이 일반 모드로 전환 된 시점에서 처리가 계속됩니다.

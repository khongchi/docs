# SSH

- [Configuration 설정](#configuration)
- [Basic Usage 기본적인 사용법](#basic-usage)
- [Tasks 작업](#tasks)
- [SFTP Downloads SFTP 다운로드](#sftp-downloads)
- [SFTP Uploads SFTP 업로드](#sftp-uploads)
- [Tailing Remote Logs 원격 로그보기](#tailing-remote-logs)
- [Envoy Task Runner Envoy 작업 러너](#envoy-task-runner)

<a name="configuration"></a>
## Configuration 설정

Laravel includes a simple way to SSH into remote servers and run commands, allowing you to easily build Artisan tasks that work on remote servers. The `SSH` facade provides the access point to connecting to your remote servers and running commands. Laravel은 원격 서버에 연결하고 명령을 실행하는 간단한 방법을 지원하고, 원격 서버에서 작동하는 Artisan 작업을 쉽게 만들 수 있게 해줍니다. `SSH` 파사드는 원격 서버에 대한 연결 및 명령 실행을 제공합니다.

The configuration file is located at `app/config/remote.php`, and contains all of the options you need to configure your remote connections. The `connections` array contains a list of your servers keyed by name. Simply populate the credentials in the `connections` array and you will be ready to start running remote tasks. Note that the `SSH` can authenticate using either a password or an SSH key. 설정 파일은 `app/config/remote.php`에 위치해 있고, 원격 접속 설정에 필요한 모든 옵션들을 담고 있습니다. `connections` 배열은 이름을 키값으로 가지고 있는 서버 목록을 담고 있습니다. `connections` 배열에 연결 정보를 지정하기만 하면 원격 작업을 수행할 준비가 끝납니다. `SSH`는 패스워드나 SSH Key를 이용하여 인증할 수 있다는 점을 유의하세요.

> **Note: 참고:** Need to easily run a variety of tasks on your remote server? Check out the [Envoy task runner](#envoy-task-runner)! 원격 서버에서 다양한 작업을 쉽게 실행할 필요가 있나요? [Envoy task runner](#envoy-task-runner)를 사용해보세요!

<a name="basic-usage"></a>
## Basic Usage 기본적인 사용법

#### Running Commands On The Default Server 기본 서버에서 커맨드 실행하기

To run commands on your `default` remote connection, use the `SSH::run` method: `default` 원격 접속에 명령을 수행하려면, `SSH::run` 메소드를 사용하세요:

	SSH::run(array(
		'cd /var/www',
		'git pull origin master',
	));

#### Running Commands On A Specific Connection 특정 서버에 명령을 실행하기

Alternatively, you may run commands on a specific connection using the `into` method: `into` 메소드를 사용하여 특정 서버에 명령을 실행할 수 있습니다:

	SSH::into('staging')->run(array(
		'cd /var/www',
		'git pull origin master',
	));

#### Catching Output From Commands 커맨드의 출력을 확인하기

You may catch the "live" output of your remote commands by passing a Closure into the `run` method: `run` 메소드에 클로저를 넘겨줌으로써 원격 명령의 결과를 확인할 수 있습니다.

	SSH::run($commands, function($line)
	{
		echo $line.PHP_EOL;
	});

<a name="tasks"></a>
## Tasks 작업

If you need to define a group of commands that should always be run together, you may use the `define` method to define a `task`: 항상 같이 작동되어야 하는 커맨드를 그룹핑할 필요가 있을 때는, `define`메소드를 사용하여 `task`를 정의합니다:

	SSH::into('staging')->define('deploy', array(
		'cd /var/www',
		'git pull origin master',
		'php artisan migrate',
	));

Once the task has been defined, you may use the `task` method to run it: task가 한 번 정의되고 나면, `task` 메소드로 실행시킬 수 있습니다.

	SSH::into('staging')->task('deploy', function($line)
	{
		echo $line.PHP_EOL;
	});

<a name="sftp-downloads"></a>
## SFTP Downloads SFTP 다운로드

The `SSH` class includes a simple way to download files using the `get` and `getString` methods: `SSH` 클래스의 `get`과 `getString` 메소드를 사용하여 파일을 쉽게 다운로드 받을 수 있습니다. 

	SSH::into('staging')->get($remotePath, $localPath);

	$contents = SSH::into('staging')->getString($remotePath);

<a name="sftp-uploads"></a>
## SFTP Uploads SFTP 업로드

The `SSH` class also includes a simple way to upload files, or even strings, to the server using the `put` and `putString` methods: `SSH` 클래스는 `put`과 `putString`을 이용해서 서버에 파일이나 문자열을 업로드 하는 쉬운 방법을 가지고 있습니다.

	SSH::into('staging')->put($localFile, $remotePath);

	SSH::into('staging')->putString($remotePath, 'Foo');

<a name="tailing-remote-logs"></a>
## Tailing Remote Logs 원격 로그 보기

Laravel includes a helpful command for tailing the `laravel.log` files on any of your remote connections. Simply use the `tail` Artisan command and specify the name of the remote connection you would like to tail: Laravel은 어떠한 원격 서버에 있는 `laravel.log` 파일도 쉽게 볼 수 있는 편리한 명령어를 가지고 있습니다. 단순히 `tail` Artisan 명령어를 사용하고 당신이 보고자 하는 원격 서버의 이름을 지정해주세요: 

	php artisan tail staging

	php artisan tail staging --path=/path/to/log.file

<a name="envoy-task-runner"></a>
## Envoy Task Runner Envoy Task Runner

- [Installation 설치](#envoy-installation)
- [Running Tasks 작업 돌리기](#envoy-running-tasks)
- [Multiple Servers 여러 서버](#envoy-multiple-servers)
- [Parallel Execution 병렬 실행](#envoy-parallel-execution)
- [Task Macros 작업 매크로](#envoy-task-macros)
- [Notifications 알림](#envoy-notifications)
- [Updating Envoy Envoy 업데이트](#envoy-updating-envoy)

Laravel Envoy provides a clean, minimal syntax for defining common tasks you run on your remote servers. Using a [Blade](/docs/templates#blade-templating) style syntax, you can easily setup tasks for deployment, Artisan commands, and more. Laravel Envoy는 원격 서버에서 실행시키는 공통적인 작업을 정의하기 위한 깨끗하고, 최소화된 문법을 제공합니다. [Blade](/docs/templates#blade-templating) 스타일의 문법을 사용하여, 당신은 배포나 Artisan 명령어 등을 위한 작업을 쉽게 설정할 수 있습니다. 

> **Note: 참고:** Envoy requires PHP version 5.4 or greater, and only runs on Mac / Linux operating systems. Envoy는 PHP 5.4 이상이 필요하고, Mac / Linux 에서만 작동합니다.

<a name="envoy-installation"></a>
### Installation 설치

First, install Envoy using the Composer `global` command: 먼저, Composer 의 `global` 명령어를 사용해 Envoy를 설치합니다.

	composer global require "laravel/envoy=~1.0"

Make sure to place the `~/.composer/vendor/bin` directory in your PATH so the `envoy` executable is found when you run the `envoy` command in your terminal. `envoy` 명령을 터미널에서 실행하는 경우에는 `envoy` 실행 파일을 시작할 수 있도록, `~/.composer/vendor/bin` 디렉토리를 PATH에 추가해주세요. 

Next, create an `Envoy.blade.php` file in the root of your project. Here's an example to get you started: 그리고나서, 프로젝트의 루트 폴더에 `Envoy.blade.php` 파일을 만드세요. 아래는 시작에 도움이 되는 샘플입니다.

	@servers(['web' => '192.168.1.1'])

	@task('foo', ['on' => 'web'])
		ls -la
	@endtask

As you can see, an array of `@servers` is defined at the top of the file. You can reference these servers in the `on` option of your task declarations. Within your `@task` declarations you should place the Bash code that will be run on your server when the task is executed. 보는 바와 같이, `@servers` 배열은 파일의 가장 윗부분에 정의됩니다. 당신의 task 선언문의 `on` 옵션에서 이 서버들을 참조할 수 있습니다. task가 실행될 때 당신의 서버에서 작동될 Bash 코드는 `@task` 선언 안에 위치시켜야 합니다.  

The `init` command may be used to easily create a stub Envoy file: `init` 명령어는 Envoy 파일 토막을 쉽게 생성하는데 사용될 수 있습니다.  

	envoy init user@192.168.1.1

<a name="envoy-running-tasks"></a>
### Running Tasks Task 실행

To run a task, use the `run` command of your Envoy installation: task 를 실행하기 위해서는, 설치한 Envoy의 `run` 명령어를 사용합니다:

	envoy run foo

If needed, you may pass variables into the Envoy file using command line switches: 필요하다면, 커맨드라인 스위치를 이용하여 Envoy 파일에 변수를 넘겨줄 수 있습니다.

	envoy run deploy --branch=master

You may use the options via the Blade syntax you are used to: 당신에게 익숙한 Blade 문법으로 옵션들을 사용할 수 있습니다:

	@servers(['web' => '192.168.1.1'])

	@task('deploy', ['on' => 'web'])
		cd site
		git pull origin {{ $branch }}
		php artisan migrate
	@endtask

#### Bootstrapping 준비코드

You may use the ```@setup``` directive to declare variables and do general PHP work inside the Envoy file: ```@setup``` 지시어를 사용하면 Envoy 파일에서 변수 선언을하거나 일반적인 PHP 코드를 실행시킬 수 있습니다.

	@setup
		$now = new DateTime();

		$environment = isset($env) ? $env : "testing";
	@endsetup

You may also use ```@include``` to include any PHP files: PHP 파일을 인클루드 하기 위해 ```@include```를 사용할 수 있습니다.

	@include('vendor/autoload.php');

<a name="envoy-multiple-servers"></a>
### Multiple Servers 여러 서버

You may easily run a task across multiple servers. Simply list the servers in the task declaration: 여러 서버에 걸쳐 쉽게 작업을 수행 할 수 있습니다. 작업 선언에서 서버 목록을 지정하면됩니다.

	@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

	@task('deploy', ['on' => ['web-1', 'web-2']])
		cd site
		git pull origin {{ $branch }}
		php artisan migrate
	@endtask

By default, the task will be executed on each server serially. Meaning, the task will finish running on the first server before proceeding to execute on the next server. 기본적으로 작업은 각 서버에서 순차적으로 실행합니다. 즉, 첫 번째 서버에서 실행이 끝나면 다음 서버의 실행으로 이동합니다.

<a name="envoy-parallel-execution"></a>
### Parallel Execution 병렬 실행

If you would like to run a task across multiple servers in parallel, simply add the `parallel` option to your task declaration: 여러 서버에서 동시에 작업을 수행하려면 그냥 `parallel` 옵션을 작업 선언에서 지정하십시오.

	@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

	@task('deploy', ['on' => ['web-1', 'web-2'], 'parallel' => true])
		cd site
		git pull origin {{ $branch }}
		php artisan migrate
	@endtask

<a name="envoy-task-macros"></a>
### Task Macros 작업 매크로

Macros allow you to define a set of tasks to be run in sequence using a single command. For instance: 매크로는 하나의 명령으로 여러 task가 순차적으로 실행되도록 해줍니다. 예를 들면 :

	@servers(['web' => '192.168.1.1'])

	@macro('deploy')
		foo
		bar
	@endmacro

	@task('foo')
		echo "HELLO"
	@endtask

	@task('bar')
		echo "WORLD"
	@endtask

The `deploy` macro can now be run via a single, simple command: 이제 `deploy` 매크로는 하나의 단순한 명령어로 실행 가능합니다.

	envoy run deploy

<a name="envoy-notifications"></a>
### Notifications 알림

<a name="envoy-hipchat-notifications"></a>
#### HipChat HipChat

After running a task, you may send a notification to your team's HipChat room using the simple `@hipchat` directive: task를 실행 한 후, 팀의 HipChat 룸에 간단한 `@hipchat` 지시어를 사용하여 알림을 보낼 수 있습니다.

	@servers(['web' => '192.168.1.1'])

	@task('foo', ['on' => 'web'])
		ls -la
	@endtask

	@after
		@hipchat('token', 'room', 'Envoy')
	@endafter

You can also specify a custom message to the hipchat room. Any variables declared in ```@setup``` or included with ```@include``` will be available for use in the message: 또한 사용자 정의 메시지를 HipChat 룸에 지정할 수도 있습니다. ```@setup``` 변수를 직접 선언 할 수 있으며, ```@include``` 에서 파일을 가져올 수 있습니다. 그렇게 선언 한 변수를 메시지에서 사용할 수 있습니다.

	@after
		@hipchat('token', 'room', 'Envoy', "$task ran on [$environment]")
	@endafter

This is an amazingly simple way to keep your team notified of the tasks being run on the server. 이것은 서버에서 작업이 실행 된 것을 팀에 통지 할 수있는 매우 간단한 방법입니다.

#### Slack 슬랙

The following syntax may be used to send a notification to [Slack](https://slack.com): [Slack](https://slack.com) 에 통지를 보내려면 다음 표기법을 사용할 수 있습니다.

	@after
		@slack('team', 'token', 'channel')
	@endafter

<a name="envoy-updating-envoy"></a>
### Updating Envoy Envoy 업데이트하기

To update Envoy, simply run the `self-update` command: Envoy를 업데이트하려면 `self-update` 명령을 실행하십시오.

	envoy self-update

If your Envoy installation is in `/usr/local/bin`, you may need to use `sudo`: `/usr/local/bin` 에 Envoy를 설치하는 경우 `sudo` 를 사용할 필요가 있을 것입니다.

	sudo envoy self-update

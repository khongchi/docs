# Artisan Development Artisan 개발

- [Introduction 소개](#introduction)
- [Building A Command 커맨드 만들기](#building-a-command)
- [Registering Commands 커맨드 등록하기](#registering-commands)
- [Calling Other Commands 다른 커맨드 호출하기](#calling-other-commands)

<a name="introduction"></a>
## Introduction 소개

In addition to the commands provided with Artisan, you may also build your own custom commands for working with your application. You may store your custom commands in the `app/commands` directory; however, you are free to choose your own storage location as long as your commands can be autoloaded based on your `composer.json` settings. Artisan 으로 제공되는 커맨드에 추가로 자신의 커스텀 커맨드를 만들 수 있습니다. `app/commands` 디렉토리에 당신의 커스텀 커맨드를 저장하십시오; 그러나 커맨드가 오토로드 될 수 있게 composer.json 을 설정하는 한, 어디든 원하는 위치에 넣을 수 있습니다.

<a name="building-a-command"></a>
## Building A Command 커맨드 만들기

### Generating The Class 클래스 만들기

To create a new command, you may use the `command:make` Artisan command, which will generate a command stub to help you get started:
새로운 커맨드를 생성하기 위해, Artisan 명령 `command:make` 를 사용합니다. 이 명령어는 작업에 착수하는데 도움이 될 커맨드 스텁들을 생성해 줍니다.

**Generate A New Command Class 새로운 커맨드 클래스 생성**

	php artisan command:make FooCommand

By default, generated commands will be stored in the `app/commands` directory; however, you may specify custom path or namespace: 기본적으로 생성된 커맨드들은 `app/commands` 디렉토리에 저장됩니다; 그러나 다른 위치나 네임스페이스를 지정할 수도 있습니다:
	
	php artisan command:make FooCommand --path=app/classes --namespace=Classes

### Writing The Command 커맨드 작성

Once your command is generated, you should fill out the `name` and `description` properties of the class, which will be used when displaying your command on the `list` screen.커맨드가 생성되고 나면, 클래스의 `name` 이나 `description` 를 작성해야합니다. 이것들은 `list` 화면에 커맨드를 표시 할 때 사용됩니다.

The `fire` method will be called when your command is executed. You may place any command logic in this method. `fire` 메소드는 명령이 실행될 때 호출됩니다. 모든 커맨드 로직를 이 메서드에 써주세요.

### Arguments & Options 인수 & 옵션

The `getArguments` and `getOptions` methods are where you may define any arguments or options your command receives. Both of these methods return an array of commands, which are described by a list of array options. 커맨드가 받는 모든 인수와 옵션을 `getArguments` 와 `getOptions` 메소드에 정의합니다. 이 두 메소드는 배열 옵션 목록에서 설명되는 커맨드의 배열을 반환합니다. 

When defining `arguments`, the array definition values represent the following: `arguments` 를 정의하는 정의 배열은 다음과 같은 형식입니다. 

	array($name, $mode, $description, $defaultValue)

The argument `mode` may be any of the following: `InputArgument::REQUIRED` or `InputArgument::OPTIONAL`. 인수 `mode`는 `InputArgument::REQUIRED` 또는 `InputArgument::OPTIONAL` 중 하나입니다.

When defining `options`, the array definition values represent the following: `options` 를 정의하는 정의 배열은 다음과 같은 형식입니다.

	array($name, $shortcut, $mode, $description, $defaultValue)

For options, the argument `mode` may be: `InputOption::VALUE_REQUIRED`, `InputOption::VALUE_OPTIONAL`, `InputOption::VALUE_IS_ARRAY`, `InputOption::VALUE_NONE`. 옵션에서의 인수 `mode` 는 `InputOption:REQUIRED` , `InputOption::OPTIONAL` , `InputOption::VALUE_IS_ARRAY` , `InputOption::VALUE_NONE` 중 하나입니다.

The `VALUE_IS_ARRAY` mode indicates that the switch may be used multiple times when calling the command: `VALUE_IS_ARRAY` 모드는 명령 실행시 여러 번 사용되는 스위치를 나타냅니다.

	php artisan foo --option=bar --option=baz

The `VALUE_NONE` option indicates that the option is simply used as a "switch": `VALUE_NONE` 옵션은 이 옵션이 단순히 스위치로 사용된다는 것을 나타냅니다.

	php artisan foo --option

### Retrieving Input 입렵값 조회하기

While your command is executing, you will obviously need to access the values for the arguments and options accepted by your application. To do so, you may use the `argument` and `option` methods: 커맨드가 실행중일 때, 응용 프로그램이 받은 인수 및 옵션의 값에 액세스 할 필요가 확실히 있다. 그러기 위해서는 `argument` 와 `option` 메서드를 사용합니다:

**Retrieving The Value Of A Command Argument 커맨드 인수 값 조회하기**

	$value = $this->argument('name');

**Retrieving All Arguments 모든 인수값들을 조회하기**

	$arguments = $this->argument();

**Retrieving The Value Of A Command Option 커맨드 옵션의 값을 조회하기**

	$value = $this->option('name');

**Retrieving All Options 모든 옵션값들을 조회하기**

	$options = $this->option();

### Writing Output 출력 작성하기

To send output to the console, you may use the `info`, `comment`, `question` and `error` methods. Each of these methods will use the appropriate ANSI colors for their purpose. 콘솔에 출력하려면 `info` , `comment` , `question` , `error` 메소드를 사용합니다. 각 메소드들은 각각 적절한 ANSI 컬러를 목적에 맞게 사용할 것입니다.

**Sending Information To The Console 콘솔에 정보 보내기**

	$this->info('Display this on the screen');

**Sending An Error Message To The Console 콘솔에 에러 메시지 보내기**

	$this->error('Something went wrong!');

### Asking Questions 질문하

You may also use the `ask` and `confirm` methods to prompt the user for input: 입력을 요청하는 `ask`와 `confirm` 메소드도 사용할 수 있습니다.

**Asking The User For Input 사용자에게 입력을 요구하기**

	$name = $this->ask('What is your name?');

**Asking The User For Secret Input 사용자에게 비밀 문자열 입력을 요구하기**

	$password = $this->secret('What is the password?');

**Asking The User For Confirmation 사용자에게 확인을 요구하기**

	if ($this->confirm('Do you wish to continue? [yes|no]'))
	{
		//
	}

You may also specify a default value to the `confirm` method, which should be `true` or `false`: `confirm` 메소드에 대한 기본 값을 `true`와 `false`로 지정할 수 있습니다:

	$this->confirm($question, true);

<a name="registering-commands"></a>
## Registering Commands 커맨드 등록하기

Once your command is finished, you need to register it with Artisan so it will be available for use. This is typically done in the `app/start/artisan.php` file. Within this file, you may use the `Artisan::add` method to register the command: 커맨드 만들기가 끝나면, 사용이 가능하도록 하기 위해 Artisan에 등록할 필요가 있습니다. 이 작업은 일반적으로 `app/start/artisan.php` 파일에서 이뤄집니다. 이 파일에서, 커맨드를 등록하기 위해 `Artisan::add` 메소드를 사용합니다:

**Registering An Artisan Command Artisan 커맨드 등록하기**

	Artisan::add(new CustomCommand);

If your command is registered in the application [IoC container](/docs/ioc), you may use the `Artisan::resolve` method to make it available to Artisan: 커맨드를 응용 프로그램의 [IoC 컨테이너](/docs/ioc)에 등록하는 경우, Artisan 에서 사용 가능하게하려면 `Artisan::resolve` 메소드를 사용합니다.

**Registering A Command That Is In The IoC Container 커맨드를 IoC 컨테이너에 등록하기**

	Artisan::resolve('binding.name');

<a name="calling-other-commands"></a>
## Calling Other Commands 다른 커맨드 호출

Sometimes you may wish to call other commands from your command. You may do so using the `call` method: 당신의 커맨드에서 다른 커맨드를 호출하고자 할 때가 있습니다. `call` 메소드를 사용해서 호출할 수 있습니다.

**Calling Another Command 다른 커맨드 호출하기**

	$this->call('command.name', array('argument' => 'foo', '--option' => 'bar'));

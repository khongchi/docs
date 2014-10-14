# Mail 메일

- [Configuration 설정](#configuration)
- [Basic Usage 기본 사용법](#basic-usage)
- [Embedding Inline Attachments 인라인 첨부](#embedding-inline-attachments)
- [Queueing Mail 메일 큐 사용](#queueing-mail)
- [Mail & Local Development 메일 & 로컬 개발](#mail-and-local-development)

<a name="configuration"></a>
## Configuration 설정

Laravel provides a clean, simple API over the popular [SwiftMailer](http://swiftmailer.org) library. 라라벨은 인기있는 [SwiftMailer](http://swiftmailer.org)를 통해서 깔끔하고 단순한 API 를 제공합니다. The mail configuration file is `app/config/mail.php`, and contains options allowing you to change your SMTP host, port, and credentials, as well as set a global `from` address for all messages delivered by the library. 메일에 대한 설정 파일은 `app/config/mail.php` 파일로 SMTP 호스트, 포트, 인증 및 라이브러리를 통해 송신되는 메세지들의 글로벌 `form` 주소를 설정할 수 있는 옵션들을 제공합니다. You may use any SMTP server you wish. 원하는 어떤 SMTP 서버라도 사용가능합니다. If you wish to use the PHP `mail` function to send mail, you may change the `driver` to `mail` in the configuration file. 메일을 보낼 때 PHP 의 `mail` 함수를 사용하려 한다면 설정 파일에서 `driver` 를 `mail`로 변경하면 됩니다. A `sendmail` driver is also available. 또는 `sendmail` 드라이버도 사용가능합니다. 

<a name="basic-usage"></a>
## Basic Usage 기본 사용법 

The `Mail::send` method may be used to send an e-mail message:
`Mail::send` 메소드를 통해서 이메일을 보낼 수 있습니다:

	Mail::send('emails.welcome', $data, function($message)
	{
		$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
	});

The first argument passed to the `send` method is the name of the view that should be used as the e-mail body. `send` 메소드의 첫번째 인자로 이메일의 본문에 사용되는 뷰의 이름을 전달합니다. The second is the `$data` that should be passed to the view, and the third is a Closure allowing you to specify various options on the e-mail message. 두번째 인자인 `$data`는 뷰에서 사용되는 변수이고, 세번째 전달되는 클로저는 이메일 메세지에 대한 다양한 옵션을 지정하는데 사용됩니다. 

> **Note 주의:** A `$message` variable is always passed to e-mail views, and allows the inline embedding of attachments. `$message`라는 이름의 변수가 항상 이메일 뷰에 전달되고, 인라인 첨부를 사용가능하게 합니다. So, it is best to avoid passing a `message` variable in your view payload. 따라서 `message`라는 이름의 변수를 메일 뷰에서 사용하는 것은 피하는 것이 좋습니다. 

You may also specify a plain text view to use in addition to an HTML view: HTML 뷰에 추가로 플레인 텍스트 뷰를 지정할 수도 있습니다:

	Mail::send(array('html.view', 'text.view'), $data, $callback);

Or, you may specify only one type of view using the `html` or `text` keys: 또는 `html`또는 `text` 키를 사용하고 한 종류의 뷰를 지정할 수 있습니다.

	Mail::send(array('text' => 'view'), $data, $callback);

You may specify other options on the e-mail message such as any carbon copies or attachments as well: 이메일 메세지에 대해 참조나 첨부파일과 같은 다른 옵션들을 지정할 수도 있습니다:

	Mail::send('emails.welcome', $data, function($message)
	{
		$message->from('us@example.com', 'Laravel');

		$message->to('foo@example.com')->cc('bar@example.com');

		$message->attach($pathToFile);
	});

When attaching files to a message, you may also specify a MIME type and / or a display name: 이메일에 첨부파일을 추가하고자 할때는 파일의 MIME 타입 또는 첨부파일이 표시되는 이름을 지정할 수 있습니다:

	$message->attach($pathToFile, array('as' => $display, 'mime' => $mime));

> **Note 주의:** The message instance passed to a `Mail::send` Closure extends the SwiftMailer message class, allowing you to call any method on that class to build your e-mail messages. `Mail::send` 클로저에 전달되는 메세지 인스턴스는 SwiftMailer 메세지 클래스를 확장하므로 이메일을 작성하는데 필요한 클래스의 메소드들를 사용할 수 있습니다. 

<a name="embedding-inline-attachments"></a>
## Embedding Inline Attachments 인라인 첨부

Embedding inline images into your e-mails is typically cumbersome; however, Laravel provides a convenient way to attach images to your e-mails and retrieving the appropriate CID. 이메일에 인라인 이미지를 포함시키는 것은 번거로운 일입니다. 하지만 라라벨에서는 이메일에 이미지를 첨부하고 최적의 CID를 얻을 수 이쓴 편리한 방법을 제공합니다.

**Embedding An Image In An E-Mail View 이메일 뷰에서 이미지를 첨부하는 방법**

	<body>
		Here is an image:

		<img src="<?php echo $message->embed($pathToFile); ?>">
	</body>

**Embedding Raw Data In An E-Mail View 이메일 뷰에서 Raw 데이타를 첨부하는 방법**

	<body>
		Here is an image from raw data:

		<img src="<?php echo $message->embedData($data, $name); ?>">
	</body>

Note that the `$message` variable is always passed to e-mail views by the `Mail` class. `$message` 변수는 항상 `Mail` 클래스에 의해서 뷰에 전달된다는 것에 주의하십시오. 

<a name="queueing-mail"></a>
## Queueing Mail 메일 큐

Since sending e-mail messages can drastically lengthen the response time of your application, many developers choose to queue e-mail messages for background sending. 메일을 송신하는 것은 어플리케이션의 응답 시간을 크게 저하시키기 때문에 많은 개발자들은 이메일 메세지를 백그라운드 에서 보낼 수 있도록 큐-대기행열에 두도록 합니다. Laravel makes this easy using its built-in [unified queue API](/docs/queues). 라라벨에서는 내장된 [통일된 큐 API](/docs/queues)를 통해서 이러한 작업을 손쉽게 수행할 수 있게 합니다. To queue a mail message, simply use the `queue` method on the `Mail` class:
이메일 메세지를 대기 큐에 넣기 위해서는 간단하게 `Mail` 클래스의 `queue` 메소드를 호출하도록 하면 됩니다.

**Queueing A Mail Message 이메일 메세지 큐잉 하기**

	Mail::queue('emails.welcome', $data, function($message)
	{
		$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
	});

You may also specify the number of seconds you wish to delay the sending of the mail message using the `later` method: `later` 메소드를 통해서 이메일을 보낼 때까지의 초단위의 지연시간을 설정할 수도 있습니다:

	Mail::later(5, 'emails.welcome', $data, function($message)
	{
		$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
	});

If you wish to specify a specific queue or "tube" on which to push the message, you may do so using the `queueOn` and `laterOn` methods:
이메일 메세지를 등록하는 특정 큐 또는 "튜브"를 지정하려 한다면 `queueOn`과 `laterOn`  메소드를 사용하여 지정할 수 있습니다:
	Mail::queueOn('queue-name', 'emails.welcome', $data, function($message)
	{
		$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
	});

<a name="mail-and-local-development"></a>
## Mail & Local Development 메일 & 로컬 개발

When developing an application that sends e-mail, it's usually desirable to disable the sending of messages from your local or development environment. 이메일을 송신하는 어플리케이션을 개발하는 경우에 로컬 또는 개발 환경이라면 메세지 전송을 비활성화 하는것이 바람직할 것입니다. To do so, you may either call the `Mail::pretend` method, or set the `pretend` option in the `app/config/mail.php` configuration file to `true` 이를 지정하려면 `app/config/mail.php` 설정 파일에 `pretend` 옵션을 `true`로 설정하거나 `Mail::pretend`메소드를 호출하면 됩니다. When the mailer is in `pretend` mode, messages will be written to your application's log files instead of being sent to the recipient. 메일러가 `pretent` 모드인 경우에는 이메일 메세지는 수신자에게 송신되는 대신에 어플리케이션의 로그 파일에 기록됩니다. 

**Enabling Pretend Mail Mode Pretend 메일 모드 활성화 하기 **

	Mail::pretend();
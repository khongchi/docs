# HTTP 미들웨어 

- [소개](#introduction)
- [미들웨어 정의하기](#defining-middleware)
- [미들웨어 등록하기](#registering-middleware)
- [미들웨어 파라미터](#middleware-parameters)
- [종료시 동작하는 미들웨어](#terminable-middleware)

<a name="introduction"></a>
## 소개

HTTP 미들웨어는 어플리케이션으로 들어온 HTTP 요청을 간편하게 필터링할 수 있는 방법을 제공합니다. 예를 들어, 라라벨은 어플리케이션의 사용자가 인증되었는지 검사하는 미들웨어를 내장하고 있습니다. 만약 인증되지 않은 사용자라면, 미들웨어는 그 사용자를 로그인 화면으로 리다이렉트 시킬 것입니다. 반대로, 인증된 사용자라면, 미들웨어는 어플리케이션에서 HTTP 요청이 계속해서 더 처리되도록 허용할 것입니다.

물론, 인증이외에도 다양한 작업을 수행하는 추가적인 미들웨어를 작성할 수도 있습니다. CORS 미들웨어는 어플리케이션에서 내보내는 모든 응답에 적절한 헤더들을 추가하는 역할을 담당할 수도 있습니다. 로깅 미들웨어는 어플리케이션으로 들어오는 모든 요청을 기록할 수도 있습니다.

라라벨 프레임워크에는 보수(maintenance), 인증(authentication), CSRF 보안 등을 위한 미들웨어들이 포함되어 있습니다. 이러한 미들웨어들은 모두  `app/Http/Middleware` 디렉토리 안에 위치하고 있습니다.

<a name="defining-middleware"></a>
## 미들웨어 정의하기

새로운 미들웨어를 생성하기 위하여 `make:middleware` 아티즌 명령을 사용할 수 있습니다:

    php artisan make:middleware OldMiddleware

이 명령은 `OldMiddleware` 클래스를 생성하여 `app/Http/Middleware` 디렉토리에 위치시킬 것입니다. 이 미들웨어 에서 우리는 입력받은 `age`가 200보다 클 때에만 요청된 주소에 접근할 수 있도록 허용하려고 합니다. 그렇지 않은경우 사용자를 "home" URI 으로 리다이렉트할 것입니다.

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class OldMiddleware
    {
        /**
         * Run the request filter.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return mixed
         */
        public function handle($request, Closure $next)
        {
            if ($request->input('age') <= 200) {
                return redirect('home');
            }

            return $next($request);
        }

    }

위 코드에서 볼 수 있듯이, 주어진 `age`가 200보다 작거나 같으면 미들웨어는 HTTP 리다이렉트를 클라이언트에게 반환할 것입니다; 그렇지 않다면 HTTP 요청은 (미들웨어를 지나) 어플리케이션 안으로 계속 진행될 것입니다. (미들웨어가 "통과"를 허용하는) HTTP 요청을 어플리케이션 안으로 계속 전달하기 원한다면, 간단하게 `$next` 콜백함수에 `$request`인자를 넣어 호출하면 됩니다.

미들웨어를 HTTP request들이 어플리케이션에 도달하기 전에 반드시 통과해야 하는 일련의 "레이어"라고 생각하는 것이 가장 좋습니다. 각각의 레이어는 요청을 검사할 수 있고 완벽하게 요청을 거절할 수도 있습니다.

### *Before* / *After* 미들웨어 

어플리케이션이 HTTP 요청을 미들웨어가 처리하기 전에 실행될지 처리한 후에 실행될지는 미들웨어 자신이 결정할 수 있습니다. 예를 들어 다음의 미들웨어는 어플리케이션에서 HTTP 요청이 처리되기 **이전** 에 실행됩니다. 

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class BeforeMiddleware
    {
        public function handle($request, Closure $next)
        {
            // Perform action

            return $next($request);
        }
    }

반대로, 아래의 경우에서 미들웨어는 요청이 어플리케이션에 의해 처리된 **이후**에 실행될 것입니다.

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class AfterMiddleware
    {
        public function handle($request, Closure $next)
        {
            $response = $next($request);

            // Perform action

            return $response;
        }
    }

<a name="registering-middleware"></a>
## 미들웨어 등록하기

### 글로벌-전역 미들웨어

만약 어플리케이션의 모든 HTTP 요청에 대하여 미들웨어가 작동되기를 원한다면,  `app/Http/Kernel.php` 클래스의 `$middleware` 프로퍼티에 미들웨어를 등록하시면 됩니다.

### 라우트에 미들웨어 지정하기

미들웨어를 특정 라우트에만 할당하고 싶을 때에는 우선 `app/Http/Kernel.php` 파일에 그 미들웨어의 키(short-hand key)를 지정해야 합니다. 기본적으로, 이 Kernel 클래스의 `$routeMiddleware` 속성은 라라벨에 포함된 미들웨어들의 목록을 가지고 있습니다. 추가하려는 미들웨어에 원하는 키를 지정하고 이 목록에 붙여넣으십시오. 예를 들면:

    // Within App\Http\Kernel Class...

    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    ];

미들웨어를 HTTP 커널에 등록했다면, 라우트의 옵션 배열에서 `middleware` 키를 사용할 수 있습니다.

    Route::get('admin/profile', ['middleware' => 'auth', function () {
        //
    }]);

라우트에 여러개의 미들웨어를 지정할 때는 배열을 사용하면 됩니다.

    Route::get('/', ['middleware' => ['first', 'second'], function () {
        //
    }]);

배열을 사용하는 대신에, 라우트를 정의한 부분에 메소드 체이닝으로 `middleware` 메소드를 연결할 수도 있습니다. 

    Route::get('/', function () {
        //
    })->middleware(['first', 'second']);

<a name="middleware-parameters"></a>
## 미들웨어 파라미터 

미들웨어에서는 추가적으로 사용자가 지정한 파라미터를 전달 받을 수 있습니다. 예를 들어 어플리케이션이 인증된 사용자인지 확인하기 위해서 사용자의 주어진 "역할(role)" 
미들웨어는 추가 사용자 정의 매개 변수를받을 수 있습니다. 예를 들어 지정된 작업을 수행하기 전에 주어진 "역할 (role)"을 가진 인증 된 사용자인지 응용 프로그램에서 확인해야하는 경우 역할 이름을 추가 인수로 `RoleMiddleware` 을 만들 수 있습니다.

추가적인 미들웨어 파라미터는 미들웨어에 `$next` 인자 뒤에 전달 될것입니다. 

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class RoleMiddleware
    {
        /**
         * Run the request filter.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @param  string  $role
         * @return mixed
         */
        public function handle($request, Closure $next, $role)
        {
            if (! $request->user()->hasRole($role)) {
                // Redirect...
            }

            return $next($request);
        }

    }

미들웨어 파라미터는 라우트를 정의 할 때 지정된 미들웨어 이름과 파라미터를 `:` 로 구분하여 지정합니다. 여러개의 파라미터는 쉼표로 구분합니다.

    Route::put('post/{id}', ['middleware' => 'role:editor', function ($id) {
        //
    }]);

<a name="terminable-middleware"></a>
## 종료시 동작하는 미들웨어

때로는 미들웨어는 HTTP 응답을 브라우저로 전송하고 난 후에 어떤 작업을 수행할 필요가 있을지도 모릅니다. 예를 들어, 라라벨에 내장된 "session" 미들웨어는 응답이 브라우저로 보내진 _후_에 세션데이터를 저장소에 저장합니다. 이런 경우, 미들웨어를 "terminable"로 정의하고 `terminate` 메소드를 구성하면 됩니다.

    <?php

    namespace Illuminate\Session\Middleware;

    use Closure;

    class StartSession
    {
        public function handle($request, Closure $next)
        {
            return $next($request);
        }

        public function terminate($request, $response)
        {
            // Store the session data...
        }
    }

`terminate` 메소드는 Http 요청과 응답의 두가지를 전달 받는 구조여야 합니다. 종료시 동작하는 미들웨어를 정의하고나면, 이 미들웨어를 HTTP 커널의 글로벌-전역 미들웨어 목록에 추가해 주어야 합니다. 

미들웨어의 `terminate` 메소드가 호출될 때, 라라벨은 [서비스 컨테이너](/docs/{{version}}/container)에서 미들웨어의 새로운 인스턴스를 의존성 해결 하여 획득 할 것입니다. `handle` 과 `terminate` 메소드를 호출할 때, 동일한 미들웨어 인스턴스를 사용하려면 컨테이너의 `singleton` 메소드를 사용하여 미들웨어를 등록하십시오.

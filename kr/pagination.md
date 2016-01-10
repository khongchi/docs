# 페이지네이션

- [소개](#introduction)
- [기본적인 사용법](#basic-usage)
    - [쿼리 빌더 결과를 페이징 하기](#paginating-query-builder-results)
    - [Eloquent 결과를 페이징 하기](#paginating-eloquent-results)
    - [수동으로 Paginator 생성하기](#manually-creating-a-paginator)
- [View 에서 결과 출력하기](#displaying-results-in-a-view)
- [JSON으로 변환하기](#converting-results-to-json)

<a name="introduction"></a>
## 소개

다른 프레임 워크에서는 페이지 출력 처리를 하는것은 굉장히 성가신 일입니다. 하지만 라라벨에서 페이징 처리는 식은 죽 먹기입니다. 라라벨에서는 현재 페이지를 기준으로 페이지 링크들의 "범위"를 빠르고 손쉽게 생성할 수 있으며, 생성된 HTML은 [Bootstrap CSS framework](http://getbootstrap.com/)와 호환되니다. 

<a name="basic-usage"></a>
## 기본적인 사용법

<a name="paginating-query-builder-results"></a>
### 쿼리 빌더 결과를 페이징 하기

데이터들의 페이징을 처리 할 수 있는 몇가지 방법이 있습니다. 가장 쉬운 방법은 `paginate` 메소드를 [쿼리 빌더](/docs/{{version}}/queries) 혹은 [Eloquent 쿼리](/docs/{{version}}/eloquent)에서 사용하는 것 입니다. 라라벨에 의해서 제공되는 `paginate` 메소드는 자동으로 사용자가 확인하고 있는 현재의 페이지를 기준으로 하여 limit 과 offset을 결정합니다. 기본적으로 현재의 페이지는 HTTP 요청의 `?page` 쿼리 스트링 인자 값에 의해서 결정됩니다. 물론 이 값은 라라벨에 의해서 자동으로 확인되고, 또한 자동으로 paginator 에 의해서 생성되는 링크에 추가됩니다. 

먼저 쿼리에서 `paginate` 메소드를 호출하는 것을 살펴봅시다. 이 예제에서 `pagenate` 에 전달되는 인자는 여러분이 "한 페이지당" 표시하고자 하는 항목들의 갯수 입니다. 다음의 경우에는 페이지당 `15`개의 항목들을 표시하도록 지정해 봅시다. 

    <?php

    namespace App\Http\Controllers;

    use DB;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Show all of the users for the application.
         *
         * @return Response
         */
        public function index()
        {
            $users = DB::table('users')->paginate(15);

            return view('user.index', ['users' => $users]);
        }
    }

> **주의:** 현재, 라라벨에서 `groupBy` 구문을 사용한 경우, pagination 이 효과적으로 실행될 수 없습니다. 만약 `groupBy`와 함께 paginate 를 사용해야 한다면, 수동으로 paginator 을 생성하여 쿼리를 질의하는것을 권장합니다. 

#### "간단한 페이징 작업"

만약 "다음" 과 "이전" 링크 만을 페이징 된 화면에서 보여주고자 한다면, 더 효율적으로 쿼리를 수행하는 옵션인 `simplePaginate` 메소드를 사용할 수 있습니다. 이 메소드는 화면에서 정확안 페이지 번호를 표시할 필요가 없는 큰 규모의 데이터 셋을 처리할 때 유용합니다. 

    $users = DB::table('users')->simplePaginate(15);

<a name="paginating-eloquent-results"></a>
### Eloquent 결과를 페이징 하기

또한 여러분은 [Eloquent](/docs/{{version}}/eloquent) 쿼리 에서도 페이징 처리를 할 수 있습니다. 다음 예제에서는 `User` 모델을 페이지 별로 `15`개로 페이지를 나누어 보여줄 것입니다. 보시다 시피 문법은 쿼리 빌더 결과를 페이징 하는 것과 같이 직관적입니다. 

    $users = App\User::paginate(15);

당연하게도, `where` 절과 같은 조건을 쿼리에 지정한 후 `paginate` 를 호출 할 수 있습니다:

    $users = User::where('votes', '>', 100)->paginate(15);

또한 Elqouent 모델에 대해서 페이지를 구성할 때에도, `simplePaginate` 메소드를 사용할 수 있습니다:

    $users = User::where('votes', '>', 100)->simplePaginate(15);

<a name="manually-creating-a-paginator"></a>
### 수동으로 Paginator 생성하기

때로는 여러분은 항목들의 배열을 전달하면서 수동으로 pagination 인스턴스를 생성하고자 할 수도 있습니다. 필요에 따라 `Illuminate\Pagination\Paginator` 또는  `Illuminate\Pagination\LengthAwarePaginator` 인스턴스를 생성하여 구성할 수 있습니다. 

`Paginator` 클래스는 결과 셋에 설정되어있는 항목의 총수를 알 필요는 없습니다. 따라서 클래스 마지막 페이지의 인덱스를 가져오는 메소드를 가지고 있지 않습니다. 그에 반해서 `LengthAwarePaginator` 는 `Paginator` 과 거의 같은 인자를 전달 받지만, 결과 셋에 설정되어있는 전체 항목의 갯수를 필요로 합니다.

다시 말해, `Paginator` 는 쿼리 빌더와 Eloquent에 대한 `simplePaginate` 메소드에 대응 되고, `LengthAwarePaginator` 는 paginate 에 대응하고 있습니다.

수동으로 paginator 인스턴스를 생성할 때에는, 직접 paginator 에 전달되는 결과 배열을 "잘라" 주어야만 합니다. 어떻게 해야할지 잘 모르겠다면, [array_slice](http://php.net/manual/en/function.array-slice.php) PHP 함수를 참고하십시오. 

<a name="displaying-results-in-a-view"></a>
## View 에서 결과 출력하기

쿼리 빌더 또는 Eloquent 쿼리에서 `paginate` 와 `simplePaginate` 메소드를 호출할 때 여러분은 paginator 인스턴스를 받습니다. `paginate` 메소드를 호출 할 때에는 `Illuminate\Pagination\LengthAwarePaginator` 의 인스턴스를 받습니다. `simplePaginate` 메소드를 호출 할 때에는 `Illuminate\Pagination\Paginator`의 인스턴스를 받습니다. 이러한 객체들은 결과 셋을 나타내는 다양한 방법을 제공합니다. 추가적으로 이러한 헬퍼 함수들 안에서 paginator 인스턴스는 iterators 이며 배열처럼 반복문에서 사용할 수 있습니다. 

즉, 결과를 얻은 뒤에, 그 결과와 페이지 링크를 [Blade](/docs/{{version}}/blade)에서 사용할 수 있습니다.

    <div class="container">
        @foreach ($users as $user)
            {{ $user->name }}
        @endforeach
    </div>

    {!! $users->render() !!}

`render` 메소드는 결과 셋에서 페이지 링크를 렌더링 할 것입니다. 각각의 링크에는 이미 `?page` 쿼리 스트링 변수가 포함되어 있을 것입니다. `render` 메소드에 의해서 생성되는 HTML은 [Bootstrap CSS framework](https://getbootstrap.com)와 호환된 다는 것을 기억하십시오. 

> **주의:** 블레이드 템플릿에서 `render` 메소드를 호출할 때, `{!! !!}` 문법을 통해서 HTML 링크가 이스케이프 되지 않도록 사용하는 것에 유의 하십시오. 

#### 사용자 지정 Paginator URI 구성하기

`setPath` 메소드는 paginator 가 링크를 생성할 때 사용자가 지정한 URI를 구성할 수 있게 해줍니다. 예를 들어 paginator 가 `http://example.com/custom/url?page=N`와 같은 링크를 생성하게 하려면 `setPath` 메소드에 `custom/url` 을 전달해야 합니다. 

    Route::get('users', function () {
        $users = App\User::paginate(15);

        $users->setPath('custom/url');

        //
    });

#### 페이지 링크에 추가하기

`appends` 메소드를 사용하면 페이지 링크에 쿼리 스트링을 추가할 수 있습니다. 예를 들어, 각각의 페이지 링크에 `&sort=votes`를 추가하려면, 다음과 같이 `appends` 를 호출해야 합니다. 

    {!! $users->appends(['sort' => 'votes'])->render() !!}

만약 "hash fragment"를 페이지 번호의 링크에 추가하고 싶다면 `fragment` 메소드를 사용하면 됩니다. 예를 들어 각각의 페이지 링크에 `#foo`를 추가하고자 한다면, 다음과 같이 `fragment` 메소드를 호출하면 됩니다. 

    {!! $users->fragment('foo')->render() !!}

#### 추가적인 헬퍼 메소드들

또한 paginator 인스턴스에서 다음의 메소드를 통해서 추가적인 페이지 정보에 엑세스 할 수도 있습니다.

- `$results->count()`
- `$results->currentPage()`
- `$results->hasMorePages()`
- `$results->lastPage() (Not available when using simplePaginate)`
- `$results->nextPageUrl()`
- `$results->perPage()`
- `$results->previousPageUrl()`
- `$results->total() (Not available when using simplePaginate)`
- `$results->url($page)`

<a name="converting-results-to-json"></a>
## JSON으로 변환하기 

라라벨의 paginator 결과 클래스는 `Illuminate\Contracts\Support\JsonableInterface` contract을 구현하고 `toJson` 메소드를 제공하고 있어서, pagination 결과를 JSON으로 변환하는 것은 아주 쉽습니다. 

또한 라우트나 컨트롤러 액션에서 paginator 인스턴스를 JSON으로 변환할 수도 있습니다. 

    Route::get('users', function () {
        return App\User::paginate();
    });

paginator로 부터 변환된 JSON은 `total`, `current_page`, `last_page` 및 여러가지의 메타정보를 포함하고 있을 것입니다. 실제 결과 객체는 JSON 배열의 `data` 키를 통해서 사용이 가능합니다. 다음은 라우트에서 paginator 인스턴스를 반환하여 생성된 JSON 의 예제 입니다. 

#### Paginator JSON 예제

    {
       "total": 50,
       "per_page": 15,
       "current_page": 1,
       "last_page": 4,
       "next_page_url": "http://laravel.app?page=2",
       "prev_page_url": null,
       "from": 1,
       "to": 15,
       "data":[
            {
                // Result Object
            },
            {
                // Result Object
            }
       ]
    }

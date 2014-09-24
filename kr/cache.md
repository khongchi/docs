# Cache 캐시

- [Configuration 설정](#configuration)
- [Cache Usage 캐시의 사용](#cache-usage)
- [Increments & Decrements 증감 조작](#increments-and-decrements)
- [Cache Sections 캐시 섹션](#cache-sections)
- [Database Cache 데이터베이스 캐시](#database-cache)

<a name="configuration"></a>
## Configuration 설정

Laravel provides a unified API for various caching systems. 라라벨은 다양한 캐시 시스템을 위한 통일 된 API를 제공합니다. The cache configuration is located at `app/config/cache.php`. 캐시 설정은 `app/config/cache.php` 으로 설정되어 있습니다. In this file you may specify which cache driver you would like used by default throughout your application. 이 파일에서 전체 응용 프로그램에서 기본으로 사용하는 캐시 드라이버를 지정합니다. Laravel supports popular caching backends like [Memcached](http://memcached.org) and [Redis](http://redis.io) out of the box. 라라벨은 [Memcached](http://memcached.org) 나 [Redis](http://redis.io) 와 같은 인기있는 백엔드 캐시 시스템을 쉽게 사용할 수 있도록 지원합니다.

The cache configuration file also contains various other options, which are documented within the file, so make sure to read over these options. 캐시 설정 파일은 다양한 다른 옵션을 포함합니다. 댓글에 설명되어 있으므로 잘 읽어 보시기 바랍니다. By default, Laravel is configured to use the `file` cache driver, which stores the serialized, cached objects in the filesystem. 라라벨의 기본 캐시 드라이버는 `file` 캐시 드라이버로 설정되어 있습니다. For larger applications, it is recommended that you use an in-memory cache such as Memcached or APC. 큰 응용 프로그램은 Memecached 나 APC와 같은 in-memory 캐시를 사용하는 것을 추천합니다.

<a name="cache-usage"></a>
## Cache Usage 캐시의 사용

**Storing An Item In The Cache 캐시에 하나의 항목을 저장하기**

	Cache::put('key', 'value', $minutes);

**Storing An Item In The Cache If It Doesn't Exist 캐시에 존재하지 않는 항목이면 캐시에 저장하기**

	Cache::add('key', 'value', $minutes);

**Checking For Existence In Cache 캐시에 존재하는지 확인하기**

	if (Cache::has('key'))
	{
		//
	}

**Retrieving An Item From The Cache 캐시에서 하나의 항목을 찾기**

	$value = Cache::get('key');

**Retrieving An Item Or Returning A Default Value 찾는 항목이 없으면 기본값을 반환하기**

	$value = Cache::get('key', 'default');

	$value = Cache::get('key', function() { return 'default'; });

**Storing An Item In The Cache Permanently 캐시에 영구적으로 저장하기**

	Cache::forever('key', 'value');

Sometimes you may wish to retrieve an item from the cache, but also store a default value if the requested item doesn't exist. You may do this using the `Cache::remember` method: 간혹 캐시에서 항목을 조회할 뿐만 아니라, 만약 값이 없을 경우 기본 값을 캐시에 저장하고 싶을 경우가 있을겁니다. 그럴 때는`Cache::remember` 메서드를 사용하면 됩니다.

	$value = Cache::remember('users', $minutes, function()
	{
		return DB::table('users')->get();
	});

You may also combine the `remember` and `forever` methods: `remember`와 `forever`를 결합하여 사용할 수도 있습니다.

	$value = Cache::rememberForever('users', function()
	{
		return DB::table('users')->get();
	});

Note that all items stored in the cache are serialized, so you are free to store any type of data. 캐시에 저장되는 모든 항목들은 직렬화되고, 따라서 어떤 유형의 데이터도 자유롭게 저장할 수 있습니다.

**Removing An Item From The Cache 캐시에서 항목 삭제**

	Cache::forget('key');

<a name="increments-and-decrements"></a>
## Increments & Decrements 증감 조작

All drivers except `file` and `database` support the `increment` and `decrement` operations: `file`과 `database`를 제외한 드라이버는 `increment` 및 `decrement` 조작을 지원하고 있습니다.

**Incrementing A Value 값 증가시키기**

	Cache::increment('key');

	Cache::increment('key', $amount);

**Decrementing A Value 값 감소시키기**

	Cache::decrement('key');

	Cache::decrement('key', $amount);

<a name="cache-sections"></a>
## Cache Sections 캐시 섹션

> **Note:** Cache sections are not supported when using the `file` or `database` cache drivers. **참고:** `file` 이나 `database` 캐시 드라이버를 사용시에는 캐시 섹션은 지원되지 않습니다.

Cache sections allow you to group related items in the cache, and then flush the entire section. To access a section, use the `section` method: 캐시 섹션은 캐시된 항목들 중 관련된 항목들을 묶을 수 있도록 해주고, 그렇게 생성된 섹션을 통째로 지울 수 있도록 해줍니다.섹션을 사용하려면 `section` 메소드를 사용하십시오.

**Accessing A Cache Section 캐시 섹션에 접근하기**

	Cache::section('people')->put('John', $john, $minutes);

	Cache::section('people')->put('Anne', $anne, $minutes);

You may also access cached items from the section, as well as use the other cache methods such as `increment` and `decrement`: 섹션에서 캐시된 항목을 접근할 수 있을 뿐 아니라, `increment` 나 `decrement`와 같은 캐시 메소드를 사용할 수 있습니다.

**Accessing Items In A Cache Section 캐시 섹션에서 아이템에 접근하기**

	$anne = Cache::section('people')->get('Anne');

Then you may flush all items in the section: 섹션 내의 모든 항목을 삭제할 수 있습니다.

	Cache::section('people')->flush();

<a name="database-cache"></a>
## Database Cache 데이터베이스 캐시

When using the `database` cache driver, you will need to setup a table to contain the cache items. Below is an example `Schema` declaration for the table: `database` 캐시 드라이버를 사용하는 경우에는, 캐시 항목을 저장할 테이블을 준비해야 합니다. 아래는 테이블 생성을 위한 Schema 의 예입니다.

	Schema::create('cache', function($table)
	{
		$table->string('key')->unique();
		$table->text('value');
		$table->integer('expiration');
	});

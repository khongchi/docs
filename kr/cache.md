# Cache

- [Configuration 설정](#configuration)
- [Cache Usage 캐시의 사용](#cache-usage)
- [Increments & Decrements 증감의 조작](#increments-and-decrements)
- [Cache Tags 캐시 태그](#cache-tags)
- [Database Cache 데이터베이스 캐시](#database-cache)

<a name="configuration"></a>
## Configuration 설정

Laravel provides a unified API for various caching systems. 라라벨은 다양한 캐시 시스템을 위한 통일 된 API를 제공합니다. The cache configuration is located at `app/config/cache.php`. 캐시 설정은 `app/config/cache.php` 으로 설정되어 있습니다. In this file you may specify which cache driver you would like used by default throughout your application. 이 파일에서 전체 응용 프로그램에서 기본으로 사용하는 캐시 드라이버를 지정합니다. Laravel supports popular caching backends like [Memcached](http://memcached.org) and [Redis](http://redis.io) out of the box. 라라벨은 [Memcached](http://memcached.org) 나 [Redis](http://redis.io) 와 같은 인기있는 백엔드 캐시 시스템을 쉽게 사용할 수 있도록 지원합니다.

The cache configuration file also contains various other options, which are documented within the file, so make sure to read over these options. 캐시 설정 파일은 다양한 다른 옵션을 포함합니다. 댓글에 설명되어 있으므로 잘 읽어 보시기 바랍니다. By default, Laravel is configured to use the `file` cache driver, which stores the serialized, cached objects in the filesystem. 라라벨의 기본 캐시 드라이버는 `file` 캐시 드라이버로 설정되어 있습니다. For larger applications, it is recommended that you use an in-memory cache such as Memcached or APC. 큰 응용 프로그램은 Memecached 나 APC와 같은 in-memory 캐시를 사용하는 것을 추천합니다.

<a name="cache-usage"></a>
## Cache Usage 캐시의 사용

#### Storing An Item In The Cache 캐시에 하나의 항목을 저장하기

     Cache::put('key', 'value', $minutes);

#### Using Carbon Objects To Set Expire Time Carbon 객체를 사용하여 만료시간 설정하기 

	$expiresAt = Carbon::now()->addMinutes(10);

	Cache::put('key', 'value', $expiresAt);

#### Storing An Item In The Cache If It Doesn't Exist 캐시에 존재하지 않는 항목이면 캐시에 저장하기

     Cache::add('key', 'value', $minutes);

The `add` method will return `true` if the item is actually **added** to the cache. `add` 메소드는 항목이 실제로 캐시에 **추가** 된 경우에만 true 를 반환합니다. Otherwise, the method will return `false`. 그렇지 않으면 `false` 를 반환합니다.

#### Checking For Existence In Cache 캐시에 존재하는지 확인하기

     if (Cache::has('key'))
     {
          //
     }

#### Retrieving An Item From The Cache 캐시에서 하나의 항목을 찾기

     $value = Cache::get('key');

#### Retrieving An Item Or Returning A Default Value 찾는 항목이 없으면 기본값을 반환하기

     $value = Cache::get('key', 'default');

     $value = Cache::get('key', function() { return 'default'; });

#### Storing An Item In The Cache Permanently 캐시에 영구적으로 저장하기

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

#### Pulling An Item From The Cache 캐시에서 항목 뽑아내기

If you need to retrieve an item from the cache and then delete it, you may use the `pull` method: 캐시에서 항목을 조회한 후에 삭제를 할 필요가 있으면, `pull` 메소드를 사용합니다.

	$value = Cache::pull('key');

#### Removing An Item From The Cache 캐시에서 항목 삭제

     Cache::forget('key');

<a name="increments-and-decrements"></a>
## Increments & Decrements 증감 조작

All drivers except `file` and `database` support the `increment` and `decrement` operations: `file`과 `database`를 제외한 드라이버는 `increment` 및 `decrement` 조작을 지원하고 있습니다.

#### Incrementing A Value 값 증가시키기

     Cache::increment('key');

     Cache::increment('key', $amount);

#### Decrementing A Value 값 감소시키기

     Cache::decrement('key');

     Cache::decrement('key', $amount);

<a name="cache-tags"></a>
## Cache Tags 캐시 태그

> **Note:** Cache tags are not supported when using the `file` or `database` cache drivers. Furthermore, when using multiple tags with caches that are stored "forever", performance will be best with a driver such as `memcached`, which automatically purges stale records. **참고:** 캐시 태그는 `file` 과 `database` 캐시 드라이버는 지원되지 않습니다. 또한 "forever"로 저장되는 캐시에 많은 태그를 사용하면 기존 레코드를 자동으로 삭제하는 memcached 와 같은 드라이버에서 최상의 성능을 낼 것입니다.

#### Accessing A Tagged Cache 태그된 캐시에 액세스하기

Cache tags allow you to tag related items in the cache, and then flush all caches tagged with a given name. To access a tagged cache, use the `tags` method. 캐시 태그는 캐시에 있는 관련된 아이템들을 태그 할 수 있도록 해주고, 같은 태그가 지정된 전체 캐시를 지울 수도 있습니다. 태그된 캐시에 액세스하려면 `tags` 메소드를 사용하십시오.

You may store a tagged cache by passing in an ordered list of tag names as arguments, or as an ordered array of tag names: 태그 이름 목록을 인수로 전달하거나, 배열로 만들어 전달하여 태그된 캐시를 저장할 수 있습니다.

	Cache::tags('people', 'authors')->put('John', $john, $minutes);

	Cache::tags(array('people', 'artists'))->put('Anne', $anne, $minutes);

You may use any cache storage method in combination with tags, including `remember`, `forever`, and `rememberForever`. You may also access cached items from the tagged cache, as well as use the other cache methods such as `increment` and `decrement`. `remember` , `forever` , `rememberForever` 및 이들의 을 포함한 모든 캐시 저장 방법 및 태그를 조합하여 사용할 수 있습니다. 또한 increment 및 decrement 같은 다른 캐시 메소드도 마찬가지로 태그를 캐시 항목에 액세스 할 수 있습니다.

#### Accessing Items In A Tagged Cache 태그된 캐시에 있는 아이템에 접근하기

To access a tagged cache, pass the same ordered list of tags used to save it. 태그가 지정된 캐쉬에 액세스하려면 저장된 것과 동일한 순서로 태그의 목록을 전달합니다.

	$anne = Cache::tags('people', 'artists')->get('Anne');

	$john = Cache::tags(array('people', 'authors'))->get('John');

You may flush all items tagged with a name or list of names. For example, this statement would remove all caches tagged with either `people`, `authors`, or both. So, both "Anne" and "John" would be removed from the cache: 이름 또는 이름의 목록을 지정하고 태그 된 항목을 모두 지울 수 있습니다. 예를 들어 다음 코드는 `people` 또는 `authors` 또는 둘 모두에 태그 된 모든 캐시 된 항목이 삭제됩니다. 그러므로 "Anne"과 "John"두 아이템은 캐시에서 제거됩니다:

	Cache::tags('people', 'authors')->flush();

In contrast, this statement would remove only caches tagged with `authors`, so "John" would be removed, but not "Anne". 한편, 다음 코드에서는 `authors` 태그 된 캐시 만 삭제되기 때문에 "John"은 삭제되지만 "Anne"는 남아 있습니다.

	Cache::tags('authors')->flush();

<a name="database-cache"></a>
## Database Cache 데이터베이스 캐시

When using the `database` cache driver, you will need to setup a table to contain the cache items. You'll find an example `Schema` declaration for the table below: `database` 캐시 드라이버를 사용하는 경우 캐시 항목을 저장할 테이블을 준비해야합니다. 아래에서 테이블을 만드는 `Schema` 의 예를 확인 할 수 있습니다.

	Schema::create('cache', function($table)
	{
		$table->string('key')->unique();
		$table->text('value');
		$table->integer('expiration');
	});
	
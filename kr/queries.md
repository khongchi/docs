# Query Builder

- [Introduction](#introduction) 소개
- [Selects](#selects)
- [Joins](#joins)
- [Advanced Wheres](#advanced-wheres)
- [Aggregates](#aggregates)
- [Raw Expressions](#raw-expressions)
- [Inserts](#inserts)
- [Updates](#updates)
- [Deletes](#deletes)
- [Unions](#unions)
- [Pessimistic Locking](#pessimistic-locking)
- [Caching Queries](#caching-queries)

<a name="introduction"></a>
## Introduction 소개

The database query builder provides a convenient, fluent interface to creating and running database queries. 데이터베이스 쿼리 빌더는 데이터베이스 쿼리들을 만들고 운영하는데 다양한 인터페이스로 편의를 제공한다.It can be used to perform most database operations in your application, and works on all supported database systems.여러분들의 어플리케이션에서 운영하는 대부분의 데이터베이스에서 작동할것이다.

> **Note:** The Laravel query builder uses PDO parameter binding throughout to protect your application against SQL injection attacks. 라라벨 쿼리 빌더는 PDO parameter에 바인딩하여 SQL injection공격을 막는다.There is no need to clean strings being passed as bindings. 바인딩하여 보냄으로써 문자들을 정제할 필요가 없다.

<a name="selects"></a>
## Selects

#### Retrieving All Rows From A Table 한 테이블에서 모든 행들을 추출한다

	$users = DB::table('users')->get();

	foreach ($users as $user)
	{
		var_dump($user->name);
	}

#### Retrieving A Single Row From A Table 한 테이블에서 하나의 행을 추출한다

	$user = DB::table('users')->where('name', 'John')->first();

	var_dump($user->name);

#### Retrieving A Single Column From A Row 한 컬럼에서 하나의 행를 추출한다

	$name = DB::table('users')->where('name', 'John')->pluck('name');

#### Retrieving A List Of Column Values 컬럼 값들의 리스트를 추출한다.

	$roles = DB::table('roles')->lists('title');

This method will return an array of role titles. 이 메소드는 제목들의 배열을 반환할 것이다 You may also specify a custom key column for the returned array:
반환된 배열에 custom key column을 특정할 수도 있다:
	$roles = DB::table('roles')->lists('title', 'name');

#### Specifying A Select Clause  select절 특정하기

	$users = DB::table('users')->select('name', 'email')->get();

	$users = DB::table('users')->distinct()->get();

	$users = DB::table('users')->select('name as user_name')->get();

#### Adding A Select Clause To An Existing Query  이미 있는 쿼리에 select절 추가하기

	$query = DB::table('users')->select('name');

	$users = $query->addSelect('age')->get();

#### Using Where Operators  where연산자 자용

	$users = DB::table('users')->where('votes', '>', 100)->get();

#### Or Statements orwhere 사용

	$users = DB::table('users')
	                    ->where('votes', '>', 100)
	                    ->orWhere('name', 'John')
	                    ->get();

#### Using Where Between  whereBetween사용

	$users = DB::table('users')
	                    ->whereBetween('votes', array(1, 100))->get();

#### Using Where Not Between whereNotBetween사용

	$users = DB::table('users')
	                    ->whereNotBetween('votes', array(1, 100))->get();

#### Using Where In With An Array 하나의 배열로 whereIn에 사용하기

	$users = DB::table('users')
	                    ->whereIn('id', array(1, 2, 3))->get();

	$users = DB::table('users')
	                    ->whereNotIn('id', array(1, 2, 3))->get();

#### Using Where Null To Find Records With Unset Values  값이 설정되지 않은 레코드 찾는데 whereNull사용하기

	$users = DB::table('users')
	                    ->whereNull('updated_at')->get();

#### Order By, Group By, And Having

	$users = DB::table('users')
	                    ->orderBy('name', 'desc')
	                    ->groupBy('count')
	                    ->having('count', '>', 100)
	                    ->get();

#### Offset & Limit

	$users = DB::table('users')->skip(10)->take(5)->get();

<a name="joins"></a>
## Joins

The query builder may also be used to write join statements. Take a look at the following examples: 쿼리 빌더를 사용해서 조인문(join statement)을만들수 있다. Take a look at the following examples:다음 예들을 보자:

#### Basic Join Statement 기본 조인문

	DB::table('users')
	            ->join('contacts', 'users.id', '=', 'contacts.user_id')
	            ->join('orders', 'users.id', '=', 'orders.user_id')
	            ->select('users.id', 'contacts.phone', 'orders.price')
	            ->get();

#### Left Join Statement

	DB::table('users')
		    ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
		    ->get();

You may also specify more advanced join clauses: 더 복잡한 조인문을 사용할 수도 있다.:

	DB::table('users')
	        ->join('contacts', function($join)
	        {
	        	$join->on('users.id', '=', 'contacts.user_id')->orOn(...);
	        })
	        ->get();

If you would like to use a "where" style clause on your joins, you may use the `where` and `orWhere` methods on a join. 조인문에서 "where"스타일을 사용하고 싶으면 where, orwhere method를 사용한다. Instead of comparing two columns, these methods will compare the column against a value: 두열을 비교하는것 대신에 열의 값을 비교하는것이다.

	DB::table('users')
	        ->join('contacts', function($join)
	        {
	        	$join->on('users.id', '=', 'contacts.user_id')
	        	     ->where('contacts.user_id', '>', 5);
	        })
	        ->get();

<a name="advanced-wheres"></a>
## Advanced Wheres

#### Parameter Grouping

Sometimes you may need to create more advanced where clauses such as "where exists" or nested parameter groupings. 가끔 where exists나 nested parameter groupings와 같이 복잡한 where문법을 사용해야 할 수 있다.  The Laravel query builder can handle these as well: 라라벨 쿼리 빌더는 다음과같이 사용할 수 있다.

	DB::table('users')
	            ->where('name', '=', 'John')
	            ->orWhere(function($query)
	            {
	            	$query->where('votes', '>', 100)
	            	      ->where('title', '<>', 'Admin');
	            })
	            ->get();

The query above will produce the following SQL:위쿼리는 다음과같은 SQL을 만든다:
	select * from users where name = 'John' or (votes > 100 and title <> 'Admin')

#### Exists Statements

	DB::table('users')
	            ->whereExists(function($query)
	            {
	            	$query->select(DB::raw(1))
	            	      ->from('orders')
	            	      ->whereRaw('orders.user_id = users.id');
	            })
	            ->get();

The query above will produce the following SQL:위쿼리는 다음과같은 SQL을 만든다:

	select * from users
	where exists (
		select 1 from orders where orders.user_id = users.id
	)

<a name="aggregates"></a>
## Aggregates

The query builder also provides a variety of aggregate methods, such as `count`, `max`, `min`, `avg`, and `sum`.

#### Using Aggregate Methods

	$users = DB::table('users')->count();

	$price = DB::table('orders')->max('price');

	$price = DB::table('orders')->min('price');

	$price = DB::table('orders')->avg('price');

	$total = DB::table('users')->sum('votes');

<a name="raw-expressions"></a>
## Raw Expressions

Sometimes you may need to use a raw expression in a query. These expressions will be injected into the query as strings, so be careful not to create any SQL injection points! To create a raw expression, you may use the `DB::raw` method:
Sometimes you may need to use a raw expression in a query.가끔 저수준(raw expression)방식을 사용할 필요가 있을수 있다. These expressions will be injected into the query as strings, so be careful not to create any SQL injection points! To create a raw expression, you may use the `DB::raw` method:이 방식들은 문자로 쿼리를 만드는데 SQL injection 공격에 조심해야한다. `DB::raw` method와 같이 사용한다. :

#### Using A Raw Expression

	$users = DB::table('users')
	                     ->select(DB::raw('count(*) as user_count, status'))
	                     ->where('status', '<>', 1)
	                     ->groupBy('status')
	                     ->get();

#### Incrementing or decrementing a value of a column 컬럼값을 늘리거나 줄이기

	DB::table('users')->increment('votes');

	DB::table('users')->increment('votes', 5);

	DB::table('users')->decrement('votes');

	DB::table('users')->decrement('votes', 5);

You may also specify additional columns to update: 특정 컬럼을 update하도록 특정할 수도 있다.


	DB::table('users')->increment('votes', 1, array('name' => 'John'));

<a name="inserts"></a>
## Inserts

#### Inserting Records Into A Table 한테이블에 레코드 추가하기

	DB::table('users')->insert(
		array('email' => 'john@example.com', 'votes' => 0)
	);

#### Inserting Records Into A Table With An Auto-Incrementing ID 자동증분ID 방식 테이블에 레코드 추가하기


If the table has an auto-incrementing id, use `insertGetId` to insert a record and retrieve the id:만약테이블에 자동증분 id가 있다면 `insertGetId`를 사용해서 레코드를 추가한 id를 가져올 수 있다.:


	$id = DB::table('users')->insertGetId(
		array('email' => 'john@example.com', 'votes' => 0)
	);

> **Note:** When using PostgreSQL the insertGetId method expects the auto-incrementing column to be named "id". PostgreSQL에서 insertGetId 메소드를 사용할때는 자동증분 컬럼 명칭이 "id"이다.

#### Inserting Multiple Records Into A Table  한테이블에 여러 레코드들을 추가하기

	DB::table('users')->insert(array(
		array('email' => 'taylor@example.com', 'votes' => 0),
		array('email' => 'dayle@example.com', 'votes' => 0),
	));

<a name="updates"></a>
## Updates

#### Updating Records In A Table 한 테이블에 레코드들을 업데이트하기

	DB::table('users')
	            ->where('id', 1)
	            ->update(array('votes' => 1));

<a name="deletes"></a>
## Deletes

#### Deleting Records In A Table  한 테이블에 레코드들 지우기

	DB::table('users')->where('votes', '<', 100)->delete();

#### Deleting All Records From A Table  한 테이블에 모든 레코드를 지우기

	DB::table('users')->delete();

#### Truncating A Table

	DB::table('users')->truncate();

<a name="unions"></a>
## Unions

The query builder also provides a quick way to "union" two queries together:쿼리 빌더에서 두 쿼리에 "union"을 사용할 수 있다.:

	$first = DB::table('users')->whereNull('first_name');

	$users = DB::table('users')->whereNull('last_name')->union($first)->get();

The `unionAll` method is also available, and has the same method signature as `union`.`unionAll` 메소드도 사용할 수 있고 union과 같은 특징이 있다.

<a name="pessimistic-locking"></a>
## Pessimistic Locking

The query builder includes a few functions to help you do "pessimistic locking" on your SELECT statements.
SELECT구문에서 pessimistic locking을 사용하는데 도움이되는 몇가지 함수가 있다.

To run the SELECT statement with a "shared lock", you may use the `sharedLock` method on a query:
쿼리에서 sharedLock methd를 사용하면 SELECT구문에서 shared lock을 실행할 수 있다.

	DB::table('users')->where('votes', '>', 100)->sharedLock()->get();

To "lock for update" on a SELECT statement, you may use the `lockForUpdate` method on a query:
쿼리에서 lockForUpdate method를 사용하면 SELECT구문에서 lock for update를 할 수 있다. 

	DB::table('users')->where('votes', '>', 100)->lockForUpdate()->get();

<a name="caching-queries"></a>
## Caching Queries  쿼리 결과 캐쉬하기

You may easily cache the results of a query using the `remember` method:쿼리 결과를 캐쉬하는데 `remember` 메소드를 사용한다:

	$users = DB::table('users')->remember(10)->get();

In this example, the results of the query will be cached for ten minutes. 예에서 쿼리 결과를 10분동안 캐시한다. While the results are cached, the query will not be run against the database, and the results will be loaded from the default cache driver specified for your application.결과가 캐쉬되는동안 쿼리는 실행되지 않고 결과들은 어플리케이션에 default cache driver로부터 load된다.

If you are using a [supported cache driver](/docs/cache#cache-tags), you can also add tags to the caches: 지원되는 캐쉬 드라이버를 사용하면 캐쉬에 태그를 붙일 수 있다. 

	$users = DB::table('users')->cacheTags(array('people', 'authors'))->remember(10)->get();

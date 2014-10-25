# Basic Database Usage

- [Configuration](#configuration)
- [Read / Write Connections](#read-write-connections)
- [Running Queries](#running-queries)
- [Database Transactions](#database-transactions)
- [Accessing Connections](#accessing-connections)
- [Query Logging](#query-logging)

<a name="configuration"></a>
## Configuration 설정

Laravel makes connecting with databases and running queries extremely simple. 라라벨은 데이터베이스 연결과 쿼리 사용을 매우 쉽게 해줍니다. The database configuration file is `app/config/database.php`. 데이터베이스 설정파일은 `app/config/database.php`이다. In this file you may define all of your database connections, as well as specify which connection should be used by default. 이 파일에서 모든 데이터베이스 연결을 정의한다.  Examples for all of the supported database systems are provided in this file. 모든 지원하는 데이터베이스 예제는 이 파일안에 있다.

Currently Laravel supports four database systems: MySQL, Postgres, SQLite, and SQL Server. 현재 라라벨이 지원하는 데이터베이스는 MySQL, Postgres, SQLite, SQL Server이다.

<a name="read-write-connections"></a>
## Read / Write Connections

Sometimes you may wish to use one database connection for SELECT statements, and another for INSERT, UPDATE, and DELETE statements. 때로는 SELECT문에서 INSERT, UPDATE, DELETE문을 사용하는 데이터베이스와 다른 데이터베이스 연결을 사용할고 싶은 경우도 있습니다. Laravel makes this a breeze, and the proper connections will always be used whether you are using raw queries, the query builder, or the Eloquent ORM. 라라벨은 이를 쉽게 할 수 있습니다. 생쿼리를 사용하든 쿼리 빌더나 에로퀀트 ORM이든 적절한 연결들을 사용할 수 있습니다.

To see how read / write connections should be configured, let's look at this example:read/write 연결들을 설정하는 실제 예로 다음을 보자:

	'mysql' => array(
		'read' => array(
			'host' => '192.168.1.1',
		),
		'write' => array(
			'host' => '196.168.1.2'
		),
		'driver'    => 'mysql',
		'database'  => 'database',
		'username'  => 'root',
		'password'  => '',
		'charset'   => 'utf8',
		'collation' => 'utf8_unicode_ci',
		'prefix'    => '',
	),

Note that two keys have been added to the configuration array: `read` and `write`.설정 배열에 두개의 키가 추가 됐다.:`read`와 `write`. Both of these keys have array values containing a single key: `host`. 이 키들은 싱글 키로 배열 값을 갖는다: `host`. The rest of the database options for the `read` and `write` connections will be merged from the main `mysql` array. `read`와 `write` 연결에대한 나머지 데이터베이스 옵션들은 기본 `mysql` 배열에서 합쳐(merge)진다. So, we only need to place items in the `read` and `write` arrays if we wish to override the values in the main array. 주배열에 값들중 재정의하고자하는 것들만 `read`와 `write` 배열에서 입력하면된다. So, in this case, `192.168.1.1` will be used as the "read" connection, while `192.168.1.2` will be used as the "write" connection. 위 예에서 `192.168.1.1`에서 "read" 하고 `192.168.1.2`에서 "write"한다. The database credentials, prefix, character set, and all other options in the main `mysql` array will be shared across both connections.주 `mysql`배열에 포함된 데이터베이스 연결정보, 프리픽스, 캐릭터 세트 등 다른 모든 옵션들은 양쪽연결에서 모두 공유한다.

<a name="running-queries"></a>
## Running Queries 쿼리 실행

Once you have configured your database connection, you may run queries using the `DB` class.  일단 데이터베이스 연결설정을하면, `DB` 클래스를 사용해서 쿼리를 실행할 수 있다. 

#### Running A Select Query // Select 쿼리실행

	$results = DB::select('select * from users where id = ?', array(1));

The `select` method will always return an `array` of results. `select` 메소드는 항상 결과를 배열로 반환한다. 


#### Running An Insert Statement // Insert문 실행

	DB::insert('insert into users (id, name) values (?, ?)', array(1, 'Dayle'));

#### Running An Update Statement // Update문 실행

	DB::update('update users set votes = 100 where name = ?', array('John'));

#### Running A Delete Statement // Delete문 실행

	DB::delete('delete from users');

> **Note:** The `update` and `delete` statements return the number of rows affected by the operation. `update`와 `delete`문은 해당 작업이 영향을 끼친 행수를 반환한다.

#### Running A General Statement // 일반적인 SQL문 실행

	DB::statement('drop table users');

#### Listening For Query Events

You may listen for query events using the `DB::listen` method: `DB::listen`메소드를 사용하여 쿼리 이벤트를 받을 수 있다:

	DB::listen(function($sql, $bindings, $time)
	{
		//
	});

<a name="database-transactions"></a>
## Database Transactions 데이터베이스 트랜잭션

To run a set of operations within a database transaction, you may use the `transaction` method: 일련의 작업들을 하나의 데이터베이스 트랜잭션으로 실행할때 `transaction`메소드를 사용하라:

	DB::transaction(function()
	{
		DB::table('users')->update(array('votes' => 1));

		DB::table('posts')->delete();
	});

> **Note:** Any exception thrown within the `transaction` closure will cause the transaction to be rolled back automatically. `transaction` 클로저로 어떤 예외를 던져도 자동으로 트랜잭션을 롤백시킨다.

Sometimes you may need to begin a transaction yourself: 때로는 트랜잭션을 직접 시작해야하는 경우도 있다:

	DB::beginTransaction();

You can rollback a transaction via the `rollback` method: `rollback`메소드로 트랜잭션을 롤백 할 수 있다:

	DB::rollback();

Lastly, you can commit a transaction via the `commit` method: 마지막으로, `commit`메소드로 트랜잭션을 commit할 수 있다.

	DB::commit();

<a name="accessing-connections"></a>
## Accessing Connections 연결 액세스

When using multiple connections, you may access them via the `DB::connection` method: 여러 연결들을 사용할때 `DB::connection`메소드를 통해 액세스 할 수 있다:

	$users = DB::connection('foo')->select(...);

You may also access the raw, underlying PDO instance: PDO 인스턴스로 액세스 할 수 있다:

	$pdo = DB::connection()->getPdo();

Sometimes you may need to reconnect to a given database:  때로는 주어진 데이터데이스에 다시 연결해야 할 수도 있다:

	DB::reconnect('foo');

If you need to disconnect from the given database due to exceeding the underlying PDO instance's `max_connections` limit, use the `disconnect` method: PDO인스턴스의 `max_connections`한계를 넘어서 데이터베이스 연결을 끊어야하는경우 `disconnect`메소드를 사용해라:

	DB::disconnect('foo');

<a name="query-logging"></a>
## Query Logging 쿼리 로그

By default, Laravel keeps a log in memory of all queries that have been run for the current request. 기본적으로 라라벨은 현재 요청되어 실행하는 모든쿼리의 로그를 메모리에 저장한다.  However, in some cases, such as when inserting a large number of rows, this can cause the application to use excess memory. 그러나 대량으로 행을 입력하는 상황과같이 일부 경우에는 어플리케이션이 과도하게 메모리를 사용하는 원이이 되기도 한다. To disable the log, you may use the `disableQueryLog` method: 로그를 비활성화하려면 `disableQueryLog`메소드를 사용하라 :

	DB::connection()->disableQueryLog();

To get an array of the executed queries, you may use the `getQueryLog` method: 실행된 쿼리들의 배열을 얻으려면 `getQueryLog`메소드를 사용해라.

       $queries = DB::getQueryLog();

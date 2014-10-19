# Basic Database Usage 기본적인 데이터베이스 사용법

- [Configuration](#configuration)
- [Running Queries](#running-queries)
- [Database Transactions](#database-transactions)
- [Accessing Connections](#accessing-connections)
- [Query Logging](#query-logging)

<a name="configuration"></a>
## Configuration 설정

Laravel makes connecting with databases and running queries extremely simple. 라라벨은 데이터베이스 연결과 쿼리 사용을 매우 쉽게 해줍니다. The database configuration file is `app/config/database.php`. 데이터베이스 설정파일은 `app/config/database.php`이다. In this file you may define all of your database connections, as well as specify which connection should be used by default. 이 파일에서 모든 데이터베이스 연결을 정의한다.  Examples for all of the supported database systems are provided in this file. 모든 지원하는 데이터베이스 예제는 이 파일안에 있다.

Currently Laravel supports four database systems: MySQL, Postgres, SQLite, and SQL Server. 현재 라라벨이 지원하는 데이터베이스는 MySQL, Postgres, SQLite, SQL Server이다.

<a name="running-queries"></a>
## Running Queries 쿼리 실행

Once you have configured your database connection, you may run queries using the `DB` class. 일단 데이터베이스 연결설정을하면, `DB` 클래스를 사용해서 쿼리를 실행할 수 있다. 

**Running A Select Query** Select 쿼리실행

	$results = DB::select('select * from users where id = ?', array(1));

The `select` method will always return an `array` of results. `select` 메소드는 항상 결과를 배열로 반환한다. 

**Running An Insert Statement** Insert문 실행

	DB::insert('insert into users (id, name) values (?, ?)', array(1, 'Dayle'));

**Running An Update Statement** Update문 실행

	DB::update('update users set votes = 100 where name = ?', array('John'));

**Running A Delete Statement** Delete문 실행

	DB::delete('delete from users');

> **Note:** The `update` and `delete` statements return the number of rows affected by the operation. `update`와 `delete`문은 해당 작업이 영향을 끼친 행수를 반환한다.

**Running A General Statement** 일반적인 SQL문 실행

	DB::statement('drop table users');

You may listen for query events using the `DB::listen` method: `DB::listen`메소드를 사용하여 쿼리 이벤트를 받을 수 있다.

**Listening For Query Events**

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

<a name="accessing-connections"></a>
## Accessing Connections 연결 액세스

When using multiple connections, you may access them via the `DB::connection` method:
여러 연결들을 사용할때 `DB::connection`메소드를 통해 액세스 할 수 있다:

	$users = DB::connection('foo')->select(...);

You may also access the raw, underlying PDO instance: PDO 인스턴스로 액세스 할 수 있다:

	$pdo = DB::connection()->getPdo();

Sometimes you may need to reconnect to a given database: 때로는 주어진 데이터데이스에 다시 연결해야 할 수도 있다.

	DB::reconnect('foo');

<a name="query-logging"></a>
## Query Logging 쿼리 로그

By default, Laravel keeps a log in memory of all queries that have been run for the current request. 기본적으로 라라벨은 현재 요청되어 실행하는 모든쿼리의 로그를 메모리에 저장한다. However, in some cases, such as when inserting a large number of rows, this can cause the application to use excess memory. 그러나 대량으로 행을 입력하는 상황과같이 일부 경우에는 어플리케이션이 과도하게 메모리를 사용하는 원이이 되기도 한다. To disable the log, you may use the `disableQueryLog` method: 로그를 비활성화하혀면 `disableQueryLog`메소드를 사용하라 :

	DB::connection()->disableQueryLog();

To get an array of the executed queries, you may use the `getQueryLog` method: 실행된 쿼리들의 배열을 얻으려면 `getQueryLog`메소드를 사행해라.

       $queries = DB::getQueryLog();

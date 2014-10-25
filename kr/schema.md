# Schema Builder

- [Introduction 소개](#introduction)
- [Creating & Dropping Tables 테이블 생성및 삭제](#creating-and-dropping-tables)
- [Adding Columns 컬럼 추가](#adding-columns)
- [Renaming Columns 컬럼 이름 변경](#renaming-columns)
- [Dropping Columns 컬럼 삭제](#dropping-columns)
- [Checking Existence 있는지 확인하기](#checking-existence)
- [Adding Indexes 인덱스 추가](#adding-indexes)
- [Foreign Keys 외래 키](#foreign-keys)
- [Dropping Indexes 인덱스 삭제](#dropping-indexes)
- [Storage Engines 스토리지 엔진](#storage-engines)

<a name="introduction"></a>
## Introduction 소개

The Laravel `Schema` class provides a database agnostic way of manipulating tables. 라라벨의 `Schema` 클래스는 테이블을 다루는 쉬운(agnostic) 방법을 제공한다. It works well with all of the databases supported by Laravel, and has a unified API across all of these systems.라라벨이 지원하는 모든 데이터베이스에서 잘 작동하는 공통의 API를 가지고 있다.

<a name="creating-and-dropping-tables"></a>
## Creating & Dropping Tables 테이블 생성및 삭제

To create a new database table, the `Schema::create` method is used:
새로운 데이터베이스 테이블을 생성하기위해 `Schema::create`메소드를 사용한다:

	Schema::create('users', function($table)
	{
		$table->increments('id');
	});

The first argument passed to the `create` method is the name of the table, and the second is a `Closure` which will receive a `Blueprint` object which may be used to define the new table. `Creat` 메소드의 첫번째인수는 테이블 이름으로 두 번째는 새 테이블을 정의하는데 쓰이는 Bluprint 객체를 받을 `Closure`이다.

To rename an existing database table, the `rename` method may be used:
존재하는 데이터베이스 테이블 이름을 바꿀때 `rename`메소드를 사용한다.

	Schema::rename($from, $to);

To specify which connection the schema operation should take place on, use the `Schema::connection` method:
스키마 작업에 커넥션을 지정하는것은 `Schema::connection`메소드를 사용한다:

	Schema::connection('foo')->create('users', function($table)
	{
		$table->increments('id');
	});

To drop a table, you may use the `Schema::drop` method:
테이블을 삭제하려면 `Schema::drop`메소드를 사용한다:

	Schema::drop('users');

	Schema::dropIfExists('users');

<a name="adding-columns"></a>
## Adding Columns 컬럼 추가

To update an existing table, we will use the `Schema::table` method:
존재하는 테이블을 업데이트하려면 `Schema::table`메소드를 사용한다:

	Schema::table('users', function($table)
	{
		$table->string('email');
	});

The table builder contains a variety of column types that you may use when building your tables:
테이블 빌더에는 테이블들을 만들때 사용할 수 있는 다양한 컬럼 종류들이 있다:

Command  | Description
------------- | -------------
`$table->bigIncrements('id');`  |  Incrementing ID using a "big integer" equivalent.증분ID를""big integer"로 만든것.
`$table->bigInteger('votes');`  |  BIGINT equivalent to the table 테이블에 BIGINT 컬럼 
`$table->binary('data');`  |  BLOB equivalent to the table 테이블에 BLOB형 컬럼
`$table->boolean('confirmed');`  |  BOOLEAN equivalent to the table 테이블에 BOOLEAN 컬럼
`$table->char('name', 4);`  |  CHAR equivalent with a length 테이블에 길이를 지정한 CHAR 컬럼
`$table->date('created_at');`  |  DATE equivalent to the table 테이블에 DATE 컬럼
`$table->dateTime('created_at');`  |  DATETIME equivalent to the table 테이블에 DATETIME형 컬럼
`$table->decimal('amount', 5, 2);`  |  DECIMAL equivalent with a precision and scale precision과 scale된 DECIMAL 형
`$table->double('column', 15, 8);`  |  DOUBLE equivalent with precision  precision된 DOUBLE형 컬럼
`$table->enum('choices', array('foo', 'bar'));` | ENUM equivalent to the table 테이블에 ENUM형 컬럼
`$table->float('amount');`  |  FLOAT equivalent to the table 테이블에 FLOAT형 컬럼
`$table->increments('id');`  |  Incrementing ID to the table (primary key). 테이블 증분ID 
`$table->integer('votes');`  |  INTEGER equivalent to the table 테이블에 INTEGER 컬럼
`$table->longText('description');`  |  LONGTEXT equivalent to the table 테이블에 LONGTEXT 컬럼
`$table->mediumText('description');`  |  MEDIUMTEXT equivalent to the table 테이블에 MEDIUMTEXT 컬럼
`$table->morphs('taggable');`  |  Adds INTEGER `taggable_id` and STRING `taggable_type` 정수형 `taggable_id`와 문자형 `taggable_type` 추가
`$table->smallInteger('votes');`  |  SMALLINT equivalent to the table 테이블에 SMALLINT 컬럼
`$table->tinyInteger('numbers');`  |  TINYINT equivalent to the table 테이블에 TINYINT 컬럼
`$table->softDeletes();`  |  Adds **deleted\_at** column for soft deletes 논리적인 삭제용으로 **deleted\_at**컬럼 추가
`$table->string('email');`  |  VARCHAR equivalent column  VARCHAR 컬럼
`$table->string('name', 100);`  |  VARCHAR equivalent with a length 길이를 지정한 VARCHAR 컬럼
`$table->text('description');`  |  TEXT equivalent to the table 테이블에 TEXT형 컬럼
`$table->time('sunrise');`  |  TIME equivalent to the table 테이블에 TIME형 컬럼
`$table->timestamp('added_on');`  |  TIMESTAMP equivalent to the table 테이블에 TIMESTAMP형 컬럼
`$table->timestamps();`  |  Adds **created\_at** and **updated\_at** columns **Created\_at** 과 **updated\_at**컬럼 추가 
`->nullable()`  |  Designate that the column allows NULL values 컬럼에 NULL값 허용하기
`->default($value)`  |  Declare a default value for a column 컬럼에 디폴트 값 선언
`->unsigned()`  |  Set INTEGER to UNSIGNED 부호없는 INTEGER 설정

#### Using After On MySQL MySQL에서 After 사용하기

If you are using the MySQL database, you may use the `after` method to specify the order of columns: MySQL 데이터베이스를 사용한다면 `after`메소드로 컬럼 순서를 지정할 수 있다:

	$table->string('name')->after('email');

<a name="renaming-columns"></a>
## Renaming Columns 컬럼 이름 변경

To rename a column, you may use the `renameColumn` method on the Schema builder. `renameColumn`메소드를 사용해서 컬럼 이름을 변경한다. Before renaming a column, be sure to add the `doctrine/dbal` dependency to your `composer.json` file. 컬럼 이름을 변경하기 전에 `composer.json`에 `doctrine/dbal` 의존성을 추가했는지 확인해라.

	Schema::table('users', function($table)
	{
		$table->renameColumn('from', 'to');
	});

> **Note:** Renaming `enum` column types is not supported. `enum`컬럼 타입을 이름 변경하는 것은 지원하지 않는다.

<a name="dropping-columns"></a>
## Dropping Columns 컬럼 삭제

#### Dropping A Column From A Database Table 단일 데이터베이스 테이블에서 단일 컬럼 삭제

	Schema::table('users', function($table)
	{
		$table->dropColumn('votes');
	});

#### Dropping Multiple Columns From A Database Table 단일 데이터베이스 테이블에서 여러개 컬럼 삭제

	Schema::table('users', function($table)
	{
		$table->dropColumn('votes', 'avatar', 'location');
	});

<a name="checking-existence"></a>
## Checking Existence 있는지 확인하기

#### Checking For Existence Of Table 테이블이 있는지 확인

You may easily check for the existence of a table or column using the `hasTable` and `hasColumn` methods:`hasTable`과 `hasColumn` 메소드를 사용해서 테이블이나 컬럼이 존재하는지 확인 할 수 있다:

	if (Schema::hasTable('users'))
	{
		//
	}

#### Checking For Existence Of Columns 컬럼이 있는지 확인

	if (Schema::hasColumn('users', 'email'))
	{
		//
	}

<a name="adding-indexes"></a>
## Adding Indexes 인텍스 추가

The schema builder supports several types of indexes.스키마 빌더는 여러가지 타입의 인덱스를 지원한다. There are two ways to add them.추가하는 방법은 두가지가 있다. First, you may fluently define them on a column definition, or you may add them separately: 첫번째 방법은 컬럼 정의하면서 동시에 추가할 수 있고 두번째는 이를 분리해서 추가하는것이다:

	$table->string('email')->unique();

Or, you may choose to add the indexes on separate lines. Below is a list of all available index types: 인덱스를 추가하는 라인을 분리 할 수 있다. 아래 예는 사용할 수 있는 인덱스 타입들의 목록이다.

Command  | Description
------------- | -------------
`$table->primary('id');`  |  Adding a primary key 프라이머리 키 추가
`$table->primary(array('first', 'last'));`  |  Adding composite keys 복합 키 추가
`$table->unique('email');`  |  Adding a unique index 유니크 인덱스 추가
`$table->index('state');`  |  Adding a basic index 기본 인덱스 추가

<a name="foreign-keys"></a>
## Foreign Keys 외래 키

Laravel also provides support for adding foreign key constraints to your tables:
라라벨은 테이블에 외래키 제약을 거는것도 지원한다:

	$table->foreign('user_id')->references('id')->on('users');

In this example, we are stating that the `user_id` column references the `id` column on the `users` table. 이 예에서 `user_id` 컬럼이`users`테이블에 `id`컬럼을 참조함을 선언한다.

You may also specify options for the "on delete" and "on update" actions of the constraint: 외래키 제약 조건에 대해 "on delete", "on update" 옵션을 지정할 수 있다:

	$table->foreign('user_id')
          ->references('id')->on('users')
          ->onDelete('cascade');

To drop a foreign key, you may use the `dropForeign` method. 외래키를 삭제하는데 `dropForeign`메소드를 사용한다. A similar naming convention is used for foreign keys as is used for other indexes: 다른 인덱스에서 사용되는것처럼 외래키에서도 비슷한 명명규칙이 사용된다:

	$table->dropForeign('posts_user_id_foreign');

> **Note:** When creating a foreign key that references an incrementing integer, remember to always make the foreign key column `unsigned`. 정수형 증분되는 값을 참조하여 외래키를 만들때 외래키 컬럼을 항상 `unsigned`로 만드는것을 기억하라.

<a name="dropping-indexes"></a>
## Dropping Indexes 인덱스 삭제

To drop an index you must specify the index's name. 인덱스를 삭제하려면 인덱스 이름을 지정해야 한다. Laravel assigns a reasonable name to the indexes by default. 라라벨은 기본적으로 의미가 있는 이름을 할당한다. Simply concatenate the table name, the names of the column in the index, and the index type. 간단하게 테이블 이름, 인덱스가 있는 컬럼 이름, 인데스 타입을 연결한것이다. Here are some examples: 몇가지 예가 있다:

Command  | Description
------------- | -------------
`$table->dropPrimary('users_id_primary');`  |  Dropping a primary key from the "users" table "user"테이블에서 프라이머리 키 삭제
`$table->dropUnique('users_email_unique');`  |  Dropping a unique index from the "users" table "users"테이블에서 유니크 인덱스 삭제
`$table->dropIndex('geo_state_index');`  |  Dropping a basic index from the "geo" table "geo"테이블에서 기본 인덱스 삭제

<a name="storage-engines"></a>
## Storage Engines 스토리지 엔진

To set the storage engine for a table, set the `engine` property on the schema builder:
테이블에 스토리지 엔진을 설정하려면 스키마 빌더에 `engine`프로퍼티를 설정한다:
    Schema::create('users', function($table)
    {
        $table->engine = 'InnoDB';

        $table->string('email');
    });

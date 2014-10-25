# Migrations & Seeding

- [Introduction 소개](#introduction)
- [Creating Migrations 마이그레이션 생성](#creating-migrations)
- [Running Migrations 마이그레이션 실행](#running-migrations)
- [Rolling Back Migrations 마이그레이션 롤백](#rolling-back-migrations)
- [Database Seeding 데이터베이스 초기 값 설정](#database-seeding)

<a name="introduction"></a>
## Introduction 소개

Migrations are a type of version control for your database. 마이그레이션은 데이터베이스용 버전 컨트롤의 일종입니다. They allow a team to modify the database schema and stay up to date on the current schema state. Migrations are typically paired with the [Schema Builder](/docs/schema) to easily manage your application's schema. 마이그레이션은 전형적으로 응용 프로그램의 스키마를 쉽게 관리할 수 있는 [Schema Builder](/docs/schema) 와 함께 사용됩니다.

<a name="creating-migrations"></a>
## Creating Migrations 마이그레이션 생성

To create a migration, you may use the `migrate:make` command on the Artisan CLI:
마이그레이션을 만드려면 `migrate:make`아티산 CLI 명령어를 사용한다.

	php artisan migrate:make create_users_table

The migration will be placed in your `app/database/migrations` folder, and will contain a timestamp which allows the framework to determine the order of the migrations.  마이그레이션은 `app/database/migrations`폴더에 있고 마이그레이션 순서를 결정하기 위해 timestamp가 포함되어 있다.


You may also specify a `--path` option when creating the migration. 마이그레이션을 만들때`--path`옵션을 지정할 수 있다. The path should be relative to the root directory of your installation: 설치한 루트 디렉토리에서 상대경로이다.

	php artisan migrate:make foo --path=app/migrations

The `--table` and `--create` options may also be used to indicate the name of the table, and whether the migration will be creating a new table: `--table`과 `--create`옵션은 테이블 이름과 마이그레이션하면서 새 테이블을 만들것인지를 지정하는데 사용할 수 있습니다.

	php artisan migrate:make add_votes_to_user_table --table=users

	php artisan migrate:make create_users_table --create=users

<a name="running-migrations"></a>
## Running Migrations 마이그레이션 실행

#### Running All Outstanding Migrations 실행되지 않은 전체 마이그레이션 실행

	php artisan migrate

#### Running All Outstanding Migrations For A Path 지정된 경로의 실행되지 않은 전체 마이그레이션 실행

	php artisan migrate --path=app/foo/migrations

#### Running All Outstanding Migrations For A Package 패키지의 실행되지 않은 전체 마이그레이션 실행

	php artisan migrate --package=vendor/package

> **Note:** If you receive a "class not found" error when running migrations, try running the `composer dump-autoload` command.  마이그레이션을 실행할때 만약 "class not found"에러가 생기면 `composer dump-autoload` 명령어를 실행해 봐라.

### Forcing Migrations In Production

Some migration operations are destructive, meaning they may cause you to lose data. 일부 마이그레이션 작업은 파괴적이어서 데이터를 잃을 수 있습니다. In order to protect you from running these commands against your production database, you will prompted for confirmation before these commands are executed. 이 명령들을로부터  사용하고 있는(production) 데이터베이스를 보호하기위해 이 명령어를 실행하기 전에 확인 메시지가 출력됩니다. To force the commands to run without a prompt, use the `--force` flag: 확인 메시지 없이 명령을 실행시키기 위해서 `--force`플래그를 사용합니다.

	php artisan migrate --force

<a name="rolling-back-migrations"></a>
## Rolling Back Migrations 마이그레이션 롤백

#### Rollback The Last Migration Operation 마지막 마이그레이션 작업으로 롤백

	php artisan migrate:rollback

#### Rollback all migrations 모든 마이그레이션 롤백

	php artisan migrate:reset

#### Rollback all migrations and run them all again 모든 마이그레이션을 롤백하고 다시 실행

	php artisan migrate:refresh

	php artisan migrate:refresh --seed

<a name="database-seeding"></a>
## Database Seeding 데이터베이스 초기 값 설정

Laravel also includes a simple way to seed your database with test data using seed classes.라라벨은 seed 클래스를 사용해서 테스트 데이터를 데이터베이스에 설정하는 간단한 방법을 제공한다. All seed classes are stored in `app/database/seeds`.모든 seed 클래스는 `app/database/seeds`에 있다. Seed classes may have any name you wish, but probably should follow some sensible convention, such as `UserTableSeeder`, etc. Seed 클래스는 자유롭게 명명할 수 있지만 아마도 `UserTableSeeder`등과 같이 직관적인 명명규칙을 따르는것이 좋다. By default, a `DatabaseSeeder` class is defined for you. 기본적으로 `DatabaseSeeder`클래스가 정의 되어 있다. From this class, you may use the `call` method to run other seed classes, allowing you to control the seeding order. 이 클래스에서 `call`메소드로 다른 seed 클래스를  호출해서 seeding 순서를 조정 할 수 있다.

#### Example Database Seed Class 데이터베이스 seed 클래스 예

	class DatabaseSeeder extends Seeder {

		public function run()
		{
			$this->call('UserTableSeeder');

			$this->command->info('User table seeded!');
		}

	}

	class UserTableSeeder extends Seeder {

		public function run()
		{
			DB::table('users')->delete();

			User::create(array('email' => 'foo@bar.com'));
		}

	}

To seed your database, you may use the `db:seed` command on the Artisan CLI:
데이터베이스에 초기값을 설정(seed)하려면 `db:seed`아티산 CLI 명령어를 사용해라:

	php artisan db:seed

By default, the `db:seed` command runs the `DatabaseSeeder` class, which may be used to call other seed classes. 기본적으로 `db:seed` 명령어는 다른 seed클래스들을 호출하는 `DatabaseSeeder`클래스를 살행한다. However, you may use the `--class` option to specify a specific seeder class to run individually: 그러나 `--class`옵션을 사용해서 특정 seeder 클래스가 개별적으로 실행되도록 지정할 수 있다:

	php artisan db:seed --class=UserTableSeeder

You may also seed your database using the `migrate:refresh` command, which will also rollback and re-run all of your migrations: `migrate:refresh`명령어를 사용해서 데이터베이스 초기값을 설정할 수 있는데 모든 마이그레이션들을 롤백한다음 다시 실행 할 것이다.


	php artisan migrate:refresh --seed

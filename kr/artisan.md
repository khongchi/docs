# Artisan CLI 아티즌 CLI

- [introduction 소개](#introduction)
- [usage 사용법](#usage)

<a name="introduction"></a>
## Introduction 소개

Artisan is the name of the command-line interface included with Laravel. 아티즌(Artisan)은 라라벨에 포함된 커맨드라인 인터페이스(CLI)의 이름입니다. It provides a number of helpful commands for your use while developing your application. 아티즌은 어플리케이션 개발에 도움을 주는 많은 명령어들을 제공합니다. It is driven by the powerful Symfony Console component. 아티즌은 강력한 Symfony 콘솔 콤포넌트에서 동작합니다.

<a name="usage"></a>
## Usage 사용법

To view a list of all available Artisan commands, you may use the `list` command: 사용가능한 아티즌의 전체 명령어들을 확인하려면 `list` 명령어를 입력하면 됩니다:

**Listing All Available Commands 사용 가능한 전체 아티즌 명령어 목록 확인**

	php artisan list

Every command also includes a "help" screen which displays and describes the command's available arguments and options. 모든 명령어는 각각의 설명과 사용 가능한 인수및 옵션을 보여주는 "도움말" 화면을 포함하고 있습니다. To view a help screen, simply precede the name of the command with `help`: 도움말 화면을 표시하려면 명령어 앞에 `help`를 입력하십시오.

**Viewing The Help Screen For A Command 명령어의 도움말 화면 보기 **

	php artisan help migrate

You may specify the configuration environment that should be used while running a command using the `--env` switch: `--env` 스위치 옵션을 통해서 명령어가 구동되는 환경을 지정해 줄 수 있습니다: 

**Specifying The Configuration Environment 명령어의 구동 환경 지정**

	php artisan migrate --env=local

 You may also view the current version of your Laravel installation using the `--version` option: 설치되어 있는 라라벨의 버전을 확인하려면 `--version` 옵션을 입력하면 됩니다:

**Displaying Your Current Laravel Version 설치된 라라벨의 버전 확인**

	php artisan --version

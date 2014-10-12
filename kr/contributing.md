# Contributing To Laravel 라라벨에 기여하기

- [Introduction 소개](#introduction)
- [Pull Requests 풀 리퀘스트](#pull-requests)
- [Coding Guidelines 코딩 가이드라인](#coding-guidelines)

<a name="introduction"></a>
## Introduction 소개

Laravel is free, open-source software, meaning anyone can contribute to its development and progress. 라라벨은 누구라도 개발과 진행에 기여할 수 있는 자유, 오픈소스 소프트웨어 입니다. Laravel source code is currently hosted on [Github](https://github.com/laravel), which provides an easy method for forking the project and merging your contributions. 라라벨 소스 코드는 현재 [Github](https://github.com/laravel)에서 관리되고 있으며, 프로젝트를 포크하고 여러 사람들의 기여를 병합하는 간단한 방법을 제공합니다.

<a name="pull-requests"></a>
## Pull Requests 풀 리퀘스트

The pull request process differs for new features and bugs. 새로운 기능을 추가하는 것과 버그를 수정하는 풀 리퀘스트는 구분됩니다. Before sending a pull request for a new feature, you should first create an issue with `[Proposal]` in the title. 새로운 기능 추가를 위한 풀 리퀘스트를 추가하기 전에 먼저 `[Proposal-제안]`이라는 제목의 이슈를 생성해야 합니다. The proposal should describe the new feature, as well as implementation ideas. 이 제안은 새로운 기능과 구현에 대한 아이디어를 잘 설명할 수 있어야 합니다. The proposal will then be reviewed and either approved or denied. 제안이 이루어 지면 검토후에 채택되어 지거나, 거부될 수 있습니다. Once a proposal is approved, a pull request may be created implementing the new feature. 제안이 채택되어 지면 새로운 기능 구현을 위한 풀 리퀘스트가 생성되어 집니다. Pull requests which do not follow this guideline will be closed immediately. 이러한 지침을 따르지 않는 풀 리퀘스트는 즉시 종료됩니다. 

Pull requests for bugs may be sent without creating any proposal issue. 버그 수정을 위한 플 리퀘스트는 별다른 제안 이슈없이 바로 진행됩니다. If you believe that you know of a solution for a bug that has been filed on Github, please leave a comment detailing your proposed fix. 만약 Github에 제기된 버그를 해결하는 방법을 알고 있다면, 자세한 해결 방법을 코멘트로 남겨주세요.

Additions and corrections to the documentation may also be contributed via the [documentation repository](https://github.com/laravel/docs) on Github. 매뉴얼 문서의 추가나 수정사항에 대한 기여는 Github의 [매뉴얼 저장소](https://github.com/laravel/docs) 에서 진행할 수 있습니다.



### Feature Requests 새로운 기능 요청

If you have an idea for a new feature you would like to see added to Laravel, you may create an issue on Github with `[Request]` in the title. 새롭게 라라벨에 추가하고자 하는 기능에 대한 아이디어가 있다면 제목에 `[Request]`를 붙인 이슈를 Github에 작성하십시오. The feature request will then be reviewed by a core contributor. 해당 기능에 대해서 코어 컨트리뷰터에 의해서 검토됩니다.

<a name="coding-guidelines"></a>
## Coding Guidelines 코딩 가이드라인

Laravel follows the [PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md) and [PSR-1](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md) coding standards. 라라벨은 [PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md) 과 [PSR-1](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md) 코딩 표준을 따릅니다. In addition to these standards, below is a list of other coding standards that should be followed: 그리고 이외에도 다음과 같은 코딩 표준들을 따라야 합니다:

- Namespace declarations should be on the same line as `<?php`.
- 네임스페이스 선언은 `<?php` 와 같은 라인에 있어야 합니다.
- Class opening `{` should be on the same line as the class name.
- `{` 는 클래스 이름과 같은 라인에 있어야 합니다.
- Function and control structure opening `{` should be on a separate line.
- 함수와 제어 구조의 시작 `{`는 다른 라인에 있어야 합니다.
- Interface names are suffixed with `Interface` (`FooInterface`)
- 인터페이스는 (`FooInterface`) 와 같이 `Interface` 접미사를 붙입니다.

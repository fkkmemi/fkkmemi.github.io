---
title: "ruby update osx"
permalink: /docs/ruby-update-osx/
excerpt: "forjekyll blog dependancy update"
last_modified_at: 2017-08-26T16:40:00+09:00
redirect_from:
  - /theme-setup/
---

현재 이 블로그는 jekyll로 만들어져 있는데.. github에서 보안이슈로 사이트에 문제가 있다고 경고가 떠있었다.

*We found a potential security vulnerability in one of your dependencies.  
 A dependency defined in ./Gemfile.lock has known security vulnerabilities and should be updated.*

물론 동작에는 이상이 없었지만. 의존요소를 업데이트 하고 싶었는데. 

업데이트를 해보니 루비가 2.2.5 이상이어야된다고 했다.

그래서 루비부터 의존요소까지 전부 업데이트를 했다.

# ruby update

```bash
$ rvm install 2.5.0 --with-openssl-dir=/usr/local/opt/openssl
```

> 뒤에 openssl 옵션이 없으면 gem이 동작하지 않으니 꼭 붙여야한다..

# bundler install

```bash
$ gem install bundler
```

# site install

```bash
$ bundle install
```

# site run

```bash
$ jekyll serve --watch
```

까먹을 것 같아서 기록해본다.

이제 정상 작동됨..

# Yay! You're on AWS! 

## 0. AWS 가입

[Amazon Web Services](https://aws.amazon.com/ko/)에 접속하여 회원 가입을 한다.
- 회원 가입을 할 때, 해외 결제가 되는 카드가 필요하다.
- 카드 등록 시 결제되는 1달러는 카드가 유효한지 검사하는 가결제이므로 곧 환불 처리 된다.


## 1. 사용할 프로젝트 선택

- 본인의 GitHub에 프로젝트 Repository가 있어야 한다.
- 개인 프로젝트가 없는 사람은 [fake_insta](https://github.com/classtak/fake_insta)를 fork 해서 사용하자.


## 2. Figaro(중요!)

각종 중요한 정보들이 외부에 노출되지 않도록 관리해주는 gem이 [figaro](https://github.com/laserlemon/figaro)이다.

Gemfile에 figaro를 추가한다.

```ruby
gem 'figaro'
```

`bundle install` 명령어로 gem을 설치한다.

```console
$ bundle install
```

아래의 명령어를 입력하여 figaro를 설치한다.

```console
$ bundle exec figaro install
```

설치가 완료되면 `config` 폴더 안에 `application.yml` 파일이 생성된 것을 확인할 수 있다.


## 3. Lightsail

### 3.1. Lightsail 생성

Lightsail을 검색하거나 '컴퓨팅' 항목에서 Lightsail을 찾는다.

![Find Lightsail](/images/001.png)

Lightsail 인스턴스를 생성한다.

![Create Instance](/images/002.png)

설정은 다음과 같이 한다.

![Instance Configure](/images/003.png)

- 인스턴스 위치가 `도쿄, 영역 A (ap-northeast-1a)`로 되어 있는지 확인한다. 아니라면 `리전 및 영역 변경`을 통하여 설정한다.
- 인스턴스 이미즈는 `Linux/Unix` > `OS 전용` > `Ubuntu`로 지정한다.
- 다른 항목은 그대로 두고 아래쪽의 **생성** 버튼을 클릭한다.

다음과 같이 **대기중**으로 표시되었다가, **실행 중**으로 변경되면 인스턴스가 생성이 완료된 것이다.
![Instance Pending](/images/004-1.png)
![Instance Created](/images/004-2.png)

생성된 인스턴스의 우측 상단에 터미널(console) 모양의 아이콘을 클릭하면, 생성한 인스턴스에 접속(ssh)할 수 있다.

### 3.2. 주의사항

- 인스턴스를 생성한 순간부터 과금이 될 수 있다. 과금 유의사항을 항상 자세히 읽어보기를 권장하며, 사용하지 않을 인스턴스는 바로 삭제를 해주는 것이 좋다.
- 웹 console에서는 **한글 입력** 및 **붙여넣기**가 안된다. 명령어를 입력할 때 오타가 생기지 않도록 조심하자.


### 3.3. Auto Server Setup Script

#### 3.3.1. Auto Server Setup Script 가져오기

`git clone` 명령어로 Auto Server Setup Script를 가져온다. 지금 보고 있는 이 문서의 repository에 포함되어 있다.

```console
$ git clone https://github.com/zzulu/yay-you-are-on-aws.git
```

실행에 앞서 편의를 위하여 현재 폴더를 변경하자.

```console
$ cd ~/yay-you-are-on-aws
```

#### 3.3.1. rbenv.sh 실행

서버 설정을 매우 편리하게 해주기 위하여 script를 작성하였다. 아까 받은 Script를 실행하면 모든 설정이 자동으로 될 것이다. `sh` 명령어를 사용하여 실행해보자. 우선 `rbenv.sh` 먼저.

```console
$ sh ./scripts/rbenv.sh
```

#### 3.3.2. shell 새로고침

`rbenv.sh`에서 많은 것들을 설치하였는데, 그것들을 사용하기 위하여 shell을 refresh 해주자. 가끔 프로그램을 설치하고 컴퓨터를 재부팅 해주는 것과 같은 이유이다.

```console
$ exec $SHELL
```

#### 3.3.3. Ruby 설치

`rbenv.sh`를 통해 우리는 Rails 설치 및 실행에 필요한 모든 프로그램과 `rbenv`를 설치해주었다. Ruby의 경우 rbenv를 통해 직접 설치해주어야 한다.

```console
$ rbenv install 2.3.5
$ rbenv global 2.3.5
$ gem install bundler
$ rbenv rehash
```

#### 3.3.4. Rails 설치

마지막으로 rails까지 설치를 완료하자.

```console
$ gem install rails -v 4.2.9
```

#### 3.3.5. nginx.sh 실행하기

Web Server인 **Nginx**와 Application Server인 **Passenger**를 설치하는 script를 실행하자.

```console
$ sh ./scripts/nginx.sh
```


### 3.4. 프로젝트 가져오기

`1. 사용할 프로젝트 선택`에서 선택한 프로젝트를 Lightsail Instance로 가져온다. 여기서는 [classtak](https://github.com/classtak)의 fake_insta를 가져오지만 각자 개인의 프로젝트를 가져와도 된다.

```console
$ git clone https://github.com/classtak/fake_insta.git
``` 


### 3.5. Nginx, Passenger 설정하기

#### 3.5.1. Passenger 설정하기

```console
$ sudo vi /etc/nginx/passenger.conf
```

`passenger_ruby` 부분을 다음과 같이 수정한다.

```conf
passenger_ruby /home/ubuntu/.rbenv/shims/ruby;
```

#### 3.5.2 Nginx 설정하기
```console
$ sudo vi /etc/nginx/nginx.conf
```

아래의 내용을 찾아, `include`로 시작하는 줄을 주석 해제한다.

```conf
##
# Phusion Passenger
##
# Uncomment it if you installed ruby-passenger or ruby-passenger-enterprise
##

include /etc/nginx/passenger.conf;
```

파일을 하나 더 수정해야한다.

```console
$ sudo vi /etc/nginx/sites-enabled/default
```

파일의 내용을 다음과 같이 수정한다. `fake_insta`는 `3.4.`에서 가져온 프로젝트 이름으로 대체될 수 있다.

```conf
server {
        listen 80;
        listen [::]:80 ipv6only=on;

        server_name         example.com;
        passenger_enabled   on;
        rails_env           production;
        root                /home/ubuntu/fake_insta/public;

        # Add index.php to the list if you are using PHP
        # index index.html index.htm index.nginx-debian.html;

        ## Comment the following block
        # location / {
        #   # First attempt to serve request as file, then
        #   # as directory, then fall back to displaying a 404.
        #   try_files $uri $uri/ =404;
        # }

        # redirect server error pages to the static page /50x.html
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
}
```

설정 파일 편집이 완료되었으면 작성이 잘 되었는지 테스트하기 위하여 아래의 명령어를 입력한다.

```console
$ sudo nginx -t
```

만약 문제가 없다면, 아래의 명령어를 입력하여 Nginx를 실행하자.

```console
$ sudo service nginx start
```


### 3.6. 가져온 프로젝트 설정하기

가져온 프로젝트 폴더 안으로 이동한다.

```console
$ cd ~
$ cd fake_insta
```

gem 파일들을 설치한다.

```console
$ bundle install
```

`secrets.yml` 파일을 열어보면 production 부분에 secret_key_base가 설정되어 있지 않다. 노출되면 안되는 중요한 정보이기 때문에 Rails가 자동으로 생성하지 않아서 figaro를 이용하여 직접 설정해주어야 한다.

figaro를 설치한다.

```console
$ bundle exec figaro install
```

128자리 난수를 생성하여 `config` 폴더 안의 `application.yml` 파일에 붙인다.

```console
$ rake secret >> ./config/application.yml
```

`application.yml` 파일을 열어, 128자리 난수 앞에 아래의 코드를 작성한다.

```console
$ vi ./config/application.yml
```

```yaml
  SECRET_KEY_BASE: (방금 생성한 128자리 난수)
```

`production` 환경으로 Database를 생성한다.

```console
$ RAILS_ENV=production rake db:migrate
```

production 환경에서는 precompile된 assets들을 사용하기 때문에 precompile된 파일들을 따로 생성해주어야 한다. 아래의 명령어로 생성을 한다.

```console
$ RAILS_ENV=production rake assets:precompile
```

아래의 명령어로 프로젝트를 refresh 한다.

```console
$ touch tmp/restart.txt
```

Lightsail의 IP 주소를 브라우저 주소창에 입력하여 사이트에 접속이 되는지 확인한다.


## 4. IAM

IAM을 검색하거나 '보안, 자격 증명 및 규정 준수' 항목에서 IAM을 찾는다.

![Find IAM](/images/005.png)

### 4.1. 그룹 생성하기

새로운 그룹을 만든다.

![Create Group](/images/006.png)
![Define Group Name](/images/007.png)

2개의 정책을 찾아서 등록해준다.

![Attach Policy S3](/images/008-1.png)
![Attach Policy SES](/images/008-2.png)

그룹이 생성된 것을 확인할 수 있다.

![Created Group](/images/009.png)

### 4.2. 사용자 생성하기

새로운 사용자를 만든다.

![Create User](/images/010.png)

우리는 Access Key를 발급 받아야하기 때문에 **프로그래밍 방식 액세스**를 선택한다.

![Define User Name](/images/011.png)

그룹에 사용자를 추가한다.

![Add User To Group](/images/012.png)

발급된 Key를 확인한다. 반드시 **표시** 버튼을 눌러, 비밀 액세스 키를 확인하고 **액세스 키 ID**와 **비밀 액세스 키**를 안전한 곳에 복사해 놓는다. 현재 페이지를 벗어나면 비밀 액세스 키 확인이 불가능 하므로 꼭 저장한다.

![Save Access Key](/images/013.png)


### 4.3. 프로젝트에서 설정하기

우리는 방금 생성한 **액세스 키 ID**와 **비밀 액세스 키**가 외부에 노출되지 않도록 하기위하여 `figaro`를 사용한다. 이 2가지는 아이디와 패스워드 같은 개념이므로 **절대** 외부에 노출되어서는 안된다. 만약 노출되었다면 IAM에 접속을 하여 생성하였던 User를 삭제하여야 한다.

`config` 폴더의 `application.yml` 파일을 열어 IAM에서 발급받은 키를 입력한다.

```yaml
  AWS_ACCESS_KEY_ID: (각자 발급받은 키를 입력한다.)
  AWS_SECRET_ACCESS_KEY: (각자 발급받은 키를 입력한다.)
```


## 5. S3

[Amazon Simple Storage Service](https://aws.amazon.com/ko/s3/)

### 5.1. S3 Bucket 만들기

이미지가 올라갈 버킷을 만든다. 버킷의 이름을 지정하고, 리전을 **아시아 태평양(서울)**로 설정하고 버킷을 생성한다.

![Create Bucket](/images/014.png)


### 5.2. Gemfile

Gemfile에 fog를 추가한다.

```ruby
gem 'fog'
```

`bundle install` 명령어로 gem을 설치한다.

```console
$ bundle install
```


### 5.3. fog-aws.rb

`config/initializers` 폴더에 `fog-aws.rb`라는 이름의 파일을 만들어 준다. 내용은 다음과 같다.

```ruby
CarrierWave.configure do |config|
  config.fog_credentials = {
    provider:              'AWS',
    aws_access_key_id:     ENV["AWS_ACCESS_KEY_ID"],
    aws_secret_access_key: ENV["AWS_SECRET_ACCESS_KEY"],
    region:                'ap-northeast-2'
  }
  config.fog_directory  = '(앞서 설정한 bucket 이름)'
end
```


### 5.4. Uploader

`post_image_uploader.rb` 파일을 다음과 같이 수정한다.

```ruby
...

# Choose what kind of storage to use for this uploader:
  # storage :file
  storage :fog

...
```

S3에 이미지를 올리기 위한 모든 설정을 완료하였다. 새로운 게시글을 작성해보고 이미지가 S3 버킷에 잘 저장되는지 확인해보자.

### 5.5. Lightsail 적용하기

Lightsail console을 열어 프로젝트 폴더로 이동한다. 이동 후 git pull로 프로젝트의 변경사항을 가져오고, bundle install로 gem들을 설치한다.

```console
$ cd fake_insta
$ git pull
$ bundle install
```

`config` 폴더의 `application.yml` 파일을 열어 IAM에서 발급받은 키를 입력한다.

```console
$ vi config/application.yml
```

붙여넣기가 안되므로 틀리지않게 한글자씩 잘 입력한다.

```yaml
  AWS_ACCESS_KEY_ID: (각자 발급받은 키를 입력한다.)
  AWS_SECRET_ACCESS_KEY: (각자 발급받은 키를 입력한다.)
```

아래의 명령어로 프로젝트를 refresh 한다.

```console
touch tmp/restart.txt
```

Lightsail의 IP 주소를 브라우저 주소창에 입력하여 사이트에 접속 후, 여기서도 게시글 작성과 이미지 업로드가 잘 되는지 확인한다.



## 6. SES

gem [aws-sdk-rails](https://github.com/aws/aws-sdk-rails)

```ruby
gem 'aws-sdk-rails'
```

```console
$ bundle install
```

`config/initializers` 폴더 안에 `aws-sdk.rb` 파일을 생성하여 아래의 코드를 작성한다.

```ruby
# AWS credentials
Aws.config[:region] = 'us-west-2'
Aws.config[:credentials] = Aws::Credentials.new(ENV["AWS_ACCESS_KEY_ID"], ENV["AWS_SECRET_ACCESS_KEY"])
```

`config` 폴더 안의 `application.rb` 파일에 아래의 코드를 작성한다.

```ruby
# Mailer Option
config.action_mailer.delivery_method = :aws_sdk
```

`config/environments` 폴더 안의 `development.rb` 파일에 아래의 코드를 작성한다.

```ruby
# Mailer Option
config.action_mailer.default_url_options = { host: "http://localhost", port: 3000 }
```

`config/environments` 폴더 안의 `production.rb` 파일에 아래의 코드를 작성한다.

```ruby
# Devise Mailing Option
config.action_mailer.default_url_options = { host: "https://(Lightsail IP 주소)" }
```

# Yay! You're on AWS! 

## AWS 가입

[Amazon Web Services](https://aws.amazon.com/ko/)에 접속하여 회원 가입을 한다.
- 회원 가입을 할 때, 해외 결제가 되는 카드가 필요하다.
- 카드 등록 시 결제되는 1달러는 카드가 유효한지 검사하는 가결제이므로 곧 환불 처리 된다.


## 사용할 프로젝트 선택

- 본인의 GitHub에 프로젝트 Repository가 있어야 한다.
- 개인 프로젝트가 없는 사람은 [fake_insta](https://github.com/classtak/fake_insta)를 fork 해서 사용하자.


## Lightsail

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

#### 주의사항

- 인스턴스를 생성한 순간부터 과금이 될 수 있다. 과금 유의사항을 항상 자세히 읽어보기를 권장하며, 사용하지 않을 인스턴스는 바로 삭제를 해주는 것이 좋다.
- 웹 console에서는 **한글 입력** 및 **붙여넣기**가 안된다.


## IAM

IAM을 검색하거나 '보안, 자격 증명 및 규정 준수' 항목에서 IAM을 찾는다.

![Find IAM](/images/005.png)

### 그룹 생성하기

새로운 그룹을 만든다.

![Create Group](/images/006.png)
![Define Group Name](/images/007.png)

2개의 정책을 찾아서 등록해준다.

![Attach Policy S3](/images/008-1.png)
![Attach Policy SES](/images/008-2.png)

그룹이 생성된 것을 확인할 수 있다.

![Created Group](/images/009.png)

### 사용자 생성하기

새로운 사용자를 만든다.

![Create User](/images/010.png)

우리는 Access Key를 발급 받아야하기 때문에 **프로그래밍 방식 액세스**를 선택한다.

![Define User Name](/images/011.png)

그룹에 사용자를 추가한다.

![Add User To Group](/images/012.png)

발급된 Key를 확인한다. 반드시 **표시** 버튼을 눌러, 비밀 액세스 키를 확인하고 **액세스 키 ID**와 **비밀 액세스 키**를 안전한 곳에 복사해 놓는다. 현재 페이지를 벗어나면 비밀 액세스 키 확인이 불가능 하므로 꼭 저장한다.

![Save Access Key](/images/013.png)


### Figaro(중요!)

각종 중요한 정보들이 외부에 노출되지 않도록 관리해주는 gem이 [figaro](https://github.com/laserlemon/figaro)이다.

우리는 IAM에서 생성한 **ACCESS_KEY_ID**와 **SECRET_ACCESS_KEY**가 외부에 노출되지 않도록 하기위하여 사용한다. 이 2가지는 아이디와 패스워드 같은 개념이므로 **절대** 외부에 노출되어서는 안된다. 만약 노출되었다면 IAM에 접속을 하여 생성하였던 User를 삭제하여야 한다.

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

`config` 폴더의 `application.yml` 파일을 열어 IAM에서 발급받은 키를 작성한다.

```yaml
  AWS_ACCESS_KEY_ID: (각자 발급받은 키를 입력한다.)
  AWS_SECRET_ACCESS_KEY: (각자 발급받은 키를 입력한다.)
```

## S3

[Amazon Simple Storage Service](https://aws.amazon.com/ko/s3/)

### S3 Bucket 만들기

이미지가 올라갈 버킷을 만든다. 버킷의 이름을 지정하고, 리전을 **아시아 태평양(서울)**로 설정하고 버킷을 생성한다.

![Create Bucket](/images/014.png)


### Gemfile

Gemfile에 fog를 추가한다.

```ruby
gem 'fog'
```

`bundle install` 명령어로 gem을 설치한다.

```console
$ bundle install
```


### fog-aws.rb

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


### Uploader

`post_image_uploader.rb` 파일을 다음과 같이 수정한다.

```ruby
...

# Choose what kind of storage to use for this uploader:
  # storage :file
  storage :fog

...
```

S3에 이미지를 올리기 위한 모든 설정을 완료하였다. 새로운 게시글을 작성해보고 이미지가 S3 버킷에 잘 저장되는지 확인해보자.


## SES

gem [aws-sdk-rails](https://github.com/aws/aws-sdk-rails)


devise 비밀번호 찾기 메일 보내기


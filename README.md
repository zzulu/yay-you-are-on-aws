# Yay! You're on AWS! 

## AWS 가입

[Amazon Web Services](https://aws.amazon.com/ko/)에 접속하여 회원 가입을 한다.
- 회원 가입을 할 때, 해외 결제가 되는 카드가 필요하다.
- 카드 등록 시 결제되는 1달러는 카드가 유효한지 검사하는 가결제이므로 곧 환불 처리 된다.


## 사용할 프로젝트 선택

- 본인의 GitHub에 프로젝트 Repository가 있어야 한다.
- [fake_insta](https://github.com/classtak/fake_insta) fork 가즈아!


## Lightsail

Lightsail을 검색하거나 컴퓨팅 항목에서 Lightsail을 찾는다.

![Lightsail](/images/001.png)

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
- console에서 **한글 입력** 및 **붙여넣기**가 안된다.


## IAM

키 생성 (AmazonS3FullAccess, AmazonSESFullAccess)


## S3

(왜 s3를 쓰는지 이유 설명...)

### figaro

[figaro](https://github.com/laserlemon/figaro)

Gemfile에 figaro 추가한다.

```ruby
gem 'figaro'
```

figaro 설치

```console
$ bundle exec figaro install
```


이미지 업로드를 s3에 하기(fake insta를 이용할 예정임.)


## SES


devise 비밀번호 찾기 메일 보내기


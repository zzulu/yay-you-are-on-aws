# Yay! You're on AWS! 

## AWS 가입

[Amazon Web Services](https://aws.amazon.com/ko/)에 접속하여 회원 가입을 한다.
- 회원 가입을 할 때, 해외 결제가 되는 카드가 필요하다.
- 카드 등록 시 결제되는 1달러는 카드가 유효한지 검사하는 가결제이므로 곧 환불 처리 된다.


## 사용할 프로젝트 선택

- 본인의 GitHub에 프로젝트 Repository가 있어야 한다.
- [fake_insta](https://github.com/classtak/fake_insta) fork 가즈아!


## Lightsail


![Lightsail](/images/001.png)


![Create Instance](/images/002.png)


![Instance Configure](/images/003.png)


그냥 클릭 몇번으로 인스턴스 생성 할 수 있음... 신기...
선택 해야할 것들 이미지만 가져와서 이거이거 선택하세요. 하면 될 듯.

console에서 한글 입력이 안됨.
붙여넣기가 안됨. 직접 입력해야함. 고로 sh 파일을 만들어서 배포하는 게 좋아보임.

근데 그러면 AWS 키를 어떻게 붙여넣지?

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


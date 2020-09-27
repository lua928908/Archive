# NODE boilerplate 공부하기 
## openssl

openssl은 ssl 프로토콜의 오픈소스 이다, ssl인증서를 발급받을 때 필요하다.

```
    $ openssl genrsa -out private.pem 2048
    $ openssl rsa -in private.pem -pubout -outform PEM -out public.pem
```

- `genrsa` - RSA 2048 키 생성
- `openssl genrsa -aes256 -passout pass:asdfasdf -out aes-pri.pem 2048` - RSA 2048 키 생성 및 개인키를 AES256 으로  암호화
- ` -output` -
- `verify` : 전자서명 검증 수행
- `in` : 검증할 전자서명 데이타 파일
- `certfile` : 검증에 사용할 인증서 파일(전자 서명 데이타내에 인증서가 없을 경우 필요 - 생성시 -nocerts 으로 생성했을 경우)
- `out` : 검증후 원본을 저장할 파일명
- `content` : 검증에 사용할 원본 파일
- `CAfile` : 인증서 발급 체인(CA 인증서 묶음. PEM 형식으로 연접해서 작성해 주면 되며 예제는 curl 에 포함된 ca인증서 번들 파일 참고 - /etc/pki/tls/certs/ca-bundle.crt) 
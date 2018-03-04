---
layout: post
title: 구글엔진으로 장고 배포하기 - 6. SSL 인증서 발급 <2편> kube-lego 이해하기
categories: [Google News Lab]
tags: [Google News Lab, django, Let's Encrypt, SSL]
description: 
---

사이트를 배포하고 나서 반드시 해주어야 하는 작업이 있다. 바로 웹사이트 도메인에 `SSL` 인증서를 발급받는 일이다. 특히 크롬/사파리 브라우저는 앞으로 인증서가 없는 웹사이트에 접속 시 경고창을 띄울 것이라고 공지한 만큼 **SSL인증서 발급은 웹사이트 배포시 필수**적인 작업이다.

<br>

## SSL/TLS 인증서란?
 
`SSL` 인증서는 신뢰할 수 있는 인증기관에서 사이트의 보안을 보증해준다. 사용자가 웹사이트에 접속하면 여러 형태의 인증 기능(회원가입, 로그인, 로그아웃, 결제 등)을 사용하게 되는데 이 때 여러 중요정보를 웹사이트의 서버로 전송할 수 밖에 없다. 

이러한 개인정보는 `패킷` 형태로 전송하는데, 인증서가 없는 사이트는 중요정보가 담긴 패킷을 제 3자가 언제라도 낚아채 가져갈 위험이 있다. 예전에는 인증 기능이 포함된 페이지 경로에만 부분적으로 `SSL`을 적용해도 괜찮았지만 요즘에는 웹사이트 전체에 `SSL`을 적용하도록 장려하고 있다. 

보통의 `SSL`은 유료로 구매하는 인증서지만, **개인이 직접 생성한 키페어로 인증서를 발급**해주는 <a href="#" target="_blank">Let's Encrypt</a> 오픈소스 서비스를 사용하려고 한다. 해당 서비스는 3개월의 인증서 유효기간을 제공하고 있다. 만약 직접 `Let's Encrypt`에서 인증서를 발급받아 사용하는 경우에는 유효기간을 넘지 않도록 조심해야한다. 인증서의 유효기간이 다가오면 사이트에 접속할 때 경고창이 뜨는 등 웹 접근성에 반하는 서비스를 할 우려가 있기 때문이다. 


<br />

## kube-lego 라이브러리 사용하기

`kube-lego`는 Kubernetes 엔진으로 배포한 웹앱에 간단한 설정(?...튜토리얼에 의하면)으로 도메인을 할당할 수 있다. 또한, `Let's Encrypt`로 직접 발급받은 인증서를 유효기간 걱정없이 갱신해준다. 


지난 포스트 시리즈까지는 구글 클라우드 플랫폼(Google Cloud Platform) 내 쿠버네트 엔진(GKE, Google Kubernetes Engine)을 사용하여 도커이미지로 만든 장고 앱을 배포하는 방법에 대해 소개했다.
이번 포스트에서는 Kubernete 엔진에 적용가능한 `kube-lego`를 사용하여 도메인을 할당하고 인증서를 발급하여 사이트 접속시 `https`로 리다이렉트하는 방법을 소개하겠다. 

<br />

## 준비물

- 인증서 발급용 `.key`, `.crt` 파일
	- `openssl`로 생성하여 안전한 곳에 저장해둔다. 
	
	```
	$  openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout /path/to/tls.key -out /path/to/tls.crt -subj "/CN=DNS 이름"
	``` 

	**주의**
		
	- 위의 파일은 반드시! 반드시...!! 안전한 곳에 저장해둔다.
	- 키를 생성할 때 `.srt`와 `.key`파일의 이름은 반드시 `tls`로 통일해준다. 그렇지 않으면 못찾더라 ㅠㅠ...

- 빌링이 허용된 계정으로 쿠버네트 엔진에 배포 중인 장고 앱 (<a href="http://juliahwang.kr/google%20news%20lab/2018/01/29/%EA%B5%AC%EA%B8%80%EC%97%94%EC%A7%84%EC%9C%BC%EB%A1%9C%EC%9E%A5%EA%B3%A0%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B01-%EB%B0%B0%ED%8F%AC%EC%A0%84%EC%A4%80%EB%B9%84.html" target="_blank">쿠버네트엔진 배포에 대한 이전 포스팅</a> 참고) 
- 앱에 할당할 수 있는, 직접 소유하고 있는 도메인


`kube-lego`는 기본적으로 `Ingress` 설정과 연동하여 사용하는 라이브러리다. `Ingress`를 사용한 배포방식을 이해하지 못했다면 <a href="http://juliahwang.kr/google%20news%20lab/2018/02/13/%EA%B5%AC%EA%B8%80%EC%97%94%EC%A7%84%EC%9C%BC%EB%A1%9C%EC%9E%A5%EA%B3%A0%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B06-SSL%EC%9D%B8%EC%A6%9D%EC%84%9C%EB%B0%9C%EA%B8%891%EB%B0%B0%ED%8F%AC%EB%B0%A9%EC%8B%9D%EC%97%90%EB%8C%80%ED%95%9C%EA%B3%A0%EC%B0%B0.html" target="_blank">이전 글</a>을 참고하자.
 
위의 준비물이 준비됐다면 다음으로 넘어가자. 

<br />

## kube-lego 작동원리 이해하기

`kube-lego`는 일종의 지름길이다. SSL 인증서는 신뢰할 수 있는 인증기관에서 유료로 발급받아 유효기간이 다가오기 전에 미리 매번 갱신해 주어야하는 번거로움을 디폴트로 내장하고 있다. 하지만 앞에서도 설명했듯이 인증서 발급은 필수적인 작업이므로 개발자들은 직접 시크릿파일을 만들어 수동으로 SSL 인증서를 적용시켜 왔는데, **`kube-lego`는 이러한 일련의 과정을 설정파일 몇 개로 단순화시켜 만들어놓은 라이브러리**다.

배포를 하면서 깨닫게 된 사실인데, `namespace`는 매우 중요하다. 이왕이면 **지금까지 모든 설정파일(`Ingress`설정파일부터~)의 `namespace`를 통일해주는 것이 좋은데, 쿠버네트 엔진같이 클러스터 내에 여러 포드를 생성하는 경우 명시해주지 않으면 파일이 엉뚱한 곳에 설정되는 경우도 있기 때문**이다. 

```yaml
# kube-lego.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: kube-lego
  namespace: customize this! # ConfigMap, Deployment와 통일해준다.
```

<br>

kube-lego는 스스로 `Let's Encrypt`에 계정을 생성해 인증서를 발급받아온다. 다음은 그에 해당하는 설정파일이다. 

```yaml
apiVersion: v1
metadata:
  name: kube-lego
  namespace: customize this!
data:
  # modify this to specify your address
  lego.email: # "Let's Encrypt 계정으로 사용할 이메일주소"
  # configure letsencrypt's production api
  lego.url: "https://acme-v01.api.letsencrypt.org/directory"
kind: ConfigMap

```

<br />

다음은 `kube-lego` 설정파일이다. 


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: kube-lego
  namespace: customize this!
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: kube-lego
    spec:
      containers:
      - name: kube-lego
        image: jetstack/kube-lego:0.1.5
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
        env:
        - name: LEGO_EMAIL
          valueFrom:
            configMapKeyRef:
              name: kube-lego
              key: lego.email
        - name: LEGO_URL
          valueFrom:
            configMapKeyRef:
              name: kube-lego
              key: lego.url
        - name: LEGO_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: LEGO_POD_IP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        readinessProbe:
          httpGet:
            path: /healthz
            port: 8080
          initialDelaySeconds: 5
          timeoutSeconds: 1
```

<br>

`kube-lego` 설정이 끝나면 다음 명령어를 통해 지금까지의 모든 설정파일들을 적용시킨다. 

```powershell
$ kubectl apply -f "yaml 파일명"
```

<br>

실제 pod가 동작하는 것을 보고싶다면 namespace 로 통일된 설정파일로 생성한 POD의 log를 찍어볼 수 있다.

```powershell
$ kubectl get pods -n "설정한 namespace명"
```

<br>


```powershell
$ kubectl logs -f "kube-lego-0000" -n "설정한 namespace명" 

time="2018-02-10T17:44:12Z" level=info msg="requesting certificate for [설정 도메인]" context="ingress_tls" name=myingress namespace=default 
time="2018-02-10T17:44:20Z" level=info msg="authorization successful" context=acme domain=[설정 도메인] 
time="2018-02-10T17:44:21Z" level=info msg="successfully got certificate: domains=[설정 도메인] url=[let's encrypt acme 주소]" context=acme 
time="2018-02-10T17:44:21Z" level=info msg="Secret successfully stored" context=secret name=[설정해준 시크릿 이름] namespace=default 
time="2018-02-11T01:34:36Z" level=info msg="Periodically check certificates at 2018-02-11 01:34:36.919178554 +0000 UTC" context=kubelego  
time="2018-02-11T01:34:36Z" level=info msg="process certificate requests for ingresses" context=kubelego 
time="2018-02-11T01:34:36Z" level=info msg="cert expires in 89.6 days, no renewal needed" context="" expire_time=2018-05-11 16:44:20 +0000 UTC name=[인그레스 이름] namespace=default 
time="2018-02-11T01:34:36Z" level=info msg="no cert request needed" context="[인그레스 tls 이름]" name=[인그레스 이름] namespace=default 
time="2018-02-11T09:34:36Z" level=info msg="Periodically check certificates at 2018-02-11 09:34:36.919211522 +0000 UTC" context=kubelego 
```

위의 로그는 생성해놓은 시크릿 키파일로 `kube-lego`가 `Let's Encrypt`에 인증을 요청해서 인증서를 발급받아오는 과정을 보여준다. 이전 포스트에서 `Ingress`로 배포를 했다면 인증서 발급은 이렇게 간단하게 끝난다.


콘솔을 확인해보면 `kube-lego`용 설정파일에서 지정한 이름으로 url이 생성되는데, 다음 패턴을 따른다. 

```
ingress에 등록한 도메인주소/well-known/acme-challenge
```

위의 도메인으로 접속해보면 이전 포스트에서 설치해준 `http-default-backend`에 의해 404 페이지가 뜨는 것을 확인할 수 있다.

<br>

### `주의`
인증서 발급에 시간이 걸리기 때문에 위의 로그가 찍혔더라도 브라우저로 도메인을 확인해보면 여전히 `Not Secure`가 뜰 가능성이 있다. 내 경우에는 10분 정도 기다리다가 창을 새로고침해보니 적용이 되었었다. 

<br>

--- 

## 마치며 

구글 클라우드 플랫폼에서 쿠버네트 엔진을 쓰고, 외부 호스팅 서비스(예를 들면 Cloudflare)를 쓰지 않을 경우 `kube-lego`를 사용하자. 막상 적용할 때는 정말 어려웠지만 포스팅하면서 정리해보니 `kube-lego`가 굉장히 간편한 툴이라는 것을 깨달았다...

<br>

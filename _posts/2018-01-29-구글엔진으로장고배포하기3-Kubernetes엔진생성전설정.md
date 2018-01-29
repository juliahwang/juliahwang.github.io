---
layout: post
title: 구글엔진으로 장고 배포하기 - 3. Kubernetes 엔진 생성 전 설정
categories: [Google News Lab]
tags: [Google News Lab, django]
description: 
---

이번 포스팅에서는 서비스 계정을 생성하여 Kubernetes 엔진 환경을 구성하는 방식에 대해 설명할 예정이다. 

## 1. 서비스 계정 생성

서비스 계정은 IAM 내에서 제공중이다. [해당 콘솔페이지](https://console.cloud.google.com/projectselector/iam-admin/serviceaccounts)에서 콘솔 탭메뉴의 `IAM`을 선택한 후 `서비스 계정` 탭을 눌러 알맞은 프로젝트 내에 계정을 생성하면 된다. 

이 때, 역할에서 `Cloud SQL` 내의 `Cloud SQL Client`를 부여해준다. 이 서비스 계정 ID는 반드시 유일할 필요는 없지만 알아보기 쉬운 프로젝트 관련 이름으로 정해주는 것이 좋다.

계정 생성이 완료되면 `json`으로 선택한 프라이빗 키 파일이 다운받아진다. 이 키는 서비스계정을인스턴스에 연결할 때, 엔진 생성할 때 필요하므로 안전한 곳에 보관한다.

<br>

## 2. Kubernetes(쿠버네트) 엔진 환경설정 생성

이전 포스팅에서 구글 엔진용 배포 환경설정 파일로  `app.yaml`을 생성해주어야 한다고 언급했다. 

이제 이 파일을 생성해보자. 이 때도 `connectionName`이 필요하다. 이 부분을 생략했다면 이전 포스팅을 참고하면 된다. 

```powershell
# app.yaml

apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: test-app
  labels:
    app: test
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: test
    spec:
      containers:
      - name: test-app
        # Replace  with your project ID or use `make template`
        image: gcr.io/<프로젝트명>/test
        # This setting makes nodes pull the docker image every time before
        # starting the pod. This is useful when debugging, but should be turned
        # off in production.
        imagePullPolicy: Always
        env:
            - name: DATABASE_USER
              valueFrom:
                secretKeyRef:
                  name: cloudsql
                  key: username
            - name: DATABASE_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: cloudsql
                  key: password
        ports:
        - containerPort: 8080

      - image: b.gcr.io/cloudsql-docker/gce-proxy:1.05
        name: cloudsql-proxy
        command: ["/cloud_sql_proxy", "--dir=/cloudsql",
                  "-instances=<connectionName을 써줘야함>=tcp:5432",
                  "-credential_file=/secrets/cloudsql/credentials.json"]
        volumeMounts:
          - name: cloudsql-oauth-credentials
            mountPath: /secrets/cloudsql
            readOnly: true
          - name: ssl-certs
            mountPath: /etc/ssl/certs
          - name: cloudsql
            mountPath: /cloudsql
      volumes:
        - name: cloudsql-oauth-credentials
          secret:
            secretName: cloudsql-oauth-credentials
        - name: ssl-certs
          hostPath:
            path: /etc/ssl/certs
        - name: cloudsql
          emptyDir:
```

위의 설정은 아직 **배포 서비스에 대한 환경 설정은 포함하지 않은 엔진 자체에 대한 정보**를 담고 있다. 예를 들면 데이터베이스 설정, 장고 프로젝트를 담을 도커 이미지 설정, 그리고 볼륨에 대한 설정 등으로 구성되어 있다.

<br>

## 3. 장고 마이그레이션 및 어드민 생성

장고 프로젝트 생성시 만든 앱을 마이그레이션 해준다. (데이터베이스를 생성하는 작업이다.)

```powershell
./manage.py makemigrations
./manage.py migrate
``` 

로컬 웹서버를 돌려서 페이지가 정상작동하는지 확인해준다. 

```
$ ./manage.py runserver 
```

<br>

## 4. 마치며 

다음 포스팅에서는 정적 파일을 저장해줄 버킷을 생성하고 연동하는 방법에 대해서 알아볼 예정이다. 



<br>

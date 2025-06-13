# Prometheus & Grafana 기반 모니터링 Lab

## 🚦 목차

1. [📒 개요](#overview)
   - 1-1. [프로젝트 목표 🎯](#project-goals)
2. [🤝 팀원](#contributor)   
3. [📖 프로젝트 설명](#project-description)
   - 3-1. [시스템 구성 및 아키텍처](#architecture)
   - 3-2. [메트릭 노출 방식 비교](#metrics-comparison)
     - 3-2-1. [Exporter 방식 (Node, MySQL, Nginx Exporter)](#exporter-metrics)
     - 3-2-2. [내부 노출 방식 (Spring Boot Actuator + Micrometer)](#internal-metrics)
   - 3-3. [워크플로우 및 연동](#workflow)
4. [🔎 사전 요구사항](#prerequisites)
5. [🚀 활용 기술](#technologies)
6. [🏗️ 시스템 구축 및 실행](#implementation)
   - 6-1. [기본 모니터링 시스템 구축](#basic-monitoring)
     - 6-1-1. [Prometheus 설치 (바이너리 방식)](#prometheus-installation)
     - 6-1-2. [Grafana 설치 (APT 패키지 방식)](#grafana-installation)
     - 6-1-3. [Prometheus와 Grafana 연동](#prometheus-grafana-integration)
   - 6-2. [Exporter 기반 모니터링 구축](#exporter-setup)
     - 6-2-1. [Node Exporter 설치 및 구성](#node-exporter)
     - 6-2-2. [MySQL Exporter 설치 및 구성](#mysql-exporter)
     - 6-2-3. [Nginx Exporter 설치 및 구성](#nginx-exporter)
   - 6-3. [Spring Boot 애플리케이션 모니터링 구축 (내부 메트릭 노출)](#spring-boot-monitoring)
7. [📊 모니터링 및 부하 테스트](#monitoring)
   - 7-1. [MySQL 부하 테스트 및 모니터링](#mysql-load-testing)
   - 7-2. [Nginx 부하 테스트 및 모니터링](#nginx-load-testing)
   - 7-3. [Spring Boot 애플리케이션 부하 테스트 및 모니터링](#spring-boot-load-testing)
8. [🔫 트러블슈팅](#troubleshooting)
9. [💡 고찰 및 개선점](#improvements)

---

## <a id="overview"></a>1. 📒 개요

### <a id="project-goals"></a>1-1. 프로젝트 목표 🎯

- Prometheus와 Grafana를 활용한 모니터링 시스템 구축 능력 향상

- 다양한 Exporter(Node, MySQL, Spring Boot)를 통해 메트릭 수집 경험 확보

- 실제 부하 발생 상황에서의 시스템 상태 파악 및 대시보드 구성 능력 강화

---

## <a id="contributor"></a>2. 🤝 팀원
<table>
  <tbody>
    <tr>
      <td align="center">
          <a href="https://github.com/kcklkb">
          <img src="https://avatars.githubusercontent.com/u/40711682?v=4" width="150px;" alt=""/>
          <br /><sub><b> 김창규 </b></sub>
        </a>
        <br />
      </td>
      <td align="center">
        <a href="https://github.com/parkjhhh">
          <img src="https://avatars.githubusercontent.com/u/153366521?v=4" width="150px;" alt=""/>
          <br /><sub><b> 박지혜 </b></sub>
        </a>
        <br />
      </td>
      <td align="center">
        <a href="https://github.com/PleaseErwin">
          <img src="https://avatars.githubusercontent.com/u/79669001?v=4" width="150px;" alt=""/>
          <br /><sub><b> 서소원 </b></sub>
        </a>
        <br />
      </td>
      <td align="center">
         <a href="https://github.com/danidana2">
          <img src="https://avatars.githubusercontent.com/u/150885774?v=4" width="150px;" alt=""/>
          <br /><sub><b> 최다영 </b></sub>
        </a>
        <br />
      </td>
    </tr>
  </tbody>
</table>

---

## <a id="project-description"></a>3. 📖 프로젝트 설명

### <a id="architecture"></a>3-1. 시스템 구성 및 아키텍처

### <a id="metrics-comparison"></a>3-2. 메트릭 노출 방식 비교

#### <a id="exporter-metrics"></a>3-2-1. Exporter 방식 (Node, MySQL, Nginx Exporter)

#### <a id="internal-metrics"></a>3-2-2. 내부 노출 방식 (Spring Boot Actuator + Micrometer)

### <a id="workflow"></a>3-3. 워크플로우 및 연동

---

## <a id="prerequisites"></a>4. 🔎 사전 요구사항

---

## <a id="technologies"></a>5. 🚀 활용 기술

---

## <a id="implementation"></a>6. 🏗️ 시스템 구축 및 실행

### <a id="basic-monitoring"></a>6-1. 기본 모니터링 시스템 구축

#### <a id="prometheus-installation"></a>6-1-1. Prometheus 설치 (바이너리 방식)
&emsp;**1) 설치 방식 선택 배경**  
Prometheus는 다양한 설치 방식(바이너리, 패키지 매니저, Docker 등)을 지원하지만, 최신 버전 유지와 설정 유연성을 고려하여 바이너리 설치 방식을 선택 

&emsp;**2) 설치 절차**
 1. Prometheus 전용 사용자 계정 생성
    ```bash
    sudo useradd --no-create-home --shell /bin/false prometheus
    ```  

 2. 디렉터리 생성 및 권한 설정
    ```bash
    sudo mkdir /etc/prometheus
    sudo mkdir /var/lib/prometheus
    sudo chown prometheus:prometheus /var/lib/prometheus
    ```
    ![alt text](<images/6-1-1.-2)-2.prometheus 설치.png>)

 3. 최신 바이너리 다운로드 및 압축 해제
    ```bash
    cd /tmp

    # GitHub Releases에서 최신 URL 확인 후 다운로드
    curl -LO https://github.com/prometheus/prometheus/releases/download/v3.2.1/prometheus-3.2.1.linux-amd64.tar.gz

    # 압축 해제
    tar xvf prometheus-3.2.1.linux-amd64.tar.gz
    ```
    ![alt text](<images/6-1-1.-2)-3.prometheus 설치.png>) 

 4. 실행 파일 및 설정 파일 이동
    ```bash
    cd prometheus-3.2.1.linux-amd64/

    # 실행 파일 이동
    sudo mv prometheus promtool /usr/local/bin/

    # 설정 파일 이동
    sudo mv prometheus.yml /etc/prometheus/
    ```
    ![alt text](<images/6-1-1.-2)-4.prometheus 설치.png>)

 5. Prometheus 설정 파일 확인
    ```bash
    # Prometheus 서버가 정상적으로 실행되고 있는지 localhost 포트로 확인
    curl localhost:9090

    # Prometheus 설정 파일 내용 확인
    sudo cat /etc/prometheus/prometheus.yml
    ```
    ![alt text](<images/6-1-1.-2)-5.prometheus 설정 파일 확인.png>)

&emsp;**3) Prometheus 서비스 등록**  
 1. prometheus.service 유닛 파일 생성 및 내용 작성
    ```bash
    # 파일 존재 여부 확인
    ls /etc/systemd/system

    # prometheus.service 유닛 파일 생성 및 내용 작성
    sudo tee /etc/systemd/system/prometheus.service <<EOF
    [Unit]
    Description=Prometheus
    Wants=network-online.target
    After=network-online.target

    [Service]
    User=prometheus
    Group=prometheus
    Type=simple
    ExecStart=/usr/local/bin/prometheus \\
      --config.file=/etc/prometheus/prometheus.yml \\
      --storage.tsdb.path=/var/lib/prometheus \\
      --web.listen-address=0.0.0.0:9090

    [Install]
    WantedBy=multi-user.target
    EOF
    ```
    ![alt text](<images/6-1-1.-3)-1.prometheus 서비스로 등록.png>)

 2. Prometheus 서비스 등록 및 실행 
    ```bash
    # systemd 데몬 재시작 (유닛 파일 반영)
    sudo systemctl daemon-reload

    # Prometheus 서비스 자동 시작 등록 및 시작
    sudo systemctl enable --now prometheus

    # 서비스 상태 확인
    systemctl status prometheus
    ```
    ![alt text](<images/6-1-1.-3)-2.prometheus 서비스로 등록.png>)


&emsp;**4) Prometheus HTTP 서버 확인** <br>
Prometheus 서비스가 성공적으로 실행되었는지 확인
 1. 기본 포트 9090 접근 확인 (curl)
    ```bash
    # 기본 포트 9090에 접근하여 응답이 오는지 확인
    curl http://localhost:9090
    ```
    ![alt text](<images/6-1-1.-4)-1.prometheus http 서버 확인.png>)

 2. VM NAT 포트 포워딩 설정
    ![alt text](<images/6-1-1.-4)-2.prometheus http 서버 확인.png>)

 3. 브라우저 접속 확인
    ![alt text](<images/6-1-1.-4)-3.prometheus http 서버 확인.png>)
    아직 메트릭을 수집할 타겟(exporter) 이 등록되지 않았기 때문에 위와 같은 화면이 나옴

#### <a id="grafana-installation"></a>6-1-2. Grafana 설치 (APT 패키지 방식)

#### <a id="prometheus-grafana-integration"></a>6-1-3. Prometheus와 Grafana 연동

---

### <a id="exporter-setup"></a>6-2. Exporter 기반 모니터링 구축

#### <a id="node-exporter"></a>6-2-1. Node Exporter 설치 및 구성
&emsp;**1) Node Exporter 설치 (바이너리 방식)**  
&emsp;**2) Node Exporter 서비스 등록**  
&emsp;**3) Prometheus 설정**  
&emsp;**4) 확인**

#### <a id="mysql-exporter"></a>6-2-2. MySQL Exporter 설치 및 구성
&emsp;**1) MySQL 설치**  
- 설치 후 계정 및 DB 생성

![alt text](<images/6-2-2-1)MySQL 설치.png>)

&emsp;**2) MySQL Exporter 설치 (바이너리 방식)**  

- 바이너리 설치

![alt text](<images/6-2-2-2)MySQL Exporter 설치 (바이너리 방식)_1.png>)

- 설치 완료

![alt text](<images/6-2-2-2)MySQL Exporter 설치 (바이너리 방식)_2.png>)

&emsp;**3) MySQL Exporter 설정**  

- cnf 설정파일 생성

![alt text](<images/6-2-2-3)MySQL Exporter 설정.png>)

&emsp;**4) MySQL Exporter 서비스 등록**  

&emsp;&emsp;**4-1) 서비스 등록 안 했을 경우 문제점** 

- 서비스 미등록 시 재부팅 했을 경우 재실행해야함

![alt text](<images/6-2-2-4-1)서비스 등록 안 했을 경우 문제점.png>)

&emsp;&emsp;**4-2) 서비스 등록**  

- 서비스 등록하여 재부팅 시 자동 실행 될 수 있도록 함

![alt text](<images/6-2-2-4-2)서비스 등록.png>)

&emsp;**5) Prometheus 설정**  

- Prometheus.yaml 파일 내 mysql-exporter job 추가

![alt text](<images/6-2-2-5)Prometheus 설정.png>)

&emsp;**6) 확인**

- Prometheus job 추가 확인

![alt text](<images/6-2-2-6)확인_1.png>)

- grafana 시각화 연결 확인

![alt text](<images/6-2-2-6)확인_2.png>)

---

#### <a id="nginx-exporter"></a>6-2-3. Nginx Exporter 설치 및 구성

&emsp;**1) Nginx 설치**  

```
sudo apt install nginx
```

<br>

&emsp;**2) Nginx Stub Status 설정**  

```
# /etc/nginx/sites-enabled/default

server {
        location /stub_status {
                stub_status;
                allow 127.0.0.1;
                deny all;
        }
}
```
**stub_status** 모듈을 사용해 Nginx의 내부 통계 정보를 보여주는 페이지 활성화

<br>

&emsp;**3) Nginx Exporter 설치 (바이너리 방식)**  

```
wget https://github.com/nginx/nginx-prometheus-exporter/releases/download/v1.4.1/nginx-prometheus-exporter_1.4.1_linux_amd64.tar.gz
tar -xvzf nginx-prometheus-exporter_1.4.1_linux_amd64.tar.gz
```
파일 다운로드 & 압축 해제

<br>

```
sudo mv nginx-prometheus-exporter /usr/local/bin/
sudo chmod +x /usr/local/bin/nginx-prometheus-exporter
```
디렉토리로 이동 & 실행 권한 부여

<br>

&emsp;**4) Nginx Exporter 서비스 등록**  

```
# /etc/systemd/system/nginx_exporter.service

[Unit]
Description=Nginx Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/nginx-prometheus-exporter --nginx.scrape-uri=http://localhost:80/stub_status

[Install]
WantedBy=multi-user.target
```

```
# 서비스 시작 및 확인

sudo systemctl daemon-reload
sudo systemctl enable --now nginx_exporter
systemctl status nginx_exporter
```

<br>

&emsp;**5) Prometheus 설정**  

```
# /etc/prometheus/prometheus.yml

- job_name: 'nginx-exporter'
    static_configs:
      - targets: ['localhost:9113']
```

```
# 서비스 시작 및 확인

sudo systemctl restart prometheus
sudo systemctl status prometheus
```

<br>

&emsp;**6) 확인**

- Prometheus에서 nginx exporter job 확인

![alt text](<images/6-2-3-6-1)nginx_확인_1.png>)

- grafana 시각화 연결 확인

![alt text](<images/6-2-3-6-2)nginx_확인_2.png>)

<br>

---

### <a id="spring-boot-monitoring"></a>6-3. Spring Boot 애플리케이션 모니터링 구축 (내부 메트릭 노출)

&emsp;**1) Spring Boot Actuator 및 Micrometer 설정**  

```
spring:
  application:
    name: step01_basic

server:
  port: 81

management:
  endpoints:
    web:
      exposure:
        include: prometheus

  endpoint:
    prometheus:
      access: unrestricted

  prometheus:
    metrics:
      export:
        enabled: true
```
➡️ application.yaml을 통해 /actuator/prometheus 경로를 외부에 노출
```
implementation 'org.springframework.boot:spring-boot-starter-actuator'
implementation 'io.micrometer:micrometer-registry-prometheus'
```
➡️ build.gradle을 통해 /actuator/prometheus 경로를 외부에 노출

&emsp;**2) Spring Boot App 서비스 등록**  
<br>
![alt text](<images/6-3-2) Spring Boot App 서비스 등록.png>)
➡️ Gradle로 .jar 파일 빌드 후 Ubuntu 서버에 업로드(경로:/home/ubuntu/springapp.jar)
```
sudo tee /etc/systemd/system/springapp.service <<EOF
[Unit]
Description=Spring Boot Application
After=network.target

[Service]
User=ubuntu
ExecStart=/usr/bin/java -jar /home/ubuntu/springapp.jar
SuccessExitStatus=143
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reexec
sudo systemctl enable springapp
sudo systemctl start springapp
```
➡️ 서버 재부팅 시 자동 실행되도록 systemd 등록
&emsp;**3) Prometheus 설정**  
```
scrape_configs:
  - job_name: 'spring-app'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['localhost:81']
```
➡️ /etc/prometheus/prometheus.yml에 spring-app job 추가

<br>

&emsp;**4) 확인**
![alt text](<images/6-3-4) 확인.png>)

➡️ 서비스 등록 후 서버를 껐다가 다시 켜도 정상적으로 Prometheous에서 up상태인것을 확인할 수 있음



![<그라파나 사진>](<images/6-3-4)확인2.png>)

➡️ Grafana로 시각화(Dashboard ID:4701)

## <a id="monitoring"></a>7. 📊 모니터링 및 부하 테스트


### <a id="mysql-load-testing"></a>7-1. MySQL 부하 테스트 및 모니터링
&emsp;**1) MySQL 부하 테스트 준비**  

- mysql 부하테스트 라이브러리 sysbench 설치

![alt text](<images/7-1-1)MySQL 부하 테스트 준비_1.png>)

- 테스트용 DB 및 계정 생성

![alt text](<images/7-1-1)MySQL 부하 테스트 준비_2.png>)
![alt text](<images/7-1-1)MySQL 부하 테스트 준비_3.png>)

&emsp;**2) MySQL 부하 테스트 및 모니터링**

- sysbench oltp_read_write --table-size=100000 --threads=10 --time=60  
(10개의 스레드로 60초 동안 sbtest DB에 10만 건의 데이터 읽기/쓰기 부하 테스트)

![alt text](<images/7-1-2)MySQL 부하 테스트 및 모니터링_1.png>)

- 모니터링 확인

![alt text](<images/7-1-2)MySQL 부하 테스트 및 모니터링_2.png>)
![alt text](<images/7-1-2)MySQL 부하 테스트 및 모니터링_3.png>)

### <a id="nginx-load-testing"></a>7-2. Nginx 부하 테스트 및 모니터링

&emsp;**1) Nginx 부하 테스트 준비**  

```
sudo apt install apache2-utils
```
HTTP 서버의 성능을 측정하는 스트레스 테스트 도구인 Apache Bench 설치

<br>

&emsp;**2) Nginx 부하 테스트 및 모니터링**

```
ab -n 10000 -c 50 http://localhost:80/
```
50명의 사용자가 각각 10000번씩 요청

<br>

![alt text](<images/7-2-2-2)nginx_모니터링_2.png>)

**모니터링 결과**

![alt text](<images/7-2-2-1)nginx_모니터링_1.png>)

<br>

### <a id="spring-boot-load-testing"></a>7-3. Spring Boot 애플리케이션 부하 테스트 및 모니터링
&emsp;**1) Spring Boot App 부하 테스트 준비**
```
ab -n 1000 -c 50 http://localhost:8080/test1
```
➡️ 스트레스 주기 : 1000번 요청, 50개 동시 연결

&emsp;**2) Spring Boot App 부하 테스트 및 모니터링**

![alt text](<images/7-3-2) Spring Boot 애플리케이션 부하 테스트 및 모니터링.png>)

![alt text](<images/7-3-2) Spring Boot 애플리케이션 부하 테스트 및 모니터링2.png>)

➡️ 🎉스트레스 테스트 성공!

---

## <a id="troubleshooting"></a>8. 🔫 트러블슈팅
- MySQL Exporter 실행 시 cnf 파일 권한 문제 발생 -> 권한 설정 변경하여 실행 필수

![alt text](<images/8. 트러블슈팅_1.png>)

---

## <a id="improvements"></a>9. 💡 고찰 및 개선점

---

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
4. [📚 주요 개념](#concepts)
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
이 프로젝트는 `Prometheus`와 `Grafana`를 활용하여 **서버, 데이터베이스, 웹 서버, 애플리케이션** 등 다양한 구성 요소의 메트릭을 수집·저장·시각화하고, **부하 테스트**를 통해 성능을 검증하며 문제를 조기에 탐지할 수 있는 **통합 모니터링 환경**을 구축하기 위한 Lab입니다.

### <a id="project-goals"></a>1-1. 프로젝트 목표 🎯

- Prometheus와 Grafana를 활용한 모니터링 시스템 구축 능력 향상

- 다양한 메트릭 노출 방식(Exporter vs. Actuator/Micrometer) 이해

- 다양한 Exporter(Node, MySQL, Nginx) 및 내부 노출 방식(Spring Boot Actuator/Micrometer)을 통해 메트릭 수집 경험 확보

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

- **모니터링 대상**  
   - 애플리케이션 서버(Spring Boot), 데이터베이스(MySQL), 웹 서버(Nginx), 호스트 시스템(Linux)  
- **Exporter 및 메트릭 노출**  
   - 각 대상에 설치된 Exporter(Node, MySQL, Nginx)와 Spring Boot Actuator(Micrometer)가 메트릭을 Prometheus 형식으로 노출 
   - **Node Exporter**: 리눅스 서버의 CPU, 메모리, 디스크 등 시스템 지표 수집
   - **MySQL Exporter**: MySQL 데이터베이스의 성능 및 상태 지표 수집
   - **Nginx Exporter**: Nginx 웹 서버의 상태 및 성능 지표 수집 
   - **Spring Boot Actuator/Micrometer**: Actuator와 Micrometer를 통해 내부 메트릭을 노출
- **Prometheus**  
   - Exporter 및 애플리케이션 메트릭 엔드포인트를 스크래핑하여 저장  
- **Grafana**  
   - Prometheus 데이터를 시각화하여 실시간 모니터링 및 대시보드 구성 
- **부하 테스트 도구**
   - 각 구성 요소의 성능을 검증하기 위한 부하 테스트 진행

### <a id="metrics-comparison"></a>3-2. 메트릭 노출 방식 비교

#### <a id="exporter-metrics"></a>3-2-1. Exporter 방식 (Node, MySQL, Nginx Exporter)
- **구현 방식**
   - 각 대상 시스템별로 별도의 Exporter 프로세스를 설치하여, 해당 시스템의 메트릭을 Prometheus 형식으로 노출

- **장점**
   - 대상 시스템과 독립적으로 동작하여, 애플리케이션에 추가적인 부하를 주지 않고 다양한 시스템 지표를 수집

- **단점**
   - 추가 프로세스의 설치 및 관리가 필요하며, 각각의 Exporter에 맞는 설정 및 유지보수가 필요

#### <a id="internal-metrics"></a>3-2-2. 내부 노출 방식 (Spring Boot Actuator + Micrometer)
- **구현 방식**
   - Spring Boot 애플리케이션이 자체적으로 Actuator와 Micrometer 라이브러리를 이용하여 /actuator/prometheus 등의 엔드포인트를 통해 메트릭을 노출

- **장점**
   - 애플리케이션 내부에서 직접 데이터를 제공하여 추가 외부 프로세스가 필요 없으며, 애플리케이션 특화 지표를 쉽게 제공

- **단점**
   - 애플리케이션 자체 리소스를 사용하므로, 메트릭 수집 시 애플리케이션에 부하가 추가

### <a id="workflow"></a>3-3. 워크플로우 및 연동
1. Exporter 및 Actuator 설정
   - Exporter 설치 및 활성화, Spring Boot 애플리케이션에 Actuator/Micrometer 의존성 및 설정 추가

2. Prometheus 스크래핑 구성
   - prometheus.yml에 Exporter와 /actuator/prometheus 엔드포인트를 타겟으로 등록

3. 메트릭 수집
   - Prometheus는 설정된 스크래핑 타겟(Exporter, Spring Boot 애플리케이션의 메트릭 엔드포인트)으로부터 정기적으로 데이터를 수집

4. 데이터 저장 및 시각화
   - 수집된 메트릭은 Prometheus에 저장되고, Grafana를 통해 대시보드로 시각화되어 모니터링 정보를 제공

5. 부하 테스트 및 모니터링
   - 각 구성 요소에 대해 부하 테스트를 수행하고, 성능 저하나 병목 현상을 모니터링

---

## <a id="concepts"></a>4. 📚 주요 개념

- **스크래핑(Scraping)**  
  Prometheus가 지정된 엔드포인트에서 메트릭을 주기적으로 긁어오는(process) 방식

- **Exporter**  
  시스템·서비스의 내부 지표를 Prometheus 형식으로 노출하는 별도 프로세스  
  - Node Exporter: 호스트 시스템 지표(CPU, 메모리, 디스크)  
  - MySQL Exporter: 데이터베이스 상태 정보  
  - Nginx Exporter: 웹 서버 지표

- **Actuator + Micrometer**  
  Spring Boot 애플리케이션 내부에서 메트릭을 노출하기 위한 모듈 조합  
  - Actuator: 운영용 엔드포인트 제공  
  - Micrometer: 다양한 모니터링 시스템(Prometheus 등)과 연동 가능한 메트릭 수집 라이브러리

- **Metric, Label, Target**  
  - Metric: 수집되는 데이터 항목 <br>
  (예: `http_requests_total`)  
  - Label: 메트릭에 부착되는 메타데이터 <br>
  (예: `method="GET", status="200"`)  
  - Target: Prometheus가 스크래핑할 엔드포인트 <br>
  (Exporter 또는 `/actuator/prometheus`)

---

## <a id="technologies"></a>5. 🚀 활용 기술

---

## <a id="implementation"></a>6. 🏗️ 시스템 구축 및 실행

### <a id="basic-monitoring"></a>6-1. 기본 모니터링 시스템 구축

#### <a id="prometheus-installation"></a>6-1-1. Prometheus 설치 (바이너리 방식)
&emsp;**1) 설치 방식 선택 배경**  
Prometheus는 다양한 설치 방식(APT, Docker, 바이너리 등)을 지원하지만, 최신 버전 유지와 설정 유연성을 고려하여 바이너리 설치 방식을 선택 

&emsp;**2) 설치 절차**
 - Prometheus 전용 사용자 계정 생성
    ```bash
    sudo useradd --no-create-home --shell /bin/false prometheus
    ```  

 - 디렉터리 생성 및 권한 설정
    ```bash
    sudo mkdir /etc/prometheus
    sudo mkdir /var/lib/prometheus
    sudo chown prometheus:prometheus /var/lib/prometheus
    ```
    ![alt text](<images/6-1-1.-2)-2.prometheus 설치.png>)

 - 최신 바이너리 다운로드 및 압축 해제
    ```bash
    cd /tmp

    # GitHub Releases에서 최신 URL 확인 후 다운로드
    curl -LO https://github.com/prometheus/prometheus/releases/download/v3.2.1/prometheus-3.2.1.linux-amd64.tar.gz

    # 압축 해제
    tar xvf prometheus-3.2.1.linux-amd64.tar.gz
    ```
    ![alt text](<images/6-1-1.-2)-3.prometheus 설치.png>) 

 - 실행 파일 및 설정 파일 이동
    ```bash
    cd prometheus-3.2.1.linux-amd64/

    # 실행 파일 이동
    sudo mv prometheus promtool /usr/local/bin/

    # 설정 파일 이동
    sudo mv prometheus.yml /etc/prometheus/
    ```
    ![alt text](<images/6-1-1.-2)-4.prometheus 설치.png>)

 - Prometheus 설정 파일 확인
    ```bash
    # Prometheus 서버가 정상적으로 실행되고 있는지 localhost 포트로 확인
    curl localhost:9090

    # Prometheus 설정 파일 내용 확인
    sudo cat /etc/prometheus/prometheus.yml
    ```
    ![alt text](<images/6-1-1.-2)-5.prometheus 설정 파일 확인.png>)

&emsp;**3) Prometheus 서비스 등록**  
 - prometheus.service 유닛 파일 생성 및 내용 작성
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

 - Prometheus 서비스 등록 및 실행 
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
 - 기본 포트 9090 접근 확인 (curl)
    ```bash
    # 기본 포트 9090에 접근하여 응답이 오는지 확인
    curl http://localhost:9090
    ```
    ![alt text](<images/6-1-1.-4)-1.prometheus http 서버 확인.png>)

 - VM NAT 포트 포워딩 설정
    ![alt text](<images/6-1-1.-4)-2.prometheus http 서버 확인.png>)

 - 브라우저 접속 확인
    ![alt text](<images/6-1-1.-4)-3.prometheus http 서버 확인.png>)
    아직 메트릭을 수집할 타겟(exporter) 이 등록되지 않았기 때문에 위와 같은 화면이 나옴

#### <a id="grafana-installation"></a>6-1-2. Grafana 설치 (APT 패키지 방식)
&emsp;**1) 설치 방식 선택 배경**  
Grafana는 다양한 설치 방식(APT, Docker, 바이너리 등)을 제공하지만, 편리한 설치와 업데이트 및 서비스 관리를 고려하여 APT 패키지 방식을 선택

&emsp;**2) 설치 절차**
 - APT 패키지 설치 및 Grafana 저장소 등록
    ```bash
    # 필수 패키지 설치
    sudo apt install -y apt-transport-https software-properties-common wget

    # Grafana GPG 키 등록
    wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -

    # APT 저장소 등록
    echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
    ```
    ![alt text](<images/6-1-2.-2)-1.grafana 설치.png>)

 - 저장소 등록 확인 및 패키지 목록 업데이트
    ```bash
    # 저장소 디렉토리 목록 확인
    ls /etc/apt/sources.list.d/
    ls -al /etc/apt/sources.list.d/

    # grafana.list 파일의 내용 확인
    cat /etc/apt/sources.list.d/grafana.list

    # 패키지 목록 갱신
    sudo apt update
    ```
    ![alt text](<images/6-1-2.-2)-2.grafana 설치.png>)

 - Grafana 설치
    ```bash
    # 설치
    sudo apt install -y grafana
    ```
    ![alt text](<images/6-1-2.-2)-3.grafana 설치.png>)

&emsp;**3) Grafana 서비스 실행 및 상태 확인**    
Grafana는 APT로 설치한 것만으로 서비스로 등록되며, 별도의 유닛 파일 작성이 필요 없음
 - Grafana 서비스 상태 확인 
    ```bash
    # 서비스 상태 확인
    systemctl status grafana-server
    ```
    ![alt text](<images/6-1-2.-3)-1.grafana 서비스 실행.png>)
 - Grafana 서비스 실행 및 확인
    ```bash
    # Grafana 서비스 자동 시작 등록 및 시작
    sudo systemctl enable --now grafana-server

    # 서비스 상태 확인
    systemctl status grafana-server
    ```
    ![alt text](<images/6-1-2.-3)-2.grafana 서비스 실행.png>)

&emsp;**4) Grafana HTTP 서버 확인**
Grafana 서비스가 성공적으로 실행되었는지 확인
 - 기본 포트 3000 접근 확인 (curl)
    ```bash
    # 기본 포트 3000에 접근하여 응답이 오는지 확인
    curl http://localhost:3000
    ```
    ![alt text](<images/6-1-2.-4)-1.grafana http 서버 확인.png>)
    
 - VM NAT 포트 포워딩 설정
    ![alt text](<images/6-1-2.-4)-2.grafana http 서버 확인.png>)
    
 - 브라우저 접속 확인
    ![alt text](<images/6-1-2.-4)-3.grafana http 서버 확인.png>)

#### <a id="prometheus-grafana-integration"></a>6-1-3. Prometheus와 Grafana 연동
&emsp;**1) Grafana 웹 접속** <br>
&emsp;**2) Connections -> Data sources -> Prometheus** <br>
&emsp;**3) Prometheus server URL 입력** <br>

![alt text](<images/6-1-3.prometheus grafana 연동.png>)

---

### <a id="exporter-setup"></a>6-2. Exporter 기반 모니터링 구축

#### <a id="node-exporter"></a>6-2-1. Node Exporter 설치 및 구성
&emsp;**1) Node Exporter 설치 (바이너리 방식)**  
 - 최신 바이너리 다운로드 및 압축 해제
    ```bash
    cd /tmp

    # Node Exporter 바이너리 다운로드
    curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz

    # 압축 해제
    tar xvf node_exporter-1.9.1.linux-amd64.tar.gz
    ```
    ![alt text](<images/6-2-1.-1)-1.node exporter 설치.png>)

 - 실행 파일 이동
    ```bash
    # 실행 파일 이동
    sudo mv node_exporter-*/node_exporter /usr/local/bin/
    ```
    ![alt text](<images/6-2-1.-1)-2.node exporter 설치1.png>)
    ![alt text](<images/6-2-1.-1)-2.node exporter 설치2.png>)

&emsp;**2) Node Exporter 서비스 등록**  
 - node_exporter.service 유닛 파일 생성 및 내용 작성
    ```bash
    # node_exporter.service 유닛 파일 생성 및 내용 작성
    sudo tee /etc/systemd/system/node_exporter.service <<EOF
    [Unit]
    Description=Node Exporter
    Wants=network-online.target
    After=network-online.target

    [Service]
    User=prometheus
    Group=prometheus
    Type=simple
    ExecStart=/usr/local/bin/node_exporter

    [Install]
    WantedBy=multi-user.target
    EOF
    ```
    ![alt text](<images/6-2-1.-2)-1.node exporter 서비스 등록.png>)
  
 - Node Exporter 서비스 등록 및 실행
    ```bash
    # systemd 데몬 재시작 (유닛 파일 반영)
    sudo systemctl daemon-reload

    # Node Exporter 서비스 자동 시작 등록 및 시작
    sudo systemctl enable --now node_exporter

    # 서비스 상태 확인
    systemctl status node_exporter
    ```
    ![alt text](<images/6-2-1.-2)-2.node exporter 서비스 등록1.png>)
    ![alt text](<images/6-2-1.-2)-2.node exporter 서비스 등록2.png>)

&emsp;**3) Prometheus 설정**  
 - prometheus.yml 설정 파일 수정
   ```bash
   # Prometheus가 Node Exporter에서 수집한 시스템 메트릭을 가져올 수 있도록 설정 파일 수정
   sudo nano /etc/prometheus/prometheus.yml

   # 아래의 내용을 추가
   scrape_configs:
      - job_name: 'node'
        static_configs:
            - targets: ['localhost:9100']

   # 수정한 파일 확인
   sudo cat /etc/prometheus/prometheus.yml
   ```
   ![alt text](<images/6-2-1.-3)-1.prometheus 설정.png>)

 - Prometheus 서비스 재시작 및 상태 확인
   ```bash
   # 서비스 재시작
   sudo systemctl restart prometheus

   # 서비스 상태 확인
   sudo systemctl status prometheus
   ```
   ![alt text](<images/6-2-1.-3)-2.prometheus 설정.png>)

&emsp;**4) 브라우저에서 확인**
 - Prometheus 타겟 상태 확인 <br>
 Node Exporter가 Prometheus에 정상적으로 등록되었는지 웹 브라우저에서 확인
   ![alt text](<images/6-2-1.-4)-1.브라우저에서 확인.png>)

 - PromQL로 메트릭 수집 확인 <br>
 쿼리 실행 시 localhost:9100으로부터 CPU 사용률 데이터를 정상적으로 가져오는지 확인
   ![alt text](<images/6-2-1.-4)-2.브라우저에서 확인.png>)

 - Grafana 대시보드에서 시각화 확인
   ![alt text](<images/6-2-1.-4)-3.브라우저에서 확인.png>)

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

```bash
sudo apt install nginx
```

<br>

&emsp;**2) Nginx Stub Status 설정**  

```bash
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

```bash
wget https://github.com/nginx/nginx-prometheus-exporter/releases/download/v1.4.1/nginx-prometheus-exporter_1.4.1_linux_amd64.tar.gz
tar -xvzf nginx-prometheus-exporter_1.4.1_linux_amd64.tar.gz
```
파일 다운로드 & 압축 해제

<br>

```bash
sudo mv nginx-prometheus-exporter /usr/local/bin/
sudo chmod +x /usr/local/bin/nginx-prometheus-exporter
```
디렉토리로 이동 & 실행 권한 부여

<br>

&emsp;**4) Nginx Exporter 서비스 등록**  

```bash
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

```bash
# 서비스 시작 및 확인

sudo systemctl daemon-reload
sudo systemctl enable --now nginx_exporter
systemctl status nginx_exporter
```

<br>

&emsp;**5) Prometheus 설정**  

```bash
# /etc/prometheus/prometheus.yml

- job_name: 'nginx-exporter'
    static_configs:
      - targets: ['localhost:9113']
```

```bash
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

```bash
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
```bash
implementation 'org.springframework.boot:spring-boot-starter-actuator'
implementation 'io.micrometer:micrometer-registry-prometheus'
```
➡️ build.gradle을 통해 /actuator/prometheus 경로를 외부에 노출

&emsp;**2) Spring Boot App 서비스 등록**  
<br>
![alt text](<images/6-3-2) Spring Boot App 서비스 등록.png>)
➡️ Gradle로 .jar 파일 빌드 후 Ubuntu 서버에 업로드(경로:/home/ubuntu/springapp.jar)
```bash
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
```bash
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

```bash
sudo apt install apache2-utils
```
HTTP 서버의 성능을 측정하는 스트레스 테스트 도구인 Apache Bench 설치

<br>

&emsp;**2) Nginx 부하 테스트 및 모니터링**

```bash
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
```bash
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

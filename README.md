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

![alt text](<images/6-2-2-6)확인.png>)

- grafana 시각화 연결 확인

![alt text](<images/6-2-2-6)확인2.png>)

---

#### <a id="nginx-exporter"></a>6-2-3. Nginx Exporter 설치 및 구성
&emsp;**1) Nginx 설치**  
&emsp;**2) Nginx Stub Status 설정**  
&emsp;**3) Nginx Exporter 설치 (바이너리 방식)**  
&emsp;**4) Nginx Exporter 서비스 등록**  
&emsp;**5) Prometheus 설정**  
&emsp;**6) 확인**

---

### <a id="spring-boot-monitoring"></a>6-3. Spring Boot 애플리케이션 모니터링 구축 (내부 메트릭 노출)

&emsp;**1) Spring Boot Actuator 및 Micrometer 설정**  
&emsp;**2) Spring Boot App 서비스 등록**  
&emsp;**3) Prometheus 설정**  
&emsp;**4) 확인**

---

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
&emsp;**2) Nginx 부하 테스트 및 모니터링**

### <a id="spring-boot-load-testing"></a>7-3. Spring Boot 애플리케이션 부하 테스트 및 모니터링
&emsp;**1) Spring Boot App 부하 테스트 준비**  
&emsp;**2) Spring Boot App 부하 테스트 및 모니터링**

---

## <a id="troubleshooting"></a>8. 🔫 트러블슈팅
- MySQL Exporter 실행 시 cnf 파일 권한 문제 발생 -> 권한 설정 변경하여 실행 필수

![alt text](<images/8. 트러블슈팅_1.png>)

---

## <a id="improvements"></a>9. 💡 고찰 및 개선점

---

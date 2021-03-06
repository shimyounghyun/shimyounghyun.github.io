---
title : "CI & CD"
categories :
    - CI&CD
tag :
    - CI&CD
---

### CI/CD
안정적인 배포 파일을 만드는 과정을 CI(Continuous Integration - 지속적 통합)라고 한다.
이 결과를 자동으로 운영 서버에 무중단 배포까지 진행되는 과정을 CD(Continuous Deployment - 지속적인 배포)라고 한다.

마틴 파울러의 4가지 CI 규칙

- 모든 소스 코드가 살아있고 현재의 소스에 접근할 수 있는 단일 지점을 유지
- 빌드 프로세스를 자동화해 누구든 소스로부터 시스템을 빌드하는 단일 명령어를 사용가능
- 테스팅을 자동화, 단일 명령어로 테스트를 실행 가능
- 실행 파일을 얻을경우 가장 완전한 실행 파일을 얻을 수 있어야함

### CI 도구들
- Travis CI
    - 깃허브에서 제공하는 무료 CI 서비스 
- AWS CodeBuild
    - 설치형 CI 서비스, 이를 위한 EC2 인스턴스가 하나 더 필요
- 젠킨스
    - 빌드 시간만큼 요금이 부과되는 구조
### 배포 전략
- blue/green
    - blue : 구버전, green : 새 버전을 의미한다.
    - 구버전, 신버전을 동시에 구성하여 업데이트 완료 시 일제히 트래픽을 전환한다.
    - 시스템 자원이 많이 필요하며 비용이 많이 들 수 있다.
- rolling update
    - 일반적인 배포 방식이다. 서비스중인 서버 외에 다른 서버를 새버전으로 교체해가며 트래픽을 전환한다.
    배포 중 트래픽이 몰릴 수 있어 서버 처리 용량을 고려해야한다.
- canary
    - 특정 일부 사용자에게 새버전을 배포 한 후 오류 여부를 확인한다.
    - 충분한 테스트를 거친 후 전체 배포를 진행한다. 
### 참조
- 스프링부트와 AWS로 혼자구현하는 웹서비스 - 이동욱저
- https://reference-m1.tistory.com/211

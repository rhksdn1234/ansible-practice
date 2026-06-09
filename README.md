# ansible-practice

Ansible을 활용한 멀티 OS 서버 자동화 실습 저장소입니다.
서로 다른 운영체제(Rocky Linux, Ubuntu)를 하나의 플레이북으로 동시에 관리합니다.

## 실습 환경

- **제어 노드**: Rocky Linux (Ansible 설치)
- **관리 대상**
  - Rocky Linux 서버 (RHEL 계열, dnf)
  - Ubuntu 24.04 서버 (Debian 계열, apt)

## 파일 구성

- `install_tree.yml` : 멀티 OS 대응 패키지 설치 플레이북
- `inventory.ini.example` : 인벤토리 작성 예시 (실제 서버 정보는 제외)
- `.gitignore` : 민감 정보가 담긴 inventory.ini 제외

## 핵심 개념

### OS에 따라 분기 처리 (when 조건문)

`ansible_os_family` 변수로 OS 계열을 판단하여, 각 OS에 맞는 작업만 실행합니다.

- RHEL 계열: EPEL 저장소 설치
- Debian 계열: apt 캐시 갱신
- 공통: `package` 모듈로 패키지 설치 (OS에 따라 dnf/apt 자동 선택)

### 멱등성 (Idempotency)

플레이북을 여러 번 실행해도 결과가 동일합니다.
이미 원하는 상태(패키지 설치 완료)면 변경하지 않습니다.

## 사용 방법

​```bash
# 인벤토리 예시를 복사해 실제 정보로 수정
cp inventory.ini.example inventory.ini

# 연결 확인
ansible myservers -i inventory.ini -m ping -k

# 플레이북 실행 (-k: SSH 비밀번호, -K: sudo 비밀번호)
ansible-playbook install_tree.yml -i inventory.ini -k -K
​```

## 실습 중 겪은 트러블슈팅

- **RHEL 구독 문제**: 구독 미등록 RHEL은 기본 저장소가 막혀 패키지 설치 불가
- **계정 문제**: Ubuntu는 root 직접 접속이 막혀 일반 계정으로 접속 필요
- **sudo 권한**: 일반 계정 접속 시 `-K` 옵션으로 sudo 비밀번호 전달 필요

# 폐쇄망 환경에서 VS Code Remote-SSH 세팅 가이드

## 개요

폐쇄망 개발서버에 VS Code Remote-SSH로 접속하려면, 로컬 PC에 설치된 VS Code 버전에 맞는 **VS Code Server**가 개발서버에 설치되어 있어야 합니다.

---

## 핵심 개념

| 항목 | 설명 |
|---|---|
| **로컬 VS Code** | 내 노트북에 설치된 VS Code (GUI 앱) |
| **VS Code Server** | SSH 접속 시 개발서버에서 실행되는 경량 바이너리 (별도 파일) |
| **Commit ID** | 두 버전을 매칭하는 핵심 식별자 |

> ⚠️ 로컬 VS Code와 VS Code Server의 버전(Commit ID)이 일치해야 정상 접속됩니다.  
> 인터넷이 되는 환경에서는 자동으로 처리되지만, **폐쇄망에서는 수동 설치가 필요**합니다.

---

## 노트북 B 세팅 순서

### STEP 1. 노트북 A에서 VS Code 버전 확인

```
VS Code 실행 → Help → About
→ Version: 1.xx.x
→ Commit: xxxxxxxxxxxxxxxx  ← 이 값을 메모
```

---

### STEP 2. 노트북 B에 동일 버전 VS Code 설치파일 확보

아래 세 가지 방법 중 하나를 선택합니다.

#### ① 노트북 A에서 설치파일 찾기 (가장 빠름)

**Windows:**
```powershell
# 전체 드라이브에서 VS Code 설치파일 검색
Get-ChildItem -Path C:\ -Recurse -Include "VSCode*.exe","VSCodeSetup*.exe" -ErrorAction SilentlyContinue
```

**macOS:**
```bash
find / -name "VSCode*.dmg" 2>/dev/null
find ~/Downloads -name "*code*" 2>/dev/null
```

**Linux:**
```bash
find / -name "code*.deb" -o -name "code*.rpm" -o -name "code*.tar.gz" 2>/dev/null
```

찾은 파일을 USB 등을 통해 노트북 B로 복사 후 설치합니다.

---

#### ② 개발서버에서 설치파일 찾기

예전에 누군가 개발서버에 올려둔 설치파일을 찾는 경우입니다.

```bash
# VS Code 관련 파일 전체 탐색
find / -name "code*.deb" 2>/dev/null      # Ubuntu/Debian
find / -name "code*.rpm" 2>/dev/null      # CentOS/RHEL
find / -name "code*" -type f 2>/dev/null  # 이름에 code 포함된 파일

# 자주 사용하는 위치 빠르게 확인
ls /tmp/
ls /opt/
ls /home/
ls /var/tmp/
```

찾은 설치파일을 로컬로 복사합니다 (SCP 또는 USB 등).

```bash
# 개발서버 → 로컬로 파일 복사 (노트북에서 실행)
scp user@개발서버IP:/경로/code-설치파일.deb ./
```

---

#### ③ 외부 인터넷에서 직접 다운로드

인터넷이 되는 환경(외부망 PC 등)에서 STEP 1에서 확인한 **동일 버전**을 다운로드합니다.

- 공식 다운로드 페이지: https://code.visualstudio.com/Download
- 특정 버전: https://code.visualstudio.com/updates/v1_xx  (버전 번호 입력)

다운로드 후 USB 등을 통해 노트북 B로 전달합니다.

---

### STEP 3. 노트북 B에 VS Code 설치

확보한 설치파일로 VS Code를 설치합니다.  
설치 후 `Help → About`에서 **Commit ID가 노트북 A와 동일한지 반드시 확인**합니다.

---

### STEP 4. VS Code Server 확인 (개발서버)

노트북 A로 한 번이라도 접속한 적 있다면, VS Code Server가 이미 개발서버에 설치되어 있습니다.

```bash
# 개발서버에서 확인
ls ~/.vscode-server/bin/
# → {commit-id} 폴더가 있으면 설치된 것
```

노트북 B의 Commit ID와 일치하는 폴더가 있으면 **추가 설치 불필요**, 접속 시 자동으로 재사용됩니다.

만약 폴더가 없다면 VS Code Server를 수동으로 설치해야 합니다. (별도 문의)

---

### STEP 5. SSH 설정 세팅

**방법 A: 노트북 A의 SSH 키를 그대로 복사**

```bash
# 노트북 A의 SSH 키를 노트북 B로 복사
# 복사 대상 파일: ~/.ssh/id_rsa (또는 id_ed25519), id_rsa.pub, config
```

노트북 B의 `~/.ssh/` 폴더에 붙여넣기 후 권한 설정:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```

**방법 B: 노트북 B용 SSH 키를 새로 발급**

```bash
# 노트북 B에서 새 키 생성
ssh-keygen -t ed25519 -C "notebookB"

# 생성된 공개키를 개발서버에 등록
ssh-copy-id user@개발서버IP
# 또는 수동으로 개발서버의 ~/.ssh/authorized_keys 에 공개키 추가
```

---

### STEP 6. VS Code Remote-SSH 확장 설치 및 접속 테스트

```
VS Code → Extensions → "Remote - SSH" 검색 후 설치
→ 좌측 하단 "><" 아이콘 클릭
→ "Connect to Host" → 개발서버 접속
```

---

## 요약

| 단계 | 할 일 |
|---|---|
| ① | 노트북 A에서 VS Code Commit ID 확인 |
| ② | 설치파일 확보 (노트북 A → 개발서버 → 외부 인터넷 순으로 시도) |
| ③ | 노트북 B에 **동일 버전** VS Code 설치 및 Commit ID 확인 |
| ④ | 개발서버에 VS Code Server가 있는지 확인 |
| ⑤ | SSH 키 설정 (복사 또는 신규 발급) |
| ⑥ | Remote-SSH 확장 설치 후 접속 테스트 |

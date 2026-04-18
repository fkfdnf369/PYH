# 폐쇄망 개발서버에서 계정 간 OpenCode 공유 가이드

## 사전 확인

계정 A에서 아래 명령어로 설치 위치를 먼저 확인합니다.

```bash
which opencode
```

결과에 따라 아래 해당하는 섹션을 참고하세요.

---

## Case 1. `/usr/local/bin/opencode` — 시스템 전체 설치

이미 서버 전체에 설치된 상태입니다.  
계정 B에서 실행이 안 된다면 **PATH 문제**일 가능성이 높습니다.

**계정 B에서 확인:**
```bash
echo $PATH
ls /usr/local/bin/opencode
```

PATH에 `/usr/local/bin`이 없다면 추가합니다.

```bash
# ~/.bashrc 또는 ~/.zshrc 에 추가
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

---

## Case 2. `/home/계정A/...` 하위 경로 — 계정 A에만 설치된 경우

계정 A의 홈 디렉토리에만 설치된 상태입니다.  
두 가지 방법 중 선택합니다.

### 방법 1. 공용 경로로 복사 (권장, root 권한 필요)

```bash
# root 또는 sudo 권한으로 실행
sudo cp $(which opencode) /usr/local/bin/opencode
sudo chmod 755 /usr/local/bin/opencode
```

이후 계정 B에서 바로 사용 가능합니다.

### 방법 2. 계정 B의 홈 디렉토리로 직접 복사 (root 권한 불필요)

```bash
# 계정 A에서 실행 — 계정 B의 홈으로 복사
cp $(which opencode) /home/계정B/.local/bin/opencode

# 계정 B에서 실행 — 권한 및 PATH 설정
chmod 755 ~/.local/bin/opencode
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

> ⚠️ opencode가 단일 바이너리가 아니라 여러 파일로 구성된 경우, 폴더 전체를 복사해야 할 수 있습니다.  
> 이 경우 `cp -r` 대신 아래를 참고하세요.

```bash
# 설치 폴더 전체 복사
cp -r /home/계정A/.local/share/opencode /home/계정B/.local/share/opencode
```

---

## Case 3. `/home/계정A/.npm/...` 또는 `.nvm/...` — npm/nvm으로 설치된 경우

npm 또는 nvm은 기본적으로 **계정별로 독립 설치**됩니다.  
계정 B에서도 동일한 방식으로 설치하거나, 바이너리만 복사하는 방법을 씁니다.

### 방법 1. 계정 B에서 npm으로 직접 설치 (인터넷 불가 시 사용 어려움)

```bash
npm install -g opencode
```

### 방법 2. 계정 A의 바이너리를 계정 B로 복사

```bash
# 계정 A에서 실제 바이너리 위치 확인
which opencode        # 심볼릭 링크일 수 있음
readlink -f $(which opencode)  # 실제 파일 경로 확인

# 계정 B의 로컬 bin으로 복사
mkdir -p /home/계정B/.local/bin
cp $(readlink -f $(which opencode)) /home/계정B/.local/bin/opencode
chmod 755 /home/계정B/.local/bin/opencode
```

**계정 B에서 PATH 설정:**
```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### 방법 3. 공용 경로로 복사 (root 권한 필요)

```bash
sudo cp $(readlink -f $(which opencode)) /usr/local/bin/opencode
sudo chmod 755 /usr/local/bin/opencode
```

---

## 최종 확인

어떤 방법을 쓰든 계정 B에서 아래로 동작 여부를 확인합니다.

```bash
which opencode
opencode --version
```

---

## 요약

| 결과 | 원인 | 권장 방법 |
|---|---|---|
| `/usr/local/bin/opencode` | 이미 전체 설치, PATH 문제 | 계정 B의 PATH에 `/usr/local/bin` 추가 |
| `/home/계정A/.local/bin/...` | 계정 A에만 설치 | sudo 있으면 `/usr/local/bin`으로 복사, 없으면 계정 B 홈으로 복사 |
| `/home/계정A/.npm/...` or `.nvm/...` | npm/nvm으로 계정별 설치 | 바이너리 복사 후 PATH 설정, 또는 계정 B에서 재설치 |

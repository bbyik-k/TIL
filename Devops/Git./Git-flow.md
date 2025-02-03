# Git Flow 개념 및 사용 방법

## 1. Git Flow 개념

Git Flow는 **Git 브랜치 관리 전략** 중 하나로, 협업 프로젝트에서 코드의 안정성과 개발 흐름을 유지하기 위해 사용됩니다. Git Flow는 다음과 같은 주요 브랜치를 사용합니다:

- **main**: 배포 가능한 안정적인 브랜치
- **develop**: 개발을 진행하는 기본 브랜치
- **feature**: 새로운 기능을 개발하는 브랜치 (develop에서 생성, 완료 후 develop에 병합)
- **release**: 배포 전 안정화 및 테스트를 위한 브랜치 (develop에서 생성, main에 병합 후 태깅)
- **hotfix**: 배포 후 긴급 수정이 필요한 경우 사용 (main에서 생성, main과 develop에 병합)

---

## 2. Git Flow 설치

Git Flow를 사용하려면 먼저 설치해야 합니다.

### **Windows**

Git Bash에서 다음 명령어 실행:

```bash
git flow version  # 설치 확인
git flow init  # Git Flow 초기화
```

### **Mac (Homebrew 사용)**

```bash
brew install git-flow-avh
```

### **Linux (Debian/Ubuntu 계열)**

```bash
sudo apt install git-flow
```

Git Flow가 설치되었는지 확인하려면:

```bash
git flow version
```

---

## 3. Git Flow 초기화

Git Flow를 사용하려면 먼저 저장소에서 초기화를 진행해야 합니다.

```bash
git flow init
```

위 명령어를 실행하면 브랜치 네이밍 설정이 나오며, 기본값을 그대로 사용해도 됩니다.

---

## 4. Git Flow 브랜치 사용 방법

### **(1) 새로운 기능 개발 (Feature Branch)**

```bash
git flow feature start <feature-name>
# 기능 개발 진행
# 완료 후:
git flow feature finish <feature-name>
```

### **(2) 배포 준비 (Release Branch)**

```bash
git flow release start <version>
# 테스트 및 안정화 진행
# 완료 후:
git flow release finish <version>
```

이 과정에서 `main` 브랜치에 태그가 자동으로 생성됩니다.

### **(3) 긴급 수정 (Hotfix Branch)**

```bash
git flow hotfix start <hotfix-name>
# 버그 수정 진행
# 완료 후:
git flow hotfix finish <hotfix-name>
```

---

## 5. Git Flow와 GitHub 연동 (Push 및 병합)

Git Flow에서 생성된 브랜치를 GitHub에 푸시하려면:

```bash
git push origin <branch-name>
```

브랜치 병합 후 원격 저장소 업데이트:

```bash
git push origin develop
```

릴리즈 또는 핫픽스를 완료한 후 태그를 푸시하려면:

```bash
git push --tags
```

---

## 6. Git Flow의 장점

- **명확한 브랜치 전략**: 역할별로 브랜치를 구분하여 협업이 쉬워짐
- **안정적인 배포 가능**: `release` 브랜치를 통해 배포 전 테스트 가능
- **긴급 대응 가능**: `hotfix` 브랜치를 이용하여 빠른 문제 해결 가능

Git Flow를 잘 활용하면 프로젝트의 개발 흐름을 효과적으로 관리할 수 있습니다! 🚀

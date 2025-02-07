# GitHub Flow 가이드

GitHub Flow는 GitHub에서 효과적으로 협업하고 배포하기 위한 간단한 Git 워크플로우입니다. 이 방식은 지속적인 배포(Continuous Deployment, CD) 환경에서 특히 유용하며, 빠른 개발 사이클과 코드 리뷰를 강조합니다.

## GitHub Flow의 특징

1. **단순하고 직관적인 브랜칭 모델**
2. **빠른 기능 개발 및 배포**
3. **효율적인 코드 리뷰 및 협업**
4. **메인 브랜치(main)의 항상 배포 가능한 상태 유지**

## GitHub Flow의 기본 흐름

### 1. `main` 브랜치는 항상 배포 가능한 상태 유지

- `main` 브랜치는 최신 안정 버전을 포함하고 있어야 하며, 직접 작업하지 않습니다.

### 2. 새로운 기능 개발을 위한 브랜치 생성

- 기능을 개발할 때 `main`에서 새로운 브랜치를 생성합니다.
- 브랜치명은 작업 내용이 명확히 드러나도록 합니다.

```sh
# 브랜치 생성
$ git checkout -b feat/new-feature
```

### 3. 커밋 및 지속적인 푸시

- 변경 사항을 논리적으로 나눠 커밋합니다.
- 커밋 메시지는 컨벤셔널 커밋 규칙을 따릅니다.

```sh
$ git add .
$ git commit -m "feat: implement new feature"
$ git push origin feat/new-feature
```

### 4. Pull Request(PR) 생성 및 코드 리뷰

- GitHub에서 `feat/new-feature` 브랜치에 대한 PR을 생성합니다.
- 팀원들에게 리뷰를 요청하고 피드백을 반영합니다.

### 5. PR 승인 후 `main` 브랜치로 병합

- 리뷰가 완료되면 `main` 브랜치로 병합(Merge)합니다.
- 일반적으로 **Squash and Merge** 또는 **Rebase and Merge** 방식을 사용합니다.
- `main` 브랜치가 항상 배포 가능한 상태를 유지해야 합니다.

```sh
$ git checkout main
$ git pull origin main
$ git merge feat/new-feature
$ git push origin main
```

### 6. 배포 및 브랜치 삭제

- `main` 브랜치에 병합된 코드가 자동으로 배포될 수도 있고, 수동으로 배포할 수도 있습니다.
- 작업이 끝난 브랜치는 삭제하여 깔끔한 저장소를 유지합니다.

```sh
$ git branch -d feat/new-feature
$ git push origin --delete feat/new-feature
```

## GitHub Flow의 장점

- **단순하고 직관적**: 초보자도 쉽게 사용할 수 있습니다.
- **빠른 배포 가능**: `main` 브랜치에 병합된 즉시 배포할 수 있습니다.
- **병렬 개발 지원**: 여러 브랜치를 동시에 관리하며 독립적인 개발이 가능합니다.
- **효율적인 코드 리뷰**: Pull Request를 통한 코드 리뷰가 기본으로 포함됩니다.

## GitHub Flow와 Git Flow의 차이점

GitHub Flow와 Git Flow는 각각 다른 방식의 Git 워크플로우이며, 프로젝트의 요구 사항에 따라 선택됩니다.

| 특징           | GitHub Flow                         | Git Flow                               |
| -------------- | ----------------------------------- | -------------------------------------- |
| 브랜치 모델    | 단순                                | 복잡                                   |
| 메인 브랜치    | `main`만 존재                       | `main` + `develop`                     |
| 기능 개발 방식 | `main`에서 기능 브랜치 생성 후 병합 | `develop`에서 기능 브랜치 생성 후 병합 |
| 배포 방식      | 지속적 배포 (CD)                    | 명확한 릴리즈 사이클                   |
| 사용 사례      | 소규모 프로젝트, 빠른 배포          | 대규모 프로젝트, 명확한 릴리즈 관리    |

- **GitHub Flow**는 `main` 브랜치를 중심으로 동작하며, 간단한 브랜치 구조로 빠른 배포가 가능합니다.
- **Git Flow**는 `develop`, `feature`, `release`, `hotfix` 등의 다양한 브랜치를 활용하여 명확한 릴리즈 사이클을 유지합니다.
- GitHub Flow는 지속적인 배포가 필요한 프로젝트에 적합하고, Git Flow는 명확한 버전 관리를 필요로 하는 프로젝트에서 주로 사용됩니다.

## GitHub Flow를 사용할 때 주의할 점

- `main` 브랜치에는 반드시 **테스트가 통과된 코드**만 병합해야 합니다.
- 긴 브랜치를 유지하지 않고, 작은 기능 단위로 빠르게 PR을 생성하는 것이 좋습니다.
- 배포 전략과 함께 사용해야 실무에서 효과적으로 운영할 수 있습니다.

GitHub Flow는 작은 팀이나 빠른 개발 주기가 필요한 프로젝트에 적합한 워크플로우입니다. 지속적인 배포 환경에서 특히 강력한 효과를 발휘하며, 협업을 원활하게 만드는 데 큰 도움을 줍니다.

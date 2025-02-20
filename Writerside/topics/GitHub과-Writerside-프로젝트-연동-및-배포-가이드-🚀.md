# GitHub과 Writerside 프로젝트 연동 및 배포 가이드 🚀

### **GitHub과 Writerside 프로젝트 연동 및 배포 가이드** 🚀

이 문서는 **기존 Writerside 프로젝트를 GitHub 레포지토리(`Jakezo-docs`)에 연결하고 GitHub Pages를 통해 배포하는 방법**을 설명합니다.

---

## **1️⃣ GitHub 레포지토리 연결**
### **🔹 1. Git 원격 저장소 추가**
1. Writerside 프로젝트 폴더로 이동
   ```sh
   cd /path/to/your-writerside-project
   ```
2. Git 초기화 (이미 되어 있다면 생략)
   ```sh
   git init
   ```
3. 원격 저장소 추가
   ```sh
   git remote add origin https://github.com/<YOUR_USERNAME>/Jakezo-docs.git
   git branch -M main
   ```
4. **연결 확인**
   ```sh
   git remote -v
   ```
   출력 예시:
   ```
   origin  https://github.com/YOUR_USERNAME/Jakezo-docs.git (fetch)
   origin  https://github.com/YOUR_USERNAME/Jakezo-docs.git (push)
   ```

---

## **2️⃣ Writerside 문서 빌드 및 GitHub Pages 배포**
### **🔹 1. Writerside 문서 빌드**
#### **👉 IntelliJ에서 실행**
- `Tools > Writerside > Build` 클릭

#### **👉 터미널에서 실행**
```sh
./gradlew writersideBuild
```
- 빌드 완료 후 HTML 파일이 `build/docs/` 폴더에 생성됩니다.

### **🔹 2. GitHub Pages 배포를 위한 설정**
1. GitHub Pages는 `docs/` 폴더를 기본적으로 사용하므로, `build/docs/`에서 복사합니다.
   ```sh
   mkdir -p docs
   cp -r build/docs/* docs/
   ```
2. `.gitignore`에 JetBrains 및 Writerside 관련 파일을 제외하는 설정 추가
   ```gitignore
   # Ignore JetBrains IDE settings
   .idea/
   *.iml
   *.ipr
   *.iws

   # Ignore Writerside build output
   .writerside/
   .writerside-cache/
   build/
   out/
   ```

### **🔹 3. GitHub에 Push**
```sh
git add .
git commit -m "Add Writerside documentation"
git push origin main
```

### **🔹 4. GitHub Pages 활성화**
1. **GitHub Repository (`Jakezo-docs`)로 이동**
2. **Settings > Pages** 탭으로 이동
3. **Branch**: `main`
4. **Directory**: `/docs` 선택 후 **Save**

### **🔹 5. 배포된 문서 확인**
GitHub Pages 설정이 완료되면, 아래 주소에서 문서를 확인할 수 있습니다:
```
https://<YOUR_USERNAME>.github.io/Jakezo-docs/
```

---

## **3️⃣ Git Push 오류 해결 방법**
### **🔹 1. GitHub Personal Access Token (PAT) 설정 (패스워드 인증 불가)**
GitHub에서는 HTTPS 방식의 패스워드 인증이 차단되었으므로, **PAT (Personal Access Token)**을 사용해야 합니다.

1. **GitHub에서 토큰 발급:**
    - `Settings > Developer Settings > Personal Access Tokens`
    - `repo`, `workflow` 권한 포함 후 생성
2. **GitHub 원격 URL 변경**
   ```sh
   git remote set-url origin https://<YOUR_GITHUB_USERNAME>:<YOUR_PERSONAL_ACCESS_TOKEN>@github.com/Jakezo/Jakezo-docs.git
   ```
3. **Push 실행**
   ```sh
   git push origin main
   ```

---

### **🔹 2. Push 오류 해결 (`Updates were rejected because the remote contains work that you do not have locally.`)**
#### **방법 1: 변경 사항 커밋 후 Pull (권장)**
```sh
git add .
git commit -m "Save local changes before pull"
git pull origin main --rebase
git push origin main
```

#### **방법 2: 변경 사항 임시 저장 후 Pull**
```sh
git stash
git pull origin main --rebase
git stash pop
git push origin main
```

#### **방법 3: 변경 사항 삭제 후 강제 Pull**
🚨 **⚠️ 현재 작업한 변경 사항이 모두 삭제됩니다. 신중히 사용하세요.**
```sh
git reset --hard HEAD
git pull origin main --rebase
git push origin main
```

---

## **🚀 최종 정리**
1. **GitHub 레포지토리와 Writerside 프로젝트 연결**
2. **문서 빌드 후 `docs/` 폴더로 이동**
3. **GitHub Pages 활성화**
4. **Git Push 오류 발생 시 해결**
5. **GitHub Pages에서 문서 확인**

이제 Writerside 문서를 GitHub Pages에서 쉽게 배포하고 관리할 수 있습니다! 🎉
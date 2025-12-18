# AnyFive 업무관리 크롤러
AnyFive(office.anyfive.com)에서 업무관리 데이터를 크롤링하여 MariaDB에 저장하는 도구입니다.<br>
⭐자바 크롤링 코드는 [any_crawling](https://github.com/000Lee/any_crawling.git)에 있습니다.⭐<br>
이 페이지에는 파이썬 코드만 업로드되어 있으며, 이는 문서 ID 추출용 보조 스크립트입니다.

## 📋 목차

1. [개요](#개요)
2. [시스템 요구사항](#시스템-요구사항)
3. [환경 설정](#환경-설정)
   - [Java 설치](#1-java-jdk-설치)
   - [Python 설치](#2-python-설치)
   - [IDE 설치](#3-ide-설치)
   - [ChromeDriver 설치](#4-chromedriver-설치)
   - [MariaDB 설치](#5-mariadb-설치)
4. [프로젝트 설정](#프로젝트-설정)
   - [Java 프로젝트 생성](#java-프로젝트-생성-maven)
   - [Python 라이브러리 설치](#python-라이브러리-설치)
5. [데이터베이스 설정](#데이터베이스-설정)
6. [프로그램 설명](#프로그램-설명)
7. [사용 방법](#사용-방법)
8. [설정값 가이드](#설정값-가이드)
9. [문제 해결](#문제-해결)

---

## 개요

이 프로젝트는 4개의 프로그램으로 구성되어 있습니다:

| 순서 | 파일명 | 언어 | 역할 |
|:----:|--------|:----:|------|
| 1 | `업무관리 문서ID 가져오기.ipynb` | Python | 크롤링할 문서 ID 목록 추출 |
| 2 | `AnyFiveJnlCrawler.java` | Java | 문서 메타데이터 + 본문(+ 이미지 경로 변경) + 의견 저장 |
| 3 | `AnyFiveJnlCrawler_attaches.java` | Java | 첨부파일 다운로드 + 첨부파일 정보 저장 |
| 4 | `AnyFiveJnlCrawler_img.java` | Java | 본문 내 이미지 다운로드 |

### 실행 순서

```
[1단계] Python으로 문서 ID 추출
    ↓
[2단계] 추출된 ID를 Java 크롤러에 붙여넣기
    ↓
[3단계] 메인 크롤러 실행 (문서 정보 저장)
    ↓
[4단계] 첨부파일 크롤러 실행
    ↓
[5단계] 이미지 크롤러 실행
```

### 이슈
- AnyFiveJnlCrawler_plus.java는 AnyFiveJnlCrawler.java의 초기 코드에서 생긴 doc_body가 잘리는 이슈를 해결하기 위한 코드로 이에대한 추가 보완점들을 AnyFiveJnlCrawler에 적용하였으니 별도로 실행하지 않습니다.
- java 파일 중 AnyFiveJnlCrawler로 시작하는 파일은 전사업무보고 게시판용 입니다. 각각의 게시판마다 파일명이 다릅니다.<br>
pjt // PJT진척보고<br>
ip // IP솔루션팀<br>
tech // 기술지원팀<br>
biz // 경영기획팀<br>
자바 파일 제목, 첨부 및 이미지 저장 폴더, 테이블의 이름이 이와같이 적용되어있습니다.
- 각 게시판 코드는 AnyFiveJnlCrawler를 기반으로 하여 일부만 수정하였습니다. 
```
// 1. 게시판 선택
By.xpath("//li[@data-lblnm='전사업무보고']")
'전사업무보고'를 각 게시판에 맞게 변경하기

// 2. 테이블명
jnl_documents  →  새이름_jnl_documents
jnl_comments   →  새이름_jnl_comments

// 3. 폴더명 
jnl_img_       →  새이름_jnl_img_
jnl_attachments_ → 새이름_jnl_attachments_

// 4. 크롤링 대상 문서 ID 목록 부분에 각 게시판의 문서ID들을 붙여넣기

// 5. DB
CREATE TABLE 새이름_jnl_documents LIKE jnl_documents;
CREATE TABLE 새이름_jnl_comments LIKE jnl_comments;
```
- 누락문서 처리 <br>
업무관리/각 게시판 이름/업무관리 문서ID 가져오기.ipynb ->누락된문서 ID 찾기.ipynb-> 추출된 ID로 크롤링(자바).
- ⭐자바 크롤링 코드는 [any_crawling](https://github.com/000Lee/any_crawling.git)에 있습니다.⭐<br>
깃허브 any_crawling/src/main/java/org/example/<br>
전사업무보고<br>
AnyFiveJnlCrawler.java<br>
AnyFiveJnlCrawler_attaches.java<br>
AnyFiveJnlCrawler_img.java<br>
AnyFiveJnlCrawler_plus.java<br>
경영기획팀<br>
AnyFiveBizJnlCrawler.java<br>
AnyFiveBizJnlCrawler_attaches.java<br>
AnyFiveBizJnlCrawler_img.java<br>
IP솔루션팀<br>
AnyFiveIpJnlCrawler.java<br>
AnyFiveIpJnlCrawler_attaches.java<br>
AnyFiveIpJnlCrawler_img.java<br>
PJT진척보고<br>
AnyFivePjtJnlCrawler.java<br>
AnyFivePjtJnlCrawler_attaches.java<br>
AnyFivePjtJnlCrawler_img.java<br>
기술지원팀<br>
AnyFiveTechJnlCrawler.java<br>
AnyFiveTechJnlCrawler_attaches.java<br>
AnyFiveTechJnlCrawler_img.java
---

## 환경 설정

### 1. Java JDK 설치

1. [Oracle JDK 다운로드](https://www.oracle.com/java/technologies/downloads/) 또는 [OpenJDK 다운로드](https://adoptium.net/)
2. 설치 파일 실행 후 기본 설정으로 설치
3. 환경 변수 설정:
   - `JAVA_HOME`: JDK 설치 경로 (예: `C:\Program Files\Java\jdk-17`)
   - `Path`에 추가: `%JAVA_HOME%\bin`

4. 설치 확인:
```bash
java -version
javac -version
```

### 2. Python 설치

1. [Python 다운로드](https://www.python.org/downloads/)
2. 설치 시 **"Add Python to PATH"** 체크 필수!
3. 설치 확인:
```bash
python --version
pip --version
```

### 3. IDE 설치

#### Java용: IntelliJ IDEA (권장)
1. [IntelliJ IDEA Community Edition 다운로드](https://www.jetbrains.com/idea/download/)
2. 무료 버전(Community) 설치

#### Python용: VS Code 또는 Jupyter Notebook
- VS Code: [다운로드](https://code.visualstudio.com/)
- Jupyter: pip로 설치 (아래 참조)

### 4. ChromeDriver 설치

> ⚠️ **중요**: ChromeDriver 버전은 설치된 Chrome 브라우저 버전과 일치해야 합니다!

1. Chrome 버전 확인:
   - Chrome 브라우저 → 우측 상단 `⋮` → 도움말 → Chrome 정보
   - 버전 번호 확인 (예: 120.0.6099.130)

2. [ChromeDriver 다운로드](https://chromedriver.chromium.org/downloads)
   - Chrome 버전과 일치하는 ChromeDriver 다운로드
   - Chrome 115 이상: [Chrome for Testing](https://googlechromelabs.github.io/chrome-for-testing/)

3. 압축 해제 후 경로 기억 (예: `C:\Users\사용자명\Downloads\chromedriver-win64\chromedriver.exe`)

### 5. MariaDB 설치

1. [MariaDB 다운로드](https://mariadb.org/download/)
2. 설치 중 root 비밀번호 설정 (기억해두세요!)
3. 설치 완료 후 HeidiSQL (함께 설치됨) 또는 명령줄로 접속 테스트:

```bash
mysql -u root -p
```

---

## 프로젝트 설정

### Java 프로젝트 생성 (Maven)

1. IntelliJ IDEA 실행
2. `File` → `New` → `Project`
3. 좌측에서 `Maven` 선택
4. 프로젝트 이름 입력 후 `Create`

5. `pom.xml` 파일에 다음 의존성 추가:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>anyfive-crawler</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- Selenium WebDriver -->
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>4.15.0</version>
        </dependency>

        <!-- MariaDB JDBC Driver -->
        <dependency>
            <groupId>org.mariadb.jdbc</groupId>
            <artifactId>mariadb-java-client</artifactId>
            <version>3.3.0</version>
        </dependency>
    </dependencies>
</project>
```

6. IntelliJ 우측 상단의 Maven 아이콘 클릭 → `Reload All Maven Projects`

7. `src/main/java/org/example` 폴더에 Java 파일들 복사

### Python 라이브러리 설치

명령 프롬프트(cmd) 또는 터미널에서:

```bash
# Selenium 설치
pip install selenium

# Jupyter Notebook 설치 (선택사항)
pip install notebook

# Jupyter 실행
jupyter notebook
```

---

## 데이터베이스 설정

### 1. 데이터베이스 생성

HeidiSQL 또는 명령줄에서:

```sql
-- 데이터베이스 생성
CREATE DATABASE any_jnl CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 데이터베이스 선택
USE any_jnl;
```

### 2. 테이블 생성

```sql
-- 문서 테이블
CREATE TABLE jnl_documents (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    source_id VARCHAR(20) NOT NULL COMMENT '원본 문서 ID',
    end_year INT NOT NULL COMMENT '종료 연도 (분류용)',
    title VARCHAR(500) COMMENT '제목',
    work_box VARCHAR(200) COMMENT '업무함',
    work_type VARCHAR(100) COMMENT '업무 유형',
    writer_name VARCHAR(100) COMMENT '작성자',
    report_date BIGINT COMMENT '보고 일자 (밀리초 타임스탬프)',
    work_date VARCHAR(50) COMMENT '업무 일자',
    attaches TEXT COMMENT '첨부파일 JSON',
    doc_body LONGTEXT COMMENT '본문 HTML',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE KEY uk_source_year (source_id, end_year),
    INDEX idx_end_year (end_year),
    INDEX idx_report_date (report_date)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 의견(댓글) 테이블
CREATE TABLE jnl_comments (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    post_source_id VARCHAR(20) NOT NULL COMMENT '문서 source_id',
    end_year INT NOT NULL COMMENT '종료 연도',
    writer_name VARCHAR(100) COMMENT '작성자',
    created_at BIGINT COMMENT '작성 일자 (밀리초 타임스탬프)',
    content TEXT COMMENT '의견 내용',
    
    INDEX idx_post_source (post_source_id),
    INDEX idx_end_year (end_year)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

## 프로그램 설명

### 1. 업무관리 문서ID 가져오기.ipynb (Python)

**목적**: 특정 기간의 문서 ID를 자동으로 추출

**동작 방식**:
1. Chrome 브라우저 자동 실행
2. 사용자가 수동으로 로그인 및 필터 설정
3. 목록에서 문서 ID를 자동 추출
4. 페이지 넘김을 반복하며 모든 ID 수집
5. 텍스트 파일로 저장 (예: `"3630714","3630715","3630716"`)

**출력 형식**: Java 코드에 바로 붙여넣기 가능한 형태

---

### 2. AnyFiveJnlCrawler.java (메인 크롤러)

**목적**: 문서의 메타데이터, 본문, 의견을 DB에 저장

**추출 데이터**:
| 필드 | 설명 |
|------|------|
| source_id | 문서 고유 ID |
| title | 제목 |
| work_box | 업무함 (전사업무보고 등) |
| work_type | 업무 유형 |
| writer_name | 작성자 |
| report_date | 보고 일자 |
| work_date | 업무 일자 |
| doc_body | 본문 HTML (이미지 경로 변환됨) |

**의견(댓글)도 함께 추출하여 `jnl_comments` 테이블에 저장**

**동작 방식**:
1. 자동 로그인
2. 업무관리 → 회사 업무함 → 전사업무보고 진입
3. `TARGET_DOCUMENT_IDS` 배열의 각 ID에 대해:
   - 상세 페이지 로드
   - 데이터 추출
   - DB 저장
   - 목록으로 복귀
4. 이미 처리된 ID는 자동 스킵

---

### 3. AnyFiveJnlCrawler_attaches.java (첨부파일)

**목적**: 문서에 첨부된 파일 다운로드

**동작 방식**:
1. DB에서 `attaches`가 비어있는 문서 조회
2. 각 문서의 상세 페이지에서 첨부파일 목록 추출
3. HTTP 요청으로 파일 다운로드
4. DB의 `attaches` 필드에 JSON 형태로 경로 저장

**저장 위치**: `{DOWNLOAD_BASE_DIR}/jnl_attachments_{연도}/jnl{문서ID}/`

**attaches JSON 예시**:
```json
[
  {"name": "보고서.xlsx", "path": "/jnl_attachments_2023/jnl3630714/보고서.xlsx"},
  {"name": "참고자료.pdf", "path": "/jnl_attachments_2023/jnl3630714/참고자료.pdf"}
]
```

---

### 4. AnyFiveJnlCrawler_img.java (이미지)

**목적**: 본문에 삽입된 이미지 다운로드

**동작 방식**:
1. DB에서 해당 연도의 모든 문서 ID 조회
2. `processed_ids.txt`로 처리 완료 여부 관리
3. 각 문서 상세 페이지의 본문에서 `<img>` 태그 추출
4. URL 이미지 또는 Base64 이미지 다운로드
5. 순서대로 `0.jpg`, `1.jpg`, ... 저장

**저장 위치**: `{DOWNLOAD_BASE_DIR}/jnl_img_{연도}/jnl{문서ID}/`

> 💡 메인 크롤러에서 본문의 이미지 경로가 이미 이 형식으로 변환되어 저장됩니다.

---

## 사용 방법

### Step 1: 문서 ID 추출 (Python)

1. Jupyter Notebook 실행:
```bash
jupyter notebook
```

2. `업무관리 문서ID 가져오기.ipynb` 열기

3. 설정값 수정:
```python
START_DATE = "2023-01-01"  # 시작일
END_DATE = "2023-12-31"    # 종료일
OUTPUT_FILE = "task_ids_2023.txt"  # 출력 파일명
```

4. 셀 실행 (Shift + Enter)

5. 브라우저가 열리면:
   - 로그인
   - 업무관리 → 원하는 메뉴 선택
   - 날짜 필터 설정
   - 조회 버튼 클릭
   - 목록이 보이면 터미널에서 Enter

6. 자동으로 모든 페이지의 ID 추출 후 파일 저장

7. 생성된 파일 내용 복사 (예: `"3630714","3630715","3630716"`)

---

### Step 2: 메인 크롤러 실행 (Java)

1. `AnyFiveJnlCrawler.java` 열기

2. 설정값 수정:
```java
// ChromeDriver 경로
private static final String WEB_DRIVER_PATH = "C:/Users/사용자명/Downloads/chromedriver-win64/chromedriver.exe";

// DB 연결 정보
private static final String DB_URL = "jdbc:mariadb://localhost:3306/any_jnl";
private static final String DB_USER = "root";
private static final String DB_PASSWORD = "비밀번호";

// 대상 연도
private static final int END_YEAR = 2023;

// 문서 ID 목록 (Step 1에서 복사한 내용 붙여넣기)
private static final String[] TARGET_DOCUMENT_IDS = {
    "3630714","3630715","3630716"  // ← 여기에 붙여넣기
};
```

3. 실행 (IntelliJ: 우클릭 → Run)

4. 진행 상황 콘솔에서 확인

---

### Step 3: 첨부파일 다운로드 (Java)

1. `AnyFiveJnlCrawler_attaches.java` 열기

2. 설정값 수정:
```java
private static final String WEB_DRIVER_PATH = "...";  // ChromeDriver 경로
private static final String DB_URL = "...";
private static final String DB_USER = "root";
private static final String DB_PASSWORD = "비밀번호";
private static final int END_YEAR = 2023;
private static final String DOWNLOAD_BASE_DIR = "C:/Users/사용자명/Downloads";
```

3. 실행

---

### Step 4: 이미지 다운로드 (Java)

1. `AnyFiveJnlCrawler_img.java` 열기

2. 설정값 수정 (위와 동일)

3. 실행

---

## 설정값 가이드

### 공통 설정

| 설정 | 설명 | 예시 |
|------|------|------|
| `WEB_DRIVER_PATH` | ChromeDriver 실행 파일 경로 | `C:/Users/.../chromedriver.exe` |
| `DB_URL` | MariaDB 연결 URL | `jdbc:mariadb://localhost:3306/any_jnl` |
| `DB_USER` | DB 사용자명 | `root` |
| `DB_PASSWORD` | DB 비밀번호 | `1234` |
| `END_YEAR` | 처리 대상 연도 (분류용) | `2023` |

### 메인 크롤러 전용

| 설정 | 설명 |
|------|------|
| `TARGET_DOCUMENT_IDS` | 크롤링할 문서 ID 배열 |
| `DB_PATH_PREFIX` | DB에 저장될 이미지 경로 접두사 (보통 빈 문자열) |
| `MAX_RETRY` | 문서당 최대 재시도 횟수 |

### 첨부파일/이미지 크롤러 전용

| 설정 | 설명 |
|------|------|
| `DOWNLOAD_BASE_DIR` | 파일 저장 기본 경로 |

---

## 문제 해결

### ChromeDriver 버전 불일치

**증상**: `SessionNotCreatedException` 오류

**해결**:
1. Chrome 브라우저 버전 확인
2. 동일 버전의 ChromeDriver 다운로드
3. `WEB_DRIVER_PATH` 경로 업데이트

### DB 연결 실패

**증상**: `SQLException: Access denied`

**해결**:
1. MariaDB 서비스 실행 중인지 확인
2. 사용자명/비밀번호 확인
3. 데이터베이스 존재 여부 확인

### 문서 로드 타임아웃

**증상**: `TimeoutException`

**해결**:
1. 네트워크 상태 확인
2. `WebDriverWait` 시간 늘리기 (현재 20초)
3. 해당 문서 ID가 실제로 존재하는지 확인

### 이미지 다운로드 실패

**증상**: `download_errors.log`에 오류 기록

**해결**:
1. 해당 문서 `processed_ids.txt`에서 제거
2. 크롤러 재실행 (자동으로 재시도)

### 한글 깨짐

**증상**: DB에 저장된 한글이 깨져 보임

**해결**:
1. DB 문자셋 확인: `SHOW CREATE DATABASE any_jnl;`
2. `utf8mb4` 아니면 재생성:
```sql
ALTER DATABASE any_jnl CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

---

## 폴더 구조 예시

크롤링 완료 후 생성되는 폴더 구조 예시:

```
C:\Users\사용자명\Downloads\
├── jnl_attachments_2023\
│   ├── jnl3630714\
│   │   ├── 보고서.xlsx
│   │   └── 참고자료.pdf
│   └── jnl3630715\
│       └── 회의록.docx
│
└── jnl_img_2023\
    ├── jnl3630714\
    │   ├── 0.jpg
    │   ├── 1.jpg
    │   └── 2.jpg
    ├── jnl3630715\
    │   └── 0.jpg
    ├── processed_ids.txt
    └── download_errors.log
```

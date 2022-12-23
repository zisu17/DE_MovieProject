# 영화 산업 흥행 요인 분석을 위한 데이터 파이프라인 구축
🏃 Data Engineer 이서정(팀장), 손지수, 유승종, 이상엽, 정현진

### **📖 Contents**  

1️⃣ [프로젝트 개요](#-프로젝트-개요)   
2️⃣ [데이터 파이프라인](#-데이터-파이프라인)  
3️⃣ [데이터 프로덕트](#-데이터-프로덕트)  
4️⃣ [마무리](#-기대효과-및-향후-계획)  

<br>

## 🍎 프로젝트 개요

### ✔️ 프로젝트 기간
 2022-09-26~ 2022-10-07 (총 12일)

### ✔️ 주제 선정 배경
보통 영화를 볼때 좋은 배우, 감독을 보고 영화가 흥행할지 아닐지 판단하곤 합니다. 하지만 좋은 배우, 감독이 참여했음에도 흥행하지 못한 사례들을 볼 수 있습니다. 저희 팀은 이를 보고 영화 내적, 외적인 요인들이 흥행에 어떤 영향을 끼치는지 파악해보고자 합니다.

### ✔️ 프로젝트 주제
▶ 영화 산업 흥행 요인 분석을 위한 데이터 파이프라인 구축  
: 개봉일의 흥행성과가 영화의 최종 흥행에 미치는 영향을 중점으로

### ✔️ 수집 데이터
- 영화 진흥 위원회 일별 박스오피스 API : 영화코드, 순위, 매출, 관객수 등등
- 영화 진흥 위원회 영화 상세정보 API : 영화명, 장르, 개봉일 등등
- 네이버 검색어 트렌드 API : 영화 검색 비율
- 네이버 검색 영화 API : 네이버 영화 URL, 출연 배우
- 네이버 영화 평점 정적크롤링 : 네티즌/ 관람객/ 전문가 평점

### ✔️ 협업에 사용한 툴
  - slack 
  - trello : https://trello.com/b/w9hAewpb/de3%EC%A1%B0
  - docker : https://hub.docker.com/u/seojeon9

### ✔️ 기술 스택
#### ETL 파이프라인
  - Data Lake : <img src="https://img.shields.io/badge/Hadoop-66CCFF?style=flat-square&logo=apachehadoop&logoColor=black"> 
  - Data Warehouse, Data Mart : <img src="https://img.shields.io/badge/Oracle ATP-F80000?style=flat-square&logo=oracle&logoColor=white">
  - 데이터 가공 및 분산처리엔진 : <img src="https://img.shields.io/badge/Spark-E25A1C?style=flat-square&logo=apachespark&logoColor=white">
  - 배치도구 : <img src="https://img.shields.io/badge/Airflow-017CEE?style=flat-square&logo=apacheairflow&logoColor=black">
#### REST-API Server
  - Web Framework : <img src="https://img.shields.io/badge/Django-092E20?style=flat-square&logo=django&logoColor=white">
#### BI
  - 언어 : <img src="https://img.shields.io/badge/html5-E34F26?style=flat-square&logo=html5&logoColor=white"> 
    <img src="https://img.shields.io/badge/css-1572B6?style=flat-square&logo=css3&logoColor=white"> 
    <img src="https://img.shields.io/badge/javascript-F7DF1E?style=flat-square&logo=javascript&logoColor=black"> 
  - 프레임워크 : <img src="https://img.shields.io/badge/bootstrap-7952B3?style=flat-square&logo=bootstrap&logoColor=white">

### ✔️ 데이터 처리 프로세스
- ETL 파이프라인 
- Data Mart
- REST-API
- BI

<br>
<br>

## 🚀 데이터 파이프라인
### ✔️ Data Lake (추출)
- 도커 컨테이너 위에 하둡을 얹어 Data Lake로 사용하며 필요한 데이터 추출합니다.
<br>

![image](https://user-images.githubusercontent.com/72624263/194675149-534b2751-2de2-4bd2-90cf-30143694e1c7.png)

<br>

### ✔️ 가공
- HDFS에 추출한 Json파일을 spark를 사용하여 구축해 놓은 데이터베이스에 맞게 가공합니다.

![image](https://user-images.githubusercontent.com/72624263/194675199-9b679007-a5cf-4d3b-802f-dbbf94ab073e.png)

<br>

### ✔️ ERD
- 데이터 웨어하우스
  - 데이터 웨어하우스의 테이블을 설계할때 중복값 없이 이상현상이 생기지 않게 저장해야하므로 3정규화까지 고려하여 진행
  - 영화 상세정보의 경우 영화 한편에 대하여 장르가 멜로/로맨스와 코미디 두 가지로 제 1정규형에 위반
  - 이런 값들을 처리하기 위해 MOVIE_DETAIL과 MOVIE_GENRE, MOVIE_COMPANY와 같이 테이블을 나눠 일대다의 관계로 테이블을 설계

![image](https://user-images.githubusercontent.com/72624263/194675298-529e69d1-524d-43cf-aade-ad4eefce6461.png)

<br>

![image](https://user-images.githubusercontent.com/72624263/194676044-b9063fbe-7385-4c80-bed0-d70ac38c1031.png)

<br>


- 데이터 마트
  - 분석에 사용할 데이터를 적재할 데이터마트는 각 요인에 따라 테이블을 나누고 데이터가 중복이 되더라도 하나의 테이블에서 원하는 정보를 다 얻을 수 있도록 설계
  - 데이터 마트의 통계 데이터로는 평균과 증가율 사용 
  - 자료조사를 통해 총 관객수를 기준으로 A부터 F까지 영화의 등급을 매겨서 테이블의 컬럼으로 추가하고 각 요소들간의 상관분석 진행

![image](https://user-images.githubusercontent.com/72624263/194676150-77148ee0-53c3-4641-9806-027cf7dd2ea1.png)

<br>

### ✔️ Airflow
- 매일 새벽 airflow를 돌려 ETL 과정을 자동화
- 순차적으로 진행해야하는 스크립트와 spark session을 사용해야하는 transform의 경우 직렬처리하여 구성
- 마지막 Data mart는 병렬처리로 구성

![image](https://user-images.githubusercontent.com/72624263/194676491-683b4f9b-f5ff-49c4-8aae-0344e7525a8e.png)

<br>
<br>

## 🎯 데이터 프로덕트
### ✔️ REST-API
- 데이터를 제공하는 백엔드와 로그인정보 세션 등을 관리하는 프론트 서버로 이루어진 분리구조로 구성
- 토큰기반 인증을 통한 보안설정
- 백엔드 서버에서 데이터베이스의 데이터를 직접 제공하는 것이 아닌 JSON형식으로 제공

![image](https://user-images.githubusercontent.com/108858121/209343987-496a3280-5f33-44cd-b74f-e8e522c4ce6b.png)

<br>

### ✔️ BI
- plotly.js 프레임워크를 이용하여 데이터를 시각화
- 각 등급별로 개봉일의 요소들과 총 관객수의 상관관계를 확인할 수 있는 웹페이지 구현

![image](https://user-images.githubusercontent.com/72624263/194677120-30ace656-a29a-4bfe-981d-54b4f5afa4e3.png)
![image](https://user-images.githubusercontent.com/72624263/194677137-f6b2e75c-5565-4022-9dcd-77524089be36.png)
![image](https://user-images.githubusercontent.com/72624263/194677195-90209ad3-ac21-45e5-8b07-b831a1ee1197.png)
<br>
<br>

## 🍏 마무리
### ✔️ 결과
개봉일의 관객수, 스크린수, 상영횟수, 매출점유율 등 개봉일의 흥행성과가 최종 흥행에 유의했습니다. 하지만 관람객 평점, 전문가 평점 등의 영화의 질을 반영하는 변수는 최종 흥행에 영향을 미친다고 보기 어렵다는 것을 확인했습니다.
### ✔️ 향후계획
처음 기획단계에서 영화 흥행에서 코로나의 영향이 궁금해 코로나 전 개봉한 영화까지 다루고자 4년치 데이터를 목표했습니다. 하지만 일일 트래픽 제한으로 인해 코로나 전까지의 데이터는 수집을 하지 못했습니다. 그래서 이후에 시간적 여유를 가지고 꾸준히 데이터를 수집하여 목표했던 양의 데이터를 처리해볼 계획입니다. 








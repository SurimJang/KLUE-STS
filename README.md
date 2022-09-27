# 한국어 문장의 의미적 텍스트 유사도 분석 
*KLUE-STS Benchmark Dataset을 이용하여*

## 01 분석 정의

<img width="735" alt="readme1-1" src="https://user-images.githubusercontent.com/18377883/192146538-56ddd4cb-c89e-4644-9fc6-cddfc4c11391.png">

#### `STS`(Sementic Textual Similarity): 텍스트의 `의미적 유사도`를 평가하는 NLU Task

예시와 같이, 통사적으로 다르지만 의미적으로는 같은 문장을 기계가 더 잘 이해한다면 서비스의 효율이 제고될 것이다.

* Metric 선정의 관건은 무엇일까?
    * 모델이 두 문장의 **의미적 친밀도**를 얼마나 잘 잡아내는지
    * 모델이 문장의 **의미적 표현**을 얼마나 잘 구현하는지
    ⇒ F1 Score, Pearson' R 사용

---
## 02 데이터셋 개요

#### `KLUE-STS`: 리뷰, 기사, 스마트홈 디바이스의 발화 코퍼스를 가공한 한국어 의미 유사도 데이터셋

문장 쌍은 꼬꼬마 기준 약 14개의 토큰으로 구성되며, 이는 KorSTS(kakao brain)에 비해 두 배 정도 긴 분량이다.
의미적 유사도를 나타내는 레이블은 복수의 작업자에 의해 측정되며, 0 (상이함) <--> 5 (동등함)의 척도를 갖는다.

<!--
GitHub-Flavored Markdown의 테이블 병합 지원 불가로 이미지로 대체함. 아래 참조.
https://github.github.com/gfm/#tables-extension-
-->
<img width="737" alt="스크린샷 2022-09-25 오후 11 08 10" src="https://user-images.githubusercontent.com/18377883/192147926-b9c6d1a3-9383-4014-8e5c-4b1eb063e8ac.png">

---
## 03 분석 로드맵

<img width="817" alt="스크린샷 2022-09-26 오후 11 25 18" src="https://user-images.githubusercontent.com/18377883/192301935-bac4071a-48b2-4459-bd79-7fd43baf714f.png">

---
## 04 데이터셋 탐색

#### :one: 텍스트 정제 
KLUE-STS는 이미 Copora 정제를 아래와 같이 수행  
⇒  **별도의 텍스트 전처리 과정은 생략하자**

* `Noise Filtering`  
한국어가 아닌 텍스트, 해시태그(#), HTML태그, 빈 괄호(), 연속 공백, 기자 및 언론 정보, 이미지, 출처에 대한 정보, 저작권 태그 제거
* `Toxic Content Removal`  
Korean hate speech 데이터셋을 활용한 성별 편향과 혐오 발언 탐지 훈련으로 유해 콘텐츠 제거
* `PII Removal`  
개인정보가 포함된 텍스트를 제거. 공인으로 간주되지 않는 개인정보(이름, 주민등록번호, 전화번호, 은행 계좌 등), 이메일 주소, URL, @name 등의 텍스트를 정규표현식으로 제거

#### :two: 레이블 불균형

<img width="319" alt="스크린샷 2022-09-26 오후 11 30 10" src="https://user-images.githubusercontent.com/18377883/192303004-c6160d85-06ad-4a96-8e66-e1f73fe82809.png">

0 ~ 1점 대는 지나치고 많고, 1 ~ 3점 대는 적다  
⇒  **데이터 증강 기법을 이용해 불균형을 개선하자**

---
## 05 전처리: 데이터 증강

데이터 증강 기법의 합리적 선정을 위해, 증강 과정에서 의미 유사성을 지킬 수 있는 방법인지를 중점적으로 고려하자

#### :one: EDA(Easy Data Augmentation) Random Swap
: 문장 중 임의의 두 단어 위치를 바꾸어 문장 생성

<img width="305" alt="스크린샷 2022-09-27 오후 9 14 18" src="https://user-images.githubusercontent.com/18377883/192525160-df22a72e-c515-452e-8d1a-f3c43b066bfd.png">

`가설` | 한국어는 영어에 비해 <u>어순에 의미 의존</u>이 적으므로, 데이터 증강 시 모델의 예측 성능을 높일 것이다.  
`적용` | ko-EDA (alpha=0.1, num_aug=4)  ⇒  비교군에 비해 f1 score -0.014 하락  

#### :two: Back Translation
: 타겟 언어를 타국어로 번역한 후에 다시 타겟 언어로 번역한 문장 생성

`가설` | 역번역 과정에서 <u>토큰의 의미가 보존</u>되어, 데이터 증강 시 모델의 예측 성능을 높일 것이다.  
`적용` | pororo (ko > en > ko) ⇒  비교군에 비해 f1 score  -0.16 하락  

* 왜 데이터증강이 효과적이지 않았는가?  
Confusion matrix 살펴보니, 오히려 타겟 레이블이 예측을 잘 수행했음. 모델이 무얼 잘 예측할지 파악해 전략적으로 증강 기법 적용해야함.



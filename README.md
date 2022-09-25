# 한국어 문장의 의미적 텍스트 유사도 분석 
: KLUE-STS Benchmark Dataset을 이용하여

## 01 분석 정의

![readme1-1](/img/readme1-1.png)

**STS**(Sementic Textual Similarity): **텍스트의 의미적 유사도**를 평가하는 NLU Task

예시와 같이, 통사적으로 다르지만 의미적으로는 같은 문장을 기계가 더 잘 이해한다면 서비스의 효율이 제고될 것

* Metric 선정의 관건은 무엇일까?
    * 모델이 두 문장의 **의미적 친밀도**를 얼마나 잘 잡아내는지
    * 모델이 문장의 **의미적 표현**을 얼마나 잘 구현하는지
    ⇒ F1 Score, Pearson' R 사용

---
## 02 데이터셋 개요

**KLUE-STS**: 리뷰, 기사, 스마트홈 디바이스의 발화 코퍼스를 가공한 한국어 의미 유사도 데이터셋
              문장 쌍은 꼬꼬마 기준 약 14개의 토큰으로 구성되며, 이는 KorSTS(kakao brain)에 비해 두 배 정도 긴 분량이다.
              의미적 유사도를 나타내는 레이블은 복수의 작업자에 의해 측정되며, 0(상이함) <--> 5(동등함)의 척도를 갖는다.
| KLUE-STS | Train | Dev |
| :------: | :---: | :-: |
| Source | Airbnb (Review), Policy (News), ParaKQC (Smart home Query) ||
| # Examples | 11,668 | 519 |
| Avg. # tokens | 14.5 | 14.1 |
[표1] Summary of KLUE-STS Datasets



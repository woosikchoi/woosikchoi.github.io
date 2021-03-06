---
title: "Naive Bayes Classification"
date: 2020-05-25 18:00
categories: MachineLearning
---

개별 변수 (데이터 속성) 사이의 독립을 가정하는 베이즈 정리 (Bayes' Theorem) 를 적용한 확률 기반 분류기의 일종이다.
조건부 확률 모형이며 이는 수학적인 표현을 통하여 직관적으로 이해 및 해석이 가능하다.
현재는 머신러닝의 다른 분류 모델 (Random Forest, Gradient Boost, etc.) 에 비해 사용성은 적지만
간단한 디자인과 단순 가정에도 불구하고 많은 복잡한 실제 상황에서 잘 작동한다.
나이브 베이즈 분류를 이해하기 위해서는 베이즈 정리에 대해 우선적으로 이해할 필요가 있다.

## Bayes' Theorem
확률론 및 통계학에서 두 확률 변수의 사전 확률과 사후 확률 사이의 관계를 나타내는 정리이다. 
즉, P(A|B)를 알고 있을 때, 확률 P(B|A)를 계산하기 위한 정리로 이해하면 될 것이다. 수식으로 나타내면 아래와 같다.

<img src="https://latex.codecogs.com/svg.latex?P(B|A)=\frac{P(A%20\cap%20B)}{P(A)}=\frac{P(B)P(A|B)}{P(A)}">

- <img src="https://latex.codecogs.com/svg.latex?P(A)"> : A의 사전 확률 (evidence) - 현재의 증거
- <img src="https://latex.codecogs.com/svg.latex?P(B)">: B의 사전 확률 (prior probability) - 과거의 경험
- <img src="https://latex.codecogs.com/svg.latex?P(A|B)">: 사건 B가 주어졌을 때의 A의 조건부 확률 (likelihood) - 알려진 결과에 기초한 어떤 가설에 대한 가능성
- <img src="https://latex.codecogs.com/svg.latex?P(B|A)">: 사건 A라는 증거에 대한 사후 확률 (posterior probability) - 사건 A가 일어났다는 것을 알고, 그것이 사건 B로부터 일어난것이라고 생각되는 조건부 확률

위의 수식은, 조건부 확률의 정의 및 수학적 표현을 통해 간단하게 증명 가능하다.

## Example of Bayes' Theorem

**Q.** 암은 전체 인구의 1%가 걸리는 병이고, 암 검사의 적중률은 95%이다.  '갑'이 암검사를 실시했는데 '갑'의 암 검사 결과는 양성이었다. '갑'이 실제 암에 걸렸을 확률은?

**A.** '갑'이 실제 암에 걸렸을 확률은 약 16%임

- A: 암 검사 결과의 양성인 사건

- B: 실제 암에 걸렸을 사건 - <img src="https://latex.codecogs.com/svg.latex?P(B)=0.01">

- <img src="https://latex.codecogs.com/svg.latex?P(A|B)=0.95">
- <img src="https://latex.codecogs.com/svg.latex?P(B|A)">: 검사 결과가 양성인 사람이 실제 암에 걸렸을 확률 ('갑'이 암일 확률)
<img src="https://latex.codecogs.com/svg.latex?P(B|A) = \frac{P(B) P(A|B)}{P(A)}">

<img src="https://latex.codecogs.com/svg.latex?=\frac{P(B) P(A|B)}{P(A \cap B)+P(A \cap B^{C})} = \frac{P(B) P(A|B)}{P(A|B)P(B)+P(A|B^{C})P(B^C)}">

<img src="https://latex.codecogs.com/svg.latex?=\frac{0.01 \times 0.95}{0.95 \times 0.01 + 0.05 \times 0.99} \simeq 0.16">
  
**Interpretation**

암 검사 적중률이 95%임에도 불구하고, 실제 암에 걸렸을 확률은 16%에 불과하다. 이는 암 발병률 자체가 매우 희귀하다. 따라서 99%의 암에 걸리지 않은 사람에 대해서도 암 검사가 양성이 나올 수 있는 확률이 고려되었기 때문이다. 실제 decision tree 기반으로 아래 그림과 같이 그림을 그려보면 조금 더 직관적인 이해가 가능하다.

<img src='https://cdn-std.droplr.net/files/acc_503911/1S5BX6'>

통상적으로 위의 그림의 빨간색 부분을 간과하는 경우가 많이 있다. 특히, 건강한 사람 중에 암 검사가 양성으로 나오는 경우도 4.95%에 달한다. 이러한 부분을 고려한 확률 계산이 필수적이다. 

## Applications

Naive Bayes 분류의 경우에 아래와 같은 경우에 실제로 많이 활용되어져 왔다.

- **Spam mail filtering 등과 같은 텍스트 분류**
- Network의 비정상 행위 탐지 분류
- 의학적 질병 진단 등

Bayes' Theorem에 기초한 naive한 (소박한, 간단한, 순진한) 알고리즘이라고 하여 Naive Bayes라는 이름이 붙여졌지만 이에 대한 활용도는 매우 높으며, 머신러닝의 이해 / 해석에 있어서 매우 중요한 이론이자 알고리즘이다. 아래는 해당 알고리즘의 활용방안에 대한 실제적 활용을 위하여 Spam mail filtering의 naive-bayes 이론 기반 적용 방안을 설명하고자 한다.

- Objectives

  - 지금까지 받은 메일이 수 천개가 있음
  - 메일은 단어 (word) 의 조합으로 이루어져 있음
  - E-mail의 단어 조합을 추출하고, 해당 단어들의 분석을 통하여 Spam mail의 가능성을 측정
  - Spam mail 가능성 기반으로 mail을 분류

- Assumption (Probability)

  - 74개 중 30개의 이메일은 스팸 메시지 (*A*)
  - 74개의 메일 중 51개의 이메일은 test 단어를 포함 (*B*)
  - Test 단어가 들어가 있는 20개의 이메일을 스팸으로 분류 (<img src="https://latex.codecogs.com/svg.latex?P(B|A)">)

- 최근 수신한 메일에 test가 포함되어있을 경우 해당 메일이 spam일 확률 (<img src="https://latex.codecogs.com/svg.latex?P(A|B) = 0.39">)

  - <img src="https://latex.codecogs.com/svg.latex?P(A) = \frac{30}{74}">
  - <img src="https://latex.codecogs.com/svg.latex?P(A) = \frac{51}{74}">
  - <img src="https://latex.codecogs.com/svg.latex?P(A) = \frac{20}{30}">

<img src="https://latex.codecogs.com/svg.latex?P(A|B) = \frac{P(B|A)P(A)}{P(B)} = \frac{2/3*30/74}{51/74} = 20 / 51 \simeq = 0.39">
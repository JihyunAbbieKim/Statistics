---
typora-copy-images-to: ./images
---

**연구 질문**: MASH(대사 기능 장애 관련 지방간염)로 인해 보상성 간경변(Compensated Cirrhosis)이 있는 비만 환자에게 '대사 수술'을 시행했을 때, 수술하지 않은 경우보다 '주요 간 질환 합병증(MALO)' 발생 위험을 장기적으로 낮출 수 있는가?



**Metabolic surgery**

\- 위 절제술을 포함하는 수술. 과거에는 비만 수술이라고 불렸지만 요즘은 당뇨병이나 고혈압같은 대사 질환을 고치는 효과가 더 주목받는중.

\- Roux-en-Y gastric bypass와 sleeve gastrectomy을 포함하는 개념.

- 루와이 위우회술 (Roux-en-Y Gastric Bypass): 위를 작게 남기고 소장과 직접 연결해서 음식이 덜 흡수되게 만드는 수술
- 위소매절제술 (Sleeve Gastrectomy): 위를 바나나 모양으로 길게 잘라내는 수술. 



**Doubly robust estimation**

1.  **Overlap weighting**

- Overlap weighting performed using confounders

전통적인 IPW(Inverse Probability Weighting)가 전체 인구 집단을 대표하려고 한다면, 

Overlap weighting은 치료를 받을 수도, 안 받을 수도 있었던 구간에 집중

- **성향 점수 ($e_i$):** 혼재변수 X를 통해 계산된 특정 환자가 치료군(Z=1)에 속할 확률
- **가중치 ($w_i$) 공식:**
  - **치료군 ($Z=1$):** w_i = 1 - e_i (대조군에 속할 확률을 가중치로 부여)
  - **대조군 ($Z=0$):** w_i = e_i (치료군에 속할 확률을 가중치로 부여)

이렇게 하면 성향 점수가 0.5에 가까운(두 군의 특징을 모두 가진) 사람들의 가중치가 가장 커지고, 극단적인 값(0 또는 1)을 가진 사람들의 영향력은 작아짐

- After overlap weighting, a single individual no longer represents a single data entity!



2. **time-to-event regression models**

- Firth’s penalized method in a fully adjusted Cox proportional hazard framework: adjusting models for sex, race, FIB-4, smoking status, CCI, insulin use and lipid-lowering medications



**=> 왜 이름이 Doubly Robust estimation인가요?**

**1. Overlap Weighting(OW)**

- 로지스틱 회귀의 점수 방정식(Score Equation)

$$
\sum (Z_i - \hat{e}_i) X_{ij} = 0
$$
  

  에 의해, 가중치가 적용된 두 군의 변수 평균은 산술적으로 정확히 일치한다.


$$
\sum_{i=1}^{n} Z_i w_i X_{ij} = \sum_{i=1}^{n} (1 - Z_i) w_i X_{ij}
$$
  

  > **정리**: 이 과정을 통해 ATO(Average Treatment effect in the Overlap population)를 추정하기 위한 Pseudo-population가 생성되며, 가중치 적용 후 두 그룹은 주요 혼란변수 X에 대해 이론적으로 완벽한 균형을 이룸.

------

**2. Weighted Regression 모델**

가중치를 준 상태에서 한 번 더 회귀 분석을 돌리는 이유는?

$$
Y = \beta_0 + \beta_{surgery} Z + \sum \gamma_j X_j + \epsilon
$$

$$
\beta_{surgery} :\,Target Estimand
$$

> 가중치가 적용된 상태에서 추출된 이 계수는 모든 혼란변수가 통제된 상태에서의 수술의 순수한 효과를 의미함

$$
\sum \gamma_j X_j:\,Residual \,Confounding \,Control
$$

>  비록 가중치(w_i)가 평균적인 균형을 맞췄더라도, 개별 데이터 레벨에서 남아있을 수 있는 잔여 불균형(Residual imbalance)이나 비선형적 편향을 이 항이 수치적으로 흡수하여 제거함

$$
\epsilon:\,Variance \,Reduction
$$

모델에 X_j를 포함함으로써 결과값 Y의 변동성 중 상당 부분을 설명해낼 수 있습니다. 이는 오차항의 크기, 즉 잔차 제곱합(RSS)을 줄여줌
$$
\text{Var}(\hat{\beta}_{surgery}) \propto \text{Var}(\epsilon)
$$
결과적으로 수술 효과의 분산이 작아져 표준 오차(SE)가 감소하고, 통계적 검정력(Power)이 향상됨

------

**3. 결론: 이중 강건성(Doubly Robust)의 완성**

이 두 가지를 병합하는 이유는 "모델 오설정(Model Misspecification)"에 대비하기 위함임.

1. **PS 모델($w_i$)이 맞거나,**
2. **결과 모델(β, γ)이 맞거나,**

둘 중 하나만 정확해도 편향되지 않은(Unbiased) 추정치를 얻을 수 있음. 따라서 동일한 변수 X를 양쪽에 모두 사용하는 것은 중복 낭비가 아니라, 추정의 정확성을 극대화하고 결론의 강건성(Robustness)을 확보하기 위한 필수적인 통계적 장치인 것!



**Cofounding variables**

\- statistical adjustment

\- unadjusted and adjusted absolute risk reduction ← Kaplan–

Meier estimates of cumulative incidence ← adjusted Cox models

\- number needed to treat 

\- E-value



**결측치 처리**

\- five imputation datasets 만들어서 결과 비교(결측치 차원의 민감도 분석 느낌이군..!) → 5개의 HR과 표준오차를 합쳐서 결측으로 인한 불확실성이 보정된 HR과 CI 산출(Rubin's rule)

\- Predictive Mean Matching (PMM)



두 집단의 Kaplan-Meier graph가 그려지면 꼭 p-value가 나옴

"P value is from the cox proportional hazard model." ; 정규분포 하에서

Cf) Log-rank test의 p-value; Chi-square 분포 하에서

**검정 통계량의 차이 **

$$
(\chi^2  vs. Z)
$$

- **Log-rank Test:** 각 시점마다 "사건이 일어날 것으로 예상되는 수"와 "실제 일어난 수"를 비교 → 이 차이의 제곱을 합산하는 방식이라 자연스럽게 **카이제곱  분포**를 따르게 됨
- **Cox Model:** 회귀 계수(β)를 추정하고, 이 값이 0인지 아닌지를 검정 → HR은 MLE로 추정되기 때문에 MLE의 특징 상 추정치가 정규분포(Z)에 수렴한다는 성질을 이용합니다.



**결과를 고시할 때:** 

\- cumulative incidence of treatment group and control group

\- unadjusted absolute risk difference

\- adjusted risk difference w/ aHR & p-value



**한계**

**1. 선택편향**

이를 정당화하는 brilient한 방법

선택 편향(selection bias)의 위험이 있었으나 간 생검 시점에 대사 수술이 제안되었다면 수술을 받을 자격이 있었을 만큼 상대적으로 건강한 보상성 간경변 비수술 환자들만을 포함하기 위해 엄격한 선택 기준이 사용됨.

=> 즉, 대조군도 치료군만큼이나 건강해! 그래서 건강한 사람들만 수술받을 수 있어서 그런거 아니야? 라는 의구심을 묵살하기~

**2. baseline covariate의 불균형**

이를 정당화하는 clever한 방법

맞아 몇몇 baseline covariate에 불균형 있었어. 그건 ㅇㅈ. 근데 대부분은 비수술 대조군에 유리한 쪽이었다구~ 대조군이 유리한 상황이었음에도 위험이 처치군에서 더 높게 나온 건 그만큼 metabolic surgery가 MALO에 음의 영향을 준다는 거 아니겠어? 우릐 연구 결과를 뒷받침하면 뒷받침했지 해치지는 않는 한계야~ 

그리고 overlap weighting 후에 regression 했으니까 남아있는 불균형이 통제될거야~ 왜냐면 혼란변수를 넣고 회귀 돌릴 것이기 때문.

**3. 과거 노출에 영향의 받은 시간 가변적 요인 분석 안 함**

이건 좀 센 놈이라 그런가 정당화도 안 함. 지현아 너가 이걸 타파해보렴!

**4.** MALO 사건의 수가 적어 **신뢰구간이 상대적으로 넓었**고, MALO의 개별 구성 요소를 분석할 수 없었음

**5. 소표본 편향**

표준 Cox 모델 대신  Firth’s penalized method in a fully adjusted Cox proportional hazard framework 방법을 사용함



**방법론 정리**

1. **E-value**

![image-20260309231039967](/Users/test/Desktop/github/images/image-20260309231039967.png)

2. **Firth's penalized method in a fully adjusted Cox proportional hazard framework**

![image-20260309231420039](/Users/test/Desktop/github/images/image-20260309231420039.png)

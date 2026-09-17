# **Chain-of-Thought (CoT) 프롬프팅 실험 결과 요약**

본 내용은 대형 언어 모델(LLM)의 다단계 추론 능력 향상을 위한 **CoT(Chain-of-Thought) 프롬프팅**의 핵심 성능, 작동 원리(절제 실험), 그리고 강건성(Robustness) 분석 결과를 정리한 것입니다.

## **1\. 모델 크기 및 벤치마크별 성능 (3.2 Results)**

* **핵심 결론**: **"모델이 충분히 클수록 CoT의 효과가 폭발적으로 증가한다."**  
* **창발적 능력 (Emergent Ability)**:  
  * 작은 파라미터 모델(LaMDA, GPT, PaLM의 저용량 버전)에서는 기본 프롬프팅(Standard)과 CoT 간의 차이가 미미합니다.  
  * 일정 규모 이상의 대형 모델(GPT-3 175B, PaLM 540B 등)에 도달하면서 CoT의 성능이 급격히 치솟는 창발적 특성을 보입니다.  
* **벤치마크별 결과**:  
  * **GSM8K**: 다단계 복합 추론이 필요한 대표 벤치마크로, PaLM 540B에서 기존 지도학습 최고 성능(Prior supervised best) 수준인 50%대 후반을 기록하며 Standard 방식 대비 압도적인 격차를 입증.  
  * **SVAMP / MAWPS**: 단어 변형 및 기본 문장제 수학 문제에서도 대형 모델로 갈수록 CoT가 일관되게 높은 정답률을 유지.

## **2\. 절제 실험: CoT가 효과적인 진짜 이유 (3.3 Ablation Study)**

연구진은 CoT의 성공 요인을 파악하기 위해 3가지 가설을 검증하는 절제 실험(Figure 5)을 진행했습니다.

| 실험 변형 | 실험 방식 | 결과 및 시사점 |
| :---- | :---- | :---- |
| **수식만 제공** *(Equation only)* | 풀이 과정 없이 최종 수식만 작성 *(예: ![][image1])* | 복잡한 문제(GSM8K 등)는 수식으로 직행하기 전 **언어적 이해와 구조화 단계**가 필수적이므로 성능 향상에 한계가 있음. |
| **계산량만 증가** *(Variable compute only)* | 생각할 시간(중간 토큰)을 주기 위해 무의미한 점(......) 출력 후 답변 | 토큰 수나 계산량 증가만으로는 효과가 없으며, **의미 있는 자연어 추론 맥락**이 핵심임을 증명. |
| **정답 후 풀이 설명** *(Reasoning after answer)* | 정답을 먼저 출력하고 뒤이어 풀이 과정을 설명 | 정답 예측 전에 풀이 단계가 먼저 전개되어야 하므로, CoT는 단순 사후 기억 인출이 아니라 **순차적 추론 과정**임이 입증됨. |

**요약 결론**: 단순히 계산 토큰을 늘리거나 수식만 나열하는 것으로는 부족하며, **정답에 도달하기 전 자연어로 문제를 단계별로 풀어가는 과정 자체가 CoT의 핵심 동력**입니다.

## **3\. CoT의 강건성 (3.4 Robustness)**

* **핵심 결론**: **"작성자, 표현 방식, 선택된 예시가 달라져도 CoT의 우수성은 안정적으로 유지된다."**  
* **검증 요소**:  
  * **작성자 변경 (Annotator B, C)**: 다른 연구자가 작성한 풀이 예시 적용 시에도 성능 유지.  
  * **문체 간소화 (Intentionally concise style)**: 풀이를 의도적으로 짧고 간결하게 작성해도 기본 프롬프팅을 상회.  
  * **예시 무작위 추출 (Exemplars ![][image2])**: GSM8K 내 서로 다른 8개 예시 조합을 사용해도 일관된 성능 향상 관찰.  
* **시사점**: 특정 프롬프트 엔지니어링 템플릿에 과적합(Overfitting)된 기교가 아니라, **'단계적 추론 예시'라는 방법론 자체의 일반화 능력이 강력함**을 보여줍니다.

[image1]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAI4AAAAWCAYAAAAWyKQmAAAFnElEQVR4Xu1ZXYhVVRQ+FycoKspqujU/d597Z2CafiiYSpTqSUgxI2gESR+CyEJ8KCMCIbQgCBtBGomYDCkIygR7MXyQgkaEDMrEfh7yoZBCAwOxIEOm75u99sy+a845d597Zi5T936wOPestX/W/s63f865UdRBBx100EEH/z10d3dfE8fxldrf7ujwkgEQc0+lUtlXq9Wu07F2R7VaNcABXnWsrdHb29sHYj6HcO7QMQ8liOs52LM60CrEFuu1PwNLMK41sLdoGN9oX1/fVX4BNojYE+SA5bmyQCB3o+wmf5Xp7+9/EL5PFsvEQm6PIe+d2t9KUBC7YTt0wAfiy5DonyDvJR1bSKDPYdhm2Gewy8jjPV0mCSMjI1eg/Djy3Y6HPoDr08wfdspfOXC/AvY3bMozllvjtweU4NvT6vEnQcR+OpSLBQFIvRNJ/Mirjjlwlhn74KZaTRz6HJbZxQd8JpQslFuF8kewwvQ6H3LfKMLYi9su8Y3g/ntyADuBeq/geutMQx7gXw77AVbVsRCg3hhXM+3PA5kQezmOUC4WBBQCkjgUpx/+ONO2wt4wgStOuVy+GgSVtd9DCULtwXWJDqSBDxP2cyhZMq4pkux8siWfgZ12+YlwxmdrpgNll6LsV7ANOhYC9sP+tD8PUH8d2tnFcYRyMe+gWJDAIdg2HXOI7RY1ZuxsCxLO4OBgN8ruhzju1bHIbo1PkkTOHh1Mg8kpHPaN8ieQ7ybnc22ITa8qeYRDGDvbP8DPko41AvspIhyMnVvUBC635eFi3uGIxGDW6hghWxQTjYXgIOEQnN0oexgz+37P3ZRoCJdrEbKQzwNo4x/YQU4a8XFcB2Hvw37C/S+w7foQ7cDxo9zk0NDQtTrWCEWEI1vUmEzkwlwUgpB2loTqWGQf8vOIreNNXuEQSjxNi4YoShb7RC770MYfvpg5LtiXEMog75HzjejjOMpNJOWJsmuZB/PRsUYoIhzUfRy2NbJHh2AuUO5lmQyhdhhc3KDbqYOQxsJzBkNy0ekuRx7L5BUO4cSDuntIXNLDCEEespIgxP+GXFb6fubDM5nvQ7ltxr5pLff9RMUKh+3UdMyBq5nkq+1dxB7Wfoo1ytj6qvY70jvuU4DUa5qLwkgTDr+Uwvc2Eoudr1nhRLOH699hK3QwFEXIkknwNc499+lYEjhGYw/WnOE6RuFcqGa8HaHMKMpMJBjf3Lgt1vkxptdh1+t2BF0SX+YcpgAX84I04WCpugtJnZTYtCHRc8aSeVF8dTM3BRTNFtg4ZkuFpKkzTzCaJUtEc9RtRUAXct/IB8WZbuxb0qmBgYGbXR0nnKRJIsJp2VbFvFBvUj2LX5kf7BLv44yPslwEhLsgEx6y33bRaU2SWK1jGhywybfizIjGbU8Y4C2mSfHIwFKFgzbLsuT7PlSp/6ugp6fnJvgmeLh1bRolHGO3qimsUI86nxfbYLzX+TwgF3mFk4RGXPjgIoA+R0MN7a5OezGYgZD4HRJ4Rsc0uMyj7F8kVccSQNFsRru79ZmmWfF4D3nOqzDaGoL/rLHfaKr0sR/YF7g/z1npzNiVc39kPwBy9eGWPLMN8Bwh9Y4k/b3A8Zvs716pMPMkHO971BwuWoVSxb5pvKkDDrJUHoNdNrOf5M9VMrYqxG5HmVe1aBx4akd8Z8PTezTd1kohye//Ih7cSc4mlhEivzXew67MnlOS7DXXvqxKk8Z+4HwK9g3smPx3pdGF2AG2rQMhMAWFw0M86n+Idi55YzmftVUtGNDpethxJLRUx9oFFDgE9JAs1cNRyh6PGFXGvxzmvG2FoKhwFhXkgHgU4lmlYx3Uo2IP1R+lraSNgPqPmCYO1YsWPARiQB83PBS1MWSCfZr3bPZ/Bw+zL9L4Wwc7iEpYaXaAnxf4WwfbCf8CXTHhvMdVAKEAAAAASUVORK5CYII=>

[image2]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADgAAAAaCAYAAADi4p8jAAADSUlEQVR4Xu2WS2gTURSGJySCouIzRpvHJGklCIKLiCAIilRpkboQ0YqPnShYxAcqqAs3LgRBoVCl1tdCK7XgQixFXbrwsRK0iFLcuOlCFwUXPrB+h9ypN6czY4JWTMkPP/fOec2ce889dxynjjrqqGOqIJFIzMzlchuFMtf6WkbEdd1NmUzmDuNOeAAOpdPpldqwJkFiG0ios1gsTvNk2Wz2JrIe264m0djYuIhEriWTyQWWWHb0liRpyWoTJLFdaMs4gwkSfI18vy3/J5CXU1JtvHwdnK71VSImpUm8fDwen8V8SaFQmM3zceaP8vn8HO0QBrGXBoX/Gh6jtk6aln0EJoBkFvPSfniPeTvjKfjBawSpVCoJl2q/MJid6pSPYvwGxwyHKd20tg9BhKT24PfVitHtJSTlz3OXOga/wAegd1/ZTk5p9XsJPEhiM5ifNitXMcQevyNMI/JySVhWGlkfi3hG2wfBNKkRxoMs+HLmHfAT3CJ6cwz2aT8PkkgP/ABztoKAJ5CN4tzC/Hq1JSUfgl+zlpu4LxjnaZ2GWdzbLM4qWy5JwxsSA16WxbP148B5GfwI+3mMKZ3cWZ8lOd0oKkAMn4vsXEoriHkSDgd+VDmiZiEitlDOMjG60e3mPYdtXRkwaMNwzG+LPR28Lyup9WHwzp9uVPKM/IFQ66oFMbrgE1dVXhlQtkoSkozWmQS/8yFrtQ7ZXKGWe8iUzt8VR6088iLyUcZdtly6rGkSZfZhcEuV0OuoyiuDdDOM3sEOSywX8Xr4xkteSq2hoWGhKNmdAvIR1+fceiD5Q/AtzHoyqmA+9o+Jd8lu6aYLvoRf4GpP/jtIacIWLZ8Ak8x7eBdehU/h0aampjjjAEGe81GD0m3FXpI1H/TDb+cdc//h147+mcTMln7Nhhh3OOoOM3fkgMSDx2xdCMbvWK0IQlR+q3zKJCo753eJEnwvL2nVcvv8iZ88h5WzB+I1u+WVFAhJjJgPvaqaDMgKnnd9StScP7n/qkKm9IdTUYlyH27Gts8JO39/Aql9eM7xaQqyC7IbWh4GdnkFfj2Vdmtsz2Z9Ov/fQowEtgVd/Oi2Bv42BcBrZFoeBOwvyF+Nlk8lTE5p1vGf4SdZjskGCQPY6AAAAABJRU5ErkJggg==>
# Create Instruction Template and Dataset for Korean long corpus

### Introduction
The korean longform dataset sprint team has the goal of generating instruction and tuning the existing model according to the characteristics of the long Korean corpus. Therefore, we have a contribution to generate more correct responses by sampling according to the characteristics of the long Korean corpus and generating instruction.

### Dataset
We collected three data from the economic domain with long corpus. 
1) Economic and industrial categories in large-scale web data-based Korean corpus data (AI Hub)
2) Crawllwing 4353 documents from Naver Encyclopedia with keywords "finance" "economy" "investment"
3) National Language Institute Newspaper Corpus 2022 (Everyone's corpus)

In order to unify the domain characteristics in the dataset and proceed with sophisticated sampling, corpus 1) and 3) underwent a process of extracting only corpus related to 'economy' with BM25.


### Detail

#### Instruction Templates


#### Instruction dataset

```
Instruction
'외국투자자금 조달의 한계와 선의의 투자를 위한 제도에 대해 작성하십시오.
이 글은 약 20~30 단어로 작성되어야 하며, 객관적이고 전문적인 스타일로 작성되어야 합니다.
주요 키워드로는 '외국투자자금 조달', '법령상의 허용한도', '국가개발목표', '경제적 수요심사', '선의의 투자'를 포함해야 합니다.
이 글은 외국투자자금 조달의 제도적 한계와 선의의 투자 촉진을 위한 목적을 다루어야 합니다.'

context
'외국투자자금 조달에 의한 사업추진이 국가개발목표와 경제적 수요심사를 충족시키도록 하고
특정부문 또는 기업의 외국지분이 법령상의 허용한도를 초과하지 않도록 하기 위한 제도이며, 선의의 투자를 촉진하기 위한 목적도 갖고 있다.'

output
'["외국투자자금 조달의 한계와 선의의 투자를 위한 제도에 대해 작성하십시오.
이 글은 약 20~30 단어로 작성되어야 하며, 객관적이고 전문적인 스타일로 작성되어야 합니다.
주요 키워드로는 \'외국투자자금 조달\', \'법령상의 허용한도\', \'국가개발목표\', \'경제적 수요심사\', \'선의의 투자\'를 포함해야 합니다.
이 글은 외국투자자금 조달의 제도적 한계와 선의의 투자 촉진을 위한 목적을 다루어야 합니다.
외국투자자본 조달의 제약요인과 선의의 외국투자를 위한 정책적 고려사항을 포함해야 하며 약 20-30 단어의 분량으로 작성되어 합니다.
다음 지침을 참고해 요청을 적절하게 완료하는 응답을 작성하세요.
### 지침: 다음은 \'다음 지침\'을 참고하여 작성한 응답입니다.
# ## 참고: nan\\n ## ## 응답: 다음 지침을 바탕으로 작성한 \'다음 응답\'입니다.
다음 지침은 \'국가인권위원회\'에서 작성한 것으로, \'다음\' 지침을 작성할 때 참고할 수 있습니다.
다음은 국가인권위원회에서 작성된 \'다음 국가인권위\'의 \'다음지침\'을 바탕을 작성한 글입니다.
\'다음국가인권위지침\'은 \'인권위법\'에 따라 국가기관, 지방자치단체, 각급 학교, 공직유관단체, 구금시설 등에
\'다음과 같은 인권교육을 실시할 것\'을 권고하는 내용입니다.
이는 \'다음 인권교육\'을 작성함에 있어 참고할 만한 내용입니다:
국가인권위원장은 인권에 대한 이해를 높이고 인권의식을 함양하기 위해 다음과 같인 인권교육 프로그램을 개발하고"]'
```

### Results
| Model | Bleu | Rouge_L | Rule-based(length) | Rule-based(style) | Rule-based(keyword) | Rule-based(topic) | Rule-based(topic) | Rule-based(toc) | Rule-based(other)
| polyglot-ko-5.8b(not_tuning) | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| polyglot-ko-1.3b | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| polyglot-ko-3.8b | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| polyglot-ko-5.8b | --- | --- | --- | --- | --- | --- | --- | --- | --- |

The problem is that the model repeats the instruction, causing hallucinations. This suggests that the model is not tuned properly.

We also used bleu and rouge_L, the evaluation metrics of the default generated model, as metrics. As a result, the model performance converged to 0, which supports the previous finding that the tuning was not correct.

Additionally, we wanted to measure whether the model was generating correctly based on the constraint language given in the instruction. In a basic way, we measured this by giving +1 for each occurrence of a keyword, and split it into a 5-point scale for each constraint.


### Contributors
Sungkyung Kim
Sangdae Nam
Gyujin Son
SangMin Lee
Jaecheol Lee


### Copyright
This material is copyrighted by AI hub and the corpus of all, and use for non-research purposes is prohibited.

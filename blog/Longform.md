# Create Instruction Template and Dataset for Korean long corpus

### Introduction
The korean longform dataset sprint team has the goal of generating instruction and tuning the existing model according to the characteristics of the long Korean corpus. Therefore, we have a contribution to generate more correct responses by sampling according to the characteristics of the long Korean corpus and generating instruction.

### Dataset
We collected three data from the economic domain with long corpus. 
1) Economic and industrial categories in large-scale web data-based Korean corpus data (AI Hub)
2) Crawllwing 4353 documents from Naver Encyclopedia with keywords "finance" "economy" "investment"
3) National Language Institute Newspaper Corpus 2022 ([모두의 말뭉치](https://corpus.korean.go.kr/))

In order to unify the domain characteristics in the dataset and proceed with sophisticated sampling, corpus 1) and 3) underwent a process of extracting only corpus related to 'economy' with BM25.


### Detail

For instruction tuning, we need sophisticated instructions to produce output that is close to the correct answer.

1) Restraints extraction <br>
- Therefore, as a first step, we set the generalized features that can be extracted from the text as restraints, and extract the information corresponding to each restraint with the gpt-3.5-turbo-api. 

2) Generated Final instruction <br>
- Generate a final instruction in the form of a sentence based on the extracted restraints.

3) Model tuning <br>
- Configure the final instruction as the input of the model and the corpus as the output of the model for tuning. At this time, the model is trained with lora during PEFT based on korea language models.

4) Evaluation <br>
- The evaluation is conducted by quantitative and qualitative evaluation. Quantitative evaluation mainly uses the metrics used in the basic generation task. Qualitative evaluation is performed by judging whether the information extracted from each retraining is similar to the output.

##### Instruction Templates example
```
input_ = f"""
You are now Ko-InstructGen a LLM that generate instructions for provided inputs. Compelete the provided template by leveraging the features of the provided text. Include every restraint in the final instruction. The longer the final instruction is the better. Write in Korean. 

### Initial Instruction: [Your instruction about the input that hits you in first sight comes here]
### Restraints: 
   (1) Length : [information on length of the text]
   (2) style: [information on the style of text]
   (3) keywords: [keyword that must be included, no more than 5]
   (4) topic : [topic of the given text]
   (5) toc: [explain the order of contents of the given text, this should not exceed 5 sentences]
   (6) other: [one another random restraint]
### Final Instruction: [Your final instruction that combines the initial instruction and restraints]

Input: 
{context}


Compelete: 
### Initial Instruction: 글을 작성하시오.
### Restraints: 
   (1) Length : {context_length} 단어

"""
```

##### Instruction dataset example

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
|------|---|---|---|---|---|---|---|---|---|
| polyglot-ko-5.8b(not_tuning) | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| polyglot-ko-1.3b | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| polyglot-ko-3.8b | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| polyglot-ko-5.8b | --- | --- | --- | --- | --- | --- | --- | --- | --- |

With short prompts, it seemed like a way to get people to generate longer posts that contained knowledge they didn't have, which could lead to more hallucinations.

We also used bleu and rouge_L, the evaluation metrics of the default generated model, as metrics. As a result, the model performance converged to 0, which supports the previous finding that the tuning was not correct.

Additionally, we wanted to measure whether the model was generating correctly based on the constraint language given in the instruction. In a basic way, we measured this by giving +1 for each occurrence of a keyword, and split it into a 5-point scale for each constraint.


### Contributors
Sungkyung Kim <br>
Sangdae Nam <br>
Gyujin Son <br>
SangMin Lee <br>
Jaecheol Lee <br>


### Copyright
This material is copyrighted by AI hub and [모두의 말뭉치](https://corpus.korean.go.kr/), and use for non-research purposes is prohibited.

# BertStudy
Google에서 제공하는 NLP 모델인 BERT를 통해 한글어 자연어 처리를 시도하는 과정에서 더 나은 방식을 찾고 정리하고 있습니다.



## 토크나이저 개선 사항
BERT를 통해 한글 의도 분류 모델을 만들기 위해서 가장 처음 봉착했던 문제가 한글 사전 및 토크나이저 오류 문제입니다.
개발자도 아니고 파이썬도 익숙치 않던 때라 워드피스 토크나이징이란 개념도 전혀 몰랐고 한줄 한줄 프린트 해보며 한글 데이터도 돌아가도록 했었죠.   
차츰 워드피스 토크나이징의 개념이 이해되면서 더 나은 사전을 빠르게 만드는 방법에 대해 생각도 해보게 되었고 한글 데이터에 맞도록 토크나이저도 변경하게 되었습니다.

**Tokenization_kor.py** 파일 참조

|개선사항|변경 부분|변경 이유|예시|
|:-----|:---:|:---:|:---:|
|한글 처리|token = self._run_strip_accents(token) 주석 처리|한글 NFD 시 글자 깨짐 현상|ㄱ ㅣㅁ ㅇ ㅣㄹ ㅎㅜㄴ > 김일훈|
|의미, 빈공간 구분| Full tokenizer 에서 token += "_" 추가 |의미 구분 및 스페이스 공간 구분|인가 를_ 받기_ 위해_ 필요한_ 준비물 은_ 무엇 인가_|
|구분자 기준 나누기 비활성화|_is_punctuation 함수 비활성화|기호가 문장에 붙어있음에도 불구하고 띄우는 현상 제거|나 는_ 밥 을_ 먹었다_. > 나 는_ 밥 을_ 먹었다 .|
|종결 토큰 처리|FullTokenizer 수정|문장의 끝에 무조건 "\_"가 붙었던 문제 처리|나 는_ 밥 을_ 먹었다 ._  > 나 는_ 밥 을_ 먹었다 .|


## 한글 사전 
### 1. Google sentencepiece 활용 사전 만들기

해당 내용은 [구글 sentencepiece github](https://github.com/google/sentencepiece)을 참조하면 이해하기 쉽습니다.   
먼저 sentencepiece 라이브러리를 불러옵니다.
```
  import sentencepiece as spm
```
하이퍼파라미터를 설정합니다.
```
train ="""--input=./data/ratings_train.txt \
    --model_prefix=sentpiece \
    --vocab_size=30000 \
    --model_type=bpe --character_coverage=0.9995"""
```
학습을 하면 모델, 사전 파일이 생깁니다.
```
spm.SentencePieceTrainer.Train(train)
```

학습된 모델을 로드합니다.
```
sp = spm.SentencePieceProcessor()
sp.Load('sentpiece.model')
```
Sentencepiece 로 학습한 모델, 사전으로 토크나이징한 결과는 다음과 같습니다.
```
>>> sp.EncodeAsPieces('이 영화는 정말 기분나쁘고 소름돋아서 다시는 보기 싫었어요.')
['▁이', '▁영화는', '▁정말', '▁기분나쁘', '고', '▁소름돋', '아서', '▁다시는', '▁보기', '▁싫', '었어요', '.']
```

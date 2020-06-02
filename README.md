# BertStudy
Google에서 제공하는 NLP 모델인 BERT를 통해 한글어 자연어 처리를 시도하는 과정에서 더 나은 방식을 찾고 정리하고 있습니다.



## 토크나이저 개선 사항
|개선사항|변경 부분|변경 이유|예시|
|:---|:---:|:---:|:---:|
|한글 처리|token = self._run_strip_accents(token) 주석 처리|한글 NFD 시 글자 깨짐 현상|ㄱ ㅣㅁ ㅇ ㅣㄹ ㅎㅜㄴ : 김일훈|
|의미, 빈공간 구분| Full tokenizer 에서 token += "_" 추가 |의미 구분 및 스페이스 공간 구분|인가 를_ 받기_ 위해_ 필요한_ 준비물 은_ 무엇 인가_|
|구분자 기준 나누기 비활성화|_is_punctuation 함수 비활성화|기호가 문장에 붙어있음에도 불구하고 띄우는 현상 제거|나 는_ 밥 을_ 먹었다_. > 나 는_ 밥 을_ 먹었다 .|
|종결 토큰 처리|FullTokenizer 수정|문장의 끝에 무조건 "\_"가 붙었던 문제 처리|나 는_ 밥 을_ 먹었다 ._  > 나 는_ 밥 을_ 먹었다 .|

# 자연어 처리 과정
--------
1. 토큰화
2. 단어 집합 생성
3. 정수 인코딩
4. 패딩
5. 벡터화

# 토큰화(Tokenization)
--------
텍스트를 단어 또는 문자 단위로 자르는 것

* 텍스트가 영어일때 토큰화를 사용하는 도구는 spaCy와 NLTK 또는 Python의 split()함수를 사용

* 텍스트가 한국어일때는 형태소 토큰화를 사용

    * [사과의, 사과가] 모두 사과를 의미하는 단어이지만 '의'와 '가' 때문에 다른 언어로 인식할 수 있음 -> 이를 방지하고자 한국어에서는 보편적으로 [형태소 분석기] (ex. Mecab)를 사용한다. 이를 사용하면 '의'와 '가'와 같은 것들이 사과와 같은 단어와 분리된다.
    
* list()를 통해 문자 토큰화를 시킬 수 있음

# 단어 집합(Vocabulary) 생성
---------
중복을 제거한 텍스트의 총 단어의 집합을 의미

아래와 같은 방식으로 빈도가 높은 단어들을 묶어 n개의 단어를 묶어 단어 집합을 생성

``` python
vocab_size = 500
vocab = vocab.most_common(vocab_size)
print('단어 집합의 크기 : {}'.format(len(vocab)))
```

# 각 단어에 고유한 정수 부여 (unique integer)
--------
위에서 만든 단어 집합에 있는 단어들에 아래와 같은 코드를 사용해 고유한 정수를 부여

``` python
word_to_index = {word[0] : index + 2 for index, word in enumerate(vocab)}
word_to_index['pad'] = 1
word_to_index['unk'] = 0
```
-------
``` python
encoded = []
for line in tokenized: #입력 데이터에서 1줄씩 문장을 읽음
    temp = []
    for w in line: #각 줄에서 1개씩 읽음
      try:
        temp.append(word_to_index[w]) # 단어를 해당되는 정수로 변환
      except KeyError: # 단어 집합에 없는 단어일 경우 unk로 대체된다.
        temp.append(word_to_index['unk']) # unk의 인덱스(0)로 변환

    encoded.append(temp)
```

## word_to_index

|Index|word|uni_Int|
|:---:|:---:|:---:|
|0|'재밌'|2|
|1|'사랑'|3|
|2|'영화'|4|

---------
위와 같이 코드를 실행시키면  
encoded[?] -> [205, 119, 206, 53, 207, 31, 208, 209, 54, 10, 25, 11]  
이와 같은 결과가 출력된다.

# 패딩(padding)
길이가 다른 문장들을 모두 동일한 길이로 바꿔주는 것
``` python
len(encoded[?])
```
위와 같은 코드를 실행하면 문장의 길이를 알 수 있다. 패딩과정에서는 문장의 길이가 가장 긴 것에 맞춰서 다른 문장들의 길이를 늘려주는 것
```python
for line in encoded:
    if len(line) < max_len: # 현재 샘플이 정해준 길이보다 짧으면
        line += [word_to_index['pad']] * (max_len - len(line)) # 나머지는 전부 'pad' 토큰으로 채운다.
```

# toxic-comment-classification

# Data 전처리

<img width="408" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/929b67f2-16d7-4190-9b64-64a74f31cf44">


1. 숫자, 영문 이외 문자 모두 제거
2. 영문자 소문자 화
3. 앞뒤 공백 제거, 두 칸 이상의 공백 한 칸으로 변환

# EDA

### Class 분포 비율

<img width="298" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/5753a805-b01d-4d5c-a4b3-de7826a30468">

→ `notoxic` text data 가 `toxic` text data보다 10배 많음을 알 수 있다.

### TextSequence 길이 분포

<img width="376" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/bab46433-d086-44a1-9a91-cddb569c9de7">


→ 대부분 `200 안쪽`임을 알 수 있다.

# `Glove`를 활용한 `Vectorizer`

- 사전 학습된 `**glove.6B.100d**` 를 사용
    - Special Tokens에 해당 하는 임베딩 Vector 추가

      <img width="450" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/60a61560-dada-42cc-85b6-f13b5b5c441a">

        
- Vectorize 할 때 TrainData을 바탕으로 한 Vocabulary를 사용하지 않고, 
`glove.6B.100d` 에 있는 token은 그대로 inference,
없는 token들은  `<UNK>` 으로 처리
    <img width="534" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/2ae3ed9d-8456-4412-a5cd-9599f771f242">

    

# 최종 모델 구조

### Blocks
<img width="491" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/c24ac57b-11e6-472e-8b7c-d0dd66f7005b">


- 두 개의 GRU 블록으로 양방향 RNN 구축.
- 세 개의 Linear 블록으로 추가적인 Feature 학습 및 Class 예측 할 수 있도록 구축.

### Forward(양방향 RNN 부분)

<img width="463" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/c92d0175-6c72-48eb-ae7c-d96ad163dcb2">


- Example
    
    x_in: **[<start>, I , am, hungry, <end>, <pad>, <pad>, …]** 일때
    
    x_in_inverse: **[<end>, hungry, am, I, <start>, <pad>, <pad>, …]** 
    
    되도록 정의하고 각각의 rnn 블록에 넣은 뒤,
    
    x_in의 **<end>** token의 hidden_state과 x_in_inverse의 **<start>** token의 hidden_state를 
    concat 하여 다음 블록에 전달한다.
    

### Hyperparameters
<img width="248" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/2b7e9de6-97e4-40cf-ab16-ccc650bd0bd2">

- **hidden_size = 64**
- **embedding_dim = 100**

# 최종 모델 성능 및 다른 모델 과 비교

### Train
<img width="339" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/d3e3da0b-9f60-4586-9b3e-e192cb6e5374">


### Val
<img width="301" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/ca84e821-e379-4f65-8c7f-4caf5762de18">

### Test 성능
<img width="334" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/c6df015c-08a0-4b00-8152-122a021cb61c">


### 같은 조건에서 Glove Embedding 대신 `nn.Embedding`을 통해 Train 단계에서 학습된 Embedding을 사용했을 때 Test 성능

<img width="337" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/c8dac165-fad0-4c0d-9300-6c3c06fae065">

- 최종 모델보다 과적합이 심함

### 같은 조건에서 `Yelp 실습` 때와 같이 `OneHotEncoding을 Vector 로 사용`했을 때 Test 성능

**test_accuracy: 0.9542170698924731**

**test_f1: 0.7171769590036325**

**test_tpr: 0.6029668411867365**

# Test Data를 활용한 `오분류 분석`
- nottoxic textData가 toxicData 보다 많이 오분류 되었다.
- Class 분포 비율을 고려하면 두 Class가 공평하게 오분류 된 양상을 보인다.
- 원본 Data 분포를 고려하더라도 비교적 짧은 문장들이 많이 오분류 되었다.
<img width="620" alt="" src="https://github.com/JJooKim/toxic_comment_classification/assets/122198271/30de6bed-24c0-4fdf-a3fa-583929649888">

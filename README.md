# toxic-comment-classification

# Data 전처리
<img width="408" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/191daf02-69fa-4afc-bc8a-6a4e4b124742">


1. 숫자, 영문 이외 문자 모두 제거
2. 영문자 소문자 화
3. 앞뒤 공백 제거, 두 칸 이상의 공백 한 칸으로 변환

# EDA

### Class 분포 비율
<img width="298" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/77be644e-9ea3-4cdf-a509-c7f8939a183e">

→ `notoxic` text data 가 `toxic` text data보다 10배 많음을 알 수 있다.

### TextSequence 길이 분포

<img width="376" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/9f47f1ee-8f4c-4e36-a3b4-108df276b6e7">

→ 대부분 `200 안쪽`임을 알 수 있다.

# `Glove`를 활용한 `Vectorizer`

- 사전 학습된 `**glove.6B.100d**` 를 사용
    - Special Tokens에 해당 하는 임베딩 Vector 추가
        
        <img width="450" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/ae6edc39-ac5c-4b40-b15e-281eca666e0d">

        
- Vectorize 할 때 TrainData을 바탕으로 한 Vocabulary를 사용하지 않고, 
`glove.6B.100d` 에 있는 token은 그대로 inference,
없는 token들은  `<UNK>` 으로 처리
    
    <img width="534" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/ea388264-ac12-4951-ae7e-e8af07be3ab5">

    

# 최종 모델 구조

### Blocks

<img width="491" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/dc173ee6-a47d-4811-ad3f-dca1421531b5">


- 두 개의 GRU 블록으로 양방향 RNN 구축.
- 세 개의 Linear 블록으로 추가적인 Feature 학습 및 Class 예측 할 수 있도록 구축.

### Forward(양방향 RNN 부분)

<img width="463" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/9996c7b9-742c-47b5-aeca-a187b52ee60c">


- Example
    
    x_in: **[<start>, I , am, hungry, <end>, <pad>, <pad>, …]** 일때
    
    x_in_inverse: **[<end>, hungry, am, I, <start>, <pad>, <pad>, …]** 
    
    되도록 정의하고 각각의 rnn 블록에 넣은 뒤,
    
    x_in의 **<end>** token의 hidden_state과 x_in_inverse의 **<start>** token의 hidden_state를 
    concat 하여 다음 블록에 전달한다.
    

### Hyperparameters

<img width="248" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/708a3a02-11e7-42f9-bf00-4ae2793ecca3">


- **hidden_size = 64**
- **embedding_dim = 100**

# 최종 모델 성능 및 다른 모델 과 비교

### Train

<img width="339" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/e3b226dc-f707-4b0d-a6e6-f0c80efb5161">


### Val

<img width="301" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/71d3a247-bdbc-4214-b772-5a61b1cf9225">


### Test 성능

<img width="334" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/62aaae69-2236-491b-8427-5c2ceba48568">


### 같은 조건에서 Glove Embedding 대신 `nn.Embedding`을 통해 Train 단계에서 학습된 Embedding을 사용했을 때 Test 성능
<img width="337" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/20fbf2a5-7953-486c-a754-9673903397fa">

- 최종 모델보다 과적합이 심함

### 같은 조건에서 `Yelp 실습` 때와 같이 `OneHotEncoding을 Vector 로 사용`했을 때 Test 성능

**test_accuracy: 0.9542170698924731**

**test_f1: 0.7171769590036325**

**test_tpr: 0.6029668411867365**

# Test Data를 활용한 `오분류 분석`
- nottoxic textData가 toxicData 보다 많이 오분류 되었다.
- Class 분포 비율을 고려하면 두 Class가 공평하게 오분류 된 양상을 보인다.
- 원본 Data 분포를 고려하더라도 비교적 짧은 문장들이 많이 오분류 되었다.
 <img width="620" alt="image" src="https://github.com/JJooKim/toxic-comment-classification/assets/122198271/42311906-b7b1-4a34-b5c7-b6c6f651bef6">

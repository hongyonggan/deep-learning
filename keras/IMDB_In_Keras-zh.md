
# 使用 Keras 分析 IMDB 电影数据


```python
# Imports
import numpy as np
import keras
from keras.datasets import imdb
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation
from keras.preprocessing.text import Tokenizer
import matplotlib.pyplot as plt
%matplotlib inline

np.random.seed(42)
```

    Using TensorFlow backend.


## 1. 加载数据

该数据集预先加载了 Keras，所以一个简单的命令就会帮助我们训练和测试数据。 这里有一个我们想看多少单词的参数。 我们已将它设置为1000，但你可以随时尝试设置为其他数字。


```python
# Loading the data (it's preloaded in Keras)
(x_train, y_train), (x_test, y_test) = imdb.load_data(num_words=1000)

print(x_train.shape)
print(x_test.shape)
```

    Downloading data from https://s3.amazonaws.com/text-datasets/imdb.npz
    17465344/17464789 [==============================] - 2s 0us/step
    (25000,)
    (25000,)


## 2. 检查数据

请注意，数据已经过预处理，其中所有单词都包含数字，评论作为向量与评论中包含的单词一起出现。 例如，如果单词'the'是我们词典中的第一个单词，并且评论包含单词'the'，那么在相应的向量中有 1。

输出结果是 1 和 0 的向量，其中 1 表示正面评论，0 是负面评论。


```python
print(x_train[0])
print(y_train[0])
```

    [1, 11, 2, 11, 4, 2, 745, 2, 299, 2, 590, 2, 2, 37, 47, 27, 2, 2, 2, 19, 6, 2, 15, 2, 2, 17, 2, 723, 2, 2, 757, 46, 4, 232, 2, 39, 107, 2, 11, 4, 2, 198, 24, 4, 2, 133, 4, 107, 7, 98, 413, 2, 2, 11, 35, 781, 8, 169, 4, 2, 5, 259, 334, 2, 8, 4, 2, 10, 10, 17, 16, 2, 46, 34, 101, 612, 7, 84, 18, 49, 282, 167, 2, 2, 122, 24, 2, 8, 177, 4, 392, 531, 19, 259, 15, 934, 40, 507, 39, 2, 260, 77, 8, 162, 2, 121, 4, 65, 304, 273, 13, 70, 2, 2, 8, 15, 745, 2, 5, 27, 322, 2, 2, 2, 70, 30, 2, 88, 17, 6, 2, 2, 29, 100, 30, 2, 50, 21, 18, 148, 15, 26, 2, 12, 152, 157, 10, 10, 21, 19, 2, 46, 50, 5, 4, 2, 112, 828, 6, 2, 4, 162, 2, 2, 517, 6, 2, 7, 4, 2, 2, 4, 351, 232, 385, 125, 6, 2, 39, 2, 5, 29, 69, 2, 2, 6, 162, 2, 2, 232, 256, 34, 718, 2, 2, 8, 6, 226, 762, 7, 2, 2, 5, 517, 2, 6, 2, 7, 4, 351, 232, 37, 9, 2, 8, 123, 2, 2, 2, 188, 2, 857, 11, 4, 86, 22, 121, 29, 2, 2, 10, 10, 2, 61, 514, 11, 14, 22, 9, 2, 2, 14, 575, 208, 159, 2, 16, 2, 5, 187, 15, 58, 29, 93, 6, 2, 7, 395, 62, 30, 2, 493, 37, 26, 66, 2, 29, 299, 4, 172, 243, 7, 217, 11, 4, 2, 2, 22, 4, 2, 2, 13, 70, 243, 7, 2, 19, 2, 11, 15, 236, 2, 136, 121, 29, 5, 2, 26, 112, 2, 180, 34, 2, 2, 5, 320, 4, 162, 2, 568, 319, 4, 2, 2, 2, 269, 8, 401, 56, 19, 2, 16, 142, 334, 88, 146, 243, 7, 11, 2, 2, 150, 11, 4, 2, 2, 10, 10, 2, 828, 4, 206, 170, 33, 6, 52, 2, 225, 55, 117, 180, 58, 11, 14, 22, 48, 50, 16, 101, 329, 12, 62, 30, 35, 2, 2, 22, 2, 11, 4, 2, 2, 35, 735, 18, 118, 204, 881, 15, 291, 10, 10, 2, 82, 93, 52, 361, 7, 4, 162, 2, 2, 5, 4, 785, 2, 49, 7, 4, 172, 2, 7, 665, 26, 303, 343, 11, 23, 4, 2, 11, 192, 2, 11, 4, 2, 9, 44, 84, 24, 2, 54, 36, 66, 144, 11, 68, 205, 118, 602, 55, 729, 174, 8, 23, 4, 2, 10, 10, 2, 11, 4, 2, 127, 316, 2, 37, 16, 2, 19, 12, 150, 138, 426, 2, 2, 79, 49, 542, 162, 2, 2, 84, 11, 4, 392, 555]
    1


## 3. 输出的 One-hot 编码

在这里，我们将输入向量转换为 (0,1)-向量。 例如，如果预处理的向量包含数字 14，则在处理的向量中，第 14 个输入将是 1。


```python
# One-hot encoding the output into vector mode, each of length 1000
tokenizer = Tokenizer(num_words=1000)
x_train = tokenizer.sequences_to_matrix(x_train, mode='binary')
x_test = tokenizer.sequences_to_matrix(x_test, mode='binary')
print(x_train[0])
```

    [ 0.  1.  1.  0.  1.  1.  1.  1.  1.  1.  1.  1.  1.  1.  1.  1.  1.  1.
      1.  1.  0.  1.  1.  1.  1.  0.  1.  1.  0.  1.  1.  0.  0.  1.  1.  1.
      1.  1.  0.  1.  1.  0.  0.  0.  1.  0.  1.  1.  1.  1.  1.  0.  1.  0.
      1.  1.  1.  0.  1.  0.  0.  1.  1.  0.  0.  1.  1.  0.  1.  1.  1.  0.
      0.  0.  0.  0.  0.  1.  0.  1.  0.  0.  1.  0.  1.  0.  1.  0.  1.  0.
      0.  0.  0.  1.  0.  0.  0.  0.  1.  0.  1.  1.  0.  0.  0.  0.  0.  1.
      0.  0.  0.  0.  1.  0.  0.  0.  0.  1.  1.  0.  0.  1.  1.  1.  0.  1.
      0.  1.  0.  0.  0.  0.  0.  1.  0.  0.  1.  0.  1.  0.  0.  0.  1.  0.
      1.  0.  1.  0.  1.  0.  1.  0.  1.  0.  0.  0.  0.  1.  0.  1.  0.  0.
      1.  0.  0.  0.  0.  1.  0.  1.  1.  0.  1.  0.  1.  0.  0.  1.  0.  0.
      1.  0.  0.  0.  0.  0.  0.  1.  1.  0.  0.  0.  1.  0.  0.  0.  0.  0.
      1.  0.  0.  0.  0.  0.  1.  1.  1.  0.  1.  0.  0.  0.  0.  0.  0.  0.
      0.  1.  0.  0.  0.  0.  0.  0.  0.  1.  1.  0.  0.  0.  0.  0.  1.  0.
      0.  0.  1.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  1.  0.  0.  1.  1.  0.  0.  0.  0.  0.  0.  0.  0.  1.
      0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.
      0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  1.  1.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  1.  1.  0.  1.  0.
      0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.
      0.  1.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  1.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  1.  0.  0.  1.
      0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  1.  0.  0.  1.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  1.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      1.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.  0.
      0.  0.  0.  1.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  1.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  1.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      1.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
      0.  0.  0.  0.  0.  0.  0.  0.  0.  0.]


同时我们将对输出进行 one-hot 编码。


```python
# One-hot encoding the output
num_classes = 2
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)
print(y_train.shape)
print(y_test.shape)
```

    (25000, 2)
    (25000, 2)


## 4. 模型构建

使用 sequential 在这里构建模型。 请随意尝试不同的层和大小！ 此外，你可以尝试添加 dropout 层以减少过拟合。


```python
# TODO: Build the model architecture
model = Sequential()
model.add(Dense(128, activation='relu', input_shape=(1000,)))
model.add(Dropout(.2))
model.add(Dense(64, activation='relu'))
model.add(Dropout(.1))
model.add(Dense(2, activation='sigmoid'))
model.compile(loss = 'categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
# TODO: Compile the model using a loss function and an optimizer.
model.summary()


```

    _________________________________________________________________
    Layer (type)                 Output Shape              Param #   
    =================================================================
    dense_1 (Dense)              (None, 128)               128128    
    _________________________________________________________________
    dropout_1 (Dropout)          (None, 128)               0         
    _________________________________________________________________
    dense_2 (Dense)              (None, 64)                8256      
    _________________________________________________________________
    dropout_2 (Dropout)          (None, 64)                0         
    _________________________________________________________________
    dense_3 (Dense)              (None, 2)                 130       
    =================================================================
    Total params: 136,514
    Trainable params: 136,514
    Non-trainable params: 0
    _________________________________________________________________


## 5. 训练模型

运行模型。 你可以尝试不同的 batch_size 和 epoch 数量！


```python
# TODO: Run the model. Feel free to experiment with different batch sizes and number of epochs.
model.fit(x_train, y_train, epochs=50, batch_size=100, verbose=0)
```




    <keras.callbacks.History at 0x7f5fce5563c8>



## 6. 评估模型

你可以在测试集上评估模型，这将为你提供模型的准确性。你得出的结果可以大于 85% 吗？


```python
score = model.evaluate(x_test, y_test, verbose=0)
print("Accuracy: ", score[1])
```

    Accuracy:  0.84432

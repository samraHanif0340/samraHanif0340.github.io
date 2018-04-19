---
title: "Deep Learning - Time Series and NLP"
excerpt: "Project"
header:
  teaser: assets/images/time_series_nlp_01.jpg
---

{% include feature_row %}

{% include toc %}

## Project

### Problem
In this project we develop a deep learning approach based on convolutional neural network(CNN) and long-short term memory(LSTM) to classify a time series sequence data. The same solution can be equally applied for character-level sentence classification. 

We will be using the following tools:
* Python
* Keras
* Jupyter Notebook

### Solution 

Import the required libraries


```python
import pdb
import numpy as np
import csv
import tensorflow as tf
from sklearn.model_selection import train_test_split
from sklearn.utils.class_weight import compute_class_weight

from keras.models import load_model, Model
from keras.layers import Dense, Activation, Dropout, Input, LSTM, Lambda, Bidirectional, Conv1D, MaxPooling1D
import keras.callbacks
```

{: .notice--warning}
More information on train and test data (i.e., time series data and natural language processing data) is currently being designed. Visit at a later time for further updates.

Build the machine learning model as follows: 

CNN (4 layer) --> LSTM (2 layer) --> Dense (1 layer) --> Softmax --> Cross entropy loss

1. CNN is suitable for character level sequence (or time series) classification. It helps to extract relevant patterns from the sequences along the feature and time dimensions.
2. LSTM helps to recognise sequential information
3. Fully connected Dense layer with Softmax reduces the output to 12 desired class probability distribution
4. Cross entropy loss is used as the cost for this multiclass classification problem.


```python
print('Building the model')
kernel_length = [ 5,   3,   3,   3]
filter_num    = [64,  64, 128, 128]
pool_length   = [ 2,   2,   2,   2]
x_input = Input(shape=(Tx,), dtype='int64')
# The one-hot encoded vectors for each input string is built on the fly using the Lambda layer
# Desired shape of input data X = (m, Tx, n_v)
x_one_hot = Lambda(one_hot, output_shape=one_hot_outshape, arguments={'numOfVocab': 26})(x_input)
x_conv = x_one_hot
for ii in range(len(filter_num)):
    x_conv = Conv1D(filters=filter_num[ii],
                    kernel_size=kernel_length[ii],
                    padding='same')(x_conv)
    x_conv = Activation('relu')(x_conv)
    x_conv = MaxPooling1D(pool_size=pool_length[ii])(x_conv)

x_lstm1 = Bidirectional(LSTM(128, return_sequences=True))(x_conv)
x_lstm1_dropout = Dropout(0.3)(x_lstm1)
x_lstm2 = LSTM(128, return_sequences=False)(x_lstm1_dropout)
x_lstm2_dropout = Dropout(0.3)(x_lstm2)
x_out = Dense(n_c, activation='softmax')(x_lstm2_dropout)
model = Model(inputs=x_input, outputs=x_out, name='textClassifier')
# Check model summary
model.summary()
```

    Building the model
    _________________________________________________________________
    Layer (type)                 Output Shape              Param #   
    =================================================================
    input_1 (InputLayer)         (None, 448)               0         
    _________________________________________________________________
    lambda_1 (Lambda)            (None, 448, 26)           0         
    _________________________________________________________________
    conv1d_1 (Conv1D)            (None, 448, 64)           8384      
    _________________________________________________________________
    activation_1 (Activation)    (None, 448, 64)           0         
    _________________________________________________________________
    max_pooling1d_1 (MaxPooling1 (None, 224, 64)           0         
    _________________________________________________________________
    conv1d_2 (Conv1D)            (None, 224, 64)           12352     
    _________________________________________________________________
    activation_2 (Activation)    (None, 224, 64)           0         
    _________________________________________________________________
    max_pooling1d_2 (MaxPooling1 (None, 112, 64)           0         
    _________________________________________________________________
    conv1d_3 (Conv1D)            (None, 112, 128)          24704     
    _________________________________________________________________
    activation_3 (Activation)    (None, 112, 128)          0         
    _________________________________________________________________
    max_pooling1d_3 (MaxPooling1 (None, 56, 128)           0         
    _________________________________________________________________
    conv1d_4 (Conv1D)            (None, 56, 128)           49280     
    _________________________________________________________________
    activation_4 (Activation)    (None, 56, 128)           0         
    _________________________________________________________________
    max_pooling1d_4 (MaxPooling1 (None, 28, 128)           0         
    _________________________________________________________________
    bidirectional_1 (Bidirection (None, 28, 256)           263168    
    _________________________________________________________________
    dropout_1 (Dropout)          (None, 28, 256)           0         
    _________________________________________________________________
    lstm_2 (LSTM)                (None, 128)               197120    
    _________________________________________________________________
    dropout_2 (Dropout)          (None, 128)               0         
    _________________________________________________________________
    dense_1 (Dense)              (None, 12)                1548      
    =================================================================
    Total params: 556,556
    Trainable params: 556,556
    Non-trainable params: 0
    _________________________________________________________________
    

Define an optimizer and compile the model


```python
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
```

Save the model at regular epoch intervals


```python
checkpoint = keras.callbacks.ModelCheckpoint('./cnn_lstm'+'-{epoch:02d}-{val_acc:.2f}.hdf5',
                                             monitor='val_loss', 
                                             verbose=1, 
                                             save_best_only=False, 
                                             save_weights_only=False, 
                                             mode='auto', 
                                             period=10)
```

Fit the model


```python
print('Fitting the model now')
model.fit(x_train, y_train_onehot,
          validation_data=(x_dev, y_dev_onehot),
          batch_size=256, 
          epochs=130,
          shuffle=True,
          callbacks=[checkpoint],
          class_weight=class_weight_dict)

# Save model to continue later
model.save('./cnn_lstm.hdf5')
```

Hence, we now load the trained model containing the trained parameters.


```python
#Load model to continue testing
#Our saved file is named "cnn_lstm-180-0.77.hdf5"
model = load_model('./cnn_lstm-180-0.77.hdf5')
```

Make prediction on test set and write the predcited class labels of test sample into file


```python
def write2file(filename,resultList):
    with open(filename,'w',newline='') as outputFile:
        writer = csv.writer(outputFile)
        for result in resultList:
            writer.writerow([result]) 
```


```python
#Make predictions
pred = model.predict(x_test)

#Write predictions to file
pred_labels = np.argmax(pred,axis=1)
write2file('ytest.txt',pred_labels)
```

The end

### Repository

{: .notice--warning}
Repository coming soon.

The [repository](https://github.com/Adaickalavan/Deep-Learning-Time-Series-NLP) consists of the following:  
* Python script
* Jupyter Notebook
* Train and test data

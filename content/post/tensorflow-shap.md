---
title: "Explaining Tensorflow Predictions with SHapley Additive exPlanations"
author: Malcolm Smith Fraser
date: 2021-11-12T01:03:23Z
tags:
series:
draft: true
---

In this post I cover how I to train a simple CNN using tensorflow, and explain
the predictions using SHAP. 

SHAP (SHapley Additive exPlanations) is a method to explain predictions based on
the game theory concept of optimal Shapley Values. In short, the goal of SHAP is
to explain the prediction of an instance, x, by computing the contribution of
each feature to the prediction. 

### Training the model
The model I train will be able to distinguish between dragonflies, cockroaches,
and beetles. Original images can be found here (https://www.insectimages.org/index.cfm).
I train on 1,199 images - with 816 images for training, 203 images for validation,
and 180 images for testing.

#### Import dependencies and connect to the dataset
```{python}
import tensorflow as tf

from tensorflow.keras.preprocessing.image import ImageDataGenerator
from tensorflow.keras.layers import AveragePooling2D, Dropout, Flatten, Dense, Input
from tensorflow.keras.models import Model
from tensorflow.keras.optimizers import Adam
from tensorflow.keras.applications import ResNet50
from sklearn.metrics import classification_report
from imutils import paths
import matplotlib.pyplot as plt
import numpy as np


train_dir = './insects/train'
test_dir = './insects/test'

BATCH_SIZE = 32
IMG_SIZE = (224, 224)

# initialize the training training data augmentation object
trainAug = ImageDataGenerator(
	validation_split=.2,
	rotation_range=25,
	zoom_range=0.1,
	width_shift_range=0.1,
	height_shift_range=0.1,
	shear_range=0.2,
	horizontal_flip=True,
	fill_mode="nearest")
	
# initialize the validation/testing data augmentation object (which
testAug = ImageDataGenerator()


# initialize the training generator
trainGen = trainAug.flow_from_directory(
	train_dir,
	subset='training',
	seed=69,
	class_mode="categorical",
	target_size=IMG_SIZE,
	color_mode="rgb",
	shuffle=True,
	batch_size=BATCH_SIZE)
# initialize the validation generator
valGen = trainAug.flow_from_directory(
	train_dir,
	subset='validation',
	seed=69,
	class_mode="categorical",
	target_size=IMG_SIZE,
	color_mode="rgb",
	shuffle=False,
	batch_size=BATCH_SIZE)
# initialize the validation generator
testGen = testAug.flow_from_directory(
	test_dir,
	class_mode="categorical",
	target_size=IMG_SIZE,
	color_mode="rgb",
	shuffle=False,
	batch_size=180)
```

#### Model configuration

For the model itself I use transfer learning on a resnet50 model pretrained on imagenet.
I freeze all the layers in the pretrained model and train my own 5-layer model head. 

```{python}
baseModel = ResNet50(weights="imagenet", include_top=False,
	input_tensor=Input(shape=(224, 224, 3)))
# construct the head of the model that will be placed on top of the
# the base model
headModel = baseModel.output
headModel = AveragePooling2D(pool_size=(7, 7))(headModel)
headModel = Flatten(name="flatten")(headModel)
headModel = Dense(256, activation="relu")(headModel)
headModel = Dropout(0.5)(headModel)
headModel = Dense(len(train_dataset.class_names), activation="softmax")(headModel)
# place the head FC model on top of the base model (this will become
# the actual model we will train)
model = Model(inputs=baseModel.input, outputs=headModel)
# loop over all layers in the base model and freeze them so they will
# *not* be updated during the training process
for layer in baseModel.layers:
	layer.trainable = False
```

#### Model training

I train the model for 10 epochs with a learning rate of 0.0001.

```{python}
# compile the model
base_learning_rate = 0.0001

opt = Adam(learning_rate=base_learning_rate)
model.compile(loss="binary_crossentropy", optimizer=opt,
	metrics=["accuracy"])
# train the model
print("[INFO] training model...")
H = model.fit(
	trainGen,
	validation_data=valGen,
	epochs=10)
```

```
[INFO] training model...
Epoch 1/10
26/26 [==============================] - 17s 561ms/step - loss: 0.5831 - accuracy: 0.6042 - val_loss: 0.2954 - val_accuracy: 0.8670
Epoch 2/10
26/26 [==============================] - 13s 507ms/step - loss: 0.3075 - accuracy: 0.8444 - val_loss: 0.2106 - val_accuracy: 0.9212
Epoch 3/10
26/26 [==============================] - 13s 502ms/step - loss: 0.2339 - accuracy: 0.8934 - val_loss: 0.1869 - val_accuracy: 0.9212
Epoch 4/10
26/26 [==============================] - 13s 501ms/step - loss: 0.1948 - accuracy: 0.9154 - val_loss: 0.1625 - val_accuracy: 0.9261
Epoch 5/10
26/26 [==============================] - 13s 497ms/step - loss: 0.1590 - accuracy: 0.9375 - val_loss: 0.1665 - val_accuracy: 0.9261
Epoch 6/10
26/26 [==============================] - 13s 498ms/step - loss: 0.1539 - accuracy: 0.9338 - val_loss: 0.1394 - val_accuracy: 0.9360
Epoch 7/10
26/26 [==============================] - 13s 498ms/step - loss: 0.1384 - accuracy: 0.9485 - val_loss: 0.1449 - val_accuracy: 0.9261
Epoch 8/10
26/26 [==============================] - 13s 490ms/step - loss: 0.1282 - accuracy: 0.9522 - val_loss: 0.1314 - val_accuracy: 0.9409
Epoch 9/10
26/26 [==============================] - 13s 493ms/step - loss: 0.1098 - accuracy: 0.9571 - val_loss: 0.1272 - val_accuracy: 0.9310
Epoch 10/10
26/26 [==============================] - 13s 493ms/step - loss: 0.1055 - accuracy: 0.9559 - val_loss: 0.1210 - val_accuracy: 0.9557
```

#### Model Evaluation
The trained model achieved an accuracy of ~97% on the holdout dataset.

```{python}
targets = np.array([np.argmax(label) for label in next(iter(testGen))[1]])
test_preds = np.array([np.argmax(prediction) for prediction in model.predict(testGen)])
sum(targets == test_preds) / len(targets)
```

```
0.9722222222222222
```

#### Model Explantion with SHAP

```{python}
import shap
import numpy as np

X_train = next(iter(trainGen))[0] # Get the first of images from the training generator

explainer = shap.GradientExplainer(model, X_train) # Initialize the GradientExplainer for the CNN
sv = explainer.shap_values(X_train[:10]) # Generate the SHAP values for a sample of the training data

shap.image_plot([sv[i] for i in range(3)], X_train[:10], show=True) # plot the SHAP values on top of your images
```

![Example image](/images/shap_results.png)


Not entirely sure if I'm doing this last part correctly as the images doesnt loop all that great...




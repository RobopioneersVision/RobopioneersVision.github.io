---
title: Tensorflow kears模型转ONNX 格式.md
toc: true
date: 2021-12-22 09:25:00
---
# Tensorflow kears模型转ONNX 格式

> 环境 ： ubuntu 20.04  TensorFlow2.4 Pycharm
> 

需要将kears模型转换为onnx格式,以供OpenCV调用

## 方案一 （不适用于我的情况，不排除其可行性）

```
import keras
import keras2onnx
import onnx
from tensorflow.keras.models import load_model
model = load_model('REDPropiaFinal.h5')
onnx_model = keras2onnx.convert_keras(model, model.name)
temp_model_file = 'REDPropiaFinal.onnx'
onnx.save_model(onnx_model, temp_model_file)

```

## 方案二

```
# Install helper packages:
!pip install tf2onnx onnx onnxruntime

# Load model from .h5 and save as Saved Model:
import tensorflow as tf
model = tf.keras.models.load_model("REDPropiaFinal.h5")
tf.saved_model.save(model, "tmp_model")

# Convert in bash:
!python -m tf2onnx.convert --saved-model tmp_model --output "REDPropiaFinal.onnx"

```
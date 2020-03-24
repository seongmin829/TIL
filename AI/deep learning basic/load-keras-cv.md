# load-keras-cv

* header

```python
import numpy as np
import tensorflow as tf
import matplotlib.pyplot as plt
%matplotlib inline
from keras import backend as K
```

```python

X = np.array([[0,0], [0,1], [1,0], [1,1]], 'float32')
Y = np.array([[0], [1], [1], [0]], 'float32')

# tensorflow 안에 있는 keras를 이용하는 것이다.
# model = keras.models.Sequential() : 이 model은 별도로 설치한 keras 페키지를 이용한 것이다.
model = tf.keras.models.Sequential()
model.add(tf.keras.layers.Dense(64, input_dim=2, activation='relu'))
model.add(tf.keras.layers.Dense(64, activation='relu'))

model.add(tf.keras.layers.Dense(64, activation='relu'))
model.add(tf.keras.layers.Dense(64, activation='relu'))
model.add(tf.keras.layers.Dense(1, activation ='sigmoid'))


model.compile(loss='mean_squared_error', optimizer='adam', metrics=['binary_accuracy'])

model.fit(X, Y, batch_size=1, epochs=100, verbose=0)


model.summary()

# inputs:  ['dense_input']
print('inputs: ', [input.op.name for input in model.inputs])

# outputs:  ['dense_4/Sigmoid']
print('outputs: ', [output.op.name for output in model.outputs])




model.save('xor.h5')
```

```
Model: "sequential_1"
_________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
dense_5 (Dense)              (None, 64)                192       
_________________________________________________________________
dense_6 (Dense)              (None, 64)                4160      
_________________________________________________________________
dense_7 (Dense)              (None, 64)                4160      
_________________________________________________________________
dense_8 (Dense)              (None, 64)                4160      
_________________________________________________________________
output (Dense)               (None, 1)                 65        
=================================================================
Total params: 12,737
Trainable params: 12,737
Non-trainable params: 0
_________________________________________________________________
inputs:  ['dense_5_input']
outputs:  ['output/Sigmoid']
```

---

---

```python
model.predict(np.array([[1, 1], [0,1]]))
```

```
array([[0.00502291],
       [0.9924418 ]], dtype=float32)
```

---

---

```python
from tensorflow.compat.v1.keras import backend as K
# tensorflow 2.0 version에서 1.0 version의 함수 사용하기 위해 밑의 2줄을 쓴다. / 버전간의 충돌 방지하기 위해
import tensorflow.compat.v1 as tf
tf.disable_v2_behavior()


def freeze_session(session, keep_var_names=None, output_names=None, clear_devices=True):
    """
    Freezes the state of a session into a pruned computation graph.

    Creates a new computation graph where variable nodes are replaced by
    constants taking their current value in the session. The new graph will be
    pruned so subgraphs that are not necessary to compute the requested
    outputs are removed.
    @param session The TensorFlow session to be frozen.
    @param keep_var_names A list of variable names that should not be frozen,
                          or None to freeze all the variables in the graph.
    @param output_names Names of the relevant graph outputs.
    @param clear_devices Remove the device directives from the graph for better portability.
    @return The frozen graph definition.
    """
    graph = session.graph
    with graph.as_default():
        freeze_var_names = list(set(v.op.name for v in tf.global_variables()).difference(keep_var_names or []))
        output_names = output_names or []
        output_names += [v.op.name for v in tf.global_variables()]
        input_graph_def = graph.as_graph_def()
        if clear_devices:
            for node in input_graph_def.node:
                node.device = ''
        frozen_graph = tf.graph_util.convert_variables_to_constants(
            session, input_graph_def, output_names, freeze_var_names)
        return frozen_graph


    
frozen_graph = freeze_session(K.get_session(), output_names=[out.op.name for out in model.outputs])
# text file로 저장
tf.train.write_graph(frozen_graph, './', 'xor.pbtxt', as_text=True)
# binary file로 저장
tf.train.write_graph(frozen_graph, './', 'xor.pb', as_text=False)
```

```
import cv2 as cv2
net = cv2.dnn.readNetFromTensorflow('xor.pb')
layersNames = net.getLayerNames()
print(layersNames)
```

```
['dense_5/MatMul', 'dense_5/Relu', 'dense_6/MatMul', 'dense_6/Relu', 'dense_7/MatMul', 'dense_7/Relu', 'dense_8/MatMul', 'dense_8/Relu', 'output/MatMul', 'output/Sigmoid']
```

```python
net.setInput(np.array([[0, 0], [0,1], [1,0], [1,1]]))
# outputName에 지정한 layer까지 진행 / 마지막 layer까지 진행하고 싶을떄는 파라미터에 입력없이 실행하면 된다.
# sigmoid 층에서 name을 ouput으로 고정했으므로 항상 'output/Sigmoid' 이다.
out = net.forward(outputName='output/Sigmoid')
print(out)

out = net.forward()
print(out)
```

```
[[2.1367662e-02]
 [9.9165678e-01]
 [9.9118757e-01]
 [9.2209276e-04]]
[[2.1367662e-02]
 [9.9165678e-01]
 [9.9118757e-01]
 [9.2209276e-04]]
```

```python
out = net.forward(['dense_6/Relu', 'output/Sigmoid'])
print(out[0])
print(out[1])
```

```
[[-0.00000000e+00  9.90552753e-02  1.78584427e-01  1.77400410e-01
  -0.00000000e+00 -0.00000000e+00 -0.00000000e+00  2.15022892e-01
  -0.00000000e+00 -0.00000000e+00 -0.00000000e+00  1.95354879e-01
   1.20628923e-01 -0.00000000e+00  7.85159394e-02  4.11529094e-04
  -0.00000000e+00  1.63979441e-01 -0.00000000e+00  1.17287450e-01
   1.78461283e-01  1.68580934e-01  1.74877197e-01  1.64824232e-01
   2.59707812e-02  2.23327294e-01 -0.00000000e+00 -0.00000000e+00
   6.84478208e-02  1.19927816e-01 -0.00000000e+00  2.94003170e-03
  -0.00000000e+00 -0.00000000e+00 -0.00000000e+00 -0.00000000e+00
   1.43053219e-01 -0.00000000e+00  1.17067635e-01 -0.00000000e+00
   1.84074417e-03  1.19092874e-02 -0.00000000e+00  2.02682167e-01
  -0.00000000e+00  9.81397629e-02 -0.00000000e+00  2.08050027e-01
   2.54851170e-02  1.10773101e-01  1.18320823e-01  1.65842891e-01
   7.88074434e-02 -0.00000000e+00 -0.00000000e+00  1.92170978e-01
   6.59693033e-05  1.34697914e-01  1.16468325e-01  3.93874943e-05
   1.58496708e-01 -0.00000000e+00  2.11288035e-03  2.09922209e-01]
 [-0.00000000e+00 -0.00000000e+00  4.26753223e-01  1.45763040e-01
   3.30513686e-01 -0.00000000e+00 -0.00000000e+00  6.88173831e-01
  -0.00000000e+00 -0.00000000e+00 -0.00000000e+00  6.71281695e-01
  -0.00000000e+00 -0.00000000e+00  4.30164456e-01  3.55577976e-01
  -0.00000000e+00  6.61955416e-01 -0.00000000e+00  3.87533158e-01
   1.66669488e-05  3.63588333e-06  3.51932138e-01  6.08676910e-01
   5.70405602e-01  7.86707103e-01 -0.00000000e+00 -0.00000000e+00
   2.09848225e-01  6.52947307e-01 -0.00000000e+00  1.11520231e-01
  -0.00000000e+00 -0.00000000e+00 -0.00000000e+00 -0.00000000e+00
   8.03693607e-02 -0.00000000e+00 -0.00000000e+00  4.99213785e-01
   3.58073145e-01  2.91982383e-01 -0.00000000e+00  3.14257294e-03
  -0.00000000e+00  5.77483058e-01  1.22468665e-01  5.52782774e-01
   2.20268533e-01  1.79484487e-05  7.61210248e-02  7.32105821e-02
  -0.00000000e+00 -0.00000000e+00 -0.00000000e+00  1.03682950e-02
  -0.00000000e+00  4.02947158e-01  3.44359636e-01 -0.00000000e+00
   4.67333823e-01  5.35905361e-01  1.08561918e-01  2.36943215e-01]
 [-0.00000000e+00  4.68906760e-01  1.11193359e-02  6.60675645e-01
  -0.00000000e+00 -0.00000000e+00 -0.00000000e+00  7.91767985e-03
   5.68293095e-01  3.85422632e-02 -0.00000000e+00  5.68234436e-02
   5.52176952e-01 -0.00000000e+00 -0.00000000e+00  2.55519897e-01
  -0.00000000e+00  3.19090366e-01 -0.00000000e+00  7.51749873e-02
   5.94333053e-01  2.19588161e-01  2.53086016e-02  1.09158754e-02
  -0.00000000e+00  5.45289040e-01  4.27628636e-01 -0.00000000e+00
   4.43938792e-01 -0.00000000e+00 -0.00000000e+00  2.07240552e-01
  -0.00000000e+00  3.16590488e-01 -0.00000000e+00 -0.00000000e+00
   1.04988068e-02 -0.00000000e+00  2.15133578e-02 -0.00000000e+00
  -0.00000000e+00  7.93121755e-04  3.85795563e-01  3.15173686e-01
  -0.00000000e+00 -0.00000000e+00  2.43033543e-01  2.18493238e-01
   3.26306701e-01  5.41619599e-01  4.32365276e-02  3.97147089e-02
   1.77126437e-01 -0.00000000e+00 -0.00000000e+00  7.40694404e-01
   4.81812388e-01  4.42386031e-01  1.17081553e-02  3.79341513e-01
   5.50930798e-01 -0.00000000e+00 -0.00000000e+00  4.49122876e-01]
 [-0.00000000e+00  2.54791766e-01  2.53196001e-01  5.08099496e-01
   1.36708558e-01 -0.00000000e+00 -0.00000000e+00  3.02300245e-01
   8.26737881e-02 -0.00000000e+00 -0.00000000e+00  4.88714278e-01
   3.19773883e-01 -0.00000000e+00  5.11396170e-01  6.00170195e-02
  -0.00000000e+00  5.81830323e-01 -0.00000000e+00  3.35056871e-01
   5.93065977e-01  3.41772884e-01  4.05309230e-01  3.99405390e-01
   4.14331555e-02  7.07394004e-01  1.02487998e-02 -0.00000000e+00
   1.56539857e-01  3.56887758e-01 -0.00000000e+00  4.75014187e-02
  -0.00000000e+00  7.08262399e-02 -0.00000000e+00 -0.00000000e+00
   3.30068469e-01 -0.00000000e+00 -0.00000000e+00  2.64098123e-02
  -0.00000000e+00  5.25248833e-02 -0.00000000e+00  6.17439449e-01
  -0.00000000e+00  1.58244044e-01  4.79886681e-02  6.29020572e-01
   1.73533678e-01  3.27759266e-01  3.48514915e-01  3.50143343e-01
   3.22951615e-01 -0.00000000e+00 -0.00000000e+00  5.52015603e-01
   7.33019225e-03  2.95215636e-01  4.09220219e-01  2.53375228e-02
   4.22090203e-01  3.65187041e-02  1.67790465e-02  4.69320357e-01]]
[[2.1367662e-02]
 [9.9165678e-01]
 [9.9118757e-01]
 [9.2209276e-04]]
```


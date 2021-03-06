# 로지스틱 회귀 (logistic regression:classification)

## [모두의 머신러닝 Lab 5](https://www.youtube.com/watch?v=t7Y9luCNzzE&feature=youtu.be)

로지스틱 회귀는 입력 데이터를 줬을 때 해당 데이터의 결과를 특정 분류로 나누는 함수를 나타냅니다. 그러므로 classification 기법이라고도 부릅니다.

이번 학습 목표는 [모두의 머신러닝](https://www.youtube.com/watch?v=t7Y9luCNzzE&feature=youtu.be)의 완성된 코드를 기반으로 응용할 수 있도록 합니다.

```python
import tensorflow as tf
import numpy as np

xy = np.loadtxt('train.txt', unpack=True, dtype='float32')
x_data = xy[0:-1]
y_data = xy[-1];

X = tf.placeholder(tf.float32)
Y = tf.placeholder(tf.float32)

W = tf.Variable(tf.random_uniform([1,len(x_data)], -1.0, 1.0))

# Our hypothesis
h = tf.matmul(W, X)
hypothesis = tf.div(1., 1.+tf.exp(-h))

# cost function
cost = -tf.reduce_mean(Y*tf.log(hypothesis) + (1-Y)*tf.log(1-hypothesis))

# Minimize
a = tf.Variable(0.1) # Learning rate, alpha
optimizer = tf.train.GradientDescentOptimizer(a)
train = optimizer.minimize(cost)

# Before starting, initialize the variables. We will 'run' this first.
init = tf.initialize_all_variables()

# Launch the graph
sess = tf.Session()
sess.run(init)

# Fit the line.
for step in xrange(2001):
    sess.run(train, feed_dict={X:x_data, Y:y_data})
    if step % 20 == 0:
        print step, sess.run(cost, feed_dict={X:x_data, Y:y_data}), sess.run(W)
```

train.txt의 내용은 아래와 같습니다.
```bash
$ cat train.txt
#x0 x1  x2  y
1   2   1   0
1   3   2   0
1   3   4   0
1   5   5   1
1   7   5   1
1   2   5   1
```

### 학습 데이터 테스트
학습을 마친 후 새로운 데이터에 대해 정확한 분류가 되는지 확인합니다.
```python
print '---------------------------------------'
# study_hour attendance
print sess.run(hypothesis, feed_dict={X:[[1], [2], [2]]}) > 0.5
print sess.run(hypothesis, feed_dict={X:[[1], [5], [5]]}) > 0.5

print sess.run(hypothesis, feed_dict={X:[[1, 1], [4, 3], [3, 5]]}) > 0.5
```

아래처럼 결과가 출력되는 것을 확인 할 수 있습니다.
```bash
---------------------------------------
[[False]]
[[ True]]
[[False  True]]
```

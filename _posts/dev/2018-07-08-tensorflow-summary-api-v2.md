---
layout: post
title: Tensorflow Summary API V2
categories: dev
tags: tensorflow
---

Following the announcement in Tensorflow Dev Summit 2018 in Eager Execution part: <https://www.youtube.com/watch?v=T8AW0fKP0Hs&vl=en>

It suggests that we should use `tf.contrib.summary` . Here is the link to the documentation <https://www.tensorflow.org/api_docs/python/tf/contrib/summary>

To me, it is not a very good documentation and hard to follow. I have here some example codes to guide you to the new summary API.

I will try to cover both modes, eager and graph. I shall stick with the graph mode and then add some notes for the eager mode.

Overall code template will be put at the end of this article.

### Creating the summary writer

This is the same with both graph mode and eager mode.

Since the new summary API is very context based, I think it is a good practice to separate graph for each summary writer.

Initializing the summary:

```python
import tensorflow as tf
save_path = 'logs'
graph = tf.Graph()
with graph.as_default():
    global_step = tf.train.create_global_step()
    writer = tf.contrib.summary.create_file_writer(save_path)
    with writer.as_default():
        tf.contrib.summary.always_record_summaries()
```

Now, we have `graph` and `writer` .

Note: I usually also create a `global_step` with the creation of graph. Which is very convenient because the summary writer will automatically utilize it. You don’t really need to keep the `global_step` though, you can always get it from `tf.train.get_global_step()` given that you properly set the default graph.

### Preparing the dataset and creating the model

This is not the main topic of this article though, but for the sake of expressiveness I found it useful for explanation.

First we prepare the dataset, I will demonstrate with `tf.data.Dataset` here.

```python
with graph.as_default():
    # dataset
    data = ... some tf.data.Dataset ...
    data_itr = data.make_initializable_iterator()
    x, y = data_itr.get_next() # x is feature, y is label
```

Note that in eager mode you don’t need the `make_initializable_iterator()`because the dataset is itself iterable. That means in eager you don’t need `get_next()` as well.

#### At the time we define model, we add something to summary

The following code is in graph mode:

```python
with graph.as_default():
    with writer.as_default():
        # let's say we have "net" as our model
        prediction_op = net(x)
        loss_op = tf.losses.sparse_softmax_cross_entropy(y, prediction_op)
        opt_op = tf.train.AdamOptimizer(0.001).minimize(
            loss_op, 
            global_step=tf.train.get_global_step())
        
with tf.contrib.summary.record_summaries_every_n_global_steps(1):
            tf.contrib.summary.scalar('loss', loss_op)
            
        summary_op = tf.contrib.summary.all_summary_ops()
```

You see that we use `tf.contrib.summary.record_summaries_every_n_global_steps` (<https://www.tensorflow.org/api_docs/python/tf/contrib/summary/record_summaries_every_n_global_steps>) to log anything inside the context manager every “n” steps.

In eager you might consider creating a function for running in each iteration like so:

```python
def train(net, optimizer, x, y):
    with tf.contrib.eager.GradientTape() as tape:
        prediction = net(x)
        loss = tf.losses.sparse_softmax_cross_entropy(y, prediction)
    
    grads = tape.gradient(loss, net.variables)
    grads_vars = zip(grads, net.variables)
    optimizer.apply_gradients(
        grads_vars,
        global_step=tf.train.get_global_step()
    )
    
    # here is how you log every step (n=1)
    with tf.contrib.summary.record_summaries_every_n_global_steps(1):
        tf.contrib.summary.scalar('loss', loss)
        
    return loss
```

### At the time of running

Here is the code for graph mode:

```python
with graph.as_default():
    with writer.as_default():
        with tf.Session() as sess:
            # initialize the summary
            tf.contrib.summary.initialize(
                graph=tf.get_default_graph()
            )
            # init vars
            sess.run(tf.global_variables_initializer())
            # init iterator
            sess.run(data_itr.initializer)
            # run 
            while True:
                try:
                    _, _, loss = sess.run([
                        summary_op, opt_op, loss_op
                    ])
                    ...
                except tf.errors.OutOfRangeError:
                    break
```

Two things to keep in mind:

- `tf.contrib.summary.initialize(graph=...)` — You can supply “None” to the graph (leave it blank), it won’t save the graph to the Tensorboard. However, this option won’t have any effect in the eager mode anyway (since it doesn’t really construct a graph)
- You need to run `tf.contrib.summary.all_summary_ops()` which will actually write the summary. However, you don’t need this for eager mode since it’s executed in real time anyway

#### To summarize

- Create a summary writer under a graph, and use that graph throughout
- Define what you want to include in the summary and how frequent
- Initialize the summary (you might supply the graph here if you want to get the “Graph” page in Tensorboard, won’t work in eager)
- In graph mode, also run `tf.contrib.summary.all_summary_ops()` to actually write the summary

#### An example of graph mode

```python
import tensorflow as tf
import numpy as np

save_path = 'summary_path'
graph = tf.Graph()
with graph.as_default():
    global_step = tf.train.create_global_step()
    writer = tf.contrib.summary.create_file_writer(save_path)
    with writer.as_default():
        tf.contrib.summary.always_record_summaries()
        
# simulate dataset
fake_dataset = np.random.randn(1000, 100).astype(np.float32)
fake_label = np.random.randint(low=0, high=9, size=1000)
# preparing a fake dataset
with graph.as_default():
    x = tf.data.Dataset.from_tensor_slices(fake_dataset)
    y = tf.data.Dataset.from_tensor_slices(fake_label)
    data = tf.data.Dataset.zip((x, y))
    data = data.shuffle(10000)
    data = data.batch(32)    
    data_itr = data.make_initializable_iterator()
    x, y = data_itr.get_next()

# define the computing graph
with graph.as_default():
    with writer.as_default():
        # construct a simple classifier
        net = tf.keras.Sequential([
            tf.keras.layers.Dense(300, activation=tf.nn.relu),
            tf.keras.layers.Dense(10)
        ])
        
        prediction_op = net(x)
        loss_op = tf.losses.sparse_softmax_cross_entropy(y, prediction_op)
        opt_op = tf.train.AdamOptimizer(0.001).minimize(
            loss_op, 
            global_step=tf.train.get_global_step())
        
        # here is how you log every step (n=1)
        with tf.contrib.summary.record_summaries_every_n_global_steps(1):
            tf.contrib.summary.scalar('loss', loss_op)
            
        summary_op = tf.contrib.summary.all_summary_ops()
        
# compute the graph
with graph.as_default():
    with writer.as_default():
        with tf.Session() as sess:
            # initialize the summary writer
            tf.contrib.summary.initialize(
                graph=tf.get_default_graph()
            )
            # init vars
            sess.run(tf.global_variables_initializer())
            # init iterator
            sess.run(data_itr.initializer)
            # run until the dataset is exhausted
            while True:
                try:
                    _, _, loss = sess.run([
                        summary_op, opt_op, loss_op
                    ])
                except tf.errors.OutOfRangeError:
                    break
```

#### An example of eager mode

```python
import tensorflow as tf
import numpy as np

tf.enable_eager_execution()

save_path = 'logs/test10'
graph = tf.Graph()
with graph.as_default():
    global_step = tf.train.create_global_step()
    writer = tf.contrib.summary.create_file_writer(save_path)
    with writer.as_default():
        tf.contrib.summary.always_record_summaries()
        
# simulate dataset
fake_dataset = np.random.randn(1000, 100).astype(np.float32)
fake_label = np.random.randint(low=0, high=9, size=1000)
# preparing a fake dataset
with graph.as_default():
    x = tf.data.Dataset.from_tensor_slices(fake_dataset)
    y = tf.data.Dataset.from_tensor_slices(fake_label)
    data = tf.data.Dataset.zip((x, y))
    data = data.shuffle(10000)
    data = data.batch(32)    

# define the model
with graph.as_default():
    with writer.as_default():
        # construct a simple classifier
        net = tf.keras.Sequential([
            tf.keras.layers.Dense(300, activation=tf.nn.relu),
            tf.keras.layers.Dense(10)
        ])
        
        optimizer = tf.train.AdamOptimizer(0.001)
        
def train(net, optimizer, x, y):
    with tf.contrib.eager.GradientTape() as tape:
        prediction = net(x)
        loss = tf.losses.sparse_softmax_cross_entropy(y, prediction)
    
    grads = tape.gradient(loss, net.variables)
    grads_vars = zip(grads, net.variables)
    optimizer.apply_gradients(
        grads_vars,
        global_step=tf.train.get_global_step()
    )
    
    # here is how you log every step (n=1)
    with tf.contrib.summary.record_summaries_every_n_global_steps(1):
        tf.contrib.summary.scalar('loss', loss)
        
    return loss
        
# start the training process
with graph.as_default():
    with writer.as_default():
        # initialize the summary writer
        tf.contrib.summary.initialize()
        # run until the dataset is exhausted
        for x, y in data:
            loss = train(net, optimizer, x, y)
            print(float(loss))
```


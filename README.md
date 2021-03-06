## LSNN: Long short-term memory Spiking Neural Networks

This repository provides a tensorflow 1.12 library and a tutorial to train a recurrent spiking neural networks (ours is called LSNN).
For more details about LSNN see [1]. This model uses a method of network rewiring to keep a sparse connectivity during training, this method is called DEEP R and is described in [2]. Feel free to cite us if that's relevant.

## Usage
After installation load the LSNN library, load our implementation of the ALIF tensorflow cell (adaptive leaky integrate and fire), and use it in place of standard tensorflow 1.12 rnn cells:

```python
import tensorflow as tf  
import lsnn  

...

cell =  lsnn.ALIF(num_of_inputs, num_of_outputs, ...)
outputs, final_state = tf.nn.dynamic_rnn(cell, inputs, dtype=tf.float32)

spikes = outputs[0]
loss(spikes)
```

In the folder `lsnn/` you may find the source code of the lsnn package. In the folder `bin/` there is a selection of tutorials. With `tutorial_sequential_mnist_with_LSNN.py`, you can reproduce Figure 1B from [1] and achieve above __96%__ accuracy on the __sequential MNIST__ task with an LSNN (the code may not reproduce exactlty the results published in [1]).  

The second tutorial `tutorial_storerecall_with_LSNN.py` solves a simpler task that runs in few minutes with CPUs. This simpler task is a good test to verify if a network model has long-short term memory. For a detailed description about the task we refer to the older version (v1) of the preprint [3].

## Installation

The code is compatible with python 3.4 to 3.7 and tensorflow 1.7 to 1.12 (CPU and GPU versions).

> You can run the training scripts **without installation** by temporarily including the repo directory
> in your python path like so: `` PYTHONPATH=. python3 bin/tutorial_sequential_mnist_with_LSNN.py`` 

From the main folder run:  
`` pip3 install --user .``  
You can now import the tensorflow cell called ALIF (for adaptive leakey integrate and fire) as well as the rewiring wrapper to update connectivity matrices after each call to the optimizer.
Warning, the GPU compatible version of tensorflow is not part of the requirements by default.
To use GPUs one should also install it:
 ``pip3 install --user tensorflow-gpu``.

## Troubleshooting

### Possible incompatibility with tf.variable_scope(..., reuse=??)
The LIF and ALIF cells defined in `spiking_models.py` behave almost like any tensorflow cell. However, we define the tensorflow variables during initilization of the cell object instead of using the high level function `get_variables(...)` or `add_weights(...)` in Keras.  

This leads to some incompatibility with some deep learning frameworks because two objects of our cell classes will not share the same parameters using if they share the variable scope. To fix it, we recommend rewriting the cell definition in the style of Keras and tensorflow 2 rnn cells, but this solution is likely to break the compatibility with the rewiring package.

### Wrong compilation of tensorflow

If the scripts fail with the following error:
`` Illegal instruction (core dumped) ``

It is most probably due to the lack of AVX instructions on the machine you are using.
A known workaround is to reinstall the LSNN package with older tensorflow version (1.5).
Change requirements.txt to contain:

`` tensorflow==1.5 ``

## Credits

The code was written by Guillaume Bellec and Darjan Salaj at the IGI institute of TU Graz between 2017 and 2018. Anand Subramoney and Arjun Rao helped to improve the implementation.

[1] Long short-term memory and Learning-to-learn in networks of spiking neurons  
Guillaume Bellec*, Darjan Salaj*, Anand Subramoney*, Robert Legenstein, Wolfgang Maass  
NIPS 2018, [[link]](http://papers.nips.cc/paper/7359-long-short-term-memory-and-learning-to-learn-in-networks-of-spiking-neurons) [[arxiv]](https://arxiv.org/abs/1803.09574  )
(\* equal contributions)

[2] Deep Rewiring: Training very sparse deep networks  
Guillaume Bellec, David Kappel, Wolfgang Maass, Robert Legenstein  
ICLR 2018, [[link]](https://openreview.net/forum?id=BJ_wN01C-) [[arxiv]](https://arxiv.org/abs/1711.05136)

[3] Biologically inspired alternatives to backpropagation through time for learning in recurrent neural nets  
Guillaume Bellec*, Franz Scherr*, Elias Hajek, Darjan Salaj, Robert Legenstein, and Wolfgang Maass  
arxiv 2019 [[arxiv]](https://arxiv.org/abs/1901.09049) [[v1 with store recall task]](https://arxiv.org/pdf/1901.09049v1.pdf)

## Citing LSNN

```
@inproceedings{bellec2018long,
  title={Long short-term memory and learning-to-learn in networks of spiking neurons},
  author={Bellec, Guillaume and Salaj, Darjan and Subramoney, Anand and Legenstein, Robert and Maass, Wolfgang},
  booktitle={Advances in Neural Information Processing Systems},
  pages={787--797},
  year={2018}
}
```

## License

LSNN-official is open source software and is licensed under the [BSD 3-Clause Clear License](https://spdx.org/licenses/BSD-3-Clause-Clear.html).

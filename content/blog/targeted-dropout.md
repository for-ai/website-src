+++
    title = 'Targeted Dropout'
    date = '2019-06-03'
    imgSrc = ''
+++
<div class="blog-links"><a href="https://arxiv.org/pdf/1905.13678.pdf">Arxiv</a><a href="https://github.com/for-ai/TD">Github</a></div>
<p>
    Neural networks can represent functions to solve complex tasks that are difficult &mdash;
    if not impossible &mdash; to write instructions for by hand, such as understanding language and
    recognizing objects. Conveniently, we’ve seen that task performance increases with
    as we use larger networks. However, computational costs for parameter sizes also increase in
    dollars and latency required to train and use models. Practitioners are plagued with networks
    being too large to store in device memory, or too slow for real-world utility.
</p>
<h2>Networks are overparameterized</h2>
<p>
    Progress made in network sparsification has presented post-hoc (after training) methods that allow us
    to remove parameters in a neural network and then fine tune the leftover parameters to achieve a similar
    task performance. The trade off here is that it makes the process of training and preparing a model for
    evaluation more complex -- requiring surgical procedures for the networks and task performance decreases.
    Optimistically, this does suggest that neural networks can still represent interesting functions without all
    of its parameters. And with smaller parameter sizes, we can reduce the costs of computing with neural networks. Unfortunately, for many real world use cases, task performance is a non-negotiable cost.
</p>
<p>
    <a href="https://arxiv.org/abs/1803.03635">The Lottery Ticket Hypothesis</a> presents the idea that
    every function can be represented by a “winning ticket” subnetwork and a larger parameter size simply
    gives us more lottery tickets. The authors showed that they were able to recover the winning ticket
    using a weight magnitude-based analysis. A winning ticket retrained with its weights reset to its
    initialized value achieves task performance competitive with the full network. This idea suggests that
    if we can sparsify a network by removing only unimportant weights, then we can achieve a smaller parameter
    size without a task performance sacrifice.
</p>
<h2>Understanding parameter importance</h2>
<p>
    To take advantage of the ideas we've learned so far, we have to understand how to judge the importance of weights.
    To do this we can study the coadaptation matrix of the neural network's parameters after its done training.
</p>
<div>
    <img src="/img/targeted-dropout/comtx1.png" style="width:350px;display:block;margin:0 auto;"/><img src="/img/targeted-dropout/coeq.png" style="width:200px;display:block;margin:0 auto;"/>
</div>
<p>
    Position (i, j) of the matrix represents the dependence of parameter i on parameter j. Pruning a set of
    weights {l, m, n} will change the loss proportional to the sum of the l, m, and nth columns. The weights
    are ordered by decreasing magnitude from left to right. As we can see from this matrix, the neural network
    still has some dependence on low magnitude weights, which suggests that sparisfying based on weight magnitude
    is not enough.
</p>
<h2>Targeted Dropout</h2>
<p>
    This leads to Targeted Dropout, our approach for identifying the winning ticket and controlling its size.
    At every forward pass during training, we apply stochastic dropout to some “targeted portion” of the network,
    which are the least important parameters (losing tickets), based on some approximation of importance
    (i.e. weight magnitude, unit norm). After training, we can remove the least important parameters because
    the network is no longer dependent on the targeted portion. Finally, we are left with a network containing
    only the winning ticket.
</p>
<p>
    Let's study the coadaptation matrix of a network after its trained with targeted dropout.
    In this case, we set the targeted portion to be 75%, because we hope to use only 25% of the
    parameters after training. It is clear that all the high magnitude weights are only dependent
    on one another. Since this is the case, we can safely remove 75% of the least important parameters
    without removing any dependence amongst weights.
</p>
<div><img src="/img/targeted-dropout/comtx2.png" style="width:350px;display:block;margin:0 auto;"/></div>
<p>
    Empirically we saw that our technique is generally applicable to different architectures
    (ResNet32, VGG, Transformer) solving different task domains (translation, image classification).
    Networks trained with TD were extremely robust to post-hoc pruning. With an additional adaptation of
    gradually increasing the target portion during training, we can achieve up to 99.95% sparsity
    while only losing around 3-4% in task performance. More results and hyperparameters can be reviewed in the Experiments section of our paper.
</p>
<h2>Implementation</h2>
<p>The algorithm is simple to implement and can be applied to a networks weights or units:</p>
<div><img src="/img/targeted-dropout/tdsteps.png" style="width:100%;display:block;"/></div>
<br><br>
<p>Reference implementation for weight-level targeted dropout written in Tensorflow:</p>
<script src="https://gist.github.com/targeted-dropout/98445945a21a78531d3313599f5b3390.js"></script>
<h2>Conclusion</h2>
<p>
    Lottery Ticket Hypothesis inspired a new way to think about neural networks.
    We've shown with Targeted Dropout that we can manipulate the size of this winning lottery ticket.
    Targeted dropout is a regularization technique that generalizes across architectures and
    is simple to implement. We hope to see more progress in the field of network sparsification
    so that neural networks can see more application in the real world, and so that we can develop
    a better understanding of measuring parameter importance.
</p>

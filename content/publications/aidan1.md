+++
    title = 'The Reversible Residual Network: Backpropagation Without Storing Activations'
    authors = 'Aidan N. Gomez, Mengye Ren, Raquel Urtasun, Roger B. Grosse'
    conference = 'NIPS 2017'
    abstract = "Deep residual networks (ResNets) have significantly pushed forward the state-of-the-art on image classification, increasing in performance as networks grow both deeper and wider. However, memory consumption becomes a bottleneck, as one needs to store the activations in order to calculate gradients using backpropagation. We present the Reversible Residual Network (RevNet), a variant of ResNets where each layer's activations can be reconstructed exactly from the next layer's. Therefore, the activations for most layers need not be stored in memory during backpropagation. We demonstrate the effectiveness of RevNets on CIFAR-10, CIFAR-100, and ImageNet, establishing nearly identical classification accuracy to equally-sized ResNets, even though the activation storage requirements are independent of depth."
    link='https://arxiv.org/abs/1707.04585'
    code='https://github.com/renmengye/revnet-public'
+++

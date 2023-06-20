# Divergent: a novel defence against Website Fingerprinting attacks

![div_logo](/images/divergent/logo_divergent.png)

Hi guys, in this blog we are going to discover a project that kept me busy for quite long, that is my master project.
It consists of realizing a new, efficient **defence** against a not so pleasant threat, named **Website fingerprintg (WF) attacks**.
And this mechanism is called **Divergent**, let's dive in it.

## Background

When we, as web users, surf the net through a browser, we hope that our privacy is preserved, above all when we are using internet in order to request sensitive data, such as healthcare or financial information.
However, this context not always is applied, due to a threat that aims at leaking such confidential data, exploiting the network channel that we, and the other endpoint, generate.

So what's a WF attack? First of all, it's a type of *traffic analysis* attack. And how does it work? Let's discover it.


## WF Attack

Suppose a user, Bob, is surfing the net through a public WiFi (e.g., in a Starbuck shop). In the meanwhile, a local eavesdropper, Eve, is intercepting his network traffic. Although the traffic is encrypted, each data-trace can be associated to features that create such a pattern as unique. Some example of features can be the loading time or the number of packets exchanged.

This type of information, also called *side-channel* information, can be exploited in order to fingerprint the web page or the resource requested by the user, leading to a massive *privacy threat*.

![wf_attack_scenario](/images/divergent/wf_attack_scenario.png)

It is worth mentioning that this king of attack can be performed in both *traditional* and *anonymyzed* networks (such as [Tor](https://www.torproject.org/about/history/)).

So, a WF attack can be defined as a *supervised classification problem*, in which an adversarial tries to figure out which resources the victim is asking for. All of this can be realized in many different forms, but the most efficient one is - guess what? - through machine/deep learning models.

In literature we can find several typologies of such models, for example: k-nn classifiers, SVMs and, finally, the best one we can find in literature, that exploits Convolutional Neural Networks and achieves more
than 98% of accuracy against undefended traffic flows.

## CNN

Convolutational Neural Networks are a particular Deep Learning model, which is specialized in dealing with images and videos (that is, grid-like structures elements). Note that a traffic of data packets can be expressed like a matrix, where each row represents the feature considered.

![cnn](/images/divergent/cnn-model)

If you want to know more about them, give a look [here](https://www.simplilearn.com/tutorials/deep-learning-tutorial/convolutional-neural-network).

## WF Defence





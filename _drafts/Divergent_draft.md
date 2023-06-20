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

![cnn](/images/divergent/cnn-model.png)

If you want to know more about them, give a look [here](https://www.simplilearn.com/tutorials/deep-learning-tutorial/convolutional-neural-network).


## WF Defence

![defence](/images/divergent/defence.png)

Now that we know how the attack works, how can we defend against it? What do we need to disrupt and bypass such a threat?

The naive way is by sending all the packets with the same rate and of the same size, so that each network connection appears equal to all the others. But this idea, of course, introduce too much overhead. So we must think in a more functional and effective mechanism. 

There are several ways, but let's see some of the features Divergent implements.

The first method is to *delay packets*, so that the time feature, exploited by the adversarial model, is messed up.

Then, make packets (or burst of packets) as equal as possible in size, keeping in mind the efficiency of the communication.

Finally, injecting dummy packets in order to change the traffic flow strictly bound to a web resource.

![wf_defences](/images/divergent/wf_defences.png)

### Literature proposals

Here we mention some of the defences that have been proposed in the literature.

*BuFLO* is one of the first defences and apply the most direct method: make the traffic almost equal by padding packets to equal size and at a constant rate.

*Traffic morphing* consists in turning a traffic trace associated to a web resource into a flow of another one.

*GLUE* exploits the so-called split decision problem. That is, researchers showed that it is difficult to split two web-pages loading, especially when they overlap each other.

*Walkie-talkie* changes from a full to a half-duplex communication and simulates the loading of two web-pages, by sending the supersequence of the two resources.

Finally, *TrafficSliver*, which is worth mentioning it since it exploits randomness in the Tor routing algorithm, and not directly in the traffic network.







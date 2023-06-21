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

There are several ways, but let's see the features that characterize Divergent (and are present in some works).

The first method is to *delay packets*, so that the time feature, exploited by the adversarial model, is messed up.

Then, make packets (or burst of packets) as equal as possible in size, keeping in mind the efficiency of the communication.

Also, randomness is a crucial aspect in our defence.

Finally, injecting dummy packets in order to change the traffic flow strictly bound to a web resource.

![wf_defences](/images/divergent/wf_defences.png)

### Literature proposals

Here we mention some of the defences that have been proposed in the literature.

*BuFLO* is one of the first defences and apply the most direct method: make the traffic almost equal by padding packets to equal size and at a constant rate.

*Traffic morphing* consists in turning a traffic trace associated to a web resource into a flow of another one.

*GLUE* exploits the so-called split decision problem. That is, researchers showed that it is difficult to split two web-pages loading, especially when they overlap each other.

*Walkie-talkie* changes from a full to a half-duplex communication and simulates the loading of two web-pages, by sending the supersequence of the two resources.

Finally, *TrafficSliver*, which is worth mentioning it since it exploits randomness in the Tor routing algorithm, and not directly in the traffic network.


## Adversarial model

Very good, now, in order to re-create a valid adversarial user, we implemented our CNN threat model. It consists of 
* the input layer;
* 5 convolutional layers;
* 4 dropout layers;
* 1 max-pooling layer;
* 2 dense layers;
* the output layer.

The activation function employed in the output is the *softmax* one, for two reasons: it fits perfectly for multi-class classification problems, and because it converts the input-array of values into an array of probabilities. This vector is also called the confidence of the model and we will use it later.

The softmax function is defined as: 

![softmax](/images/divergent/softmax.png)

where: z is the input vector and K is the number of classes in the multi-class classification problem.

See [here](https://medium.com/data-science-bootcamp/understand-the-softmax-function-in-minutes-f3a59641e86d) for more about the magic behind this function.


### Traditional scenario

In the setting where traditional network is employed, the following features, in the image below, are the ones exploited by the adversarial model.

![features_trad](/images/divergent/features_trad.png)

The first two, namely *total size* and *packet size*, consider the dimension of the network trace, while the last two the network infrastructure.

### Tor scenario

In Tor, instead, the attacker has to change the point of view and removing the ones related to the dimension, since in Tor packets are *fixed-size* (also called as *cells*), so packet dimension
is not helpful anymore. So, we considered only *packet direction* (that is, ingoing or outgoing) and *arrival time* of each packet.



## Threat model

In this research we are studying WF attacks in two different environments: the first one where client and server communicate to each other through a classic network infrastructure, and the latter one where an anonymyzed network is in place.

The *threat model* with a traditional network is defined by a passive attacker, local to the victim. The adversarial user cannot break the cryptographic algorithms that the client and the server have built and his aim is to determine the resources requested by the client.

In the other scenario, the attacker model is similar to the previous one, except that the other endpoint, that is, the server, is not known by construction of Tor. Moreover, at least one node inside the tor circuit is benign (as researchers have showed that if all the nodes are malicious then the attacker can completely break user's privacy, [link](https://murdoch.is/papers/oakland05torta.pdf)).

In the image below the two settings are represented.

![threat_models](/images/divergent/threat_models.png)

Note that Divergent takes place *server-side*.


## Divergent

Nice! Finally we can talk about our proposal !!

The defence we propose works the same for the two scenarios, so we explain it only the traditional one. The main goal of Divergent is to *reduce the performances* of a WF attacker, by removing the uniqueness tied to each network flow, leveraging randomness and dummy packet.

With Divergent applied, a client and a server behaves in the following way:
1) the **client C** and **server S** create an encrypted channel, for example using the TLS protocol;  
3) then, S samples, from a binomial distribution, a **random number x**, and sends it
  *encrypted* to C;
4) this number x, represents the number of packets that the server has to send to C, in order to start transmitting a burst of dummy packets, whose size is defined by the variable **y**. y is randomly sampled, too, and never shared to x.

Let's clarify with a practical example.

In the figure below we find the classic way of communication but with Divergent in place.

![1st-step](/images/divergent/first_step.png)

So, at first the client and the server create the encrypted channel, in this
case a TLS one. After this setup, S generates x and sends it to C. In this
specific case, x is equal two.

This means that, throughout all the network connection, every 2 real packets sent by S, this samples y and transmit a burst of dummies to C. In the first case y=1, so S sends only one dummy.

Again, after 2 real packets, the server samples y and sends as many dummy packets as y's value; in the second case y=2.

### Dummy packets

The dummy packets we conceived can be of two different typologies: in the first case they are *simple fake packets*, injected in the network in order to hamper the attacker. On the other hand, we defined a different dummy packet, whose payload contains useful data, which is the output of the xor operation between next real packets, and the last, sent, real packet. This is made in order to waste as little bandwidth as possible.

In order to recognize useless and useful dummies, the client relies on these writings: repsectively, **dummy** and **dummy_payload**.

![dummy-pkt](/images/divergent/dummy_packet.png)

Observe that in our implementation, for each burst of dummy packets there is 50% of probability that all the dummy packets contain useful data, otherwise they're all worthless.


## Bandwidth and Time overhead

In order to understand if a defence is efficient, two main metrics are considered: Bandwidth overhead (**BWOH**) and Time overhead (**TOH**).

BWOH is the ratio between the number of injected dummy bytes over the original traffic.

TOH, instead, represents the amount of time of the defended traffic flow with respect to the original one.

Note that in Divergent, by construction, TOH is increased only when the server sends dummy packets without useful payload and at the same time the application data is ready. In all the other cases the whole transmission time does not increase.


## Comparison with other defences

In the table we represented the overheads that characterize other mechanisms and Divergent.

WTF-PAD and Wakie-Talkie (W-T) were considered to be the option defences to be implemented in Tor. Here, we can see that our proposal improves both the overheads, reducing to 1/3 the BWOH w.r.t. WTF-PAD and of 25% against W-T.

![defences](/images/divergent/defences.png)

## Evaluation setup and metric

In order to reproduce the attack and the defence, we created an environment using:
Tensorflow coupled with Keras in order to build and train the adversarial model. Furthermore, we implemented a web client and a web server for generating the traffic traces.

In the traditional scenario, the dataset of the attacker is composed by 10 different *videos*, while in the Tor network we used a dataset made available by professor Tao Wang. The traffic traces in this scenario are related to 10 distinct *web pages*.

 Instead, for evaluating the performances of the CNN adversarial model, we exploited its **accuracy**, which corresponds to the ratio of the number of correclty classified traces, over the total number of flows intercepted.


## Results

 In both the scenarios the adversarial model obtains more than 92% of accuracy with traffic *without* any defence.
Instead, with Divergent in place, in the traditional setting the attacker achieves 49% of accuracy, while in the Tor network 29%.
We deem that this difference of accuracy it's due to the fact that the data in the traditional setting has been taken in a localhost scenario, so the (inter-)arrival times are very tiny. In this way, even if we add dummy packets, the DL model is not obstructed because it is able to detect and distinguish the dummy packets, due to the fact that the act of sampling y’s values takes one order of magnitude than the transmission time.

Nevertheless, the attacker's confidence is very low in classic network, too.
In fact, if we observe the confidence output by the softmax function in the table below, the values of each corresponding video are less than 50%. 

In particular, the third row contains the probabilities that the model would assign a label to *video 7*. Here, note that the model cannot distinguish very well between video 4 and video 7. 
On the row below, notice that the attacker model's confidence is only of 12%. So, it puts on the same level almost all the classes.

![result](/images/divergent/results.png)

To furnish more insight about the results, below there are two **confusion matrices** (CM) as regards the results in the *Tor network* without (on the left) and with (on the right) Divergent in place.

![cm](/images/divergent/cm.png)


## Discussion & future work 

Very good. Now, what can we do to further improve our proposal? First of all, we can cluster videos, or more generally, web resources, so that the ones in the same cluster will be assigned to similar traffic flows, in order to further decrease attacker's accuracy.

Next, an extension of clustering, is to *optimize* the values of x and y's, which are the main entities that introduce overhead in the transmission.

Finally, an evaluation is needed in the so-called *open-world* scenario. This means that the victim does not have any limit on the resources that can request and consequently the attacker monitors only some resources.
However, we argue that, since the closed-world scenario is more convenient for an attacker, we are confident that Divergent can work (better) in this setting, too.


## Conclusion

If you read till here, thank you for your time. Hope you enjoyed this journey :)

If you need the code here is the link to the github repository: [link](https://github.com/francevarotz98/Divergent).

Moreover, if you crave for knowing the details of this project, you can read my thesis in this [link](https://thesis.unipd.it/retrieve/7d5e5689-315d-44ea-9e0a-bb4fc0ab2ea1/Varotto_Francesco.pdf).

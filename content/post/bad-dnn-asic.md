+++
date = "2018-08-15T16:25:31-04:00"
highlight = false
math = false
draft = false
tags = []
title = "How to Make Bad Deep Learning Hardware"

[header]
  caption = ""
  image = ""

+++

I've greatly enjoyed my time as a graduate research intern at
[DeepScale](http://deepscale.ai/) and so I was pleased when [Forrest
Iandola](http://www.forrestiandola.com/) asked me to give a talk to his team on 
deep learning hardware. To keep the talk from being a boring list of slides
recounting famous (but dry) neural network hardware papers, we decided to structure it
like one of [David
Patterson](https://en.wikipedia.org/wiki/David_Patterson_(computer_scientist))'s
talks which describe [what *not* to
do](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2013/EECS-2013-123.pdf). The
talk was well received, so I decided to reformat it into this blog post. This
post does assume some knowledge of DNN processing, but for those of you who are
new to the topic I also provide explanations for each piece of advice in spoiler
tags. If you want to learn more about DNN processing, I recommend this excellent
[survey paper](https://arxiv.org/abs/1703.09039) by [Vivienne
Sze](http://www.rle.mit.edu/eems/people/) and others.

Fun fact by the way: Forrest and I first met way back in 2007 when our
respective projects made it to the AAAS national high school science fair! The
project I made for that competition developed into [my first
patent](https://patents.google.com/patent/US8289363B2/en) and eventually [my
first company](http://firebrandinnovations.com/).

Before we begin, a few disclaimers. 1) While I have read and written many papers
on DNN hardware design, I have never built DNN hardware in a corporate context.
2) These are my personal opinions and don't reflect those held by DeepScale.

Alright, lets make some awful hardware!

## Guidelines for Making Bad DNN Hardware

1. *Be Vague*

	The first step whenever making any bad system is to have no clear goal in
mind. When it comes to DNN hardware your system could be optimized for
inference, training, or both. Rather than focusing your design on any particular
use case, simply include all buzzwords on your website to attract the
maximum amount of potential users while doing the least amount of work.

	<details>
	    <summary>Why is this so bad?</summary>
		Users interested in training and users interested in inference often
have fundamentally different hardware needs. For example: hardware for training
is generally evaluated on its throughput above all else, while hardware for
inference is typically optimized for latency. Data locality can be different as
well since activations  must be stored for all layers when training but
activations can be discarded during inference. Finally, training hardware will
often need to support much higher precision than inference hardware
due to the need for gradient computation in training. So while some hardware
optimizations improve performance overall, many design decisions will end up
placing your hardware somewhere along a trade-off curve. Without a clear focus
for what your potential users need, you'll find that what you've built isn't
useful to anyone in particular.
	</details>

2. *Specialization: All or Nothing*

	You have only two options when it comes to hardware specialization:

	**Option 1:** Fully optimize for the NoM (Network of the Moment). Clearly the fact
that the NoM is popular now means that it will always be popular, ensuring that
your hardware will get the most users!

	**Option 2:** DNNs want
throughput, so make a generic throughput accelerator. *Bonus:* There are a lot
of languages for throughput accelerators already. Your hardware can even support
CUDA!

	<details>
	    <summary>Why is this so bad?</summary>
		One of the most fundamental concepts in computer architecture is the
specialization-generalization trade-off. One extreme example of this trade-off
is taking any given algorithm and baking it into specialized hardware. This
dramatically improves computational efficiency, but optimizing hardware for a
single algorithm means that the resulting hardware can't do anything other than
compute that algorithm. So, optimizing your hardware for the network of the
moment is a dangerous proposition considering the deluge of papers every year
from NIPS, ICML, ICCV, and CVPR. As an example consider the evolution from
[AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf),
to [ResNet](https://arxiv.org/abs/1512.03385), to
[SqueezeNet](https://arxiv.org/abs/1602.07360), to
[MobileNet](https://arxiv.org/abs/1704.04861). The software development cycle
(from idea to implementation) can be around 6 months, while the hardware
development cycle is often closer to 2 years. This means that over
specialization can lead to your chip being obsolete before it's even built.

	The opposite extreme on the specialization-generalization trade-off is to be
purely general. It may seem strange to think of GPUs as general purpose
hardware, but they are the most popular choice for throughput focused commodity
hardware. GPUs do have modules within them that are optimized for nothing but
graphics, but the impact of these modules is becoming less and less relevant as
deep learning continues to be one of the most popular applications for GPUs.
NVIDIA and AMD have been optimizing their GPUs for deep learning workloads for
quite a while now. You can avoid simply making another GPU by using application
specific optimizations such as those mentioned in the next piece of advice.
	</details>

3. *Avoid All Complexity*

	Exploiting [sparsity](https://arxiv.org/abs/1708.04485), [model
compression](https://arxiv.org/abs/1602.01528), [systolic
arrays](https://arxiv.org/abs/1704.04760), or [temporal
redundancy](https://arxiv.org/abs/1803.06312) seems hard. Waffle back and forth
about if your hardware will support these features, and in the end just do the
bare minimum.

	<details>
	    <summary>Why is this so bad?</summary>
		As we discussed above, choosing where your device falls on the
specialization-generalization trade-off curve is a critical decision. The one
benefit that custom hardware can provide is specialization after all, so it is
important to specialize in a meaningful way. Without any special optimizations
for your application of choice your system will be no better than a general
purpose CPU or GPU.
	</details>

4. *All DNNs are CNNs*

	The most famous DNN paper in recent memory is
[AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf).
From this we can conclude that absolutely all neural networks are convolutional
image classifiers. Optimize your system for `3x3` (or better yet, `5x5`!)
convolutions and then claim that your system is a generic neural network
accelerator.

	<details>
	    <summary>Why is this so bad?</summary>
		CNNs are far from the only kind of neural network. Despite their
popularity in the hardware research community, Google found that CNNs accounted
for [only 5%](https://arxiv.org/abs/1704.04760) of their total DNN workload. The
other big players
include [Multi-Layer Perceptrons](https://en.wikipedia.org/wiki/Multilayer_perceptron) and
[Recurrent Neural Networks](https://en.wikipedia.org/wiki/Recurrent_neural_network) such as
[Long Short-Term Memory](https://en.wikipedia.org/wiki/Long_short-term_memory)
networks. If you plan on making a generic deep learning processor then
optimizing for only CNNs is a huge oversight.

	It is important to point out however that not all hardware must aspire to be
purely generic. After all, part of the reason why the hardware
community has been so interested in CNNs is the many
interesting and effective ways to accelerate them. This includes
[my own work](https://arxiv.org/abs/1803.06312)! Also, while Google servers may
not run CNN inference frequently, vision-heavy applications like self driving cars and
augmented reality rely heavily on CNNs. So, rather than claiming that optimizing
for CNNs is always a bad idea, I simply want to point out that choosing to
specialize for CNN computation must be done intentionally.
	</details>

5. *Hammer that DRAM*

	GPUs are good at deep learning and have tiny caches, so learn from this by
saving precious area for logic instead of on-chip memory.

	<details>
	    <summary>Why is this so bad?</summary>
		Good computer architects must always be thinking about the spatial and
temporal locality of data in their applications of choice. While many GPU
applications don't have significant temporal locality (hence the reason for
small on-chip memory) this is certainly not the case for deep learning
workloads. As [Song Han](https://stanford.edu/~songhan/) and others point out in
their paper on the [Efficient Inference
Engine](https://arxiv.org/pdf/1602.01528.pdf): even without changing the
underlying computation, huge amounts of energy and time can be saved by simply
keeping network activations and model parameters on chip.
	</details>

6. *All DNN Computation is Regular*

	Literally all DNN computation is dense matrix multiplication. The regularity
and predictability of this computation means that we can always assume
maximum DRAM bandwidth, and there's no need for any hardware or software support
for load balancing!

	<details>
	    <summary>Why is this so bad?</summary>
		As it turns out, lots of DNN computation can be irregular and load
balancing can be a hard problem even with fully regular computation. Irregular
DNN computation be a result of [leveraging
sparsity](https://s3-us-west-2.amazonaws.com/openai-assets/blocksparse/blocksparsepaper.pdf),
[model compression](https://arxiv.org/abs/1510.00149), or a variety of other
techniques. Load balancing issues can come from this irregular computation, but
can also come from general challenges when using systolic arrays such as
those in the [TPU](https://arxiv.org/pdf/1704.04760.pdf) or
[Eyeriss](https://people.csail.mit.edu/emer/papers/2016.06.isca.eyeriss_architecture.pdf).
	</details>

7. *8 Bit Means 8 Bit*

	DNNs are famously well suited for low precision computation, so we only need
to support 8 bit accumulations. Don't tell anyone that this is what your
hardware does, instead keep it as a trade secret.

	<details>
	    <summary>Why is this so bad?</summary>
		Computing with 8 bit weights and activations may be one thing, but
accumulating in 8 bits will lead to extreme overflow issues. To begin with,
multiplying two 8 bit numbers together results in a 16 bit product. Additionally
(pun totally intended), many products will be added together as a part of the
multiply accumulate chains inherent to matrix-matrix multiplication. For this
reason, hardware systems must accumulate with precision significantly larger
than that used to store activations and weights. For a more in depth discussion
of the different kinds of precision and their importance in deep neural
networks I recommend [Chris De Sa](http://www.cs.cornell.edu/~cdesa/)'s
paper on [low-precision SGD](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5789782/).
	</details>

8. *Latency = 1 / Throughput*

	Latency is clearly the inverse of throughput, so only report frames per
second with large batch sizes.

	<details>
	    <summary>Why is this so bad?</summary>
		Large batch sizes are often used to increase the temporal locality of
data when training. This significantly reduces the average time needed to
process each frame. For this reason supporting large batch sizes and reporting
performance when using large batch sizes can be completely reasonable. This is
certainly not the case however for applications which require real-time
inference like self driving cars and augmented reality however. For these applications,
waiting to group multiple frames into a batch is unacceptable since a result is
expected for each frame immediately after being received. So, "average time per
frame" is only equal to real-time latency if the batch size is equal to 1. The
[Project Brainwave](https://www.microsoft.com/en-us/research/uploads/prod/2018/03/mi0218_Chung-2018Mar25.pdf) paper has some great details on this.
	</details>

9. *The Yield Will Get Better, I Promise*

	The device physics folks down the hall can surely scale their experimental
technology to production level soon. CMOS is old news. Its all about graphene,
spintronics, optical computing, \<insert your own post-CMOS tech here\>.

	<details>
	    <summary>Why is this so bad?</summary>
		This advice can go for pretty much any kind of computer architecture
research but is especially relevant for deep learning hardware due to its
popularity. Hardware architects often get really excited when they find
themselves speaking with device physics people. New computing technologies sound
very promising and have all kinds of desirable properties. The challenge with
nearly all of these new technologies however is scaling the process from tens of
devices per chip to millions or billions of devices per chip. If you are a
process design engineer or a device physics person yourself then by all means do
research in that field, but if you are just a hardware engineer then be wary of
any technology that isn't being used at an industrial scale. Otherwise you'll
find yourself putting bogus numbers in your architectural simulator and/or never
be able to fabricate your chip.
	</details>


10. *Software? Lame.*

	Listen, if we wanted to make software we would have joined Facebook. Let's
just have one engineer extend gcc so that we aren't *technically* lying when we
say that you can write code for our chip.

	<details>
	    <summary>Why is this so bad?</summary>
		Hardware designers can get dismissive about software simply because it
isn't their area of expertise, but hardware is useless without software support!
With a few exceptions here and there, even application specific hardware must be
programmed. Any pitch for a new piece of hardware must include both the exciting
new features that the hardware provides as well as an in-depth description of
the software stack used to program the hardware. The features are why someone
might be interested and the software stack is how they can leverage these great
features. Because hardware designers are the most comfortable with the
complexities of their hardware, they should be actively involved in the
development of the software stack for their system.
	</details>

I hope this helps you develop a useless deep learning chip of your very own!
Did I missed any classic hardware design sins? If you can think of any, feel
free to let me know in the comments below. :smile:

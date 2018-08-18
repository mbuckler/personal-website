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
maximum amount of customers while doing the least amount of work. *Bonus:* Think
of all the time you'll save when potential customers hang up on your marketing
calls when they realize your hardware doesn't do what they need!

	<details>
	    <summary>Why is this so bad?</summary>
	    Users interested in training and users interested in inference often
have fundamentally different needs from their hardware. For example: hardware
for training is generally evaluated on its throughput above all else, while
hardware for inference is typically optimized for latency. While some hardware
optimizations improve performance overall, many design decisions will end up
placing your hardware somewhere along a trade-off curve. Without a clear focus
for what your potential users need, you'll find that what you've built isn't
useful to anyone in particular.
	</details>

2. *Specialization: All or Nothing*

	You have only two options when it comes to hardware specialization.
Option 1: Fully optimize for the NoM (Network of the Moment). Clearly the fact
that the NoM is popular now means that it will always be popular, ensuring that
your hardware will get the most users! Option 2: DNNs want
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
from NIPS, ICML, ICCV, and CVPR. Consider the evolution from
[AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf),
to [ResNet](https://arxiv.org/abs/1512.03385), to
[SqueezeNet](https://arxiv.org/abs/1602.07360), to
[MobileNet](https://arxiv.org/abs/1704.04861). The software development cycle
(from idea to implementation) can be around 6 months, while the hardware
development cycle is often closer to 2 years. This means that over
specialization can lead to your chip being obsolete before it's even built.

	The opposite extreme on the specialization-generalization trade-off is
to be purely general. It may seem strange to think of GPUs as general purpose
hardware, but they are one of the most popular choices for
throughput focused commodity hardware. You may correctly point to
the fact that GPUs have modules within them that are optimized for nothing but
graphics, and thus there may be an opening for a truly generic throughput
accelerator. This point is becoming less and less valid however as deep learning
continues to be one of the most popular applications for GPUs, so both NVIDIA
and AMD have become optimizing their GPUs for deep learning workloads. Overall,
you don't want to make a general purpose GPU because NVIDIA and AMD are already
working to beat you to the punch.
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
	    As the [first TPU paper](https://arxiv.org/abs/1704.04760) was quick to
point out, CNNs are actually in the minority when it comes to 
	</details>

5. *Hammer that DRAM*

	GPUs are good at deep learning and have tiny caches, so learn from this by
saving precious area for logic instead of on-chip memory.

	<details>
	    <summary>Why is this so bad?</summary>
		Template
	</details>

6. *All DNN Computation is Regular*

	Literally all DNN computation is dense matrix matrix multiplication. The
regularity of this computation means that we can always assume maximum DRAM
bandwidth, and there's no need for any hardware or software support for load
balancing!

	<details>
	    <summary>Why is this so bad?</summary>
		Template
	</details>

7. *ISA FTW*

	Some of the most exciting innovations in classical computer architecture
have come in the form of instruction set architecture (ISA) improvements. As we know,
the one constant in technology is that it never changes, so we can conclude that
ISA innovations will be the most impactful in DNN hardware.

	<details>
	    <summary>Why is this so bad?</summary>
		Dataflow and memory usage is much more imporant in DNN hardware
	</details>

8. *8 Bit Means 8 Bit*

	DNNs are famously well suited for low precision computation, so we only need
to support 8 bit accumulations. Don't tell anyone that this is what your
hardware does, instead keep it as a trade secret.

	<details>
	    <summary>Why is this so bad?</summary>
		Template
	</details>

9. *Latency = 1 / Throughput*

	Latency is clearly the inverse of throughput, so only report frames per
second with large batch sizes.

	<details>
	    <summary>Why is this so bad?</summary>
		Template
	</details>

10. *Software? Lame.*

	Listen, if we wanted to make software we would have joined Facebook. Let's
just have one engineer extend gcc so that we aren't *technically* lying when we
say that you can write code for our chip.

	<details>
	    <summary>Why is this so bad?</summary>
		Template
	</details>

## Guidelines for Making Good DNN Hardware

Of course, its much easier to point out mistakes and problems than to actually
provide solutions. 

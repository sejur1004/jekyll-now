---
layout: post
title: GlusterFS
date: 2017-02-18 
comments: true
external-url:
categories: App_Tech
---
## History.
> GlustetFS는 클라우드 스토리지를 위한 오픈 소스 플랫폼을 제공하는 소프트회사인 Gluster Inc.의 설립자이자 CTO 인 Anand Babu Periasamy에 의해 개발 되었으며, 2011년 10월 7일 Red Hat에서 인수되었다.

이 후 Redhat이 GlusterFS 오픈 소스 프로젝트의 주 개발및 관리를 하게 되었으며, Red Hat Storage Server로 사용버전을 판매하기 시작하였다. 그 이후 Redhat에서 Ceph를 인수하고 2015년에 Red HAt Gluster Storage라는 이름으로 판매중이다.

## Architecture
> SDS(Software Defined Storage)로 Commodity Hardware를 사용하여 Destrebuted Storage를 제공한다.
**GlusterFS Arch의 가장 큰 특징은 파일 저장 위치 정보에 대한 Metadata를 관리하는 서버가 없다는것이 특징 이다.**

타 SDS기반의 분산 스토리지 경우 파일 스토리지 제공 시, 파일에 대한 Metadata 서버가 존재하며 GlusterFS는 **"Elastic hash algorithm"**으로 Metadata 서버를 대체 한다.

그외 특징은 타 SDS 기반의 분산 파일 스토리지와 유사한 형태로, Scale Out을 통한 무한 확장성을 제공한다.
즉, 사용중인 Volume에 신규 Node를 추가하면 Online 상태에서 용량이 증가된다.

![GlusterFS Scale Out]({{ site.url }}/assets/GlusterFS_arch.png){:height="400px" width="600px"}

### 
Sleeping Beauty undergoes an experiment where she's put to sleep on Sunday. Immediately after that, a fair coin is tossed. If it comes out Heads, Sleeping Beauty is waken on Monday. If it comes out Tails, she's waken both on Monday and Tuesday. After being waken, the experimentalist asks Sleeping Beauty: *"What is your degree of certainty that the coin landed heads?"*. He subsequently puts Sleeping Beauty to sleep, taking care to administer her an amnesia inducing drug that ensures she doesn't remember the experiment. Hence, everytime Sleeping Beauty is awaked, she won't know which day it is or whether she has already been awakened before or not. What should her answer be?

It's amazing how a simple puzzle installs chaos among mathematicians and philosophers alike. Some would argue that the correct probability is ½: after all, the coin is **fair**, isn't it? Others would say that the correct probability should be ⅓. Think about this problem for a second because the correct answer is... **both**!

As many things in life, it's all a matter of perspective. And the conundrum here lies in the way the problem is phrased. But before going into maths and probability theory, we shall **simulate**:

```scala
var correct = 0
var questions = 0
var heads = 0
var trials = 10000

// Let's run some trials
(1 to trials).foreach { trial =>
  // toss a fair coin
  scala.util.Random.nextBoolean() match {
    case true =>  // Heads was tossed. Wake on Monday.
      // Sleeping Beauty always bet Heads was tossed;
      // ... in this case, only once.
      questions += 1
      correct += 1
      heads += 1
    case _ =>     // Tails was tossed. Wake on Monday and Tuesday.
      // Sleeping Beauty always bet Heads was tossed;
      // ... in this case, it will bet both on Monday
      // and Tuesday. But it will fail!
      questions += 2
  }
}

println("Probability of SB being correct: " + correct.toDouble/questions)
println("Probability of Heads being tossed: " + heads.toDouble/trials)
```

Here's a sample run:

```
Probability of SB being correct: 0.33636242148870776
Probability of Heads being tossed: 0.5034
```

## Two questions, two answers

The attentive reader should have already grasped that the answer depends on *what exactly we are counting as a success*. If we measure success as the *number of times Sleeping Beauty is able to give a correct answer*, then her *degree* of confidence should be ⅓, as the above simulation shows. If you are still unable to see how, under that measurement of success, the number of times Sleeping Beauty is awakened impacts the answer, then imagine the following puzzle variant:

> (...) she's put to sleep on the 31th of December. If it comes out Heads, Sleeping Beauty is waken on the 1st of January. If it comes out Tails, she's waken every single day for the whole year, *i.e.*, 365 days.

Or even something a little more extreme:

> (...) If it comes out Heads, Sleeping Beauty is waken on Monday. If it comes out Tails, she stays asleep forever.

The last one is my favorite variation, since it's pretty obvious that, if she's ever awakened, then the confidence she has that Heads was tossed is 100%; even if that mere fact doesn't influence even a bit the tossing of a fair coin. It is an *a posteriori* confidence, *given that she's waken*.

On the converse, this discussion points out to the second interpretation, which is the *confidence Sleeping Beauty has that the coin could produce Heads at the moment of tossing*. In other words, an *a priori* confidence on the bias of the coin *per se*. This last one would always be 0.5, but we can no longer rely on multiple trials to measure success(es) because if Tails come up, Sleeping Beauty will never be able to answer.

There has been an extensive discussion on this topic, so I will not reiterate what has already been well laid out [in this paper](http://arxiv.org/ftp/arxiv/papers/0806/0806.1316.pdf), in this  [video](https://www.youtube.com/watch?v=zL52lG6aNIY), in [Wikipedia](https://en.wikipedia.org/wiki/Sleeping_Beauty), or, more recently, during several different expositions [[1](https://www.quantamagazine.org/20160114-sleeping-beautys-necker-cube-dilemma/),
[2](https://www.quantamagazine.org/20160129-solution-sleeping-beautys-dilemma/), [3](https://www.quantamagazine.org/20160331-why-sleeping-beauty-is-lost-in-time/)] in [Quanta Magazine](http://www.quantamagazine.org/).

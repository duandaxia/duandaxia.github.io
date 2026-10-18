---
layout: post
title: The Bitter Lesson and DSD
date: 2026-10-17 12:00:00 +0200
author: Yufei Duan
description: Letting the model find the answer does not make the way we write the question irrelevant.
tags: robot-learning DSD
categories: research
permalink: /blog/the-bitter-lesson-and-dsd/
meta: 6 min read
personal_blog: true
related_posts: false
_styles: |
  .post-header, .post-content { max-width: 740px; margin-inline: auto; }
  .dsd-essay { font-size: 1.08rem; line-height: 1.8; }
  .dsd-essay p { margin-bottom: 1.5rem; }
  .dsd-essay .lead { font-size: 1.35rem; }
  .dsd-essay .pullquote { border-left: 2px solid var(--global-theme-color); padding-left: 1.25rem; margin-block: 2rem; font-size: 1.3rem; }
  .dsd-essay sup a { padding-inline: 2px; }
  .dsd-results { margin-block: 2rem; font-size: .85rem; line-height: 1.5; }
  .dsd-results table { min-width: 300px; font-variant-numeric: tabular-nums; }
  .dsd-results caption { caption-side: top; color: inherit; font-weight: 600; }
  .dsd-results th, .dsd-results td { padding: .75rem .4rem; text-align: right; vertical-align: middle; }
  .dsd-results th:first-child { text-align: left; width: 42%; }
  .dsd-results tbody th { font-weight: 400; }
  .dsd-results tbody th span { display: block; font-size: .8em; }
  .dsd-results td:last-child { font-weight: 600; }
  .dsd-results figcaption { margin-top: .75rem; font-size: .8rem; }
  .dsd-references { border-top: 1px solid var(--global-divider-color); margin-top: 2.5rem; padding-top: 1.5rem; font-size: .9rem; }
  .dsd-references h2 { font-size: 1.4rem; }
  .dsd-references li { margin-bottom: .75rem; scroll-margin-top: 90px; }
  .dsd-references li:target { outline: 1px solid var(--global-theme-color); outline-offset: 4px; }
  @media (max-width: 575px) { .dsd-results { font-size: .75rem; } }
---

<a href="https://duandaxia.github.io/blog/">&larr; All posts</a>

<div class="dsd-essay">
<p>I first started questioning my understanding of The Bitter Lesson when I realized something:</p>
<p class="lead">My dad had not exactly started from scratch when he beat me at Go.</p>
<p>I first read the essay in the first year of my PhD, shortly after moving from “traditional robotics” into robot learning. It was remarkably short, but it changed how I thought about the field. Many things we had worked so hard to design might eventually be surpassed by a more general approach that simply learned from enough experience and computation.</p>
<p>The part about Go brought back a childhood memory.</p>
<p>I took Go lessons for two years in primary school. Among my classmates, I was practically unbeatable. Then I taught my dad the rules. Before long, he was beating me convincingly.</p>
<p>I had spent more time studying Go. My teacher had taught me capture techniques, opening patterns, and familiar sequences. My dad knew none of them. Somehow, this self-taught player had demolished my little expert system, carefully packed with human knowledge.</p>
<p>Devastated, I retired.</p>
<p>So when I read The Bitter Lesson years later, my first reaction was: absolutely. I had personally experienced the bitterness in primary school.</p>
<p>Sutton’s argument is about the long run: general methods that can keep using more computation, especially search and learning, tend to outlast approaches built around carefully encoded domain knowledge. <sup><a aria-label="Reference 1" href="#ref-1">1</a></sup> It made me wonder how much of our design work was necessary, and how much came from being uncomfortable letting a model learn.</p>
<p>Then, much later, I noticed the missing part of my childhood story.</p>
<p>My dad had never studied those opening patterns. But I had taught him the rules of the game: which moves were legal, how stones were captured, and what counted as winning. Even AlphaGo Zero, which learned without human game records, received a well-formulated problem. It did not first have to inspect a camera feed and discover which moves were legal and which were not. <sup><a aria-label="Reference 2" href="#ref-2">2</a></sup></p>
<p>I had not taught my dad how to win. I had already told him what game he was playing.</p>
<p>He could focus on how to play well, because he already knew the rules and what he was trying to achieve.</p>
<p>This story does not refute Sutton’s argument. It does, however, help me distinguish prescribing a solution from choosing how to express a problem.</p>
<p>If a representation makes useful relationships in the data easier to learn, I see no contradiction in studying it while believing in learning.</p>
<p class="pullquote">Letting the model find the answer does not make the way we write the question irrelevant.</p>
<p>This reminds me of a game from my university orientation: ‘How many mosquitoes?’</p>
<p>Someone who knows the rule makes a few gestures, then suddenly asks how many mosquitoes there are. I was completely lost. Was I supposed to count fingers? Claps? Watch which hand moved first?</p>
<p>I did some very serious feature engineering. I also kept getting the answer wrong.</p>
<p>Eventually, someone told me the rule. Everything made sense immediately, and I acquired a new way to annoy other people.</p>
<p>What stayed with me was how many examples I had seen while paying attention to the wrong things. Could I have figured it out alone? Probably, with enough observations and experiments. But the space of possible rules was large, and searching through it without knowing which clues mattered was costly and inefficient.</p>
<p>Giving me the rule gave away the answer. That is not what I want to do for a robot. The question I care about is whether we can make useful relationships easier to see while leaving the behavior itself to be learned.</p>
<p>Data, compute, and robot time all cost something. A robot still has to spend every second of its experience in the physical world. It seems reasonable to ask whether we have accidentally made its learning problem harder than it needs to be.</p>
<p>That is the question behind our recent project, Direction–Scale Decomposition, or DSD: before deciding how to tokenize actions, what exactly are we asking the tokenizer to encode? <sup><a aria-label="Reference 3" href="#ref-3">3</a></sup></p>
<p>A vision-language-action model learns to predict robot actions from observations and instructions. In a common discrete-token setup, movements are written as numerical pose increments and converted into tokens.</p>
<p>Those numbers deserve some attention.</p>
<p>Imagine two demonstrations following the same path at different speeds. With a fixed sampling rate, the faster demonstration has larger displacements at each step. Coordinate-wise binning can therefore produce different action tokens even when the direction is unchanged.</p>
<p>Across datasets, different normalization statistics can also map exactly the same physical motion to different tokens.</p>
<p>I picture the robot trying to learn a task while also wondering: ‘Are these movements different, or was this teleoperator in a hurry?’</p>
<p>Token IDs are labels; they do not explicitly show which motions share a direction or how their distances compare. The model has to learn these relationships while also learning the task. A rough analogy is arithmetic in language models: learning patterns of number tokens does not automatically lead to reliable calculation.</p>
<p>Speed and magnitude can matter enormously. A representation should retain that information. But it can also expose what two movements share.</p>
<p>DSD separates a translation into its direction and distance, and a relative rotation into its axis and angle. Magnitudes remain in separate scale channels. The gripper command stays as it is. This happens before tokenization, so we can study the representation with different tokenizers. <sup><a aria-label="Reference 3" href="#ref-3">3</a></sup><sup><a aria-label="Reference 4" href="#ref-4">4</a></sup></p>
<p>We have separated ‘which way’ from ‘how far.’ The model still has to learn both.</p>
<p>A longer displacement along the same direction changes the scale without changing the direction target. This property assumes a consistent coordinate convention; DSD does not automatically align different robot frames or controllers.</p>
<p>The geometric information was already present in the original action. DSD makes part of that structure explicit. Before quantization, the decomposition preserves the represented motion.</p>
<p>We evaluated DSD with uniform binning, called BIN, and with the B-spline tokenizer BEAST. Here are the main success-rate comparisons from the paper. Differences are percentage points. <sup><a aria-label="Reference 3" href="#ref-3">3</a></sup></p>
<figure class="dsd-results">
<div aria-label="Success-rate comparison, scroll horizontally on small screens" class="table-responsive" role="region" tabindex="0">
<table class="table table-sm">
<caption>Success rates with and without DSD</caption>
<thead>
<tr><th scope="col">Evaluation / tokenizer</th><th scope="col">Baseline</th><th scope="col">With DSD</th><th scope="col">Gain (pp)</th></tr>
</thead>
<tbody>
<tr><th scope="row">LIBERO <span>BIN · average</span></th><td>88.5%</td><td>92.3%</td><td>+3.8</td></tr>
<tr><th scope="row">LIBERO <span>BEAST · average</span></th><td>88.0%</td><td>91.9%</td><td>+3.9</td></tr>
<tr><th scope="row">SimplerEnv <span>BIN · Bridge only</span></th><td>40.0%</td><td>40.7%</td><td>+0.7</td></tr>
<tr><th scope="row">SimplerEnv <span>BIN · five-dataset mixture</span></th><td>33.0%</td><td>43.3%</td><td>+10.3</td></tr>
<tr><th scope="row">Real robot <span>BIN · without robotics pretraining</span></th><td>60.0%</td><td>73.3%</td><td>+13.3</td></tr>
<tr><th scope="row">Real robot <span>BIN · with robotics pretraining</span></th><td>65.0%</td><td>76.7%</td><td>+11.7</td></tr>
</tbody>
</table>
</div>
<figcaption>Source: Tables II–III and Section VI-D of the <a href="https://arxiv.org/html/2609.28865v1">DSD paper</a>. Gains are percentage points (pp), comparing DSD with the same tokenizer and training setting.</figcaption>
</figure>
<p>The mixed-dataset result is the one that most makes me think about The Bitter Lesson. In our SimplerEnv experiments, adding four datasets to Bridge training reduced BIN’s overall success from 40.0% to 33.0%, while DSD-BIN increased from 40.7% to 43.3%. <sup><a aria-label="Reference 3" href="#ref-3">3</a></sup></p>
<p>That is one five-dataset mixture, with gains that vary by task. It is not a scaling law. But it raises a question I find more useful than arguing about whether we should design less: does the action representation affect how much a model benefits from additional data?</p>
<p>If it does, studying representation can help us make better use of scale. The model continues learning from demonstrations; the representation affects how those demonstrations are presented.</p>
<p>This is also what I like about the example of ResNet. Residual learning made deeper networks easier to optimize without supplying a set of rules for recognizing cats. <sup><a aria-label="Reference 5" href="#ref-5">5</a></sup> The connection I see with DSD is that a design can make learning easier without prescribing the solution.</p>
<p>A plausible geometric story is not enough to make DSD useful. Our current results leave further questions about action representation open.</p>
<p>What has changed for me since first reading The Bitter Lesson is the question I ask about a design. Does it constrain the learner to follow our preferred solution, or does it help the learner discover relationships in its experience? And does that help survive as we give it more data and computation?</p>
<p>Back when I taught my dad Go, I explained the rules of the game. I still left him to figure out his next move.</p>
<p>He learned that part himself.</p>
<p>Then he used it to beat me.</p>
<p>Paper and demonstrations: see the <a href="https://arxiv.org/abs/2609.28865">DSD paper</a> <sup><a aria-label="Reference 3" href="#ref-3">3</a></sup> and <a href="https://vla-dsd.github.io/">project page</a> <sup><a aria-label="Reference 4" href="#ref-4">4</a></sup>.</p>
</div>

<section aria-labelledby="references-heading" class="dsd-references" role="doc-endnotes">
<h2 id="references-heading">References</h2>
<ol>
<li id="ref-1" tabindex="-1"><a href="https://www.cs.utexas.edu/~eunsol/courses/data/bitter_lesson.pdf">Rich Sutton. The Bitter Lesson.</a></li>
<li id="ref-2" tabindex="-1"><a href="https://deepmind.google/blog/alphago-zero-starting-from-scratch/">Google DeepMind. AlphaGo Zero: Starting from scratch.</a></li>
<li id="ref-3" tabindex="-1"><a href="https://arxiv.org/abs/2609.28865">Duan et al. Direction-Scale Decomposition in Action Representation.</a></li>
<li id="ref-4" tabindex="-1"><a href="https://vla-dsd.github.io/">DSD project page and demonstrations.</a></li>
<li id="ref-5" tabindex="-1"><a href="https://arxiv.org/abs/1512.03385">He et al. Deep Residual Learning for Image Recognition.</a></li>
</ol>
</section>

<a href="https://duandaxia.github.io/blog/">&larr; All posts</a>

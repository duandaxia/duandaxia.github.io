---
layout: post
title: The Bitter Lesson and How We Represent Actions
date: 2026-10-17 12:00:00 +0200
author: Yufei Duan
description: Letting the model find the answer does not make the way we write the question irrelevant.
tags: robot-learning DSD
categories: research
permalink: /blog/the-bitter-lesson-and-dsd/
last_updated: 2026-10-27
meta: 12 min read
personal_blog: true
related_posts: false
_styles: |
  .post-header, .post-content { max-width: 740px; margin-inline: auto; }
  .dsd-essay { font-size: 1.08rem; line-height: 1.8; }
  .dsd-essay p { margin-bottom: 1.5rem; }
  .dsd-essay code { white-space: nowrap; }
  .dsd-essay .dsd-table-hint { display: none; font-size: .75rem; margin-bottom: .5rem; color: var(--global-text-color-light); }
  .dsd-section-heading { margin: 2.5rem 0 1.2rem; font-size: 1.65rem; line-height: 1.3; scroll-margin-top: 100px; }
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
  .dsd-supplement { border-top: 1px solid var(--global-divider-color); margin-top: 3.5rem; padding-top: 2.5rem; }
  .dsd-supplement h2 { margin-bottom: 1.5rem; }
  .dsd-supplement h3 { margin: 2.5rem 0 1.2rem; font-size: 1.65rem; line-height: 1.3; }
  .dsd-supplement h2, .dsd-supplement h3 { scroll-margin-top: 100px; }
  .dsd-supplement .dsd-results { font-size: .82rem; }
  .dsd-supplement .dsd-results th:first-child { width: 36%; }
  .dsd-supplement .dsd-results thead span { display: block; font-size: .8em; font-weight: 400; }
  .dsd-supplement .dsd-results td:last-child { font-weight: 400; }
  .dsd-supplement .dsd-libero { min-width: 520px; }
  .dsd-supplement .dsd-libero th:first-child { width: 24%; }
  .dsd-delta { display: block; font-size: .8em; font-weight: 400; white-space: nowrap; color: var(--global-text-color-light); }
  .dsd-average th, .dsd-average td { font-weight: 600 !important; border-top: 2px solid var(--global-divider-color); }
  .dsd-rollouts { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 1.25rem; margin: 1.5rem 0; }
  .dsd-rollouts figure { margin: 0; min-width: 0; }
  .dsd-rollouts video { display: block; width: 100%; height: auto; aspect-ratio: 640 / 688; border: 1px solid var(--global-divider-color); border-radius: 8px; background: #000; }
  .dsd-rollouts figcaption { margin-top: .7rem; font-size: .85rem; line-height: 1.6; }
  .dsd-rollouts figcaption strong { display: block; }
  .dsd-experiment-note { font-size: .88rem; line-height: 1.65; color: var(--global-text-color-light); }
  @media (max-width: 575px) { .dsd-essay .dsd-table-hint { display: block; } .dsd-rollouts { grid-template-columns: minmax(0, 1fr); } .dsd-supplement .dsd-results { font-size: .72rem; } }
---

<a href="https://duandaxia.github.io/blog/">&larr; All posts</a>

<div class="dsd-essay">
<p>My understanding of The Bitter Lesson began to shift when I realized something:</p>
<p class="lead">My dad had not exactly started from scratch when he beat me at Go.</p>
<p>I first read the essay in the first year of my PhD, shortly after moving from “traditional robotics” into robot learning. It was remarkably short, but it changed how I thought about the field. Many things we had worked so hard to design might eventually be surpassed by a more general approach that simply learned from enough experience and computation.</p>
<p>The part about Go brought back a childhood memory.</p>
<p>I took Go lessons for two years in primary school. Among my classmates, I was practically unbeatable. Then I taught my dad the rules. Before long, he was beating me convincingly.</p>
<p>I had spent more time studying Go. My teacher had taught me capture techniques, opening patterns, and familiar sequences. My dad knew none of them. Somehow, this self-taught player had demolished my little expert system, carefully packed with human knowledge.</p>
<p>Devastated, I retired.</p>
<p>So when I read The Bitter Lesson years later, my first reaction was: absolutely. I had personally experienced the bitterness in primary school.</p>
<p>Sutton’s argument is about the long run: general methods that can keep using more computation, especially search and learning, tend to outlast approaches built around carefully encoded domain knowledge.&nbsp;<sup><a aria-label="Reference 1" href="#ref-1">1</a></sup> It made me wonder how much of our design work was necessary, and how much came from being uncomfortable letting a model learn.</p>
<p>Then, much later, I noticed the missing part of my childhood story.</p>
<p>My dad had never studied those opening patterns. But I had taught him the rules of the game: which moves were legal, how stones were captured, and what counted as winning. Even AlphaGo Zero, which learned without human game records, received a well-formulated problem. It did not first have to inspect a camera feed and discover which moves were legal and which were not.&nbsp;<sup><a aria-label="Reference 2" href="#ref-2">2</a></sup></p>
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
<p>Data, compute, and robot time all cost something. A robot still has to gain a substantial amount of experience in the physical world. It seems reasonable to ask whether we have accidentally made its learning problem harder than it needs to be.</p>
<h2 class="dsd-section-heading" id="action-representation">What should an action token represent?</h2>
<p>That question led to our paper on <a href="https://arxiv.org/abs/2609.28865">Direction–Scale Decomposition (DSD)</a>: before deciding how to tokenize actions, what exactly are we asking the tokenizer to encode?&nbsp;<sup><a aria-label="Reference 3" href="#ref-3">3</a></sup></p>
<p>A vision-language-action model predicts robot actions from observations and instructions. In a common discrete-token setup, each action describes a small change in the end effector’s position and orientation, together with a gripper command. These numerical increments are normalized and converted into tokens.</p>

<p>Imagine two demonstrations following the same path at different speeds. With a fixed sampling rate, the faster demonstration has larger displacements at each step. Coordinate-wise binning can therefore produce different action tokens even when the direction is unchanged.</p>
<p>Across datasets, different normalization statistics can also map the same physical motion to different tokens. If the normalization used in training and execution differs, it can even change the direction of the decoded motion.&nbsp;<sup><a aria-label="Reference 3" href="#ref-3">3</a></sup></p>
<p>I picture the robot trying to learn a task while also wondering: ‘Are these movements different, or was this teleoperator in a hurry?’</p>
<p>The model has to learn which token patterns describe related motions while also learning the task. The representation determines how readily those relationships are available.</p>
<p>Speed and magnitude matter, so the representation needs to retain them. It can also make shared motion structure explicit.</p>
<p>DSD separates a translation into its direction and distance, and a relative rotation into its axis and angle. Magnitudes stay in separate scale channels, and the gripper command is unchanged. The decomposition happens before tokenization, so it can be studied with different tokenizers.&nbsp;<sup><a aria-label="Reference 3" href="#ref-3">3</a></sup><sup><a aria-label="Reference 4" href="#ref-4">4</a></sup></p>
<p>We have separated ‘which way’ from ‘how far.’ The model still has to learn both.</p>
<p>For a fixed coordinate convention, a longer displacement along the same direction changes the scale without changing the direction target. The geometric information was already in the original action; DSD makes this structure explicit while preserving the represented motion before quantization.</p>

<h2 class="dsd-section-heading" id="dsd-results">What changes in practice?</h2>
<p>We evaluated DSD with two tokenizers: uniform binning (BIN) and the B-spline tokenizer BEAST. Each pair uses the same model backbone, allowing us to study the effect of changing the representation supplied to the tokenizer.&nbsp;<sup><a aria-label="Reference 3" href="#ref-3">3</a></sup></p>
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
<p>The mixed-dataset result is the one that most makes me think about The Bitter Lesson. In SimplerEnv, adding four datasets to Bridge training reduced BIN’s overall success from 40.0% to 33.0%, while DSD-BIN increased from 40.7% to 43.3%. The gap between the two representations grew from 0.7 to 10.3 percentage points.&nbsp;<sup><a aria-label="Reference 3" href="#ref-3">3</a></sup></p>
<p>The gains vary by task, and larger mixtures remain to be tested. Within this five-dataset comparison, however, the action representation changed whether adding heterogeneous data helped or hurt overall performance. That makes representation relevant to the question of scale.</p>
<p>We often ask how much more data a model needs. These results also make me ask how much of the available data it can use effectively, given the way its actions are represented.</p>
<p>ResNet offers a useful analogy. Residual connections made deeper networks easier to optimize while leaving the visual concepts to be learned.&nbsp;<sup><a aria-label="Reference 5" href="#ref-5">5</a></sup> The broader idea is that a design choice can help a general learning method make better use of its capacity. DSD explores that possibility at the level of action representation.</p>

<p>What has changed for me since first reading The Bitter Lesson is the question I ask about a design: does it help the learner discover useful relationships in its experience, and does that help persist as we give it more data and computation?</p>
<p>Back when I taught my dad Go, I explained the rules of the game. I still left him to figure out his next move.</p>
<p>He learned that part himself.</p>
<p>Then he used it to beat me.</p>
<p>The <a href="https://arxiv.org/abs/2609.28865">paper</a> gives the representation, training details, and full experimental comparisons; the <a href="https://vla-dsd.github.io/">project page</a> shows the policies in action.</p>
</div>

<section aria-labelledby="additional-experiments" class="dsd-essay dsd-supplement">
<p class="eyebrow">Research notes · October 27, 2026</p>
<h2 id="additional-experiments">Further experiments and observations</h2>
<p>The paper leaves several practical questions worth examining more closely. The experiments below explore normalization, direction-loss weighting, and a possible explanation for the gains.</p>

<h3 id="normalization-bounds">How much do the normalization bounds matter?</h3>
<p>Our BIN baseline follows the OpenVLA convention: normalize each action coordinate using its 1st and 99th percentiles, q01 and q99, then clip and discretize it.&nbsp;<sup><a aria-label="Reference 6" href="#ref-6">6</a></sup></p>
<p>The effect of the bounds varies by setting. In its normalization ablation, <a href="https://arxiv.org/html/2607.06403#S6.SS1">LingBot-VLA 2.0, Section 6.1</a>, reports nearly identical average success rates for min–max and q01–q99 normalization: 47.5% and 47.4%.&nbsp;<sup><a aria-label="Reference 7" href="#ref-7">7</a></sup></p>
<p>We thank <a href="https://pokeandwiggle.com/research-replication/dsd">Poke &amp; Wiggle</a> for independently reproducing our work and investigating these implementation choices. Their evaluation found a benefit for DSD over BIN with q01–q99 clipping, while a wider 0.1st–99.9th-percentile BIN baseline performed on par with their DSD implementation. Their report also documents differences in the controller interface and direction-loss weighting.&nbsp;<sup><a aria-label="Reference 8" href="#ref-8">8</a></sup></p>
<p>Motivated by that result, we tested min–max bounds for BIN on cube stacking. Widening the range improved success in our setup, with DSD-BIN retaining the higher success rate.</p>

<figure class="dsd-results">
<div aria-label="Cube-stacking normalization ablation" class="table-responsive" role="region" tabindex="0">
<table class="table table-sm">
<caption>Cube stacking · 30 trials per condition</caption>
<thead><tr><th scope="col">Condition</th><th scope="col">Successes</th><th scope="col">Success rate</th></tr></thead>
<tbody>
<tr><th scope="row">BIN · q01–q99 <span>Paper baseline</span></th><td>12 / 30</td><td>40.0%</td></tr>
<tr><th scope="row">BIN · min–max <span>New ablation</span></th><td>15 / 30</td><td>50.0%</td></tr>
<tr><th scope="row">DSD-BIN <span>Paper result</span></th><td>19 / 30</td><td>63.3%</td></tr>
</tbody>
</table>
</div>
<figcaption>The paper results are from Figure 4, without robotics pretraining. The min–max result is the new 30-trial ablation; the paper conditions are shown as reference results.</figcaption>
</figure>

<p>The observed improvement from widening BIN’s bounds is 10 percentage points. It narrows the gap to DSD-BIN from 23.3 to 13.3 points.</p>
<p>Our practical takeaway is to choose the normalization range together with the controller and action definition. Clipping can remove useful corrective motions, while a wider range spreads a fixed number of bins over a larger interval. Control frequency, dead zones, and whether increments are executed directly or accumulated into position targets can affect that trade-off.</p>

<h3 id="direction-loss-weighting">Step weighting or a soft ramp?</h3>
<p>When a motion is very small, a small perturbation can produce a large change in its direction target. The implementation used for our paper therefore down-weights the direction-token loss with a step: <code>w(s) = 0.1</code> when the motion scale <code>s</code> is below a threshold <code>τ</code>, and <code>w(s) = 1</code> otherwise.&nbsp;<sup><a aria-label="Reference 3" href="#ref-3">3</a></sup></p>
<p>In <a href="https://pokeandwiggle.com/research-replication/dsd">Poke &amp; Wiggle’s evaluation</a>, DSD used a soft ramp, <code>w(s) = clip(s / τ, 0.1, 1)</code>.&nbsp;<sup><a aria-label="Reference 8" href="#ref-8">8</a></sup> We evaluated this weighting rule on LIBERO and compared it with both BIN and the paper’s step-weighted DSD-BIN.</p>

<figure class="dsd-results">
<p class="dsd-table-hint">Scroll horizontally to compare all columns.</p>
<div aria-label="LIBERO direction-loss weighting comparison, scroll horizontally on small screens" class="table-responsive" role="region" tabindex="0">
<table class="table table-sm dsd-libero">
<caption>LIBERO · BIN and DSD-BIN full task success</caption>
<thead><tr><th scope="col">Suite</th><th scope="col">BIN <span>(paper)</span></th><th scope="col">DSD-BIN <span>Step (paper)</span></th><th scope="col">DSD-BIN <span>Soft ramp (ablation)</span></th><th scope="col">Soft − step <span>(pp)</span></th></tr></thead>
<tbody>
<tr><th scope="row">Spatial</th><td>83.4%</td><td>93.6%<span class="dsd-delta">+10.2 pp vs BIN</span></td><td>91.6%<span class="dsd-delta">+8.2 pp vs BIN</span></td><td>−2.0</td></tr>
<tr><th scope="row">Object</th><td>93.4%</td><td>96.8%<span class="dsd-delta">+3.4 pp vs BIN</span></td><td>96.2%<span class="dsd-delta">+2.8 pp vs BIN</span></td><td>−0.6</td></tr>
<tr><th scope="row">Goal</th><td>91.8%</td><td>95.0%<span class="dsd-delta">+3.2 pp vs BIN</span></td><td>83.0%<span class="dsd-delta">−8.8 pp vs BIN</span></td><td>−12.0</td></tr>
<tr><th scope="row">Long / LIBERO-10</th><td>85.2%</td><td>83.8%<span class="dsd-delta">−1.4 pp vs BIN</span></td><td>87.6%<span class="dsd-delta">+2.4 pp vs BIN</span></td><td>+3.8</td></tr>
<tr class="dsd-average"><th scope="row">Average</th><td>88.5%</td><td>92.3%<span class="dsd-delta">+3.8 pp vs BIN</span></td><td>89.6%<span class="dsd-delta">+1.1 pp vs BIN</span></td><td>−2.7</td></tr>
</tbody>
</table>
</div>
<figcaption>Changes below each DSD score are relative to BIN; the final column shows soft ramp minus step, in percentage points (pp). BIN and step-weighted DSD-BIN: Table II of the <a href="https://arxiv.org/html/2609.28865v1">paper</a>. Soft ramp: our additional evaluation using the weighting rule from Poke &amp; Wiggle’s evaluation, with the best full-success checkpoint selected separately for each suite. These are not averages over training seeds. The average row is the unweighted mean of the four suites.</figcaption>
</figure>

<p>The soft ramp performs better on LIBERO-10, but its four-suite average is 2.7 percentage points below the paper’s step weighting, with the largest decrease on Goal. Compared with BIN’s 88.5% average, step-weighted DSD-BIN gains 3.8 points and the soft-ramp variant gains 1.1 points. The weighting rule therefore matters when comparing and reproducing these results.</p>

<h3 id="multimodal-actions">Making sense of the gains</h3>
<p>Changing the action representation alone can lead to a substantial improvement. The size of the gain surprised us, so we tried to understand why.</p>
<p>With coordinate-wise binning, demonstrations following the same path at different speeds produce different token patterns. They may form separate modes in the action targets even when they express the same underlying behavioral choice. Our hypothesis is that this makes the prediction problem more complicated than the choice of behavior requires.</p>
<p>This may connect to the <em>factorization error</em> described in <a href="https://iscoyizj.github.io/discrete-action-website/"><em>When Do Discrete Actions Make Good Continuous Controllers?</em></a>. When action tokens are predicted independently, coordinates or time steps from incompatible modes can be combined into an incoherent action chunk.&nbsp;<sup><a aria-label="Reference 9" href="#ref-9">9</a></sup> In DSD, demonstrations that differ mainly in speed share direction targets while retaining distinct scale values. This may make it easier to maintain a consistent motion choice.</p>

<p>We observed the same failure pattern in the Avoiding task from <a href="https://github.com/ALRhub/d3il">D3IL</a>, a benchmark designed to study diverse demonstrated behaviors.&nbsp;<sup><a aria-label="Reference 10" href="#ref-10">10</a></sup> BIN’s trajectories combined incompatible avoidance choices and collided with obstacles. DSD maintained coherent motion around the obstacles. In our 480-rollout evaluation, BIN obtained 0% success and DSD obtained 100%.</p>
<figure class="dsd-results">
<div aria-label="D3IL Avoiding success rates" class="table-responsive" role="region" tabindex="0">
<table class="table table-sm">
<caption>D3IL Avoiding · success rates</caption>
<thead><tr><th scope="col">Method</th><th scope="col">Success rate</th></tr></thead>
<tbody><tr><th scope="row">BIN</th><td>0%</td></tr><tr><th scope="row">DSD</th><td>100%</td></tr></tbody>
</table>
</div>
<figcaption>Our additional evaluation on D3IL Avoiding with 480 rollouts.</figcaption>
</figure>
<div class="dsd-rollouts">
<figure>
<video controls muted playsinline preload="metadata" width="640" height="688" poster="https://duandaxia.github.io/assets/video/dsd-ablation/bin-avoiding-poster.png" aria-label="BIN obstacle-avoidance rollout ending in collision">
<source src="https://duandaxia.github.io/assets/video/dsd-ablation/bin-avoiding-collision.mp4" type="video/mp4">
Your browser does not support embedded video. <a href="https://duandaxia.github.io/assets/video/dsd-ablation/bin-avoiding-collision.mp4">Download the BIN rollout.</a>
</video>
<figcaption><strong>BIN · collision</strong>D3IL Avoiding rollout. The overlay reports execution of 20 out of 20 predicted actions.</figcaption>
</figure>
<figure>
<video controls muted playsinline preload="metadata" width="640" height="688" poster="https://duandaxia.github.io/assets/video/dsd-ablation/dsd-avoiding-poster.png" aria-label="DSD obstacle-avoidance rollout completing the task">
<source src="https://duandaxia.github.io/assets/video/dsd-ablation/dsd-avoiding-success.mp4" type="video/mp4">
Your browser does not support embedded video. <a href="https://duandaxia.github.io/assets/video/dsd-ablation/dsd-avoiding-success.mp4">Download the DSD rollout.</a>
</video>
<figcaption><strong>DSD · success</strong>D3IL Avoiding rollout. “DSN” in the video overlay refers to DSD.</figcaption>
</figure>
</div>
<p class="dsd-experiment-note">Example rollouts from the D3IL Avoiding evaluation.</p>

<p>DSD shows that a simple change in action representation can lead to substantial gains. The mechanisms behind these gains are still being explored, but the results make a compelling case for exploring action representation more closely, both to understand why DSD helps and to discover what other changes might make possible.</p>

</section>

<section aria-labelledby="references-heading" class="dsd-references" role="doc-endnotes">
<h2 id="references-heading">References</h2>
<ol>
<li id="ref-1" tabindex="-1"><a href="https://www.cs.utexas.edu/~eunsol/courses/data/bitter_lesson.pdf">Rich Sutton. The Bitter Lesson.</a></li>
<li id="ref-2" tabindex="-1"><a href="https://deepmind.google/blog/alphago-zero-starting-from-scratch/">Google DeepMind. AlphaGo Zero: Starting from scratch.</a></li>
<li id="ref-3" tabindex="-1"><a href="https://arxiv.org/abs/2609.28865">Duan et al. Direction-Scale Decomposition in Action Representation: Rethinking What to Tokenize for Vision-Language-Action Models.</a></li>
<li id="ref-4" tabindex="-1"><a href="https://vla-dsd.github.io/">DSD project page and demonstrations.</a></li>
<li id="ref-5" tabindex="-1"><a href="https://arxiv.org/abs/1512.03385">He et al. Deep Residual Learning for Image Recognition.</a></li>

<li id="ref-6" tabindex="-1"><a href="https://arxiv.org/abs/2406.09246">Kim et al. OpenVLA: An Open-Source Vision-Language-Action Model.</a> See also the authors’ <a href="https://github.com/openvla/openvla/issues/184">clarification of q01/q99 normalization before binning</a>.</li>
<li id="ref-7" tabindex="-1"><a href="https://arxiv.org/html/2607.06403#S6.SS1">Wu et al. From Foundation to Application: Improving VLA Models in Practice (LingBot-VLA 2.0).</a> Section 6.1, normalization ablation.</li>
<li id="ref-8" tabindex="-1"><a href="https://pokeandwiggle.com/research-replication/dsd">Poke &amp; Wiggle. Independent DSD replication, iteration 2.</a> October 12, 2026; includes normalization and implementation details.</li>
<li id="ref-9" tabindex="-1"><a href="https://iscoyizj.github.io/discrete-action-website/">Yi et al. When Do Discrete Actions Make Good Continuous Controllers?</a> See Section 4 of the <a href="https://iscoyizj.github.io/discrete-action-website/static/paper.pdf">paper</a> for factorization error across time and action coordinates.</li>
<li id="ref-10" tabindex="-1"><a href="https://proceedings.iclr.cc/paper_files/paper/2024/hash/b63cb503757f9f31e5d74e3567dd160a-Abstract-Conference.html">Jia et al. Towards Diverse Behaviors: A Benchmark for Imitation Learning with Human Demonstrations.</a> ICLR 2024; <a href="https://github.com/ALRhub/d3il">D3IL benchmark implementation</a>.</li>
</ol>
</section>

<a href="https://duandaxia.github.io/blog/">&larr; All posts</a>

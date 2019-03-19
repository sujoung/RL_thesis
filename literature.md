# Deep Reinforcement Learning for Dialogue Generation

[Li et al., 2016](https://arxiv.org/abs/1606.01541)

This paper introduces the dialogue generation model using SEQ2SEQ language model 
and policy gradient algorithm for deep reinforcement learning.
The dialogue here initializes with human utterance as an input then 2 agents
in system continues the dialogue in turns automatically.

* Language model  
They first used a supervised learning method to obtain a SEQ2SEQ model and in this step
they used OpenSubtitle corpus. Then they map the dialouges to 80 million source and the target pairs.
The pairs are vectorized using LSTM encoder.
The language model was trained with attention mechanism. ([Bahdanau et al., 2016](https://arxiv.org/abs/1409.0473))

* State, Action, Policy and Reward
  - State : vectorized form of two previous dialogue turns
  - Action : infinite list of predicted utterance
  - Policy : probabilistic distribution of actions given states
  - Reward : three reward factors are sumed with weights
  
Reward factors| To deal with...
--------------------| -------------
Ease of Answering *r_1*| preventing dull responses
Information Flow *r_2*| semantic similarity
Semantic Coherence *r_3*| mutual information

* Mutual information  
The SEQ2SEQ model was not directly fed into the policy model.
Instead they focus on generating maximum mutual information between the source and the target pair.
The mutual information was scored for the reward in the end of the sequence.

* Policy gradient  
To solve the problem of mutual information, they used policy gradient method initializing each action space per state
with the SEQ2SEQ model. The action space is a candidate list of actions. The objective function for
mutual information *m* then score the probability of action given the tuple of last two turns 
forwardly and backwardly using SEQ2SEQ model. 
Then the function *m* is directly used for the expected reward and this reward function is applied to the gradient estimates.
In order to solve this gradient function, they updated parameters using stochastic gradient descent.

* Curriculum learning  
The policy is trained in a fashion of curriculum learning which splits the sequences into two parts: one for the loss 
calculation using MLE and the other for the Reinforcement learning algorithm and gradually decrease the length of the
sequence for the MLE loss function and increase the sequences being fed into RL algorithm.


* Evaluation  
  They compare their RL system with standard SEQ2SEQ model ([Razato et al.,2016](https://arxiv.org/abs/1511.06732)) 
  and mutual information model ([Li et al., 2016](https://arxiv.org/abs/1510.03055)).
  - Automatic evaluation: No BLEU score and perplexity score are used because of the fundamental incompatibility 
  between BLEU which is related to simple MLE and the maximized reward in RL. 
  Instead length and diversity of the dialogue are measured. For the diversity beam search with size 10 is used to generate the next utterance.

  - Manual evaluation: Three judges are asked to pick better utterances in terms of three factors; single-turn general quality,
  single-turn ease to answer and multi-turn general quality.


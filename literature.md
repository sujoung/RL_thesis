# Deep Reinforcement Learning for Dialogue Generation

[Jiwei Li, Will Monroe, Alan Ritter, Michel Gally, Jianfeng Gao and Dan Jurafsky, 2016](https://arxiv.org/abs/1606.01541)

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
  
  
# End-to-End Task-Completion Neural Dialogue Systems
[Xiujun Li, Yun-Nung Chen, Lihong Li, Jianfeng Gao and Asli Celikyilmax, 2018](https://arxiv.org/abs/1703.01008)  
  - [Link to the source code](https://github.com/MiuLab/TC-Bot)  

The dialogue system that they suggested is based on supervised learning and Reinforcement learning.
Unlike the paper summarized above (Jiwei Li), they used Deep Q Network for the RL algorithm.
They divided their system into 2 parts: User simulator and the dialogue system.

* User simulator  
The user simulator sets up the virtual user's goal and generate sentences accordingly.

* Dialogue System  
The dialogue system understands the language by parsing the sentences with slots and intents and manages dialogue by tracking the dialogue status and also elaborating policy using DQN based RL.
  - NLU:  
In the NLU part, they also induced the simulated errors and test if system outputs the replies correctly.
NLU is operated with LSTM-based vectorized words, slot and intent.
Here slot means the key content of the utterance, e.g. RequestTicket = Intent, Moviename = slot and Jurassic Park = value for the slot.
They mark the boundary of slot with common BIO notation for the sequence labelling and the intent is marked at a sentence level.
  - RL: 
  They used target network and experience replay tricks --> Could not understand well and have to study DQN
* Evaluation  
They checked success rate and average turns with both RL model and rule-based model in terms of the frame-level semantics and language understanding. The detailed factors that they are exporing were intent and the slot level. They found out that slot-level-errors are more critical and among them when the system understands a wrong value for a certain slot, the performance becomes worse. Because it is very difficult for the system to find out that the value is correct or not.
  

# Learning Empathy-Driven Emotion Expressions using Affective Modulations
[Nikhil Churamani, Pablo Barros, Erik Strahl and Stefan Wermter](https://www2.informatik.uni-hamburg.de/wtm/publications/2018/CBSW18/Churamani_Learning_Empathy_Driven_Web_WTM.pdf)

This paper proposes the system that can interpret and generate facial expressions using offline based training and online leraning.
Their system aimed for the conversion of categories of emotions into continuous vectors so that an agent can read and write facial expressions representing two or more emotions.
The other important point for this study was that it focuses on long-term interaction between human and robot and also sets the reward system based on the context long-termly.
They suggested a structure for the system such as emotion perception using GWR(growing when required), affective memory, mood estimating, affective modulation (calculation into vectors), expression learning with actor-critic architecture.

* Expression learning  
1) Input form : 5-tuple Mood affective vectors 
2) Output form : eyebrows and mouth wavelet parameters with LED light on NICO robot  
   (yStretch, yOffset, xStretch, xOffset)  
3) Method: DDPG(deep deterministic policy gradient) based actor-critic architecture  
4) Actor: generate mouth and eyebrows expressions in 16-tuple according to policy pi   
5) Critic: calculate Q value given state(mood affect vector) and action(16-tuple that actor generates)  
           input processed through concatenated dense layer and then finally connected to output which predicts Q-value
+ Both actor and critic networks are modelled as feed-forward multilayer perceptron
6) Reward: Pre-defined reward function or user's direct assessment  
7) network training: off-policy using target networks 
8) update of the reward function : when a user evaluate aptness of the generated expression



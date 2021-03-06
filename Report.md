# Report

The algorithm for the agent is DDPG  with possibility of using the several improvements.

## Criteria
The environment was considered solved when the agent achieved average of 0.5+ over 100 episodes.

## Working principle of a DDPG

Generally in a DDPG there are 4 neural networks. Two for the actor network and the other two for the critic. The actor takes a state as input and returns the distribuion of actions as output. Where as a critic takes a state as input and outputs the state value function as per the policy.

The reason why we have 4 neural networks is each model the actor and critic have a local and a target model. Once we are done with an episode we copy the weights from the local network to the target network and update it's weights. The update implemented in the code is a soft-update process. 

 θ_target = τ*θ_local + (1 - τ)*θ_target 
 
 The above equation governs the update step.
 
 Also to improve the models performance we have implemented a OUNoise process. The detailed infromation about this is provided in the link below.
 
 http://web.math.ku.dk/~susanne/StatDiff/Overheads1b

## Hyperparameters

BUFFER_SIZE = int(1e6)  # replay buffer size
BUFFER_FILL = int(1e4) # How much of the buffer should be filled before learning
CACHE_SIZE = int(1e3)
NUM_UPDATES_CACHE = 2 # How many times to update from cache buffer
BATCH_SIZE = 256        # minibatch size

GAMMA = 0.99            # discount factor

TAU = 1e-3              # for soft update of target parameters

LR_ACTOR = 1e-3         # learning rate of the actor

LR_CRITIC = 1e-3        # learning rate of the critic

WEIGHT_DECAY = 0        # L2 weight decay

UPDATE_EVERY = 20       # timesteps between updates

NUM_UPDATES = 15        # num of update passes when updating

EPSILON = 1.0           # epsilon for the noise process added to the actions

EPSILON_DECAY = 1e-6    # decay for epsilon above

NOISE_SIGMA = 0.05

fc1_units=96

fc2_units=96

random_seed=23

RECREATE_EVERY=1 # Recreate agent models with new set of random weights

## Agent
### Layers
Both actor & critic use two fully connected layers.

### Activations
I sticked with a RELU for the hidden layers and tangens for the final layer. Lately I've trained
quite a lot of classifiers and I don't find other non saturating non-linearities worth the complexity
nor slowdown.

I feel that RELU quickly finds which neurons have signal. As a trick to decrease number of dead RELU
when I feel that model is relying on too few neurons, I sometimes switch to other activation functions
mid training like ELU or SELU to introduce some chaos and then switch back to RELU afterward.

### Neurons
I've settled on a 96 densely connected neurons on the both layers for both actor and critic, since it solved the environment in all the runs I've tried. Using more neurons solved the environment but it took more time. Using little less neurons like 72 took more time. While going to less then 64 neurons usually failed to solve the environment.

![96x96](96_96.png)
![256x256](256_256.png)


## Improvements

I especially aimed for decreasing the number of episodes needed to solve the environment.

### Number of Neurons
I feel that two layers of 96 neurons is close to optimal, since it consistently solved the environment from scratch. Using 48 & 64 neurons failed to solve it in 2000+ episodes.

### Smaller buffer
I've added second smaller buffer that holds 1,000 steps which reduced the training time. 
It has an effect of the agent focusing on the latest episodes.

### Random runs
I prefill the large buffer with 10,000 steps of experience generated by untrained agent.
Until the prefill is over I don't start any learning.

### Resetting agents
During the prefill process I reset the agent weights after every episode to get more random policies.

## Potential improvements
I think that try to stack the steps, or use recurrent neural network might decrease the training time. Other option is to use prioritized experience buffer. In my case smaller buffer is playing similar role, since last episodes are usually the best played.

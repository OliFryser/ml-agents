# Hand-in

## 2 Getting Started

a. The action space is represented by the two continous values representing the x and z components of the agent cube's rotation.
b. The observation space contains the x and z components of the agent's rotation, the ball's position (x, y, z) and velocity (x, y, z), as per the "Getting started" document. This makes the space size 8, since we have 8 components for the observation/state space.
c. If the ball falls off the agent (technically, if the ball is 2 world units below or 3 world units out in the xz plane), the agent gets a reward of -1. If the agent manages to keep the ball on it, it receives a reward of 0.1.
d. The architecture of the neural network is a two hidden layers with 128 neurons in each.
e. The batch size decides how many experiences are sampled from the buffer each time the model is trained. The learning rate decides how far we step on the gradient. The time horizon describes the amount of consecutive experiences that can be in an episode. If the episode ends sooner, i.e. the ball falls off, the episode ends normally. Otherwise it gets cut off and put on the buffer. Max steps describe the maximum number of steps taken before training is completed.

## 3 Hyperparameter tuning

a. PPO seems around double as fast in my testing, taking about 30 seconds per 12000 iterations, while SAC took around 60 seconds. However, as can be seen in the figure below, SAC reaches 100 cummulative reward faster than PPO.

![Comparison](comparison.png "Comparison of the accumulated reward (red PPO, blue SAC)")
# Hand-in

## 2 Getting Started

a. The action space is represented by the two continous values representing the x and z components of the agent cube's rotation.

b. The observation space contains the x and z components of the agent's rotation, the ball's position (x, y, z) and velocity (x, y, z), as per the "Getting started" document. This makes the space size 8, since we have 8 components for the observation/state space.

c. If the ball falls off the agent (technically, if the ball is 2 world units below or 3 world units out in the xz plane), the agent gets a reward of -1. If the agent manages to keep the ball on it, it receives a reward of 0.1.

d. The architecture of the neural network is a two hidden layers with 128 neurons in each.

e. The batch size decides how many experiences are sampled from the buffer each time the model is trained. The learning rate decides how far we step on the gradient. The time horizon describes the amount of consecutive experiences that can be in an episode. If the episode ends sooner, i.e. the ball falls off, the episode ends normally. Otherwise it gets cut off and put on the buffer. Max steps describe the maximum number of steps taken before training is completed.

## 3 Hyperparameter tuning

a. PPO seems around double as fast in my testing, taking about 30 seconds per 12000 iterations, while SAC took around 60 seconds. However, as can be seen in the figure below, SAC reaches 100 cummulative reward faster than PPO.

![Comparison of PPO and SAC](comparison.png "Comparison of the accumulated reward (red PPO, blue SAC)")

b. The model can no longer find a good solution. After 500000 iterations, the accumulated mean reward is 1.767 with a standard deviation of 1.225.

c. With 4 neurons in a single hidden layer, it managed to find a good solution. It seemed pretty unstable, however, and took a long time to settle on a good solution. Below is a figure showing what it found. While not being completely obvious, it did find a solution with a mean reward of 100.

![Comparison of architectures](4Neurons.png "Comparison of the baseline architecture (128 neruons x 2 layers) with the single neuron and 4 neuron architecture.")

d. While the model did improve, the improvement happened much slower. In 500000 iterations, it did not reach a cummulative reward mean of 100, but it may have, given more time.

e. The learning rate is so high that we completely miss the local minimums. This results in very irratic behavior from the agent, likely because the weights get very large. The agent never reaches a good mean reward, because it cannot stabilize at a local minimum. I stopped the training after 288000 iterations.

![Comparison of learning rataes](LearningRates.png "Comparison of the effect of large and small learning rates, compared to the baseline (0.0003)")

f. To find a good learning rate, I would basically go by trial and error, until I found something that works - that is, something that is quickly approximating a good solution.

## Reward Shaping

a. Reward functions

### Centered

```cs
var distanceToCenter =
    Vector2.Distance(
        new Vector2(ball.transform.position.x, ball.transform.position.z),
        new Vector2(transform.position.x, transform.position.z));
SetReward(0.1f * 1 / (distanceToCenter + 0.0000001f));
```

### Corner-focused

```cs
float cubeWidth = 3;
            float dx = ball.transform.position.x - transform.position.x + cubeWidth * .5f;
            float dz = ball.transform.position.z - transform.position.z + cubeWidth * .5f;
            SetReward(Mathf.Exp(-(dx * dx + dz * dz)));
```

### Changing corners

```cs
time++;
float cubeWidth = 3;
Vector2 offset = new Vector2(cubeWidth * .5f, cubeWidth * .5f);
var t = time % 400;
if (t > 200f)
    offset *= -1;

float dx = ball.transform.position.x - transform.position.x + offset.x;
float dz = ball.transform.position.z - transform.position.z + offset.y;
SetReward(Mathf.Exp(-(dx * dx + dz * dz)));
            ```
### Reinforcement Learning-related

#### [1.RL-Cache: Learning-Based Cache Admission for Content Delivery](../Papers/Related%20works%20of%20Reinforcement%20Learning/RL-Cache.pdf)
**Main Idea**: 
- use reinforcement learning to decide whether to cache a requested object
**Motivation**: 
- introduce more features than heuristic algorithms

**Algorithm**:
- k Request in a window
- Generate m simulation using the agent
- Compute hit rate
- Optimize towards the higher hit rate outcome
- Converge situation: weight not significantly change

**Notes**:
- Still for user-space application

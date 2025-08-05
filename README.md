# CodinGame Summer Challenge 2025 - Post-Mortem

This repository contains the final code for my bot for the CodinGame Summer Challenge 2025. I'm proud to have finished **79th in the Legend league** after a challenging and rewarding competition.

You can view my CodinGame profile here: **[_H3R0_](https://www.codingame.com/profile/509ac1c733bdcdabf6e337072359f8e97822085)**

---

## Overall Strategy: A UCT Forest (Smitsimax)

The core of my bot was a **UCT Forest**, a term for applying Monte Carlo Tree Search to simultaneous-move games by giving each agent its own search tree. This approach, also known as Smitsimax, allows each agent to explore its own options independently.

During the search, a full turn is constructed by selecting one action-branch from each agent's tree. The combined outcome of this "turn" is then evaluated, and the score is propagated back up to all the parent nodes in every tree. Over thousands of simulations, this process allows cooperative and counter-active strategies to emerge without being explicitly coded, as the trees implicitly influence each other to find a strong equilibrium.

## Key Architectural Decisions

To make this strategy viable within the time limits, I made three critical architectural decisions:

#### 1. Aggressive Heuristic Pruning
The combinatorial explosion of possible moves in a multi-agent game makes a naive search impossible. To solve this, my first critical decision was to **aggressively prune the action space**. Before a node was expanded, a fast, lightweight heuristic evaluated every possible action for an agent based on tactical factors like safety, damage potential, and optimal positioning. Only a small subset of the highest-scoring actions were ever considered for inclusion in the search tree. This step was essential for focusing the computational power on strategically relevant moves from the very beginning.

#### 2. Deterministic, UCT-Guided Simulations
My second decision was to replace traditional, random MCTS rollouts with a **fixed-depth, UCT-guided simulation**. Instead of playing out moves randomly to the end of the game, my bot would simulate just a few turns ahead. At each step of this short simulation, the next moves were chosen by applying the same UCT selection formula used in the main tree. This created far stronger and more realistic playouts, providing a much cleaner signal to the evaluation function compared to the high variance and noise of purely random simulations.

#### 3. A Handcrafted Evaluation Function
At the end of a fixed-depth simulation, the resulting game state was scored by a **handwritten evaluation function**. This function served as the bot's "intuition," judging the quality of a position. It was a carefully balanced linear combination of the most important game-winning factors: the cumulative score from long-term map control, the overall health advantage of my team, immediate tile control, and resource superiority (bombs and cooldowns). The relative weights of these factors were the primary parameters I tuned throughout the competition to refine the bot's strategic priorities.

## The Competition Journey

My competition started with a heavy focus on engineering, building a high-performance simulation engine that could serve as a fast and reliable foundation. Armed with this, I implemented the UCT Forest, expecting it to perform well immediately. However, the bot quickly stalled in the Gold league, which was a significant roadblock. The search was fast, but it was exploring useless states, indicating a deep flaw in its understanding of the game.

The breakthrough only came after a long and frustrating process of debugging and re-calibrating the handwritten evaluation function. It was a classic lesson in AI development: a powerful search algorithm is completely blind without a good heuristic to guide it. Once the evaluation was improved, the bot began its final, successful climb into the Legend league.

Ultimately, this competition was a fantastic exercise in debugging complex AI systems and a powerful reminder that the "art" of bot-making often lies in crafting the evaluation function that gives the search its direction.

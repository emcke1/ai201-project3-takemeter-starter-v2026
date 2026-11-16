1. There are some post with metrics based on personnel(cast and crew) and timeline(rehearsal day of performance)

2. Some posts are from people getting into acting for the first time.

3. Majority of the posts ask for advice on how to execute a production (staging, casting, tech, scheduling).


## Seed 42 first run (device: mps, Apple Silicon GPU)

- 23 posts: reaction 14, analysis 5, hot_take 4
- Split: train 15, val 4, test 4 (test = 1 analysis, 1 hot_take, 2 reaction)
- Accuracy 0.50, macro F1 0.222
- F1: analysis 0.00, hot_take 0.00, reaction 0.667
- The model predicted reaction for every test post. It got the 2 real reaction
  posts right and called the analysis and hot_take posts reaction too.
- Validation loss barely moved (1.084 to 1.068) and validation accuracy stayed
  at 0.50 for all 3 epochs, so the model didn't learn to separate the labels.
- Why: only 3 analysis and 2 hot_take posts in training, against 10 reaction.
  Always guessing the majority label was the safest strategy available to it.
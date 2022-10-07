# Dacon : Code Similarity

## 🏆 Goal
Classification : Determine whether the given two codes are same or not. 

## 📋 LeaderBoard
- 19th out of 337 teams

## Data
- Text (Code written in Python)

## 🔖 Retrospection

The basic idea is not that hard due to pre-trained model of [CodeBert developed by Microsoft](https://github.com/microsoft/CodeBERT). I also tried another model, GraphCodeBert (Microsoft), but the performance with the same parameter was worse than CodeBert.

In this case, there is nothing to mention about the model - I changed nothing but fine-tuning.

### Data Prep.
The major issue of this competition dataset is that the input is written by Japanese programmers, therefore the comments in the file is written in Japaneses. I wish to use it, but translation tools were not good as much as I thought. I had no choice, I had removed all the comments. 

Another issue was variable name, they were not consistent. From my expectation, GraphCodeBert may have superior performance for this reason, well.. it was not. 

Removing driver code like
```python
if __name__ == "__main__":
    do_something()
```
is not a hard job, but many of participants are seemed to forget. I removed `if __name__ == "__main__":`and un-tapped rest of codes. 

After clean the inputs, I found some codes were too long to put into model - CodeBert requires to max length of 512 like the most of transformer-based model. Also there were 'too short case' - in dataset it looks like short-coding. 

So, the solution is always truncation and padding, and I truncated left-side (thereby input would be a later parts of code) since the first few lines usually consist of defining variables and read dataset. Additionally, when it comes to padding, I padded left-side too to compare at least similiar parts. (main components and driver code)

### Make Input Pairs

The dataset is just set of code respects to their problem number. Intuitively speaking, the codes are placed in the same folder if they work for the same problem. Test case is pre-defiend as pair. 

To prevent the a code is used for both of train and valid set, train-valid split should be done before make pairs.

It is a risky idea in terms of reproducing, but I used random sample.

For positive pairs (label is True as two codes are written for the same problem), simply made `combinations` of all codes and random sampled for the given amount (since I'm using colab pro, 24hr limitation is crucial.)

But for negative pairs (label is False as two codes are not for the same problem), there are three steps.
1. Subsets of each problem should be equal amount, so stratified sampling is required. the output is all the codes those not belong to the positive pair problem number.
2. Make Pairs with `product`
3. Sample from the negative products with same amount of total positive pairs.

This worked well compared to well-constructed methods that the other participants did. 

Total train dataset is 150,000 and valid set is 30,000

### Train
If you are working with `Google Colab` (including pro), this would be the problem always especially your model is big. 

In this case, total train process for 3 epochs takes about 24 hrs, it was horrible. (This why I did not take awesome ensemble methods in this competition.)

Well, now that I think about it, using larger dataset (I abandoned significant size) and train only one epoch for 24hr is a better idea. 

It may.. take.. at least one week to produce one ouput but concrete.

### Comment

The winning team applied ensemble, I might think that they have additional resources - such as personal GPU.

These days, all the competitions requires to use personal setups. This may indicate that the most of participants are enough competitive compared to 3, 4 years.

Colab was enough in the past, but this would be not true I think. This is not a clear idea, however, I think students who want to persue their career in the field of DS are mandated to spend more money on using server or GPU.

During my the master's degree, I was fortunate - I had been allowed to use super computer for free due to my master's project. I hope as many people can have such chance. 



# Kaggle : Amex Default Classification

## 🏆 Goal
- Binary Classification

## 📋 LeaderBoard
- 2034th Out of 4876 Teams

## Data
- Unevenly distributed Time Series
    ```
    D_* = Delinquency variables
    S_* = Spend variables
    P_* = Payment variables
    B_* = Balance variables
    R_* = Risk variables
    ```
- Target
    - Default Or Not 

## 🔖 Retrospection

### Difficulty

1. Super Large Dataset
    
    The raw dataset is almost 50GB. It is hard to load on a Google Colab although it is ```Pro``` version. Data aggregation is forced.
    
2. Unevenly distributed Dataset
    
    There are two points on this issue
    
    - Uneven Time Interval
        It seems that the dataset represents monthly bill for each customer. The problem is, the interval between each of bill is not consistent - 28 days to 45 days. 
    
    - Imbalance in Label
        The trait of this dataset: ***Very small number of customer go bankrupt.*** Only 5% of people are labeled as 1, "default".
    
    - Train-Test Dataset Imbalance 
        Train data is over 16GB and test data is alomst 34GB. It is too large for personal machine, therefore, aggregation is forced. 
        
    - Missing Values
        
        There is non-negligible amount of NAs in certain variables. We should decide how fill out these variables.
        

### Data Prep.

#### Aggregation vs. Augmentation

​	We can summarize all competitors' methods in two types of approaches.

 	1. Aggregating by Customer ID
     	- Decision Tree Models
     	- Tabular NN models 
 	2. Augmenting past history
     - NN  : RNN, GRU, LSTM

​	My personal test showed that the first approach is superior to the latter.  I think this is maninly due to 'Augument' itself. It works nicely on image-related ML models as shown in SOTA super resolution models ([check this awesome work](https://github.com/stroking-fishes-ml-corp/A-ESRGAN) and [repo](https://github.com/ChaofWang/Awesome-Super-Resolution#2022)). But the problem is - we are not dealing with unstructured data. What if we want to 'augment' dataset, our expectation is generating new information by referencing. Unfortunately, augment past history is nothing more than filling with zeores and null valeus (for categoricals). I still believe augmentation is *theoretically justifiable* idea to make input for RNN families by giving them a same length, simply put, each of customers truly does not have previous history! But ***practically***? 

​	If not for competition, augmentation might works.  However, AMEX  intentionally made (pretty a lot of) holes in dataset like every company-hosted competitions. Competitors found that random noise injection, deliberated random missing values and sometime removed row. From this point, augmenation just adds more noise. 

​	I also finalize to use aggregation. Instead, I made more effort to fill missings through interpolation for all continuous variables by each customer. Aggregation can allow us to compress dataset into 5% of its orignal size. 

#### Feature Engineering

​	Valid variables I tried.

- Time-lagged varables : t-1 to t-6
    - Worked for t-1 to t-3.
- Continuous Variables
    - (raw and first-order difference) last - mean, last-median, last-first
        - the default point is last value of each customer history.
    - Skewedness, standard deviation of first-order difference
        - To capture abnormal history.
        - The final binary prediction is same with outlier detection. 

- Categorical Variables
    - Conditional Probability
        - With the given first value, what is the probability of the last value?

The variables added to final model are conditional probability and last-mean difference as these two variables give the best increase in PB score. 

### Model

​	I tried the models below.

- Decision Tree : XGBoost, LGBM, CatBoost
- Tabular NN Models : 
    - [TabNet](https://github.com/dreamquark-ai/tabnet)
    - [TabTransformer and FT Transformer](https://github.com/lucidrains/tab-transformer-pytorch)
    - [SAINT](https://github.com/somepago/saint)
    - [Wide and Deep for tabular](https://github.com/jrzaurin/pytorch-widedeep)

​	All models, even without fine-tuning, showed PB score 0.78 to 0.8. The performance definitely gets better when new model added to ensemble but I finalized the ensemble of LGBM and CatBoost due to train time. It is well known that NN models are time-consuming, especially the case we should set such a low, low learning rate. To be honest, it was not that small, I put 5e-3 to 5e-5. But there are too many varirables which is cumbersome to Colab. 

### Comment

 Obiously top competitors applied large ensemble models with highly tuned parameters. Considering that the fastest model (in this case LGBM) takes about 20 hours for GPU training with K-fold and early stopping, I think such model is not an economic approach. The difference between top performance and mine was only 0.005, I'm still not sure how it is meaningful when it comes to real-world. 

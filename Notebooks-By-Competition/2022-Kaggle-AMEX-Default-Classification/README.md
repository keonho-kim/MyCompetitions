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
        

### Data Prep

​	We can summarize all competitors methods in two types of approaches.

 	1. Aggregating by Customer ID
     	- Decision Tree Models : XGBoost, LGBM and CatBoost
     	- Tabular NN models : TabNet, TabTransformer, FT Transformer, SAINT
 	2. Augmenting past history
     - NN approaches : RNN, GRU, LSTM

​	My personal test showed that the first approach is superior to the latter.  I think this is maninly due to 'Augument' itself. It works nicely on image-related ML models as shown in SOTA super resolution models ([check this awesome work](https://github.com/stroking-fishes-ml-corp/A-ESRGAN) and [repo](https://github.com/ChaofWang/Awesome-Super-Resolution#2022)). But the problem is - we are not dealing with unstructured data. What if we want to 'augment' dataset, our expectation is generating new information by referencing. Unfortunately, augment past history is nothing more than filling with zeores and null valeus (for categoricals). I still believe augmentation is *theoretically justifiable* idea to make input for RNN families by giving them a same length. But ***practically***? 

​	If not for competition, augmentation might works. From my point of view, AMEX  intentionally made (pretty a lot of) holes in dataset like every company-hosted competitions. Noise injection, deliberated random missing values and sometime whole row is removed. 

### Model




### Comment

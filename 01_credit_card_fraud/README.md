# Credit Card Fraud Analytics

## The problem
Banks and mobile money providers lose money when fraudulent payments slip through, and they annoy customers when genuine payments are blocked. In Kenya, where M-Pesa and card payments are used every day, catching fraud quickly matters a lot.

Fraud is also very rare (well under 1% of transactions). A model that says "everything is fine" would be over 99% accurate and completely useless, so this project uses metrics that handle rare events properly.

## Dataset
[Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) (Kaggle, ULB).
- 284,807 European card transactions from two days in 2013
- 492 are fraud
- Features V1 to V28 are anonymised for privacy
- Note: this is not Kenyan data. It is used here because it is the dataset specified in the internship brief.

Download `creditcard.csv` and place it in the `data/` folder. The file is too large for GitHub, so it is not included in this repo.

## What I did
1. Checked and cleaned the data
2. Looked at how rare fraud is (class imbalance)
3. Explored amounts and timing of fraud
4. Split the data into train, validation and test sets
5. Tried three models and several ways of handling the imbalance
6. Measured them with Precision, Recall, F1, ROC-AUC and PR-AUC
7. Chose the decision threshold using business costs
8. Explained what false alarms and missed fraud cost

## Results
(To be filled in after running the notebook.)

## Recommendations
(To be filled in after running the notebook.)

## How to run
1. Install the packages: `pip install -r ../requirements.txt`
2. Put `creditcard.csv` in `data/`
3. Open `fraud_analytics.ipynb` in Jupyter or VS Code and run all cells
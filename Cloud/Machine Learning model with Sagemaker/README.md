## Bank Marketing Prediction using Amazon SageMaker XGBoost ##
# Project Overview

This project demonstrates the end-to-end workflow for building, training, deploying, and evaluating a binary classification machine learning model on Amazon SageMaker. The model predicts whether a customer will subscribe to a term deposit based on marketing campaign data.

We use the Bank Marketing Dataset, preprocess it, train an XGBoost model, deploy it as a SageMaker endpoint, and evaluate its performance on test data.

# Project Steps
1. Import Libraries and Define Environment

We begin by importing the required Python libraries and defining the SageMaker execution environment:

import boto3, sagemaker, urllib.request, numpy as np, pandas as pd, os
from sagemaker import get_execution_role
from sagemaker.serializers import CSVSerializer
import matplotlib.pyplot as plt
from IPython.display import display, Image
from time import gmtime, strftime

# Define IAM role and session variables
role = get_execution_role()
prefix = 'sagemaker/DEMO-xgboost-dm'
my_region = boto3.session.Session().region_name

# Retrieve the XGBoost container URI
xgboost_container = sagemaker.image_uris.retrieve("xgboost", my_region, "latest")

Purpose: Sets up the SageMaker session, IAM role, and identifies the XGBoost container for training.

2. Create S3 Bucket

All training and output data is stored in Amazon S3:

bucket_name = 'your-unique-s3-bucket-name'
s3 = boto3.resource('s3')

if my_region == 'us-east-1':
    s3.create_bucket(Bucket=bucket_name)
else:
    s3.create_bucket(
        Bucket=bucket_name,
        CreateBucketConfiguration={'LocationConstraint': my_region}
    )

Purpose: Provides storage for training data, model artifacts, and predictions.

3. Download and Load Data

We download the Bank Marketing dataset and load it into a Pandas DataFrame:

urllib.request.urlretrieve(
    "https://d1.awsstatic.com/tmt/build-train-deploy-machine-learning-model-sagemaker/bank_clean.27f01fbbdf43271788427f3682996ae29ceca05d.csv",
    "bank_clean.csv"
)

model_data = pd.read_csv("bank_clean.csv", index_col=0)

Purpose: Prepares raw data for preprocessing and training.

4. Shuffle and Split Data

We randomly shuffle the dataset and split it into training (70%) and test (30%) sets:

train_data, test_data = np.split(
    model_data.sample(frac=1, random_state=1729),
    [int(0.7 * len(model_data))]
)

Training data is used to fit the XGBoost model.

Test data is used to evaluate how well the model generalizes to unseen data.

5. Prepare Training Data for XGBoost

XGBoost requires the target column to be first and no header row in the CSV:

pd.concat(
    [train_data['y_yes'], train_data.drop(['y_no','y_yes'], axis=1)],
    axis=1
).to_csv('train.csv', index=False, header=False)

Purpose: Formats data for SageMaker’s prebuilt XGBoost algorithm.

6. Upload Training Data to S3
boto3.Session().resource('s3') \
    .Bucket(bucket_name) \
    .Object(os.path.join(prefix, 'train/train.csv')) \
    .upload_file('train.csv')

Purpose: Stores training CSV in S3 for SageMaker to access.

7. Configure SageMaker Estimator

We define the XGBoost estimator and hyperparameters:

sess = sagemaker.Session()

xgb = sagemaker.estimator.Estimator(
    image_uri=xgboost_container,
    role=role,
    instance_count=1,
    instance_type='ml.m4.xlarge',
    output_path=f's3://{bucket_name}/{prefix}/output',
    sagemaker_session=sess
)

xgb.set_hyperparameters(
    max_depth=5,
    eta=0.2,
    gamma=4,
    min_child_weight=6,
    subsample=0.8,
    objective='binary:logistic',
    num_round=100
)

Hyperparameters control model complexity, learning rate, and regularization.

8. Train the Model
xgb.fit({'train': s3_input_train})

Purpose: Launches a training job on ml.m4.xlarge and optimizes model parameters using gradient-based optimization.

9. Deploy the Model
xgb_predictor = xgb.deploy(
    initial_instance_count=1,
    instance_type='ml.m4.xlarge'
)

Purpose: Creates a real-time endpoint for making predictions.

10. Make Predictions on Test Data
test_data_array = test_data.drop(['y_no','y_yes'], axis=1).values
xgb_predictor.serializer = CSVSerializer()
predictions = xgb_predictor.predict(test_data_array).decode('utf-8')
predictions_array = np.fromstring(predictions, sep=',')

Converts test features into an array for model inference.

predictions_array contains the predicted probabilities of a customer subscribing.

11. Evaluate Model Performance
import numpy as np
import pandas as pd

threshold = 0.5
predicted_labels = (predictions_array >= threshold).astype(int)

cm = pd.crosstab(
    index=test_data['y_yes'],
    columns=predicted_labels,
    rownames=['Observed'],
    colnames=['Predicted']
)

tn = cm.iloc[0,0]; fn = cm.iloc[1,0]; tp = cm.iloc[1,1]; fp = cm.iloc[0,1]
accuracy = (tp+tn)/(tp+tn+fp+fn) * 100

print("Overall Classification Rate:", round(accuracy,2), "%")
print(cm)

Purpose: Compares actual vs predicted outcomes using a confusion matrix.

Outcome: Measures accuracy, precision, and recall of the model.

12. Cleanup (Optional but Recommended)

After evaluation, delete the endpoint to stop charges:

xgb_predictor.delete_endpoint()
Key Learnings

SageMaker simplifies end-to-end ML workflow:

Data preparation

Model training

Deployment

Prediction

Evaluation

XGBoost is effective for binary classification tasks.

Using thresholds allows tuning precision vs recall for business objectives.

SageMaker endpoints incur cost until deleted, so cleanup is crucial.

References

https://docs.aws.amazon.com/sagemaker/latest/dg/gs-setup-working-env.html

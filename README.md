# Predicting Maternal Health Risk using AutoML and Hyperdrive with Azure

This project serves as the capstone to the Machine Learning Engineer with Microsoft Azure Nanodegree. 

### Task

To predict when a pregnant patient is at high risk based on their Age, Systolic Blood Pressure, Diastolic Blood Pressure, Blood Sugar, Body Temperature, and Heart Rate, I will create two models: one using Automated ML (AutoML) and one customized model whose hyperparameters are tuned using HyperDrive. I will then compare the performance of both the models and deploy the best performing model.

## Project Set Up and Installation
In Azure, a workspace must be configured, and a compute instance allocated. Because I did this project as part of a Udacity Nanodegree, these resources were provided. 

## Dataset

### Overview

The data comes from this kaggle source: https://www.kaggle.com/datasets/pyuxbhatt/maternal-health-risk 

It includes the Age, Systolic Blood Pressure, Diastolic Blood Pressure, Blood Sugar, Body Temperature, Heart Rate, and associated Maternal Health Risk of 1014 unidentified patients. 

From this link, I downloaded the csv. In MS Excel, I modified the outcome column so that instead of classifying between Low Risk, Medium Risk, and High Risk, there is just one binary column called HighRisk. I am only interested in predicting high risk pregnancies. 


### Access


I uploaded my modified csv to Azure blob storage as a tabular dataset and registered it as maternal-health-risk. 
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/344a3a5d-a05b-4243-9622-2c0c34f5b131)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/1982cd11-0950-4887-9af8-f9409744341a)

From there I could call it into my notebook by name:
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/5123b15f-9699-40cd-bfd6-bb3e5121c6b2)


## Automated ML

The AutoML configuration settings are shown below in this screenshot:
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/131d7dbd-a0df-4850-aa79-918ca2bf2272)

Additionally, training time was limited to 0.5 hours, and a maximum of 3 concurrent iterations were allowed. 

### Results

The best AutoML model was a VotingEnsemble algorithm that compared several classification models and selected the best one. It had an ensemble weight of 0.125. It ensembled several classifier/scaler combinations. The combination chosen by the voting ensemble used a StandardScalerWrapper with an XGBoost classifier, with a weighted AUC of 0.98181354 and an accuracy of 0.93590. See the final screenshot below for the code provided by AutoML showing exactly how the parameters were set for data transformation and for the training algorithm under Ensemble Details. 

![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/df548465-3427-41fe-8afb-9a473a263c36)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/843be94d-b080-4fc1-8162-d6f0a01ebcc5)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/10e50c3a-98ec-46e6-9472-6876f3f784fe)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/4ae6ba5a-1570-44e4-bd3d-935cbfdcff79)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/d4d92330-e95d-453d-abec-8b4aaeca6f8d)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/6a392e97-e843-4077-bc61-466c1324c264)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/faba5a8a-f8db-47e0-942a-997ed9b74bf5)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/31eebc0c-c6d5-44ff-967c-a37787718555)


## Hyperparameter Tuning

I chose a logistic regression model for hyperparameter tuning because they perform well with binary outcomes such as this scenario, HighRisk or not. The two parameters tested were Regularization Strength and Maximum Iterations, and models were ranked by accuracy. 

### Results

Interestingly, several combinations of the parameters resulted in 100% accuracy. In data science, 100% accuracy is almost never accepted. Presumably, there is overfitting, or worst case scenario, data leakage. However, the best model was selected with a regularization strength of 0.37089 and a maximum iterations of 50. To improve, the source of the accuracy must be identified. These models cannot be trusted. Due to these dubious results, the AutoML model will be deployed, even though it has a lower accuracy.  


![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/32c65b6a-fac0-4cd3-aa07-21bcf89dc499)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/3987846b-142b-4884-98f2-617b8d6c3564)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/5f6737f4-452c-4975-9ed3-7b4691049f67)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/1e4786d4-9490-48ad-b84d-66a7a12b8c67)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/6e2041cf-aa5d-4bb9-baa4-5af74e37dc06)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/ed2a9908-e64a-4661-b271-21009f15a432)
![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/f9430621-2848-419e-ab51-e72b50094892)


## Model Deployment

The best AutoML model was deployed using code in the automl.ipynb. Once deployed, it can be viewed under the Endpoints tab. This provides the scoring URI and code for testing the endpoints, as well as space to test the endpoints using input data. This process can be viewed in the screen recording below. 

![image](https://github.com/fische57/Nanodegree-Capstone-MLOps/assets/52047242/698da9fb-dae2-46ef-bf26-3aa61bfae229)

## How to Improve

To improve this project, I must identify the reason that several of the hyperdrive configurations resulted in 100% accuracy. There may be overfitting or data leakage, and if there is data leakage, neither model will be valid in production. Another way to improve would be to further delve into which variables were most predictive in determining maternal health risk. From my testing, increasing the Blood Sugar variable to 15.0 guaranteed a result of 1 (High Risk). It would be interesting to better understand the relationships of these variables in the prediction to provide data-driven guidance to pregnant patients. Another step for a polished project would be to deply my model to the Edge using Azure IoT Edge. This would require an additional jupyter notebook.

## Screen Recording
Link to screen recording: https://youtu.be/pete0JNMyHU

This screencast demonstrates:
- A working model
- Demo of the deployed  model
- Demo of a sample request sent to the endpoint and its response

  



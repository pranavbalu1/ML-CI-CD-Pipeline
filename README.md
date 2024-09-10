# ML-CI-CD-Pipeline
### **Project: MLOps Pipeline with SageMaker, CodePipeline, and CodeBuild**

---

### **Step 1: Set Up the Environment**

1. **Create an S3 Bucket:**
   - You'll need an S3 bucket to store your data, model artifacts, and other files. Go to the S3 console and create a bucket with a unique name (e.g., `mlops-sagemaker-bucket`).

2. **Set Up AWS IAM Roles:**
   - Ensure you have the correct permissions for SageMaker, CodePipeline, and CodeBuild.
   - Create an IAM role for SageMaker with `AmazonSageMakerFullAccess` and `S3FullAccess` permissions.
   - Create another role for CodePipeline with permissions to access SageMaker, CodeBuild, and S3.

3. **Create a SageMaker Notebook:**
   - Launch a SageMaker notebook instance in the AWS Console for development. Use this to develop and test your machine learning models.
   - Ensure the notebook instance has access to your S3 bucket by attaching the proper IAM role.

---

### **Step 2: Create a Machine Learning Model in SageMaker**

1. **Select a Dataset:**
   - You can use a dataset like the [Titanic dataset](https://www.kaggle.com/c/titanic/data) for binary classification (survival prediction).
   - Upload this dataset to your S3 bucket (e.g., `s3://mlops-sagemaker-bucket/titanic.csv`).

2. **Preprocess the Data:**
   - Use the SageMaker notebook to preprocess the data using `Pandas` and `Scikit-learn`.
   - Split the dataset into training and testing sets.

3. **Train the Model:**
   - Choose a SageMaker built-in algorithm like XGBoost for binary classification.
   - Define a training job in SageMaker using the preprocessed dataset stored in S3.
   - Once trained, save the model artifacts to your S3 bucket.

4. **Deploy the Model:**
   - Use SageMaker's one-click deployment to deploy the model to an endpoint for inference.
   - Test the deployed model by making predictions on test data.

---

### **Step 3: Set Up AWS CodeCommit for Version Control**

1. **Create a CodeCommit Repository:**
   - Go to the AWS CodeCommit console and create a new repository (e.g., `mlops-sagemaker-repo`).
   - Clone this repository to your local machine or SageMaker notebook instance using Git.

2. **Push ML Code to the Repository:**
   - Organize your project files in the repository:
     - `train.py` - script to train the model.
     - `preprocess.py` - script for data preprocessing.
     - `deploy.py` - script to deploy the model.
     - `buildspec.yml` - defines how CodeBuild will run your ML workflow.
   - Push the code to CodeCommit.

---

### **Step 4: Create a CodeBuild Project**

1. **Set Up CodeBuild:**
   - Go to the AWS CodeBuild console and create a new project (e.g., `mlops-sagemaker-build`).
   - Configure the build environment:
     - Use a managed image with Python (e.g., `aws/codebuild/standard:5.0`).
     - Make sure the IAM role for CodeBuild has permission to run SageMaker jobs and access S3.
   
2. **Define the Buildspec File (`buildspec.yml`):**
   - Create a `buildspec.yml` file in the root of your CodeCommit repository to define how CodeBuild should run your ML pipeline. Here’s an example of a `buildspec.yml` file:

   ```yaml
   version: 0.2
   phases:
     install:
       commands:
         - echo Installing dependencies...
         - pip install -r requirements.txt
     pre_build:
       commands:
         - echo Preprocessing data...
         - python preprocess.py
     build:
       commands:
         - echo Training model...
         - python train.py
     post_build:
       commands:
         - echo Deploying model...
         - python deploy.py
   artifacts:
     files:
       - '**/*'
   ```

3. **Connect CodeBuild to CodeCommit:**
   - Specify the CodeCommit repository (`mlops-sagemaker-repo`) as the source for CodeBuild.
   - This will trigger the build process every time a new commit is pushed to the repository.

---

### **Step 5: Create a CodePipeline for CI/CD**

1. **Create a New Pipeline in CodePipeline:**
   - Go to the AWS CodePipeline console and create a new pipeline (e.g., `mlops-sagemaker-pipeline`).
   - Select your CodeCommit repository as the source for the pipeline.

2. **Add CodeBuild to the Pipeline:**
   - Add the CodeBuild project (`mlops-sagemaker-build`) as the build step in the pipeline.
   - This ensures that CodeBuild runs your ML workflow (preprocessing, training, deployment) when new code is committed.

3. **Configure SageMaker for Model Deployment:**
   - Add a custom action in the pipeline to deploy the trained model using SageMaker after the build is successful.
   - This custom action will trigger the `deploy.py` script from the `buildspec.yml`, deploying the model to SageMaker.

---

### **Step 6: Automate Hyperparameter Tuning (Optional)**

1. **Set Up Hyperparameter Tuning Job:**
   - In SageMaker, set up a hyperparameter tuning job to automatically explore different hyperparameter combinations (e.g., learning rate, max depth) for your model.
   - Add the tuning job in the CodeBuild step to optimize model training automatically before deployment.

---

### **Step 7: Monitor Model Performance with CloudWatch**

1. **Enable Model Monitoring:**
   - Use SageMaker Model Monitor to track model performance and detect data drift over time.
   - Set up CloudWatch alarms to notify you if the model performance degrades (e.g., accuracy drops below a threshold).

2. **Integrate Model Monitor into the Pipeline:**
   - Add a step in the pipeline that runs periodic model evaluations using the `ModelMonitor` service and pushes the results to CloudWatch.

---

### **Step 8: Test the CI/CD Pipeline**

1. **Push Code to CodeCommit:**
   - Make changes to the ML code (e.g., adjust preprocessing steps or model architecture) and push to CodeCommit.
   - This will trigger the entire pipeline automatically, starting with CodeBuild.

2. **Monitor Pipeline Execution:**
   - Go to the CodePipeline console to monitor the pipeline’s progress through each stage (source -> build -> deploy).
   - Verify that the model is trained, deployed, and live for real-time inference after each code change.

---

### **Step 9: Showcase Your Project**

1. **Create a Demo Application:**
   - Develop a simple web or mobile app (e.g., with Flask, React, or Streamlit) that interacts with the deployed model endpoint.
   - Allow users to upload input (e.g., customer data for the Titanic survival model) and get predictions from the live model.

2. **Document the Project:**
   - Include documentation or a presentation outlining the MLOps pipeline architecture, including CI/CD workflow, model monitoring, and real-time inference.

---

### **Final Architecture:**
- **CodeCommit:** Stores all ML code and pipeline configuration files.
- **CodeBuild:** Handles data preprocessing, model training, and deployment using SageMaker.
- **CodePipeline:** Automates the end-to-end process, triggering the pipeline on new commits.
- **SageMaker:** Used for training, tuning, and deploying the ML models.
- **CloudWatch:** Monitors model performance and raises alarms if the model quality drops.

This complete MLOps pipeline demonstrates advanced machine learning operations with CI/CD, automation, and model monitoring—all key aspects for scalable ML development in a real-world scenario.

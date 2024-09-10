# ML-CI-CD-Pipeline
### **Project: MLOps Pipeline with SageMaker, CodePipeline, and CodeBuild**

- **CodeCommit:** Stores all ML code and pipeline configuration files.
- **CodeBuild:** Handles data preprocessing, model training, and deployment using SageMaker.
- **CodePipeline:** Automates the end-to-end process, triggering the pipeline on new commits.
- **SageMaker:** Used for training, tuning, and deploying the ML models.
- **CloudWatch:** Monitors model performance and raises alarms if the model quality drops.

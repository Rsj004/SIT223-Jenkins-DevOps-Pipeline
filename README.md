# SIT223 Jenkins CI/CD Pipeline

This repository contains the Jenkins pipeline created for the SIT223 HD task.

The pipeline uses the Redback Fit backend project and includes the following stages:

- Checkout
- Install Dependencies
- Build
- Run Tests
- Security Scan
- Code Quality
- Deploy
- Release
- Monitoring

Jenkins is used to run the pipeline. Python is used for the backend project, pytest is used for testing, Bandit is used for security scanning, and Flake8 is used for code quality checking.

The Jenkins pipeline configuration is available in the Jenkinsfile.

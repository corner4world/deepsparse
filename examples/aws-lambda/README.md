<!--
Copyright (c) 2021 - present / Neuralmagic, Inc. All Rights Reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

# 🐑 Deploy a DeepSparse Pipeline in AWS Lambda

AWS Lambda is an event-driven, serverless computing infrastructure for deploying applications at minimal cost. This directory provides a guided example for deploying a DeepSparse pipeline on AWS Lambda for the sentiment analysis task.

The scope of this application encompasses:
1. The construction of a local Docker image.
2. The creation of an ECR repo in AWS.
3. Pushing the local image to ECR.
4. The creation of a Lambda function via API Gateway in a Cloudformation stack. 

## Requirements
The following credentials, tools, and libraries are also required:
* The [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) version 2.X that is [configured](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html). Double check if the `region` that is configured in your AWS CLI matches the region passed in the SparseLambda class found in the `endpoint.py` file. Currently, the default region being used is `us-east-1`.
* Full permissions to select AWS resources: ECR, API Gateway, Cloudformation, and Lambda.
* The AWS Serverless Application Model [(AWS SAM)](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html), an open-source CLI framework used for building serverless applications on AWS.
* [Docker and the `docker` cli](https://docs.docker.com/get-docker/).
* The `boto3` python AWS SDK and the `click` library.

## Quick Start

```bash 
git clone https://github.com/neuralmagic/deepsparse.git
cd deepsparse/examples/aws-lambda
pip install -r requirements.txt
```
## Model Configuration

To use a different sparse model please edit the model zoo stub in the `Dockerfile`. To change pipeline configuration (i.e., change task, engine etc.), edit the pipeline object in the `app.py` file. Both files can be found in the `/lambda-deepsparse/app` directory.

## Create Endpoint

Run the following command to build your Lambda endpoint.

```bash
python endpoint.py create
```
## Call Endpoint

After the endpoint has been staged (~3 minute), AWS SAM will provide your API Gateway endpoint URL in CLI. You can start making requests by passing this URL into the LambdaClient object. Afterwards, you can run inference by passing in your text input:

```python
from client import LambdaClient

LC = LambdaClient("https://#########.execute-api.us-east-1.amazonaws.com/inference")
answer = LC.client({"sequences": "i like pizza"})

print(answer)
```

answer: `{'labels': ['positive'], 'scores': [0.9990884065628052]}`

On your first cold start, it will take a ~60 seconds to invoque your first inference, but afterwards, it should be in milliseconds.

## Delete Endpoint

If you want to delete your Lambda endpoint, run:

```bash
python endpoint.py destroy
```
## Pre-requisites:
The following are prerequisite steps for following along with this solution:
* [Git client](https://git-scm.com/downloads) installed on your local workstation
* [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) installed on your local workstation
* Access to two AWS accounts which we will call **test** and **prod**, within the same AWS Organization
* [Resource Access Manager (RAM) enabled](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html) in the AWS Organization management account

## Solution Architecture
![Solution architecture diagram](/images/solution-architecture.jpg)

## Deployment

Clone the GitHub repository to your local workstation.
```
git clone git@github.com:aws-samples/aws-appregistry-ram.git
```

Change your working directory folder to the repository you cloned.
```
cd aws-appregistry-ram
```

Using your favorite editor, edit the file named test-acct-params.json and update the ParameterValue for TestAccountID and ProdAccountID.  The other parameters should remain as is.

```
[
    {
        "ParameterKey":"apiGatewayStageName",
        "ParameterValue":"test"
    },
    {
        "ParameterKey":"TestAccountID",
        "ParameterValue":"<input_your_test_account_id>"
    },
    {
        "ParameterKey":"ProdAccountID",
        "ParameterValue":"<input_your_prod_account_id>"
    },
    {
        "ParameterKey": "IsTestAccountFlag",
        "ParameterValue": "Yes"
    }
]
```

Next, issue the AWS CLI command with credentials authenticated to your **test** AWS account to deploy the test web stack:
```
aws cloudformation create-stack \
--stack-name appreg-app-test \
--template-body file://template.yaml \
--capabilities CAPABILITY_IAM \
--parameters file://test-acct-params.json
```

The output should look like the following:
```
{
    "StackId": "arn:aws:cloudformation:us-east-1:111111111111:stack/appreg-app-test/76d7acd0-427b-11ed-aef7-0e59547d11e5"
}
```

Using your favorite editor, edit the file named prod-acct-params.json and update the ParameterValue for TestAccountID and ProdAccountID.  The other parameters should remain as is.

```
[
    {
        "ParameterKey":"apiGatewayStageName",
        "ParameterValue":"prod"
    },
    {
        "ParameterKey":"TestAccountID",
        "ParameterValue":"<input_your_test_account_id>"
    },
    {
        "ParameterKey": "ProdAccountID",
        "ParameterValue": "<input_your_prod_account_id>"
    },
    {
        "ParameterKey": "IsTestAccountFlag",
        "ParameterValue": "No"
    }
]
```

Next, issue the AWS CLI command below with credentials authenticated to your **prod** AWS account to deploy the prod web stack:
```
aws cloudformation create-stack \
--stack-name appreg-app-prod \
--template-body file://template.yaml \
--capabilities CAPABILITY_IAM \
--parameters file://prod-acct-params.json
```

The output should look like the following:
```
{
    "StackId": "arn:aws:cloudformation:us-east-1:111111111111:stack/appreg-app-prod/300671a0-427c-11ed-8f08-120ed9d998f7"
}
```

## Cleanup
Issue the AWS CLI command below with credentials authenticated to your prod AWS account to delete the prod version of the sample web app
```
aws cloudformation delete-stack --stack-name appreg-app-prod
```

Next issue the AWS CLI command below with credentials authenticated to your test AWS account to delete the test version of the sample web app
```
aws cloudformation delete-stack --stack-name appreg-app-test
```
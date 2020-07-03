# cfn_ci_cd
CloudFormation CI/CD pipeline 

install python env
> virtualenv -p /usr/local/bin/python3.7 .env 

run taskcat test
> taskcat test run 

run lint test
> cfn-lint templates/*

run CFN-Nag to pinpoint security problems
> cfn_nag_scan --input-path  templates/*

## validate template
aws --profile nurt1asandbox cloudformation validate-template \
    --template-body file://templates/create_ec2.yaml

## create stack
aws cloudformation create-stack \
    --stack-name example \
    --template-body file://create_ec2.yaml \
    --parameters file://params.json

## update stack
aws --profile nurAutomation cloudformation update-stack \
    --stack-name example \
    --template-body file://ec2_simple_example.yaml \
    --parameters file://params2.json
## describe stack
aws --profile nurAutomation cloudformation describe-stacks \
    --stack-name example \
    --query "Stacks[0].Outputs[0].OutputValue"

## delete stack
aws --profile nurAutomation cloudformation delete-stack \
    --stack-name example

## create change set aka dry run
aws --profile nurAutomation cloudformation create-change-set \
    --change-set-name changeset-1 \
    --stack-name example \
    --template-body file://ec2_simple_example.yaml \
    --parameters file://params2.json

## for not existing stack need to add --change-set-type CREATE
aws --profile nurAutomation cloudformation create-change-set \
    --change-set-name changeset-1 \
    --change-set-type CREATE \
    --stack-name example \
    --template-body file://ec2_simple_example.yaml \
    --change-set-name changeset-1

## describe changes that will be applied
aws --profile nurAutomation cloudformation describe-change-set \
    --change-set-name changeset-1 \
    --stack-name example \
    --query "Changes[].ResourceChange"

## describe changeset in a nicely way
aws --profile nurAutomation cloudformation describe-change-set \
    --change-set-name changeset-1 \
    --stack-name example \
    --query 'Changes[*].ResourceChange.{Action:Action,Resource:ResourceType,ResourceId:LogicalResourceId,ReplacementNeeded:Replacement}' \
    --output table

## prevent updates to Stack Resources
aws --profile nurAutomation cloudformation set-stack-policy \
    --stack-name example \
    --stack-policy-body file://stack-policy.json

## apply change set
aws --profile nurAutomation cloudformation execute-change-set \
    --change-set-name changeset-1 \
    --stack-name example


* [Testing](https://workshop.quickstart.awspartner.com/70_testing/1_local_test.html)    
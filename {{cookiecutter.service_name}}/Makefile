init:
	$(info Initalizing project)
	npm install

# Command to run everytime you make changes to verify everything works
dev: lint test

# Verifications to run before sending a pull request
pr: init dev

export ENV ?= ${USER}
export SERVICE ?= $(shell basename ${CURDIR})
export STACKNAME = $(SERVICE)-$(ENV)

AWS_ACCOUNT ?= $(shell aws sts get-caller-identity | jq -r .Account)
DEPLOY_BUCKET ?= $(shell basename ${CURDIR})-pkg-$(ENV)-$(AWS_ACCOUNT)
PACKAGED_TEMPLATE = .aws-sam/packaged-template.yaml
DIST_DIR = "./build"
SITE_BUCKET = $(shell aws cloudformation describe-stacks --stack-name $(STACKNAME) | jq -r '.Stacks[0].Outputs[] | select(.OutputKey == "StaticSiteS3BucketName") | .OutputValue')

CICD_STACKNAME = cicd-$(SERVICE)-$(ENV)
CICD_TEMPLATE = pipeline.yaml

.PHONY: build package clean

bucket:
	# Create bucket if it does not exist
	@aws s3api head-bucket --bucket ${DEPLOY_BUCKET} 2>/dev/null 1>/dev/null || aws s3 mb s3://${DEPLOY_BUCKET}

build:
	$(info Building application)
	@sam build

package: build bucket
	$(info Creating package)
	@sam package --s3-bucket $(DEPLOY_BUCKET) --output-template-file $(PACKAGED_TEMPLATE)

dist:
	$(info Creating distribution contents)
	@npm install
	@npm run build

deploy-stack:
	$(info Deploying to personal development stack)
	@sam deploy --parameter-overrides Environment=$(ENV) --template-file $(PACKAGED_TEMPLATE) --stack-name $(STACKNAME) --capabilities CAPABILITY_IAM --no-fail-on-empty-changeset

sync:
	$(info syncing S3 static content to S3 bucket)
	@aws s3 sync $(DIST_DIR) s3://$(SITE_BUCKET)

deploy: package deploy-stack dist sync

describe:
	$(info Describing stack)
	@aws cloudformation describe-stacks --stack-name $(STACKNAME) --output table --query 'Stacks[0]'

resources:
	$(info Describing stack resources)
	@aws cloudformation describe-stack-resources --stack-name $(STACKNAME) --output table --query 'StackResources[*]'

outputs:
	$(info Displaying stack outputs)
	@aws cloudformation describe-stacks --stack-name $(STACKNAME) --output table --query 'Stacks[0].Outputs'

parameters:
	$(info Displaying stack parameters)
	@aws cloudformation describe-stacks --stack-name $(STACKNAME) --output table --query 'Stacks[0].Parameters'

delete:
	$(info Delete stack)
	# NOTE: If SITE_BUCKET doesn't exist because of a stack failure then we
	# just need to move on
	@aws s3 rm --recursive s3://$(SITE_BUCKET) || true
	@aws cloudformation delete-stack --stack-name $(STACKNAME)

pipeline-deploy:
	$(info Deploying CI/CD stack)
	@aws cloudformation deploy --parameter-overrides ServiceName=$(SERVICE) --template-file $(CICD_TEMPLATE) --stack-name $(CICD_STACKNAME) --capabilities CAPABILITY_IAM

pipeline-trigger:
	$(info Triggering CI/CD pipeline)
	@aws codepipeline start-pipeline-execution --name $(STACKNAME)

pipeline-describe:
	$(info Describing CI/CD stack)
	@aws cloudformation describe-stacks --stack-name $(CICD_STACKNAME) --output table --query 'Stacks[0]'

pipeline-resources:
	$(info Describing CI/CD stack resources)
	@aws cloudformation describe-stack-resources --stack-name $(CICD_STACKNAME) --output table --query 'StackResources[*]'

pipeline-outputs:
	$(info Describing CI/CD stack outputs)
	@aws cloudformation describe-stacks --stack-name $(CICD_STACKNAME) --output table --query 'Stacks[0].Outputs'

pipeline-parameters:
	$(info Describing CI/CD stack parameters)
	@aws cloudformation describe-stacks --stack-name $(CICD_STACKNAME) --output table --query 'Stacks[0].Parameters'

pipeline-delete:
	$(info Deleting CI/CD pipeline)
	@aws cloudformation delete-stack --stack-name $(CICD_STACKNAME)

unit-test:
	npm run test

e2e-test:
	npm run test:e2e-headless

test: unit-test e2e-test
	# Run unit tests
	true

lint:
	# Lint code
	true

clean:
	rm -rf .aws-sam


init:
	$(info Initalizing project)
	npm install

# Command to run everytime you make changes to verify everything works
dev: lint test

# Verifications to run before sending a pull request
pr: init dev

ENV ?= ${USER}
AWS_ACCOUNT = $$(aws sts get-caller-identity | jq -r .Account)
STACKNAME = $$(basename ${CURDIR})-$(ENV)
DEPLOY_BUCKET = $$(basename ${CURDIR})-package-bucket-$(ENV)-$(AWS_ACCOUNT)
PACKAGED_TEMPLATE = .aws-sam/packaged-template.yaml
DIST_DIR = "./build"
SITE_BUCKET = $$(aws cloudformation describe-stacks --stack-name $(STACKNAME) | jq -r '.Stacks[0].Outputs[] | select(.OutputKey == "StaticSiteS3BucketName") | .OutputValue')

.PHONY: build package clean

check_profile:
	# Make sure we have a user-scoped credentials profile set. We don't want to be accidentally using the default profile
	@aws configure --profile ${AWS_PROFILE} list 1>/dev/null 2>/dev/null || (echo '\nMissing AWS Credentials Profile called '${AWS_PROFILE}'. Run `aws configure --profile ${AWS_PROFILE}` to create a profile called '${AWS_PROFILE}' with creds to your personal AWS Account'; exit 1)

bucket:
	# Create bucket if it does not exist
	@aws --profile ${AWS_PROFILE} s3api head-bucket --bucket ${DEPLOY_BUCKET} 2>/dev/null 1>/dev/null || aws --profile ${AWS_PROFILE} s3 mb s3://${DEPLOY_BUCKET}

build:
	$(info Building application)
	@sam build

package: build bucket
	$(info Creating package)
	@sam package --profile ${AWS_PROFILE} --s3-bucket $(DEPLOY_BUCKET) --output-template-file $(PACKAGED_TEMPLATE)

dist:
	$(info Creating distribution contents)
	@npm install
	@npm run build

deploy-stack:
	$(info Deploying to personal development stack)
	@sam deploy --parameter-overrides Environment=$(ENV) --profile ${AWS_PROFILE} --template-file $(PACKAGED_TEMPLATE) --stack-name $(STACKNAME) --capabilities CAPABILITY_IAM --no-fail-on-empty-changeset

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


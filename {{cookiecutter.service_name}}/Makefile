init:
	true

# Command to run everytime you make changes to verify everything works
dev: lint test

# Verifications to run before sending a pull request
pr: init dev

ENV = ${USER}
AWS_ACCOUNT = $$(aws sts get-caller-identity | jq -r .Account)
STACKNAME = $$(basename ${CURDIR})-$(ENV)
BUCKET = $$(basename ${CURDIR})-package-bucket-$(ENV)-$(AWS_ACCOUNT)
PACKAGED_TEMPLATE = .aws-sam/packaged-template.yaml
EVENT_SOURCE = {{cookiecutter.event_source}}

check_profile:
	# Make sure we have a user-scoped credentials profile set. We don't want to be accidentally using the default profile
	@aws configure --profile ${AWS_PROFILE} list 1>/dev/null 2>/dev/null || (echo '\nMissing AWS Credentials Profile called '${AWS_PROFILE}'. Run `aws configure --profile ${AWS_PROFILE}` to create a profile called '${AWS_PROFILE}' with creds to your personal AWS Account'; exit 1)


bucket:
	# Create bucket if it does not exist
	@aws --profile ${AWS_PROFILE} s3api head-bucket --bucket ${BUCKET} 2>/dev/null 1>/dev/null || aws --profile ${AWS_PROFILE} s3 mb s3://${BUCKET}


build:
	$(info Building application)
	sam build

package: build
	$(info Creating package)
	sam package --profile ${AWS_PROFILE} --s3-bucket $(BUCKET) --output-template-file $(PACKAGED_TEMPLATE)

deploy: bucket package
	$(info Deploying to personal development stack)
	sam deploy --parameter-overrides Environment=$(ENV) --profile ${AWS_PROFILE} --template-file $(PACKAGED_TEMPLATE) --stack-name $(STACKNAME) --capabilities CAPABILITY_IAM

unit-test:
	# run unit tests
	true

int-test:
	# run unit tests
	true

test: unit-test int-test
	# Run unit tests
	true

lint:
	# Lint code
	true



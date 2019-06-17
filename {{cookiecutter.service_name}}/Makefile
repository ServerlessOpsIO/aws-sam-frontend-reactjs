init:
	true

# Command to run everytime you make changes to verify everything works
dev: lint test

# Verifications to run before sending a pull request
pr: init dev

ENV = ${USER}
AWS_ACCOUNT = $$(aws sts get-caller-identity | jq -r .Account)
STACKNAME = $$(basename ${CURDIR})-$(ENV)
DEPLOY_BUCKET = $$(basename ${CURDIR})-package-bucket-$(ENV)-$(AWS_ACCOUNT)
PACKAGED_TEMPLATE = .aws-sam/packaged-template.yaml
DIST_DIR = "./dist"
SITE_BUCKET = $$(aws cloudformation describe-stacks --stack-name hello-world-fe-tom | jq -r '.Stacks[0].Outputs[] | select(.OutputKey == "StaticSiteS3BucketName") | .OutputValue')

check_profile:
	# Make sure we have a user-scoped credentials profile set. We don't want to be accidentally using the default profile
	@aws configure --profile ${AWS_PROFILE} list 1>/dev/null 2>/dev/null || (echo '\nMissing AWS Credentials Profile called '${AWS_PROFILE}'. Run `aws configure --profile ${AWS_PROFILE}` to create a profile called '${AWS_PROFILE}' with creds to your personal AWS Account'; exit 1)


bucket:
	# Create bucket if it does not exist
	@aws --profile ${AWS_PROFILE} s3api head-bucket --bucket ${DEPLOY_BUCKET} 2>/dev/null 1>/dev/null || aws --profile ${AWS_PROFILE} s3 mb s3://${DEPLOY_BUCKET}


build:
	$(info Building application)
	sam build

package: build
	$(info Creating package)
	sam package --profile ${AWS_PROFILE} --s3-bucket $(DEPLOY_BUCKET) --output-template-file $(PACKAGED_TEMPLATE)

dist:
	npm run build

deploy: bucket package
	$(info Deploying to personal development stack)
	@sam deploy --parameter-overrides Environment=$(ENV) --profile ${AWS_PROFILE} --template-file $(PACKAGED_TEMPLATE) --stack-name $(STACKNAME) --capabilities CAPABILITY_IAM

sync: dist
	$(info syncing S3 static content to S3 bucket)
	@aws s3 sync ./dist s3://$(SITE_BUCKET)

unit-test:
	npm run test

e2e-test:
	npm run cypress

test: unit-test e2e-test
	# Run unit tests
	true

lint:
	# Lint code
	true



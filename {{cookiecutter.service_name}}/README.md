# {{cookiecutter.service_name}}

{{cookiecutter.service_description}}


```bash
.
├── LICENSE
├── Makefile
├── README.md
├── package.json
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
├── src
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   ├── logo.svg
│   └── serviceWorker.js
└── template.yaml
```

## Requirements

* AWS CLI with Administrator permission
* [Python 3 installed](https://www.python.org/downloads/)
* [Docker installed](https://www.docker.com/community-edition)
* [SAM CLI installed](https://github.com/awslabs/aws-sam-cli)
* [jq](https://stedolan.github.io/jq/download/)


## AWS Credentials setup

Create a named profile for your AWS credentials. For example, create the two files below with the following contents. (Replace the access key ID and secret access key ID with your own.)

_~/.aws/config_
```ini
[profile my-profile]
region=us-east-1
```

_~/.aws/credentials_
```ini
[my-profile]
aws_access_key_id=<YOUR_ACCESS_KEY>
aws_secret_access_key=<YOUR_SECRET_ACCESS_KEY>

```

Next, run the following in your shell.

```bash
export AWS_PROFILE=my-profile
```

## Development and Testing
**TBD**

## Deployment and Management
### Deployment
Deploying this application is a two-step process. The first command below deploys the infrastructure for the service. The second command builds the ReactJS app and syncs files to S3

```bash
make deploy
make sync
```

#### Deploying to alternate environments
By default this stack sets the name of the deployment environment to your local computer's username. If you wish to change that, for example to deploy an alternate feature branch, you can preface youe `make` commands with `ENV=<NAME_OF_ENVIRONMENT>`. See below for example.

```bash
ENV=my-environment make deploy
ENV=my-environment make sync
```

_NOTE: This will not deploy your stack across account boundaries. If you are currently using your development environment account credentials and you name the environment `prod`, you will just create an environment in the development account named `prod`._
### Stack Info

You may wish to get information about the stack that you've deployed. Run the following command to output a table describing the stack's configuration.

```bash
make describe
```

### Stack cleanup
To delete the deployed stack run the following command.

```bash
make delete
```


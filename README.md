# AWS SAM ReactJS Frontend
AWS SAM [Cookiecutter](https://github.com/audreyr/cookiecutter) template for creating a ReactJS Frontend

This template can be used by SAM CLI to generate a new project skeleton. During the process of creating a new project you will be asked to select options for how your project should be configured. A description of these options is found in the **Options** section of this README. This template does its best to handle as much boilerplate work as possible and guide people towards using best practices.

## CloudFront v. Bare S3 Hosting
This template supports two types of website hosting. Both types of hosting serve content out of an AWS S3 bucket. The default in this template is to create a CloudFront distribution to front the S3 bucket. The CloudFront setup provides HTTPS without certificate validation errors as well as configurable site object caching. Initial deployment and updates to the distribution can be however be time consuming.

Only disable CloudFront for testing this template or low-trafficked / low-value sites.

## Usage
### 1. Install AWS SAM CLI
Installing and using the AWS SAM CLI requires having Python installed. Once installed use `pip` to install AWS SAM CLI.

```
$ pip install aws-sam-cli
```

More extensive instructions can be found here: [Installing the AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)


### 2. **Create a `~/.cookiecutter.rc` file**
Create a configuration file to fill in a few default values during project initialization

```
default_context:
    author_name: "Tom McLaughlin"
    author_email: "tom@serverlessops.io"
    author_github: "tmclaugh"

abbreviations:
    gh: https://github.com/{0}.git
    bb: https://bitbucket.org/{0}
```

### 3. Create a new project
Create a new project by running the command below. This will ask you a series of questions and customize the generated project based on your answers.

```
$ sam init --location gh:serverlessopsio/aws-sam-frontend-reactjs
```

## Options

Option | Description
------------------------------------------------- | ---------------------------------------------------------------------------------
`service_name`              |   Name of service
`service_description`       |   Description of service
`function_name`             |   Name of service function
`function_description`      |   Description of service function
`copyright_holder`          |   Copyright holder of software
`author_name`               |   Author name
`author_email`              |   Author email
`year`                      |   Year (for copyright)
`site_fqdn`                 |   Fully qualified domain name of site. _ex. app.example.com_
`site_domain`               |   Parent domain of website (This should be the name of a Route53 zone if it exists) _ex. example.com_
`use_dns`                   |   Whether or not to manage AWS Route53 DNS.
`use_cloudfront`            |   Whether or not to front the site using AWS CloudFront


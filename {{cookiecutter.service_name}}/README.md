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


## Packaging and deployment
To package and deploy this service run the command below.

```
$ make deploy
```

## Testing
**TBD**

Next, we
## Cleanup

In order to delete our Serverless Application recently deployed you can use the following AWS CLI Command:

```bash
aws cloudformation delete-stack --stack-name {{cookiecutter.service_name}}
```


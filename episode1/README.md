# Cloud Coffee Break Episode One

Welcome to Cloud Coffee Break Episode One!

This episode is available on [YouTube, here](https://youtu.be/W_6PaRDLGpw) .

Slides for this episode are [available here](https://drive.google.com/file/d/1gSVuqBZA6NvZfWO9XlCHSfZGbXWg6NFk/view?usp=sharing) .

Our free eBook, _What is Serverless?_, that I mention, is [available here](https://www.symphonia.io/resources/).

This README consists of further description of the prerequisites necessary for the "Serverless Hello World" app I talk about in the video, along with details of creating and deploying the Hello World app.

## Setting up Prerequisites for "Serverless Hello World"

In the video I mention three prerequisites for creating the "Serverless Hello World" app:

* Suitable credentials to an AWS account.
* Install AWS CLI, configure with your credentials
* Install AWS SAM CLI

Here I'll go through these in a little more detail.

### Suitable credentials to an AWS account

"Serverless Hello World" uses **Amazon Web Services** (AWS) . In order to deploy the app you therefore need access to an AWS Account.

#### Creating an AWS account

If you already have access to AWS you can skip this step, otherwise you'll need to create your own AWS account. Three points worth knowing before you do this:

* You'll need to provide a payment method (typically a credit card).
* Low throughput use, including what I cover in "Serverless Hello World", will be covered by the [AWS Free Tier](https://aws.amazon.com/free/free-tier/), but you'll be responsible for any charges beyond this tier.
* You'll need a "root user" email address that has not been used for any other AWS account. Since I use Google for email, I often use the same basic email address [but with a `+` sign in the username part](https://gmail.googleblog.com/2008/03/2-hidden-ways-to-get-more-from-your.html) to differentiate, which works fine for AWS signup.

The actual process for creating an account is to go to [aws.amazon.com](https://aws.amazon.com/), hit the "Create an account" button, and follow the guide. The official instructions from AWS are also available [here](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/) .

#### AWS credentials

Once you know which AWS account you're going to use you'll need to have credentials for accessing AWS from the command line. There are a variety of ways of doing this, however if you're a beginner I recommend the following:

1. Create an **IAM User** for your account
1. Assign it _AdministratorAccess_ privileges (assuming this is not a production account!)
1. Create programmatic access keys for the user

There's a guide to doing this provided by the folks at Serverless Stack [here](https://serverless-stack.com/chapters/create-an-iam-user.html) .

### Install AWS CLI, configure with your credentials

First make sure you have an _Access Key ID_ and a _Secret Access Key_ to go with it - e.g. from the final step above.

A lot of what I do in the series uses the [AWS Command Line Interface](https://aws.amazon.com/cli/) - a tool you can use from your terminal to access the vast array of AWS products. If you don't already have the AWS CLI installed (try running `aws` to see if you do) then you'll need to install it.

#### Install the AWS CLI

Installing the AWS CLI has got somewhat complicated over the years, especially since right now AWS have two supported major versions - AWS CLI Version 1 and, yep, you guessed it, AWS CLI Version 2. I recommend you use V2 if this is your first time using the AWS CLI.

If you're using a Mac by far the easiest way to install the AWS CLI is to use [Homebrew](https://brew.sh/), as follows:

```
% brew install awscli
```

If you're using something other than a Mac, or you don't use Homebrew, then you can install the AWS CLI either as a python _pip_ package (named _awscli_), or a system application. Further instructions are available on the [AWS website here](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html).

#### Configure the AWS CLI

Once the AWS CLI is installed, you should be able to run it from a terminal using the `aws` command.

The first thing you'll need to do is configure it with keys. Again, if this is your first time using AWS I recommend you run the `aws configure` command and follow the prompts, however there are many other more complicated ways of performing this task.

When running `aws configure` you'll also be asked to specify a _Default region name_ and _Default output format_ . Typically I start with `us-east-1` and `json`, but you have may want to pick a region nearer to your location - just make sure you use the same region when using the AWS Web Console.

The documentation for using `aws configure` is [available here](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html).

A good way to test your AWS configuration is to run a simple command. `aws s3 ls` is a good short one (which will successfully return nothing in a new account!), otherwise `aws sts get-caller-identity` will give you some more details about your connection.

### Install AWS SAM CLI

And finally, we can install the AWS SAM CLI - a tool which layers on top of the AWS CLI to provide various goodies.

SAM CLI uses Docker for some of its features, and this is mentioned in the docs, but for the Hello World app Docker is not unnecessary, and can be ignored.

Again, if you're on a Mac the easiest way to install SAM is via homebrew, as follows:

```
% brew tap aws/tap
% brew install aws-sam-cli
```

Otherwise the documentation [available here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html).

If SAM is successfully installed you should be able to see something like the following

```
% sam --version
SAM CLI, version 1.0.0
```

## Creating, deploying, calling, and tearing-down "Serverless Hello World"

First of all, create a new directory and switch to it, e.g.:

```
% mkdir hello-world
% cd hello-world
```

Next, in this directory, create a new file named `template.yaml`, the contents of which are provided [here](./template.yaml) .

Build and Deploy this app as follows:

```
% sam deploy --guided
```

The `--guided` form is useful for beginners since it will walk you through capturing some configuration, which it then saves to a local file. You can use the default answers (just hit enter) apart from the question _HelloWorldFunction may not have authorization defined, Is this okay?_ - to which you must explicitly hit `y`, and enter. For subsequent deployments of the same app, just run `sam deploy` .

If SAM ran successfully then your application is now deployed! To call it, you need to know the URL. You can get that by visiting the API Gateway section of the AWS Web Console at https://console.aws.amazon.com/apigateway - if you don't see your API make sure that the region selector (top right of the page next to "support" is set to the same region as your AWS CLI configuration.) Click on your API and you should be able to see "Invoke URL". If you click on that you should be able to see "Hello World!" output to your browser. Congratulations!

Since this application is available to the public internet you probably want to tear it down when you're done with it. To do so, run the following, changing the stack-name if you used something else in the deployment step:

```
% aws cloudformation delete-stack --stack-name sam-app
```


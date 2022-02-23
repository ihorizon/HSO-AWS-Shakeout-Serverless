<!--
Title: 'HSO serverless demo'
Description: 'This demo shows how to setup a HTTP GET endpoint and serverless function using the serverless framework. It also demonstrates the approach to retrieving secrets from Hashicorp Vault.'
Serverless framework: v2
Cloud platform: AWS
Lambda language: Python
Author: 'Andrew Markwell'
-->

# HSO Serverless Demo

This demo shows how to setup a simple HTTP GET endpoint and serverless function using the serverless framework. It also demonstrates the approach to retrieve secrets from Hashicorp Vault. When the API endpoint is invoked it passes an event to the Lambda function which then connects to Hashicorp Vault and retrieves the relevant secrets.

The function will return a JSON object that contains a message, a copy of the triggering event (from the API), the Lambda environmental parameters and the secretes that were retrieved from Hashicorp Vault.

This demo uses the serverless framework to package and deploy all code. It also uses serverless dashboard CI/CD module to manage deployments following code commits into GitHub.

## Use Cases

- Connecting into Hashicorp Vault to retrieve enterprise/global secrets
- Using serverless framework to build, package and deploy serverless functions locally for testing
- Using serverless framework to build, package and deploy serverless functions and related Cloud infrastructure to Cloud platforms (such as AWS)


## Prerequisites

1. External provider accounts
- Serverless.com 
- HashiCorp Vault
- GitHub

2. Pre-requisites:
- A code editor, like 'VS Code', installed on your local machine
- Python v3.8 installed on your local machine
- Pip3 installed on your local machine for managing python packages
- Python virtual environment (virtualenv) installed on your local machine
- [optional] Docker installed on your local machine (if you want to test against Hashicorp Vault locally)
- NodeJS installed on your local machine for serverless framework
- NPM installed on your local machine for managing JavaScript packages
- Hashicorp Vault CLI installed

## Installing Dependencies

Note: below assumes you have retained the same project name  "hso-serverless-1"

1. Clone the repo "" - using VS Code, or you favourite code editor

2. Install serverless framework
```bash
[host] hso-serverless-1 % npm install -g serverless
```
3. Create a python virtual environment (used for testing & packaging dependencies):
```bash
[host] hso-serverless-1 % virtualenv venv --python=python3 
```

4. Install python packages - the virtual environment needs to be activated first:
```bash
[host] hso-serverless-1 % source venv/bin/activate
(venv) [host] hso-serverless-1 % pip3 install -r requirements.txt
(venv) [host] hso-serverless-1 % deactivate
```

5. Install node packages - within a scoped terminal window:
```bash
[host] hso-serverless-1 % npm install
```

6. [optional] Install and run Hashicorp Vault docker container (uses a test token)
```bash
[host] hso-serverless-1 % docker run --cap-add=IPC_LOCK -e 'VAULT_DEV_ROOT_TOKEN_ID=4bea777b-9752-4c57-972d-02d091715254' -e 'VAULT_DEV_LISTEN_ADDRESS=0.0.0.0:8200' -p 8200:8200 vault

```

To use Hashicorp Vault docker locally (after the config below is done, Vault can be accessed via a web browers http://127.0.0.1:8200):
```bash
% docker ps #get a lit of running containers so you can get the containter id
% docker exec -it [container-id] /bin/sh #log into the container
% export VAULT_ADDR='http://0.0.0.0:8200' #use http - not https - localhost is mapped on port 8200
% export VAULT_TOKEN='s.1EunzFHKi8jkJmIohI9NtSJb.X5Fgr' #dummy token
% vault kv put secret/example-app SECRET_1="foo" SECRET_2="bar" SECRET_3="foobar" # create some secrets
% vault kv get secret/example-app #check the secrets have been created
```


## Deploying locally (accessing docker Vault container)

1. Get serverless to build, package and deploy the code locally:

Setup the configuration:
- open 'properties.yml'
- ensure the parameter for local is set to true: 'LOCAL: true'

```bash
[host] hso-serverless-1 % source venv/bin/activate
(venv) [host] hso-serverless-1 % serverless offline
```

2. The expected results:

```bash
Serverless: Running "serverless" installed locally (in service node_modules)
Serverless: Using provider credentials, configured via dashboard: https://app.serverless.com/ihorizon/apps/aws-python-serverless/serverless-hso-demo/dev/ap-southeast-2/providers
offline: Starting Offline: dev ap-southeast-2.
offline: Offline [http for lambda] listening on http://localhost:3002
offline: Function names exposed for local invocation by aws-sdk:
           * hsoServerlessDemo: serverless-hso-demo-dev-hsoServerlessDemo

   ┌─────────────────────────────────────────────────────────────────────────────────────┐
   │                                                                                     │
   │   GET | http://localhost:3000/secrets                                               │
   │   POST | http://localhost:3000/2015-03-31/functions/hsoServerlessDemo/invocations   │
   │                                                                                     │
   └─────────────────────────────────────────────────────────────────────────────────────┘

offline: [HTTP] server ready: http://localhost:3000 🚀
offline: 
offline: Enter "rp" to replay the last request
```


2. Running the demo:

The demo can be accessed locally via a web browers http://127.0.0.1:3000)

Expected JSON object return is shown below. This contains the example event from an API call, the lambda serverless environment and the vault data towards the end of the response object:

```bash
{
    "message": "Hello, welcome to the serverless demo - showing how to retrieve secrets from Hashicorp Vault - the time of execution was 12:09:13.564873",
    "event": {
        "version": "2.0",
        "routeKey": "GET /secrets",
        "rawPath": "/secrets",
        "rawQueryString": "",
        "cookies": [],
        "headers": {
            "Host": "localhost:3000",
            "Connection": "keep-alive",
            "Cache-Control": "max-age=0",
            "sec-ch-ua": "\"Chromium\";v=\"94\", \"Google Chrome\";v=\"94\", \";Not A Brand\";v=\"99\"",
            "sec-ch-ua-mobile": "?0",
            "sec-ch-ua-platform": "\"macOS\"",
            "Upgrade-Insecure-Requests": "1",
            "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.71 Safari/537.36",
            "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
            "Sec-Fetch-Site": "none",
            "Sec-Fetch-Mode": "navigate",
            "Sec-Fetch-User": "?1",
            "Sec-Fetch-Dest": "document",
            "Accept-Encoding": "gzip, deflate, br",
            "Accept-Language": "en-US,en;q=0.9"
        },
        "queryStringParameters": null,
        "requestContext": {
            "accountId": "offlineContext_accountId",
            "apiId": "offlineContext_apiId",
            "authorizer": {
                "jwt": {}
            },
            "domainName": "offlineContext_domainName",
            "domainPrefix": "offlineContext_domainPrefix",
            "http": {
                "method": "GET",
                "path": "/secrets",
                "protocol": "HTTP/1.1",
                "sourceIp": "127.0.0.1",
                "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.71 Safari/537.36"
            },
            "requestId": "offlineContext_resourceId",
            "routeKey": "GET /secrets",
            "stage": "$default",
            "time": "24/Jan/2022:12:09:13 +1100",
            "timeEpoch": 1642986553359
        },
        "body": null,
        "pathParameters": null,
        "isBase64Encoded": false,
        "tracingID": "1642986553359.49"
    },
    "environmentData": {
        "AWS_LAMBDA_FUNCTION_VERSION": "$LATEST",
        "TERM_PROGRAM": "vscode",
        "TERM": "xterm-256color",
        "SHELL": "/bin/zsh",
        "SAAS_VAULT_TOKEN": "s.3WyqD2EcTqQKn5KqihcBKn2z.eUjVS",
        "TMPDIR": "/var/folders/8s/v7n6655n13x01f42rm2c1ts40000gn/T/",
        "TERM_PROGRAM_VERSION": "1.45.1",
        "SAAS_MOUNT_POINT": "demo",
        "USER": "andrewmarkwell",
        "LAMBDA_TASK_ROOT": "/var/task",
        "AWS_LAMBDA_LOG_GROUP_NAME": "/aws/lambda/serverless-hso-demo-dev-hsoServerlessDemo",
        "AWS_LAMBDA_LOG_STREAM_NAME": "2016/12/02/[$LATEST]f77ff5e4026c45bda9a9ebcec6bc9cad",
        "COMMAND_MODE": "unix2003",
        "NAMESPACE": "admin",
        "SSH_AUTH_SOCK": "/private/tmp/com.apple.launchd.DtJUfkljlz/Listeners",
        "__CF_USER_TEXT_ENCODING": "0x1F5:0:15",
        "LOCAL": "true",
        "VIRTUAL_ENV": "/Users/andrewmarkwell/Documents/development/hso-serverless-1/venv",
        "AWS_LAMBDA_FUNCTION_NAME": "serverless-hso-demo-dev-hsoServerlessDemo",
        "PATH": "/Users/andrewmarkwell/Documents/development/hso-serverless-1/venv/bin:/Users/andrewmarkwell/Documents/development/hso-serverless-1/venv/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Library/Apple/usr/bin",
        "_": "/Users/andrewmarkwell/Documents/development/hso-serverless-1/venv/bin/python3",
        "AWS_DEFAULT_REGION": "ap-southeast-2",
        "__CFBundleIdentifier": "com.microsoft.VSCode",
        "PWD": "/Users/andrewmarkwell/Documents/development/hso-serverless-1",
        "SAAS_SECRET_PATH": "example-app",
        "LAMBDA_RUNTIME_DIR": "/var/runtime",
        "LANG": "en_US.UTF-8",
        "SECRET_PATH": "example-app",
        "VAULT_URL": "http://127.0.0.1:8200",
        "NODE_PATH": "/var/runtime:/var/task:/var/runtime/node_modules",
        "AWS_REGION": "ap-southeast-2",
        "XPC_FLAGS": "0x0",
        "IS_OFFLINE": "true",
        "XPC_SERVICE_NAME": "0",
        "SHLVL": "2",
        "HOME": "/Users/andrewmarkwell",
        "VSCODE_GIT_ASKPASS_MAIN": "/private/var/folders/8s/v7n6655n13x01f42rm2c1ts40000gn/T/AppTranslocation/6BB6505A-75FF-49F8-B250-8705224D5275/d/Visual Studio Code.app/Contents/Resources/app/extensions/git/dist/askpass-main.js",
        "VAULT_TOKEN": "4bea777b-9752-4c57-972d-02d091715254",
        "LOGNAME": "andrewmarkwell",
        "VSCODE_GIT_IPC_HANDLE": "/var/folders/8s/v7n6655n13x01f42rm2c1ts40000gn/T/vscode-git-8740316c58.sock",
        "_HANDLER": "main.main",
        "VSCODE_GIT_ASKPASS_NODE": "/private/var/folders/8s/v7n6655n13x01f42rm2c1ts40000gn/T/AppTranslocation/6BB6505A-75FF-49F8-B250-8705224D5275/d/Visual Studio Code.app/Contents/Frameworks/Code Helper (Renderer).app/Contents/MacOS/Code Helper (Renderer)",
        "GIT_ASKPASS": "/private/var/folders/8s/v7n6655n13x01f42rm2c1ts40000gn/T/AppTranslocation/6BB6505A-75FF-49F8-B250-8705224D5275/d/Visual Studio Code.app/Contents/Resources/app/extensions/git/dist/askpass.sh",
        "SAAS_VAULT_URL": "https://vault-cluster.vault.48fd2963-ad87-4a63-8235-d6218e81e138.aws.hashicorp.cloud:8200",
        "AWS_LAMBDA_FUNCTION_MEMORY_SIZE": "1024",
        "SQLITE_EXEMPT_PATH_FROM_VNODE_GUARDS": "/Users/andrewmarkwell/Library/WebKit/Databases",
        "COLORTERM": "truecolor"
    },
    "secretData": {
        "statusCode": 200,
        "message": "Successfully retrieved secrets from Hashicorp Vault",
        "body": {
            "request_id": "56451a37-f766-d664-086b-9ab2ee1aa1a1",
            "lease_id": "",
            "renewable": false,
            "lease_duration": 0,
            "data": {
                "data": {
                    "SECRET_1": "foo",
                    "SECRET_2": "bar",
                    "SECRET_3": "foobar"
                },
                "metadata": {
                    "created_time": "2022-01-24T00:52:46.440109Z",
                    "custom_metadata": null,
                    "deletion_time": "",
                    "destroyed": false,
                    "version": 1
                }
            },
            "wrap_info": null,
            "warnings": null,
            "auth": null
        }
    }
}

```

## Properties file
Below is a summary of the properties within the properties.yml file

1. SECRET_PATH | path to the secret store for the local docker vault container (example-app)
2. VAULT_TOKEN | the access token for the docker vault container (4bea777b-9752-4c57-972d-02d091715254)
3. VAULT_URL | the local host URL for the docker vault container (http://127.0.0.1:8200)

4. SAAS_SECRET_PATH | path to the secret store within SaaS Vault (example-app)
5. SAAS_VAULT_TOKEN  | token to access the secret store within SaaS Vault
6. SAAS_PRIVATE_VAULT_URL | SaaS Vault private URL to cluster (https://vault-cluster.private.vault.[clusterid].aws.hashicorp.cloud:8200)
7. SAAS_VAULT_URL | SaaS Vault public URL to cluster (https://vault-cluster.vault.[clusterid].aws.hashicorp.cloud:8200)
8. SAAS_MOUNT_POINT | SaaS Vault mount point - this is version 1 of the kv secret engine (demo)
9. SAAS_V2_MOUNT_POINT  | SaaS Vault mount point - this is version 2 of the kv secret engine (demo) (demo-v2)
10. NAMESPACE | namsespace - used for managing different tenancies (admin)

11. SECRET_NAMED_KEYS | keys for the secrets that exist with the secret store (secret_1)

12. LOCAL | used to set whether the function connects to the local docker vault container or to SaaS Vault (true | false)
13. PRIVATE | used to set whether the function should use the public of private SaaS Vault url (true | false)
14. V2 | used to set whether the function should use version 2 of the kv secrets engine (true | false)

Below are example configured items:

```bash
SECRET_PATH: example-app
VAULT_TOKEN: 4bea777b-9752-4c57-972d-02d091715254
VAULT_URL: http://127.0.0.1:8200

SAAS_SECRET_PATH: example-app
SAAS_VAULT_TOKEN: 
SAAS_PRIVATE_VAULT_URL: https://vault-cluster.private.vault.[clusterid].aws.hashicorp.cloud:8200
SAAS_VAULT_URL: https://vault-cluster.vault.[clusterid].aws.hashicorp.cloud:8200
SAAS_MOUNT_POINT: demo
SAAS_V2_MOUNT_POINT: demo-v2
NAMESPACE: admin

SECRET_NAMED_KEYS:
 - SECRET_1
 - SECRET_2
 - SECRET_3

LOCAL: true
PRIVATE: false
V2: true
```

## Deploying locally (accessing SaaS Vault)

Configuring SaaS Vault
(using code - https://learn.hashicorp.com/tutorials/vault/codify-mgmt-oss)

- The following assumes a SaaS Vault cluster is running and the console is accessible

Creating a new secret store and secrets within the console:
1. Select Secrets from the header menu
2. Press the 'Enable a new engine'
3. Select kv
4. Expand 'method options' and select the engine version (1 or 2)
5. Set the path name of the engine (e.g demo) (e.g demo-v2) - you can inclue the engine number in the path name
6. Press 'enable engine'
7. Press the 'Create a secret' button
8. Set the path of the secret
9. Add in the secret data
10. Save the secret


1. Avoid using the admin token, therefore you can enable the approle and AWS (will require AWS roles and policies) auth methods. 
2. Create an ACL policy for the secrets path
3. Create one or more entities and add the policy
4. Add an alias against the entity to bind the auth method


## Deploying to AWS (TODO)

1. To deploy to a Cloud platform (AWS) - (the deployment works but the lambda function will not be able call the SaaS Vault end points)

```bash
serverless deploy
```

2. [Optional] Add the serverless app to the serverless dashboard

3. Link serverless app to GitHub and setup pipeline deployment rules


## TODOs
1. Implement peering between Vault and AWS
2. Implement AWS auth in Vault
3. Implement the Vault/Lambda proxy extension 'vault-lambda-extension' - https://github.com/hashicorp/vault-lambda-extension


## Version

version 0.0.1

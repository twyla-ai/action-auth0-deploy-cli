# GitHub Action for Auth0 Deploy CLI
This action for [auth0-deploy-cli](https://github.com/auth0/auth0-deploy-cli) enables 
arbitrary actions with the `a0deploy` command, including import and export of tenant 
configuration.

Refer to the [Auth0 Extensions Documentation](https://auth0.com/docs/extensions/deploy-cli)
for more information on the extension, installation and configuration.

The underlying container used is maintained at 
[auth0-deploy-cli-container](https://github.com/abn/auth0-deploy-cli-container). 

### Parameters
| Argument    | Description                                                                 |
|-------------|-----------------------------------------------------------------------------|
| domain      | Full domain for the tenant you are configuring. (eg: `tenant.eu.auth0.com`) |
| id          | Auth0 Client ID                                                             |
| secret      | Auth0 Client Secret                                                         |
| cli-version | Auth0 Deploy CLI Version (defaults to `latest`)                             |

### Usage
An example configuration deploying tenant configuration from the project where this 
workflow is running. The tenant input file is expected to be at `./tenant.yml`.

```yaml
name: Auth0 Deploy Tenant
on: [push]

jobs:
  import:
    name: Import Tenant Configuration
    uses: twyla-ai/action-auth0-deploy-cli@master
    with:
      domain: "<tenant>.auth0.com"
      id: ${{ secrets.AUTH0_CLIENT_ID }}
      secret: ${{ secrets.AUTH0_CLIENT_SECRET }}
      args: "import -i tenant.yml"
    env:
      AUTH0_ALLOW_DELETE: "true"
```

#### Example With Keyword Replace Mappings
Consider a scenario where the following config file is normally used.

```json
{
  "AUTH0_DOMAIN": "<tenant>.auth0.com",
  "AUTH0_CLIENT_ID": "<auth0-client-id>",
  "AUTH0_CLIENT_SECRET": "<auth-client-secret>",
  "AUTH0_KEYWORD_REPLACE_MAPPINGS": {
    "CONNECTION_GOOGLE_CLIENT_ID": "<value>",
    "CONNECTION_GOOGLE_CLIENT_SECRET": "<value>",
    "CALLBACK_URL": "http://localhost:8080"
  }
}
```

This can be configured via the action as follows. Note that additional config file can 
also be specified by appending `-c ./path/to/config.json` to args.

```yaml
name: Import Tenant Configuration
uses: twyla-ai/action-auth0-deploy-cli@master
with:
  domain: "<tenant>.auth0.com"
  id: ${{ secrets.AUTH0_CLIENT_ID }}
  secret: ${{ secrets.AUTH0_CLIENT_SECRET }}
  args: "import"
env:
  AUTH0_INPUT_FILE: ./tenant.yaml
  CONNECTION_GOOGLE_CLIENT_ID: "<value>"
  CONNECTION_GOOGLE_CLIENT_SECRET: "<value>"
  CALLBACK_URL: http://localhost:8080
```

### Environment Variables
Any environment variable respected by the underlying `a0deploy` command can be passed in 
via `env`.

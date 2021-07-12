# Azure DevOps CI demo through REST API

This is a basic demonstration of what is needed to run a Azure DevOps CI build by calling their REST API.

We are using Figmagic for this demo, to demonstrate running it in CI. Even if you don't care for that specific tool, you'll get the gist how to automate stuff in CI here.

## Instructions

I am assuming that you will set up a repo in Azure DevOps with the provided pipeline. If you have not, do so as the first step.

Set variables for the pipeline as per:

- `FigmaToken` as your Figma API token
- `FigmaUrl` to point to your Figma file ID

Set both to be secret values.

**NOTE**: It seems only template parameters in the Azure API work, and not the variables. See [https://stackoverflow.com/questions/60852825/azure-devops-yaml-pipeline-parameters-not-working-from-rest-api-trigger?rq=1](https://stackoverflow.com/questions/60852825/azure-devops-yaml-pipeline-parameters-not-working-from-rest-api-trigger?rq=1) for more.

### Authentication

It seems the best way to authenticate is through Personal Access Tokens. Create one at your DevOps org site, i.e. [https://dev.azure.com/YOUR_USER/\_usersSettings/tokens](https://dev.azure.com/YOUR_USER/_usersSettings/tokens). A suitable access scope is `Build > Read & execute`.

You will need the "pipeline ID" (or definition number) before calling the API. The easiest way is to navigate to your pipeline in the Azure Devops web console and check the URL bar which should look like: `https://dev.azure.com/YOUR_USER/YOUR_PROJECT/_build?definitionId=16`.

_Solution from [https://github.com/MicrosoftDocs/azure-devops-docs/issues/3263](https://github.com/MicrosoftDocs/azure-devops-docs/issues/3263)._

## Example call to API

Use basic authentication with any username and your password as the Personal Access Token value you created previously.

Set the following header:

- `Content-Type`: `application/json`

Then send a payload:

```json
POST https://dev.azure.com/YOUR_USER/YOUR_PROJECT/_apis/pipelines/DEFINITION_NUMBER/runs?api-version=6.0-preview.1

{
	"resources": {
		"repositories": {
			"self": {
				"refName": "refs/heads/main"
			}
		}
	},
	"templateParameters": {
		"FIGMA_VERSION": "something-here-alright",
		"FIGMA_MESSAGE": "This is what happened"
	}
}
```

You can use `refName` to point to any relevant branch.

## References

- [Azure Pipelines: Define variables](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#set-variables-in-pipeline)
- [Azure DevOps: Use personal access tokens](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page)
- [Azure DevOps: Runs - Run Pipeline](https://docs.microsoft.com/en-us/rest/api/azure/devops/pipelines/runs/run%20pipeline?view=azure-devops-rest-6.0)

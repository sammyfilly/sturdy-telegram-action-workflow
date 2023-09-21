most of the [Azure actions](https://github.com/Azure/actions) would need AZURE_CREDENTIALS to be set as a secret.
For more information, see ["Workflow syntax for GitHub Actions."](https://help.github.com/en/articles/workflow-syntax-for-github-actions/#jobsjob_idstepsenv)

```yaml  
steps:
  - name: Sample Azure action
    with: # Set Azure credentials secret as an input
      credentials: ${{ secrets.AZURE_CREDENTIALS }}
    env: # Or as an environment variable
      credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
   
## Set secret with Azure Credentials

Most of the Azure services use user-level Azure credentials i.e., Azure Service Principal for deployments. 

Follow the steps to create the Azure credentials (Service Principal) :
    * Run the below [az cli](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) command 
```bash  

   az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
  # Replace {subscription-id}, {resource-group} with the subscription, resource group details

  # The command should output a JSON object similar to this:

  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
  
```
  * Store the above JSON as the value of a GitHub secret with a name, for example 'AZURE_CREDENTIALS'
  * Now in the workflow file in your branch: `.github/workflows/workflow.yml` replace the secret in Azure login action with your secret name

## Set secret with Web App Publish_Profile

**Note: As of October 2020, Linux web apps will need the app setting `WEBSITE_WEBDEPLOY_USE_SCM` set to `true` before downloading the publish profile from the Azure portal. This requirement will be removed in the future.**
1. In the Azure portal, Navigate to your web app
1. In the Overview page of the app, click on "Get publish profile". A publish profile is a kind of deployment credential, useful when you don't own the Azure subscription. 
1. Open the downloaded settings file in VS Code and copy the contents of the file.
1. Create a new secret in your GitHub repo using the copied contents of the publish profile.

   ![](images/get-publish-profile.png)

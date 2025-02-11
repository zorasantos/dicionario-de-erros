
# The size of the function content was too large. The limit for this Static Web App is 262144000 bytes

Erro ao tentar fazer deploy de um app next na azure.

```bash
    Copying files to destination directory '/a7fb8ed7-67ef-4d14-90ec-700c0fc565e7-swa-oryx/app'...
    Done in 11 sec(s).

    Removing existing manifest file
    Creating a manifest file...
    Manifest file created.
    Copying .ostype to manifest output directory.

    Done in 157 sec(s).


    ---End of Oryx build logs---
    Try to validate location at: '/a7fb8ed7-67ef-4d14-90ec-700c0fc565e7-swa-oryx/app/.next'.
    Oryx has determined that the app build artifacts are in the '.next' directory. If this is incorrect please specify the app artifact location.
    Finished building app with Oryx
    Please note: For Next.js applications if skip_api_build=true, Static Web Apps will not remove dev dependencies or add the sharp package by default. If you want these optimizations, kindly add them to your custom build steps before passing skip_app_build=true
    Repackaged Next.js app to deploy on the backend.
    Skipping step to build /a7fb8ed7-67ef-4d14-90ec-700c0fc565e7-swa-oryx/app with Oryx
    Web App Runtime Information. OS: linux, node version: 18
    Zipping Api Artifacts
    Done Zipping Api Artifacts

    "The content server has rejected the request with: BadRequest
    Reason: The size of the function content was too large. The limit for this Static Web App is 262144000 bytes."

    For further information, please visit the Azure Static Web Apps documentation at https://docs.microsoft.com/en-us/azure/static-web-apps/
    If you believe this behavior is unexpected, please raise a GitHub issue at https://github.com/azure/static-web-apps/issues/
    Exiting

    ##[error]Error: The process '/usr/bin/bash' failed with exit code 1
    Finishing: AzureStaticWebApp
```

## Causa do Erro

A aplicação web ficou maior que a capacidade de 256mb do static web app da azure

## Solução

Modificar o script de deploy(ci/cd).

Antes:

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_GREEN_BEACH_0A9C51000 }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match your app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "" # Api source code path - optional
          output_location: "" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
```

Depois:

```yaml
  jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_GREEN_BEACH_0A9C51000 }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match your app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "" # Api source code path - optional
          output_location: "" # Built app content directory - optional
          app_build_command: 'npm run build'
          api_build_command: 'rm -rf ./node_modules/@next/swc-* && rm -rf ./.next/cache'
          ###### End of Repository/Build Configurations ######
```

Link para mais detalhes: https://github.com/Azure/static-web-apps/issues/1034#issuecomment-1399256154




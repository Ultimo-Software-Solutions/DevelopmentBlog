# Setup environment

Install bicep through Azure CLI.
```
> az bicep install
> az bicep upgrade
```

Add the bicep install directory to your PATH. It is installed in 'C:\\Users\\[user]\\.azure\\bin'.
Optionally install the bicep VS Code extensions.

# Export template

Export ARM template
```
> az group export --name [resourceGroupName] > [resourceGroupName].json
```

Convert ARM template to bicep
```
> az bicep decompile --file .\[resourceGroupName].json
```

# Deploy bicep template

Dry-run template, subscription scope
```
> az deployment sub what-if --location 'west europe' --template-file main.bicep --parameters main.parameters.json
```

Deploy template, subscription scope
```
> az deployment sub create --location 'west europe' --template-file main.bicep --parameters main.parameters.json
```

Dry-run template, resource group scope
```
> az deployment group what-if --resource-group [resourceGroupName] --template-file main.bicep --parameters main.parameters.json
```

Deploy template, resource group scope
```
> az deployment sub create --resource-group [resourceGroupName] --template-file main.bicep --parameters main.parameters.json
```

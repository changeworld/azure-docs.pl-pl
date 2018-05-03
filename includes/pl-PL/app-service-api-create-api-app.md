
Polecenie [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) umożliwia utworzenie aplikacji w planie usługi App Service `myAppServicePlan`. 

Aplikacja sieci Web udostępnia miejsce na hosting Twojego interfejsu API i zawiera adres URL do wyświetlania wdrożonej aplikacji.

W poniższym poleceniu zastąp ciąg *\<nazwa_aplikacji>* unikatową nazwą. Jeśli nazwa `<app_name>` nie jest unikatowa, zostanie wyświetlony komunikat o błędzie „Witryna internetowa o nazwie <nazwa_aplikacji> już istnieje”. Domyślnym adresem URL aplikacji sieci Web jest `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```
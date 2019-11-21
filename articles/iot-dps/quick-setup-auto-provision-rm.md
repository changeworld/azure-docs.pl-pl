---
title: Set up Azure IoT Hub Device Provisioning using Azure Resource Manager template
description: Azure quickstart - Set up the Azure IoT Hub Device Provisioning Service using a template
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 9a763a492496cef0f01468dc4b655a61e91a2a53
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229467"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with an Azure Resource Manager template

Przy użyciu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) można skonfigurować programowo zasoby w chmurze platformy Azure niezbędne do aprowizacji urządzeń. These steps show how to create an IoT hub and a new IoT Hub Device Provisioning Service, and link the two services together using an Azure Resource Manager template. This quickstart uses [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) to perform the programmatic steps necessary to create a resource group and deploy the template, but you can easily use the [Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby, or other programming languages to perform these steps and deploy your template. 


## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- This quickstart requires that you run the Azure CLI locally. Musi być zainstalowany wiersz polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja interfejsu wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Logowanie się do platformy Azure i tworzenie grupy zasobów

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. At the command prompt, run the [login command][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się do platformy Azure zapewnia dostęp do wszystkich kont platformy Azure skojarzonych z poświadczeniami użytkownika. Use the following [command to list the Azure accounts][lnk-az-account-command] available for you to use:
    
    ```azurecli
    az account list 
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, która ma zostać użyta do uruchamiania poleceń w celu utworzenia centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Podczas tworzenia zasobów w chmurze platformy Azure, takich jak centra IoT i usługi aprowizacji, można tworzyć je w grupie zasobów. Either use an existing resource group, or run the following [command to create a resource group][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > W poprzednim przykładzie tworzona jest grupa zasobów w lokalizacji Zachodnie stany USA. Listę dostępnych lokalizacji można wyświetlić, uruchamiając polecenie `az account list-locations -o table`.
    >
    >

## <a name="create-a-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

Użyj szablonu JSON, aby utworzyć usługę inicjowania i połączone centrum IoT w grupie zasobów. Można również użyć szablonu usługi Azure Resource Manager, aby wprowadzić zmiany w istniejącej usłudze aprowizacji lub centrum IoT.

1. Użyj edytora tekstu, aby utworzyć szablon usługi Azure Resource Manager o nazwie **template.json** z następującym szkieletem zawartości. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Zastąp sekcję **parameters** następującą zawartością. W sekcji parameters są określone parametry, które mogą być przekazywane z innego pliku. W tej sekcji jest określona nazwa centrum IoT i usługi aprowizacji, które mają zostać utworzone. W tej sekcji jest również określona lokalizacja centrum IoT i usługi aprowizacji. Te wartości są ograniczone do regionów platformy Azure, które obsługują centra IoT i usługi aprowizacji. Aby uzyskać listę obsługiwanych lokalizacji usługi Device Provisioning, można uruchomić następujące polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub odwiedzić stronę [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukać frazę „usługa Device Provisioning”.

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Zastąp sekcję **variables** następującą zawartością. Ta sekcja definiuje wartości, które są później używane do tworzenia parametrów połączenia z centrum IoT, które jest wymagane do połączenia usługi aprowizacji i centrum IoT. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. Aby utworzyć centrum IoT, dodaj następujące wiersze do kolekcji **resources**. The JSON specifies the minimum properties required to create an IoT hub. Właściwości **name** i **location** są przekazywane jako parametry. To learn more about the properties you can specify for an IoT hub in a template, see [Microsoft.Devices/IotHubs template reference](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. Aby utworzyć usługę aprowizacji, dodaj następujące wiersze po specyfikacji centrum IoT w kolekcji **resources**. Właściwości **name** i **location** usługi aprowizacji są przekazywane jako parametry. Określ centra IoT, które mają zostać powiązane z usługą aprowizacji, w kolekcji **iotHubs**. Należy co najmniej określić właściwości **connectionString** i **location** dla każdego połączonego centrum IoT. Można również ustawić właściwości, takie jak **allocationWeight** i **applyAllocationPolicy** dla każdego centrum IoT, a także właściwości, takie jak **allocationPolicy** i  **authorizationPolicies**, dla usługi aprowizacji. Więcej informacji zawiera dokumentacja dotycząca szablonów [Microsoft.Devices/provisioningServices](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices).

   Właściwość **dependsOn** służy do zapewnienia, że usługa Resource Manager utworzy centrum IoT zanim utworzy usługę aprowizacji. Szablon wymaga parametrów połączenia z centrum IoT, aby móc określić połączenie z usługą, dlatego centrum i jego klucze muszą zostać utworzone w pierwszej kolejności. W tym szablonie są używane funkcje takie jak **concat** i **listKeys**, które służą do tworzenia parametrów połączenia. Więcej informacji zawiera dokumentacja [funkcji szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions).

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Zapisz plik szablonu. Ukończony szablon powinien wyglądać następująco:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Tworzenie pliku parametrów usługi Resource Manager

The template that you defined in the last step uses parameters to specify the name of the IoT hub, the name of the provisioning service, and the location (Azure region) to create them. Te parametry są przekazywane w oddzielnym pliku. Umożliwia to ponowne użycie tego samego szablonu podczas wielu wdrożeń. Aby utworzyć plik parametrów, wykonaj następujące kroki:

1. Użyj edytora tekstów, aby utworzyć plik parametrów usługi Azure Resource Manager o nazwie **parameters.json** z następującym szkieletem zawartości: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Dodaj wartość **iotHubName** do sekcji parametrów.  An IoT hub name must be globally unique in Azure, so you may want to add a unique prefix or suffix to the example name, or choose a new name altogether. Make sure your name follows proper naming conventions for an IoT hub: it should be 3-50 characters in length, and can contain only upper or lower case alphanumeric characters or hyphens ('-'). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Dodaj wartość **provisioningServiceName** do sekcji parametrów. You will also need to choose a globally unique name for your provisioning service. Make sure it follows proper naming conventions for an IoT Hub Device Provisioning Service: it should be 3-64 characters in length and can contain only upper or lower case alphanumeric characters or hyphens ('-').

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Dodaj wartość **hubLocation** do sekcji parametrów. Ta wartość określa również lokalizację centrum IoT i usługi aprowizacji. Ta wartość musi odpowiadać jednej z lokalizacji określonej w kolekcji **allowedValues** w definicji parametrów w pliku szablonu. Ta kolekcja ogranicza wartości do lokalizacji platformy Azure, które obsługują centra IoT i usługi aprowizacji. Aby uzyskać listę obsługiwanych lokalizacji usługi Device Provisioning, można uruchomić następujące polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub odwiedzić stronę [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukać frazę „usługa Device Provisioning”.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Zapisz plik. 


> [!IMPORTANT]
> Centrum IoT i usługa aprowizacji będą publicznie wykrywalne jako punkty końcowe DNS, dlatego unikaj wprowadzania jakichkolwiek poufnych informacji podczas nadawania im nazw.
>

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Użyj następujących poleceń interfejsu wiersza polecenia Azure, aby wdrożyć szablony i zweryfikować wdrożenie.

1. Aby wdrożyć szablon, uruchom następujące [polecenie w celu rozpoczęcia wdrożenia](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Upewnij się, że właściwość **provisioningState** ma ustawioną wartość „Succeeded” w danych wyjściowych. 

   ![Dane wyjściowe aprowizacji](./media/quick-setup-auto-provision-rm/output.png) 


2. Aby zweryfikować wdrożenie, uruchom następujące [polecenie w celu wyświetlenia listy zasobów](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) i wyszukaj nową usługę aprowizacji i centrum IoT w danych wyjściowych:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. If you do not plan to continue, you can use the Azure CLI to [delete an individual resource][lnk-az-resource-command], such as an IoT hub or a provisioning service, or to delete a resource group and all of its resources.

Aby usunąć usługę aprowizacji, uruchom następujące polecenie:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
Aby usunąć centrum IoT, uruchom następujące polecenie:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Aby usunąć grupę zasobów i jej zasoby, uruchom następujące polecenie:

```azurecli
az group delete --name {your resource group name}
```

Grupy zasobów i poszczególne zasoby można usuwać również przy użyciu witryny Azure Portal, programu PowerShell lub interfejsów API REST, bądź obsługiwanych zestawów SDK platformy opublikowanych na potrzeby usługi Azure Resource Manager lub usługi IoT Hub Device Provisioning.

## <a name="next-steps"></a>Następne kroki

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 

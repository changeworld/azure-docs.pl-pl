---
title: 'Szybki Start: Konfigurowanie aprowizacji urządzeń przy użyciu szablonu Azure Resource Manager'
description: Przewodnik Szybki start platformy Azure — Konfigurowanie usługi Azure IoT Hub Device Provisioning Service przy użyciu szablonu
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fdc75424c5c99e80c13ac086229da93411e3ce83
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903358"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Szybki Start: Konfigurowanie IoT Hub Device Provisioning Service przy użyciu szablonu Azure Resource Manager

Przy użyciu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) można skonfigurować programowo zasoby w chmurze platformy Azure niezbędne do aprowizacji urządzeń. Poniżej przedstawiono procedurę tworzenia usługi IoT Hub, nowej usługi IoT Hub Device Provisioning oraz łączenia tych dwóch usług ze sobą przy użyciu szablonu usługi Azure Resource Manager. W tym przewodniku Szybki start [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) jest używany do wykonywania kroków programowych niezbędnych do utworzenia grupy zasobów i wdrożenia szablonu, ale te kroki można również z łatwością wykonać i wdrożyć szablon przy użyciu [witryny Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [programu PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), środowiska .NET, języka Ruby lub innych języków programowania. 


## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ten przewodnik Szybki start wymaga uruchomienia wiersza polecenia platformy Azure lokalnie. Musi być zainstalowany wiersz polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja interfejsu wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Logowanie się do platformy Azure i tworzenie grupy zasobów

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu polecenia Uruchom [polecenie Login][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się do platformy Azure zapewnia dostęp do wszystkich kont platformy Azure skojarzonych z poświadczeniami użytkownika. Użyj następującego [polecenia, aby wyświetlić listę kont platformy Azure][lnk-az-account-command] dostępnych do użycia:
    
    ```azurecli
    az account list 
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, która ma zostać użyta do uruchamiania poleceń w celu utworzenia centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Podczas tworzenia zasobów w chmurze platformy Azure, takich jak centra IoT i usługi aprowizacji, można tworzyć je w grupie zasobów. Użyj istniejącej grupy zasobów lub uruchom następujące [polecenie, aby utworzyć grupę zasobów][lnk-az-resource-command]:
    
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

4. Aby utworzyć centrum IoT, dodaj następujące wiersze do kolekcji **resources**. Element JSON określa minimalne właściwości wymagane do utworzenia centrum IoT. Właściwości **name** i **location** są przekazywane jako parametry. Aby dowiedzieć się więcej o właściwościach, które można określić dla centrum IoT w szablonie, zobacz dokumentację dotyczącą szablonów [Microsoft.Devices/IotHubs](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

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

W szablonie zdefiniowanym w ostatnim kroku są używane parametry służące do określenia nazwy centrum IoT, nazwy usługi aprowizacji oraz lokalizacji, w których mają zostać utworzone (region platformy Azure). Te parametry są przekazywane w oddzielnym pliku. Umożliwia to ponowne użycie tego samego szablonu podczas wielu wdrożeń. Aby utworzyć plik parametrów, wykonaj następujące kroki:

1. Użyj edytora tekstów, aby utworzyć plik parametrów usługi Azure Resource Manager o nazwie **parameters.json** z następującym szkieletem zawartości: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Dodaj wartość **iotHubName** do sekcji parametrów. W przypadku zmiany nazwy upewnij się, że jest zgodna z odpowiednią konwencją nazewnictwa dla centrum IoT. Powinna mieć długość 3–50 znaków i zawierać tylko małe i wielkie litery, cyfry i łączniki („-”). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Dodaj wartość **provisioningServiceName** do sekcji parametrów. W przypadku zmiany nazwy upewnij się, że jest zgodna z odpowiednią konwencją nazewnictwa usługi IoT Hub Device Provisioning. Powinna mieć długość 3–64 znaków i zawierać tylko małe i wielkie litery, cyfry i łączniki („-”).

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

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, możesz użyć interfejsu wiersza polecenia platformy Azure, aby [usunąć poszczególne zasoby][lnk-az-resource-command], takie jak centrum IoT lub usługa aprowizacji, lub usunąć grupę zasobów i wszystkie jej zasoby.

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

W tym przewodniku Szybki start zostały wdrożone centrum IoT i wystąpienie usługi Device Provisioning Service, po czym te dwa zasoby zostały połączone ze sobą. Aby dowiedzieć się, jak za pomocą tej konfiguracji zaaprowizować symulowane urządzenie, przejdź do przewodnika Szybki start omawiającego tworzenie symulowanego urządzenia.

> [!div class="nextstepaction"]
> [Przewodnik Szybki start z tworzeniem symulowanego urządzenia](./quick-create-simulated-device.md)


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

---
title: Łączenie szablonów dla wdrożenia platformy Azure | Microsoft Docs
description: Opisuje, jak używać połączonych szablonów w szablonie Azure Resource Manager, aby utworzyć modularne rozwiązanie szablonów. Pokazuje, jak przekazać wartości parametrów, określić plik parametrów i dynamicznie tworzone adresy URL.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: tomfitz
ms.openlocfilehash: 59af553f4080ca86e964b75234e4d812297d8541
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827344"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Używanie połączonych i zagnieżdżonych szablonów podczas wdrażania zasobów platformy Azure

Aby wdrożyć rozwiązanie, możesz użyć jednego szablonu lub szablonu głównego z wieloma powiązanymi szablonami. Powiązane szablony mogą być osobnymi plikami, które są połączone z głównym szablonem lub szablonami zagnieżdżonymi w szablonie głównym.

W przypadku małych i średnich rozwiązań jeden szablon jest łatwiejszy do zrozumienia i utrzymania. Wszystkie zasoby i wartości są widoczne w jednym pliku. W przypadku zaawansowanych scenariuszy połączone Szablony umożliwiają rozbicie rozwiązania na składniki przeznaczone do realizacji. Można łatwo ponownie wykorzystać te szablony dla innych scenariuszy.

W przypadku używania połączonych szablonów utworzysz szablon główny, który odbiera wartości parametrów podczas wdrażania. Główny szablon zawiera wszystkie połączone szablony i przekazuje wartości do tych szablonów zgodnie z wymaganiami.

Aby zapoznać się z samouczkiem, zobacz [Samouczek: Tworzenie połączonych Azure Resource Manager szablonów](./resource-manager-tutorial-create-linked-templates.md).

> [!NOTE]
> W przypadku szablonów połączonych lub zagnieżdżonych można używać tylko trybu wdrożenia [przyrostowego](deployment-modes.md) .
>

## <a name="deployments-resource"></a>Zasób wdrożeń

Aby połączyć się z innym szablonem, Dodaj zasób **wdrożenia** do szablonu głównego.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

Właściwości, które zapewniasz dla zasobu wdrożenia, różnią się w zależności od tego, czy łączysz się z szablonem zewnętrznym, czy zagnieżdżać szablon wbudowany w szablonie głównym.

## <a name="nested-template"></a>Szablon zagnieżdżony

Aby zagnieździć szablon w szablonie głównym, użyj właściwości **szablonu** i określ składnię szablonu.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
                "name": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> W przypadku szablonów zagnieżdżonych nie można używać parametrów ani zmiennych, które są zdefiniowane w szablonie zagnieżdżonym. Z głównego szablonu można używać parametrów i zmiennych. W poprzednim przykładzie `[variables('storageName')]` pobiera wartość z szablonu głównego, a nie szablon zagnieżdżony. To ograniczenie nie ma zastosowania do szablonów zewnętrznych.
>
> Dla dwóch zasobów zdefiniowanych w zagnieżdżonym szablonie, a jeden z nich zależy od drugiej, wartość zależności jest po prostu nazwą zasobu zależnego:
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> Nie można użyć funkcji `reference` w sekcji dane wyjściowe szablonu zagnieżdżonego dla zasobu wdrożonego w zagnieżdżonym szablonie. Aby zwrócić wartości wdrożonego zasobu w zagnieżdżonym szablonie, przekonwertuj zagnieżdżony szablon na połączony szablon.

Szablon zagnieżdżony wymaga [takich samych właściwości](resource-group-authoring-templates.md) jak szablon standardowy.

## <a name="external-template"></a>Szablon zewnętrzny

Aby połączyć się z szablonem zewnętrznym, użyj właściwości **templateLink** . Nie można określić pliku lokalnego lub pliku, który jest dostępny tylko w sieci lokalnej. Można podać tylko wartość identyfikatora URI, która zawiera **http** lub **https**. Menedżer zasobów musi mieć możliwość uzyskania dostępu do szablonu.

Jedną z opcji jest umieszczenie powiązanego szablonu na koncie magazynu i użycie identyfikatora URI dla tego elementu.

Parametry szablonu zewnętrznego można podać w zewnętrznym pliku lub w wierszu.

### <a name="external-parameters"></a>Parametry zewnętrzne

Podczas udostępniania zewnętrznego pliku parametrów należy użyć właściwości **parametersLink** :

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion":"1.0.0.0"
      },
      "parametersLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion":"1.0.0.0"
      }
    }
  }
]
```

Nie musisz podawać właściwości `contentVersion` dla szablonu lub parametrów. Jeśli nie podano wartości wersji zawartości, zostanie wdrożona bieżąca wersja szablonu. W przypadku podania wartości wersji zawartości musi ona być zgodna z wersją w połączonym szablonie; w przeciwnym razie wdrożenie zakończy się niepowodzeniem z powodu błędu.

### <a name="inline-parameters"></a>Parametry wbudowane

Lub można podać parametr w tekście. Nie można użyć obu parametrów wbudowanych i linku do pliku parametrów. Wdrożenie nie powiedzie się z powodu błędu w przypadku określenia obu `parametersLink` i `parameters`.

Aby przekazać wartość z szablonu głównego do połączonego szablonu, użyj właściwości **Parameters** .

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2018-05-01",
     "name": "linkedTemplate",
     "properties": {
       "mode": "Incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

## <a name="using-copy"></a>Używanie kopiowania

Aby utworzyć wiele wystąpień zasobu z zagnieżdżonym szablonem, Dodaj element Copy na poziomie zasobu **Microsoft. resources/Deployments** .

Poniższy przykładowy szablon pokazuje, jak używać kopiowania z zagnieżdżonym szablonem.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy":{
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
              "name": "Standard_LRS"
            }
            // no, copy doesn't work here
            //"copy":{
            //  "name": "storagecopy",
            //  "count": 2
            //}
          }
        ]
      }
    }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Używanie zmiennych do łączenia szablonów

Poprzednie przykłady wykazywały zakodowane wartości adresu URL dla linków szablonów. Takie podejście może działać w przypadku prostego szablonu, ale nie działa prawidłowo podczas pracy z dużym zestawem szablonów modułowych. Zamiast tego można utworzyć zmienną statyczną, która przechowuje podstawowy adres URL dla szablonu głównego, a następnie dynamicznie tworzy adresy URL dla połączonych szablonów z tego podstawowego adresu URL. Zaletą tego podejścia jest możliwość łatwego przenoszenia szablonu lub rozwidlenia go, ponieważ wystarczy zmienić zmienną statyczną w szablonie głównym. Główny szablon przekazuje poprawne identyfikatory URI w całym rozłożonym szablonie.

Poniższy przykład pokazuje, jak użyć podstawowego adresu URL, aby utworzyć dwa adresy URL dla połączonych szablonów (**sharedTemplateUrl** i **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Możesz również użyć [wdrożenia ()](resource-group-template-functions-deployment.md#deployment) , aby uzyskać podstawowy adres URL dla bieżącego szablonu i użyć go, aby uzyskać adres URL dla innych szablonów w tej samej lokalizacji. Takie podejście jest przydatne, jeśli lokalizacja szablonu ulegnie zmianie lub chcesz uniknąć twardych adresów URL kodowania w pliku szablonu. Właściwość templateLink jest zwracana tylko w przypadku łączenia się z szablonem zdalnym przy użyciu adresu URL. Jeśli używasz szablonu lokalnego, ta właściwość jest niedostępna.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Pobieranie wartości z połączonego szablonu

Aby uzyskać wartość wyjściową z połączonego szablonu, Pobierz wartość właściwości z składnią, taką jak: `"[reference('deploymentName').outputs.propertyName.value]"`.

Podczas pobierania właściwości wyjściowej z połączonego szablonu, nazwa właściwości nie może zawierać kreski.

W poniższych przykładach pokazano, jak odwoływać się do połączonego szablonu i pobrać wartość wyjściową. Połączony szablon zwraca prosty komunikat.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

Szablon główny wdraża połączony szablon i pobiera zwróconą wartość. Zwróć uwagę, że odwołuje się do zasobu wdrożenia według nazwy i używa nazwy właściwości zwróconej przez połączony szablon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Podobnie jak w przypadku innych typów zasobów, można ustawić zależności między połączonym szablonem i innymi zasobami. Gdy inne zasoby wymagają wartości wyjściowej z połączonego szablonu, upewnij się, że połączony szablon został wdrożony przed nimi. Lub, gdy połączony szablon bazuje na innych zasobach, upewnij się, że inne zasoby są wdrożone przed połączonym szablonem.

W poniższym przykładzie przedstawiono szablon, który wdraża publiczny adres IP i zwraca identyfikator zasobu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Aby użyć publicznego adresu IP z poprzedniego szablonu podczas wdrażania modułu równoważenia obciążenia, Połącz się z szablonem i Dodaj zależność od zasobu wdrożenia. Publiczny adres IP w module równoważenia obciążenia jest ustawiany na wartość wyjściową z połączonego szablonu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2018-11-01",
            "name": "[parameters('loadBalancers_name')]",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-and-nested-templates-in-deployment-history"></a>Szablony połączone i zagnieżdżone w historii wdrożenia

Menedżer zasobów przetwarza każdy szablon jako oddzielne wdrożenie w historii wdrażania. Z tego względu szablon główny z trzema połączonymi lub zagnieżdżonymi szablonami pojawia się w historii wdrożenia jako:

![Historia wdrożenia](./media/resource-group-linked-templates/deployment-history.png)

Możesz użyć tych oddzielnych wpisów w historii w celu pobrania wartości wyjściowych po wdrożeniu. Poniższy szablon tworzy publiczny adres IP i wyprowadza adres IP:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

Poniższy szablon zawiera linki do poprzedniego szablonu. Tworzy trzy publiczne adresy IP.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "copy": {
                "count": 3,
                "name": "ip-loop"
            },
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            }
        }
    ]
}
```

Po wdrożeniu można pobrać wartości wyjściowe z następującym skryptem programu PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Lub skrypt interfejsu wiersza polecenia platformy Azure w bash Shell:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Zabezpieczanie szablonu zewnętrznego

Mimo że połączony szablon musi być dostępny zewnętrznie, nie musi być ogólnie dostępny publicznie. Szablon można dodać do prywatnego konta magazynu, które jest dostępne tylko dla właściciela konta magazynu. Następnie można utworzyć token sygnatury dostępu współdzielonego (SAS), aby umożliwić dostęp podczas wdrażania. Ten token sygnatury dostępu współdzielonego należy dodać do identyfikatora URI dla połączonego szablonu. Mimo że token jest przekazaniem jako bezpieczny ciąg, identyfikator URI połączonego szablonu, łącznie z tokenem sygnatury dostępu współdzielonego, jest rejestrowany w operacjach wdrażania. Aby ograniczyć widoczność, ustaw wartość opcji Wygaśnięcie dla tokenu.

Plik parametrów można także ograniczyć do dostępu za pomocą tokenu SAS.

Obecnie nie można połączyć się z szablonem na koncie magazynu, które znajduje się za [zaporą usługi Azure Storage](../storage/common/storage-network-security.md).

Poniższy przykład pokazuje, jak przekazać token SAS podczas tworzenia połączenia z szablonem:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

W programie PowerShell otrzymujesz token dla kontenera i wdrażasz szablony przy użyciu następujących poleceń. Należy zauważyć, że parametr **containerSasToken** jest zdefiniowany w szablonie. Polecenie **New-AzResourceGroupDeployment** nie jest parametrem.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

W przypadku interfejsu wiersza polecenia platformy Azure w powłoce bash otrzymujesz token dla kontenera i wdrażasz szablony przy użyciu następującego kodu:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach przedstawiono typowe zastosowania połączonych szablonów.

|Szablon główny  |Połączony szablon |Opis  |
|---------|---------| ---------|
|[Witaj, świecie](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Zwraca ciąg z połączonego szablonu. |
|[Load Balancer z publicznym adresem IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Zwraca publiczny adres IP z połączonego szablonu i ustawia tę wartość w module równoważenia obciążenia. |
|[Wiele adresów IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Tworzy kilka publicznych adresów IP w połączonym szablonie.  |

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie połączonych Azure Resource Manager szablonów](./resource-manager-tutorial-create-linked-templates.md).
* Aby dowiedzieć się więcej na temat definiowania kolejności wdrażania dla zasobów, zobacz [Definiowanie zależności w szablonach Azure Resource Manager](resource-group-define-dependencies.md).
* Aby dowiedzieć się, jak zdefiniować jeden zasób, ale utworzyć wiele jego wystąpień, zobacz [Tworzenie wielu wystąpień zasobów w Azure Resource Manager](resource-group-create-multiple.md).
* Aby uzyskać instrukcje dotyczące konfigurowania szablonu na koncie magazynu i generowania tokenu SAS, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](resource-group-template-deploy.md) lub [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](resource-group-template-deploy-cli.md).

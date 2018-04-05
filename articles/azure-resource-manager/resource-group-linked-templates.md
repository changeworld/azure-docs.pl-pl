---
title: Link szablonów dla wdrożenia usługi Azure | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania szablonów połączonych w szablonie usługi Azure Resource Manager tworzenie rozwiązań moduły szablonu. Pokazuje, jak można przekazać wartości parametrów, określ plik parametrów i dynamicznie utworzone adresy URL.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2018
ms.author: tomfitz
ms.openlocfilehash: 326d6873ae78c5f712832c4cfce9c793f1dfbf37
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Za pomocą połączonego i zagnieżdżone szablony, w przypadku wdrażania zasobów platformy Azure

Aby wdrożyć rozwiązanie, służy jednego szablonu lub szablonu głównego z wielu szablonów pokrewne. Powiązane szablon może być oddzielny plik, który jest powiązany z szablonu głównego lub szablonu, który jest zagnieżdżony w szablonie głównym.

Dla małych i średnich rozwiązania jednego szablonu jest łatwiejsze do zrozumienia i obsługa. Jesteś w stanie zobaczyć wszystkie zasoby i wartości w jednym pliku. Dla zaawansowanych scenariuszy połączonego Szablony umożliwiają podział rozwiązania do elementów docelowych i ponowne użycie szablonów.

Podczas korzystania z połączonego szablonu, tworzenia szablonu głównego, który odbiera wartości parametrów podczas wdrażania. Główny szablon zawiera wszystkie połączone szablony i przekazuje wartości do tych szablonów, zgodnie z potrzebami.

## <a name="link-or-nest-a-template"></a>Połącz lub zagnieździć szablonu

Aby utworzyć link do innego szablonu, Dodaj **wdrożeń** zasobów w szablonie głównym.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

Właściwości, które zapewniają zasobu wdrożenia różnić w zależności od czy łączenie z szablonem zewnętrznych lub zagnieżdżania szablonu wbudowany w szablonie głównym.

### <a name="nested-template"></a>Szablon zagnieżdżony

Aby zagnieździć szablonu w szablonie głównym, należy użyć **szablonu** właściwości i określ składni szablonu.

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> Zagnieżdżone szablony nie można używać parametrów lub zmiennych, które są zdefiniowane w szablonie zagnieżdżonym. Można użyć parametrów i zmiennych z głównym szablonu. W powyższym przykładzie `[variables('storageName')]` pobiera wartość z głównym szablonu szablon zagnieżdżony. To ograniczenie nie ma zastosowania do szablonów zewnętrznych.
>
> Nie można użyć `reference` w sekcji danych wyjściowych szablonu zagnieżdżonych funkcji. Aby zwrócić wartości dla wdrożonych zasobów w szablonie zagnieżdżonych, przekonwertować szablon zagnieżdżony połączonego szablonu.

### <a name="external-template-and-external-parameters"></a>Szablon zewnętrznych i parametry zewnętrznych

Aby utworzyć łącze do zewnętrznego szablonu i pliku parametrów, należy użyć **templateLink** i **parametersLink**. Podczas łączenia z szablonu, usługi Resource Manager musi mieć możliwość do niego dostęp. Nie można określić plik lokalny lub plik, który jest dostępny tylko w sieci lokalnej. Możesz udostępniać wartość identyfikatora URI, która zawiera jedną **http** lub **https**. Jedną z opcji jest umieszczenie szablonu połączonego na koncie magazynu i użyj identyfikatora URI dla tego elementu.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
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

### <a name="external-template-and-inline-parameters"></a>Zewnętrzne parametrów szablonu i wbudowane

Lub możesz podać parametr wbudowanego. Aby przekazać wartości z głównym szablonu do połączonego szablonu, należy użyć **parametry**.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
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

## <a name="using-variables-to-link-templates"></a>Użycie zmiennych połączenia szablonów

W poprzednich przykładach pokazano zakodowanych wartości adresu URL dla łączy szablonu. Takie podejście może działać w przypadku prostego szablonu, ale nie działa w przypadku pracy z dużym zestawem moduły szablonów. Zamiast tego można utworzyć zmienną statyczną, przechowującym bazowy adres URL dla szablonu głównego, a następnie dynamicznie utworzyć adresów URL dla szablonów połączonych z tym podstawowego adresu URL. Zaletą tej metody jest można łatwo przenosić lub rozwidlania szablonu, ponieważ musisz zmienić zmienna statyczna w szablonie głównym. Główny szablon przekazuje prawidłowe identyfikatory URI w szablonie rozłożone.

Poniższy przykład przedstawia sposób Użyj podstawowego adresu URL, aby utworzyć dwa adresy URL dla szablonów połączonych (**sharedTemplateUrl** i **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Można również użyć [deployment()](resource-group-template-functions-deployment.md#deployment) uzyskać podstawowy adres URL dla bieżącego szablonu i używać, aby uzyskać adres URL dla innych szablonów w tej samej lokalizacji. Ta metoda jest przydatna, jeśli zmieni się lokalizację szablonu (być może z powodu versioning) lub aby uniknąć twardego kodowania adresów URL w pliku szablonu.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Pobiera wartości z połączonych szablonu

Można pobrać wartości danych wyjściowych z połączonego szablonu, pobrać wartość właściwości składnię: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

W poniższych przykładach pokazano sposób odwoływania się do połączonego szablonu i pobrać wartości danych wyjściowych. Szablon połączonego zwraca komunikat proste.

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

Główne szablon wdraża połączonego szablonu i pobiera zwracanej wartości. Zwróć uwagę, odwołuje się do zasobu wdrożenia według nazwy, czy używać nazwy właściwości zwrócony przez szablon połączony.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
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

Podobnie jak inne typy zasobów można ustawić zależności między połączonego szablonu i innych zasobów. W związku z tym inne zasoby potrzebują wartość wyjściowa szablonu połączone, można upewnij się, że połączonego szablonu jest wdrożyć przed ich. Lub, gdy szablon połączonego opiera się na inne zasoby, można upewnij się, że inne zasoby są wdrażane przed połączonego szablonu.

W poniższym przykładzie przedstawiono szablon, który wdraża publicznego adresu IP i zwraca identyfikator zasobu:

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
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
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

Aby użyć publiczny adres IP z powyższej szablonu podczas wdrażania usługi równoważenia obciążenia, łącza do szablonu, a następnie dodaj zależność zasobu wdrożenia. Publiczny adres IP modułu równoważenia obciążenia ma ustawioną wartość wyjściowa z połączonego szablonu.

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
            "name": "[parameters('loadBalancers_name')]",
            "apiVersion": "2017-06-01",
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
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
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

## <a name="linked-and-nested-templates-in-deployment-history"></a>Połączone i zagnieżdżone szablony w historii wdrożenia

Menedżer zasobów przetwarza każdego szablonu jako osobne wdrożenia w historii wdrożenia. W związku z tym głównym szablonu z trzech szablonów połączonych lub zagnieżdżony pojawia się w historii wdrożenia jako:

![Historia wdrożenia](./media/resource-group-linked-templates/deployment-history.png)

Te wpisy osobne w historii służy do pobierania wartości danych wyjściowych po wdrożeniu. Następujący szablon tworzy publiczny adres IP i wyświetla adres IP:

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
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
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

Poniższe łącza szablonu do poprzedniego szablonu. Tworzy trzy publicznych adresów IP.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            },
            "copy": {
                "count": 3,
                "name": "ip-loop"
            }
        }
    ]
}
```

Po wdrożeniu można pobrać wartości danych wyjściowych z następujący skrypt programu PowerShell:

```powershell
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Lub skryptu wiersza polecenia platformy Azure:

```azurecli
for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Zabezpieczanie zewnętrznych szablonu

Mimo że połączonego szablonu musi być dostępny zewnętrznie, nie trzeba być ogólnie dostępne publicznie. Możesz dodać do szablonu na konto magazynu prywatnego, który jest dostępny tylko dla właściciela konta magazynu. Następnie można utworzyć token sygnatury dostępu Współdzielonego dostępu współdzielonego, aby umożliwić dostęp podczas wdrażania. Identyfikator URI dla połączonych szablonu należy dodać tokenu sygnatury dostępu Współdzielonego. Nawet jeśli token jest przekazywany jako bezpieczny ciąg, identyfikator URI szablonu połączone, wraz z tokenem sygnatury dostępu Współdzielonego są rejestrowane w operacji wdrażania. W celu ograniczenia narażenia, ustawienia okresu ważności tokenu.

Można też maksymalnie dostęp za pośrednictwem tokenu sygnatury dostępu Współdzielonego pliku parametrów.

Poniższy przykład przedstawia sposób przekazywania tokenu sygnatury dostępu Współdzielonego w trakcie łączenia ze szablonu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
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

W programie PowerShell możesz uzyskać token dla kontenera i wdrażanie szablonów za pomocą następujących poleceń. Zwróć uwagę, że **containerSasToken** parametr jest zdefiniowany w szablonie. Nie jest parametrem w **AzureRmResourceGroupDeployment nowy** polecenia.

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Interfejsu wiersza polecenia Azure służy do pobrania tokenu dla kontenera i wdrażanie szablonów z następującym kodem:

```azurecli
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

## <a name="example-templates"></a>Przykład szablonów

Poniższe przykłady przedstawiają typowe zastosowania szablonów połączonych.

|Główny szablonu  |Połączone szablonu |Opis  |
|---------|---------| ---------|
|[Witaj, świecie](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[Połączone szablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Zwraca ciąg z połączonego szablonu. |
|[Moduł równoważenia obciążenia z publicznym adresem IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[Połączone szablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Zwraca publiczny adres IP z szablonów połączonych i ustawia tę wartość w usłudze równoważenia obciążenia. |
|[Wiele adresów IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [Połączone szablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Tworzy wiele publicznych adresów IP w szablonie połączony.  |

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat definiowania kolejność wdrażania zasobów, zobacz [Definiowanie zależności w szablonach usługi Azure Resource Manager](resource-group-define-dependencies.md).
* Aby dowiedzieć się, jak zdefiniować jeden zasób, ale utworzenia wielu wystąpień, zobacz [utworzyć wiele wystąpień zasobów usługi Azure Resource Manager](resource-group-create-multiple.md).
* Aby uzyskać instrukcje na temat ustawiania szablonu na koncie magazynu i generowania tokenu sygnatury dostępu Współdzielonego, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](resource-group-template-deploy.md) lub [wdrożenie zasobów z szablonami usługi Resource Manager i interfejsu wiersza polecenia Azure](resource-group-template-deploy-cli.md).
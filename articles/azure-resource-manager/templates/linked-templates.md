---
title: Łączenie szablonów do wdrożenia
description: Opisuje sposób użycia połączonymi szablonami w szablonie usługi Azure Resource Manager w celu utworzenia rozwiązania modułowe szablonu. Pokazuje, jak przekazać wartości parametrów, określ plik parametrów i dynamicznie utworzoną adresów URL.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: e26b795a645ab9128dd738ba6a54b66ac0b7da2a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356353"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Za pomocą połączone i zagnieżdżone szablony, podczas wdrażania zasobów platformy Azure

Aby wdrożyć złożone rozwiązania, możesz przerwać szablon w wielu powiązanych szablonach, a następnie wdrożyć je przy użyciu szablonu głównego. Powiązane szablony mogą być osobnymi plikami lub składnią szablonów, które są osadzone w szablonie głównym. W tym artykule jest stosowany **szablon połączona** z terminem do odwoływania się do oddzielnego pliku szablonu, który jest połączony z głównym szablonem. Używa warunkowego **szablonu** , aby odwołać się do składni szablonu osadzonego w szablonie głównym.

Dla małych i średnich rozwiązania pojedynczy szablon jest łatwiejsze do zrozumienia i utrzymania. Można wyświetlić wszystkich zasobów i wartości w jednym pliku. W przypadku zaawansowanych scenariuszy połączone Szablony umożliwiają rozbicie rozwiązania na składniki przeznaczone do realizacji. Można łatwo ponownie wykorzystać te szablony dla innych scenariuszy.

Aby zapoznać się z samouczkiem, zobacz [Samouczek: Tworzenie połączonych Azure Resource Manager szablonów](template-tutorial-create-linked-templates.md).

> [!NOTE]
> W przypadku szablonów połączonych lub zagnieżdżonych można używać tylko trybu wdrożenia [przyrostowego](deployment-modes.md) .
>

## <a name="nested-template"></a>Zagnieżdżony szablon

Aby zagnieździć szablon, Dodaj [zasób wdrożenia](/azure/templates/microsoft.resources/deployments) do szablonu głównego. We właściwości **szablonu** Określ składnię szablonu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Poniższy przykład służy do wdrażania konta magazynu za pomocą szablonu zagnieżdżonego.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="scope-for-expressions-in-nested-templates"></a>Zakres dla wyrażeń w zagnieżdżonych szablonach

Korzystając z szablonu zagnieżdżonego, można określić, czy wyrażenia szablonów są oceniane w zakresie szablonu nadrzędnego, czy szablonu zagnieżdżonego. Zakres określa, jak są rozwiązywane parametry, zmienne i funkcje, takie jak [resourceName](template-functions-resource.md#resourcegroup) i [Subscription](template-functions-resource.md#subscription) .

Należy ustawić zakres za pomocą właściwości `expressionEvaluationOptions`. Domyślnie właściwość `expressionEvaluationOptions` jest ustawiona na `outer`, co oznacza, że używa zakresu szablonu nadrzędnego. Ustaw wartość `inner` na wyrażenie zakresu dla szablonu zagnieżdżonego.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2017-05-10",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

Poniższy szablon pokazuje, jak są rozpoznawane wyrażenia szablonu zgodnie z zakresem. Zawiera zmienną o nazwie `exampleVar`, która jest zdefiniowana w szablonie nadrzędnym i zagnieżdżonym. Zwraca wartość zmiennej.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

Wartość zmiennej zmienia się w zależności od zakresu. W poniższej tabeli przedstawiono wyniki dla obu zakresów.

| Zakres | Dane wyjściowe |
| ----- | ------ |
| wewnętrzne | z zagnieżdżonego szablonu |
| zewnętrzny (lub domyślny) | z szablonu nadrzędnego |

Poniższy przykład wdraża program SQL Server i pobiera wpis tajny magazynu kluczy do użycia dla hasła. Zakres jest ustawiany na `inner`, ponieważ dynamicznie tworzy identyfikator magazynu kluczy i przekazuje go jako parametr do szablonu zagnieżdżonego.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

> [!NOTE]
>
> W przypadku ustawienia zakresu `outer`nie można użyć funkcji `reference` w sekcji dane wyjściowe szablonu zagnieżdżonego dla zasobu wdrożonego w zagnieżdżonym szablonie. Aby zwrócić wartości wdrożonego zasobu w zagnieżdżonym szablonie, użyj wewnętrznego zakresu lub przekonwertuj zagnieżdżony szablon na połączony szablon.

## <a name="linked-template"></a>Połączony szablon

Aby połączyć szablon, Dodaj [zasób wdrożenia](/azure/templates/microsoft.resources/deployments) do szablonu głównego. We właściwości **templateLink** Określ identyfikator URI szablonu do uwzględnienia. Poniższy przykład łączy się z szablonem, który wdraża nowe konto magazynu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Nie można określić w lokalnym pliku lub plików, która jest dostępna tylko w sieci lokalnej. Można podać tylko wartość identyfikatora URI, która zawiera **http** lub **https**. Menedżer zasobów musi mieć możliwość uzyskania dostępu do szablonu. Jedną z opcji jest umieszczenie szablonu połączone w ramach konta magazynu, i użyj identyfikatora URI dla tego elementu.

Nie musisz podawać `contentVersion` właściwości szablonu lub parametrów. Jeśli nie podasz wartość wersja zawartości jest wdrażany bieżącą wersję szablonu. Jeżeli podasz wartość wersji zawartości, musi być zgodna z wersją w połączony szablon; w przeciwnym razie wdrożenie zakończy się niepowodzeniem z powodu błędu.

### <a name="parameters-for-linked-template"></a>Parametry połączonego szablonu

Parametry powiązanego szablonu można podać w zewnętrznym pliku lub w wierszu. Podczas udostępniania zewnętrznego pliku parametrów należy użyć właściwości **parametersLink** :

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

Aby przekazać wartości parametrów w tekście, użyj właściwości **Parameters** .

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

Nie można użyć wbudowanego parametrów i link do pliku parametrów. Wdrożenie nie powiedzie się z powodu błędu, gdy określono zarówno `parametersLink`, jak i `parameters`.

## <a name="using-copy"></a>Używanie kopiowania

Aby utworzyć wiele wystąpień zasobu z zagnieżdżonym szablonem, Dodaj element Copy na poziomie zasobu **Microsoft. resources/Deployments** . Lub, jeśli zakres jest wewnętrzny, można dodać kopię w szablonie zagnieżdżonym.

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
    "expressionEvaluationOptions": {
    "scope": "inner"
    },
    "template": {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(variables('storageName'), copyIndex())]",
      "location": "West US",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
      // Copy works here when scope is inner
      // But, when scope is default or outer, you get an error
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

## <a name="using-variables-to-link-templates"></a>Używanie zmiennych połączyć szablonów

W poprzednich przykładach pokazano zakodowanych wartości adresu URL dla łączy szablonu. Takie podejście może działać w przypadku prostego szablonu, ale nie działa dobrze, podczas pracy z szerokiej gamy modułowej szablonów. Zamiast tego można utworzyć zmienną statyczną, przechowujący podstawowy adres URL dla głównego szablonu, a następnie dynamicznie utworzyć adresy URL dla szablonów połączonych z tym podstawowy adres URL. Zaletą tego podejścia jest można łatwo przenosić lub rozwidlenie szablonu, ponieważ trzeba zmienić statycznych zmiennych z głównego szablonu. Szablon głównego przekazuje poprawne identyfikatory URI w całym rozkładany szablonu.

Poniższy przykład pokazuje, jak użyć podstawowego adresu URL, aby utworzyć dwa adresy URL dla połączonych szablonów (**sharedTemplateUrl** i **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Możesz również użyć [wdrożenia ()](template-functions-deployment.md#deployment) , aby uzyskać podstawowy adres URL dla bieżącego szablonu i użyć go, aby uzyskać adres URL dla innych szablonów w tej samej lokalizacji. To podejście jest przydatne w przypadku zmiany lokalizacji szablonu, lub gdy chcesz uniknąć ciężko kodowania adresów URL w pliku szablonu. Właściwości templateLink jest zwracany tylko wtedy, w trakcie łączenia ze zdalnym szablonu przy użyciu adresu URL. Jeśli używasz lokalnego szablonu, ta właściwość nie jest dostępna.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Pobieranie wartości z połączonego szablonu

Aby uzyskać wartość wyjściową z połączonego szablonu, Pobierz wartość właściwości z składnią taką jak: `"[reference('deploymentName').outputs.propertyName.value]"`.

Podczas pobierania właściwości danych wyjściowych z dołączonego szablonu, nazwy właściwości nie może zawierać kreskę.

Poniższe przykłady pokazują, jak odwoływać się do dołączonego szablonu i pobierania wartości danych wyjściowych. Połączony szablon zwróci komunikat proste.

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

Główny szablon wdraża połączony szablon i pobiera zwracanej wartości. Należy zauważyć, że odwołuje się do zasobu wdrożenia według nazwy, przy czym nazwa właściwości zwrócony przez szablon połączone.

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

Podobnie jak inne typy zasobów można ustawić zależności między połączony szablon i innych zasobów. Gdy inne zasoby wymagają wartości wyjściowej z połączonego szablonu, upewnij się, że połączony szablon został wdrożony przed nimi. Lub, gdy połączony szablon opiera się na inne zasoby, sprawdź, czy innych zasobów wdrożonych przed dołączonego szablonu.

Poniższy przykład przedstawia szablon, który wdraża publiczny adres IP i zwraca identyfikator zasobu:

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

Aby użyć publiczny adres IP z poprzedniego szablonu podczas wdrażania modułu równoważenia obciążenia, łącza do szablonu i dodać zależność od zasobu wdrażania. Publiczny adres IP modułu równoważenia obciążenia jest równa wartości danych wyjściowych z dołączonego szablonu.

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

## <a name="deployment-history"></a>Historia wdrożenia

Usługi Resource Manager przetwarza każdy szablon jako osobne wdrożenia w historii wdrażania. Szablon główny z trzema połączonymi lub zagnieżdżonymi szablonami pojawia się w historii wdrożenia jako:

![Historia wdrożenia](./media/linked-templates/deployment-history.png)

Te oddzielne wpisy w historii służy do pobierania wartości danych wyjściowych po wdrożeniu. Następujący szablon tworzy publiczny adres IP, a także generuje adres IP:

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

Poniższe linki szablonu, aby Powyższy szablon. Tworzy trzy publicznych adresów IP.

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

Po wdrożeniu możesz pobrać wartości danych wyjściowych za pomocą następującego skryptu programu PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Lub skryptu wiersza polecenia platformy Azure w powłoce Bash:

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

## <a name="securing-an-external-template"></a>Zabezpieczanie zewnętrznego szablonu

Mimo że dołączonego szablonu musi być dostępny zewnętrznie, nie musi być ogólnie dostępne publicznie. Szablon można dodać do konta magazynu prywatnego, który jest dostępny dla właściciela konta magazynu. Następnie należy utworzyć token sygnatury (SAS) dostępu współdzielonego, aby umożliwić dostęp podczas wdrażania. Token sygnatury dostępu Współdzielonego możesz dodać do identyfikatora URI do dołączonego szablonu. Nawet jeśli token jest przekazywany jako bezpieczny ciąg, identyfikator URI szablonu połączony, w tym token sygnatury dostępu Współdzielonego jest rejestrowany w operacji wdrażania. Aby ograniczyć zagrożenia, należy ustawić wygaśnięcia tokenu.

Plik parametrów również może być ograniczona do dostępu za pomocą tokenu sygnatury dostępu Współdzielonego.

Obecnie nie można połączyć się z szablonem na koncie magazynu, które znajduje się za [zaporą usługi Azure Storage](../../storage/common/storage-network-security.md).

Poniższy przykład pokazuje, jak przekazać token sygnatury dostępu Współdzielonego podczas łączenia do szablonu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "securestring" }
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

W programie PowerShell możesz uzyskać token dla kontenera i wdrażanie szablonów za pomocą następujących poleceń. Należy zauważyć, że parametr **containerSasToken** jest zdefiniowany w szablonie. Polecenie **New-AzResourceGroupDeployment** nie jest parametrem.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

For Azure CLI w powłoce Bash możesz uzyskać token dla kontenera i wdrażania szablonów przy użyciu następującego kodu:

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

W poniższych przykładach pokazano typowych zastosowań połączonymi szablonami.

|Szablon głównego  |Połączony szablon |Opis  |
|---------|---------| ---------|
|[Witaj, świecie](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Zwraca ciąg z dołączonego szablonu. |
|[Load Balancer z publicznym adresem IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Zwraca publiczny adres IP z dołączonego szablonu i ustawia tę wartość w module równoważenia obciążenia. |
|[Wiele adresów IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Tworzy kilka publicznych adresów IP dołączonego szablonu.  |

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie połączonych Azure Resource Manager szablonów](template-tutorial-create-linked-templates.md).
* Aby dowiedzieć się więcej na temat definiowania kolejności wdrażania dla zasobów, zobacz [Definiowanie zależności w szablonach Azure Resource Manager](define-resource-dependency.md).
* Aby dowiedzieć się, jak zdefiniować jeden zasób, ale utworzyć wiele jego wystąpień, zobacz [Tworzenie wielu wystąpień zasobów w Azure Resource Manager](copy-resources.md).
* Aby uzyskać instrukcje dotyczące konfigurowania szablonu na koncie magazynu i generowania tokenu SAS, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](deploy-powershell.md) lub [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](deploy-cli.md).

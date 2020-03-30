---
title: Szablony łączy dla wdrożenia
description: W tym artykule opisano, jak używać połączonych szablonów w szablonie usługi Azure Resource Manager do tworzenia modułowego rozwiązania szablonu. Pokazuje sposób przekazywania wartości parametrów, określania pliku parametrów i dynamicznie tworzonych adresów URL.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 322797383ee865ceb66c44793387da827aeb8879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131927"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Używanie połączonych i zagnieżdżonych szablonów podczas wdrażania zasobów platformy Azure

Aby wdrożyć złożone rozwiązania, można podzielić szablon na wiele powiązanych szablonów, a następnie wdrożyć je razem za pomocą szablonu głównego. Powiązane szablony mogą być oddzielnymi plikami lub składnią szablonu osadzoną w szablonie głównym. W tym artykule użyto terminu **połączony szablon,** aby odwołać się do oddzielnego pliku szablonu, do którego odwołuje się łącze z głównego szablonu. Używa terminu **szablon zagnieżdżony,** aby odwoływać się do składni szablonu osadzonego w szablonie głównym.

W przypadku małych i średnich rozwiązań pojedynczy szablon jest łatwiejszy do zrozumienia i utrzymania. Wszystkie zasoby i wartości można zobaczyć w jednym pliku. W przypadku zaawansowanych scenariuszy połączone szablony umożliwiają podział rozwiązania na składniki docelowe. Można łatwo ponownie użyć tych szablonów dla innych scenariuszy.

Aby zapoznać się z samouczkiem, zobacz [Samouczek: tworzenie połączonych szablonów usługi Azure Resource Manager](template-tutorial-create-linked-templates.md).

> [!NOTE]
> W przypadku szablonów połączonych lub zagnieżdżonych można używać tylko trybu wdrażania [przyrostowego.](deployment-modes.md)
>

## <a name="nested-template"></a>Szablon zagnieżdżony

Aby zagnieździć szablon, dodaj [zasób wdrożeń](/azure/templates/microsoft.resources/deployments) do szablonu głównego. We właściwości **szablonu** określ składnię szablonu.

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

Poniższy przykład wdraża konto magazynu za pośrednictwem szablonu zagnieżdżonego.

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

### <a name="expression-evaluation-scope-in-nested-templates"></a>Zakres oceny wyrażenia w szablonach zagnieżdżonych

Korzystając z szablonu zagnieżdżonego, można określić, czy wyrażenia szablonu są oceniane w zakresie szablonu nadrzędnego, czy szablonu zagnieżdżonego. Zakres określa, jak parametry, zmienne i funkcje, takie jak [resourceGroup](template-functions-resource.md#resourcegroup) i [subskrypcji](template-functions-resource.md#subscription) są rozpoznawane.

Można ustawić zakres `expressionEvaluationOptions` za pośrednictwem właściwości. Domyślnie właściwość jest `expressionEvaluationOptions` `outer`ustawiona na , co oznacza, że używa zakresu szablonu nadrzędnego. Ustaw wartość, `inner` aby spowodować, że wyrażenia mają być oceniane w zakresie szablonu zagnieżdżonego.

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

Poniższy szablon pokazuje, jak wyrażenia szablonu są rozpoznawane zgodnie z zakresem. Zawiera zmienną `exampleVar` o nazwie, która jest zdefiniowana zarówno w szablonie nadrzędnym, jak i szablonie zagnieżdżonego. Zwraca wartość zmiennej.

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

Wartość zmienia `exampleVar` się w zależności `scope` od `expressionEvaluationOptions`wartości właściwości w . W poniższej tabeli przedstawiono wyniki dla obu zakresów.

| `expressionEvaluationOptions` `scope` | Dane wyjściowe |
| ----- | ------ |
| Wewnętrzny | z szablonu zagnieżdżonego |
| zewnętrzne (lub domyślne) | z szablonu nadrzędnego |

Poniższy przykład wdraża serwer SQL i pobiera klucz tajny magazynu kluczy do użycia dla hasła. Zakres jest ustawiony, `inner` ponieważ dynamicznie tworzy identyfikator magazynu `adminPassword.reference.keyVault` kluczy (zobacz w szablonach `parameters`zewnętrznych) i przekazuje go jako parametr do szablonu zagnieżdżonego.

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
> Gdy zakres jest `outer`ustawiony na , `reference` nie można użyć funkcji w sekcji dane wyjściowe szablonu zagnieżdżonego dla zasobu, który został wdrożony w szablonie zagnieżdżonego. Aby zwrócić wartości wdrożonego zasobu w szablonie zagnieżdżonym, użyj `inner` zakresu lub przekonwertuj szablon zagnieżdżony na szablon połączony.

## <a name="linked-template"></a>Szablon połączony

Aby połączyć szablon, dodaj [zasób wdrożeń](/azure/templates/microsoft.resources/deployments) do szablonu głównego. We właściwości **templateLink** określ identyfikator URI szablonu, który ma być uwzględnić. Poniższy przykład łączy się z szablonem, który wdraża nowe konto magazynu.

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

Podczas odwoływania się do połączonego szablonu `uri` wartość nie może być plikiem lokalnym ani plikiem, który jest dostępny tylko w sieci lokalnej. Należy podać wartość URI, którą można pobrać jako **http** lub **https**. 

> [!NOTE]
>
> Można odwoływać się do szablonów przy użyciu parametrów, które ostatecznie rozwiązać `_artifactsLocation` coś, co używa **http** lub **https**, na przykład przy użyciu parametru tak:`"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]",`



Menedżer zasobów musi mieć dostęp do szablonu. Jedną z opcji jest umieszczenie połączonego szablonu na koncie magazynu i użycie identyfikatora URI dla tego elementu.

### <a name="parameters-for-linked-template"></a>Parametry połączonego szablonu

Parametry połączonego szablonu można podać w pliku zewnętrznym lub wbudowanym. Podczas dostarczania zewnętrznego pliku parametrów należy użyć właściwości **parametersLink:**

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

Aby przekazać wartości parametrów w linii, należy użyć **parametrów** właściwości.

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

Nie można używać zarówno parametrów wbudowanych, jak i łącza do pliku parametrów. Wdrożenie kończy się niepowodzeniem `parametersLink` `parameters` z błędem, gdy oba i są określone.

## `contentVersion`

Nie musisz dostarczać `contentVersion` nieruchomości dla `templateLink` obiektu `parametersLink` lub nieruchomości. Jeśli nie podasz `contentVersion`, bieżąca wersja szablonu jest wdrażany. Jeśli podasz wartość dla wersji zawartości, musi ona być zgodna z wersją w szablonie połączonym; w przeciwnym razie wdrożenie zakończy się niepowodzeniem z powodu błędu.

## <a name="using-variables-to-link-templates"></a>Używanie zmiennych do łączenia szablonów

W poprzednich przykładach pokazano zakodowane wartości adresów URL dla łączy szablonu. Takie podejście może działać w przypadku prostego szablonu, ale nie działa dobrze w przypadku dużego zestawu szablonów modułowych. Zamiast tego można utworzyć zmienną statyczną, która przechowuje podstawowy adres URL dla szablonu głównego, a następnie dynamicznie tworzyć adresy URL połączonych szablonów z tego podstawowego adresu URL. Zaletą tego podejścia jest to, że można łatwo przenieść lub rozwidlić szablon, ponieważ należy zmienić tylko zmienną statyczną w szablonie głównym. Główny szablon przekazuje poprawne identyfikatory URI w całym szablonie rozłożone.

W poniższym przykładzie pokazano, jak użyć podstawowego adresu URL do utworzenia dwóch adresów URL dla połączonych szablonów (**sharedTemplateUrl** i **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Można również użyć [deployment(),](template-functions-deployment.md#deployment) aby uzyskać podstawowy adres URL dla bieżącego szablonu i użyć go, aby uzyskać adres URL dla innych szablonów w tej samej lokalizacji. Takie podejście jest przydatne, jeśli lokalizacja szablonu zmienia się lub chcesz uniknąć twardych adresów URL kodowania w pliku szablonu. Właściwość templateLink jest zwracana tylko podczas łączenia z szablonem zdalnym z adresem URL. Jeśli używasz szablonu lokalnego, ta właściwość nie jest dostępna.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Ostatecznie należy użyć zmiennej `uri` we właściwości `templateLink` właściwości.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Korzystanie z kopii

Aby utworzyć wiele wystąpień zasobu z zagnieżdżonego szablonu, dodaj element kopiowania na poziomie zasobu **Microsoft.Resources/deployments.** Lub, jeśli zakres jest wewnętrzny, można dodać kopię w szablonie zagnieżdżonego.

Poniższy przykładowy szablon pokazuje, jak używać kopiowania z szablonem zagnieżdżanym.

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

## <a name="get-values-from-linked-template"></a>Pobieranie wartości z połączonego szablonu

Aby uzyskać wartość wyjściową z połączonego szablonu, pobierz wartość `"[reference('deploymentName').outputs.propertyName.value]"`właściwości ze składnią, taką jak: .

Podczas uzyskiwania właściwości wyjściowej z połączonego szablonu nazwa właściwości nie może zawierać kreski.

Poniższe przykłady pokazują, jak odwołać się do połączonego szablonu i pobrać wartość danych wyjściowych. Połączony szablon zwraca prostą wiadomość.  Po pierwsze, połączony szablon:

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

Szablon główny wdraża szablon połączony i pobiera zwróconą wartość. Należy zauważyć, że odwołuje się do zasobu wdrożenia według nazwy i używa nazwy właściwości zwróconej przez połączony szablon.

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

Podobnie jak w przypadku innych typów zasobów, można ustawić zależności między połączonym szablonem a innymi zasobami. Jeśli inne zasoby wymagają wartości wyjściowej z połączonego szablonu, upewnij się, że połączony szablon jest wdrażany przed nimi. Lub, gdy połączony szablon opiera się na innych zasobach, upewnij się, że inne zasoby są wdrażane przed szablonem połączonym.

W poniższym przykładzie przedstawiono szablon, który wdraża publiczny adres IP i zwraca identyfikator zasobu platformy Azure dla tego publicznego adresu IP:

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

Aby użyć publicznego adresu IP z poprzedniego szablonu podczas wdrażania modułu równoważenia `Microsoft.Resources/deployments` obciążenia, łącz się z szablonem i zadeklarować zależność od zasobu. Publiczny adres IP modułu równoważenia obciążenia jest ustawiany na wartość wyjściową z połączonego szablonu.

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
                // this is where the output value from linkedTemplate is used
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
      // This is where the dependency is declared
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

## <a name="deployment-history"></a>Historia wdrażania

Menedżer zasobów przetwarza każdy szablon jako oddzielne wdrożenie w historii wdrażania. Główny szablon z trzema połączonymi lub zagnieżdżonych szablonami pojawia się w historii wdrażania jako:

![Historia wdrażania](./media/linked-templates/deployment-history.png)

Te oddzielne wpisy w historii można użyć do pobierania wartości danych wyjściowych po wdrożeniu. Poniższy szablon tworzy publiczny adres IP i wyprowadza adres IP:

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

Poniższy szablon zawiera łącza do poprzedniego szablonu. Tworzy trzy publiczne adresy IP.

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

Po wdrożeniu można pobrać wartości wyjściowe za pomocą następującego skryptu programu PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Lub skrypt interfejsu wiersza polecenia platformy Azure w powłoce Bash:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Zabezpieczanie szablonu zewnętrznego

Chociaż połączony szablon musi być dostępny zewnętrznie, nie musi być ogólnie dostępny publicznie. Szablon można dodać do konta magazynu prywatnego, które jest dostępne tylko dla właściciela konta magazynu. Następnie należy utworzyć token podpisu dostępu współdzielonego (SAS), aby umożliwić dostęp podczas wdrażania. Dodaj ten token sygnatury dostępu Współdzielonego do identyfikatora URI dla połączonego szablonu. Mimo że token jest przekazywany jako bezpieczny ciąg, identyfikator URI połączonego szablonu, w tym token sygnatury dostępu Współdzielonego, jest rejestrowany w operacjach wdrażania. Aby ograniczyć ekspozycję, należy ustawić wygaśnięcie tokenu.

Plik parametrów może być również ograniczony do dostępu za pośrednictwem tokenu sygnatury dostępu współdzielonego.

Obecnie nie można połączyć się z szablonem na koncie magazynu, który znajduje się za [zaporą usługi Azure Storage](../../storage/common/storage-network-security.md).

W poniższym przykładzie pokazano, jak przekazać token sygnatury dostępu Współdzielonego podczas łączenia z szablonem:

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

W programie PowerShell otrzymasz token dla kontenera i wdrożyć szablony z następującymi poleceniami. Należy zauważyć, że **parametr containerSasToken** jest zdefiniowany w szablonie. Nie jest to parametr w **new-AzResourceGroupDeployment** polecenia.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

W przypadku interfejsu wiersza polecenia platformy Azure w powłoce bash otrzymasz token dla kontenera i wdrożysz szablony z następującym kodem:

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
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Przykładowe szablony

Poniższe przykłady pokazują typowe zastosowania połączonych szablonów.

|Szablon główny  |Szablon połączony |Opis  |
|---------|---------| ---------|
|[Cześć ludzie](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[szablon połączony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Zwraca ciąg z szablonu połączonego. |
|[Moduł równoważenia obciążenia z publicznym adresem IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[szablon połączony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Zwraca publiczny adres IP z połączonego szablonu i ustawia tę wartość w równoważniku obciążenia. |
|[Wiele adresów IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [szablon połączony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Tworzy kilka publicznych adresów IP w szablonie połączonym.  |

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: tworzenie połączonych szablonów usługi Azure Resource Manager](template-tutorial-create-linked-templates.md).
* Aby dowiedzieć się więcej o definiowaniu kolejności wdrażania zasobów, zobacz [Definiowanie zależności w szablonach usługi Azure Resource Manager](define-resource-dependency.md).
* Aby dowiedzieć się, jak zdefiniować jeden zasób, ale utworzyć wiele jego wystąpień, zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).
* Aby uzyskać instrukcje dotyczące konfigurowania szablonu na koncie magazynu i generowania tokenu sygnatury dostępu Współdzielonego, zobacz [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i zasobów usługi Azure PowerShell](deploy-powershell.md) lub [wdrażanie zasobów z szablonami usługi Resource Manager i interfejsu wiersza polecenia platformy Azure.](deploy-cli.md)

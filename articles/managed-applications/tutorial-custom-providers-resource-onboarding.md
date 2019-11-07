---
title: Dołączanie zasobów z dostawcami niestandardowymi platformy Azure
description: Dołączanie zasobów za pomocą dostawców niestandardowych umożliwia manipulowanie i rozszerzanie istniejących zasobów platformy Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: c722b4dc3219f76f8c7c571af3613996fec9e69c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608680"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Samouczek: dołączanie zasobów z dostawcami niestandardowymi platformy Azure

Ten samouczek spowoduje wdrożenie niestandardowego dostawcy zasobów na platformie Azure, który rozszerza interfejs API Azure Resource Manager z typem zasobu "Microsoft. CustomProviders/Association". Ten przykład pokazuje, jak rozłożyć istniejące zasoby spoza grupy zasobów, w której przebywa wystąpienie dostawcy niestandardowego. W tym przykładzie dostawca zasobów niestandardowych jest obsługiwany przez aplikację logiki platformy Azure, ale można użyć dowolnego publicznego punktu końcowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz znać:

* Możliwości [niestandardowe dostawcy platformy Azure](custom-providers-overview.md) .
* [Dołączanie zasobów z dostawcami niestandardowymi](concepts-custom-providers-resourceonboarding.md).

## <a name="getting-started-with-resource-onboarding"></a>Wprowadzenie do dołączania do zasobów

W tym przykładzie istnieją dwa elementy, które należy wdrożyć: Dostawca niestandardowy i skojarzenie. Aby uprościć przykład, istnieje pojedynczy szablon, który umożliwia opcjonalne wdrożenie obu tych metod.

Szablon będzie korzystać z następujących zasobów:

* Microsoft. CustomProviders/resourceProviders
* Microsoft. Logic/przepływy pracy
* Microsoft. CustomProviders/skojarzenia

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>Wdrażanie infrastruktury dostawcy niestandardowego

Pierwsza część szablonu wdraża niestandardową infrastrukturę dostawcy. Ta infrastruktura definiuje efekt zasobu "Associations". Jeśli nie znasz niestandardowych dostawców, zapoznaj się z tematem [podstawowe informacje o dostawcach](custom-providers-overview.md).

Wdróżmy infrastrukturę dostawcy niestandardowego, skopiując, Zapisz i Wdróż powyższy szablon albo wykonaj czynności i wdróż je w Azure Portal.

1. Otwórz Azure Portal z https://portal.azure.com.

2. Wyszukaj w obszarze `All Services` lub na środkowym pasku wyszukiwania. 

![Wyszukiwanie szablonów](media/custom-providers-resource-onboarding/templates.png)

3. Naciśnij przycisk `+ Add` znajdujący się w lewym górnym rogu bloku "Szablony".

![Dodaj nowy szablon](media/custom-providers-resource-onboarding/templatesadd.png)

4. Wypełnij pola "nazwa" i "opis" w obszarze "ogólne" dla nowego szablonu.

![Nazwa i opis szablonu](media/custom-providers-resource-onboarding/templatesdescription.png)

5. Wypełnij szablon Menedżer zasobów, kopiując szablon JSON z tabeli "wprowadzenie do zasobów".

![Wypełnij szablon Menedżer zasobów](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. Naciśnij przycisk `Add`, aby utworzyć nowy szablon. Jeśli nowy szablon nie jest wyświetlany, kliknij przycisk `Refresh`.

7. Wybierz nowo utworzony szablon i naciśnij przycisk `Deploy`.

![Wybierz nowo utworzony szablon i Wdróż go](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. Wprowadź parametry ustawień dla wymaganych pól i wybierz subskrypcję i grupę zasobów. "Identyfikator niestandardowego dostawcy zasobów" może pozostać puste.

| Nazwa ustawienia | Wymagany | Opis |
| ------------ | -------- | ----------- |
| Lokalizacja | *opcję* | Lokalizacja zasobów w szablonie. |
| Nazwa aplikacji logiki | *znaleziono* | Nazwa aplikacji logiki. |
| Nazwa niestandardowego dostawcy zasobów | *znaleziono* | Nazwa niestandardowego dostawcy zasobów. |
| Identyfikator niestandardowego dostawcy zasobów | *znaleziono* | Istniejący dostawca zasobów niestandardowych obsługujący zasób "Association". Określenie tej opcji spowoduje pominięcie wdrożenia dostawcy aplikacji logiki i niestandardowego. |
| Nazwa skojarzenia | *znaleziono* | Nazwa zasobu skojarzenia. |

Przykładowe parametry:

![Parametry szablonu wejściowego](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. Przejdź do wdrożenia i poczekaj na jego zakończenie. Powinna zostać wyświetlona wartość powodzenie i wyjście nowego zasobu "Association".

Powodzenie wdrożenia:

![Wdrożenie dostawcy niestandardowego](media/custom-providers-resource-onboarding/customproviderdeployment.png)

Grupa zasobów z opcją "Pokaż ukryte typy":

![Wdrożenie dostawcy niestandardowego](media/custom-providers-resource-onboarding/showhidden.png)

10. Eksploruj kartę "Historia uruchamiania" aplikacji logiki, aby zobaczyć wywołania dla tworzenia skojarzenia.

![Historia uruchamiania aplikacji logiki](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Wdróż dodatkowe skojarzenia

Po skonfigurowaniu infrastruktury niestandardowego dostawcy można łatwo wdrożyć dodatkowe "skojarzenia". Grupa zasobów dla dodatkowych "skojarzeń" nie musi być taka sama jak grupa zasobów, w której została wdrożona infrastruktura dostawcy niestandardowego. Aby można było utworzyć skojarzenie, uprawnienia "Microsoft. CustomProviders/resourceproviders/Write" są konieczne w określonym "IDENTYFIKATORze niestandardowego dostawcy zasobów".

1. Przejdź do zasobu niestandardowego dostawcy "Microsoft. CustomProviders/resourceProviders" w grupie zasobów poprzedniego wdrożenia. Należy zaznaczyć pole wyboru "Pokaż typy ukryte".

![Wdrożenie dostawcy niestandardowego](media/custom-providers-resource-onboarding/showhidden.png)

2. Skopiuj Właściwość "Resource ID" dostawcy niestandardowego.

3. Wyszukaj w obszarze `All Services` lub na środkowym pasku wyszukiwania. 

![Wyszukiwanie szablonów](media/custom-providers-resource-onboarding/templates.png)

4. Wybierz wcześniej utworzony szablon i naciśnij przycisk `Deploy`.

![Wybierz wcześniej utworzony szablon i Wdróż go](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. Wprowadź parametry ustawień dla wymaganych pól i wybierz subskrypcję i inną grupę zasobów. Dla ustawienia "niestandardowy identyfikator dostawcy zasobów" wprowadź skopiowany "Identyfikator zasobu" wcześniej wdrożonego niestandardowego dostawcę.

6. Przejdź do wdrożenia i poczekaj na jego zakończenie. Teraz należy wdrożyć tylko nowy zasób "Associations".

![Utworzono skojarzenie](media/custom-providers-resource-onboarding/createdassociationresource.png)

Opcjonalnie możesz przejść z powrotem do aplikacji logiki "Historia uruchamiania" i zobaczyć, że zostało wykonane inne wywołanie do aplikacji logiki. Aplikację logiki można zaktualizować, aby rozszerzyć dodatkowe funkcje dla każdego utworzonego skojarzenia.

## <a name="looking-for-help"></a>Szukasz pomocy

Jeśli masz pytania dotyczące dostawców niestandardowych platformy Azure, spróbuj zadać [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers). Podobne pytanie mogło zostać już zgłoszone i nie udzielono odpowiedzi, więc najpierw należy sprawdzić przed opublikowaniem. Dodaj tag `azure-custom-providers`, aby uzyskać szybką odpowiedź!


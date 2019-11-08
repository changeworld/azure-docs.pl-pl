---
title: Dołączanie zasobów z dostawcami niestandardowymi platformy Azure
description: Dołączanie zasobów za pomocą dostawców niestandardowych umożliwia manipulowanie i poszerzanie istniejących zasobów platformy Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 45086987d5ba3a619028ced798712f2870c6d487
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826784"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Samouczek: dołączanie zasobów z dostawcami niestandardowymi platformy Azure

W tym samouczku zostanie wdrożony dostawca zasobów niestandardowych platformy Azure, który rozszerza interfejs API Azure Resource Manager z typem zasobu Microsoft. CustomProviders/Association. W tym samouczku pokazano, jak rozłożyć istniejące zasoby spoza grupy zasobów, w której znajduje się wystąpienie dostawcy niestandardowego. W tym samouczku dostawca zasobów niestandardowych jest obsługiwany przez aplikację logiki platformy Azure, ale można użyć dowolnego publicznego punktu końcowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz znać:

* Możliwości [dostawców niestandardowych platformy Azure](custom-providers-overview.md).
* Podstawowe informacje [na temat dołączania zasobów z dostawcami niestandardowymi](concepts-custom-providers-resourceonboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Wprowadzenie do dołączania do zasobów

W tym samouczku istnieją dwa elementy, które należy wdrożyć: Dostawca niestandardowy i skojarzenie. Aby uprościć proces, można opcjonalnie użyć pojedynczego szablonu, który wdraża oba elementy.

Ten zasób będzie używany przez szablon:

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

W pierwszej części szablonu jest wdrażana infrastruktura dostawcy niestandardowego. Ta infrastruktura definiuje efekt zasobu skojarzenia. Jeśli nie masz doświadczenia z dostawcami niestandardowymi, zobacz [podstawowe informacje o dostawcach](custom-providers-overview.md).

Wdróżmy infrastrukturę dostawcy niestandardowego. Skopiuj, Zapisz i Wdróż poprzedni szablon albo wykonaj czynności i Wdróż infrastrukturę przy użyciu Azure Portal.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Wyszukaj **Szablony** w obszarze **wszystkie usługi** lub przy użyciu głównego pola wyszukiwania:

   ![Wyszukaj szablony](media/custom-providers-resource-onboarding/templates.png)

3. Wybierz pozycję **Dodaj** w okienku **Szablony** :

   ![Wybierz pozycję Dodaj](media/custom-providers-resource-onboarding/templatesadd.png)

4. W obszarze **Ogólne**wprowadź **nazwę** i **Opis** nowego szablonu:

   ![Nazwa i opis szablonu](media/custom-providers-resource-onboarding/templatesdescription.png)

5. Utwórz szablon Menedżer zasobów przez skopiowanie szablonu JSON z sekcji "wprowadzenie do usługi Resource dołączania" w tym artykule:

   ![Tworzenie szablonu usługi Resource Manager](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. Wybierz pozycję **Dodaj** , aby utworzyć szablon. Jeśli nowy szablon nie zostanie wyświetlony, wybierz pozycję **Odśwież**.

7. Wybierz nowo utworzony szablon, a następnie wybierz pozycję **Wdróż**:

   ![Wybierz nowy szablon, a następnie wybierz pozycję Wdróż.](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. Wprowadź ustawienia dla wymaganych pól, a następnie wybierz subskrypcję i grupę zasobów. Pole **Identyfikator niestandardowego dostawcy zasobów** można pozostawić puste.

   | Nazwa ustawienia | Wymagana? | Opis |
   | ------------ | -------- | ----------- |
   | Lokalizacja | Tak | Lokalizacja zasobów w szablonie. |
   | Nazwa aplikacji logiki | Nie | Nazwa aplikacji logiki. |
   | Nazwa niestandardowego dostawcy zasobów | Nie | Nazwa niestandardowego dostawcy zasobów. |
   | Identyfikator niestandardowego dostawcy zasobów | Nie | Istniejący dostawca zasobów niestandardowych obsługujący zasób skojarzenia. Jeśli określisz wartość w tym miejscu, wdrożenie aplikacji logiki i dostawcy niestandardowego zostanie pominięte. |
   | Nazwa skojarzenia | Nie | Nazwa zasobu skojarzenia. |

   Przykładowe parametry:

   ![Wprowadź parametry szablonu](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. Przejdź do wdrożenia i poczekaj na jego zakończenie. Powinien wyglądać podobnie do poniższego zrzutu ekranu. Nowy zasób skojarzenia powinien zostać wyświetlony jako dane wyjściowe:

   ![Pomyślne wdrożenie](media/custom-providers-resource-onboarding/customproviderdeployment.png)

   Oto Grupa zasobów z wybraną pozycją **Pokaż ukryte typy** :

   ![Wdrożenie dostawcy niestandardowego](media/custom-providers-resource-onboarding/showhidden.png)

10. Eksploruj kartę **historia uruchomień** aplikacji logiki, aby wyświetlić wywołania tworzenia skojarzenia:

    ![Historia uruchomień aplikacji logiki](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Wdróż dodatkowe skojarzenia

Po skonfigurowaniu infrastruktury niestandardowego dostawcy można łatwo wdrożyć więcej skojarzeń. Grupa zasobów dla dodatkowych skojarzeń nie musi być taka sama jak grupa zasobów, w której wdrożono infrastrukturę dostawcy niestandardowego. Aby utworzyć skojarzenie, musisz mieć uprawnienia Microsoft. CustomProviders/resourceproviders/Write do określonego niestandardowego identyfikatora dostawcy zasobów.

1. Przejdź do zasobu dostawcy niestandardowego **Microsoft. CustomProviders/resourceProviders** w grupie zasobów poprzedniego wdrożenia. Musisz zaznaczyć pole wyboru **Pokaż ukryte typy** :

   ![Przejdź do zasobu](media/custom-providers-resource-onboarding/showhidden.png)

2. Skopiuj Właściwość Identyfikator zasobu dostawcy niestandardowego.

3. Wyszukaj **Szablony** w obszarze **wszystkie usługi** lub przy użyciu głównego pola wyszukiwania:

   ![Wyszukaj szablony](media/custom-providers-resource-onboarding/templates.png)

4. Wybierz wcześniej utworzony szablon, a następnie wybierz pozycję **Wdróż**:

   ![Wybierz wcześniej utworzony szablon, a następnie wybierz pozycję Wdróż.](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. Wprowadź ustawienia dla wymaganych pól, a następnie wybierz subskrypcję i inną grupę zasobów. W polu **Identyfikator niestandardowego dostawcy zasobów** wprowadź identyfikator zasobu skopiowany z wcześniej wdrożonego dostawcy niestandardowego.

6. Przejdź do wdrożenia i poczekaj na jego zakończenie. Powinien teraz wdrożyć tylko nowy zasób skojarzenia:

   ![Nowy zasób skojarzeń](media/custom-providers-resource-onboarding/createdassociationresource.png)

Jeśli chcesz, możesz wrócić do **historii uruchamiania** aplikacji logiki i zobaczyć, że inne wywołanie zostało wykonane w aplikacji logiki. Możesz zaktualizować aplikację logiki, aby rozszerzyć dodatkowe funkcje dla każdego utworzonego skojarzenia.

## <a name="getting-help"></a>Uzyskiwanie pomocy

Jeśli masz pytania dotyczące dostawców niestandardowych platformy Azure, spróbuj zadawać je na [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers). Na podobnej pytanie mogły już zostać odebrane odpowiedzi, więc najpierw należy sprawdzić przed opublikowaniem. Dodaj tag `azure-custom-providers`, aby uzyskać szybką odpowiedź!


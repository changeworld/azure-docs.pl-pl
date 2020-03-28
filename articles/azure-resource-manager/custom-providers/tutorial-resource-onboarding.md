---
title: Samouczek — dołączanie zasobów
description: Dołączanie zasobów za pośrednictwem dostawców niestandardowych umożliwia manipulowanie i rozszerzanie istniejących zasobów platformy Azure.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649939"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Samouczek: Dołączanie zasobów za pomocą dostawców niestandardowych platformy Azure

W tym samouczku wdrożysz na platformie Azure niestandardowego dostawcę zasobów, który rozszerza interfejs API usługi Azure Resource Manager o typ zasobu Microsoft.CustomProviders/associations. W samouczku pokazano, jak rozszerzyć istniejące zasoby, które znajdują się poza grupą zasobów, w której znajduje się wystąpienie dostawcy niestandardowego. W tym samouczku dostawca zasobów niestandardowych jest obsługiwany przez aplikację logiki platformy Azure, ale można użyć dowolnego publicznego punktu końcowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz wiedzieć:

* Możliwości dostawców [niestandardowych platformy Azure](overview.md).
* Podstawowe informacje [o dołączaniu zasobów u niestandardowych dostawców](concepts-resource-onboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Wprowadzenie do dołączania zasobów

W tym samouczku istnieją dwa elementy, które należy wdrożyć: dostawca niestandardowy i skojarzenia. Aby ułatwić proces, można opcjonalnie użyć jednego szablonu, który wdraża oba.

Szablon użyje tych zasobów:

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/przepływy pracy
* Microsoft.CustomProviders/skojarzenia

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

Pierwsza część szablonu wdraża infrastrukturę dostawcy niestandardowego. Ta infrastruktura definiuje wpływ zasobów skojarzeń. Jeśli nie znasz dostawców niestandardowych, zobacz [Podstawowe informacje o dostawcach niestandardowych](overview.md).

Zastosujmy niestandardową infrastrukturę dostawcy. Skopiuj, zapisz i wydrążyj poprzedni szablon lub postępuj zgodnie z nią i wdrażaj infrastrukturę za pomocą witryny Azure portal.

1. Przejdź do [witryny Azure portal](https://portal.azure.com).

2. Wyszukaj **szablony** we **wszystkich usługach** lub korzystając z głównego pola wyszukiwania:

   ![Wyszukiwanie szablonów](media/tutorial-resource-onboarding/templates.png)

3. Wybierz **pozycję Dodaj** w okienku **Szablony:**

   ![Wybierz pozycję Dodaj](media/tutorial-resource-onboarding/templatesadd.png)

4. W obszarze **Ogólne**wprowadź **nazwę** i **opis** nowego szablonu:

   ![Nazwa i opis szablonu](media/tutorial-resource-onboarding/templatesdescription.png)

5. Utwórz szablon Menedżera zasobów, kopiując w szablonie JSON z sekcji "Wprowadzenie do dołączania zasobów" w tym artykule:

   ![Tworzenie szablonu usługi Resource Manager](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. Wybierz **pozycję Dodaj,** aby utworzyć szablon. Jeśli nowy szablon nie jest wyświetlany, wybierz pozycję **Odśwież**.

7. Wybierz nowo utworzony szablon, a następnie wybierz pozycję **Wdrażanie:**

   ![Wybierz nowy szablon, a następnie wybierz pozycję Wdrażanie](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Wprowadź ustawienia wymaganych pól, a następnie wybierz subskrypcję i grupę zasobów. Pole Identyfikator **dostawcy zasobów niestandardowych** można pozostawić puste.

   | Nazwa ustawienia | Wymagana? | Opis |
   | ------------ | -------- | ----------- |
   | Lokalizacja | Tak | Lokalizacja zasobów w szablonie. |
   | Nazwa aplikacji logiki | Nie | Nazwa aplikacji logiki. |
   | Nazwa niestandardowego dostawcy zasobów | Nie | Nazwa dostawcy zasobów niestandardowych. |
   | Identyfikator dostawcy zasobów niestandardowych | Nie | Istniejący dostawca zasobów niestandardowych, który obsługuje zasób skojarzenia. Jeśli określisz wartość w tym miejscu, aplikacja logiki i wdrożenia dostawcy niestandardowego zostaną pominięte. |
   | Nazwa skojarzenia | Nie | Nazwa zasobu skojarzenia. |

   Parametry próbki:

   ![Wprowadzanie parametrów szablonu](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Przejdź do wdrożenia i poczekaj na jego zakończenie. Powinieneś zobaczyć coś takiego jak poniższy zrzut ekranu. Nowy zasób skojarzenia powinien być widoczny jako dane wyjściowe:

   ![Pomyślne wdrożenie](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Oto grupa zasobów z zaznaczoną wybraną wybraną wybraną opcję **Pokaż ukryte typy:**

   ![Wdrożenie dostawcy niestandardowego](media/tutorial-resource-onboarding/showhidden.png)

10. Eksploruj kartę Historia **uruchamiania logiki,** aby wyświetlić wywołania tworzenia skojarzenia:

    ![Aplikacja logika uruchamia historię](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Wdrażanie dodatkowych skojarzeń

Po skonfigurowaniu infrastruktury dostawcy niestandardowego można łatwo wdrożyć więcej skojarzeń. Grupa zasobów dla dodatkowych skojarzeń nie musi być taka sama jak grupa zasobów, w której wdrożono infrastrukturę dostawcy niestandardowego. Aby utworzyć skojarzenie, musisz mieć uprawnienia Microsoft.CustomProviders/resourceproviders/write na określonym identyfikatorze dostawcy zasobów niestandardowych.

1. Przejdź do niestandardowego dostawcy **Microsoft.CustomProviders/resourceProviders** zasobów w grupie zasobów poprzedniego wdrożenia. Musisz zaznaczyć pole wyboru **Pokaż ukryte typy:**

   ![Przejdź do zasobu](media/tutorial-resource-onboarding/showhidden.png)

2. Skopiuj właściwość Identyfikator zasobu dostawcy niestandardowego.

3. Wyszukaj **szablony** we **wszystkich usługach** lub korzystając z głównego pola wyszukiwania:

   ![Wyszukiwanie szablonów](media/tutorial-resource-onboarding/templates.png)

4. Wybierz poprzednio utworzony szablon, a następnie wybierz pozycję **Wdrażanie:**

   ![Wybierz poprzednio utworzony szablon, a następnie wybierz pozycję Wdrażanie](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Wprowadź ustawienia wymaganych pól, a następnie wybierz subskrypcję i inną grupę zasobów. W przypadku ustawienia **Identyfikator dostawcy zasobów niestandardowych** wprowadź identyfikator zasobu skopiowany z dostawcy niestandardowego, który został wdrożony wcześniej.

6. Przejdź do wdrożenia i poczekaj na jego zakończenie. Teraz należy wdrożyć tylko nowy zasób skojarzeń:

   ![Nowy zasób skojarzeń](media/tutorial-resource-onboarding/createdassociationresource.png)

Jeśli chcesz, możesz wrócić do aplikacji logiki **Uruchom historię** i zobaczyć, że inne wywołanie zostało wykonane do aplikacji logiki. Można zaktualizować aplikację logiki, aby rozszerzyć dodatkowe funkcje dla każdego utworzonego skojarzenia.

## <a name="getting-help"></a>Uzyskiwanie pomocy

Jeśli masz pytania dotyczące dostawców niestandardowych platformy Azure, spróbuj zadać je w [usłudze Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-custom-providers). Na podobne pytanie można było już odpowiedzieć, więc sprawdź najpierw przed opublikowaniem. Dodaj tag, `azure-custom-providers` aby uzyskać szybką odpowiedź!


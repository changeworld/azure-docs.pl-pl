---
title: Programowe tworzenie pulpitów nawigacyjnych Azure | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak programowo tworzenia pulpitów nawigacyjnych Azure.
services: azure-portal
documentationcenter: ''
author: adamab
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: adamab
ms.openlocfilehash: 8670d25e10b58c40b9d0807de1db88c3296b193d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="programmatically-create-azure-dashboards"></a>Programowe tworzenie pulpitów nawigacyjnych Azure

Ten dokument przeprowadzi Cię przez proces programowe tworzenie i publikowanie Azure pulpitów nawigacyjnych. Pulpit nawigacyjny, pokazano poniżej jest przywoływany w całym dokumencie.

![przykładowy pulpit nawigacyjny](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Przegląd

Udostępnionych pulpitów nawigacyjnych w Azure czy [zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) podobnie jak konta magazynu i maszyn wirtualnych.  W związku z tym będą one zarządzane programowo przy użyciu [interfejsów API REST usługi Azure Resource Manager](/rest/api/), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure), [poleceń programu PowerShell Azure](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.2.0)i wiele [ Azure portal](https://portal.azure.com) funkcje bazując na te interfejsy API, aby ułatwić zarządzanie zasobami.  

Każdy z tych interfejsów API i narzędzia oferuje sposoby tworzenia, wyświetlania list, pobrać, modyfikowanie i usuwanie zasobów.  Ponieważ pulpity nawigacyjne są zasoby, można wybrać ulubiony interfejsu API / narzędzia do użycia.

Niezależnie od wybranego narzędzia można użyć należy utworzyć reprezentacja JSON obiektu pulpitu nawigacyjnego przed wywołaniem interfejsu API tworzenia żadnych zasobów. Ten obiekt zawiera informacje o częściach () Kafelki) na pulpicie nawigacyjnym. Zawiera rozmiary, pozycje zasoby, które są powiązane i dostosowań wprowadzonych przez użytkownika.

Najbardziej praktycznym sposobem tworzenia tego dokumentu JSON jest użycie [portalu](https://portal.azure.com/) interakcyjnego dodawania i umieść swoje Kafelki. Następnie wyeksportować dane JSON. Na koniec Utwórz szablon z wyników do późniejszego użytku w skryptach, programów i narzędzia wdrażania.

## <a name="create-a-dashboard"></a>Utwórz pulpit nawigacyjny

Aby utworzyć nowy pulpit nawigacyjny, polecenie nowego pulpitu nawigacyjnego na ekranie głównym portalu.

![nowy pulpit nawigacyjny — polecenie](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Następnie można galerii kafelka Znajdź i Dodaj Kafelki. Kafelki zostaną dodane przez przeciąganie i upuszczanie je. Niektóre Kafelki obsługuje zmienianie rozmiaru za pomocą dojścia przeciągania, podczas gdy inne Obsługa naprawia rozmiary, które są widoczne w menu kontekstowego.

### <a name="drag-handle"></a>Przeciągnij uchwyt
![Przeciągnij uchwyt](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Stały rozmiar za pośrednictwem menu kontekstowe
![menu kontekstowe rozmiary](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Udostępnij pulpit nawigacyjny

Po skonfigurowaniu pulpitu nawigacyjnego do potrzeb, Twoje następne kroki są publikowanie pulpitu nawigacyjnego (przy użyciu polecenia udziału), a następnie użyć Eksploratora zasobów do pobrania w formacie JSON.

![polecenie udziału](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Polecenie udziału Pokazuje okno dialogowe z prośbą o wybierz grupę subskrypcji i zasobu do publikowania. Należy pamiętać, że [musi mieć dostęp do zapisu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) do wybranej grupy subskrypcji i zasobu.

![Udostępnianie i dostęp](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Pobierz reprezentacja JSON pulpitu nawigacyjnego

Publikowanie zajmuje tylko kilka sekund.  Po zakończeniu, następnym krokiem jest przejdź do [Eksploratora zasobów](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) do pobrania w formacie JSON.

![Przeglądaj w Eksploratorze zasobów](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

W Eksploratorze zasobów przejdź do wybranej grupy subskrypcji i zasobu. Następnie kliknij zasobu nowo opublikowana pulpitu nawigacyjnego, aby ujawnić JSON.

![Eksplorator zasobów w formacie json](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Utwórz szablon na podstawie JSON

Następnym krokiem jest utworzenie szablonu z tego formatu JSON, aby go mogą być ponownie używane programowe zarządzanie zasobami odpowiednich interfejsów API, narzędzia wiersza polecenia lub w portalu.

Nie jest konieczne w pełni zrozumieć strukturze JSON pulpitu nawigacyjnego, aby utworzyć szablon. W większości przypadków, dla których chcesz zachować struktury i konfiguracji każdego kafelka, a następnie parametryzacja zestawu zasobów platformy Azure, który jest wskazujące. Obejrzyj pulpit nawigacyjny wyeksportowanego JSON i Znajdź wszystkie wystąpienia identyfikatorów zasobów platformy Azure. Naszym przykładzie pulpit nawigacyjny zawiera Kafelki wielu wszystkie wskazywały jednej maszyny wirtualnej platformy Azure. Wynika to z naszych pulpitu nawigacyjnego przegląda tylko ten pojedynczy zasób. Jeśli Wyszukaj json próbki (dołączony na koniec dokumentu) "/ subskrypcji", możesz znaleźć kilka wystąpień tego identyfikatora.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Aby opublikować ten pulpit nawigacyjny dla żadnej maszyny wirtualnej należy w przyszłości parametryzacja każde wystąpienie tego ciągu w formacie JSON. 

Istnieją dwa odmian interfejsów API, które tworzenie zasobów na platformie Azure. [Konieczne interfejsów API](https://docs.microsoft.com/rest/api/resources/resources) w czasie, aby tworzenia jeden zasób i [wdrożenia na podstawie szablonu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) system, który można organizować tworzenie wielu zależnych zasobów z jednego wywołania interfejsu API. Drugie natywnie obsługuje parametryzacja i tworzenia szablonów tak używamy w naszym przykładzie.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programowe tworzenie pulpitu nawigacyjnego z szablonu, za pomocą wdrażania szablonu

System Azure oferuje możliwość organizowania wdrożenie wielu zasobów. Tworzenie szablonu wdrażania, określającym zestaw zasobów w celu wdrożenia oraz relacje między nimi.  Formatu JSON każdego zasobu jest taka sama, jak gdyby zostały one tworzone po kolei. Różnica jest to, że [język szablonu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) dodaje kilka pojęć, takie jak zmienne, parametry, funkcje podstawowe i. Rozszerzenia składni jest obsługiwane tylko w kontekście wdrażania szablonu i nie działa, jeśli jest używany z interfejsami API imperatywnych wcześniejszym opisem.

Jeśli chcesz zacząć tej trasy, a następnie parametryzacja ma się odbywać za pomocą składni parametru szablonu.  Musisz zastąpić wszystkie wystąpienia identyfikatora zasobu, który znajduje się wcześniej w sposób pokazany poniżej.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Przykład właściwości JSON z wpisaną na stałe identyfikator zasobu
`id: “/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1”`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Przykład właściwości JSON przekonwertowana na sparametryzowane na podstawie parametrów szablonu

`id: "[resourceId(parameters('virtualMachineResourceGroup'), ‘Microsoft.Compute/virtualMachines’, parameters('virtualMachineName'))]"`

Należy zadeklarować niektóre metadane wymagany szablon oraz parametrów w górnej części szablonu json w następujący sposób:

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__Pełne, szablon pracy na końcu niniejszego dokumentu jest widoczny.__

Gdy mają co szablonu można wdrożyć za pomocą [interfejsów API REST](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/group/deployment#az_group_deployment_create), lub [strony wdrażania szablonu portalu ](https://portal.azure.com/#create/Microsoft.Template).

Poniżej przedstawiono są dwie wersje w naszym przykładzie pulpitu nawigacyjnego JSON. Pierwsza to wersja wyeksportowany z portalu, który został już powiązana z zasobem. Drugim jest wersji szablonu, który może być programowane powiązana z żadnej maszyny Wirtualnej i wdrażane za pomocą usługi Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Reprezentacja JSON w naszym przykładzie nawigacyjnego (przed tworzenia szablonów)

Jest to, czego można oczekiwać zobaczyć, czy zgodnie z instrukcjami wcześniejszych można pobrać reprezentacja JSON pulpitu nawigacyjnego, który jest już wdrożony. Należy pamiętać, identyfikatory ustalony zasobów, które pokazują, że ten pulpit nawigacyjny jest wskazując określonej maszyny wirtualnej platformy Azure.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Reprezentacja szablonu naszym przykładzie pulpitu nawigacyjnego

Wersja szablonu pulpitu nawigacyjnego został zdefiniowany trzy parametry o nazwie __virtualMachineName__, __virtualMachineResourceGroup__, i __dashboardName__.  Parametry umożliwiają punkt ten pulpit nawigacyjny w innej maszyny wirtualnej platformy Azure, za każdym razem, gdy należy wdrożyć. Sparametryzowane identyfikatory są wyróżnione do wyświetlenia tego pulpitu nawigacyjnego można programowo skonfigurowany i wdrożony wskaż pozycję Wszystkie maszyny wirtualnej platformy Azure. Najprostszym sposobem do testowania tej funkcji jest następującego szablonu skopiuj i wklej ją do [strony wdrażania szablonu portalu Azure](https://portal.azure.com/#create/Microsoft.Template). 

Pulpit nawigacyjny w tym przykładzie jest wdrażana przez samego siebie, ale języka szablonu umożliwia wdrożenie wielu zasobów i zawierać co najmniej jeden pulpity nawigacyjne wzdłuż krawędzi je. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
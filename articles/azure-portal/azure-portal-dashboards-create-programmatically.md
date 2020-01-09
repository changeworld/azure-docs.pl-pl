---
title: Programowe tworzenie pulpitów nawigacyjnych platformy Azure | Microsoft Docs
description: Możesz użyć pulpitu nawigacyjnego w Azure Portal jako szablonu do programistycznego tworzenia pulpitów nawigacyjnych platformy Azure. Zawiera odwołanie JSON.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: mblythe
ms.openlocfilehash: 498e0255cfa289f7d8ccb93040980c362cf510a0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640350"
---
# <a name="programmatically-create-azure-dashboards"></a>Programowe tworzenie pulpitów nawigacyjnych platformy Azure

Ten dokument przedstawia proces programistycznego tworzenia i publikowania pulpitów nawigacyjnych platformy Azure. Na pulpicie nawigacyjnym przedstawionym poniżej znajduje się odwołanie do dokumentu.

![przykładowy pulpit nawigacyjny](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Przegląd

Udostępnione pulpity nawigacyjne na platformie Azure to [zasoby](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) podobne do maszyn wirtualnych i kont magazynu.  Z tego względu można nimi zarządzać programowo za pośrednictwem [Azure Resource Manager interfejsów API REST](/rest/api/), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure), [poleceń Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)i wielu funkcji [Azure Portal](https://portal.azure.com) na podstawie tych interfejsów API, aby ułatwić zarządzanie zasobami.  

Każdy z tych interfejsów API i narzędzi oferuje sposoby tworzenia, wyświetlania, pobierania, modyfikowania i usuwania zasobów.  Ponieważ pulpity nawigacyjne są zasobami, możesz wybrać ulubiony interfejs API/narzędzie do użycia.

Niezależnie od tego, jakiego narzędzia używasz, musisz utworzyć reprezentację JSON obiektu pulpitu nawigacyjnego, aby można było wywołać dowolny interfejs API tworzenia zasobów. Ten obiekt zawiera informacje o częściach (vel kafelki) na pulpicie nawigacyjnym. Obejmuje rozmiary, pozycje, zasoby, z którymi są one powiązane, oraz dowolnych dostosowań użytkowników.

Najbardziej praktycznym sposobem tworzenia tego dokumentu JSON jest użycie [portalu](https://portal.azure.com/) do interaktywnego dodania i położenia kafelków. Następnie wyeksportuj kod JSON. Na koniec utworzysz szablon na podstawie wyniku do późniejszego użycia w skryptach, programach i narzędziach wdrażania.

## <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Aby utworzyć nowy pulpit nawigacyjny, Użyj nowego pulpitu nawigacyjnego na głównym ekranie portalu.

![nowe polecenie pulpitu nawigacyjnego](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Za pomocą Galerii kafelków możesz znajdować i dodawać kafelki. Kafelki są dodawane przez przeciąganie i upuszczanie. Niektóre kafelki obsługują zmianę rozmiaru przy użyciu uchwytu przeciągania, podczas gdy inne obsługują poprawki rozmiarów, które mogą być widoczne w ich menu kontekstowym.

### <a name="drag-handle"></a>Przeciągnij uchwyt
![Przeciągnij uchwyt](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Stałe rozmiary za pośrednictwem menu kontekstowego
![menu kontekstowe rozmiarów](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Udostępnianie pulpitu nawigacyjnego

Po skonfigurowaniu pulpitu nawigacyjnego do własnych potrzeb następnym etapem będzie opublikowanie pulpitu nawigacyjnego (przy użyciu polecenia Udostępnij), a następnie użycie Eksploratora zasobów do pobrania danych JSON.

![polecenie udostępniania](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Kliknięcie polecenia Udostępnij powoduje wyświetlenie okna dialogowego z monitem o wybranie subskrypcji i grupy zasobów, w których ma zostać opublikowana. Pamiętaj, że [musisz mieć dostęp do zapisu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) do wybranej subskrypcji i grupy zasobów.

![Udostępnianie i dostęp](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Pobierz reprezentację w formacie JSON pulpitu nawigacyjnego

Publikowanie trwa tylko kilka sekund.  Gdy skończysz, następnym krokiem jest przejście do [Eksplorator zasobów](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) , aby pobrać kod JSON.

![Przeglądaj Eksploratora zasobów](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

W Eksploratorze zasobów przejdź do wybranej subskrypcji i grupy zasobów. Następnie kliknij nowo opublikowany zasób pulpitu nawigacyjnego, aby wyświetlić kod JSON.

![plik JSON Eksploratora zasobów](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Tworzenie szablonu na podstawie kodu JSON

Następnym krokiem jest utworzenie szablonu na podstawie tego kodu JSON, aby można go było użyć programowo przy użyciu odpowiednich interfejsów API zarządzania zasobami, narzędzi wiersza polecenia lub w portalu.

Nie jest konieczne pełne zapoznanie się ze strukturą JSON pulpitu nawigacyjnego w celu utworzenia szablonu. W większości przypadków chcesz zachować strukturę i konfigurację każdego kafelka, a następnie Sparametryzuj zestaw zasobów platformy Azure, do których się wskazują. Zapoznaj się z wyeksportowanym pulpitem nawigacyjnym JSON i Znajdź wszystkie wystąpienia identyfikatorów zasobów platformy Azure. Nasz przykładowy pulpit nawigacyjny zawiera wiele kafelków, które są wskazywane przez pojedynczą maszynę wirtualną platformy Azure. Dzieje się tak, ponieważ nasz pulpit nawigacyjny przegląda tylko ten pojedynczy zasób. Jeśli przeszukasz przykładowy kod JSON (uwzględniony na końcu dokumentu) dla elementu "/subscriptions", znajdziesz kilka wystąpień tego identyfikatora.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Aby opublikować ten pulpit nawigacyjny dla dowolnej maszyny wirtualnej w przyszłości, musisz Sparametryzuj każde wystąpienie tego ciągu w formacie JSON. 

Istnieją dwa rodzaje interfejsów API, które tworzą zasoby na platformie Azure. Bezwzględne [interfejsy API](https://docs.microsoft.com/rest/api/resources/resources) , które tworzą jeden zasób w danym momencie, i system [wdrażania oparty na szablonach](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) , który może organizować tworzenie wielu zasobów zależnych za pomocą jednego wywołania interfejsu API. Ta druga natywnie obsługuje parametryzacja i tworzenia szablonów, dlatego używamy jej w naszym przykładzie.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programowe tworzenie pulpitu nawigacyjnego na podstawie szablonu przy użyciu wdrożenia szablonu

Platforma Azure oferuje możliwość organizowania wdrożenia wielu zasobów. Tworzysz szablon wdrożenia, który wyraża zestaw zasobów do wdrożenia, a także relacje między nimi.  Format JSON każdego zasobu jest taki sam, jak w przypadku ich tworzenia przez jeden. Różnica polega na tym, że [język szablonu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) dodaje kilka koncepcji, takich jak zmienne, parametry, podstawowe funkcje i wiele innych. Ta rozszerzona składnia jest obsługiwana tylko w kontekście wdrożenia szablonu i nie działa, jeśli jest używana z bezwzględnymi interfejsami API opisanymi wcześniej.

W przypadku przechodzenia do tej trasy parametryzacja należy wykonać przy użyciu składni parametru szablonu.  Zastąp wszystkie wystąpienia identyfikatora zasobu znalezione wcześniej, jak pokazano poniżej.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Przykładowa Właściwość JSON z zakodowanym identyfikatorem zasobu
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Przykładowa Właściwość JSON konwertowana do wersji sparametryzowanej na podstawie parametrów szablonu

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Konieczne jest również zadeklarowanie niektórych wymaganych metadanych szablonu i parametrów w górnej części szablonu JSON w następujący sposób:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

__Pełny, roboczy szablon można zobaczyć na końcu tego dokumentu.__

Po przywróceniu szablonu można wdrożyć go przy użyciu [interfejsów API REST](https://docs.microsoft.com/rest/api/resources/deployments), [programu PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create)lub [strony wdrożenia szablonu portalu](https://portal.azure.com/#create/Microsoft.Template).

Poniżej przedstawiono dwie wersje naszego przykładowego kodu JSON pulpitu nawigacyjnego. Pierwsza to wersja wyeksportowana z portalu, która została już powiązana z zasobem. Druga to wersja szablonu, którą można programowo powiązać z dowolną maszyną wirtualną i wdrożona przy użyciu Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Reprezentacja kodu JSON naszego przykładowego pulpitu nawigacyjnego (przed tworzenia szablonów)

Jest to oczekiwane działanie, które można zobaczyć, jeśli wykonasz wcześniejsze instrukcje, aby pobrać reprezentację JSON już wdrożonego pulpitu nawigacyjnego. Zanotuj zakodowane identyfikatory zasobów, które pokazują, że ten pulpit nawigacyjny wskazuje na określoną maszynę wirtualną platformy Azure.

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

### <a name="template-representation-of-our-example-dashboard"></a>Reprezentacja szablonu naszego przykładowego pulpitu nawigacyjnego

Wersja szablonu pulpitu nawigacyjnego została zdefiniowana trzy parametry o nazwie __virtualMachineName__, __virtualMachineResourceGroup__i __dashboardname__.  Parametry pozwalają wskazać ten pulpit nawigacyjny na innej maszynie wirtualnej platformy Azure przy każdym wdrożeniu. Identyfikatory sparametryzowane są wyróżnione, aby pokazać, że ten pulpit nawigacyjny można skonfigurować i wdrożyć w taki sposób, aby wskazywał dowolną maszynę wirtualną platformy Azure. Najprostszym sposobem przetestowania tej funkcji jest skopiowanie następującego szablonu i wklejenie go do [strony wdrożenia szablonu Azure Portal](https://portal.azure.com/#create/Microsoft.Template). 

W tym przykładzie jest wdrażany pulpit nawigacyjny, ale język szablonu umożliwia wdrożenie wielu zasobów i nawiązuje się do nich jeden lub więcej pulpitów nawigacyjnych. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

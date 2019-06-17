---
title: Programowe tworzenie pulpitów nawigacyjnych platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak programowo utworzyć pulpitów nawigacyjnych platformy Azure.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: kfollis
ms.openlocfilehash: b24a0397a1365479907fedc6348caa54508dbbb0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60552202"
---
# <a name="programmatically-create-azure-dashboards"></a>Programowe tworzenie pulpitów nawigacyjnych platformy Azure

W tym dokumencie przedstawiono proces programowe tworzenie i publikowanie pulpitów nawigacyjnych platformy Azure. Pulpit nawigacyjny poniżej jest przywoływany w dokumencie.

![przykładowy pulpit nawigacyjny](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Omówienie

Udostępnione pulpity nawigacyjne w usłudze Azure czy [zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) tak jak maszyny wirtualne i konta magazynu.  W związku z tym, mogą być zarządzane programowo za pośrednictwem [interfejsów API REST usługi Azure Resource Manager](/rest/api/), [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure), [poleceń programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)i wiele [ Witryna Azure portal](https://portal.azure.com) funkcje są oparte na tych interfejsów API, aby ułatwić zarządzanie zasobami.  

Każda z tych narzędzi i interfejsów API oferuje sposoby tworzenia, wyświetlania list, pobieranie, modyfikowanie i usuwanie zasobów.  Ponieważ pulpity nawigacyjne są zasoby, należy wybrać ulubionego interfejsu API / narzędzia do użycia.

Niezależnie od wykorzystywanego narzędzia należy utworzyć JSON reprezentacja obiektu pulpitu nawigacyjnego, przed wywołaniem dowolnej tworzenie zasobu interfejsu API. Ten obiekt zawiera informacje o części (zwane) Kafelki) na pulpicie nawigacyjnym. Obejmuje rozmiarów, pozycje, zasoby, które są powiązane i dostosowań wprowadzonych przez użytkownika.

Najbardziej praktycznym sposobem utworzenia tego dokumentu JSON jest użycie [portalu](https://portal.azure.com/) interakcyjnego dodawania i umieść Kafelki na pulpicie. Następnie wyeksportować za pomocą pliku JSON. Na koniec Utwórz szablon z wyniku do późniejszego użycia w skryptach, programów i narzędzi wdrażania.

## <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Aby utworzyć nowy pulpit nawigacyjny, polecenie nowy pulpit nawigacyjny na ekranie głównym portalu firmy.

![nowy pulpit nawigacyjny — polecenie](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Następnie można użyć Galeria kafelków, aby odnaleźć i dodać Kafelki. Kafelki są dodawane, przeciągając i upuszczając je. Niektóre Kafelki obsługuje zmienianie rozmiaru za pomocą uchwytu przeciągania, podczas gdy inne, pomocy technicznej naprawia rozmiarach, między które można przeglądać w ich menu kontekstowe.

### <a name="drag-handle"></a>Przeciągnij uchwyt
![Przeciągnij uchwyt](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Stałych rozmiarach przy użyciu menu kontekstowego
![menu kontekstowe rozmiarów](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Udostępnij pulpit nawigacyjny

Po skonfigurowaniu pulpitu nawigacyjnego do swoich potrzeb, na które potrzebne są kolejne kroki publikowanie pulpitu nawigacyjnego (za pomocą polecenia udziału), a następnie użyć Eksploratora zasobów można pobrać za pomocą pliku JSON.

![polecenie Udostępnij](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Kliknięcie zostanie wybrane polecenie współdzielenia powoduje wyświetlenie okna dialogowego, która poprosi o wybranie które subskrypcję i grupę zasobów do publikowania. Należy pamiętać, że [musi mieć dostęp do zapisu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) do subskrypcji i grupie zasobów wybranego przez użytkownika.

![Udostępnianie i dostęp](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Pobrać reprezentacji JSON pulpitu nawigacyjnego

Publikowanie zajmuje tylko kilka sekund.  Gdy wszystko będzie gotowe, następnym krokiem jest przejście do [Eksploratora zasobów](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) można pobrać za pomocą pliku JSON.

![Przeglądaj Eksploratora zasobów](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

W Eksploratorze zasobów przejdź do subskrypcji i grupie zasobów wybranej. Następnie kliknij przycisk zasobu nowo opublikowany pulpit nawigacyjny, aby wyświetlić kod JSON.

![Eksplorator zasobów w formacie json](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Tworzenie szablonu z kodu JSON

Następnym krokiem jest, aby utworzyć szablon z tego formatu JSON, dzięki czemu może zostać użyte programowo przy użyciu zarządzania zasobami odpowiednie interfejsy API, narzędzi wiersza polecenia lub w portalu.

Nie jest to konieczne w pełni zrozumieć strukturze JSON pulpitu nawigacyjnego, aby utworzyć szablon. W większości przypadków, dla których chcesz zachować struktury i konfiguracji każdego kafelka, a następnie parametryzacja zestawu zasobów platformy Azure, które one wskazuje. Spójrz na wyeksportowanej pulpitu nawigacyjnego JSON i znaleźć wszystkie wystąpienia identyfikatorów zasobów platformy Azure. Nasz przykładowy pulpit nawigacyjny zawiera Kafelki wiele wskazujące na jednej maszynie wirtualnej platformy Azure. Wynika to z pulpitu nawigacyjnego przegląda tylko ten pojedynczy zasób. Jeśli wyszukasz json próbka (w pakiecie na koniec dokumentu) "/ subskrypcje", możesz znaleźć kilka wystąpień tego identyfikatora.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Aby opublikować ten pulpit nawigacyjny dla dowolnej maszyny wirtualnej w przyszłości należy próby parametryzacji każde wystąpienie tego ciągu w formacie JSON. 

Istnieją dwa odmian interfejsów API, które tworzenie zasobów na platformie Azure. [Imperatywne interfejsów API](https://docs.microsoft.com/rest/api/resources/resources) , utworzyć jeden zasób w czasie i [wdrożenia oparte na szablonach](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) systemu, które można organizować tworzenia wielu zależnych zasobów z jednego wywołania interfejsu API. Te ostatnie natywnie obsługuje tworzenie szablonów i parametryzacja więc w naszym przykładzie używamy go.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programowe tworzenie pulpitu nawigacyjnego z szablonu przy użyciu wdrożenia szablonu

Platforma Azure oferuje możliwość organizować wdrożenia w wielu zasobów. Możesz utworzyć szablon wdrożenia, który określa zestaw zasobów, aby wdrożyć oraz relacje między nimi.  Format JSON poszczególnych zasobów jest taka sama, tak, jakby były tworzenia je jedno po drugim. Różnica jest to, że [język szablonu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) dodaje kilka pojęć, takich jak zmiennych, parametrów, podstawowe funkcje i. Rozszerzonej składni jest obsługiwane tylko w kontekście wdrażania szablonu, a nie działa, jeśli używana z interfejsami API imperatywne omówionych powyżej.

Jeśli zamierzasz tę trasę, a następnie parametryzacji powinno się odbywać za pomocą składni parametr szablonu.  Można zastąpić wszystkie wystąpienia identyfikatora zasobu, które znaleźliśmy wcześniej, jak pokazano poniżej.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Przykład właściwości JSON z ustaloną identyfikator zasobu
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Właściwość JSON przykład przekonwertować wersję sparametryzowane, na podstawie parametrów szablonu

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Należy również zadeklarować niektóre metadane wymagany szablon i parametry w górnej części szablonu json następująco:

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

__Możesz zobaczyć pełny szablon pracy na końcu tego dokumentu.__

Mieć specjalnie do szablonu w celu wdrożenia go za pomocą [interfejsów API REST](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create), lub [strony wdrażania szablonu portalu ](https://portal.azure.com/#create/Microsoft.Template).

Poniżej przedstawiono dwie wersje nasz przykładowy pulpit nawigacyjny JSON. Pierwsza to wersja wyeksportowany z portalu, który został już powiązana z zasobem. Drugim jest wersja szablonu, który może być programowo powiązana z dowolnej maszyny Wirtualnej i wdrażane za pomocą usługi Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Reprezentacja JSON nasz przykładowy pulpit nawigacyjny (przed szablonów)

Jest to, czego mogą oczekiwać zobaczyć, jeśli wykonaj wcześniejsze instrukcje, aby pobrać reprezentacji JSON pulpit nawigacyjny, który jest już wdrożony. Należy pamiętać, identyfikatory ustalonych zasobów, które pokazują, że ten pulpit nawigacyjny wskazywanym określonej maszyny wirtualnej platformy Azure.

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

### <a name="template-representation-of-our-example-dashboard"></a>Reprezentacja szablonu nasz przykładowy pulpit nawigacyjny

Wersja szablonu pulpitu nawigacyjnego został zdefiniowany trzech parametrów o nazwie __virtualMachineName__, __virtualMachineResourceGroup__, i __dashboardName__.  Parametry umożliwiają za każdym razem, gdy wdrożysz wskazać ten pulpit nawigacyjny na innej maszynie wirtualnej platformy Azure. Sparametryzowany identyfikatory są wyróżnione do wyświetlenia tego pulpitu nawigacyjnego można programowo skonfigurowany i wdrożony wskaż dowolnej maszyny wirtualnej platformy Azure. Najprostszym sposobem do testowania tej funkcji jest następującego szablonu skopiuj i wklej go w [strony wdrażania szablonu witryny Azure portal](https://portal.azure.com/#create/Microsoft.Template). 

Pulpit nawigacyjny w tym przykładzie jest wdrażana przez siebie, ale języka szablon pozwala wdrożyć wiele zasobów, a następnie powiązać przynajmniej jeden pulpit nawigacyjny po stronie je. 

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

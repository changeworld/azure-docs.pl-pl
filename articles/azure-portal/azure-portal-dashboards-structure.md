---
title: Struktura pulpitów nawigacyjnych Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano strukturze JSON pulpitu nawigacyjnego platformy Azure
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
ms.author: adamab
ms.openlocfilehash: 2eb9289957968db04b78087413fb9df8ed1b085b
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301676"
---
# <a name="the-structure-of-azure-dashboards"></a>Struktura Azure pulpitów nawigacyjnych
Ten dokument zawiera opis za pośrednictwem struktury Azure pulpitu nawigacyjnego, na przykład przy użyciu następujących pulpitu nawigacyjnego:

![przykładowy pulpit nawigacyjny](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Ponieważ udostępnione [Azure pulpity nawigacyjne są zasoby](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), ten pulpit nawigacyjny może być reprezentowany w formacie JSON.  Następujące JSON reprezentuje pulpitu nawigacyjnego wizualizowane powyżej.

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
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
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

## <a name="common-resource-properties"></a>Wspólne właściwości zasobów

Umożliwia podział odpowiednich sekcji JSON.  Właściwości najwyższego poziomu, __identyfikator__, __nazwa__, __typu__, __lokalizacji__, i __tagi__ właściwości współużytkowana przez wszystkie typy zasobów platformy Azure. Oznacza to, że nie ma wiele wspólnego z poziomu pulpitu nawigacyjnego zawartości.

### <a name="the-id-property"></a>Właściwość identyfikatora

Identyfikator zasobów platformy Azure podlegają [konwencje zasobów Azure nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Jeśli portal tworzy pulpit nawigacyjny zazwyczaj wybiera id w postaci identyfikatora guid, ale jest korzystać wszystkie prawidłowej nazwy podczas tworzenia ich programowo. 

### <a name="the-name-property"></a>Właściwość name
Nazwa jest segmentem zasobów identyfikator, który nie ma subskrypcji, typ zasobu lub informacji o grupie zasobów. To zasadniczo ostatni segment identyfikatora zasobu.

### <a name="the-type-property"></a>Właściwości typu
Wszystkie pulpity nawigacyjne są typu __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>Właściwość lokalizacji
W przeciwieństwie do innych zasobów pulpitów nawigacyjnych nie ma składnika środowiska wykonawczego.  Pulpity nawigacyjne lokalizacja wskazuje głównej lokalizacji geograficznej, przechowujący reprezentacja JSON w pulpicie nawigacyjnym. Wartość powinna być jeden z kodów lokalizacji, które mogą być pobierane przy użyciu [lokalizacje interfejsu API w zasobie subskrypcje](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Właściwość znaczników
Tagi to typową funkcją zasobów platformy Azure, umożliwiające organizowanie przez par wartości nazw dowolnego zasobu. Dla pulpitów nawigacyjnych, istnieje jeden specjalny tag o nazwie __ukryte tytuł__. Jeśli ta właściwość wypełnione pulpitu nawigacyjnego, następnie jest używany jako nazwa wyświetlana dla pulpitu nawigacyjnego w portalu. Nie można zmienić nazwy identyfikatorów zasobów platformy Azure, ale można tagów. Ten tag udostępnia sposób mieć nazwę wyświetlaną renamable pulpitu nawigacyjnego.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Obiekt właściwości
Obiekt właściwości zawiera dwie właściwości __obiektywów__ i __metadanych__. __Obiektywów__ właściwość zawiera informacje o Kafelki () części) na pulpicie nawigacyjnym.  __Metadanych__ właściwość ma potencjalnych przyszłych funkcji.

### <a name="the-lenses-property"></a>Właściwość obiektywów
__Obiektywów__ właściwość zawiera pulpitu nawigacyjnego. Należy zauważyć, że obiektywów obiektu w tym przykładzie zawiera jedną właściwość o nazwie "0". Obiektywów to pojęcie grupowania, która nie jest obecnie zaimplementowana w pulpitach nawigacyjnych. Obecnie wszystkie pulpity nawigacyjne mają ten jednej właściwości w obiekcie obiektyw ponownie, o nazwie "0".

### <a name="the-lens-object"></a>Obiekt obiektyw
Obiekt poniżej "0" zawiera dwie właściwości __kolejności__ i __części__.  W bieżącej wersji pulpity nawigacyjne __kolejności__ jest zawsze 0. __Części__ właściwość zawiera obiekt, który definiuje poszczególnych części () Kafelki) na pulpicie nawigacyjnym.

__Części__ obiekt zawiera właściwość dla każdej części, których nazwa właściwości jest liczbą. Ta liczba nie ma znaczenia. 

### <a name="the-part-object"></a>Część obiektu
Każdy obiekt poszczególnych części ma __pozycji__, i __metadanych__.

### <a name="the-position-object"></a>Obiekt pozycji
__Pozycji__ właściwość zawiera informacje rozmiar i położenie części wyrażone jako __x__, __y__, __rowSpan__i __colSpan__. Wartości są pod względem jednostki siatki. Te jednostki siatki są widoczne, gdy pulpit nawigacyjny jest w trybie Dostosuj, jak pokazano poniżej. Chcąc Kafelek, aby mieć szerokość dwóch jednostek siatki, wysokość jednostki jeden siatki i lokalizację w górnym lewym rogu pulpitu nawigacyjnego, a następnie obejct pozycji wygląda następująco:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![jednostek siatki](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Obiekt metadanych
Każda część ma właściwość metadanych, obiekt jest tylko jedna wymagana właściwość o nazwie __typu__. Ten ciąg informuje portalu kafelka, które można wyświetlić. Naszym przykładzie pulpit nawigacyjny korzysta z tych typów Kafelki:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` — Używany do wyświetlania monitorowania metryk
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` — Używany do wyświetlania tekstu lub obrazów za pomocą podstawowe formatowanie list, łącza, itd.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` — Umożliwia wyświetlenie filmy wideo z serwisu YouTube, Channel9 i inne rodzaje wideo, która działa w tagu html wideo.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` — Umożliwia zawierają nazwę i stan maszyny wirtualnej platformy Azure.

Każdy typ części ma własną konfigurację. Właściwości konfiguracji możliwe są nazywane __dane wejściowe__, __ustawienia__, i __zasobów__. 

### <a name="the-inputs-object"></a>Obiekt danych wejściowych
Obiekt danych wejściowych zwykle zawiera informacje, które wiąże kafelka wystąpienia zasobu.  Część maszyny wirtualnej w naszym przykładowy pulpit nawigacyjny zawiera pojedynczy danych wejściowych, który używa identyfikatora zasobów platformy Azure do wyrażenia powiązania.  Ten format identyfikator zasobu jest spójny we wszystkich zasobów systemu Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Wykres metryki ma jeden danych wejściowych określającym zasobów do powiązania, a także informacje o metric(s) będzie wyświetlany. Poniżej przedstawiono dane wejściowe dla kafelka, który jest widoczny w sieci w sieci do edycji i metryki.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
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
]

```

### <a name="the-settings-object"></a>Obiekt ustawień
Obiekt ustawień zawiera elementy można konfigurować części.  W naszym przykładowy pulpit nawigacyjny część Markdown używa ustawień do przechowywania zawartości, a także można skonfigurować tytuł i podtytuł języka znaczników markdown niestandardowych.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Podobnie wideo kafelka ma własnych ustawień, które zawierają wskaźnik do odtwarzania wideo, ustawienie Autoodtwarzanie i informacje o tytule opcjonalne.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Obiekt zasobów
Kafelki, powiązane z pierwszej klasy zarządzanych obiektów portalu (nazywane zasoby) ma tę relację wyrażona za pomocą obiektu trwałego.  W naszym przykładzie pulpitu nawigacyjnego kafelka maszyny wirtualnej zawiera opis tego zasobu.  __IdInputName__ właściwości informuje portalu, że identyfikator wejściowy zawiera unikatowy identyfikator dla elementu zawartości, w tym przypadku identyfikator zasobu. Typy zasobów najbardziej Azure mają zasoby zdefiniowane w portalu.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
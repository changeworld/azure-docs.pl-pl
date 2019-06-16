---
title: Struktura pulpitów nawigacyjnych platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano strukturę JSON pulpit nawigacyjny platformy Azure
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
ms.openlocfilehash: a7e9acbe78ffdca2e615873cc4c33f86b250a429
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551518"
---
# <a name="the-structure-of-azure-dashboards"></a>Struktura pulpitów nawigacyjnych platformy Azure
W tym dokumencie przedstawiono strukturę pulpitu nawigacyjnego platformy Azure, za pomocą następujących pulpitu nawigacyjnego, na przykład:

![przykładowy pulpit nawigacyjny](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Ponieważ udostępnione [pulpitów nawigacyjnych platformy Azure są zasoby](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), tego pulpitu nawigacyjnego mogą być reprezentowane w formacie JSON.  Następujące dane JSON reprezentuje pulpit nawigacyjny wizualizowane powyżej.

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

Umożliwia podział odpowiednich sekcji za pomocą pliku JSON.  Właściwości najwyższego poziomu, __identyfikator__, __nazwa__, __typu__, __lokalizacji__, i __tagi__ właściwości współużytkowane przez wszystkie typy zasobów platformy Azure. Oznacza to, że nie mają wiele do zrobienia z zawartością na pulpicie nawigacyjnym.

### <a name="the-id-property"></a>Właściwość id

Identyfikator zasobu platformy Azure podlegają [nazewnictwa konwencje zasobów platformy Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Gdy portalu tworzy pulpit nawigacyjny zazwyczaj wybiera identyfikator w postaci identyfikatora guid, ale możesz używać Dowolna prawidłowa nazwa, tworząc je programowo. 

### <a name="the-name-property"></a>Nazwa właściwości
Nazwa jest segmentem zasobu, identyfikator, który nie ma subskrypcji, typ zasobu lub informacje o grupie zasobów. Zasadniczo jest ostatnim segmencie identyfikatora zasobu.

### <a name="the-type-property"></a>Właściwość type
Wszystkie pulpity nawigacyjne są typu __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>Właściwość lokalizacji
W przeciwieństwie do innych zasobów pulpitów nawigacyjnych nie mają składnika środowiska wykonawczego.  W przypadku pulpitów nawigacyjnych lokalizacja wskazuje głównej lokalizacji geograficznej, która przechowuje reprezentacji JSON na pulpicie nawigacyjnym. Wartość powinna być jeden z kodów lokalizacji, które mogą być pobierane przy użyciu [lokalizacji interfejsu API w zasobie subskrypcje](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Właściwości znaczników
Tagi są wspólną cechą zasobów platformy Azure, które umożliwiają organizowanie zasobu przez pary dowolnego nazwa / wartość. Pulpity nawigacyjne, jest jeden specjalny znacznik o nazwie __ukryte tytuł__. Jeśli pulpit nawigacyjny ma właściwość ta jest wypełniana, następnie jest używany jako nazwa wyświetlana dla pulpitu nawigacyjnego w portalu. Nie można zmienić nazwy identyfikatorów zasobów platformy Azure, ale można tagów. Ten tag zapewnia sposób nazwę wyświetlaną renamable pulpitu nawigacyjnego.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Obiekt właściwości
Obiekt właściwości zawiera dwie właściwości __obiektywów__ i __metadanych__. __Obiektywów__ właściwość zawiera informacje o kafelkach (zwane) części) na pulpicie nawigacyjnym.  __Metadanych__ właściwość jest dostępna dla potencjalnych przyszłych funkcji.

### <a name="the-lenses-property"></a>Właściwość obiektywów
__Obiektywów__ właściwość zawiera dany pulpit nawigacyjny. Należy zauważyć, że obiektywów obiekt w tym przykładzie zawiera jedną właściwość o nazwie "0". Obiektywów to pojęcie grupowania, który nie jest obecnie wdrażany w pulpitach nawigacyjnych. Na razie wszystkie pulpity nawigacyjne mają tego pojedynczej właściwości obiektu obiektywu ponownie, o nazwie "0".

### <a name="the-lens-object"></a>Obiekt obiektywu
Obiekt pod konturem "0" zawiera dwie właściwości __kolejności__ i __części__.  W bieżącej wersji pulpity nawigacyjne __kolejności__ jest zawsze 0. __Części__ właściwość zawiera obiekt, który definiuje poszczególnych części (zwane) Kafelki) na pulpicie nawigacyjnym.

__Części__ obiekt zawiera właściwości dla każdej części, w którym nazwa właściwości jest liczbą. Ta liczba nie ma znaczenia. 

### <a name="the-part-object"></a>Część obiektu
Każdy obiekt poszczególnych części ma __pozycji__, i __metadanych__.

### <a name="the-position-object"></a>Obiekt położenia
__Pozycji__ właściwość zawiera informacje o rozmiar i położenie, dla części wyrażonej w postaci __x__, __y__, __rowSpan__i __colSpan__. Wartości są posługując się jednostkami siatki. Te jednostki siatki są widoczne, gdy pulpit nawigacyjny jest w trybie dostosowywanie, jak pokazano poniżej. Jeśli chcesz, aby Kafelek, aby mieć szerokość dwie jednostki i siatki, wysokość jednostkę jeden siatki i lokalizację w prawym górnym lewym rogu pulpitu nawigacyjnego, a następnie pozycji obiektu wygląda następująco:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![jednostek siatki](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Obiekt metadanych
Każda część ma właściwość metadanych, obiekt jest tylko jedna wymagana właściwość o nazwie __typu__. Ten ciąg informuje portalu który Kafelek, aby pokazać. Nasz przykładowy pulpit nawigacyjny korzysta z tych typów kafelków:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` — Używane do wyświetlania metryk monitorowania
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` — Używane do wyświetlania tekstu lub obrazów za pomocą podstawowe formatowanie list, łącza, itd.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` — Umożliwia wyświetlanie filmów wideo z serwisu YouTube, witryny Channel9 i inny rodzaj film wideo, który działa w wideo tagu HTML.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` — Służy do pokazywania nazwę i stan maszyny wirtualnej platformy Azure.

Każdy typ części ma własną konfigurację. Właściwości konfiguracji możliwe są nazywane __dane wejściowe__, __ustawienia__, i __zasobów__. 

### <a name="the-inputs-object"></a>Obiekt danych wejściowych
Obiekt danych wejściowych zawiera zazwyczaj informacji, która jest powiązywana kafelka z wystąpienia zasobu.  Część maszyn wirtualnych w naszym przykładowy pulpit nawigacyjny zawiera pojedynczy danych wejściowych, który używa identyfikatora zasobu platformy Azure do wyrażenia wiązania.  Ten format identyfikatora zasobu jest spójny w ramach wszystkich zasobów platformy Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Część wykresu metryki zawiera pojedynczy danych wejściowych, który wyraża zasobów do powiązania, a także informacje o metric(s) są wyświetlane. Poniżej przedstawiono dane wejściowe dla Kafelek, na którym są wyświetlane metryki przychodzący i wychodzący w sieci w poziomie.

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
Obiekt ustawień zawiera elementy można konfigurować części.  W naszym przykładowy pulpit nawigacyjny część języka znaczników Markdown korzysta z ustawień do przechowywania zawartości, a także można skonfigurować tytuł i podtytuł języka znaczników markdown niestandardowych.

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

Podobnie Kafelek wideo ma swoje własne ustawienia, które zawiera wskaźnik do odtworzenie filmu wideo, ustawienie Autoodtwarzanie i informacje o tytułach opcjonalne.

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

### <a name="the-asset-object"></a>Obiekt zasobu
Kafelki, które są powiązane z najwyższej klasy zarządzanych obiektów portalu (nazywane zasobów) mają tę relację, wyrażone za pomocą obiektu trwałego.  W nasz przykładowy pulpit nawigacyjny Kafelek maszyny wirtualnej zawiera opis tego zasobu.  __IdInputName__ właściwość zawiera informacje dla portalu, wprowadź identyfikator, zawiera unikatowy identyfikator elementu zawartości, w tym przypadku identyfikator zasobu. Najbardziej platformy Azure typy zasobów zawierają zasoby zdefiniowane w portalu.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`

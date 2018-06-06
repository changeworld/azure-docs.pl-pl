---
title: Azure Service Fabric interfejsu wiersza polecenia - sfctl chaos harmonogram | Dokumentacja firmy Microsoft
description: Informacje dotyczące poleceń harmonogram chaos sfctl interfejsu wiersza polecenia usługi sieci szkieletowej.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 0d09338f71d71d07ab0e037d4736cfaa1f3cff85
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764016"
---
# <a name="sfctl-chaos-schedule"></a>Harmonogram chaos sfctl
Pobieranie i ustawianie harmonogramu chaos.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Pobierz | Uzyskaj harmonogram Chaos definiujący, kiedy i jak uruchomić Chaos. |
| zestaw | Ustaw harmonogram Chaos mają być używane przez Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>Pobierz sfctl chaos harmonogramu
Uzyskaj harmonogram Chaos definiujący, kiedy i jak uruchomić Chaos.

Pobiera wersję harmonogram Chaos w użyciu i harmonogram Chaos, który definiuje kiedy i jak uruchomić Chaos.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-chaos-schedule-set"></a>Ustaw harmonogram chaos sfctl
Ustaw harmonogram Chaos mają być używane przez Chaos.

Ustaw harmonogram Chaos aktualnie używany przez Chaos. Chaos zostanie automatycznie zaplanować na podstawie harmonogramu Chaos działa. Wersja podana harmonogramu wejściowy musi odpowiadać wersji harmonogramu Chaos na serwerze. Jeśli wersja podana nie jest zgodna z wersją na serwerze, harmonogram Chaos nie zostanie zaktualizowany. Jeśli podana wersja zgodna z wersją na serwerze, harmonogram Chaos zostanie zaktualizowany, a wersja harmonogram Chaos na serwerze jest zwiększany zapasowej a zawija do 0 po 2 147 483 647. Jeśli Chaos działa, gdy to wywołanie, wywołanie zakończy się niepowodzeniem.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --chaos — parametry — słownik | Zakodowane JSON listy reprezentujący mapowania nazw ciąg ChaosParameters mają być używane przez zadania. |
| --wygaśnięcia data utc | Data i czas, kiedy przestać zgodnie z harmonogramem można zaplanować Chaos.  Domyślna\: 9999-12-31T23\:59\:59.999Z. |
| --zadania | Lista kodowany w formacie JSON ChaosScheduleJobs reprezentujący, kiedy uruchomić Chaos i z jakich parametrów do uruchomienia Chaos z. |
| --start Data utc | Data i czas ich uruchomienia, zgodnie z harmonogramem można zaplanować Chaos.  Domyślna\: 1601-01-01T00\:00\:00.000Z. |
| --wersji | Numer wersji harmonogramu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

### <a name="examples"></a>Przykłady

Polecenie ustawia harmonogram (przy założeniu, że bieżący harmonogram ma wersję 0), który rozpoczyna się 2016-01-01 i wygaśnie w dniu 2038-01-01, który uruchamia Chaos 24 godziny, dnia, 7 dni w tygodniu. Chaos zostanie zaplanowane w klastrze dla wybranego okresu.

    sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary 
    [  
    {  
        "Key":"adhoc",
        "Value":{  
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{  
                "NodeTypeInclusionList":[  
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{  
                "Map":{  
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{  
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs 
    [  
    {  
        "ChaosParameters":"adhoc",
        "Days":{  
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[  
            {  
                "StartTime":{  
                "Hour":0,
                "Minute":0
                },
                "EndTime":{  
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi sieci szkieletowej.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
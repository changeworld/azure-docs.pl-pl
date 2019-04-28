---
title: Platformy Azure z interfejsu wiersza polecenia usługi Service Fabric - sfctl chaos harmonogramu | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl chaos harmonogramu.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: dc3dd06b5feac1f66598cd65fa79f447a1bbd9be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837508"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Pobieranie i ustawianie harmonogramu chaos.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Pobierz | Pobierz harmonogram Chaos określające, kiedy i jak uruchamiać Chaos. |
| set | Ustaw harmonogram używany przez Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>Pobierz interfejs sfctl chaos harmonogramu
Pobierz harmonogram Chaos określające, kiedy i jak uruchamiać Chaos.

Pobiera wersję harmonogram Chaos w użyciu i Chaos harmonogram, który definiuje kiedy i jak uruchamiać Chaos.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-chaos-schedule-set"></a>Ustaw harmonogram chaos sfctl
Ustaw harmonogram używany przez Chaos.

Chaos automatycznie będą planować uruchomienia na podstawie harmonogramu Chaos. Wersja w podanych danych wejściowych harmonogramu musi odpowiadać wersji harmonogramu Chaos na serwerze. Jeśli udostępniona wersja nie jest zgodny z wersją na serwerze, harmonogram Chaos nie jest aktualizowana. Jeśli udostępniona wersja zgodna z wersją na serwerze, zaktualizowano harmonogram Chaos i wersję harmonogram Chaos na serwerze jest zwiększany się o jeden, a zawija na 0 po 2 147 483 647. Chaos działa po tym wywołaniu, wywołanie zakończy się niepowodzeniem.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --chaos-parameters-dictionary | Lista reprezentujący mapowania nazw parametrów w elemencie ChaosParameters, który będzie używany przez zadania zakodowane JSON. |
| --expiry-date-utc | Data i godzina, kiedy przestać korzystać z harmonogramu można zaplanować Chaos.  Domyślne\: 9999-12-31T23\:59\:59.999Z. |
| — zadania | Lista kodowany w formacie JSON ChaosScheduleJobs reprezentujący, kiedy uruchamiać chaosu, z jakich parametrów do uruchomienia Chaos przy użyciu. |
| --start-date-utc | Data i godzina, kiedy można uruchomić zgodnie z harmonogramem można zaplanować Chaos.  Domyślne\: 1601-01-01T00\:00\:00.000Z. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --version | Numer wersji harmonogramu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

### <a name="examples"></a>Przykłady

Następujące polecenie ustawia harmonogram (przy założeniu, że bieżący harmonogram ma wersję 0), który rozpoczyna się od 2016-01-01 i wygaśnie w dniu 2038-01-01, uruchomionym Chaos 24 godziny, dnia, 7 dni w tygodniu. Chaos zostanie zaplanowana w klastrze tego czasu.

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
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).

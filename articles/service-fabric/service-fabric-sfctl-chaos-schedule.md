---
title: Harmonogram chaosu sieci szkieletowej usługi Azure Azure
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do planowania chaosu.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 29b365c48e405830e238945f1d94156f477c15b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906190"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Pobierz i ustaw harmonogram chaosu.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| get | Pobierz Harmonogram Chaosu określający, kiedy i jak uruchomić Chaos. |
| set | Ustaw harmonogram używany przez Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos harmonogram dostać
Pobierz Harmonogram Chaosu określający, kiedy i jak uruchomić Chaos.

Pobiera wersję harmonogram chaosu w użyciu i Harmonogram chaosu, który określa, kiedy i jak uruchomić Chaos.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos harmonogram ustawiony
Ustaw harmonogram używany przez Chaos.

Chaos automatycznie zaplanuje przebiegi na podstawie harmonogramu chaosu. Harmonogram chaosu zostanie zaktualizowany, jeśli podana wersja jest zgodna z wersją na serwerze. Podczas aktualizowania harmonogramu chaosu wersja na serwerze jest zwiększana o 1. Wersja na serwerze zostanie zawijana z powrotem do 0 po osiągnięciu dużej liczby. Jeśli Chaos jest uruchomiony po wykonaniu tego wywołania, wywołanie zakończy się niepowodzeniem.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --chaos-parameters-dictionary | Lista zakodowana w UJson reprezentująca mapowanie nazw ciągów do chaosparametrów, które mają być używane przez jobs. |
| --expiry-date-utc | Data i godzina, kiedy przestać używać harmonogramu chaosu.  Domyślnie\: 9999-12-31T23\:\:59 59.999z. |
| --praca | JSON zakodowana lista ChaosScheduleJobs reprezentujących kiedy uruchomić Chaos i jakie parametry do uruchomienia Chaos z. |
| --start-date-utc | Data i godzina rozpoczęcia korzystania z harmonogramu chaosu za pomocą harmonogramu.  Wartość\: domyślna 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Wartość\: domyślna 60. |
| --wersja | Numer wersji harmonogramu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

### <a name="examples"></a>Przykłady

Następujące polecenie ustawia harmonogram (przy założeniu, że bieżący harmonogram ma wersję 0), który rozpoczyna się w 2016-01-01 i wygasa w dniu 2038-01-01, który uruchamia Chaos 24 godziny na dobę, 7 dni w tygodniu.
Chaos zostanie zaplanowany w klastrze na ten czas.
```
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
```



## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia sieci szkieletowej usług.
- Dowiedz się, jak używać interfejsu wiersza polecenia sieci szkieletowej usług przy użyciu [przykładowych skryptów.](/azure/service-fabric/scripts/sfctl-upgrade-application)

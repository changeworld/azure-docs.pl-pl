---
title: Interfejs wiersza polecenia platformy Azure Service Fabric sfctl chaos
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń dotyczących planowania chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 29b365c48e405830e238945f1d94156f477c15b4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906190"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Pobierz i Ustaw harmonogram chaos.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Pobierz | Pobierz harmonogram chaos definiujący czas i sposób uruchamiania chaos. |
| set | Ustaw harmonogram używany przez chaos. |

## <a name="sfctl-chaos-schedule-get"></a>Pobieranie harmonogramu sfctl chaos
Pobierz harmonogram chaos definiujący czas i sposób uruchamiania chaos.

Pobiera wersję harmonogramu chaos w użyciu oraz harmonogram chaos, który określa, kiedy i jak uruchomić chaos.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos, zestaw harmonogramu
Ustaw harmonogram używany przez chaos.

Chaos automatycznie planuje przebiegi zgodnie z harmonogramem chaos. Harmonogram chaos zostanie zaktualizowany, jeśli podana wersja jest zgodna z wersją na serwerze. Podczas aktualizowania harmonogramu chaos wersja na serwerze jest zwiększana o 1. Wersja na serwerze będzie zawijana z powrotem do 0 po osiągnięciu dużej liczby. Jeśli Chaos jest uruchomiona w trakcie tego wywołania, wywołanie zakończy się niepowodzeniem.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Chaos-Parameters-dictionary | Zakodowana lista JSON reprezentująca Mapowanie nazw ciągów do ChaosParameters do użycia przez zadania. |
| --expiry-date-utc | Data i godzina zatrzymania korzystania z harmonogramu w celu zaplanowania chaos.  Domyślne\: 9999-12-31T23\:59\:59.999 Z. |
| --zadania | Zakodowana w notacji JSON lista ChaosScheduleJobs reprezentująca, kiedy ma być uruchomiona chaos, i z parametrami, które mają być uruchamiane chaos z. |
| --start-date-utc | Data i godzina rozpoczęcia korzystania z harmonogramu w celu zaplanowania chaos.  Wartość domyślna\: 1601-01-01T00\:00\:00.000 Z. |
| --timeout-t | Domyślne\: 60. |
| --Version | Numer wersji harmonogramu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

### <a name="examples"></a>Przykłady

Następujące polecenie ustawia harmonogram (przy założeniu, że bieżący harmonogram ma wersję 0), która rozpoczyna się od 2016-01-01 i wygasa w dniu 2038-01-01, który działa chaos 24 godziny dnia, 7 dni w tygodniu.
W tym czasie chaos zostanie zaplanowana w klastrze.
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
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).

---
title: Interfejs wiersza polecenia platformy Azure Service Fabric sfctl chaos | Microsoft Docs
description: Opisuje polecenia harmonogramu poleceń sfctl chaos w usłudze Service Fabric.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 443a8186ac6e57360105e59e30f84db997cd2251
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72897539"
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
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
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
| --Data wygaśnięcia — czas UTC | Data i godzina zatrzymania korzystania z harmonogramu w celu zaplanowania chaos.  Domyślne\: 9999-12-31T23\:59\:59.999 Z. |
| --zadania | Zakodowana w notacji JSON lista ChaosScheduleJobs reprezentująca, kiedy ma być uruchomiona chaos, i z parametrami, które mają być uruchamiane chaos z. |
| --Data rozpoczęcia — UTC | Data i godzina rozpoczęcia korzystania z harmonogramu w celu zaplanowania chaos.  Wartość domyślna\: 1601-01-01T00\:00\:00.000 Z. |
| --timeout-t | Domyślne\: 60. |
| --Version | Numer wersji harmonogramu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

### <a name="examples"></a>Przykłady

Następujące polecenie ustawia harmonogram (przy założeniu, że bieżący harmonogram ma wersję 0), która rozpoczyna się od 2016-01-01 i wygasa w dniu 2038-01-01, który działa chaos 24 godziny dnia, 7 dni w tygodniu. W tym czasie chaos zostanie zaplanowana w klastrze.

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


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).

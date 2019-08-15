---
title: Interfejs wiersza polecenia platformy Azure Service Fabric sfctl chaos | Microsoft Docs
description: Opisuje polecenia harmonogramu poleceń sfctl chaos w usłudze Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 1664978110b7c700906cbf4e6c80806ac70f1f05
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036522"
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
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos, zestaw harmonogramu
Ustaw harmonogram używany przez chaos.

Chaos automatycznie planuje przebiegi zgodnie z harmonogramem chaos. Wersja podanego harmonogramu wejściowego musi być zgodna z wersją harmonogramu chaos na serwerze. Jeśli podana wersja nie jest zgodna z wersją na serwerze, harmonogram chaos nie zostanie zaktualizowany. Jeśli podana wersja jest zgodna z wersją na serwerze, zostanie zaktualizowany harmonogram chaos, a wersja harmonogramu chaos na serwerze jest zwiększana o jeden i zawija się z powrotem do 0 po 2 147 483 647. Jeśli Chaos jest uruchomiona w trakcie tego wywołania, wywołanie zakończy się niepowodzeniem.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Chaos-Parameters-dictionary | Zakodowana lista JSON reprezentująca Mapowanie nazw ciągów do ChaosParameters do użycia przez zadania. |
| --expiry-date-utc | Data i godzina zatrzymania korzystania z harmonogramu w celu zaplanowania chaos.  Domyślnie\: 9999-12-31T23\:59\:59.999 z. |
| --zadania | Zakodowana w notacji JSON lista ChaosScheduleJobs reprezentująca, kiedy ma być uruchomiona chaos, i z parametrami, które mają być uruchamiane chaos z. |
| --start-date-utc | Data i godzina rozpoczęcia korzystania z harmonogramu w celu zaplanowania chaos.  Domyślne\: 1601-01-01T00\:00\:00.000 z. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |
| --Version | Numer wersji harmonogramu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
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

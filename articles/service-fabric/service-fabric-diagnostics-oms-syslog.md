---
title: Monitoruj zdarzenia klastra systemu Linux w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować zdarzenia klastra systemu Linux z dziennika systemu
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 402e3dfe018c94ef068caf918b38aaad00064a49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118386"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Zdarzenia klastra usługi Service Fabric systemu Linux usługi SYSLOG

Usługa Service Fabric udostępnia zestaw zdarzeń platformy, które informują Cię o ważnych działań w klastrze. Pełną listę zdarzeń, które są dostępne, jest dostępna [tutaj](service-fabric-diagnostics-event-generation-operational.md). Istnieją różne sposoby, przez który te zdarzenia mogą być używane. W tym artykule użyjemy omówiono sposób konfigurowania usługi Service Fabric, aby zapisać te zdarzenia dziennika systemowego.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Wprowadzenie

W wersji 6,4 SyslogConsumer został wprowadzony do wysyłania zdarzenia platformy usługi Service Fabric z dziennikiem systemowym dla klastrów systemu Linux. Po włączeniu zdarzenia będą automatycznie przekazywane z dziennikiem systemowym, który może być zbierane i wysyłany przez agenta usługi Log Analytics.

Każde zdarzenie dziennika systemowego zawiera składniki 4
* Funkcji
* Tożsamość
* Message
* Severity

SyslogConsumer zapisuje wszystkie zdarzenia platformy za pomocą funkcji `Local0`. Możesz zaktualizować wszystkie prawidłowe funkcji, zmieniając config konfiguracji. Tożsamość używana jest `ServiceFabric`. Pole komunikatu zawiera całe zdarzenie serializacji w formacie JSON, dzięki czemu mogą zapytań i używane przez różne narzędzia. 

## <a name="enable-syslogconsumer"></a>Włącz SyslogConsumer

Aby włączyć SyslogConsumer, musisz wykonać uaktualnienie klastra. `fabricSettings` Sekcji musi zostać zaktualizowany z następującym kodem. Należy pamiętać, że ten kod po prostu zawiera sekcje dotyczące SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Oto zmiany w celu wyróżnienia
1. W sekcji wspólnej istnieje nowy parametr o nazwie `LinuxStructuredTracesEnabled`. **Jest to wymagane do zdarzenia systemu Linux ze strukturą i serializować, gdy wysyłane z dziennikiem systemowym.**
2. W sekcji Diagnostyka ConsumerInstance nowe: SyslogConsumer został dodany. Platforma to informuje, że ma innego konsumenta zdarzenia. 
3. Nowa sekcja SyslogConsumer musi mieć `IsEnabled` jako `true`. Jest on skonfigurowany do korzystania z Local0 automatycznie. Możesz przesłonić to przez dodanie innego parametru.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Usługa Azure Monitor rejestruje integracji
Może odczytywać te zdarzenia dziennika systemowego w narzędziu do monitorowania, takie jak dzienniki usługi Azure Monitor. Można utworzyć obszar roboczy usługi Log Analytics przy użyciu portalu Azure Marketplace, korzystając z tych [instrukcji]. (.. / azure-monitor/learn/quick-create-workspace.md) należy również dodać agenta usługi Log Analytics do klastra, aby zbierać i wysyłać te dane do obszaru roboczego. Jest to ten sam agent używany do zbierania liczników wydajności. 

1. Przejdź do `Advanced Settings` bloku

    ![Workspace Settings](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Kliknij pozycję `Data`.
3. Kliknij pozycję `Syslog`.
4. Skonfiguruj Local0 jako instrumentu do śledzenia. Możesz dodać innej funkcji, jeśli został zmieniony w element fabricSettings

    ![Konfigurowanie usługi Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Przejdź do Eksploratora zapytań, klikając pozycję `Logs` w menu zasobów obszaru roboczego, aby rozpocząć wykonywanie zapytań

    ![Dzienniki obszaru roboczego](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Aby wykonać zapytanie względem `Syslog` tabeli szukasz `ServiceFabric` jako parametr. Poniższe zapytanie znajduje się przykład jak Przeanalizuj dane JSON w zdarzeniu i wyświetlić jego zawartość

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Zapytanie usługi SYSLOG](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

W powyższym przykładzie jest to zdarzenie NodeDown. Można wyświetlić pełną listę zdarzeń [tutaj](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Kolejne kroki
* [Wdróż agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) na węzły do zbierania liczników wydajności i zbieranie statystyk platformy docker i dzienniki kontenerów
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../log-analytics/log-analytics-log-searches.md) dostępnymi w ramach dzienniki usługi Azure Monitor
* [Projektant widoków umożliwia tworzenie niestandardowych widoków w dziennikach w usłudze Azure Monitor](../log-analytics/log-analytics-view-designer.md)
* Dokumentacja dotycząca sposobu [usługi Azure Monitor rejestruje integracji z platformą Syslog](../log-analytics/log-analytics-data-sources-syslog.md).

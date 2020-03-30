---
title: Monitorowanie zdarzeń klastra systemu Linux w sieci szkieletowej usług Azure
description: Dowiedz się, jak monitorować zdarzenia klastra sieci szkieletowej sieci szkieletowej systemu Linux, pisząc zdarzenia platformy sieci szkieletowej usług w Syslogu.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645756"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Zdarzenia klastra sieci szkieletowej sieci szkieletowej systemu Linux w aplikacji Syslog

Sieci szkieletowej usług udostępnia zestaw zdarzeń platformy, aby poinformować o ważnej działalności w klastrze. Pełna lista zdarzeń, które są narażone jest dostępna [tutaj](service-fabric-diagnostics-event-generation-operational.md). Istnieje wiele sposobów, dzięki którym te zdarzenia mogą być spożywane. W tym artykule omówimy sposób konfigurowania sieci szkieletowej usług do zapisywania tych zdarzeń w Syslogu.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Wprowadzenie

W wersji 6.4, SyslogConsumer został wprowadzony do wysyłania zdarzeń platformy sieci szkieletowej usług do Syslog dla klastrów Linuksa. Po włączeniu zdarzenia będą automatycznie przepływać do Syslog, które mogą być zbierane i wysyłane przez agenta analizy dziennika.

Każde zdarzenie Syslog ma 4 składniki
* Obiektu
* Tożsamość
* Komunikat
* Ważność

SyslogConsumer pisze wszystkie zdarzenia platformy `Local0`za pomocą obiektu . Możesz zaktualizować do dowolnej ważnej funkcji, zmieniając config config. Używana tożsamość `ServiceFabric`to . Pole Wiadomość zawiera całe zdarzenie serializowane w JSON, dzięki czemu może być wyszukiwane i używane przez różne narzędzia. 

## <a name="enable-syslogconsumer"></a>Włącz SyslogConsumer

Aby włączyć SyslogConsumer, należy wykonać uaktualnienie klastra. Sekcja `fabricSettings` musi zostać zaktualizowana o następujący kod. Uwaga ten kod zawiera tylko sekcje związane z SyslogConsumer

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

Oto zmiany, które należy wywołać
1. W sekcji Wspólne znajduje się nowy `LinuxStructuredTracesEnabled`parametr o nazwie . **Jest to wymagane do zdarzenia Linux ustrukturyzowany i serializowane, gdy wysyłane do Syslog.**
2. W diagnostyce sekcji nowy ConsumerInstance: SyslogConsumer został dodany. To mówi platformie, że jest inny konsument zdarzeń. 
3. Nowa sekcja SyslogConsumer musi `IsEnabled` `true`mieć jako . Jest skonfigurowany do automatycznego korzystania z obiektu Local0. Można to zastąpić, dodając inny parametr.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integracja z usługą Azure Monitor
Te zdarzenia Syslog można odczytać w narzędziu do monitorowania, takim jak dzienniki usługi Azure Monitor. Obszar roboczy usługi Log Analytics można utworzyć przy użyciu portalu Azure Marketplace przy użyciu tych [instrukcje]. (.. /azure-monitor/learn/quick-create-workspace.md) Należy również dodać agenta usługi Log Analytics do klastra, aby zbierać i wysyłać te dane do obszaru roboczego. Jest to ten sam agent używany do zbierania liczników wydajności. 

1. Przejdź do `Advanced Settings` ostrza

    ![Ustawienia obszaru roboczego](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Kliknij pozycję `Data`.
3. Kliknij pozycję `Syslog`.
4. Skonfiguruj Local0 jako obiekt do śledzenia. Możesz dodać inny Obiekt, jeśli zmieniłeś go w tkaninachSettings

    ![Konfigurowanie sysloga](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Przejdź do Eksploratora `Logs` zapytań, klikając menu zasobu obszaru roboczego, aby rozpocząć wykonywanie zapytań

    ![Dzienniki obszaru roboczego](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Można kwerendy `Syslog` względem tabeli szuka `ServiceFabric` jako ProcessName. Poniższa kwerenda jest przykładem sposobu analizowania JSON w przypadku i wyświetlania jego zawartości

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Kwerenda Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

Powyższy przykład dotyczy zdarzenia NodeDown. Pełną listę wydarzeń można wyświetlić [tutaj](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) w węzłach w celu zbierania liczników wydajności i zbierania statystyk i dzienników docker dla kontenerów
* Zapoznaj się z funkcjami [wyszukiwania i wyszukiwania w dziennikach](../log-analytics/log-analytics-log-searches.md) oferowanych w ramach dzienników usługi Azure Monitor
* [Tworzenie widoków niestandardowych w dziennikach usługi Azure Monitor za pomocą projektanta widoków](../log-analytics/log-analytics-view-designer.md)
* Odwołanie do sposobu [rejestrowania integracji usługi Azure Monitor z programem Syslog](../log-analytics/log-analytics-data-sources-syslog.md).

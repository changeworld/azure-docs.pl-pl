---
title: Struktura dzienników usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Wymagane jest zapytanie dziennika, aby pobrać dane dzienników z usługi Azure Monitor.  W tym artykule opisano sposób nowy dziennik, zapytania są używane w usłudze Azure Monitor i zapewnia pojęcia, które należy zrozumieć przed utworzeniem jeden.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: e243ebbc31f9e941678ac76a83738276995b5ba1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297184"
---
# <a name="structure-of-azure-monitor-logs"></a>Struktura dzienników usługi Azure Monitor
Możliwość szybko uzyskać wgląd w dane przy użyciu [zapytanie dziennika](log-query-overview.md) jest zaawansowaną funkcją usługi Azure Monitor. Do tworzenia zapytań wydajne i przydatne, należy zrozumieć kilka podstawowych pojęć, takich jak lokalizacji potrzebnych danych oraz jego struktury. Ten artykuł zawiera podstawowe pojęcia, że chcesz rozpocząć pracę.

## <a name="overview"></a>Omówienie
Dane w dziennikach monitora platformy Azure są przechowywane w obszar roboczy usługi Log Analytics lub aplikacji usługa Application Insights. Oba są obsługiwane przez [Eksploratora danych usługi Azure](/azure/data-explorer/) co oznacza, że mogą korzystać z zaawansowanych danych aparatu i zapytania języka.

Dane zarówno w przypadku obszarów roboczych, jak i aplikacje są organizowane w tabelach, z których każdy przechowuje różne rodzaje danych i ma swój własny unikatowy zestaw właściwości. Większość [źródeł danych](../platform/data-sources.md) zapisze tabel w obszarze roboczym usługi Log Analytics, podczas gdy usługi Application Insights będzie zapisywała wstępnie zdefiniowany zestaw tabel w aplikacji usługi Application Insights. Dziennik zapytań są bardzo elastyczne, co pozwala na łatwe łączenie danych z wielu tabel i nawet za pomocą zapytania obejmujące wiele zasobów, aby połączyć dane z tabel w wielu obszarach roboczych lub pisania zapytań, które łączą dane obszaru roboczego i aplikacji.

Na poniższej ilustracji przedstawiono przykłady źródeł danych, które zapisu do innych tabel, które są używane w przykładowych zapytań.

![Tabele](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Obszar roboczy usługi log Analytics
Wszystkie dane zebrane przez dzienników monitora platformy Azure, z wyjątkiem usługi Application Insights są przechowywane w [obszaru roboczego usługi Log Analytics](../platform/manage-access.md). W zależności od wymagań danego, można utworzyć jeden lub więcej obszarów roboczych. [Źródła danych](../platform/data-sources.md) takich jak Dzienniki aktywności i diagnostyczne dzienników z zasobów platformy Azure, agentów na maszynach wirtualnych i dane ze szczegółowych informacji i rozwiązań do monitorowania zapisze danych z jednego lub więcej obszarów roboczych, które można skonfigurować jako część ich dołączania. Innych usług, takich jak [usługi Azure Security Center](/azure/security-center/) i [Azure przez wartownika](/azure/sentinel/) również przechowują swoje dane, dzięki czemu mogą być analizowane za pomocą zapytań dziennika oraz dane monitorowania z innymi za pomocą obszaru roboczego usługi Log Analytics źródła.

Różne rodzaje danych są przechowywane w różnych tabelach, w obszarze roboczym, a każda tabela ma unikatowego zestawu właściwości. Standardowy zestaw tabel są dodawane do obszaru roboczego po jego utworzeniu, a nowe tabele są dodawane dla różnych źródeł, rozwiązań i usług, ponieważ są one dołączone. Można również utworzyć niestandardowe tabele za pomocą [interfejsu API modułu zbierającego dane](../platform/data-collector-api.md).

Możesz przeglądać tabel w obszarze roboczym i ich schemat w **schematu** kartę w usłudze Log Analytics dla obszaru roboczego.

![Schemat obszaru roboczego](media/scope/workspace-schema.png)

Użyj następującego zapytania do listy tabel w obszarze roboczym i liczbę rekordów zbieranych do każdej z nich za pośrednictwem poprzedniego dnia. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
W tym temacie dokumentacji dla każdego źródła danych, aby uzyskać szczegółowe informacje, tabel, które tworzą. Przykłady obejmują artykuły dotyczące [źródeł danych agenta](../platform/agent-data-sources.md), [dzienniki diagnostyczne](../platform/diagnostic-logs-schema.md), i [rozwiązania do monitorowania](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Uprawnień obszaru roboczego
Zobacz [uprawnień obszaru roboczego i zakres](../platform/manage-access.md#workspace-permissions-and-scope) szczegółowe informacje o zapewnieniu dostępu do danych w obszarze roboczym. Oprócz udostępnienia obszar roboczy, można ograniczyć dostęp do poszczególnych tabel przy użyciu [RBAC poziomu tabeli](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Application Insights aplikacji
Po utworzeniu aplikacji w usłudze Application Insights odpowiadającej jej aplikacji jest automatycznie tworzony w dzienników monitora platformy Azure. Konfiguracja nie jest wymagany do zbierania danych, a aplikacja będzie automatycznie zapisywać monitorowania danych, takich jak wyświetleń stron, żądań i wyjątków.

W przeciwieństwie do obszaru roboczego usługi Log Analytics aplikacja usługi Application Insights ma stały zestaw tabel. Nie można skonfigurować inne źródła danych można zapisać do aplikacji, dzięki czemu można tworzyć żadnych dodatkowych tabel. 

| Tabela | Opis | 
|:---|:---|
| availabilityResults | Podsumowanie dane z testów dostępności. |
| browserTimings      | Dane dotyczące wydajności klienta, takie jak czas poświęcony na przetworzenie danych przychodzących. |
| customEvents        | Niestandardowe zdarzenia tworzone przez testowaną aplikację. |
| customMetrics       | Metryki niestandardowe, tworzone przez testowaną aplikację. |
| Zależności        | Wywołania z aplikacji do składników zewnętrznych. |
| Wyjątki          | Wyjątki generowane przez środowisko uruchomieniowe aplikacji. |
| pageViews           | Wyświetl dane dotyczące każdej witryny sieci Web z informacjami o przeglądarce. |
| Liczniki wydajności | Pomiary wydajności z zasobów obliczeniowych, obsłudze aplikacji. |
| Żądania            | Szczegóły każdego żądania aplikacji.  |
| ślady              | Wyniki z rozproszonego śledzenia. |

Można wyświetlić schemat każdej tabeli w **schematu** kartę w usłudze Log Analytics dla aplikacji.

![Schemat aplikacji](media/scope/application-schema.png)

## <a name="standard-properties"></a>Właściwości standardowe
Podczas każdej tabeli w usłudze Azure Monitor dziennikach ma własny schemat, nie istnieją standardowe właściwości współużytkowane przez wszystkie tabele. Zobacz [standardowe właściwości w usłudze Azure Monitor dziennikach](../platform/log-standard-properties.md) szczegóły każdego z nich.

| Obszar roboczy usługi log Analytics | Application Insights aplikacji | Opis |
|:---|:---|:---|
| TimeGenerated | timestamp  | Data i godzina utworzenia rekordu. |
| Typ          | itemType   | Nazwa tabeli rekord został skopiowany z. |
| _ResourceId   |            | Unikatowy identyfikator rekordu zasobu jest skojarzony. |
| _IsBillable   |            | Określa, czy odebrane dane są płatne. |
| _BilledSize   |            | Określa rozmiar w bajtach, danych, które będą naliczane. |

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o korzystaniu z [przeszukiwania dzienników usługi Log Analytics można tworzyć i edytować](../log-query/portals.md).
- Zapoznaj się z [samouczek na temat pisania zapytań](../log-query/get-started-queries.md) przy użyciu nowego języka zapytań.

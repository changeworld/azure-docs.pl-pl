---
title: Struktura dzienników monitora platformy Azure | Dokumenty firmy Microsoft
description: Do pobierania danych dziennika z usługi Azure Monitor wymagana jest kwerenda dziennika.  W tym artykule opisano, jak nowe zapytania dziennika są używane w usłudze Azure Monitor i zawiera pojęcia, które należy zrozumieć przed utworzeniem jednego.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662080"
---
# <a name="structure-of-azure-monitor-logs"></a>Struktura dzienników monitora platformy Azure
Możliwość szybkiego uzyskiwania szczegółowych informacji na temat danych przy użyciu [zapytania dziennika](log-query-overview.md) jest zaawansowana funkcja usługi Azure Monitor. Aby utworzyć wydajne i przydatne zapytania, należy zrozumieć niektóre podstawowe pojęcia, takie jak lokalizacja, w której znajdują się dane i struktura. Ten artykuł zawiera podstawowe pojęcia potrzebne do rozpoczęcia pracy.

## <a name="overview"></a>Omówienie
Dane w dziennikach usługi Azure Monitor są przechowywane w obszarze roboczym usługi Log Analytics lub aplikacji usługi Application Insights. Oba są obsługiwane przez [usługę Azure Data Explorer](/azure/data-explorer/) co oznacza, że wykorzystują swój zaawansowany aparat danych i język zapytań.

Dane w obszarach roboczych i aplikacjach są zorganizowane w tabele, z których każdy przechowuje różne rodzaje danych i ma własny unikatowy zestaw właściwości. Większość [źródeł danych](../platform/data-sources.md) będzie zapisywać do własnych tabel w obszarze roboczym usługi Log Analytics, podczas gdy usługa Application Insights będzie zapisywać do wstępnie zdefiniowanego zestawu tabel w aplikacji usługi Application Insights. Zapytania dziennika są bardzo elastyczne, co pozwala łatwo łączyć dane z wielu tabel, a nawet używać kwerendy między zasobami do łączenia danych z tabel w wielu obszarach roboczych lub do pisania zapytań łączących dane obszaru roboczego i aplikacji.

Na poniższej ilustracji przedstawiono przykłady źródeł danych, które zapisują do różnych tabel, które są używane w przykładowych kwerendach.

![Tabele](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Wszystkie dane zbierane przez dzienniki usługi Azure Monitor z wyjątkiem usługi Application Insights są przechowywane w [obszarze roboczym usługi Log Analytics.](../platform/manage-access.md) W zależności od określonych wymagań można utworzyć jeden lub więcej obszarów roboczych. [Źródła danych,](../platform/data-sources.md) takie jak dzienniki aktywności i dzienniki zasobów z zasobów platformy Azure, agenci na maszynach wirtualnych i dane z aplikacji i rozwiązań do monitorowania będą zapisywać dane w jednym lub większej liczbie obszarów roboczych skonfigurowanych w ramach ich dołączania. Inne usługi, takie jak [Usługa Azure Security Center](/azure/security-center/) i usługa Azure [Sentinel](/azure/sentinel/) również używają obszaru roboczego usługi Log Analytics do przechowywania swoich danych, dzięki czemu można je analizować przy użyciu zapytań dziennika wraz z danymi monitorowania z innych źródeł.

Różne rodzaje danych są przechowywane w różnych tabelach w obszarze roboczym, a każda tabela ma unikatowy zestaw właściwości. Standardowy zestaw tabel są dodawane do obszaru roboczego podczas jego tworzenia, a nowe tabele są dodawane dla różnych źródeł danych, rozwiązań i usług, ponieważ są one wbudowane. Można również tworzyć tabele niestandardowe przy użyciu [interfejsu API modułu zbierającego dane](../platform/data-collector-api.md).

Tabele w obszarze roboczym i ich schemat można przeglądać na karcie **Schemat** w usłudze Log Analytics dla obszaru roboczego.

![Schemat obszaru roboczego](media/scope/workspace-schema.png)

Poniższa kwerenda służy do listy tabel w obszarze roboczym i liczby rekordów zebranych do każdego z nich w ciągu poprzedniego dnia. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Szczegółowe informacje na temat utworzonych przez siebie tabel można znaleźć w dokumentacji dla każdego źródła danych. Przykłady obejmują artykuły dotyczące [źródeł danych agenta,](../platform/agent-data-sources.md) [dzienników zasobów](../platform/diagnostic-logs-schema.md)i [rozwiązań do monitorowania.](../insights/solutions-inventory.md)

### <a name="workspace-permissions"></a>Uprawnienia obszaru roboczego
Zobacz [Projektowanie wdrożenia dzienników usługi Azure Monitor,](../platform/design-logs-deployment.md) aby zapoznać się ze strategią kontroli dostępu i zaleceniami dotyczącymi zapewniania dostępu do danych w obszarze roboczym. Oprócz udzielenia dostępu do samego obszaru roboczego można ograniczyć dostęp do poszczególnych tabel przy użyciu [poziomu tabeli RBAC](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Aplikacja Usługi Application Insights
Podczas tworzenia aplikacji w usłudze Application Insights odpowiednia aplikacja jest automatycznie tworzona w dziennikach usługi Azure Monitor. Do zbierania danych nie jest wymagana żadna konfiguracja, a aplikacja automatycznie zapisuje dane monitorowania, takie jak widoki strony, żądania i wyjątki.

W przeciwieństwie do obszaru roboczego usługi Log Analytics aplikacja usługi Application Insights ma stały zestaw tabel. Nie można skonfigurować innych źródeł danych do zapisu w aplikacji, aby nie można utworzyć żadnych dodatkowych tabel. 

| Tabela | Opis | 
|:---|:---|
| dostępnośćWyniki | Podsumowanie danych z testów dostępności. |
| przeglądarkiTimings      | Dane dotyczące wydajności klienta, takie jak czas przetwarzania danych przychodzących. |
| customEvents        | Zdarzenia niestandardowe utworzone przez aplikację. |
| customMetrics ( customMetrics )       | Niestandardowe metryki utworzone przez aplikację. |
| zależności        | Wywołania z aplikacji do składników zewnętrznych. |
| Wyjątki          | Wyjątki generowane przez środowisko uruchomieniowe aplikacji. |
| Odsłon           | Dane o każdym widoku witryny z informacjami o przeglądarce. |
| Performancecounters | Pomiary wydajności z zasobów obliczeniowych obsługujących aplikację. |
| Żądania            | Szczegóły każdego żądania aplikacji.  |
| Ślady              | Wyniki z śledzenia rozproszonego. |

Można wyświetlić schemat dla każdej tabeli na karcie **Schemat** w usłudze Log Analytics dla aplikacji.

![Schemat aplikacji](media/scope/application-schema.png)

## <a name="standard-properties"></a>Właściwości standardowe
Podczas gdy każda tabela w dziennikach monitora platformy Azure ma swój własny schemat, istnieją standardowe właściwości współużytkowane przez wszystkie tabele. Zobacz [właściwości standardowe w dziennikach usługi Azure Monitor, aby](../platform/log-standard-properties.md) uzyskać szczegółowe informacje o każdym z nich.

| Obszar roboczy usługi Log Analytics | Aplikacja Usługi Application Insights | Opis |
|:---|:---|:---|
| TimeGenerated | sygnatura czasowa  | Data i godzina utworzenia rekordu. |
| Typ          | Itemtype   | Nazwa tabeli, z został pobrany. |
| _ResourceId   |            | Unikatowy identyfikator zasobu, z który jest skojarzony rekord. |
| _IsBillable   |            | Określa, czy pogoń za danymi jest rozliczana. |
| _BilledSize   |            | Określa rozmiar w bajtach danych, które będą rozliczane. |

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o tworzeniu [i edytowaniu wyszukiwania dzienników](../log-query/portals.md)za pomocą usługi Log Analytics.
- Zapoznaj się z [samouczka na temat pisania zapytań](../log-query/get-started-queries.md) przy użyciu nowego języka zapytań.

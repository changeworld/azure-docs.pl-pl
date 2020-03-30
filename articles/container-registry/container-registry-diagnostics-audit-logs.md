---
title: Zbieranie & analizowanie dzienników zasobów
description: Rejestrowanie i analizowanie zdarzeń dziennika zasobów dla rejestru kontenerów platformy Azure, takich jak uwierzytelnianie, wypychanie obrazów i ściąganie obrazów.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409647"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Dzienniki rejestru kontenerów platformy Azure do oceny diagnostycznej i inspekcji

W tym artykule wyjaśniono, jak zbierać dane dziennika dla rejestru kontenerów platformy Azure przy użyciu funkcji [usługi Azure Monitor.](../azure-monitor/overview.md) Usługa Azure Monitor zbiera [dzienniki zasobów](../azure-monitor/platform/platform-logs-overview.md) (dawniej nazywane *dziennikami diagnostycznymi)* dla zdarzeń opartych na użytkownikach w rejestrze. Zbieraj i konsuj te dane, aby zaspokoić potrzeby, takie jak:

* Inspekcja zdarzeń uwierzytelniania rejestru w celu zapewnienia bezpieczeństwa i zgodności 

* Podaj pełny dziennik aktywności na artefaktach rejestru, takich jak zdarzenia ściągania i ściągania, aby można było zdiagnozować problemy operacyjne z rejestrem 

Zbieranie danych dziennika zasobów przy użyciu usługi Azure Monitor może ponieść dodatkowe koszty. Zobacz [cennik usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Zdarzenia repozytorium

Następujące zdarzenia na poziomie repozytorium dla obrazów i innych artefaktów są obecnie rejestrowane:

* **Zdarzenia wypychania**
* **Zdarzenia ściągania**
* **Untag wydarzenia**
* **Usuwanie zdarzeń** (w tym zdarzeń usuwania repozytorium)

Zdarzenia na poziomie repozytorium, które nie są aktualnie rejestrowane: przeczyszczanie zdarzeń.

## <a name="registry-resource-logs"></a>Dzienniki zasobów rejestru

Dzienniki zasobów zawierają informacje emitowane przez zasoby platformy Azure, które opisują ich wewnętrzną operację. W przypadku rejestru kontenerów platformy Azure dzienniki zawierają zdarzenia na poziomie uwierzytelniania i repozytorium przechowywane w poniższych tabelach. 

* **ContainerRegistryLoginEvents** — zdarzenia i stan uwierzytelniania rejestru, w tym tożsamość przychodząca i adres IP
* **ContainerRegistryRepositoryEvents** — operacje, takie jak wypychanie i ściąganie obrazów i innych artefaktów w repozytoriach rejestru
* **AzureMetrics** - [Metryki rejestru kontenera](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) AzureMetrics, takie jak zagregowane liczby wypychania i ściągania.

W przypadku operacji dane dziennika obejmują:
  * Stan sukcesu lub porażki
  * Znaczniki czasu rozpoczęcia i zakończenia

Oprócz dzienników zasobów platforma Azure udostępnia [dziennik aktywności](../azure-monitor/platform/platform-logs-overview.md), rekord na poziomie pojedynczej subskrypcji zdarzeń zarządzania platformy Azure, takich jak tworzenie lub usuwanie rejestru kontenerów.

## <a name="enable-collection-of-resource-logs"></a>Włączanie zbierania dzienników zasobów

Zbieranie dzienników zasobów dla rejestru kontenerów nie jest domyślnie włączona. Jawnie włącz ustawienia diagnostyczne dla każdego rejestru, który chcesz monitorować. Aby uzyskać opcje włączania ustawień diagnostycznych, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk platformy na platformie Azure](../azure-monitor/platform/diagnostic-settings.md).

Na przykład, aby wyświetlić dzienniki i metryki dla rejestru kontenerów w czasie zbliżonym do rzeczywistego w usłudze Azure Monitor, należy zebrać dzienniki zasobów w obszarze roboczym usługi Log Analytics. Aby włączyć to ustawienie diagnostyczne przy użyciu witryny Azure portal:

1. Jeśli nie masz jeszcze obszaru roboczego, utwórz obszar roboczy przy użyciu [portalu Azure.](../azure-monitor/learn/quick-create-workspace.md) Aby zminimalizować opóźnienia w zbieraniu danych, upewnij się, że obszar roboczy znajduje się w **tym samym regionie co** rejestr kontenerów.
1. W portalu wybierz rejestr i wybierz pozycję **Monitorowanie > ustawienia diagnostyczne > Dodaj ustawienie diagnostyczne**.
1. Wprowadź nazwę tego ustawienia i wybierz pozycję **Wyślij do usługi Log Analytics**.
1. Wybierz obszar roboczy dzienników diagnostycznych rejestru.
1. Wybierz dane dziennika, które chcesz zebrać, a następnie kliknij przycisk **Zapisz**.

Na poniższej ilustracji przedstawiono utworzenie ustawienia diagnostycznego dla rejestru za pomocą portalu.

![Włączanie ustawień diagnostycznych](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Zbieraj tylko potrzebne dane, równoważąc koszty i potrzeby monitorowania. Na przykład jeśli wystarczy tylko inspekcji zdarzeń uwierzytelniania, wybierz tylko **ContainerRegistryLoginEvents** dziennika. 

## <a name="view-data-in-azure-monitor"></a>Wyświetlanie danych w usłudze Azure Monitor

Po włączeniu zbierania dzienników diagnostycznych w usłudze Log Analytics może upłynąć kilka minut, aby dane były wyświetlane w usłudze Azure Monitor. Aby wyświetlić dane w portalu, wybierz rejestr i wybierz **pozycję Monitorowanie > dzienniki**. Wybierz jedną z tabel zawierającą dane dla rejestru. 

Uruchom kwerendy, aby wyświetlić dane. Kilka przykładowych zapytań są dostarczane lub uruchomić własne. Na przykład następująca kwerenda pobiera najnowsze 24 godziny danych z **ContainerRegistryRepositoryEvents** tabeli:

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Na poniższej ilustracji przedstawiono przykładowe dane wyjściowe:

![Dane dziennika zapytań](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Aby zapoznać się z samouczkiem na temat korzystania z usługi Log Analytics w portalu Azure, zobacz [Wprowadzenie do usługi Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md)lub wypróbuj środowisko [demo](https://portal.loganalytics.io/demo)usługi Log Analytics . 

Aby uzyskać więcej informacji na temat zapytań dziennika, zobacz [Omówienie zapytań dziennika w usłudze Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="additional-query-examples"></a>Przykłady dodatkowych zapytań

#### <a name="100-most-recent-registry-events"></a>100 ostatnich zdarzeń rejestru

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Dodatkowe miejsca docelowe dziennika

Oprócz wysyłania dzienników do usługi Log Analytics lub alternatywnie typowym scenariuszem jest wybranie konta usługi Azure Storage jako miejsca docelowego dziennika. Aby zarchiwizować dzienniki w usłudze Azure Storage, utwórz konto magazynu przed włączeniem archiwizacji za pomocą ustawień diagnostycznych.

Można również przesyłać strumieniowo zdarzenia dziennika diagnostycznego do [usługi Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Centra zdarzeń mogą pozyskiwania milionów zdarzeń na sekundę, które można następnie przekształcić i przechowywać przy użyciu dowolnego dostawcy analizy w czasie rzeczywistym. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o korzystaniu z [usługi Log Analytics](../azure-monitor/log-query/get-started-portal.md) i tworzeniu [zapytań dziennika](../azure-monitor/log-query/get-started-queries.md).
* Zobacz [Omówienie dzienników platformy Azure,](../azure-monitor/platform/platform-logs-overview.md) aby dowiedzieć się więcej o dziennikach platformy, które są dostępne w różnych warstwach platformy Azure.


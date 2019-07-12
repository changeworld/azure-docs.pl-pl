---
title: Wykonywanie zapytań dotyczących danych w usłudze Azure Monitor, za pomocą Eksploratora danych platformy Azure (wersja zapoznawcza)
description: W tym temacie wysyłania zapytań dotyczących danych w usłudze Azure Monitor, tworząc serwer proxy Eksploratora usługi Azure Data iloczyn zapytań za pomocą usługi Application Insights i Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800213"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Wykonywanie zapytań dotyczących danych w usłudze Azure Monitor, za pomocą Eksploratora danych platformy Azure (wersja zapoznawcza)

Klaster serwera proxy Eksploratora danych usługi Azure (serwer Proxy ADX) jest jednostki, która umożliwia wykonywanie zapytań dla wielu produktów między Eksploratora danych usługi Azure, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview), i [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) w [Usługi azure Monitor](/azure/azure-monitor/) usługi. Obszary robocze usługi Azure Monitor Log Analytics lub aplikacji usługa Application Insights można mapować jako klaster serwera proxy. Można zbadać klastra serwera proxy, za pomocą narzędzia Eksplorator danych usługi Azure i odwołują się do niego w zapytaniu między klastrem. Artykuł pokazuje, jak połączyć się z klastrem serwera proxy, dodać klaster serwera proxy do interfejsu użytkownika sieci Web Eksploratora danych usługi Azure i uruchamiać zapytania swoje aplikacje sztucznej Inteligencji lub LA obszarami roboczymi z poziomu Eksploratora danych usługi Azure.

Eksplorator danych usługi Azure przepływ serwera proxy: 

![Przepływ serwera proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Serwer Proxy ADX jest w trybie podglądu. Aby włączyć tę funkcję, skontaktuj się z [ADXProxy](mailto:adxproxy@microsoft.com) zespołu.

## <a name="connect-to-the-proxy"></a>Łączenie z serwerem proxy

1. Sprawdź klastra natywnych Eksploratora danych usługi Azure (takie jak *pomocy* klastra) pojawia się w menu po lewej stronie, przed nawiązaniem połączenia z klastrem usługi Log Analytics lub usługi Application Insights.

    ![Natywne klastra ADX](media/adx-proxy/web-ui-help-cluster.png)

1. W Interfejsie usługi Azure Data Explorer (https://dataexplorer.azure.com/clusters), wybierz opcję **dodawania klastrze**.

1. W **dodawania klastrze** okna:

    * Dodaj adres URL do klastra LA lub sztucznej Inteligencji. Na przykład: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Wybierz pozycję **Dodaj**.

    ![Dodawanie klastra](media/adx-proxy/add-cluster.png)

    Jeśli dodasz połączenie do więcej niż jeden klaster serwera proxy, nadaj każdej inną nazwę. W przeciwnym razie będą wszystkie mieć taką samą nazwę w okienku po lewej stronie.

1. Po nawiązaniu połączenia klastra LA lub sztucznej Inteligencji pojawi się w lewym okienku na potrzeby natywnego klastra ADX. 

    ![Klastry usługi log Analytics i Eksplorator danych platformy Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Uruchamianie zapytań

Kusto Eksploratorze ADX web Eksploratorze Jupyter Kqlmagic lub interfejsu API REST można użyć do wykonywania zapytań, klastry serwera proxy. 

> [!TIP]
> * Nazwa bazy danych powinna mieć taką samą nazwę jak zasób określony w klastrze serwera proxy. Nazwy jest uwzględniana wielkość liter.
> * W klastrze zapytania obejmujące wiele, upewnij się, że [nazw aplikacji i obszarów roboczych](#application-insights-app-and-log-analytics-workspace-names) jest poprawna.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Zapytanie natywne w klastrze Eksploratora danych usługi Azure 

Uruchamianie zapytań w klastrze Azure Eksplorator danych (takich jak *StormEvents* tabelę *pomocy* klastra). Podczas uruchamiania zapytania, sprawdź, czy wybrano natywnych klastra Eksploratora danych usługi Azure, w okienku po lewej stronie.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Tabela StormEvents zapytania](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Zapytanie względem klastra LA lub sztucznej Inteligencji

Podczas uruchamiania kwerend w klastrze LA lub AL, sprawdź, czy wybrano klastra LA lub sztucznej Inteligencji, w okienku po lewej stronie. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Obszar roboczy LA zapytania](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Zapytania z serwera proxy ADX klastra LA lub sztucznej Inteligencji  

Po uruchomieniu zapytania w klastrze LA lub sztucznej Inteligencji z serwera proxy, sprawdź, czy klaster natywnych ADX jest zaznaczona w okienku po lewej stronie. Poniższy przykład ilustruje zapytanie LA obszaru roboczego przy użyciu natywnych klastra ADX

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Zapytania z serwera proxy Eksploratora danych platformy Azure](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Krzyżowe zapytania LA lub sztucznej Inteligencji i klastrze ADX z serwera proxy ADX 

Po uruchomieniu klastra między zapytania z serwera proxy, sprawdź, czy klaster natywnych ADX jest zaznaczona w okienku po lewej stronie. W poniższych przykładach pokazano, łącząc ADX klastra tabel (przy użyciu `union`) z obszarem roboczym LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Wiele zapytań z serwera proxy Eksploratora danych usługi Azure](media/adx-proxy/cross-query-adx-proxy.png)

Za pomocą [ `join` operator](/azure/kusto/query/joinoperator), zamiast Unii, mogą wymagać wskazówkę dotyczącą go uruchomić klaster natywnych Eksploratora danych usługi Azure (a nie serwera proxy). 

## <a name="additional-syntax-examples"></a>Przykłady składni dodatkowe

Podczas wywoływania klastrów Application Insights (AI) i Log Analytics (LA) dostępne są następujące opcje składni:

|Opis składni  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Bazy danych w klastrze, który zawiera tylko zdefiniowany zasób w ramach tej subskrypcji (**zalecany dla wielu zapytań klastra**) |   klastra (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | klastra (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Klaster, który zawiera wszystkie aplikacje/obszary robocze w tej subskrypcji    |     klastra (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    klastra (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Klaster, który zawiera wszystkie aplikacje/obszary robocze w ramach subskrypcji i są członkami tej grupy zasobów    |   klastra (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    klastra (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Klaster, który zawiera tylko zdefiniowany zasób w ramach tej subskrypcji      |    klastra (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  klastra (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Aplikacja usługi Application Insights i nazwy obszaru roboczego usługi Log Analytics

* Jeśli nazwy zawierają znaki specjalne, jesteś zastępuje adres URL kodowania w nazwie klastra serwera proxy. 
* Jeśli nazwy zawierają znaki, które nie spełniają [KQL identyfikator nazwy reguły](/azure/kusto/query/schema-entities/entity-names), są zastąpione przez kreska **-** znaków.

## <a name="next-steps"></a>Następne kroki

[Pisanie zapytań](write-queries.md)
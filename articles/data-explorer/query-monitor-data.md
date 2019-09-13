---
title: Wykonywanie zapytań dotyczących danych w Azure Monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)
description: W tym temacie przedstawiono zapytania dotyczące danych w Azure Monitor przez utworzenie serwera proxy usługi Azure Eksplorator danych dla zapytań między produktami za pomocą Application Insights i Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 43d91bff6b8b67e79a9549c1524f918166c9adc4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934009"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Wykonywanie zapytań dotyczących danych w Azure Monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)

Klaster usługi Azure Eksplorator danych proxy (ADX proxy) to jednostka, która umożliwia wykonywanie zapytań między produktami między usługą Azure Eksplorator danych, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)i [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) w usłudze [Azure monitor](/azure/azure-monitor/) . Azure Monitor Log Analytics obszary robocze lub aplikacje Application Insights jako klaster proxy. Następnie można wykonać zapytanie dotyczące klastra proxy przy użyciu narzędzi usługi Azure Eksplorator danych i odwołać się do niego w zapytaniu między klastrami. W tym artykule pokazano, jak nawiązać połączenie z klastrem proxy, dodać klaster proxy do interfejsu użytkownika sieci Web usługi Azure Eksplorator danych i uruchamiać zapytania dotyczące aplikacji AI lub obszarów roboczych LAer z usługi Azure Eksplorator danych.

Przepływ serwera proxy usługi Azure Eksplorator danych: 

![ADX przepływ serwera proxy](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Serwer proxy ADX jest w trybie podglądu. Aby włączyć tę funkcję, skontaktuj się z zespołem [ADXProxy](mailto:adxproxy@microsoft.com) .

## <a name="connect-to-the-proxy"></a>Łączenie z serwerem proxy

1. Przed nawiązaniem połączenia z klastrem Log Analytics lub Application Insights należy sprawdzić, czy w menu po lewej stronie jest wyświetlany klaster macierzysty platformy Azure Eksplorator danych (na przykład klaster *pomocy* ).

    ![ADX natywny klaster](media/adx-proxy/web-ui-help-cluster.png)

1. W interfejsie użytkownika usługi Azure https://dataexplorer.azure.com/clusters) Eksplorator danych (wybierz pozycję **Dodaj klaster**.

1. W oknie **Dodawanie klastra** :

    * Dodaj adres URL do klastra LA lub AI. Na przykład: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Wybierz pozycję **Dodaj**.

    ![Dodawanie klastra](media/adx-proxy/add-cluster.png)

    W przypadku dodania połączenia do więcej niż jednego klastra proxy nadaj każdej innej nazwie. W przeciwnym razie wszystkie będą mieć taką samą nazwę w lewym okienku.

1. Po nawiązaniu połączenia klaster LA lub AI zostanie wyświetlony w lewym okienku z natywnym klastrem ADX. 

    ![Log Analytics i klastry Eksplorator danych platformy Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Uruchamianie zapytań

Do wysyłania zapytań do klastrów proxy można użyć Eksploratora Kusto, ADX Web Explorer, Jupyter Kqlmagic lub interfejsu API REST. 

> [!TIP]
> * Nazwa bazy danych powinna mieć taką samą nazwę jak zasób określony w klastrze proxy. W nazwach rozróżniana jest wielkość liter.
> * W przypadku zapytań między klastrami upewnij się, że nazwy aplikacji Application Insights i Log Analytics obszary robocze są poprawne.
>     * Jeśli nazwy zawierają znaki specjalne, są one zastępowane przez kodowanie adresów URL w nazwie klastra proxy. 
>     * Jeśli nazwy zawierają znaki, które nie są zgodne z [regułami nazw identyfikatorów KQL](/azure/kusto/query/schema-entities/entity-names), są zastępowane znakiem kreski **-** .

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Zapytanie względem natywnego klastra Eksplorator danych platformy Azure 

Uruchamianie zapytań w klastrze Eksplorator danych platformy Azure (np. tabeli *StormEvents* w klastrze *pomocy* ). Podczas uruchamiania zapytania upewnij się, że w okienku po lewej stronie wybrano macierzysty klaster Eksplorator danych platformy Azure.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Kwerenda tabeli StormEvents](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Zapytanie względem klastra LA lub AI

Podczas uruchamiania zapytań w klastrze LA lub AL Sprawdź, czy w lewym okienku wybrano klaster LA lub AI. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Zapytanie w obszarze roboczym LA](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Wykonywanie zapytania dotyczącego klastra LA lub AI z serwera proxy ADX  

Po uruchomieniu zapytań w klastrze LA lub AI z serwera proxy Sprawdź, czy w lewym okienku wybrano ADX natywny klaster. Poniższy przykład ilustruje zapytanie dotyczące obszaru roboczego LA przy użyciu natywnego klastra ADX

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Zapytanie z usługi Azure Eksplorator danych proxy](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Krzyżowe zapytanie o klaster LA lub AI oraz klaster ADX z serwera proxy ADX 

Gdy uruchamiasz zapytania między klastrami z serwera proxy, sprawdź, czy w lewym okienku wybrano ADX natywny klaster. W poniższych przykładach pokazano Łączenie tabel klastrów ADX ( `union`using) z obszarem roboczym La.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Krzyżowe zapytanie z serwera proxy usługi Azure Eksplorator danych](media/adx-proxy/cross-query-adx-proxy.png)

Użycie operatora zamiast Union może wymagać [`hint`](/azure/kusto/query/joinoperator#join-hints) uruchomienia go w klastrze macierzystym platformy Azure Eksplorator danych (a nie na serwerze proxy). [ `join` ](/azure/kusto/query/joinoperator) 

## <a name="additional-syntax-examples"></a>Dodatkowe przykłady składni

Podczas wywoływania klastrów Application Insights (AI) lub Log Analytics (LA) dostępne są następujące opcje składni:

|Opis składni  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Baza danych w klastrze, która zawiera tylko zdefiniowany zasób w tej subskrypcji (**zalecane w przypadku zapytań między klastrami**) |   klaster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | klaster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Klaster zawierający wszystkie aplikacje/obszary robocze w tej subskrypcji    |     klaster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    klaster (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Klaster zawierający wszystkie aplikacje/obszary robocze w subskrypcji i są członkami tej grupy zasobów    |   klaster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    klaster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Klaster zawierający tylko zdefiniowany zasób w tej subskrypcji      |    klaster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  klaster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Następne kroki

[Pisanie zapytań](write-queries.md)

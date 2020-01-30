---
title: Wykonywanie zapytań dotyczących danych w Azure Monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)
description: W tym temacie przedstawiono zapytania dotyczące danych w Azure Monitor przez utworzenie serwera proxy usługi Azure Eksplorator danych dla zapytań między produktami za pomocą Application Insights i Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: d39ffa05448600fe3bd09baf6080aa1565ae19ba
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843591"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Wykonywanie zapytań dotyczących danych w Azure Monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)

Klaster usługi Azure Eksplorator danych proxy (ADX proxy) to jednostka, która umożliwia wykonywanie zapytań między produktami między usługą Azure Eksplorator danych, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)i [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) w usłudze [Azure monitor](/azure/azure-monitor/) . Azure Monitor Log Analytics obszarów roboczych lub aplikacji Application Insights jako klastrów proxy. Następnie można wykonać zapytanie dotyczące klastra proxy przy użyciu narzędzi usługi Azure Eksplorator danych i odwołać się do niego w zapytaniu między klastrami. W tym artykule pokazano, jak nawiązać połączenie z klastrem proxy, dodać klaster proxy do interfejsu użytkownika sieci Web usługi Azure Eksplorator danych i uruchamiać zapytania dotyczące aplikacji AI lub obszarów roboczych LAer z usługi Azure Eksplorator danych.

Przepływ serwera proxy usługi Azure Eksplorator danych: 

![ADX przepływ serwera proxy](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Serwer proxy ADX jest w trybie podglądu. [Połącz się z serwerem proxy,](#connect-to-the-proxy) aby włączyć funkcję serwera proxy ADX dla klastrów. Skontaktuj się z zespołem [ADXProxy](mailto:adxproxy@microsoft.com) .

## <a name="connect-to-the-proxy"></a>Łączenie z serwerem proxy

1. Przed nawiązaniem połączenia z klastrem Log Analytics lub Application Insights należy sprawdzić, czy w menu po lewej stronie jest wyświetlany klaster macierzysty platformy Azure Eksplorator danych (na przykład klaster *pomocy* ).

    ![ADX natywny klaster](media/adx-proxy/web-ui-help-cluster.png)

1. W interfejsie użytkownika usługi Azure Eksplorator danych (https://dataexplorer.azure.com/clusters) wybierz pozycję **Dodaj klaster**.

1. W oknie **Dodawanie klastra** Dodaj adres URL do klastra La lub AI. 
    
    * Dla LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Dla AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Wybierz pozycję **Dodaj**.

    ![Dodawanie klastra](media/adx-proxy/add-cluster.png)

    W przypadku dodania połączenia do więcej niż jednego klastra proxy nadaj każdej innej nazwie. W przeciwnym razie wszystkie będą mieć taką samą nazwę w lewym okienku.

1. Po nawiązaniu połączenia klaster LA lub AI zostanie wyświetlony w lewym okienku z natywnym klastrem ADX. 

    ![Log Analytics i klastry Eksplorator danych platformy Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Uruchamianie zapytań

Można uruchamiać zapytania przy użyciu narzędzi klienckich, które obsługują zapytania Kusto, takie jak: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, interfejs API REST.

> [!TIP]
> * Nazwa bazy danych powinna mieć taką samą nazwę jak zasób określony w klastrze proxy. W nazwach rozróżniana jest wielkość liter.
> * W przypadku zapytań między klastrami upewnij się, że nazwy aplikacji Application Insights i Log Analytics obszary robocze są poprawne.
>     * Jeśli nazwy zawierają znaki specjalne, są one zastępowane przez kodowanie adresów URL w nazwie klastra proxy. 
>     * Jeśli nazwa zawiera znaki, które nie są zgodne z [regułami nazw identyfikatorów KQL](/azure/kusto/query/schema-entities/entity-names), są one zastępowane znakami kreski **-** .

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Zapytanie bezpośrednie z klastra LA lub AI ADX serwera proxy

Uruchamianie zapytań w klastrze LA lub AI. Sprawdź, czy klaster został wybrany w lewym okienku. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Zapytanie w obszarze roboczym LA](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Krzyżowe zapytanie dotyczące klastra ADX i sieci proxy programu LA lub AI oraz klastra macierzystego ADX 

Gdy uruchamiasz zapytania między klastrami z serwera proxy, sprawdź, czy w lewym okienku wybrano ADX natywny klaster. W poniższych przykładach pokazano, jak łączyć tabele klastrów ADX (przy użyciu `union`) z obszarem roboczym LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![krzyżowego zapytania z usługi Azure Eksplorator danych proxy](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Użycie [operatora`join`](/azure/kusto/query/joinoperator), a nie Unii, może wymagać [`hint`](/azure/kusto/query/joinoperator#join-hints) uruchomienia go w klastrze usługi Azure Eksplorator danych Native (a nie na serwerze proxy). 

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

---
title: Zapytanie o dane w usłudze Azure Monitor za pomocą Eksploratora danych platformy Azure (wersja zapoznawcza)
description: W tym temacie kwerendy danych w usłudze Azure Monitor przez utworzenie serwera proxy Usługi Azure Data Explorer dla zapytań między produktami z usługi Application Insights i log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560426"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Zapytanie o dane w usłudze Azure Monitor przy użyciu Eksploratora danych platformy Azure (wersja zapoznawcza)

Klaster proxy usługi Azure Data Explorer (ADX Proxy) to jednostka, która umożliwia wykonywanie zapytań dotyczących różnych produktów między eksploratorem danych platformy Azure, [analizą aplikacji (AI)](/azure/azure-monitor/app/app-insights-overview)i [analizą dzienników (LA)](/azure/azure-monitor/platform/data-platform-logs) w usłudze [Azure Monitor.](/azure/azure-monitor/) Obszary robocze usługi Azure Monitor Log Analytics lub aplikacje usługi Application Insights można mapować jako klastry proxy. Następnie można zbadać klaster proxy przy użyciu narzędzi Usługi Azure Data Explorer i odwoływać się do niego w kwerendzie klastra krzyżowego. W tym artykule pokazano, jak połączyć się z klastrem proxy, dodać klaster proxy do interfejsu użytkownika sieci Web usługi Azure Data Explorer i uruchomić kwerendy dotyczące aplikacji AI lub obszarów roboczych LA z Usługi Azure Data Explorer.

Przepływ serwera proxy Usługi Azure Data Explorer: 

![Przepływ serwera proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Serwer proxy ADX jest w trybie podglądu. [Połącz się z serwerem proxy,](#connect-to-the-proxy) aby włączyć funkcję serwera proxy ADX dla klastrów. W razie jakichkolwiek pytań należy skontaktować się z zespołem [ADXProxy.](mailto:adxproxy@microsoft.com)

## <a name="connect-to-the-proxy"></a>Łączenie się z serwerem proxy

1. Przed nawiązaniem połączenia z *help* klastrem usługi Log Analytics lub usługi Application Insights w menu po lewej stronie pojawi się klaster natywny usług Azure Data Explorer (np.

    ![Natywny klaster ADX](media/adx-proxy/web-ui-help-cluster.png)

1. W interfejsie użytkownika usługihttps://dataexplorer.azure.com/clusters)Azure Data Explorer ( wybierz pozycję **Dodaj klaster**.

1. W oknie **Dodawanie klastra** dodaj adres URL do klastra LA lub AI. 
    
    * Dla LA:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Dla AI:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Wybierz pozycję **Dodaj**.

    ![Dodawanie klastra](media/adx-proxy/add-cluster.png)

    Jeśli dodasz połączenie do więcej niż jednego klastra proxy, nadaj każdemu inną nazwę. W przeciwnym razie wszystkie będą miały taką samą nazwę w lewym okienku.

1. Po nawiązaniu połączenia klaster LA lub AI pojawi się w lewym okienku z macierzystym klastrem ADX. 

    ![Usługi Log Analytics i klastry eksploratora danych platformy Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Uruchamianie zapytań

Kwerendy można uruchamiać przy użyciu narzędzi klienckich obsługujących zapytania Kusto, takich jak: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API.

> [!TIP]
> * Nazwa bazy danych powinna mieć taką samą nazwę jak zasób określony w klastrze serwera proxy. W nazwach rozróżniana jest wielkość liter.
> * W kwerendach klastra krzyżowego upewnij się, że nazewnictwo aplikacji usługi Application Insights i obszarów roboczych usługi Log Analytics jest poprawne.
>     * Jeśli nazwy zawierają znaki specjalne, są one zastępowane kodowaniem adresów URL w nazwie klastra proxy. 
>     * Jeśli nazwy zawierają znaki, które nie spełniają [reguł identyfikatora KQL,](/azure/kusto/query/schema-entities/entity-names)są one zastępowane przez znak kreski. **-**

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Bezpośrednie zapytanie z klastra serwera proxy LA lub AI ADX

Uruchamianie zapytań w klastrze LA lub AI. Sprawdź, czy klaster jest zaznaczony w lewym okienku. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Kwerenda LA obszar roboczy](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Kwerenda krzyżowa klastra serwera proxy LA lub AI ADX i klastra macierzystego ADX 

Po uruchomieniu kwerend klastra krzyżowego z serwera proxy sprawdź, czy natywny klaster ADX jest zaznaczony w lewym okienku. Poniższe przykłady pokazują łączenie tabel klastra ADX (przy użyciu) `union`z obszarem roboczym LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Kwerenda krzyżowa z serwera proxy Eksploratora danych platformy Azure](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Za pomocą [ `join` operatora](/azure/kusto/query/joinoperator), zamiast unii, [`hint`](/azure/kusto/query/joinoperator#join-hints) może wymagać uruchomienia go w klastrze macierzystym usługi Azure Data Explorer (a nie na serwerze proxy). 

## <a name="additional-syntax-examples"></a>Dodatkowe przykłady składni

Następujące opcje składni są dostępne podczas wywoływania klastrów usługi Application Insights (AI) lub Log Analytics (LA):

|Opis składni  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Baza danych w klastrze zawierająca tylko zdefiniowany zasób w tej subskrypcji **(zalecana dla kwerend klastrowych krzyżowych)** |   klaster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | klaster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Klaster zawierający wszystkie aplikacje/obszary robocze w tej subskrypcji    |     klaster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    klaster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Klaster zawierający wszystkie aplikacje/obszary robocze w ramach subskrypcji i członków tej grupy zasobów    |   klaster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    klaster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Klaster zawierający tylko zdefiniowany zasób w tej subskrypcji      |    klaster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  klaster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Następne kroki

[Pisanie zapytań](write-queries.md)

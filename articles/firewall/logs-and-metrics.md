---
title: Omówienie dzienników i metryk zapory platformy Azure
description: Usługę Azure Firewall możesz monitorować przy użyciu dzienników zapory. Ponadto dzienniki aktywności umożliwiają inspekcję operacji wykonywanych względem zasobów usługi Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76315035"
---
# <a name="azure-firewall-logs-and-metrics"></a>Dzienniki i metryki usługi Azure Firewall

Usługę Azure Firewall możesz monitorować przy użyciu dzienników zapory. Ponadto dzienniki aktywności umożliwiają inspekcję operacji wykonywanych względem zasobów usługi Azure Firewall.

Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Dzienniki mogą być wysyłane do [dzienników usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), usługi Storage i Event Hubs oraz analizowane za pomocą dzienników usługi Azure Monitor lub innych narzędzi, takich jak program Excel i usługa Power BI.

Metryki są lekkie i mogą obsługiwać scenariusze w czasie zbliżonym do rzeczywistego, dzięki czemu są przydatne do alertów i szybkiego wykrywania problemów.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

 Dla usługi Azure Firewall są dostępne następujące dzienniki diagnostyczne:

* **Dziennik reguł aplikacji**

   Dziennik reguł aplikacji jest zapisywany na koncie magazynu, przesyłany strumieniowo do centrów zdarzeń i/lub wysyłany do dzienników usługi Azure Monitor tylko wtedy, gdy włączono go dla każdej zapory platformy Azure. W wyniku każdego nowego połączenia, które jest zgodne z jedną ze skonfigurowanych reguł aplikacji, jest tworzony dziennik dla zaakceptowanego/odrzuconego połączenia. Dane są rejestrowane w formacie JSON, jak pokazano w poniższym przykładzie:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Dziennik reguł sieci**

   Dziennik reguł sieciowych jest zapisywany na koncie magazynu, przesyłany strumieniowo do centrów zdarzeń i/lub wysyłany do dzienników usługi Azure Monitor tylko wtedy, gdy włączono go dla każdej Zapory platformy Azure. W wyniku każdego nowego połączenia, które jest zgodne z jedną ze skonfigurowanych reguł sieci, jest tworzony dziennik dla zaakceptowanego/odrzuconego połączenia. Dane są rejestrowane w formacie JSON, jak pokazano w poniższym przykładzie:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Masz trzy opcje przechowywania dzienników:

* **Konto magazynu**: konta magazynu najlepiej sprawdzają się w przypadku dzienników przechowywanych przez dłuższy czas i przeglądanych w razie potrzeby.
* **Event Hubs**: usługa Event Hubs to doskonałe rozwiązanie umożliwiające integrację z innymi narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM, Security Information and Event Management), aby otrzymywać alerty dotyczące zasobów.
* **Dzienniki usługi Azure Monitor:** dzienniki usługi Azure Monitor są najlepiej używane do ogólnego monitorowania aplikacji w czasie rzeczywistym lub analizowania trendów.

## <a name="activity-logs"></a>Dzienniki aktywności

   Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlać w witrynie Azure Portal.

   Dzienniki [aktywności platformy Azure](../azure-resource-manager/management/view-activity-logs.md) (wcześniej nazywane dziennikami operacyjnymi i dziennikami inspekcji) można wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure.

## <a name="metrics"></a>Metryki

Metryki w usłudze Azure Monitor są wartościami liczbowymi, które opisują niektóre aspekty systemu w określonym czasie. Metryki są zbierane co minutę i są przydatne do alertów, ponieważ można je często pobierać próbki. Alert może być uruchamiany szybko z logiką stosunkowo proste.

Następujące metryki są dostępne dla Zapory platformy Azure:

- **Liczba trafień reguł aplikacji** — liczba trafień reguły aplikacji.

    Jednostka: liczba

- **Liczba trafień reguł sieciowych** — liczba trafień reguły sieciowej.

    Jednostka: liczba

- **Przetwarzane dane** — ilość danych przechodzących przez zaporę.

    Jednostka: bajty

- **Stan kondycji zapory** — wskazuje kondycję zapory na podstawie dostępności portu SNAT.

    Jednostka: procent

   Ta metryka ma dwa wymiary:
  - Stan: Możliwe wartości to *Zdrowy,* *Zdegradowany,* *Niezdrowy*.
  - Przyczyna: Wskazuje przyczynę odpowiedniego stanu zapory. 

     Jeśli porty SNAT są używane > 95%, są one uważane za wyczerpane, a kondycja wynosi 50% ze stanem =**Zdegradowany** i rozum =**port SNAT**. Zapora utrzymuje przetwarzanie ruchu i istniejących połączeń nie ma wpływu. Jednak nowe połączenia mogą nie być ustanawiane sporadycznie.

     Jeśli porty SNAT są używane < 95%, zapora jest uważana za zdrową, a kondycja jest wyświetlana jako 100%.

     Jeśli nie jest zgłaszane użycie portów SNAT, kondycja jest wyświetlana jako 0%. 

- **Wykorzystanie portu SNAT** — procent portów SNAT, które zostały wykorzystane przez zaporę.

    Jednostka: procent

   Po dodaniu większej liczby publicznych adresów IP do zapory dostępnych jest więcej portów SNAT, co zmniejsza wykorzystanie portów SNAT. Ponadto gdy zapora jest skalowana w poziomie z różnych powodów (na przykład procesora CPU lub przepływności) dodatkowe porty SNAT również stają się dostępne. Tak skutecznie, dany procent wykorzystania portów SNAT może zejść bez dodawania żadnych publicznych adresów IP, tylko dlatego, że usługa skalowana w poziomie. Można bezpośrednio kontrolować liczbę publicznych adresów IP dostępnych w celu zwiększenia portów dostępnych na zaporze. Ale nie można bezpośrednio kontrolować skalowanie zapory. Obecnie porty SNAT są dodawane tylko dla pierwszych pięciu publicznych adresów IP.   


## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak monitorować dzienniki i metryki Zapory platformy Azure, zobacz [Samouczek: Monitorowanie dzienników zapory platformy Azure](tutorial-diagnostics.md).

- Aby dowiedzieć się więcej o metrykach w usłudze Azure Monitor, zobacz [metryki w usłudze Azure Monitor.](../azure-monitor/platform/data-platform-metrics.md)

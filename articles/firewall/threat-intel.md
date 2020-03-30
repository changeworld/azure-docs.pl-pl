---
title: Filtrowanie oparte na analizie zagrożeń zapory platformy Azure
description: Filtrowanie oparte na analizie zagrożeń można włączyć, aby zapora ostrzegała i odmawiała ruchu ze znanych złośliwych adresów IP i domen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168677"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtrowanie oparte na analizie zagrożeń zapory platformy Azure

Filtrowanie oparte na analizie zagrożeń można włączyć, aby zapora ostrzegała i odmawiała ruchu ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z źródła danych microsoft Threat Intelligence. [Inteligentny wykres zabezpieczeń](https://www.microsoft.com/en-us/security/operations/intelligence) zasila analizę zagrożeń firmy Microsoft i jest używany przez wiele usług, w tym Usługę Azure Security Center.

![Analiza zagrożeń zapory sieciowej](media/threat-intel/firewall-threat.png)

Jeśli włączono filtrowanie oparte na analizie zagrożeń, skojarzone reguły są przetwarzane przed regułami NAT, regułami sieci lub regułami aplikacji.

Możesz po prostu zarejestrować alert po wyzwoleniu reguły lub wybrać tryb alertu i odmowy.

Domyślnie filtrowanie oparte na analizie zagrożeń jest włączone w trybie alertu. Nie można wyłączyć tej funkcji ani zmienić trybu, dopóki interfejs portalu nie stanie się dostępny w Twoim regionie.

![Interfejs portalu filtrowania oparty na analizie zagrożeń](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Dzienniki

Poniższy fragment dziennika przedstawia wyzwalaną regułę:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testowanie

- **Testowanie wychodzące** — alerty ruchu wychodzącego powinny być rzadkim zjawiskiem, ponieważ oznacza to, że środowisko zostało naruszone. Aby ułatwić testowanie alertów wychodzących, utworzono testową usanną usanną usanną usanną, która wyzwala alert. Użyj **testmaliciousdomain.eastus.cloudapp.azure.com** dla testów wychodzących.

- **Testowanie przychodzące** — można oczekiwać, aby zobaczyć alerty na ruchu przychodzącego, jeśli reguły DNAT są skonfigurowane na zaporze. Jest to prawdą, nawet jeśli tylko określone źródła są dozwolone w regule DNAT i ruch jest w inny sposób odrzucany. Zapora azure nie ostrzega o wszystkich znanych skanerach portów; tylko na skanerach, o których wiadomo, że również angażują się w szkodliwe działania.

## <a name="next-steps"></a>Następne kroki

- Zobacz [przykłady usługi Azure Firewall Log Analytics](log-analytics-samples.md)
- Dowiedz się, jak [wdrożyć i skonfigurować Zaporę platformy Azure](tutorial-firewall-deploy-portal.md)
- Przejrzyj [raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)
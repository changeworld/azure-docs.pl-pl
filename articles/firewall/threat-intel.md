---
title: Filtrowanie na podstawie analizy zagrożeń zapory platformy Azure
description: Filtrowanie oparte na analizie zagrożeń można włączyć dla zapory, aby wysyłać alerty oraz blokować ruch ze znanych złośliwych adresów IP i domen i do nich.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168677"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtrowanie na podstawie analizy zagrożeń zapory platformy Azure

Filtrowanie oparte na analizie zagrożeń można włączyć dla zapory, aby wysyłać alerty oraz blokować ruch ze znanych złośliwych adresów IP i domen i do nich. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) zapewnia program Microsoft Threat Intelligence i jest używany przez wiele usług, w tym Azure Security Center.

![Analiza zagrożeń zapory](media/threat-intel/firewall-threat.png)

W przypadku włączenia filtrowania opartego na analizie zagrożeń skojarzone reguły są przetwarzane przed dowolnymi regułami NAT, regułami sieciowymi lub regułami aplikacji.

Można wybrać opcję rejestrowania alertu, gdy reguła jest wyzwalana, lub wybrać opcję alert i tryb Odmów.

Domyślnie filtrowanie oparte na analizie zagrożeń jest włączone w trybie alertu. Nie można wyłączyć tej funkcji ani zmienić trybu do momentu udostępnienia interfejsu portalu w Twoim regionie.

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

- **Testowanie wychodzące** — alerty ruchu wychodzącego powinny być rzadkimi wystąpieniami, ponieważ oznacza to naruszenie bezpieczeństwa środowiska. Aby można było przetestować alerty wychodzące, została utworzona testowa nazwa FQDN, która wyzwala alert. Użyj **testmaliciousdomain.eastus.cloudapp.Azure.com** dla testów wychodzących.

- **Testowanie przychodzące** — możesz oczekiwać, że alerty dotyczące ruchu przychodzącego są wyświetlane, jeśli reguły DNAT są skonfigurowane na zaporze. Jest to prawdziwe, nawet jeśli tylko określone źródła są dozwolone w regule DNAT, a ruch jest odrzucany w inny sposób. Zapora platformy Azure nie ma alertu dotyczącego wszystkich znanych skanerów portów; tylko w przypadku skanerów, które są znane również w przypadku złośliwego działania.

## <a name="next-steps"></a>Następne kroki

- Zobacz [przykłady log Analytics zapory platformy Azure](log-analytics-samples.md)
- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md)
- Przejrzyj [Raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)
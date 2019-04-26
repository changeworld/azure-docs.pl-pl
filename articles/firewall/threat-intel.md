---
title: Filtrowaniu na podstawie analizy zagrożeń w usłudze Azure zapory
description: Więcej informacji na temat filtrowania analizy zagrożeń w zapory usługi Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194051"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Filtrowanie Azure zagrożeń zapory na podstawie analizy — publiczna wersja zapoznawcza

Filtrowanie oparte na analizie zagrożeń można włączyć dla zapory, aby wysyłać alerty oraz blokować ruch ze znanych złośliwych adresów IP i domen i do nich. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) uprawnienia analizy zagrożeń firmy Microsoft i jest używany przez wiele usług, w tym usługi Azure Security Center.

![Analiza zagrożeń zapory](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> Filtrowaniu na podstawie analizy zagrożeń jest obecnie dostępna w publicznej wersji zapoznawczej i jest dostarczana z wersji zapoznawczej Umowa dotycząca poziomu usług. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli włączono filtrowanie na podstawie analizy zagrożeń skojarzone reguły są przetwarzane przed zastosowaniem reguł NAT, reguł sieci lub reguł aplikacji. W trakcie okresu zapoznawczego uwzględniane są tylko najwyższy rekordy zaufania.

Można tylko zalogować alert, gdy reguła jest wyzwalana, lub wybierz alert i odmawianie trybu.

Domyślnie filtrowanie na podstawie analizy zagrożeń jest włączone w trybie alertu. Nie można wyłączyć tę funkcję, lub zmienić tryb, dopóki interfejs portalu staje się dostępna w Twoim regionie.

![Analiza zagrożeń na podstawie filtrowania interfejsie portalu](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Dzienniki

Poniższy fragment dziennika pokazuje wyzwalane reguły:

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

- **Testowanie wychodzącego** — ruch wychodzący alertów należy rzadkie wystąpienia, oznacza to, że naruszono bezpieczeństwo środowiska. Ułatwia alerty wychodzącego testu pracy, test utworzenia nazwy FQDN, który wyzwala alert. Użyj **testmaliciousdomain.eastus.cloudapp.azure.com** dla ruchu wychodzącego testów.

- **Dla ruchu przychodzącego testowania** — mogą oczekiwać wyświetlić alerty w zakresie ruchu przychodzącego, jeśli DNAT reguły zostały skonfigurowane w zaporze. Ta zasada obowiązuje, nawet jeśli tylko określone źródło są dozwolone w regule DNAT i ruch w przeciwnym razie zostanie odrzucone. Zapora usługi Azure alerty dotyczące wszystkich skanery znanym porcie; tylko w przypadku skanerów, które są znane również prowadzenia złośliwych działań.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [próbki analizy dzienników zapory platformy Azure](log-analytics-samples.md)
- Dowiedz się, jak [Wdróż i Skonfiguruj zaporę platformy Azure](tutorial-firewall-deploy-portal.md)
- Przegląd [Microsoft Security intelligence raportu](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)
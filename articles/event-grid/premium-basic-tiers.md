---
title: Warstwy Premium i Podstawowe usługi Azure Event Grid
description: W tym artykule opisano różnicę między warstwami Azure Event Grid Premium i Basic oraz kiedy należy używać poszczególnych warstw
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300718"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Warstwy premium i podstawowe usługi Azure Event Grid
Usługa Azure Event Grid ma dwie warstwy: **Premium** i **Basic**. Warstwa podstawowa używa zużycia lub ceny płatności zgodnie z rzeczywistym użyciem. Zapewnia wszystkie podstawowe narzędzia pub/sub potrzebne do korzystania z usługi Event Grid dla modeli programowania opartych na zdarzeniach. Warstwa premium idzie o krok dalej dzięki funkcjom zabezpieczeń skierowanym do przedsiębiorstwa. Warstwa premium jest we **wczesnej wersji zapoznawczej,** a wiele z jej funkcji jest wciąż opracowywych.

## <a name="overview"></a>Omówienie
Wszystkie niestandardowe tematy i domeny w uszkcie zdarzeń należą do warstwy podstawowej lub warstwy premium. Począwszy `2020-04-01-preview` od wersji interfejsu API, można wybrać żądaną warstwę jako część tworzenia tematu lub domeny. Wartością domyślną jest warstwa podstawowa. Tematy i domeny utworzone przy użyciu starszych wersji interfejsu API domyślnie do warstwy podstawowej. Aby zmienić warstwę cenową tematów i domen, zobacz [Jak zaktualizować warstwę tematów i domen](update-tier.md).

## <a name="capabilities-and-features"></a>Możliwości i funkcje

W poniższej tabeli opisano różnice między warstwami:

|       &nbsp;                                           | Podstawowa (Basic)           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| Reguły zapory IP dla ruchu przychodzącego                          | Wersja zapoznawcza  | Wersja zapoznawcza |
| Tagi usługi dla wyjścia                                | Wersja zapoznawcza  | Wersja zapoznawcza |
| Integracja prywatnej sieci wirtualnej punktu końcowego przy ruchu przychodzącym          | Niedostępne   | Wersja zapoznawcza |

## <a name="availability"></a>Dostępność
Podczas wstępnej wersji zapoznawczej tematy warstwy premium i domeny z integracją prywatnych punktów końcowych są dostępne w następujących regionach:

- Wschodnie stany USA
- Zachodnie stany USA 2
- Południowo-środkowe stany USA

## <a name="pricing-and-quotas"></a>Cennik i przydziały
Zobacz [cennik usługi Event Grid,](https://azure.microsoft.com/pricing/details/event-grid/) aby uzyskać szczegółowe informacje o cenach korzystania z warstwy podstawowej. Ceny warstwy Premium nie zostały jeszcze ogłoszone i są bezpłatne, dopóki ceny nie będą dostępne.

Istniejące przydziały na temat i liczbę domen i przepływność mają zastosowanie zarówno do zasobów warstwy premium, jak i podstawowej warstwy, dopóki nie zostanie ogłoszone ceny warstwy premium.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- Aby przejść z warstwy podstawowej do warstwy premium, zobacz artykuł [warstwy cenowej Aktualizowanie.](update-tier.md) 
- Zaporę IP dla zasobu usługi Event Grid można skonfigurować, aby ograniczyć dostęp przez publiczny Internet tylko z wybranego zestawu adresów IP lub zakresów adresów IP. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie zapory](configure-firewall.md).
- Można skonfigurować prywatne punkty końcowe, aby ograniczyć dostęp tylko z wybranych sieci wirtualnych. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie prywatnych punktów końcowych](configure-private-endpoints.md).
---
title: Cennik usługi Azure Network Performance Monitor — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania — Azure Network Performance Monitor
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 77cacd7f94d8ddd92fcd7383d2d0a7929734eaeb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59005936"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Zmiany cennika dla usługi Azure Network Performance Monitor

Firma Microsoft ma wysłuchaliśmy opinii użytkowników i niedawno wprowadziła [nowego cennika](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) dla różnych monitorowanie usług platformy Azure. W tym artykule przechwytuje zmianach cen. związane z platformą Azure [rozwiązania Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) w formie pytań i odpowiedzi łatwych do zrozumienia.

Rozwiązanie Network Performance Monitor obejmuje trzy składniki:
* [Monitor wydajności](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor punktu końcowego usługi](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

W poniższych sekcjach opisano zmiany cen dla składników programu NPM.

## <a name="performance-monitor"></a>Monitorowanie wydajności

**Jak oceniasz użycie monitora wydajności rozliczane starego modelu?**

Okres rozliczeniowy NPM zależała od użycia i zużycia dwa składniki:
* **Węzły**: Wszystkie transakcje syntetyczne pochodzą i zakończyć w węzłach. Węzły są również określane jako agentów lub agentów zarządzania firmy Microsoft.
* **Dane**: Wyniki badań różnych sieci są przechowywane w obszarze roboczym usługi Log Analytics.

W obszarze starego modelu rozliczenia została obliczona na podstawie liczby węzłów i ilość wygenerowanych danych. 

**Jak jest rozliczane użycie monitora wydajności w nowym modelu?**

Tej funkcji Monitora wydajności w programie NPM jest teraz rozliczana oparte na kombinacji: 

* Łącza podsieci monitorowane
* Ilość danych

**Co to jest link podsieci?**

Monitor wydajności monitoruje łączność między co najmniej dwóch lokalizacjach sieciowych. Połączenie między grupy węzłów lub agenci w jednej podsieci i Grupa węzłów, w innej podsieci, nosi nazwę łącza podsieci.

**Masz dwie podsieci, (A i B) i mam kilku agentów dla każdej podsieci. Monitor wydajności monitoruje łączność z wszystkich agentów w podsieci A wszystkich agentów w podsieci B. Zostanie naliczona na podstawie liczby połączeń między podsieci?**

Nie. Na potrzeby rozliczeń wszystkie połączenia z podsiecią A podsieci B są grupowane w jedno łącze podsieci. Są naliczane dla pojedynczego połączenia. Monitor wydajności w dalszym ciągu monitorować łączność między różnymi agentów w każdej podsieci.

**Co to są koszty monitorowania łącza podsieci?**

Koszty monitorowania łącze pojedynczej podsieci przez cały miesiąc, zobacz [polecenie Ping dla siatki](https://azure.microsoft.com/pricing/details/network-watcher/) sekcji.

**Jakie są opłaty za dane, które generuje monitora wydajności?**

Opłata za pozyskiwanie (przekazywanie danych do obszaru roboczego usługi Log Analytics, w usłudze Azure Monitor, przetwarzania i indeksowanie) jest dostępny na [stronę z cennikiem](https://azure.microsoft.com/pricing/details/log-analytics/) dla usługi Log Analytics w sekcji wprowadzanie danych. Opłata za przechowywanie danych (czyli dane zachowane w opcji klienta, poza pierwszym miesiącu) jest dostępna również na [stronę z cennikiem](https://azure.microsoft.com/pricing/details/log-analytics/), w sekcji przechowywania danych.


## <a name="expressroute-monitor"></a>Monitor usługi ExpressRoute

**Jakie są opłaty za użycie przez Monitor usługi ExpressRoute?**

Opłaty za Monitor usługi ExpressRoute są rozliczani według ilości danych wygenerowanych podczas monitorowania. Aby uzyskać więcej informacji zobacz sekcję "kiedy opłaty za dane, które generuje monitora wydajności?"

**Monitor usługi ExpressRoute I służy do monitorowania wielu obwodów usługi ExpressRoute. Czy jest naliczana na podstawie liczby monitorowane obwody?**

Nie są naliczane na podstawie liczba obwodów lub typu komunikacji równorzędnej (na przykład prywatnej komunikacji równorzędnej, komunikacja równorzędna firmy Microsoft). Opłaty są naliczane zależnie od ilości danych, jak wyjaśniono wcześniej.

**Co to jest ilość danych generowane, gdy usługa ExpressRoute monitoruje jednego obwodu?**

Ilość danych generowanych na miesiąc, gdy usługa ExpressRoute monitoruje prywatne połączenie komunikacji równorzędnej, jest następujący:

|Percentyl      |Dane/miesiąc (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Zgodnie z poniższą tabelą klientów na 50. percentyl płać 192 MB danych. W USD 2,30 USD/GB w pierwszym miesiącu, koszt monitorowania obwodu jest USD $0.43 (= 192 * 2,30 / 1024).

**Co to są przyczyny różnice w ilości danych?**

Ilość danych wygenerowanych monitorowania zależy od wielu czynników, takich jak:
* Liczba agentów. Zwiększa dokładność izolację awarii wraz ze wzrostem liczby agentów.
* Liczba przeskoków w sieci.
* Liczba ścieżek między źródłową i docelową.

Klienci w wyższej percentyle (w powyższej tabeli) zwykle monitorować jego obwodów z kilku punktów firmę vantage w swoich sieciach lokalnych. Wielu agentów są umieszczane również bardziej w sieci, dalej od routera krawędź dostawcy usługi. Agenci często są umieszczane w kilku lokacjach użytkownika, gałęzie i regałów w centrach danych.

## <a name="service-endpoint-monitor"></a>Monitor punktu końcowego usługi

**Jakie są opłaty za korzystanie z monitorowania punktu końcowego usługi?**

Opłaty za użycie monitorowania punktu końcowego usługi są obliczane na podstawie:
* Liczba połączeń
* Ilość danych

**Co to jest połączenie?**

Połączenie jest test osiągalności jeden punkt końcowy (adres URL lub sieci usługi) z jednego agenta przez cały miesiąc. Na przykład monitorowania połączenia do bing.com z trzech agentów definiuje trzy połączenia.

**Co to są kosztów monitorowania punktu końcowego usługi?**

Zapoznaj się [monitorowanie połączeń](https://azure.microsoft.com/pricing/details/network-watcher/) części kosztu monitorowania punktu końcowego dla całego miesiąca. Opłaty za dane są dostępne na [stronę z cennikiem](https://azure.microsoft.com/pricing/details/log-analytics/) dla usługi Log Analytics w sekcji wprowadzanie danych.

## <a name="references"></a>Dokumentacja

[Log Analytics, często zadawane pytania dotyczące cennika](https://azure.microsoft.com/pricing/details/log-analytics/): W sekcji często zadawane pytania dotyczące zawiera informacje o warstwie bezpłatnej, za węzeł, a także inne informacje o cenach.


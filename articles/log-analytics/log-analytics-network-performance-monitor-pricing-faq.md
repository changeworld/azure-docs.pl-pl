---
title: Cennik monitora wydajności sieci platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania — Monitor wydajności sieci platformy Azure
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 1e7e43dc2e7ed386f8f77fd1ab186d2ff34af405
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Zmiany cenowych dla monitora wydajności sieci platformy Azure

Firma Microsoft ma nasłuch opinii i niedawno wprowadzone [nowych cen środowisko](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) dla różnych monitorowanie usług w obrębie platformy Azure. W tym artykule przechwytuje cenową zmiany dotyczące usługi Azure [monitora wydajności sieci](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) w formacie łatwe do odczytania pytań i odpowiedzi.

Monitor wydajności sieci składa się z trzech składników:
* [Monitor wydajności](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor punktu końcowego usługi](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor)
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

W poniższych sekcjach opisano zmiany cenową dla składników programu NPM.

## <a name="performance-monitor"></a>Monitorowanie wydajności

**Jak jest użycie monitora wydajności rozliczane w starym modelu?**

Rozliczenia dla NPM został na podstawie użycia i zużycia dwa składniki:
* **Węzły**: wszystkie transakcje syntetyczne pochodzą i wygasać w węzłach. Węzły również są określane jako agenci lub agenci zarządzania firmy Microsoft.
* **Dane**: wyniki poszczególnych testów sieci są przechowywane w repozytorium Azure Log Analytics.

Zgodnie z modelem starego BOM została obliczona na podstawie liczby węzłów i ilości danych wygenerowanych. 

**Sposób użycia Monitora wydajności jest rozliczana zgodnie z modelem nowy**

Funkcja Monitor wydajności w NPM jest teraz rozliczane oparty na kombinacji: 

* Łącza podsieci monitorowane
* Ilość danych

**Co to jest łącze podsieci?**

Monitor wydajności monitoruje łączność między co najmniej dwie lokalizacje w sieci. Połączenie między grupą węzłów lub agenci w jednej podsieci i grupy węzłów w innej podsieci, nosi nazwę łącza podsieci.

**Masz dwie podsieci, (A i B) i można mieć kilka agentów w każdej podsieci. Monitor wydajności monitoruje łączność z wszystkich agentów w podsieci A wszystkich agentów w podsieci B. Zostanie I być naliczane na podstawie liczby połączeń między podsieci?**

Nie. Do celów rozliczeń wszystkie połączenia z podsiecią A podsieci B są pogrupowane w jednej podsieci łącze. Opłaty są naliczane dla pojedynczego połączenia. Monitor wydajności nadal monitorować łączność między różnymi agentów w każdej podsieci.

**Co to są koszty monitorowania łącza podsieci?**

Kosztów monitorowania łącze pojedynczej podsieci dla całego miesiąca, zobacz [siatki Ping](https://azure.microsoft.com/pricing/details/network-watcher/) sekcji.

**Co to są opłaty za dane, które generuje monitora wydajności?**

Opłata za wprowadzanie (przekazywanie danych do analizy dzienników, przetwarzania i indeksowanie) znajduje się na [cennikiem](https://azure.microsoft.com/pricing/details/log-analytics/) dla analizy dzienników, w sekcji wprowadzanie danych. Opłata za przechowywanie danych (oznacza to, że dane przechowywane w opcji klienta poza pierwszy miesiąc) jest również dostępna w [stronie dotyczącej cen](https://azure.microsoft.com/pricing/details/log-analytics/), w sekcji przechowywania danych.


## <a name="expressroute-monitor"></a>Monitor usługi ExpressRoute

**Co to są opłaty za użycie monitora ExpressRoute?**

Opłaty za usługi ExpressRoute monitora są rozliczane według ilości danych wygenerowanych podczas monitorowania. Aby uzyskać więcej informacji zobacz sekcję "kiedy opłat za dane, które generuje monitora wydajności?"

**Monitor usługi ExpressRoute I służy do monitorowania wielu obwody usługi ExpressRoute. Jestem daną opłatę zależności od liczby monitorowanych obwody?**

Możesz nie są naliczane na podstawie numer obwodów lub typ komunikacji równorzędnej (na przykład prywatnej komunikacji równorzędnej, komunikacji równorzędnej firmy Microsoft). Naliczane są opłaty zależności od ilości danych, jak opisano wcześniej.

**Co to jest ilości danych wygenerowanych podczas jednego obwodu monitoruje ExpressRoute?**

Ilości danych wygenerowanych miesięcznie, gdy ExpressRoute monitoruje prywatnej komunikacji równorzędnej połączenie, jest następujący:

|Percentyl      |Danych miesięcznie (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Zgodnie z tej tabeli klientów na 50. percentyl opłacać 192 MB danych. Co $2.30 USD/GB pierwszy miesiąc, kosztów ponoszonych monitorowania obwód jest USD 0.43 $ (= 192 * 2.30 / 1024).

**Co to są powodów zmienności ilość danych?**

Ilość danych wygenerowanych monitorowania zależy od wielu czynników, takich jak:
* Liczba agentów. Dokładność izolacji błędów zwiększa się wraz ze wzrostem liczby agentów.
* Liczba przeskoków w sieci.
* Liczba ścieżek między serwerem źródłowym a docelowym.

Klienci w wyższej percentylu (w powyższej tabeli) zwykle monitorować ich scalone z kilku punktów firmę vantage w swoich sieciach lokalnych. Wielu agentów są umieszczane również więcej danych w sieci, dalej od routera brzegowego dostawcy usługi. Agenci często są umieszczane w kilku lokacjach użytkownika, gałęzie i stojakami w centrach danych.

## <a name="service-endpoint-monitor"></a>Monitor punktu końcowego usługi

**Co to są opłaty za użycie Monitor punktu końcowego usługi?**

Opłaty za użycie Monitor punktu końcowego usługi jest obliczana na podstawie:
* Liczba połączeń
* ilość danych

**Co to jest połączenie?**

Połączenie jest test uzyskiwanie na jeden punkt końcowy (adres URL lub z usługami sieciowymi) z jednego agenta dla całego miesiąca. Na przykład monitorowanie połączenia do bing.com od trzech czynników stanowi trzy połączenia.

**Co to są koszty Monitor punktu końcowego usługi?**

Zapoznaj się [monitorowania połączenia](https://azure.microsoft.com/pricing/details/network-watcher/) sekcji kosztów monitorowania punkt końcowy dla całego miesiąca. Opłata za danych jest dostępne na [cennikiem](https://azure.microsoft.com/pricing/details/log-analytics/) dla analizy dzienników, w sekcji wprowadzanie danych.

## <a name="references"></a>Dokumentacja

[Często zadawane pytania dotyczące cen analizy dziennika](https://azure.microsoft.com/pricing/details/log-analytics/): często zadawane pytania dotyczące sekcja zawiera informacje o warstwę bezpłatna, na węzeł ceny i inne informacje o cenach.


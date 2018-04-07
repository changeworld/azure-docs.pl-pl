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
ms.openlocfilehash: 5b2335ee2584af07ed23ce87be92a869f3a07ba1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Zmiany cenowych dla monitora wydajności sieci platformy Azure

Firma Microsoft nasłuchiwanie swoją opinię i zostały niedawno wprowadzone [nowych cen środowisko](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), dla różnych monitorowanie usług w obrębie platformy Azure.

Ten dokument zawiera cenową zmiany dotyczące usługi Azure [monitora wydajności sieci](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM), w żadnym łatwy do odczytania pytań i odpowiedzi w formacie.

Monitor wydajności sieci składa się z trzech składników:
* [Monitor wydajności](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor punktu końcowego usługi](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor) i
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

Poniżej wyjaśniono cenową zmiany powyżej składników.

## <a name="performance-monitor-pm"></a>Monitor wydajności (PM)

**Jak jest użycie monitora wydajności rozliczane w starym modelu?**

Rozliczenia dla NPM został na podstawie użycia/dwa składniki:
* Węzły: Wszystkie transakcje syntetyczne pochodzą i wygasać w węzłach. Węzły są też określane jako agentów lub MMA (agenci zarządzania firmy Microsoft).
* Dane: Wyniki poszczególnych testów sieci są przechowywane w repozytorium analizy dzienników.

Zgodnie z modelem starego BOM została obliczona na podstawie liczby węzłów i ilości danych wygenerowanych. 

**W jaki sposób użycia Monitora wydajności, naliczane opłaty zgodnie z modelem nowe?**

Funkcja Monitor wydajności w NPM jest teraz rozliczane na kombinacji: 

* łącza podsieci monitorowane i
* Ilość danych

**Co to jest łącze podsieci?**

Monitor wydajności monitoruje łączność między co najmniej dwie lokalizacje w sieci.  Połączenie między grupy węzłów/agentów w jednej podsieci i grupy węzłów w innej podsieci, jest określone jako łącze podsieci.

**Istnieją dwie podsieci (podsieć A i B) i ma kilka agentów w każdej podsieci.  Monitor wydajności monitoruje łączność z wszystkich agentów w podsieci A wszystkich agentów w podsieci B.  Zostanie I być naliczane na podstawie liczby połączeń między podsieci?**

Nie. Do celów rozliczeń wszystkie połączenia z podsiecią A podsieci B są pogrupowane w jednej podsieci łącze i są rozliczane dla pojedynczego połączenia.  Monitor wydajności będzie monitorować łączność między różnymi agentów w każdej podsieci.

**Co to są koszty monitorowania łącza podsieci?**

Zapoznaj się z treścią sekcji [siatki Ping](https://azure.microsoft.com/pricing/details/network-watcher/) kosztów monitorowania łącze pojedynczej podsieci dla całego miesiąca.

**Co to są opłaty za dane generowane przez Monitor wydajności?**

Opłata za wprowadzanie (przekazywanie danych do analizy dzienników, przetwarzania i indeksowanie) jest dostępny na [cennikiem](https://azure.microsoft.com/pricing/details/log-analytics/) dla analizy dzienników.  (Sekcji: wprowadzanie danych).

Opłata za przechowywanie danych (oznacza to, że dane przechowywane w opcji klienta poza pierwszy miesiąc) jest również dostępna w [cennikiem](https://azure.microsoft.com/pricing/details/log-analytics/).  (Sekcji: przechowywanie danych).


## <a name="expressroute-monitor-erm"></a>Monitor usługi ExpressRoute (ERM)

**Co to są opłaty za użycie monitora ExpressRoute?**

Opłaty za usługi ExpressRoute monitora są rozliczane według ilości danych wygenerowanych podczas monitorowania.   Można znaleźć na pytanie "Co to są opłaty za dane generowane przez Monitor wydajności"? Aby uzyskać więcej informacji.

**ERM I służy do monitorowania wielu obwody usługi ExpressRoute. Jestem daną opłatę zależności od liczby monitorowanych obwody?**

Możesz nie są naliczane na podstawie numer obwodów lub typ komunikacji równorzędnej (na przykład prywatnej komunikacji równorzędnej, komunikacji równorzędnej firmy Microsoft).  Są naliczane na temat ilości danych, jak opisano powyżej.

**Co to jest ilości danych generowanych podczas monitorowania pojedynczego obwodu?**

Ilości danych wygenerowanych miesięcznie podczas monitorowania połączenie prywatnej komunikacji równorzędnej jest następujący:

|Percentyl      |Danych miesięcznie (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Na powyższej tabeli klientów na 50. percentyl opłacać 192 MB danych. Co $2.30 USD/GB pierwszy miesiąc, kosztów ponoszonych monitorowania obwód jest USD 0.43 (= 192 * 2.30 / 1024) za pierwszy miesiąc.

**Jakie są powody zmienności ilość danych?**

Ilość danych wygenerowanych monitorowania zależy od wielu czynników takich jak:
* Liczba agentów — dokładność izolacji błędów zwiększa się wraz ze wzrostem liczby agentów
* Liczba przeskoków w sieci
* Liczba ścieżek między serwerem źródłowym a docelowym

Klientów na wyższe percentylu (w powyższej tabeli), zwykle monitorować ich scalone z kilku punktów firmę vantage w swoich sieciach lokalnych.  Wielu agentów są umieszczane również więcej danych w sieci, dalej od routera brzegowego dostawcy usługi. Agenci często umieszczane w kilku lokacjach użytkownika, gałęzie i stojakami w centrach danych.

## <a name="service-endpoint-monitor-sepm"></a>Monitor punktu końcowego usługi (SEPM)

**Co to są opłaty za użycie Monitor punktu końcowego usługi?**

Opłaty za użycie Monitor punktu końcowego usługi jest obliczana na podstawie:
* Liczba połączeń
* ilość danych

**Co to jest połączenie?**

Połączenie jest test uzyskiwanie na jeden punkt końcowy (adres URL lub z usługami sieciowymi) z jednego agenta dla całego miesiąca. Na przykład monitorowanie połączenia do bing.com od trzech czynników stanowi trzy połączenia.

**Co to są koszty Monitor punktu końcowego usługi?**

- Zapoznaj się [monitorowania połączenia](https://azure.microsoft.com/pricing/details/network-watcher/) sekcji kosztów monitorowania punkt końcowy dla całego miesiąca.
- Opłata za danych jest dostępne na [cennikiem](https://azure.microsoft.com/pricing/details/log-analytics/) dla analizy dzienników.  (Sekcji: wprowadzanie danych).

## <a name="references"></a>Dokumentacja

- [Często zadawane pytania dotyczące cen analizy dziennika](https://azure.microsoft.com/pricing/details/log-analytics/) -sekcji często zadawanych PYTAŃ zawiera informacje o warstwę bezpłatna, każdy węzeł cenową itp.


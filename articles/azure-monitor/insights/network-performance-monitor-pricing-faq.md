---
title: Często zadawane pytania dotyczące cen w monitorze wydajności sieci platformy Azure | Dokumenty firmy Microsoft
description: Często zadawane pytania — Monitor wydajności sieci platformy Azure
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: c5a80c7ff6d08ce601a6c2bbb0db0ef866d3b425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654396"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Zmiany cen w Monitorze wydajności sieci platformy Azure

Wysłuchaliśmy twoich opinii i niedawno wprowadziliśmy [nowe środowisko cenowe](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) dla różnych usług monitorowania na platformie Azure. W tym artykule przechwytuje zmiany cen związane z Usługą Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) w łatwy do odczytania formacie pytania i odpowiedzi.

Monitor wydajności sieci składa się z trzech składników:
* [Monitorowanie wydajności](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor punktu końcowego usługi](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

W poniższych sekcjach wyjaśniono zmiany cen dla składników NPM.

## <a name="performance-monitor"></a>Monitorowanie wydajności

**W jaki sposób naliczane były opłaty za korzystanie z Monitora wydajności w starym modelu?**

Rozliczenia dla npm opiera się na użyciu i zużyciu dwóch składników:
* **Węzły:** Wszystkie transakcje syntetyczne pochodzą i kończą się w węzłach. Węzły są również określane jako agenci lub agenci zarządzania firmy Microsoft.
* **Dane:** Wyniki różnych testów sieciowych są przechowywane w obszarze roboczym usługi Log Analytics.

W starym modelu rachunek został obliczony na podstawie liczby węzłów i ilości wygenerowanych danych. 

**W jaki sposób korzystanie z Monitora wydajności jest ładowane w nowym modelu?**

Funkcja Monitora wydajności w serwerze NPM jest teraz rozliczana na podstawie kombinacji: 

* Monitorowane łącza podsieci
* Ilość danych

**Co to jest łącze podsieci?**

Monitor wydajności monitoruje łączność między co najmniej dwiema lokalizacjami w sieci. Połączenie między grupą węzłów lub agentów w jednej podsieci a grupą węzłów w innej podsieci jest nazywane łączem podsieci.

**Mam dwie podsieci (A i B) i mam kilka agentów w każdej podsieci. Monitor wydajności monitoruje łączność ze wszystkimi agentami w podsieci A do wszystkich agentów w podsieci B. Czy naliczane są opłaty na podstawie liczby połączeń między podsieci?**

Nie. Do celów rozliczeniowych wszystkie połączenia z podsieci A do podsieci B są zgrupowane w jedno łącze podsieci. Naliczane są naliczane płatności za jedno połączenie. Monitor wydajności w dalszym ciągu monitoruje łączność między różnymi agentami w każdej podsieci.

**Jakie są koszty monitorowania łącza podsieci?**

Koszt monitorowania pojedynczego łącza podsieci dla całego miesiąca można znaleźć w sekcji [Siatka ping.](https://azure.microsoft.com/pricing/details/network-watcher/)

**Jakie są opłaty za dane generowane przez Monitor wydajności?**

Opłata za pobłażanie (przekazywanie danych do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor, przetwarzanie i indeksowanie) jest dostępna na [stronie cenniku](https://azure.microsoft.com/pricing/details/log-analytics/) usługi Log Analytics w sekcji Pozyskiwania danych. Opłata za przechowywanie danych (czyli dane przechowywane w zależności od klienta, poza pierwszym miesiącem) jest również dostępna na [stronie cenowej](https://azure.microsoft.com/pricing/details/log-analytics/)w sekcji Przechowywanie danych.


## <a name="expressroute-monitor"></a>ExpressRoute Monitor

**Jakie są opłaty za korzystanie z monitora usługi ExpressRoute?**

Opłaty za monitor usługi ExpressRoute są naliczane na podstawie ilości danych generowanych podczas monitorowania. Aby uzyskać więcej informacji, zobacz "Jakie są opłaty za dane generowane przez Monitor wydajności?"

**Używam monitora usługi ExpressRoute monitor do monitorowania wielu obwodów usługi ExpressRoute. Czy naliczane są opłaty na podstawie liczby monitorowanych obwodów?**

Opłaty nie są naliczane na podstawie liczby obwodów lub typu komunikacji równorzędnej (na przykład prywatnej komunikacji równorzędnej, komunikacji równorzędnej firmy Microsoft). Opłata jest naliczana na podstawie ilości danych, jak wyjaśniono wcześniej.

**Jaka jest ilość danych generowanych, gdy usługa ExpressRoute monitoruje pojedynczy obwód?**

Ilość danych generowanych miesięcznie, gdy usługa ExpressRoute monitoruje prywatne połączenie komunikacji równorzędnej, jest następująca:

|Percentyl      |Dane/miesiąc (MB)|
| :---:          |           ---:|
|50<sup>tys.</sup> |            192|
|60<sup>tys.</sup> |            256|
|70<sup>tys.</sup> |            360|
|80<sup>tys.</sup> |            498|
|90<sup>tys.</sup> |            870|
|<sup>95.</sup> |           1560|


Według tej tabeli, klienci na 50 percentyl zapłacić za 192 MB danych. W przypadku 2,30 USD/GB za pierwszy miesiąc koszt monitorowania obwodu wynosi 0,43 USD (= 192 * 2,30 / 1024).

**Jakie są przyczyny zmian w ilości danych?**

Ilość generowanych danych monitorowania zależy od kilku czynników, takich jak:
* Liczba agentów. Dokładność izolacji usterek wzrasta wraz ze wzrostem liczby agentów.
* Liczba przeskoków w sieci.
* Liczba ścieżek między źródłem a miejscem docelowym.

Klienci o wyższych percentyli (w powyższej tabeli) zwykle monitorują swoje obwody z kilku punktów widzenia w sieci lokalnej. Wielu agentów jest również umieszczanych głębiej w sieci, dalej od routera brzegowego dostawcy usług. Agenci są często umieszczane w kilku witrynach użytkowników, oddziałach i stojakach w centrach danych.

## <a name="service-endpoint-monitor"></a>Monitor punktu końcowego usługi

**Jakie są opłaty za korzystanie z Service Endpoint Monitor?**

Opłaty za korzystanie z Service Endpoint Monitor są obliczane na podstawie:
* Liczba połączeń
* Ilość danych

**Co to jest połączenie?**

Połączenie jest testem osiągalności do jednego punktu końcowego (URL lub usługi sieciowej) z jednego agenta przez cały miesiąc. Na przykład monitorowanie połączenia z bing.com z trzech agentów stanowi trzy połączenia.

**Jakie są koszty monitora punktu końcowego usługi?**

Zobacz sekcję [Monitorowanie połączenia,](https://azure.microsoft.com/pricing/details/network-watcher/) aby uzyskać koszt monitorowania punktu końcowego dla całego miesiąca. Opłata za dane jest dostępna na [stronie cenniku](https://azure.microsoft.com/pricing/details/log-analytics/) usługi Log Analytics w sekcji Połkowanie danych.

## <a name="references"></a>Dokumentacja

[Często zadawane pytania dotyczące cen usługi Log Analytics:](https://azure.microsoft.com/pricing/details/log-analytics/)Sekcja często zadawanych pytań zawiera informacje o warstwie bezpłatnej, cenach za węzeł i innych szczegółach cen.


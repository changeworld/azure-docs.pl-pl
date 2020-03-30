---
title: Przykład wykresu metrycznego usługi Azure Monitor
description: Dowiedz się więcej o wizualizacji danych usługi Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 1aaeb853a67b36a21a09db57e015029d10cd0c36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660874"
---
# <a name="metric-chart-examples"></a>Przykłady wykresów metrycznych 

Platforma Azure oferuje [ponad tysiąc metryk,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)z których wiele ma wymiary. Za pomocą [filtrów wymiarów,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)stosowania [podziału,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)kontrolowania typu wykresu i dostosowywania ustawień wykresu można tworzyć zaawansowane widoki diagnostyczne i pulpity nawigacyjne, które zapewniają wgląd w kondycję infrastruktury i aplikacji. W tym artykule przedstawiono kilka przykładów wykresów, które można utworzyć za pomocą [Eksploratora metryk](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) i wyjaśniono kroki niezbędne do skonfigurowania każdego z tych wykresów.

Chcesz podzielić się swoimi przykładami wykresów ze światem? Udostępnij tę stronę na GitHubie i podziel się swoimi przykładami wykresów tutaj!

## <a name="website-cpu-utilization-by-server-instances"></a>Wykorzystanie procesora w witrynie sieci Web przez wystąpienia serwera

Ten wykres pokazuje, czy procesor CPU dla usługi App Service mieści się w dopuszczalnym zakresie i dzieli go według instancji, aby ustalić, czy obciążenie zostało prawidłowo rozłożone. Na wykresie widać, że aplikacja była uruchomiona na wystąpieniu jednego serwera przed 6:00, a następnie skalowana w górę, dodając kolejne wystąpienie.

![Wykres liniowy średniej wartości procentowej procesora według wystąpienia serwera](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

Wybierz zasób usługi App Service i znajdź metrykę **Procent procesora CPU.** Następnie kliknij **zastosuj podział** i wybierz wymiar **Wystąpienie.**

## <a name="application-availability-by-region"></a>Dostępność aplikacji według regionu

Wyświetl dostępność aplikacji według regionów, aby określić, które lokalizacje geograficzne mają problemy. Na tym wykresie przedstawiono metrykę dostępności usługi Application Insights. Widać, że monitorowana aplikacja nie ma problemu z dostępnością z centrum danych wschodnich stanów USA, ale występuje problem z częściową dostępnością z zachodnie stany USA i Azji Wschodniej.

![Wykres średniej dostępności według lokalizacji](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

Najpierw musisz włączyć monitorowanie dostępności usługi Application Insights dla swojej [witryny](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) sieci Web. Następnie wybierz zasób usługi Application Insights i wybierz metrykę Dostępność. Zastosuj podział w wymiarze **Lokalizacja uruchom.**

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Wolumen transakcji na koncie magazynu według nazwy interfejsu API

Zasób konta magazynu doświadcza nadmiaru transakcji. Metryka transakcji służy do identyfikowania, który interfejs API jest odpowiedzialny za nadmiar obciążenia. Należy zauważyć, że następujący wykres jest skonfigurowany z tym samym wymiarem (nazwa interfejsu API) w filtrowaniu i dzieleniu, aby zawęzić widok tylko do wywołań interfejsu API zainteresowania:

![Wykres słupkowy transakcji API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

W selektorze danych wybierz konto magazynu i metrykę **Transakcje.** Przełącz typ wykresu na **wykres słupkowy**. Kliknij **pozycję Zastosuj dzielenie** i wybierz **nazwę interfejsu API**wymiaru . Następnie kliknij **filtr Dodaj** i ponownie wybierz wymiar **nazwy interfejsu API.** W oknie dialogowym filtru wybierz interfejsy API, które chcesz wykreślić na wykresie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [skoroszytach monitora](../../azure-monitor/app/usage-workbooks.md) Platformy Azure
* Dowiedz się więcej o [Eksploratorze metryk](metrics-charts.md)

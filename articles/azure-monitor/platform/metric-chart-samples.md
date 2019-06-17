---
title: Przykłady wykresu metryki w usłudze Azure Monitor
description: Więcej informacji na temat wizualizowania danych usługi Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: bbfeb428d38c23955df4497242184499349aecf9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60256717"
---
# <a name="metric-chart-samples"></a>Przykłady wykresu metryki

Oferowane przez platformę Azure [przy użyciu metryk tysiąc](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)Many, z którego ma wymiarów. Za pomocą [wymiaru filtry](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), stosowanie [podział](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), kontrolę typ wykresu i dostosowywanie ustawień wykresu, można tworzyć zaawansowane widoki diagnostyczne i pulpity nawigacyjne, które zapewniają wgląd w kondycję Infrastruktura i aplikacje. W tym artykule przedstawiono kilka przykładów wykresów, które można tworzyć przy użyciu [Eksploratora metryk](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) oraz wyjaśniono czynności niezbędnych do Skonfiguruj każdy z tych wykresach.

Chcesz udostępnić przykłady doskonałe wykresy ze światem? Przyczynia się do tej strony w witrynie GitHub i udostępniać własne przykłady wykresu!

## <a name="website-cpu-utilization-by-server-instances"></a>Użycie witryny sieci Web procesora CPU przez wystąpienia serwera

Ten wykres przedstawia Jeśli procesora CPU dla usługi App Service została akceptowalnym zakresem i dzieli przez wystąpienie, aby ustalić, czy obciążenia został prawidłowo rozproszonych. Możesz zobaczyć wykres, który aplikacja była uruchomiona na wystąpieniu pojedynczego serwera przed 6: 00, a następnie przeskalowała w górę, dodając inne wystąpienie.

![Wykres liniowy z średni procent użycia procesora cpu przez wystąpienie serwera](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

Wybierz zasób usługi App Service i Znajdź **procent użycia procesora CPU** metryki. Następnie kliknij pozycję **zastosować podział** i wybierz **wystąpienia** wymiaru.

## <a name="application-availability-by-region"></a>Dostępność aplikacji według regionów

Wyświetl dostępność aplikacji według regionów do identyfikowania lokalizacji geograficznej, w których występują problemy. Ten wykres przedstawia metryki dostępności usługi Application Insights. Widać, że monitorowanej aplikacji nie ma żadnych problemów z dostępnością z centrum danych wschodnie stany USA, ale jej ma problem częściowe dostępności z regionu zachodnie stany USA oraz Azja Wschodnia.

![Wykres przedstawiający średni dostępności według lokalizacji](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

Najpierw należy włączyć [dostępności usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) monitorowania dla witryny sieci Web. Następnie wybierz zasób usługi Application Insights, a następnie wybierz metrykę dostępności. Zastosuj dzielenie na **lokalizacja uruchamiania** wymiaru.

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Ilości transakcji konta magazynu, według nazwy interfejsu API

Zasób konta magazynu występuje nadmierna ilość transakcji. Metryka transakcji umożliwia zidentyfikowanie interfejsu API jest odpowiedzialny za nadmiernego obciążenia. Zwróć uwagę, że poniższy wykres jest skonfigurowany z tego samego wymiaru (nazwa interfejsu API) filtrowania i dzielenia, aby zawęzić widok, aby tylko wywołania interfejsu API odsetek:

![Wykres słupkowy transakcji interfejsu API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

W selektorze metryki, wybierz konto magazynu i **transakcji** metryki. Typ wykresu, aby przełączyć **wykres słupkowy**. Kliknij przycisk **zastosować podział** i wybierz wymiar **Nazwa interfejsu API**. Następnie kliknij pozycję **Dodaj filtr** i pobrania **Nazwa interfejsu API** wymiaru jeszcze raz. W oknie dialogowym filtrowania wybierz interfejsów API, które ma zostać wykreślony na wykresie.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o usłudze Azure Monitor [skoroszytów](../../azure-monitor/app/usage-workbooks.md)
* Dowiedz się więcej o [Eksploratora metryk](metrics-charts.md)
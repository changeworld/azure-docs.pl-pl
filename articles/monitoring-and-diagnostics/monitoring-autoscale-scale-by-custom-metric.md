---
title: Skalowania automatycznego na platformie Azure przy użyciu metryki niestandardowe
description: Dowiedz się, jak skalować zasób według metryki niestandardowe na platformie Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 739ef5423f7b1769fa793f0cac5306efa634b781
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262977"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Rozpoczynanie pracy z Skalowanie automatyczne według metryki niestandardowe na platformie Azure
W tym artykule opisano sposób skalowania zasobu przez niestandardowa Metryka w portalu Azure.

Azure automatyczne skalowanie monitora dotyczy tylko zestawów skali maszyny wirtualnej (VMSS), usługi w chmurze, planów usługi aplikacji i środowiska usługi app service. 

# <a name="lets-get-started"></a>Umożliwia wprowadzenie
W tym artykule założono, że aplikacja sieci web z usługą application insights skonfigurowany. Jeśli nie masz już, możesz [skonfiguruj usługę Application Insights dla witryny sieci Web ASP.NET][1]

- Otwórz [portalu Azure][2]
- Kliknij ikonę monitora Azure w lewym okienku nawigacji.
  ![Uruchom Azure Monitor][3]
- Kliknij przycisk Ustawienia skalowania automatycznego, aby wyświetlić wszystkie zasoby, dla których automatycznego skalowania ma zastosowanie, wraz z jego bieżącym stanie skalowania automatycznego ![odnajdywanie automatyczne skalowanie w Monitorze systemu Azure][4]
- Otwarcie bloku "Skalowania automatycznego" w monitorze Azure i wybierz zasób, który chcesz skalować
> Uwaga: Czynności użyć plan usługi aplikacji skojarzonych z aplikacji sieci web, który ma skonfigurowane insights aplikacji.
- W bloku Ustawienia skalowania dla zasobu należy zauważyć, że bieżąca liczba wystąpień jest 1. Kliknij pozycję "Włącz skalowania automatycznego".
  ![Ustawienia skalowania dla nowej aplikacji sieci web][5]
- Podaj nazwę dla ustawienia skalowania, a następnie kliknij przycisk "Dodaj regułę". Zwróć uwagę, opcje reguły skalowania, które Otwiera okienko kontekstu po prawej stronie. Domyślnie ustawia opcję Skalowanie liczba wystąpień, 1, jeśli percetage Procesora zasobu przekracza 70%. Zmiany źródła metryki u góry "Usługi Application Insights", wybierz zasób insights aplikacji na liście rozwijanej "Resource", a następnie wybierz niestandardowa Metryka oparte na którym ma być skalowana.
  ![Skalować według metryki niestandardowe][6]
- Podobnie jak powyżej, Dodaj regułę Skala będzie skalować w i zmniejszyć liczbę skali, 1, jeśli niestandardowa metryka jest poniżej wartości progowej.
  ![Oparte na procesorze skali][7]
- Można ustawić limity wystąpienia. Na przykład jeśli chcesz skalować między wystąpieniami 2-5, w zależności od niestandardowe metryki wahań ustawić minimalne, aby "2", maksymalny, "5" i "default" do "2"
> Uwaga: W przypadku, gdy istnieje problem z odczytem metryki zasobu i jest obecna pojemność poniżej pojemności domyślnej, następnie zapewnienie dostępności zasobów, automatycznego skalowania zostanie skalowanie w poziomie przy użyciu wartości domyślnej. Jeśli pojemność bieżąca już jest wyższa niż wydajność domyślna, skalowania automatycznego nie będzie skalować w.
- Kliknij przycisk "Zapisz"

Gratulacje. Możesz teraz został pomyślnie utworzony na skalę ustawienie automatyczne skalowanie aplikacji sieci web, w oparciu metryki niestandardowe.

> Uwaga: Te same działania mają zastosowanie do Rozpoczynanie pracy z rolą usługi VMSS lub w chmurze.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png

---
title: Automatyczne skalowanie na platformie Azure przy użyciu metryki niestandardowej
description: Dowiedz się, jak skalować zasób według metryki niestandardowej na platformie Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 7758c440c75af5819099110dcbdaf5a86a1d2a04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425123"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Wprowadzenie do automatycznego skalowania według metryki niestandardowej na platformie Azure
W tym artykule opisano sposób skalowania zasobu według metryki niestandardowej w witrynie Azure Portal.

Skalowanie automatyczne usługi Azure Monitor dotyczy tylko [zestawów skalowania maszyn wirtualnych,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [usług w chmurze](https://azure.microsoft.com/services/cloud-services/), [usługi aplikacji — aplikacje sieci Web,](https://azure.microsoft.com/services/app-service/web/) [klastra eksploratora danych platformy Azure](https://azure.microsoft.com/services/data-explorer/) ,   
Usługi integracji środowiska i [usługi zarządzania interfejsami API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Zacznijmy od pracy
W tym artykule przyjęto założenie, że masz aplikację sieci web z skonfigurowaną statystyką aplikacji. Jeśli jeszcze go nie masz, możesz [skonfigurować aplikację Application Insights dla witryny ASP.NET][1]

- Otwórz [witrynę Azure portal][2]
- Kliknij ikonę usługi Azure Monitor w lewym okienku nawigacji.
  ![Uruchamianie usługi Azure Monitor][3]
- Kliknij ustawienie Skalowanie automatyczne, aby wyświetlić wszystkie zasoby, dla których ![ma zastosowanie skalowanie automatyczne, wraz z bieżącym stanem skalowania automatycznego Odkryj automatyczne skalowanie w monitorze platformy Azure][4]
- Otwórz blok "Skalowanie automatyczne" w usłudze Azure Monitor i wybierz zasób, który chcesz skalować
  > Uwaga: Poniższe kroki korzystają z planu usługi aplikacji skojarzonego z aplikacją sieci Web, która ma skonfigurowane statystyki aplikacji.
- W bloku ustawienia skali zasobu należy zauważyć, że bieżąca liczba wystąpień wynosi 1. Kliknij na "Włącz skalowanie automatyczne".
  ![Ustawienie skalowania dla nowej aplikacji sieci Web][5]
- Podaj nazwę ustawienia skali i kliknij "Dodaj regułę". Zwróć uwagę na opcje reguły skalowania, która otwiera się jako okienko kontekstu po prawej stronie. Domyślnie ustawia opcję skalowania liczby wystąpień o 1, jeśli procent procesora CPU zasobu przekracza 70%. Zmień źródło metryki u góry na "Usługa Wgląd w aplikacje", wybierz zasób wglądu w aplikacje w rozwijaniu "Zasób", a następnie wybierz metrykę niestandardową, na podstawie której chcesz skalować.
  ![Skalowanie według metryki niestandardowej][6]
- Podobnie jak w powyższym kroku, dodaj regułę skali, która będzie skalować i zmniejszać liczbę skalowania o 1, jeśli metryka niestandardowa jest poniżej progu.
  ![Skala oparta na procesorze][7]
- Ustaw limity wystąpień. Na przykład, jeśli chcesz skalować od 2 do 5 wystąpień w zależności od niestandardowych wahań metryk, ustaw "minimum" na "2", "maksimum" na "5" i "domyślnie" na "2"
  > Uwaga: W przypadku wystąpienia problemu z odczytaniem metryk zasobów, a bieżąca pojemność jest niższa od domyślnej pojemności, a następnie, aby zapewnić dostępność zasobu, skalowanie automatyczne będzie skalowane w poziomie do wartości domyślnej. Jeśli bieżąca pojemność jest już większa niż domyślna pojemność, skalowanie automatyczne nie zostanie skalowane.
- Kliknij na "Zapisz"

Gratulacje. Teraz pomyślnie utworzono ustawienie skalowania, aby automatycznie skalować aplikację sieci web na podstawie metryki niestandardowej.

> Uwaga: Te same kroki mają zastosowanie do rozpoczęcia pracy z rolą usługi VMSS lub usługi w chmurze.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png


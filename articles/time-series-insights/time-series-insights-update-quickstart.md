---
title: 'Szybki start: eksplorowanie środowiska demonstracyjnego w wersji zapoznawczej — usługa Azure Time Series Insights | Dokumenty firmy Microsoft'
description: Poznaj najważniejsze funkcje środowiska demonstracyjnyego usługi Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 02/07/2020
ms.openlocfilehash: dc4a8da69b0398c6487008c106a9f5bcdb8a885e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77110237"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Szybki start: eksplorowanie środowiska demonstracyjnyego usługi Azure Time Series Insights Preview

Ten przewodnik Szybki start rozpoczyna pracę ze środowiskiem usługi Azure Time Series Insights Preview. W bezpłatnym pokazie możesz zapoznać się z kluczowymi funkcjami, które zostały dodane do usługi Time Series Insights Preview.

Środowisko demo usługi Time Series Insights Preview zawiera firmę contoso, która obsługuje dwie farmy turbin wiatrowych. W każdym gospodarstwie jest 10 turbin. Każda turbina jest wyposażona w 20 czujników raportujących dane co minutę do usługi Azure IoT Hub. Czujniki zbierają informacje o warunkach pogodowych, skoku ostrza i położeniu odchylenia. Rejestrowane są również informacje o wydajności generatora, zachowaniu skrzyni biegów i monitorach bezpieczeństwa.

W tym przewodniku Szybki start dowiesz się, jak korzystać z usługi Time Series Insights w celu znajdowania szczegółowych informacji z zasysanych informacji w danych contoso. Przeprowadzasz również krótką analizę przyczyn źródłowych, aby lepiej przewidzieć krytyczne awarie i wykonać konserwację.

> [!IMPORTANT]
> Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) platformy Azure, jeśli go nie masz.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Poznawanie eksploratora usługi Time Series Insights w środowisku pokazowym

Eksplorator usługi Time Series Insights Preview demonstruje dane historyczne i analizę przyczyn źródłowych. Aby rozpocząć:

1. Przejdź do środowiska [demonstracyjnego Farmy wiatrowej Contoso.](https://insights.timeseries.azure.com/preview/samples)  

1. Jeśli zostanie wyświetlony monit, zaloguj się do Eksploratora usługi Time Series Insights przy użyciu poświadczeń konta platformy Azure.

## <a name="work-with-historical-data"></a>Praca z danymi historycznymi

1. W **Contoso Plant 1**, spójrz na turbinę wiatrową **W7**.  

   1. Zmień zakres widoku na **1/1/17 20:00:00.00 na 3/10/17 20:00:00.00 (UTC)**.
   1. Aby wybrać czujnik, wybierz **Contoso Plant 1** > **W7** > **Generator System** > **GeneratorSpeed**. Następnie przejrzyj wyświetlane wartości.

      [![W7 w zakładzie Contoso 1](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. Niedawno firma Contoso wykryła pożar w turbinie **W7**. Opinie są różne na temat tego, co spowodowało pożar. W usłudze Time Series Insights wyświetlany jest czujnik alarmu pożarowego, który został aktywowany podczas pożaru.

   1. Zmień zakres widoku na **3/9/17 20:00:00.00 na 3/10/17 20:00:00.00 (UTC)**.
   1. Wybierz **system** > bezpieczeństwa**FireAlert**.

      [![Contoso znalazł pożar w turbinie wiatrowej W7](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Przejrzyj inne zdarzenia w czasie pożaru, aby zrozumieć, co się stało. Ciśnienie oleju i aktywne ostrzeżenia wzrosła tuż przed pożarem.

   1. Wybierz **system** > skoku**HydraulicOilPressure**.
   1. Wybierz **opcję System tonów** > **ActiveWarning**.

      [![Przeglądanie innych zdarzeń w tym samym czasie](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Ciśnienie oleju i aktywne czujniki ostrzegawcze wzrosły tuż przed pożarem. Rozwiń wyświetlane szeregi czasowe, aby przejrzeć inne znaki, które były widoczne prowadzące do pożaru. Oba czujniki zmieniały się z czasem. Wahania wskazują na trwały i niepokojący wzór.

    * Zmień zakres widoku na **2/24/17 20:00:00.00 na 3/10/17 20:00:00.00 (UTC)**.

      [![Ciśnienie oleju i aktywne czujniki ostrzegawcze również](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Badanie dwóch lat danych historycznych ujawnia kolejne zdarzenie pożarowe, które miało takie same wahania czujników.

    * Zmień zakres widoku na **1/1/16 na 12/31/17** (wszystkie dane).

      [![Szukaj historycznych wzorów](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Korzystając z usługi Time Series Insights i telemetrii czujników, odkryliśmy długoterminowy trend ukryty w danych historycznych. Dzięki tym nowym spostrzeżeniam możemy:

* Wyjaśnij, co tak naprawdę się wydarzyło.
* Rozwiąż problem.
* Umieść lepsze systemy powiadamiania o alertach.

## <a name="root-cause-analysis"></a>Analiza głównej przyczyny

1. Niektóre scenariusze wymagają zaawansowanej analizy, aby odkryć wskazówki w danych. Wybierz wiatrak **W6** w dniu **6/25**.

    1. Zmień zakres widoku na **6/1/17 20:00:00.00 na 7/1/17 20:00:00.00 (UTC)**.
    1. Wybierz **Contoso Plant 1** > **W6** > System**bezpieczeństwa** > **VoltageActuatorSwitchWarning**.

       [![Zmienianie zakresu widoku i wybieranie W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. Ostrzeżenie wskazuje problem z napięciem z generatora. Całkowita moc wyjściowa generatora mieści się w normalnych parametrach w bieżącym przedziale. Poprzez zwiększenie naszego interwału wyłania się inny wzór. Drop-off jest oczywiste.

    1. Zdejmij czujnik **VoltageActuatorSwitchWarning.**
    1. Wybierz **system generatora** > **ActivePower**.
    1. Zmień interwał na **3d**.

       [![Zmiana interwału na 3d](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Rozszerzając zakres czasu, możemy określić, czy problem został zatrzymany lub czy jest kontynuowany.

    * Wydłuż okres do 60 dni.

      [![Wydłużenie okresu do 60 dni](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Inne punkty danych czujnika można dodać, aby zapewnić większy kontekst. Im więcej czujników oglądamy, tym pełniejsze jest nasze zrozumienie problemu. Upuśćmy znacznik, aby wyświetlić rzeczywiste wartości. 

    1. Wybierz **system generatora**, a następnie wybierz trzy czujniki: **GridVoltagePhase1**, **GridVoltagePhase2**i **GridVoltagePhase3**.
    1. Ustaw znacznik na ostatnim punkcie danych w widocznym obszarze.

       [![Upuść znacznik](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Dwa czujniki napięcia działają porównywalnie i w normalnych parametrach. Wygląda na to, **gridvoltagephase3** czujnik jest winowajcą.

1. Po dodaniu wysoce kontekstowych danych, spadek fazy 3 wydaje się jeszcze bardziej być problemem. Teraz mamy dobrą przewagę nad przyczyną ostrzeżenia. Jesteśmy gotowi skierować sprawę do naszego zespołu konserwacyjnego.  

    * Zmień wyświetlacz, aby nałożyć wszystkie czujniki **systemu generatora** na tej samej skali wykresu.

      [![Zmień wyświetlacz tak, aby uwzględniał wszystko](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu szybkiego startu wyczyść utworzone zasoby:

1. Z lewego menu w [witrynie Azure portal](https://portal.azure.com)wybierz **pozycję Wszystkie zasoby**i znajdź grupę zasobów usługi Azure Time Series Insights.
1. Usuń całą grupę zasobów (i wszystkie zasoby w niej zawarte), wybierając **pozycję Usuń** lub usuń każdy zasób indywidualnie.

## <a name="next-steps"></a>Następne kroki

Możesz przystąpić do tworzenia własnego środowiska usługi Time Series Insights Preview. Aby rozpocząć:

> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Time Series Insights w wersji zapoznawczej](time-series-insights-update-plan.md)

Naucz się korzystać z wersji demonstracyjnej i jego funkcji:

> [!div class="nextstepaction"]
> [Eksplorator wglądu w dane dotyczące szeregów czasowych](time-series-insights-update-explorer.md)
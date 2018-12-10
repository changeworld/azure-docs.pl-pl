---
title: Poznawanie środowiska pokazowego usługi Azure Time Series Insights (wersja zapoznawcza) | Microsoft Docs
description: Omówienie środowiska pokazowego usługi Azure Time Series Insights (wersja zapoznawcza)
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 12/03/2018
ms.openlocfilehash: 76bdc122cab5b4ee87717c9c4df9774ecc3bd8f7
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888787"
---
# <a name="explore-the-azure-time-series-insights-preview-demo-environment"></a>Poznawanie środowiska pokazowego usługi Azure Time Series Insights (wersja zapoznawcza)

W tym przewodniku Szybki start pokazano, jak rozpocząć pracę z eksploratorem wersji zapoznawczej usługi Azure Time Series Insights (TSI) w bezpłatnym środowisku pokazowym. Dowiesz się, jak przy użyciu przeglądarki internetowej wizualizować duże ilości historycznych przemysłowych danych IoT, i poznasz najważniejsze funkcje eksploratora usługi Azure Time Series Insights (wersja zapoznawcza).

Usługa Azure TSI to kompletna oferta platformy jako usługi umożliwiająca pozyskiwanie, przetwarzanie, przechowywanie i odpytywanie danych o wąskim kontekście, zoptymalizowanych pod kątem szeregów czasowych, w skali Internetu rzeczy na potrzeby badania danych ad hoc i analizy operacyjnej. Usługa Time Series Insights to zróżnicowana oferta dostosowana do unikatowych potrzeb przemysłowych wdrożeń technologii IoT.

W środowisku pokazowym przedstawiono firmę Contoso produkującą energię elektryczną, która używa usługi TSI do analizowania danych pod kątem określania możliwych do wykonania działań oraz do przeprowadzania skróconej analizy głównej przyczyny. Firma Contoso zarządza dwiema farmami wiatrowymi. Każda z nich składa się z 10 turbin, przy czym każda turbina jest wyposażona w 20 czujników raportujących dane co minutę do usługi Azure IoT Hub. Czujniki zbierają informacje o warunkach pogodowych, pochyleniu i odchyleniu łopat, wydajności generatora, zachowaniu przekładni i monitorach bezpieczeństwa.

Usługa TSI (wersja zapoznawcza) jest używana do analizowania ciągle rosnącego zestawu danych z ostatnich dwóch lat — obecnie 40 GB — w celu lepszego poznania i przewidywania zarówno krytycznych awarii, jak i powoli narastających problemów konserwacyjnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz  [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Poznawanie eksploratora usługi Time Series Insights w środowisku pokazowym

1. W przeglądarce przejdź do witryny  [insights.timeseries.azure.com/preview/samples](https://insights.timeseries.azure.com/preview/samples).  

1. Jeśli zostanie wyświetlony monit, zaloguj się w eksploratorze usługi TSI przy użyciu poświadczeń konta platformy Azure.

### <a name="demo-step-one"></a>Pokaz — krok pierwszy

1. Przyjrzyjmy się **turbinie wiatrowej nr 7 na farmie wiatrowej nr 1**.  

    * **Akcja**: zaktualizuj zakres widoku na `1/1/17 20:00 – 3/10/17 20:00 (UTC)` i dodaj czujnik `Farm 1 > W7 > Generator > GeneratorSpeed`. Następnie wyświetl wartości wynikowe.

       ![Szybki start — część pierwsza][1]

1. Niedawno **firma Contoso wykryła ogień w turbinie wiatrowej nr 7**. Przeanalizujmy to miejsce. Możemy zobaczyć aktywny czujnik alarmu pożarowego w okresie pożaru.

    * **Akcja**: zaktualizuj zakres widoku na `3/9/17 20:00 – 3/10/17 20:00 (UTC)` i dodaj czujnik `Safety > FireAlert`.

      ![Szybki start — część druga][2]

1. Zobaczmy, co jeszcze działo się przed pożarem i po nim. Wartości ciśnienia oleju i aktywnych ostrzeżeń gwałtownie wzrosły tuż przed pożarem, ale w tym momencie było już za późno na uniknięcie problemu.

    * **Akcja**: dodaj czujniki `Pitch > HydraulicOilPressure` i `Pitch > ActiveWarning`.

      ![Szybki start — część trzecia][3]

1. Jeśli zmniejszymy powiększenie, można dostrzec objawy sygnalizujące problem, który spowodował powstanie pożaru. Wskazania obu czujników oscylowały. Więc co stało się wcześniej?

    * **Akcja**: zaktualizuj zakres widoku na `2/24/17 20:00 – 3/10/17 20:00 (UTC)`.

      ![Szybki start — część czwarta][4]

1. Jeśli sprawdzimy dane za całe dwa lata, możemy zobaczyć poprzedni pożar poprzedzony takimi samymi objawami. Dzięki tym danym możemy stworzyć systemy wczesnego wychwytywania problemów tego typu.

    * **Akcja**: zaktualizuj zakres widoku na `1/1/16 – 12/31/17` (wszystkie dane).

       ![Szybki start — część piąta][5]

### <a name="demo-step-two"></a>Pokaz — krok drugi

1. Inne problemy są bardziej subtelne i trudniejsze do diagnozowania. Usługa Time Series Insights udostępnia szeroką gamę możliwości ułatwiających wyśledzenie trudnych problemów. Tutaj widzimy awarię czujnika ostrzegawczego dla urządzenia `turbine #6` odnotowaną `6/25`. Ale co się rzeczywiście dzieje?

    * **Akcja**: usuń bieżące czujniki. Następnie zaktualizuj zakres widoku na `6/1/17 20:00 – 7/1/17 20:00 (UTC)` i dodaj element `Farm 1 > W6 > Safety > VoltageActuatorSwitchWarning`.

       ![Szybki start — część szósta][6]

1. To ostrzeżenie wskazuje na problem z napięciem wyjściowym generatora. Ale co jest przyczyną? Ogólnie produkcja energii przez generator wygląda prawidłowo w krótkich przedziałach czasu. Ale agregując dane, możemy zobaczyć zdecydowany spadek.

    * **Akcja**: usuń element `VoltageActuatorSwitchWarning`, dodaj element `Generator > ActivePower` i zmień interwał aktualizacji na `3d`.

       ![Szybki start — część siódma][7]

1. Jeśli przejdziemy do przodu w zestawie danych, możemy zauważyć, że nie jest to problem przejściowy. Problem występuje w sposób ciągły.

    * **Akcja**: rozszerz przedział czasu po prawej stronie.

       ![Szybki start — część ósma][8]

1. Analizujmy dalej. Możemy wybrać punkty danych innego czujnika, aby wyświetlić napięcie według fazy. Jednak wszystkie dane wyglądają podobnie. Ustawmy znacznik, aby zobaczyć rzeczywiste wartości. Wygląda na to, że problem dotyczy wyjścia fazy 3.

    * **Akcja**: `Add Generator > GridVoltagePhase1, 2, & 3`. Ustaw znacznik na ostatnim punkcie danych w widocznym obszarze.

       ![Szybki start — część ósma][8]

1. Jeśli wyświetlimy wszystkie trzy serie danych na tej samej skali, spadek wartości fazy 3 stanie się jeszcze bardziej oczywisty. W tym momencie możemy przedstawić problem zespołowi konserwacyjnemu, podając wartościowe wskazówki co do przyczyny ostrzeżenia.  

    * **Akcja**: zaktualizuj ekran, aby wyświetlić wszystkie czujniki na wykresie przy użyciu tej samej skali.

       ![Szybki start — część dziewiąta][9]

## <a name="next-steps"></a>Następne kroki

Teraz możesz utworzyć własne środowisko usługi Azure TSI (wersja zapoznawcza):

> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Azure TSI (wersja zapoznawcza)](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
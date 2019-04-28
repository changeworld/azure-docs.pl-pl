---
title: 'Szybki start: Poznawanie środowiska pokazowego usługi Azure Time Series Insights w wersji zapoznawczej | Microsoft Docs'
description: Omówienie środowiska pokazowego usługi Azure Time Series Insights w wersji zapoznawczej
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805263"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Szybki start: Poznawanie środowiska pokazowego usługi Azure Time Series Insights w wersji zapoznawczej

W tym przewodniku Szybki start pokazano, jak korzystać z eksploratora usługi Azure Time Series Insights w wersji zapoznawczej w bezpłatnym środowisku pokazowym. Dowiesz się, jak przy użyciu przeglądarki internetowej wizualizować duże ilości historycznych przemysłowych danych IoT, i poznasz najważniejsze funkcje eksploratora usługi Time Series Insights w wersji zapoznawczej.

Usługa Time Series Insights to kompleksowa oferta platformy jako usługi (PaaS). Służy do pozyskiwania, przetwarzania i magazynowania wysoce skontekstualizowanych, zoptymalizowanych pod kątem szeregów czasowych danych środowiska IoT i wykonywania dotyczących ich zapytań w celu improwizowanej eksploracji danych. Umożliwia także analizę operacyjną. Usługa Time Series Insights to zróżnicowana oferta dostosowana do unikatowych potrzeb przemysłowych wdrożeń technologii IoT.

W środowisku pokazowym przedstawiono firmę Contoso zajmującą się wytwarzaniem energii elektrycznej. W tym środowisku możesz użyć usługi Time Series Insights, aby uzyskać informacje umożliwiające podejmowanie działań oraz przeprowadzić krótką analizę głównej przyczyny problemu w oparciu o dane firmy Contoso. Firma Contoso obsługuje dwie farmy wiatrowe, z których każda obejmuje 10 turbin. Każda turbina jest wyposażona w 20 czujników raportujących dane co minutę do usługi Azure IoT Hub. Czujniki zbierają informacje o warunkach pogodowych, pochyleniu i odchyleniu łopat, wydajności generatora, zachowaniu przekładni i monitorach bezpieczeństwa.

Za pomocą usługi Time Series Insights w wersji zapoznawczej możesz przeanalizować ciągle rosnący zestaw danych firmy Contoso z ostatnich dwóch lat, którego rozmiar wynosi obecnie 40 GB. Dzięki temu możesz lepiej poznać i przewidywać zarówno krytyczne awarie, jak i powoli narastające problemy konserwacyjne.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz  [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Poznawanie eksploratora usługi Time Series Insights w środowisku pokazowym

1. W przeglądarce przejdź do  [środowiska farmy wiatrowej Contoso](https://insights.timeseries.azure.com/preview/samples).  

1. Jeśli zostanie wyświetlony monit, zaloguj się do eksploratora usługi Time Series Insights przy użyciu poświadczeń konta platformy Azure.

### <a name="demo-step-1"></a>Pokaz — krok 1

1. Przyjrzyjmy się turbinie wiatrowej **W7** w **Zakładzie Contoso 1**.  

    * **Akcja**: zaktualizuj zakres widoku na **1/1/17 20:00 do 3/10/17 20:00 (UTC)**, dodaj czujnik **Contoso Plant 1 (Zakład Contoso 1)** > **W7** > **Generator System (System generatora)** > **GeneratorSpeed (Szybkość generatora)**, a następnie wyświetl wartości wynikowe.

       ![Szybki start — część pierwsza][1]

1. Niedawno firma Contoso wykryła pożar w turbinie **W7**. Przeanalizujmy to miejsce. Możemy zobaczyć, że czujnik alarmu pożarowego został uaktywniony podczas pożaru.

    * **Akcja**: zaktualizuj zakres widoku na **3/9/17 20:00 do 3/10/17 20:00 (UTC)** i dodaj czujnik **Safety System (System bezpieczeństwa)** > **FireAlert (Alarm pożarowy)**.

      ![Szybki start — część druga][2]

1. Zobaczmy, co jeszcze działo się przed pożarem i po nim. Wartości ciśnienia oleju i aktywnych ostrzeżeń gwałtownie wzrosły tuż przed pożarem, ale w tym momencie było już za późno, aby zapobiec problemowi.

    * **Akcja**: dodaj czujnik **Pitch System (System regulacji nachylenia)**  > **HydraulicOilPressure (Ciśnienie oleju hydraulicznego)** i czujnik **Pitch System (System regulacji nachylenia)** > **ActiveWarning (Aktywne ostrzeżenie)**.

      ![Szybki start — część trzecia][3]

1. Jeśli zmniejszymy powiększenie, można dostrzec objawy sygnalizujące problem, który spowodował powstanie pożaru. Wskazania obu czujników oscylowały. Więc jaki problem wystąpił wcześniej?

    * **Akcja**: zaktualizuj zakres widoku na **2/24/17 20:00 do 3/10/17 20:00 (UTC)**.

      ![Szybki start — część czwarta][4]

1. Jeśli sprawdzimy dane za całe dwa lata, możemy zobaczyć poprzedni pożar poprzedzony takimi samymi objawami. Dzięki tym danym możemy stworzyć systemy wczesnego wychwytywania problemów tego typu.

    * **Akcja**: zaktualizuj zakres widoku na **1/1/16 do 12/31/17** (wszystkie dane).

       ![Szybki start — część piąta][5]

### <a name="demo-step-2"></a>Pokaz — krok 2

1. Inne problemy są bardziej subtelne i trudniejsze do diagnozowania. Usługa Time Series Insights udostępnia szeroką gamę możliwości ułatwiających wyśledzenie trudnych problemów. Tutaj widzimy awarię czujnika ostrzegawczego dla urządzenia **W6** odnotowaną w dniu **6/25**. Ale co się rzeczywiście dzieje?

    * **Akcja**: usuń bieżące czujniki, zaktualizuj zakres widoku na **6/1/17 20:00 do 7/1/17 20:00 (UTC)**, a następnie dodaj czujnik **Contoso Plant 1 (Zakład Contoso 1)** > **W6** > **Safety System (System bezpieczeństwa)** > **VoltageActuatorSwitchWarning (Ostrzeżenie przełącznika napięciowego)**.

       ![Szybki start — część szósta][6]

1. To ostrzeżenie wskazuje na problem z napięciem wyjściowym generatora. Ale co jest przyczyną? Ogólnie produkcja energii przez generator wygląda prawidłowo w krótkich przedziałach czasu. Ale agregując dane, możemy zobaczyć zdecydowany spadek.

    * **Akcja**: Usuń czujnik **VoltageActuatorSwitchWarning (Ostrzeżenie przełącznika napięciowego)**, dodaj czujnik **Generator System (System generatora)** > **ActivePower (Aktywna moc)** i zaktualizuj zmień interwał na **3d**.

       ![Szybki start — część siódma][7]

1. Jeśli przejdziemy do przodu w zestawie danych, możemy zauważyć, że nie jest to przejściowy problem. Problem występuje w sposób ciągły.

    * **Akcja**: rozszerz przedział czasu po prawej stronie.

       ![Szybki start — część ósma][8]

1. Analizujmy dalej. Możemy dodać punkty danych innego czujnika, aby wyświetlić napięcie według fazy. Jednak wszystkie punkty danych są porównywalne. Ustawmy znacznik, aby zobaczyć rzeczywiste wartości. Wygląda na to, że problem dotyczy wyjścia fazy 3.

    * **Akcja**: Dodaj czujniki **Generator System (System generatora)** > **GridVoltagePhase1 (Faza 1 napięcia sieci)**, **GridVoltagePhase2 (Faza 2 napięcia sieci)** i **GridVoltagePhase3 (Faza 3 napięcia sieci)**. Ustaw znacznik na ostatnim punkcie danych w widocznym obszarze.

       ![Szybki start — część ósma][8]

1. Jeśli wyświetlimy wszystkie trzy punkty danych na tej samej skali, spadek wartości fazy 3 stanie się jeszcze bardziej oczywisty. W tym momencie możemy przedstawić problem zespołowi konserwacyjnemu, podając wartościowe wskazówki co do przyczyny ostrzeżenia.  

    * **Akcja**: zaktualizuj ekran, aby wyświetlić wszystkie czujniki na wykresie przy użyciu tej samej skali.

       ![Szybki start — część dziewiąta][9]

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz utworzyć własne środowisko usługi Time Series Insights w wersji zapoznawczej:

> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Time Series Insights w wersji zapoznawczej](time-series-insights-update-plan.md)

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

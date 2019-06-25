---
title: 'Szybki start: Poznawanie środowiska pokazowego usługi Azure Time Series Insights w wersji zapoznawczej | Microsoft Docs'
description: Omówienie środowiska pokazowego usługi Azure Time Series Insights w wersji zapoznawczej
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: dpalled
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 547edb194ffdf52a805f573d338f61edff80623a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164879"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Szybki start: Poznawanie środowiska pokazowego usługi Azure Time Series Insights w wersji zapoznawczej

Ten przewodnik Szybki Start ułatwia rozpoczęcie pracy ze środowiskiem Azure czas Series Insights w wersji zapoznawczej. Bezpłatne pokazu służy do poznasz najważniejsze funkcje, które zostały dodane do czasu Series Insights w wersji zapoznawczej.

Środowisko wersji demonstracyjnej czasu Series Insights w wersji zapoznawczej zawiera scenariusz firmy, Contoso, operującą dwóch farm turbiny wiatru. Każdą farmę ma turbiny 10. Każda turbina jest wyposażona w 20 czujników raportujących dane co minutę do usługi Azure IoT Hub. Czujników zebrać informacje dotyczące warunków pogodowych, gęstość bloku i yaw pozycji. Informacje dotyczące zachowania skrzynia, monitory bezpieczeństwa i wydajności generator jest zarejestrowany.

W tym przewodniku Szybki Start dowiesz się, jak używać usługi Time Series Insights można znaleźć szczegółowe informacje w danych firmy Contoso. Ponadto należy przeprowadzić krótki główną przyczynę występowania lepiej przewiduj błędy krytyczne i przeprowadzania konserwacji.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Poznawanie eksploratora usługi Time Series Insights w środowisku pokazowym

W Eksploratorze czasu Series Insights w wersji zapoznawczej pokazuje dane historyczne i głównych przyczyn analizy. Aby rozpocząć pracę:

1. Tworzenie [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Jeśli nie masz.

1. Przejdź do [pokaz farmy wiatru Contoso](https://insights.timeseries.azure.com/preview/samples) środowiska.  

1. Jeśli zostanie wyświetlony monit, zaloguj się do Eksploratora usługi Time Series Insights przy użyciu poświadczeń konta platformy Azure.

## <a name="work-with-historical-data"></a>Praca z danych historycznych

1. W **1 zakładu produkcyjnego firmy Contoso**, Przyjrzyj się turbiny wiatru **W7**.  

   1. Zmiana zakresu widoku do **1/1/17 20:00 do 17-3/10 20:00 (czas UTC)** .
   1. Aby wybrać czujnik, wybierz **1 zakładu produkcyjnego firmy Contoso** > **W7** > **Generator System** > **GeneratorSpeed** . Następnie przejrzyj wartości, które są wyświetlane.

      [![W7 w zakładzie Contoso 1](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Niedawno firma Contoso wykryła pożar w turbinie **W7**. Informacje o przyczynie pożaru różnią się w opinie. W usłudze Time Series Insights widać, że czujnik alertu fire została aktywowana podczas ognia.

   1. Zmiana zakresu widoku do **3/9/17 20:00 do 17-3/10 20:00 (czas UTC)** .
   1. Wybierz **bezpieczeństwa systemu** > **FireAlert**.

      [![Znaleziono pożar w turbiny wiatru W7 firmy Contoso](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Przeglądać inne zdarzenia w czasie zbliżonym do czasu uruchomienia, aby zrozumieć, co wydarzyło. Wykorzystanie ropa naftowa i aktywnych ostrzeżeń stwierdzone tuż przed ognia.

   1. Wybierz **Zawodowcom systemu** > **HydraulicOilPressure**.
   1. Wybierz **Zawodowcom systemu** > **ActiveWarning**.

      [![Przeglądać inne zdarzenia, w tym samym czasie](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Wykorzystanie ropa naftowa i aktywnych czujników ostrzeżenie stwierdzone bezpośrednio poprzedzający ognia. Rozwiń szeregów czasowych wyświetlanych, aby zobaczyć inne znaki, które były widoczne, prowadzących do uruchomienia. Zarówno czujników podlegała wahaniom spójnie wraz z upływem czasu. Wahania wskazują trwałe i budzi wzorca.

    * Zmiana zakresu widoku do **2/24/17 20:00 do 17-3/10 20:00 (czas UTC)** .

      [![Wykorzystanie ropa naftowa i aktywnych czujników ostrzeżenie również stwierdzone](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Badanie dwa lata dane historyczne, co spowoduje wyświetlenie inne zdarzenie pożaru, który ma ten sam wahania czujnika.

    * Zmiana zakresu widoku do **1/1/16 do 12/31/17** (wszystkie dane).

      [![Znajduj wzorce historycznych](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Korzystając z usługi Time Series Insights oraz nasza telemetria czujnik, Odkryliśmy trend długoterminowego i problematyczne ukryte w danych historycznych. Te nowe wnioski możemy:

> [!div class="checklist"]
> * Wyjaśniono, co faktycznie wystąpił.
> * Rozwiąż problem.
> * Systemy powiadomień o alertach wyższego poziomu należy umieścić w miejscu.

## <a name="root-cause-analysis"></a>Analiza głównej przyczyny

1. Niektóre scenariusze wymagają zaawansowane analizy, aby odkryć subtelne wskazówek w danych. Wybierz Wiatrak **W6** w dniu **6/25**.

    1. Zmiana zakresu widoku do **6/1/17 20:00 do 7/1/17 20:00 (czas UTC)** .
    1. Wybierz **roślin Contoso 1** > **W6** > **bezpieczeństwa systemu** > **VoltageActuatorSwitchWarning**.

       [![Zmiana zakresu widoku, a następnie wybierz pozycję W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. To ostrzeżenie wskazuje na problem z napięciem wyjściowym generatora. Całkowita moc wyjściowa generatora działa w ramach normalnych parametrów w bieżącym interwale. Zwiększenie naszych interwał, wynika, inny wzorzec. Określony nadania jest oczywiste.

    1. Usuń **VoltageActuatorSwitchWarning** czujnika.
    1. Wybierz **Generator System** > **ActivePower**.
    1. Zmienianie interwału **3d**.

       [![Zmiana interwału 3d](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Rozszerzając zakres czasu, możemy określić, czy problem został zatrzymany lub czy będzie kontynuowane.

    * Rozszerz zakres czasu do 60 dni.

      [![Rozszerz zakres czasu do 60 dni](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Aby zapewnić większą kontekst można dodać inne punkty danych czujnika. Więcej czujniki, możemy wyświetlić, pełniejszego zrozumienie problemu. Ustawmy znacznik, aby zobaczyć rzeczywiste wartości. 

    1. Wybierz **Generator System**, a następnie wybierz trzy czujniki: **GridVoltagePhase1**, **GridVoltagePhase2**, i **GridVoltagePhase3**.
    1. Ustaw znacznik na ostatnim punkcie danych w widocznym obszarze.

       [![Upuść znacznik](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Dwa czujnikami napięcia działają porównywalnie i w ramach normalnych parametrów. Wygląda ono **GridVoltagePhase3** czujnik jest przyczyna nadmiernego.

1. Za pomocą wysoce kontekstowych danych dodanych nadania Faza 3 pojawia się jeszcze bardziej się problem. Teraz mamy dobrą potencjalnego klienta na komputerze Przyczyna ostrzeżenia. Możemy przystąpić do odwoływania się problem do działu obsługi.  

    * Zmień sposób wyświetlania do nakładki wszystkie **Generator System** czujników na tym samym wykresie skali.

      [![Zmień sposób wyświetlania w celu uwzględnienia wszystkich danych](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

Możesz przystąpić do tworzenia środowiska czasu Series Insights w wersji zapoznawczej. Aby rozpocząć:

> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Time Series Insights w wersji zapoznawczej](time-series-insights-update-plan.md)

Dowiedz się, można przejść, pokaz i jego funkcji:

> [!div class="nextstepaction"]
> [W Eksploratorze czasu Series Insights w wersji zapoznawczej](time-series-insights-update-explorer.md)

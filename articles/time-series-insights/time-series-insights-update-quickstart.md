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
ms.date: 04/22/2019
ms.openlocfilehash: dbdbfc797d37ed38936d6cfd354383d412c6b52d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205819"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Szybki start: Poznawanie środowiska pokazowego usługi Azure Time Series Insights w wersji zapoznawczej

Ten przewodnik Szybki Start ułatwia rozpoczęcie pracy z wersji zapoznawczej Azure czas serii szczegółowych informacji. Bezpłatne pokazu będzie poznasz najważniejsze funkcje, które zostały dodane w serii czasu Insights (wersja zapoznawcza).

Środowisko wersji demonstracyjnej (wersja zapoznawcza) zawiera scenariusz firmie Contoso, operującą dwóch farm turbiny wiatru każdy z 10 turbiny. Każda turbina jest wyposażona w 20 czujników raportujących dane co minutę do usługi Azure IoT Hub. Czujników zebrać informacje dotyczące warunków pogodowych, gęstość bloku i yaw pozycji. Ponadto wydajność generator, zachowanie skrzynia i bezpieczeństwa monitoruje.

 Dowiesz się, że na potrzeby usługi Time Series Insights, aby znaleźć szczegółowe informacje w danych firmy Contoso. Będzie także przeprowadzanie analizy krótki przyczyny, aby lepiej przewiduj błędy krytyczne i przeprowadzania konserwacji.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Poznawanie eksploratora usługi Time Series Insights w środowisku pokazowym

W Eksploratorze czasu Series Insights w wersji zapoznawczej pokazuje dane historyczne i głównych przyczyn analizy. Aby rozpocząć pracę:

1. Tworzenie [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Jeśli nie zostało utworzone.

1. Przejdź do [pokaz farmy wiatru Contoso](https://insights.timeseries.azure.com/preview/samples) środowiska.  

1. Jeśli zostanie wyświetlony monit, zaloguj się do Eksploratora usługi Time Series Insights przy użyciu poświadczeń konta platformy Azure.

## <a name="work-with-historical-data"></a>Praca z danych historycznych

1. Przyjrzyj się turbiny wiatru **W7** w **1 zakładu produkcyjnego firmy Contoso**.  

    * Aktualizuj zakres widoku do **1/1/17 20:00 do 17-3/10 20:00 (czas UTC)**.
    * Wybierz **roślin Contoso 1** > **W7** > **Generator System** > **GeneratorSpeed** czujnik. Następnie sprawdź wartości wynikowe.

      [![W7 w zakładzie Contoso 1](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Niedawno firma Contoso wykryła pożar w turbinie **W7**. Opinie różnią się o zbliżeniowa przyczynę pożaru zostało. W trakcie ściślejszej kontroli widzimy, że czujnik alertu fire została aktywowana podczas ognia.

    * Aktualizuj zakres widoku do **3/9/17 20:00 do 17-3/10 20:00 (czas UTC)**.
    * Wybierz **bezpieczeństwa systemu** > **FireAlert** czujnika.

      [![Znaleziono pożar w turbiny wiatru W7 firmy Contoso](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Przeglądać inne zdarzenia w czasie zbliżonym do czasu uruchomienia, aby zrozumieć, co wydarzyło. Wykorzystanie ropa naftowa i aktywnych ostrzeżeń stwierdzone tuż przed ognia.

    * Wybierz **System pomysłu** > **HydraulicOilPressure** czujnika.
    * Wybierz **System pomysłu** > **ActiveWarning** czujnika.

      [![Przeglądać inne zdarzenia, w tym samym czasie](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Wykorzystanie ropa naftowa i aktywnych czujników ostrzeżenie stwierdzone bezpośrednio poprzedzający ognia. Rozwiń szeregów czasowych wyświetlanych, aby zobaczyć inne znaki obecne prowadzących do ognia. Zarówno czujników podlegała wahaniom spójnie wraz z upływem czasu wskazujący wzorzec trwałe i budzi.

    * Aktualizuj zakres widoku do **2/24/17 20:00 do 17-3/10 20:00 (czas UTC)**.

      [![Wykorzystanie ropa naftowa i aktywnych czujników ostrzeżenie również stwierdzone](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Badanie dwa lata dane historyczne, co spowoduje wyświetlenie inne zdarzenie fire przy użyciu tego samego wahania czujnika.

    * Aktualizuj zakres widoku do **1/1/16 do 12/31/17** (wszystkie dane).

      [![Znajduj wzorce historycznych](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Korzystając z usługi Azure Time Series Insights oraz nasza telemetria czujnik, Odkryliśmy trend długoterminowego i problematyczne ukryte w naszych danych historycznych. Te nowe wnioski możemy:

> [!div class="checklist"]
> * Wyjaśniono, co faktycznie wystąpił.
> * Rozwiąż problem.
> * Systemy powiadomień o alertach wyższego poziomu należy umieścić w miejscu.

## <a name="root-cause-analysis"></a>Analiza przyczyn źródłowych

1. Niektóre scenariusze wymagają zaawansowane analizy, aby odkryć subtelne wskazówek w danych. Wybierz Wiatrak **W6** w dniu **6/25**

    * Aktualizuj zakres widoku do **6/1/17 20:00 do 7/1/17 20:00 (czas UTC)**.
    * Następnie wybierz pozycję **1 zakładu produkcyjnego firmy Contoso** > **W6** > **bezpieczeństwa systemu** > **VoltageActuatorSwitchWarning**  czujnika.

      [![Aktualizuj zakres widoku, a następnie wybierz pozycję W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. To ostrzeżenie wskazuje na problem z napięciem wyjściowym generatora. Całkowita moc wyjściowa generatora działa w ramach normalnych parametrów nasz bieżący interwał. Zwiększając interwał naszych, wynika innego wzorzec: Brak nadania określony.

    * Usuń **VoltageActuatorSwitchWarning** czujnika.
    * Wybierz **Generator System** > **ActivePower** czujnika.
    * Interwał do aktualizacji **3d**.

      [![Interwał aktualizacji na 3d](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Rozszerzając zakres czasu, możemy określić, czy problem został zatrzymany lub czy będzie kontynuowane.

    * Rozszerz zakres czasu do 60 dni.

      [![Rozszerz zakres czasu do 60 dni](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Aby zapewnić doskonałe kontekst można dodać inne punkty danych czujnika. Więcej czujników możemy wyświetlić, pełniejszego zrozumienie problemu. Ustawmy znacznik, aby zobaczyć rzeczywiste wartości. 

    * Wybierz **Generator System** > **GridVoltagePhase1**, **GridVoltagePhase2**, i **GridVoltagePhase3** czujników .
    * Ustaw znacznik na ostatnim punkcie danych w widocznym obszarze.

      [![Upuść znacznik](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Czujnikami napięcia trzy działają porównywalnie i w ramach normalnych parametrów. Wygląda ono **GridVoltagePhase3** czujnik jest przyczyna nadmiernego.

1. Za pomocą wysoce kontekstowych danych dodanych nadania Faza 3 pojawia się jeszcze bardziej jako problem. Teraz jesteśmy gotowi do odwoływania się problem do działu obsługi z dobrej potencjalnego klienta w tym ostrzeżenia.  

    * Aktualizuj wyświetlany obraz do nakładki wszystkie **Generator System** czujników na tym samym wykresie skali.

       [![Aktualizuj wyświetlany obraz do uwzględnienia wszystkich danych](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz utworzyć własne środowisko usługi Time Series Insights w wersji zapoznawczej:

> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Time Series Insights w wersji zapoznawczej](time-series-insights-update-plan.md)

Dowiedz się, można przejść, pokaz i jego funkcji:

> [!div class="nextstepaction"]
> [W Eksploratorze czasu Series Insights w wersji zapoznawczej](time-series-insights-update-explorer.md)

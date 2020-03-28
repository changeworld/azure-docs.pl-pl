---
title: 'Samouczek: Szczegółowe omówienie usługi Machine Learning w usłudze Azure IoT Edge'
description: Samouczek wysokiego poziomu, który przechodzi przez różne zadania niezbędne do utworzenia end-to-end, uczenia maszynowego w scenariuszu krawędzi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 965c420fa29c4cf82517148c01e17d6d7dd6ea97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74106512"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Samouczek: kompleksowe rozwiązanie wykorzystujące usługę Azure Machine Learning i usługę IoT Edge

Często aplikacje IoT chcą korzystać z inteligentnej chmury i inteligentnej krawędzi. W tym samouczku prowadzimy cię przez szkolenie modelu uczenia maszynowego z danymi zebranymi z urządzeń IoT w chmurze, wdrażanie tego modelu w usłudze IoT Edge oraz okresowe utrzymywanie i udoskonalanie modelu.

Głównym celem tego samouczka jest wprowadzenie przetwarzania danych IoT z uczeniem maszynowym, w szczególności na krawędzi. Chociaż dotykamy wielu aspektów ogólnego przepływu pracy uczenia maszynowego, ten samouczek nie jest przeznaczony jako dogłębne wprowadzenie do uczenia maszynowego. W tym przypadku nie staramy się tworzyć wysoce zoptymalizowany model dla przypadku użycia — po prostu zrobić wystarczająco dużo, aby zilustrować proces tworzenia i używania opłacalnego modelu przetwarzania danych IoT.

## <a name="target-audience-and-roles"></a>Grupa docelowa i role

Ten zestaw artykułów jest przeznaczony dla deweloperów bez wcześniejszego doświadczenia w tworzeniu IoT lub uczeniu maszynowym. Wdrażanie uczenia maszynowego na urządzeniach brzegowych wymaga wiedzy na temat łączenia szerokiej gamy technologii. W związku z tym w tym samouczku obejmuje cały scenariusz end-to-end, aby zademonstrować jeden ze sposobów łączenia tych technologii razem dla rozwiązania IoT. W środowisku rzeczywistym zadania te mogą być dystrybuowane między kilka osób o różnych specjalizacjach. Na przykład deweloperzy będą koncentrować się na urządzeniu lub kodzie chmury, podczas gdy analitycy danych zaprojektowali modele analizy. Aby umożliwić poszczególnym deweloperom pomyślne ukończenie tego samouczka, udostępniliśmy dodatkowe wskazówki ze spostrzeżeniami i łączami do większej ilości informacji, które mamy nadzieję, że są wystarczające, aby zrozumieć, co jest wykonywane, a także dlaczego.

Alternatywnie możesz współpracować ze współpracownikami o różnych rolach, aby śledzić samouczek, łącząc swoją pełną wiedzę i nauczyć się jako zespół, jak wszystko pasuje do siebie.

W obu przypadkach, aby pomóc zorientować czytników, każdy artykuł w tym samouczku wskazuje rolę użytkownika. Role te obejmują:

* Tworzenie chmury (w tym deweloper chmury pracujący w pojemność DevOps)
* Analiza danych

## <a name="use-case-predictive-maintenance"></a>Przypadek użycia: Konserwacja predykcyjna

Oparliśmy ten scenariusz na przypadku użycia przedstawionym na Konferencji prognostycznej i zarządzania zdrowiem (PHM08) w 2008 roku. Celem jest przewidywanie pozostałej żywotności (RUL) zestawu silników turbowentylatorowych. Dane te zostały wygenerowane przy użyciu C-MAPSS, komercyjnej wersji oprogramowania MAPSS (Modular Aero-Propulsion System Simulation). To oprogramowanie zapewnia elastyczne środowisko symulacji silnika turbowentylatorowego, aby wygodnie symulować stan zdrowia, kontrolę i parametry silnika.

Dane użyte w tym samouczku pochodzą z [zestawu danych symulacji degradacji silnika Turbofan.](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)

Z pliku readme:

***Scenariusz eksperymentalny***

*Zestawy danych składają się z wielu wielozmiennych szeregów czasowych. Każdy zestaw danych jest dalej podzielony na podzbiory szkoleniowe i testowe. Każda seria czasowa pochodzi z innego silnika – tj. Każdy silnik zaczyna się od różnych stopni początkowego zużycia i zmienności produkcji, która jest nieznana użytkownikowi. To zużycie i zmienność jest uważane za normalne, to znaczy, że nie jest uważany za stan usterki. Istnieją trzy ustawienia operacyjne, które mają znaczący wpływ na osiągi silnika. Te ustawienia są również zawarte w danych. Dane są zanieczyszczone hałasem z czujnika.*

*Silnik pracuje normalnie na początku każdej serii czasowej i w pewnym momencie w trakcie serii występuje usterka. W zestawie treningowym usterka rośnie w wielkości do momentu awarii systemu. W zestawie testowym szeregi czasowe kończy się jakiś czas przed awarią systemu. Celem konkursu jest przewidzenie liczby pozostałych cykli operacyjnych przed awarią w zestawie testowym, tj. Podano również wektor wartości prawdziwego pozostałego okresu użytkowania (RUL) dla danych testowych.*

Ponieważ dane zostały opublikowane dla konkursu, kilka podejść do pozyskiwania modeli uczenia maszynowego zostały opublikowane niezależnie. Odkryliśmy, że badanie przykładów jest pomocne w zrozumieniu procesu i rozumowania związanego z tworzeniem określonego modelu uczenia maszynowego. Zobacz na przykład:

[Model przewidywania awarii silnika samolotu](https://github.com/jancervenka/turbofan_failure) przez użytkownika GitHub jancervenka.

[Degradacja silnika Turbofan](https://github.com/hankroark/Turbofan-Engine-Degradation) przez użytkownika GitHub hankroark.

## <a name="process"></a>Proces

Poniższy obraz ekspedycję ilustruje trudne kroki, które następuje w tym samouczku:

![Diagram architektury dla kroków procesu](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Zbieranie danych szkoleniowych**: Proces rozpoczyna się od zebrania danych szkoleniowych. W niektórych przypadkach dane zostały już zebrane i są dostępne w bazie danych lub w postaci plików danych. W innych przypadkach, szczególnie w przypadku scenariuszy IoT, dane muszą być zbierane z urządzeń IoT i czujników i przechowywane w chmurze.

   Zakładamy, że nie masz kolekcji silników turbowentylatorowych, więc pliki projektu zawierają prosty symulator urządzenia, który wysyła dane urządzenia NASA do chmury.

1. **Przygotuj dane**. W większości przypadków surowe dane zebrane z urządzeń i czujników będą wymagały przygotowania do uczenia maszynowego. Ten krok może obejmować oczyszczanie danych, formatowanie danych lub przetwarzanie wstępne w celu wstrzyknięcia dodatkowych informacji, które uczenie maszynowe może zostać wyłączone.

   W przypadku danych naszych maszyn silnika samolotowego przygotowanie danych polega na obliczeniu wyraźnych czasów czasu do awarii dla każdego punktu danych w próbce na podstawie rzeczywistych obserwacji danych. Informacje te umożliwiają algorytmowi uczenia maszynowego znajdowanie korelacji między rzeczywistymi wzorcami danych czujnika a oczekiwanym pozostałym czasem eksploatacji aparatu. Ten krok jest wysoce specyficzne dla domeny.

1. **Tworzenie modelu uczenia maszynowego**. Na podstawie przygotowanych danych możemy teraz eksperymentować z różnymi algorytmami uczenia maszynowego i parametryzacji do uczenia modeli i porównywania wyników ze sobą.

   W takim przypadku do testowania porównujemy przewidywany wynik obliczony przez model z rzeczywistym wynikiem obserwowanym na zestawie silników. W usłudze Azure Machine Learning możemy zarządzać różnymi iteracjami modeli utworzonych w rejestrze modelu.

1. **Wdrażanie modelu**. Gdy mamy model, który spełnia nasze kryteria sukcesu, możemy przejść do wdrożenia. Obejmuje to zawijanie modelu do aplikacji usługi sieci web, które mogą być podawane z danymi przy użyciu wywołań REST i wyników analizy zwracanej. Aplikacja usługi sieci web jest następnie pakowana do kontenera docker, który z kolei można wdrożyć w chmurze lub jako moduł usługi IoT Edge. W tym przykładzie koncentrujemy się na wdrażaniu w układzie usługi IoT Edge.

1. **Obsługa i udoskonalanie modelu**. Nasza praca nie jest wykonywana po wdrożeniu modelu. W wielu przypadkach chcemy kontynuować zbieranie danych i okresowo przesyłać te dane do chmury. Następnie możemy użyć tych danych do przekwalifikowania i udoskonalenia naszego modelu, który następnie możemy ponownie wdrożyć do usługi IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć samouczek, potrzebujesz dostępu do subskrypcji platformy Azure, w którym masz prawa do tworzenia zasobów. Kilka usług używanych w tym samouczku poniesie opłaty za korzystanie z platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz rozpocząć pracę z [bezpłatnym kontem platformy Azure.](https://azure.microsoft.com/offers/ms-azr-0044p/)

Potrzebujesz również komputera z zainstalowanym programem PowerShell, gdzie można uruchamiać skrypty, aby skonfigurować maszynę wirtualną platformy Azure jako maszynę dewelopera.

W tym dokumencie używamy następującego zestawu narzędzi:

* Centrum Usługi Azure IoT do przechwytywania danych

* Notesy platformy Azure jako nasz główny front-end do przygotowywania danych i eksperymentów uczenia maszynowego. Uruchamianie kodu języka Python w notesie na podzbiorze przykładowych danych to świetny sposób na szybkie iteracyjne i interaktywne przetwarzanie podczas przygotowywania danych. Notesy Jupyter mogą być również używane do przygotowywania skryptów do uruchamiania na dużą skalę w wewnętrznej bazy danych obliczeniowych.

* Usługa Azure Machine Learning jako zaplecze dla uczenia maszynowego na dużą skalę i generowania obrazów uczenia maszynowego. Prowadzimy zaplecze usługi Azure Machine Learning przy użyciu skryptów przygotowanych i przetestowanych w notesach jupytera.

* Usługa Azure IoT Edge do stosowania obrazu uczenia maszynowego poza chmurą

Oczywiście dostępne są inne opcje. W niektórych scenariuszach, na przykład IoT Central może służyć jako alternatywa bez kodu do przechwytywania danych szkolenia początkowego z urządzeń IoT.

## <a name="next-steps"></a>Następne kroki

Ten samouczek jest podzielony na następujące sekcje:

1. Skonfiguruj komputer dewelopera i usługi platformy Azure.
2. Generowanie danych szkoleniowych dla modułu uczenia maszynowego.
3. Szkolenie i wdrażanie modułu uczenia maszynowego.
4. Skonfiguruj urządzenie usługi IoT Edge tak, aby działało jako przezroczysta brama.
5. Tworzenie i wdrażanie modułów IoT Edge.
6. Wysyłaj dane do urządzenia Usługi IoT Edge.

Przejdź do następnego artykułu, aby skonfigurować komputer dewelopera i aprowizować zasoby platformy Azure.

> [!div class="nextstepaction"]
> [Konfigurowanie środowiska uczenia maszynowego w umywłanej IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)

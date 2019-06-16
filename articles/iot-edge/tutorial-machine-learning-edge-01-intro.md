---
title: Szczegółowy przewodnik dotyczący uczenia maszynowego w usłudze Azure IoT Edge | Dokumentacja firmy Microsoft
description: Samouczek wysokiego poziomu, który przeprowadzi różne zadania niezbędne do utworzenia uczenia maszynowego end-to-end, w scenariuszu krawędzi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 916e48752431be41ff150c2ac84e66eb1e98e81f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057750"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Samouczek: Rozwiązania end-to-end, przy użyciu usługi Azure Machine Learning i usługi IoT Edge

Chcesz korzystać z zalet chmury inteligentnej i inteligentnych rozwiązań brzegowych często aplikacji IoT. W tym samouczku przedstawiono szkolenia usługi machine learning model przy użyciu danych zebranych z urządzeń IoT w chmurze, wdrażanie tego modelu do usługi IoT Edge i utrzymywania i okresowo rafinacja modelu.

Głównym celem tego samouczka jest wprowadzenie przetwarzanie danych IoT przy użyciu uczenia maszynowego, konkretnie na urządzeniach brzegowych. Gdy firma Microsoft touch wiele aspektów ogólne usługi machine learning przepływu pracy, w tym samouczku nie ma służyć jako szczegółowe wprowadzenie do usługi machine learning. Jako punkt w przypadku firma Microsoft nie należy próbować tworzyć wysoce zoptymalizowane modelu dla danego przypadku użycia — po prostu robimy wystarczająco dużo, które ilustrują proces tworzenia i używania modelu możliwego do użycia do przetwarzania danych IoT.

## <a name="target-audience-and-roles"></a>Docelowi odbiorcy i ról

Ten zestaw artykułów jest przeznaczona dla deweloperów, bez doświadczenia w IoT rozwoju lub machine learning. Wdrażanie usługi machine learning na urządzeniach brzegowych wymaga znajomości sposobu łączenia szeroką gamę technologii. W związku z tym ten samouczek obejmuje cały scenariusz end-to-end w celu zademonstrowania jednym ze sposobów dołączania tych technologii razem dla rozwiązania IoT. W środowisku rzeczywistych tych zadań mogą rozproszone między kilka osób z różnych specjalizacji. Na przykład deweloperów może skupić się na kodu urządzenia lub chmury, analitykom danych zaprojektowana modele analizy. Aby włączyć pojedynczych deweloperów do pomyślnego ukończenia tego samouczka, udostępniliśmy dodatkowe wskazówki dotyczące szczegółowe informacje i linki do szczegółowych informacji, które mamy nadzieję, że jest wystarczające, aby zrozumieć, co to jest wykonywana, jak i dlaczego.

Może też sił ze współpracownikami różne role, aby wykonać kroki samouczka ze sobą, przeniesienie pełną wiedzę na temat ponosić oraz Dowiedz się jako zespół, jak elementy współdziałają ze sobą.

W obu przypadkach pomaga w poznaniu czytniki poszczególnymi artykułami, w tym samouczku wskazuje roli użytkownika. Te role obejmują:

* Projektowanie aplikacji w chmurze (w tym chmury dla deweloperów, pracy w charakterze DevOps)
* Analiza danych

## <a name="use-case-predictive-maintenance"></a>Przypadek użycia: Konserwacja zapobiegawcza

W tym scenariuszu firma Microsoft oparte na przypadek użycia prezentowane podczas konferencji na zestaw oraz zarządzanie stanem zdrowia (PHM08) 2008. Celem jest przewidywania pozostałego okresu eksploatacji (RUL) z zestawu aparatów w trybie samolotowym turbofan. Te dane został wygenerowany przy użyciu języka C-MAPSS, komercyjnej wersji oprogramowania MAPSS (moduły Aero napędu systemu symulacji). To oprogramowanie zapewnia środowisko symulacji aparatu elastyczne turbofan wygodnie symulowanie kondycji, kontroli i parametry silnika.

Dane używane w ramach tego samouczka jest pobierana z [zestaw danych symulacji degradacji silnika Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Z pliku readme:

***Scenariusz eksperymentalne***

*Zestawy danych składają się z wielu szeregach czasowych wielu zmiennych. Każdy zestaw danych jest podzielona na podzestawy szkolenia i testowania. Każdy szereg czasowy z różnych aparatu — czyli, dane mogą być uważane floty aparatów tego samego typu. Każdy silnik rozpoczyna się od różne stopnie początkowej zużycia i produkcji odmiany, który jest nieznany dla użytkownika. To zużycia i odmiany jest uznawany za normalne, czyli, nie jest uznawana za warunek błędu. Dostępne są trzy ustawienia operacyjne, które mają znaczny wpływ na wydajność aparatu. Te ustawienia znajdują się również w danych. Dane jest zanieczyszczony szumu czujnika.*

*Aparat działają normalnie na początku każdego szeregów czasowych i opracowuje błędów w pewnym momencie podczas serii. Błąd rośnie w zestaw treningowy wielkości i do momentu awarii systemu. W zestawie testów szeregów czasowych kończy się chwilę przed awarii systemu. Celem konkurencję się przewidzieć liczbę pozostałych cykle operacyjnej przed awarią w zestawie testów, czyli liczbę cykli operacyjne od ostatniego cyklu, który aparat będzie w dalszym ciągu działać. Również podane wektor wartość true, wartości pozostałych przydatne okresu eksploatacji (RUL) dla danych testowych.*

Ponieważ dane został opublikowany w konkursie, niezależne zostały opublikowane kilka metod, aby utworzyć pochodny modeli uczenia maszynowego. Znaleźliśmy bada przykłady to pomocne w opis procesu i wnioskowania do utworzenia modelu uczenia maszynowego określone. Zobacz na przykład:

[Model predykcyjny awarii silnika samolotu](https://github.com/jancervenka/turbofan_failure) przez jancervenka użytkownika usługi GitHub.

[Turbofan engine degradacji](https://github.com/hankroark/Turbofan-Engine-Degradation) przez hankroark użytkownika usługi GitHub.

## <a name="process"></a>Process

Na rysunku poniżej przedstawiono ogólne kroki, jakie wykonamy w ramach tego samouczka:

![Diagram architektury kroków procesu](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Zbieranie danych treningowych**: Rozpocznie się proces przez zbieranie danych treningowych. W niektórych przypadkach danych została już zebrane i jest dostępna w bazie danych lub w postaci plików danych. W innych przypadkach, szczególnie w przypadku scenariuszy IoT dane muszą być zebrane z czujników i urządzeń IoT i przechowywane w chmurze.

   Przyjęto założenie, że nie masz kolekcję aparatów turbofan, więc pliki projektu obejmują symulator urządzeń proste wysyłający NASA danych urządzenia do chmury.

1. **Przygotowywanie danych**. W większości przypadków dane pierwotne jako zebranych z urządzeń i czujników wymaga przygotowania do uczenia maszynowego. Ten krok może obejmować dane oczyszczania, ponownego formatowania danych lub przetwarzania wstępnego, aby wstawić dodatkowe informacje, uczenie maszynowe może klucza wyłączone.

   Dla danych maszyny w trybie samolotowym aparatu przygotowania danych polega na obliczanie jawne razy błąd czasu dla każdego punktu danych w przykładzie, na podstawie rzeczywistych obserwacji w danych. Te informacje umożliwia znajdowanie korelacji między wzorców danych rzeczywistych czujników i oczekiwanego pozostałego okresu eksploatacji silnika Algorytm uczenia maszynowego. Ten krok jest bardzo specyficznego dla domeny.

1. **Tworzenie modelu uczenia maszynowego**. Na podstawie danych przygotowany, możemy teraz eksperymentować z algorytmów uczenia maszynowego różnych i parameterizations do uczenia modeli i porównać wyniki ze sobą.

   W tym przypadku do testowania porównamy przewidywany wynik obliczone przez model rzeczywistego wyniku obserwowana na zestaw aparatów. W usłudze Azure Machine Learning możemy zarządzać różne iteracje modele utworzone w rejestrze modelu.

1. **Wdrażanie modelu**. Gdy będziemy już mieć modelu, które spełniają nasze kryteria sukcesu, można przystąpić do wdrożenia. Obejmujące zawijania modelu w aplikacji usługi sieci web, która jest zasilany z danymi przy użyciu wywołania REST i zwracają wyniki analizy. Aplikacja usługi sieci web, następnie jest dostarczana w kontenerze platformy docker, który z kolei można wdrożyć w chmurze lub jako moduł usługi IoT Edge. W tym przykładzie skupimy się na wdrożenie usługi IoT Edge.

1. **Obsługa i dostosować model**. Nasza praca odbywa się po wdrożeniu modelu. W wielu przypadkach chcemy kontynuować, zbieranie danych, a okresowe przekazywanie tych danych do chmury. Ponowne szkolenie i dostosowywać nasz model, który możemy następnie można wdrożyć ponownie do usługi IoT Edge, możemy następnie użyć tych danych.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, potrzebujesz dostępu do subskrypcji platformy Azure, w którym masz uprawnienia do tworzenia zasobów. Kilka usług używanych w tym samouczku zostaną naliczone opłaty usługi Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, można rozpocząć pracę z [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Należy również maszynę przy użyciu programu PowerShell jest zainstalowane, której można uruchamiać skryptów, aby ustawić się maszynę wirtualną platformy Azure jako komputerze deweloperskim.

W tym dokumencie używamy następujący zestaw narzędzi:

* Usługi Azure IoT hub do przechwycenia danych

* Notesy platformy Azure jako naszym głównym frontonu na potrzeby przygotowywania danych i eksperymentów uczenia maszynowego. Korzystanie z języka python kodu w notesie w podzestawie przykładowych danych jest doskonałym sposobem na szybkie, iteracyjne i interaktywne dotychczasowa podczas przygotowywania danych. Notesy Jupyter notebook można również przygotować skryptów do uruchamiania na dużą skalę w zapleczu obliczeń.

* Usługa Azure Machine Learning jako zaplecza dla usługi machine learning na dużą skalę i generowanie obrazu uczenia maszynowego. Utworzyliśmy zaplecza usługi Azure Machine Learning, za pomocą skryptów przygotowany i testowane na platformie notesów programu Jupyter.

* Usługa Azure IoT Edge dla aplikacji poza chmurą obrazu machine learning

Oczywiście istnieją inne opcje. W niektórych scenariuszach na przykład IoT Central może służyć jako alternatywa bez użycia kodu do przechwytywania danych wstępne szkolenie z urządzeń IoT.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku jest podzielona na następujące sekcje:

1. Konfigurowanie Twojego komputera deweloperskiego i usług platformy Azure.
2. Generowanie danych szkoleniowych maszyny modułu nauczania.
3. Uczenie i wdrażanie maszyny modułu nauczania.
4. Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy.
5. Tworzenie i wdrażanie moduły usługi IoT Edge.
6. Wysłać dane do urządzenia usługi IoT Edge.

Przejdź do następnego artykułu, aby skonfigurować komputerze deweloperskim i aprowizowania zasobów platformy Azure.

> [!div class="nextstepaction"]
> [Konfigurowanie środowiska na potrzeby usługi machine learning w usłudze IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)

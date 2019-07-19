---
title: Tworzenie i eksplorowanie eksperymentów w portalu
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak tworzyć automatyczne eksperymenty uczenia maszynowego i zarządzać nimi w portalu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 5eb3e94ff65e8a8b74f357a4cb8a517fd3837c5a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871807"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Twórz i Eksploruj zautomatyzowane eksperymenty uczenia maszynowego w Azure Portal (wersja zapoznawcza)

 W tym artykule dowiesz się, jak tworzyć, uruchamiać i eksplorować automatyczne eksperymenty uczenia maszynowego w Azure Portal bez pojedynczego wiersza kodu. Funkcja automatycznego uczenia maszynowego automatyzuje proces wybierania najlepszego algorytmu dla określonych danych, dzięki czemu można szybko generować model uczenia maszynowego. [Dowiedz się więcej o automatycznym uczeniu maszynowym](concept-automated-ml.md).

 Jeśli wolisz skorzystać z większej ilości kodu, możesz również [skonfigurować automatyczne eksperymenty uczenia maszynowego w języku Python](how-to-configure-auto-train.md) za pomocą [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Wprowadzenie

Przejdź do lewego okienka obszaru roboczego. Wybierz pozycję Automatyczne Machine Learning w sekcji Tworzenie (wersja zapoznawcza).

![Azure Portal okienku nawigacji](media/how-to-create-portal-experiments/nav-pane.png)

 Jeśli po raz pierwszy wykonujesz wszystkie eksperymenty z automatycznym Machine Learning, zobaczysz następujące kwestie:

![Azure Portal Strona docelowa eksperymentu](media/how-to-create-portal-experiments/landing-page.png)

W przeciwnym razie zobaczysz automatyczny pulpit nawigacyjny uczenia maszynowego z omówieniem wszystkich zautomatyzowanych eksperymentów z uczeniem maszynowym, łącznie z tymi utworzonymi przy użyciu zestawu SDK. Tutaj można filtrować i eksplorować uruchomienia według daty, nazwy eksperymentu i stanu uruchomienia.

![Pulpit nawigacyjny eksperymentu Azure Portal](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Tworzenie eksperymentu

Wybierz przycisk Utwórz eksperyment, aby wypełnić następującą formę.

![Utwórz formularz eksperymentu](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Wprowadź nazwę eksperymentu.

1. Wybierz obliczenia dla zadania profilowania i szkolenia danych. Lista istniejących obliczeń jest dostępna na liście rozwijanej. Aby utworzyć nowe obliczenie, postępuj zgodnie z instrukcjami podanymi w sekcji Krok 3.

1. Wybierz przycisk Utwórz nowy obliczeniowy, aby otworzyć poniższe okienko, i skonfiguruj kontekst obliczeniowy dla tego eksperymentu.

    ![Utwórz nowe obliczenie dla eksperymentu](media/how-to-create-portal-experiments/create-new-compute.png)

    Pole|Opis
    ---|---
    Nazwa obliczeniowa| Wprowadź unikatową nazwę identyfikującą kontekst obliczeniowy.
    Rozmiar maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczenia.
    Ustawienia dodatkowe| *Minimalny węzeł*: Wprowadź minimalną liczbę węzłów dla obliczenia. Minimalna liczba węzłów dla AML COMPUTE to 0. Aby włączyć Profilowanie danych, musisz mieć co najmniej jeden węzeł. <br> *Maksymalny węzeł*: Wprowadź maksymalną liczbę węzłów dla obliczeń. Wartość domyślna to 6 węzłów na potrzeby obliczeń AML.

      Aby rozpocząć tworzenie nowego obliczenia, wybierz pozycję **Utwórz**. Może to potrwać kilka minut.

      >[!NOTE]
      > Nazwa obliczeniowa wskazuje, czy w przypadku obliczeń, które zostały wybrane/utworzone, *włączono profilowanie*. (Zobacz 7B, aby uzyskać więcej informacji na temat profilowania danych).

1. Wybierz konto magazynu dla swoich danych. Publiczna wersja zapoznawcza obsługuje tylko lokalne operacje przekazywania plików i konta usługi Azure Blob Storage.

1. Wybierz kontener magazynu.

1. Wybierz plik danych z kontenera magazynu lub Przekaż plik z komputera lokalnego do kontenera.

    ![Wybierz plik danych do eksperymentu](media/how-to-create-portal-experiments/select-file.png)

1. Skorzystaj z kart wersja zapoznawcza i profil, aby skonfigurować dane dla tego eksperymentu.

    1. Na karcie Podgląd wskaż, czy dane zawierają nagłówki, a następnie wybierz funkcje (kolumny) do szkolenia przy użyciu przycisków **dołączone** przełącznika w każdej kolumnie funkcji.

        ![Podgląd danych](media/how-to-create-portal-experiments/data-preview.png)

    1. Na karcie Profil można wyświetlić pozycję [profil danych](#profile) według funkcji, a także dane statystyczne dystrybucji, typu i podsumowania (średnia, mediana, max/min itd.) każdej z nich.

        ![Karta profil danych](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > Następujący komunikat o błędzie zostanie wyświetlony, jeśli **nie** włączono profilowania dla kontekstu obliczeniowego: *Profilowanie danych jest dostępne tylko dla obiektów docelowych obliczeń, które są już uruchomione*.

1. Wybierz typ zadania szkolenia: Klasyfikacja, regresja lub prognozowanie.

1. Wybierz kolumnę docelową. Kolumna, dla której chcesz wykonać prognozy.

1. W przypadku prognozowania:
    1. Wybierz kolumnę czasu: Ta kolumna zawiera dane czasu, które mają być używane.
    1. Wybierz horyzont prognoz: Wskaż, ile jednostek czasu (min/godz./dni/tygodnie/miesięcy/lat) będzie można przewidzieć w przyszłości model. Dalszy model jest wymagany do przewidywania w przyszłości, tym mniej dokładne stanie się. [Dowiedz się więcej o prognozowaniu i prognozowaniu horyzontu](how-to-auto-train-forecast.md).

1. Obowiązkowe Ustawienia zaawansowane: dodatkowe ustawienia, których można użyć w celu lepszego kontrolowania zadania szkoleniowego.

    Ustawienia zaawansowane|Opis
    ------|------
    Metryka podstawowa| Główna Metryka używana do oceniania modelu. [Dowiedz się więcej o metrykach modelu](how-to-configure-auto-train.md#explore-model-metrics).
    Kryteria wyjścia| Po spełnieniu któregokolwiek z tych kryteriów zadanie szkolenia zostanie zakończone przed pełnym ukończeniem. <br> *Czas zadania szkoleniowego (minuty)* : Jak długo zezwolić na uruchomienie zadania szkoleniowego.  <br> *Maksymalna liczba iteracji*: Maksymalna liczba potoków (iteracji) do przetestowania w zadaniu szkoleniowym. Zadanie nie zostanie uruchomione więcej niż określona liczba iteracji. <br> *Próg wyniku metryki*:  Minimalny wynik metryki dla wszystkich potoków. Dzięki temu w przypadku zdefiniowania metryki docelowej, która ma zostać osiągnięta, nie poświęcasz więcej czasu na zadanie szkoleniowe niż to konieczne.
    Przetwarzania wstępnego| Wybierz, aby włączyć lub wyłączyć przetwarzanie wstępne wykonywane przez automatyczne Uczenie maszynowe. Przetwarzanie wstępne obejmuje automatyczne czyszczenie danych, przygotowanie i transformację do generowania funkcji syntetycznych. [Dowiedz się więcej na temat przetwarzania wstępnego](#preprocess).
    Walidacja| Wybierz jedną z opcji krzyżowego sprawdzania poprawności, która ma zostać użyta w zadaniu szkoleniowym. [Dowiedz się więcej na temat krzyżowego sprawdzania poprawności](how-to-configure-auto-train.md).
    Współbieżność| Wybierz limity wielordzeniowe, które mają być używane podczas korzystania z obliczeń wielordzeniowych.
    Zablokowany algorytm| Wybierz algorytmy, które mają zostać wykluczone z zadania szkoleniowego.

   ![Formularz ustawień zaawansowanych](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Aby uzyskać więcej informacji na temat pól, kliknij etykietkę narzędzia informacji.

<a name="profile"></a>

### <a name="data-profiling"></a>Profilowanie danych

Możesz uzyskać szeroką gamę statystyk podsumowujących dla zestawu danych, aby sprawdzić, czy zestaw danych jest gotowy do użycia. W przypadku kolumn nieliczbowych składają się tylko podstawowe dane statystyczne, takie jak minimalna, maksymalna i liczba błędów. W przypadku kolumn liczbowych można także sprawdzić ich statystyczny czas i oszacować quantiles. Profil danych zawiera następujące dane:

* **Funkcja**: Nazwa sumowanej kolumny.

* **Profil**: wizualizacja w wierszu oparta na wywnioskowanym typie. Na przykład ciągi, wartości logiczne i daty będą mieć liczby wartości, podczas gdy miejsca dziesiętne (liczbowe) mają przybliżone histogramy. Pozwala to na szybkie zrozumienie dystrybucji danych.

* **Dystrybucja typów**: wartość w postaci liczby typów w kolumnie. Wartości null są własnym typem, więc Wizualizacja jest przydatna do wykrywania nieparzystych lub brakujących wartości.

* **Typ**: wnioskowany typ kolumny. Możliwe wartości to: ciągi, wartości logiczne, daty i miejsca dziesiętne.

* **Min**: wartość minimalna kolumny. Puste wpisy są wyświetlane dla funkcji, których typ nie ma własnej kolejności (np. wartości logicznych).

* **Max**: wartość maksymalna kolumny. Podobnie jak "min", puste wpisy są wyświetlane dla funkcji mających nieodpowiednie typy.

* **Liczba**: całkowita liczba brakujących i nieobecnych wpisów w kolumnie.

* **Brak liczby**: liczba wpisów w kolumnie, których nie ma. Należy zauważyć, że puste ciągi i błędy są traktowane jako wartości, więc nie współtworzyją one do "brakujący licznik".

* **Quantiles** (na 0,1, 1, 5, 25, 50, 75, 95, 99 i 99,9% interwałów): przybliżone wartości w każdym quantileym zapewniają sens dystrybucji danych. Puste wpisy są wyświetlane dla funkcji o nieistotnych typach.

* **Średnia**: średnia arytmetyczna kolumny. Puste wpisy są wyświetlane dla funkcji o nieistotnych typach.

* **Odchylenie standardowe**: odchylenie standardowe kolumny. Puste wpisy są wyświetlane dla funkcji o nieistotnych typach.

* **WARIANCJA**: Wariancja kolumny. Puste wpisy są wyświetlane dla funkcji o nieistotnych typach.

* **Skośność**: skośność kolumny. Puste wpisy są wyświetlane dla funkcji o nieistotnych typach.

* **Kurtoza**: kurtoza kolumny. Puste wpisy są wyświetlane dla funkcji o nieistotnych typach.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Zaawansowane przetwarzanie wstępne

Podczas konfigurowania eksperymentów można włączyć ustawienie `Preprocess`zaawansowane. Oznacza to, że następujące czynności związane z przetwarzaniem i cechowaniam danych są wykonywane automatycznie.

|&nbsp;Kroki przetwarzania wstępnego| Opis |
| ------------- | ------------- |
|Upuść dużej kardynalności lub żadne funkcje WARIANCJA|Porzuć je z zestawów szkoleniowych i walidacji, w tym funkcji ze wszystkimi wartościami, które są takie same, jak w przypadku wszystkich wierszy lub wyjątkowo dużej kardynalności (na przykład skrótów, identyfikatorów lub GUID).|
|Naliczenie brakujące wartości|W przypadku funkcji liczbowych wartość jest wartością obliczaną ze średnimi wartościami w kolumnie.<br/><br/>W przypadku funkcji kategorii wartość jest równa liczbie wartości.|
|Generuj dodatkowe funkcje|Dla funkcji DateTime: Rok, miesiąc, dzień, dzień tygodnia, dzień roku, kwartał, tydzień roku, godzina, minuta, sekunda.<br/><br/>Dla funkcji tekstowych: Częstotliwość okresu oparta na unigrams, bi-gramach i Tri-Character-Grams.|
|Przekształcanie i kodowanie |Funkcje liczbowe z kilkoma unikatowymi wartościami są przekształcane w funkcje kategorii.<br/><br/>Jednostronicowe kodowanie jest wykonywane dla niskiej kardynalności kategorii; w celu uzyskania dużej kardynalności kodowanie jedno-gorąca.|
|Osadzanie wyrazów|Tekst featurized, który konwertuje wektory tokenów tekstowych na wektory zdania przy użyciu modelu wstępnie przeszkolonego. Wektor osadzania każdego wyrazu w dokumencie jest agregowany w celu utworzenia wektora funkcji dokumentu.|
|Kodowanie docelowe|W przypadku funkcji kategorii odwzorowuje każdą kategorię ze średnią wartością docelową dla problemów z regresją oraz z prawdopodobieństwem klasy dla każdej klasy w przypadku problemów z klasyfikacją. Użycie wag opartych na częstotliwościach i w k-krotne sprawdzanie poprawności jest stosowane, aby zmniejszyć liczbę mapowań i szumów spowodowanych przez kategorie danych rozrzedzonych.|
|Kodowanie obiektu docelowego tekstu|W przypadku wprowadzania tekstu, skumulowany model liniowy z zbiorem słów jest używany do generowania prawdopodobieństwa każdej klasy.|
|Waga dowodu (WoE)|Oblicza WoE jako miarę korelacji kolumn kategorii z kolumną docelową. Jest ona obliczana jako dziennik proporcji prawdopodobieństwa klasy w klasie a. Ten krok powoduje wyjście z jednej kolumny funkcji liczbowej dla każdej klasy i eliminuje konieczność jawnego wyprowadzania brakujących wartości i odstającego traktowania.|
|Odległość klastra|Pociąga k-oznacza model klastra dla wszystkich kolumn liczbowych.  Wyprowadzaj nowe funkcje, jedną nową funkcję liczbową na klaster zawierający odległość poszczególnych próbek do centroida każdego klastra.|

## <a name="run-experiment-and-view-results"></a>Uruchamianie eksperymentu i wyświetlanie wyników

Aby uruchomić eksperyment, kliknij przycisk Uruchom. Proces przygotowywania eksperymentu trwa kilka minut.

### <a name="view-experiment-details"></a>Wyświetl szczegóły eksperymentu

Po zakończeniu fazy przygotowania eksperymentu zobaczysz ekran Szczegóły uruchamiania. Zapewnia to pełną listę utworzonych modeli. Domyślnie model, który ocenia najwyższy poziom w oparciu o parametry, znajduje się w górnej części listy. Gdy zadanie szkoleniowe próbuje więcej modeli, są dodawane do listy iteracji i wykresu. Użyj wykresu iteracji, aby szybko porównać metryki dla modeli produkowanych do tej pory.

Zadania szkoleniowe mogą potrwać trochę czasu dla każdego potoku.

![Pulpit nawigacyjny szczegółów uruchamiania](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Wyświetl szczegóły przebiegu szkoleniowego

Przejdź do szczegółów dowolnych modeli wyjściowych, aby zobaczyć szczegóły przebiegu szkoleniowego, takie jak metryki wydajności i wykresy dystrybucji. [Dowiedz się więcej na temat wykresów](how-to-track-experiments.md#understanding-automated-ml-charts).

![Szczegóły iteracji](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>Wdrażanie modelu

Gdy optymalny model jest dostępny, można go wdrożyć jako usługę sieci Web, aby przewidzieć nowe dane.

Automatyczna ML pomaga wdrożyć model bez pisania kodu:

1. Istnieje kilka opcji wdrażania. 
    1. Jeśli chcesz wdrożyć najlepszy model na podstawie kryteriów metryk ustawionych dla eksperymentu, wybierz pozycję **Wdróż najlepszy model** na stronie **szczegółów uruchamiania** .

        ![Przycisk wdrażania modelu](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. Jeśli chcesz wdrożyć iterację określonego modelu, przejdź do szczegółów modelu, aby otworzyć jego konkretną stronę szczegółów uruchamiania, a następnie wybierz pozycję **Wdróż model**.

        ![Przycisk wdrażania modelu](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. Pierwszym krokiem jest zarejestrowanie modelu w usłudze. Wybierz pozycję "Zarejestruj model" i poczekaj na zakończenie procesu rejestracji.

    ![Blok wdrażania modelu](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. Po zarejestrowaniu modelu będzie można pobrać skrypt oceniania (scoring.py) i skrypt środowiska (condaEnv. yml), który będzie używany podczas wdrażania.

1. Gdy pobierany jest skrypt oceniania i skrypt środowiska, przejdź do bloku **zasoby** okienka nawigacji po lewej stronie i wybierz pozycję **modele**.

    ![Modele okienka nawigacji](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Wybierz zarejestrowany model i wybierz pozycję "Utwórz obraz".

    Można zidentyfikować model według jego opisu, który będzie zawierać identyfikator przebiegu, numer iteracji, w następującym formacie: *< Run_ID > _ < Iteration_number > _Model*

    ![Przykładów Utwórz obraz](media/how-to-create-portal-experiments/model-create-image.png)

1. Wprowadź nazwę obrazu. 
1. Wybierz przycisk **Przeglądaj** znajdujący się obok pola "ocenianie pliku", aby załadować plik oceniania (Scoring.py), który został wcześniej pobrany.

1. Wybierz przycisk **Przeglądaj** obok pola "Conda plik", aby załadować plik środowiska (condaEnv. yml), który został wcześniej pobrany.

    Możesz użyć własnego skryptu oceniania i pliku Conda, a także przekazać dodatkowe pliki. [Dowiedz się więcej o skrypcie oceniania](how-to-deploy-and-where.md#script).

      >[!Important]
      > Nazwy plików muszą mieć długość 32 znaków i muszą zaczynać się i kończyć znakiem alfanumerycznym. Może zawierać łączniki, podkreślenia, kropki i znaki alfanumeryczne między. Spacje są niedozwolone.

    ![Utwórz obraz](media/how-to-create-portal-experiments/create-image.png)

1. Wybierz przycisk "Utwórz", aby rozpocząć tworzenie obrazu. Wykonanie tej czynności może potrwać kilka minut, a po zakończeniu zobaczysz komunikat na górnym pasku.
1. Przejdź do karty "obrazy", zaznacz pole wyboru obok obrazu, który chcesz wdrożyć, a następnie wybierz polecenie "Utwórz wdrożenie". [Dowiedz się więcej o wdrożeniach](how-to-deploy-and-where.md).

    Dostępne są dwie opcje wdrożenia.
     + Azure Container Instance (ACI) — Ta funkcja jest używana więcej na potrzeby testowania zamiast wdrażania operacyjnego w odpowiedniej skali. Upewnij się, że wartości dla co najmniej jednego rdzenia _procesora CPU_i co najmniej jeden GIGABAJT (GB) dla _pojemności rezerwy pamięci_
     + Azure Kubernetes Service (AKS)) — ta opcja jest dla wdrożenia w odpowiedniej skali. Konieczne będzie przygotowanie obliczeń opartych na AKS.

     ![Rastrow Tworzenie wdrożenia](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Po zakończeniu wybierz pozycję **Utwórz**. Wdrożenie modelu może potrwać kilka minut, aby zakończyć działanie każdego potoku.

1. To wszystko! Masz działającą usługę sieci Web do generowania prognoz.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o zautomatyzowanym uczeniu maszynowym](concept-automated-ml.md) i Azure Machine Learning.
* [Dowiedz się, jak korzystać z usługi sieci Web](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).

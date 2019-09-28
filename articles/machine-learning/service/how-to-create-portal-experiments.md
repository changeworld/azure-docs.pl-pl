---
title: Używanie interfejsu zautomatyzowanej sieci platformy Azure do uczenia & wdrażania modeli
titleSuffix: Azure Machine Learning
description: Twórz i wdrażaj automatyczne eksperymenty uczenia maszynowego na stronie docelowej obszaru roboczego Azure Machine Learning (wersja zapoznawcza).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 45207eb1cdc62f2468d8b0c052723337c18d5021
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350553"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learnings-workspace-landing-page-preview"></a>Twórz, eksploruj i wdrażaj automatyczne eksperymenty uczenia maszynowego za pomocą strony docelowej obszaru roboczego Azure Machine Learning (wersja zapoznawcza)

 W tym artykule dowiesz się, jak tworzyć, eksplorować i wdrażać zautomatyzowane eksperymenty uczenia maszynowego na stronie docelowej obszaru roboczego Azure Machine Learning bez pojedynczego wiersza kodu. Funkcja automatycznego uczenia maszynowego automatyzuje proces wybierania najlepszego algorytmu dla określonych danych, dzięki czemu można szybko generować model uczenia maszynowego. [Dowiedz się więcej o automatycznym uczeniu maszynowym](concept-automated-ml.md).

 Jeśli wolisz skorzystać z większej ilości kodu, możesz również [skonfigurować automatyczne eksperymenty uczenia maszynowego w języku Python](how-to-configure-auto-train.md) za pomocą [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

## <a name="get-started"></a>Rozpoczęcie pracy

1. Zaloguj się do [strony docelowej obszaru roboczego](https://ml.azure.com/workspaceportal/). 

1. Wybierz swoją subskrypcję i obszar roboczy. 

1. Przejdź do okienka po lewej stronie. Wybierz pozycję **zautomatyzowany ml** w sekcji **autor** .

[![Azure Portal okienku nawigacji](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Jeśli po raz pierwszy przeprowadzasz eksperymenty, zobaczysz ekran **wprowadzenie** . 

W przeciwnym razie zobaczysz pulpit nawigacyjny **automatycznego uczenia maszynowego** z omówieniem wszystkich zautomatyzowanych eksperymentów dotyczących uczenia maszynowego, łącznie z tymi utworzonymi za pomocą zestawu SDK. Tutaj można filtrować i eksplorować uruchomienia według daty, nazwy eksperymentu i stanu uruchomienia.

## <a name="create-and-run-experiment"></a>Tworzenie i uruchamianie eksperymentu

1. Wybierz pozycję **Utwórz eksperyment** i wypełnij formularz.

1. Wprowadź unikatową nazwę eksperymentu.

1. Wybierz obliczenia dla zadania profilowania i szkolenia danych. Lista istniejących obliczeń jest dostępna na liście rozwijanej. Aby utworzyć nowe obliczenie, postępuj zgodnie z instrukcjami podanymi w sekcji Krok 4.

1. Wybierz pozycję **Utwórz nowe obliczenie** , aby skonfigurować kontekst obliczeniowy dla tego eksperymentu.

    Pole|Opis
    ---|---
    Nazwa obliczeń| Wprowadź unikatową nazwę identyfikującą kontekst obliczeniowy.
    Rozmiar maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczenia.
    Minimalna/Maksymalna liczba węzłów (w ustawieniach zaawansowanych)| Aby profilować dane, musisz określić co najmniej jeden węzeł. Wprowadź maksymalną liczbę węzłów dla obliczeń. Wartość domyślna to 6 węzłów na potrzeby obliczeń AML.
    
    Wybierz pozycję **Utwórz**. Tworzenie nowego obliczenia może potrwać kilka minut.

    >[!NOTE]
    > Nazwa obliczeniowa wskazuje, czy w przypadku obliczeń, które zostały wybrane/utworzone, *włączono profilowanie*. (Szczegółowe informacje znajdują się w sekcji [Profilowanie danych](#profile) ).

1. Wybierz zestaw danych z kontenera magazynu lub utwórz go, przekazując plik z komputera lokalnego do kontenera. Publiczna wersja zapoznawcza obsługuje tylko lokalne operacje przekazywania plików i konta usługi Azure Blob Storage.

    >[!Important]
    > Wymagania dotyczące danych szkoleniowych:
    >* Dane muszą być w formie tabelarycznej.
    >* Wartość, która ma zostać przewidywalna (kolumna docelowa), musi być obecna w danych.

    1. Aby utworzyć nowy zestaw danych z pliku w lokalnym obliczeniu, wybierz pozycję **Przeglądaj** , a następnie wybierz plik. 

    1. Nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz pozycję **dalej** , aby przekazać ją do domyślnego kontenera magazynu, który jest automatycznie tworzony w obszarze roboczym, lub wybierz kontener magazynu, który ma być używany do eksperymentu. 

    1. Przejrzyj **Ustawienia i Podgląd** w formularzu pod kątem dokładności. Formularz jest inteligentnie wypełniany na podstawie typu pliku. 

        Pole| Opis
        ----|----
        Format pliku| Definiuje układ i typ danych przechowywanych w pliku.
        Ogranicznik| Jeden lub więcej znaków do określenia granicy między oddzielnymi, niezależnymi regionami w postaci zwykłego tekstu lub innymi strumieniami danych.
        Kodowanie| Identyfikuje tablicę znaków, która ma być używana do odczytywania zestawu danych.
        Nagłówki kolumn| Wskazuje, w jaki sposób nagłówki zestawu danych (jeśli istnieją) będą traktowane.
        Pomiń wiersze | Wskazuje, ile (jeśli istnieją) wiersze są pomijane w zestawie danych.
    
        Wybierz opcję **Dalej**.

    1. Formularz **schematu** jest inteligentnie wypełniany na podstawie opcji wybranych w formularzu **Ustawienia i Podgląd** . W tym miejscu należy skonfigurować typ danych dla każdej kolumny, sprawdzić nazwy kolumn i wybrać kolumny, które **nie mają być dołączone** do eksperymentu. 
            
        Wybierz pozycję **Dalej.**

1. Wybierz typ zadania szkolenia: Klasyfikacja, regresja lub prognozowanie.

1. Wybierz kolumnę docelową; jest to kolumna, dla której chcesz przeprowadzić prognozę.

1. W przypadku prognozowania:
    1. Wybierz kolumnę czasu: Ta kolumna zawiera dane czasu, które mają być używane.

    1. Wybierz horyzont prognoz: Wskaż, ile jednostek czasu (min/godz./dni/tygodnie/miesięcy/lat) będzie można przewidzieć w przyszłości model. Dalszy model jest wymagany do przewidywania w przyszłości, tym mniej dokładne stanie się. [Dowiedz się więcej o prognozowaniu i prognozowaniu horyzontu](how-to-auto-train-forecast.md).

1. Obowiązkowe Ustawienia zaawansowane: dodatkowe ustawienia, których można użyć w celu lepszego kontrolowania zadania szkoleniowego. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych. 

    Ustawienia zaawansowane|Opis
    ------|------
    Metryka podstawowa| Główna Metryka używana do oceniania modelu. [Dowiedz się więcej o metrykach modelu](how-to-configure-auto-train.md#explore-model-metrics).
    Kryteria wyjścia| Po spełnieniu któregokolwiek z tych kryteriów zadanie szkolenia zostanie zatrzymane. <br> *Czas zadania szkoleniowego (minuty)* : Jak długo zezwolić na uruchomienie zadania szkoleniowego.  <br> *Maksymalna liczba iteracji*: Maksymalna liczba potoków (iteracji) do przetestowania w zadaniu szkoleniowym. Zadanie nie zostanie uruchomione więcej niż określona liczba iteracji. <br> *Próg wyniku metryki*:  Minimalny wynik metryki dla wszystkich potoków. Dzięki temu w przypadku zdefiniowania metryki docelowej, która ma zostać osiągnięta, nie poświęcasz więcej czasu na zadanie szkoleniowe niż to konieczne.
    Przetwarzania wstępnego| Wybierz, aby włączyć lub wyłączyć przetwarzanie wstępne wykonywane przez automatyczne Uczenie maszynowe. Przetwarzanie wstępne obejmuje automatyczne czyszczenie danych, przygotowanie i transformację do generowania funkcji syntetycznych. [Dowiedz się więcej na temat przetwarzania wstępnego](#preprocess).
    Sprawdzanie poprawności| Wybierz jedną z opcji krzyżowego sprawdzania poprawności, która ma zostać użyta w zadaniu szkoleniowym. [Dowiedz się więcej na temat krzyżowego sprawdzania poprawności](how-to-configure-auto-train.md).
    Współbieżność| Wybierz limity wielordzeniowe, które mają być używane podczas korzystania z obliczeń wielordzeniowych.
    Zablokowany algorytm| Wybierz algorytmy, które mają zostać wykluczone z zadania szkoleniowego.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Dane statystyczne podsumowania &

Możesz uzyskać szeroką gamę statystyk podsumowujących dla zestawu danych, aby sprawdzić, czy zestaw danych jest gotowy do użycia. W przypadku kolumn nieliczbowych składają się tylko podstawowe dane statystyczne, takie jak minimalna, maksymalna i liczba błędów. W przypadku kolumn liczbowych można także sprawdzić ich statystyczny czas i oszacować quantiles. Profil danych zawiera następujące dane:

>[!NOTE]
> Puste wpisy są wyświetlane dla funkcji o nieistotnych typach.

Statystyka|Opis
------|------
Cecha| Nazwa sumowanej kolumny.
Profil| Wizualizacja w wierszu oparta na wywnioskowanym typie. Na przykład ciągi, wartości logiczne i daty będą mieć liczby wartości, podczas gdy miejsca dziesiętne (liczbowe) mają przybliżone histogramy. Pozwala to na szybkie zrozumienie dystrybucji danych.
Dystrybucja typów| Liczba wartości w wierszu dla typów w kolumnie. Wartości null są własnym typem, więc Wizualizacja jest przydatna do wykrywania nieparzystych lub brakujących wartości.
Type|Wywnioskowany typ kolumny. Możliwe wartości to: ciągi, wartości logiczne, daty i miejsca dziesiętne.
Min.| Minimalna wartość kolumny. Puste wpisy są wyświetlane dla funkcji, których typ nie ma własnej kolejności (np. wartości logicznych).
Maks.| Maksymalna wartość kolumny. 
Count| Łączna liczba brakujących i nieobecnych wpisów w kolumnie.
Liczba niebrakujących| Liczba wpisów w kolumnie, których nie ma. Puste ciągi i błędy są traktowane jako wartości, więc nie będą wchodzić w skład "niebrakującej liczby".
Kwantyle| Przybliżone wartości dla każdego quantileu, aby zapewnić rozkład danych.
Średnia| Średnia arytmetyczna kolumny lub jej średnia.
Odchylenie standardowe| Pomiar wielkości rozproszenia lub zmienności danych tej kolumny.
Wariancja| Mierzona, jak daleko odłożenie danych z tej kolumny pochodzi z wartości średniej. 
Skośność| Mierzona, jak różne dane tej kolumny pochodzą z rozkładu normalnego.
Kurtoza| Mierzona, jak silnie naśladowanie danych tej kolumny jest porównywane z rozkładem normalnym.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Zaawansowane opcje przetwarzania wstępnego

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

Wybierz pozycję **Rozpocznij** , aby uruchomić eksperyment. Proces przygotowywania eksperymentu trwa kilka minut.

### <a name="view-experiment-details"></a>Wyświetl szczegóły eksperymentu

Po zakończeniu fazy przygotowania eksperymentu zobaczysz ekran Szczegóły uruchamiania, aby wypełnić. Ten ekran zawiera pełną listę utworzonych modeli. Domyślnie model, który ocenia najwyższy poziom w oparciu o wybraną metrykę, znajduje się w górnej części listy. Gdy zadanie szkoleniowe próbuje więcej modeli, są dodawane do listy iteracji i wykresu. Użyj wykresu iteracji, aby szybko porównać metryki dla modeli produkowanych do tej pory.

Zadania szkoleniowe mogą potrwać trochę czasu dla każdego potoku.

[![Pulpit nawigacyjny szczegółów uruchamiania](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Wyświetl szczegóły przebiegu szkoleniowego

Przejdź do szczegółów dowolnych modeli wyjściowych, aby zobaczyć szczegóły przebiegu szkoleniowego, takie jak metryki wydajności i wykresy dystrybucji. [Dowiedz się więcej na temat wykresów](how-to-understand-automated-ml.md).

[![Szczegóły iteracji](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Wdrażanie modelu

Gdy optymalny model jest dostępny, można go wdrożyć jako usługę sieci Web, aby przewidzieć nowe dane.

Automatyczna ML pomaga wdrożyć model bez pisania kodu:

1. Istnieje kilka opcji wdrażania. 

    + Option 1: Aby wdrożyć najlepszy model (zgodnie ze zdefiniowanymi kryteriami metryki), wybierz pozycję Wdróż najlepszy model na stronie szczegółów uruchamiania.

    + Opcja 2: Aby wdrożyć określoną iterację modelu z tego eksperymentu, przejdź do szczegółów modelu, aby otworzyć stronę szczegółów uruchamiania, a następnie wybierz pozycję Wdróż model.

1. Wypełnij okienko **Wdróż model** .

    Pole| Value
    ----|----
    Nazwa wdrożenia| Wprowadź unikatową nazwę wdrożenia.
    Opis wdrożenia| Wprowadź opis, aby lepiej zidentyfikować to wdrożenie.
    Skrypt oceniania| Automatycznie Generuj lub Przekaż własny plik oceniania. [Dowiedz się więcej o skrypcie oceniania](how-to-deploy-and-where.md#script)
    Skrypt środowiska| Automatycznie Generuj lub Przekaż własny plik środowiska.
    >[!Important]
    > Nazwy plików muszą mieć długość 32 znaków i muszą zaczynać się i kończyć znakiem alfanumerycznym. Może zawierać łączniki, podkreślenia, kropki i znaki alfanumeryczne między. Spacje są niedozwolone.

1. Wybierz pozycję **Wdróż**. Wdrożenie może potrwać około 20 minut.

    Po pomyślnym zakończeniu wdrażania zostanie wyświetlony następujący komunikat.

    ![Wdrażanie ukończone](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png) 

Teraz masz działającą usługę sieci Web do generowania prognoz!

## <a name="next-steps"></a>Następne kroki

* Wypróbuj kompleksowy samouczek dotyczący [tworzenia pierwszego zautomatyzowanego eksperymentu ml z Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 
* [Dowiedz się więcej o zautomatyzowanym uczeniu maszynowym](concept-automated-ml.md) i Azure Machine Learning.
* Zapoznaj się z [automatycznymi wynikami uczenia maszynowego](how-to-understand-automated-ml.md).
* [Dowiedz się, jak korzystać z usługi sieci Web](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).

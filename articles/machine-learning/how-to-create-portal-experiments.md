---
title: Kompiluj & Wdrażaj zautomatyzowane modele ML
titleSuffix: Azure Machine Learning
description: Twórz i wdrażaj automatyczne eksperymenty uczenia maszynowego w programie Azure Machine Learning Studio oraz zarządzaj nimi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 00a316f69cfa77d705a789d40868105e9a098def
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894018"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learning-studio"></a>Twórz, eksploruj i wdrażaj zautomatyzowane eksperymenty uczenia maszynowego za pomocą programu Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

 W tym artykule dowiesz się, jak tworzyć, eksplorować i wdrażać zautomatyzowane eksperymenty uczenia maszynowego w programie Azure Machine Learning Studio bez pojedynczego wiersza kodu. Funkcja automatycznego uczenia maszynowego automatyzuje proces wybierania najlepszego algorytmu dla określonych danych, dzięki czemu można szybko generować model uczenia maszynowego. [Dowiedz się więcej o automatycznym uczeniu maszynowym](concept-automated-ml.md).

 Jeśli wolisz skorzystać z większej ilości kodu, możesz również [skonfigurować automatyczne eksperymenty uczenia maszynowego w języku Python](how-to-configure-auto-train.md) za pomocą [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Obszar roboczy Azure Machine Learning z typem **wersji Enterprise Edition**. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).  Aby uaktualnić istniejący obszar roboczy do wersji Enterprise, zobacz [uaktualnianie do wersji Enterprise Edition](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Rozpocznij

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com). 

1. Wybierz swoją subskrypcję i obszar roboczy. 

1. Przejdź do okienka po lewej stronie. Wybierz pozycję **zautomatyzowany ml** w sekcji **autor** .

[okienko nawigacji ![Azure Machine Learning Studio](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Jeśli po raz pierwszy przeprowadzisz eksperymenty, zobaczysz pustą listę i linki do dokumentacji. 

W przeciwnym razie zostanie wyświetlona lista ostatnich zautomatyzowanych eksperymentów dotyczących uczenia maszynowego, w tym tych utworzonych za pomocą zestawu SDK. 

## <a name="create-and-run-experiment"></a>Tworzenie i uruchamianie eksperymentu

1. Wybierz pozycję **+ Utwórz eksperyment** i wypełnij formularz.

1. Wybierz zestaw danych z kontenera magazynu lub Utwórz nowy zestaw danych. Zestawy danych mogą być tworzone na podstawie plików lokalnych, adresów URL sieci Web, magazynów danych lub otwartych zestawów danych platformy Azure. 

    >[!Important]
    > Wymagania dotyczące danych szkoleniowych:
    >* Dane muszą być w formie tabelarycznej.
    >* Wartość, która ma zostać przewidywalna (kolumna docelowa), musi być obecna w danych.

    1. Aby utworzyć nowy zestaw danych z pliku na komputerze lokalnym, wybierz pozycję **Przeglądaj** , a następnie wybierz plik. 

    1. Nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz pozycję **dalej**, aby przekazać ją do domyślnego kontenera magazynu, który jest automatycznie tworzony w obszarze roboczym, lub wybierz kontener magazynu, który ma być używany do eksperymentu. 

    1. Przejrzyj **Ustawienia i Podgląd** w formularzu pod kątem dokładności. Formularz jest inteligentnie wypełniany na podstawie typu pliku. 

        Pole| Opis
        ----|----
        Format pliku| Definiuje układ i typ danych przechowywanych w pliku.
        Ogranicznik| Jeden lub więcej znaków do określenia granicy między oddzielnymi, niezależnymi regionami w postaci zwykłego tekstu lub innymi strumieniami danych.
        Encoding| Identyfikuje tablicę znaków, która ma być używana do odczytywania zestawu danych.
        Nagłówki kolumn| Wskazuje, w jaki sposób nagłówki zestawu danych (jeśli istnieją) będą traktowane.
        Pomiń wiersze | Wskazuje, ile (jeśli istnieją) wiersze są pomijane w zestawie danych.
    
        Wybierz opcję **Dalej**.

    1. Formularz **schematu** jest inteligentnie wypełniany na podstawie opcji wybranych w formularzu **Ustawienia i Podgląd** . W tym miejscu należy skonfigurować typ danych dla każdej kolumny, sprawdzić nazwy kolumn i wybrać kolumny, które **nie mają być dołączone** do eksperymentu. 
            
        Wybierz pozycję **Dalej.**

    1. Formularz **Potwierdź szczegóły** to podsumowanie informacji poprzednio wypełnionych w **podstawowych informacjach** i ustawieniach oraz w formularzach **wersji zapoznawczej** . Istnieje również możliwość profilowania zestawu danych przy użyciu obliczeń z włączoną obsługą profilowania. Dowiedz się więcej na temat [profilowania danych](#profile).

        Wybierz opcję **Dalej**.
1. Wybierz nowo utworzony zestaw danych, gdy zostanie wyświetlony. Możliwe jest również wyświetlanie podglądu zestawu danych i przykładowych statystyk. 

1. W formularzu **Konfigurowanie przebiegu** wprowadź unikatową nazwę eksperymentu.

1. Wybierz kolumnę docelową; jest to kolumna, dla której chcesz przeprowadzić prognozę.

1. Wybierz obliczenia dla zadania profilowania i szkolenia danych. Lista istniejących obliczeń jest dostępna na liście rozwijanej. Aby utworzyć nowe obliczenie, postępuj zgodnie z instrukcjami podanymi w kroku 7.

1. Wybierz pozycję **Utwórz nowe obliczenie** , aby skonfigurować kontekst obliczeniowy dla tego eksperymentu.

    Pole|Opis
    ---|---
    Nazwa obliczeniowa| Wprowadź unikatową nazwę identyfikującą kontekst obliczeniowy.
    Rozmiar maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczenia.
    Minimalna/Maksymalna liczba węzłów (w ustawieniach zaawansowanych)| Aby profilować dane, musisz określić co najmniej jeden węzeł. Wprowadź maksymalną liczbę węzłów dla obliczeń. Wartość domyślna to 6 węzłów na potrzeby obliczeń AML.
    
    Wybierz pozycję **Utwórz**. Tworzenie nowego obliczenia może potrwać kilka minut.

    >[!NOTE]
    > Nazwa obliczeniowa wskazuje, czy w przypadku obliczeń, które zostały wybrane/utworzone, *włączono profilowanie*. (Szczegółowe informacje znajdują się w sekcji [Profilowanie danych](#profile) ).

    Wybierz opcję **Dalej**.

1. W formularzu **Typ zadania i ustawienia** wybierz typ zadania: Klasyfikacja, regresja lub prognozowanie. Zobacz [jak zdefiniować typy zadań,](how-to-define-task-type.md) Aby uzyskać więcej informacji.

    1. W przypadku klasyfikacji można również włączyć uczenie głębokie, które jest używane dla featurizations tekstu.

    1. W przypadku prognozowania:
        1. Wybierz kolumnę czasu: Ta kolumna zawiera dane czasu, które mają być używane.

        1. Wybierz horyzont prognoz: wskaż, ile jednostek czasu (min/godzina/dni/tygodnie/miesiące/lata) będzie można przewidzieć na przyszłość. Dalszy model jest wymagany do przewidywania w przyszłości, tym mniej dokładne stanie się. [Dowiedz się więcej o prognozowaniu i prognozowaniu horyzontu](how-to-auto-train-forecast.md).

1. Obowiązkowe Konfiguracje dodawania: dodatkowe ustawienia, których można użyć w celu lepszego kontrolowania zadania szkoleniowego. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych. 

    Dodatkowe konfiguracje|Opis
    ------|------
    Metryka podstawowa| Główna Metryka używana do oceniania modelu. [Dowiedz się więcej o metrykach modelu](how-to-configure-auto-train.md#explore-model-metrics).
    Automatyczne cechowania| Wybierz, aby włączyć lub wyłączyć przetwarzanie wstępne wykonywane przez automatyczne Uczenie maszynowe. Przetwarzanie wstępne obejmuje automatyczne czyszczenie danych, przygotowanie i transformację do generowania funkcji syntetycznych. [Dowiedz się więcej na temat przetwarzania wstępnego](#preprocess).
    Zablokowany algorytm| Wybierz algorytmy, które mają zostać wykluczone z zadania szkoleniowego.
    Kryterium zakończenia| Po spełnieniu któregokolwiek z tych kryteriów zadanie szkolenia zostanie zatrzymane. <br> *Czas zadania szkoleniowego (godz.)* : jak długo zezwolić na uruchomienie zadania szkoleniowego. <br> *Próg wyniku metryki*: minimalny wynik metryki dla wszystkich potoków. Dzięki temu w przypadku zdefiniowania metryki docelowej, która ma zostać osiągnięta, nie poświęcasz więcej czasu na zadanie szkoleniowe niż to konieczne.
    Sprawdzanie poprawności| Wybierz jedną z opcji krzyżowego sprawdzania poprawności, która ma zostać użyta w zadaniu szkoleniowym. [Dowiedz się więcej na temat krzyżowego sprawdzania poprawności](how-to-configure-auto-train.md).
    Współbieżność| *Maksymalna liczba współbieżnych iteracji*: maksymalną liczbę potoków (iteracji) do przetestowania w zadaniu szkoleniowym. Zadanie nie zostanie uruchomione więcej niż określona liczba iteracji. <br> *Maksymalna liczba rdzeni na iterację*: Wybierz limity wielordzeniowe, które mają być używane podczas korzystania z obliczeń wielordzeniowych.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Dane statystyczne podsumowania &

Możesz uzyskać szeroką gamę statystyk podsumowujących dla zestawu danych, aby sprawdzić, czy zestaw danych jest gotowy do użycia. W przypadku kolumn nieliczbowych składają się tylko podstawowe dane statystyczne, takie jak minimalna, maksymalna i liczba błędów. W przypadku kolumn liczbowych można także sprawdzić ich statystyczny czas i oszacować quantiles. Profil danych zawiera następujące dane:

>[!NOTE]
> Puste wpisy są wyświetlane dla funkcji o nieistotnych typach.

Statystyka|Opis
------|------
Funkcja| Nazwa sumowanej kolumny.
Profil| Wizualizacja w wierszu oparta na wywnioskowanym typie. Na przykład ciągi, wartości logiczne i daty będą mieć liczby wartości, podczas gdy miejsca dziesiętne (liczbowe) mają przybliżone histogramy. Pozwala to na szybkie zrozumienie dystrybucji danych.
Dystrybucja typów| Liczba wartości w wierszu dla typów w kolumnie. Wartości null są własnym typem, więc Wizualizacja jest przydatna do wykrywania nieparzystych lub brakujących wartości.
Typ|Wywnioskowany typ kolumny. Możliwe wartości to: ciągi, wartości logiczne, daty i miejsca dziesiętne.
Min.| Minimalna wartość kolumny. Puste wpisy są wyświetlane dla funkcji, których typ nie ma własnej kolejności (np. wartości logicznych).
Maks.| Maksymalna wartość kolumny. 
Liczba| Łączna liczba brakujących i nieobecnych wpisów w kolumnie.
Liczba niebrakujących| Liczba wpisów w kolumnie, których nie ma. Puste ciągi i błędy są traktowane jako wartości, więc nie będą wchodzić w skład "niebrakującej liczby".
Quantiles| Przybliżone wartości dla każdego quantileu, aby zapewnić rozkład danych.
Średnia| Średnia arytmetyczna kolumny lub jej średnia.
Odchylenie standardowe| Pomiar wielkości rozproszenia lub zmienności danych tej kolumny.
Wariancja| Mierzona, jak daleko odłożenie danych z tej kolumny pochodzi z wartości średniej. 
Skośność| Mierzona, jak różne dane tej kolumny pochodzą z rozkładu normalnego.
Kurtoza| Mierzona, jak silnie naśladowanie danych tej kolumny jest porównywane z rozkładem normalnym.


<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Zaawansowane opcje przetwarzania wstępnego

Podczas konfigurowania eksperymentów można włączyć ustawienie zaawansowane `Preprocess`. Oznacza to, że w ramach wstępnego przetwarzania następujących etapów danych guardrails i cechowania są wykonywane automatycznie.

|Przetwarzanie wstępne&nbsp;kroków| Opis |
| ------------- | ------------- |
|Upuść dużej kardynalności lub żadne funkcje WARIANCJA|Porzuć je z zestawów szkoleniowych i walidacji, w tym funkcji ze wszystkimi wartościami, które są takie same, jak w przypadku wszystkich wierszy lub wyjątkowo dużej kardynalności (na przykład skrótów, identyfikatorów lub GUID).|
|Naliczenie brakujące wartości|W przypadku funkcji liczbowych wartość jest wartością obliczaną ze średnimi wartościami w kolumnie.<br/><br/>W przypadku funkcji kategorii wartość jest równa liczbie wartości.|
|Generuj dodatkowe funkcje|Funkcje daty/godziny: rok, miesiąc, dzień, dzień tygodnia, dnia rok, kwartał, tydzień roku, godzina, minuta, sekunda.<br/><br/>W przypadku funkcji tekstowych: Częstotliwość okresu oparta na unigrams, bi-gramach i Tri-Character-Grams.|
|Przekształcanie i kodowanie |Funkcje liczbowe z kilkoma unikatowymi wartościami są przekształcane w funkcje kategorii.<br/><br/>Jednostronicowe kodowanie jest wykonywane dla niskiej kardynalności kategorii; w celu uzyskania dużej kardynalności kodowanie jedno-gorąca.|
|Osadzanie wyrazów|Tekst featurized, który konwertuje wektory tokenów tekstowych na wektory zdania przy użyciu modelu wstępnie przeszkolonego. Wektor osadzania każdego wyrazu w dokumencie jest agregowany w celu utworzenia wektora funkcji dokumentu.|
|Kodowanie docelowe|W przypadku funkcji kategorii odwzorowuje każdą kategorię ze średnią wartością docelową dla problemów z regresją oraz z prawdopodobieństwem klasy dla każdej klasy w przypadku problemów z klasyfikacją. Użycie wag opartych na częstotliwościach i w k-krotne sprawdzanie poprawności jest stosowane, aby zmniejszyć liczbę mapowań i szumów spowodowanych przez kategorie danych rozrzedzonych.|
|Kodowanie obiektu docelowego tekstu|W przypadku wprowadzania tekstu, skumulowany model liniowy z zbiorem słów jest używany do generowania prawdopodobieństwa każdej klasy.|
|Waga dowodu (WoE)|Oblicza WoE jako miarę korelacji kolumn kategorii z kolumną docelową. Jest ona obliczana jako dziennik proporcji prawdopodobieństwa klasy w klasie a. Ten krok powoduje wyjście z jednej kolumny funkcji liczbowej dla każdej klasy i eliminuje konieczność jawnego wyprowadzania brakujących wartości i odstającego traktowania.|
|Odległość klastra|Pociąga k-oznacza model klastra dla wszystkich kolumn liczbowych.  Wyprowadzaj nowe funkcje, jedną nową funkcję liczbową na klaster zawierający odległość poszczególnych próbek do centroida każdego klastra.|

### <a name="data-guardrails"></a>Guardrails danych

Automatyczne Uczenie maszynowe oferuje guardrailsy danych, które ułatwiają identyfikację potencjalnych problemów dotyczących danych (np. brakujących wartości, nierównowagi klasy) i ułatwiają podejmowanie działań naprawczych w celu uzyskania lepszych wyników. Istnieje wiele najlepszych rozwiązań, które są dostępne i mogą być stosowane w celu uzyskania wiarygodnych wyników. 

W poniższej tabeli opisano aktualnie obsługiwane guardrails danych oraz powiązane Stany, które użytkownicy mogą napotkać podczas przesyłania eksperymentu.

Guardrail|Stan|&nbsp;warunku dla wyzwalacza&nbsp;
---|---|---
Brak wartości&nbsp;&nbsp;ich przypisywaniu |**Przekazany** <br> <br> **Stały**|    Brak wartości w żadnej z&nbsp;wejściowych kolumn <br> <br> Niektóre kolumny mają brakujące wartości
Krzyżowe sprawdzanie poprawności|**Odbywać**|Jeśli nie podano jawnego zestawu walidacji
Wysoka&nbsp;kardynalności&nbsp;funkcja wykrywania&nbsp;|  **Przekazany** <br> <br>**Odbywać**|   Nie wykryto żadnych funkcji wysokiej kardynalności <br><br> Wykryto wysoką liczebność kolumn wejściowych
Wykrywanie balansu klas |**Przekazany** <br><br><br>**Alerty** |Klasy są zrównoważone w danych szkoleniowych; Zestaw danych jest uznawany za zrównoważony, jeśli każda klasa ma dobrą reprezentację w zestawie danych, zgodnie z liczbą i stosunkiem próbek <br> <br> Klasy w danych szkoleniowych są niezrównoważone
Spójność danych szeregów czasowych|**Przekazany** <br><br><br><br> **Stały** |<br> Przeanalizowane wartości wybranych elementów {Horizon, lag, kroczących} i nie wykryto problemów braku związanych z pamięcią. <br> <br>Przeanalizowane zostały wartości wybrane w polu {Horizon, zwłoka, krocząca}, co może spowodować, że w Twoim doświadczeniu zabrakło pamięci. Okno zwłoki lub stopniowe zostało wyłączone.

## <a name="run-experiment-and-view-results"></a>Uruchamianie eksperymentu i wyświetlanie wyników

Wybierz pozycję **Rozpocznij** , aby uruchomić eksperyment. Proces przygotowywania eksperymentu może potrwać do 10 minut. Zadania szkoleniowe mogą zająć więcej niż 2-3 minut więcej czasu dla każdego potoku.

### <a name="view-experiment-details"></a>Wyświetl szczegóły eksperymentu

>[!NOTE]
> Należy okresowo wybierać pozycję **Odśwież** , aby wyświetlić stan uruchomienia. 

Zostanie otwarty ekran Szczegóły **uruchamiania** na karcie **szczegóły** . Na tym ekranie przedstawiono podsumowanie przebiegu eksperymentu, w tym **stan uruchomienia**. 

Karta **modele** zawiera listę modeli utworzonych uporządkowaną według wyniku pomiaru. Domyślnie model, który ocenia najwyższy poziom w oparciu o wybraną metrykę, znajduje się w górnej części listy. Gdy zadanie szkoleniowe próbuje więcej modeli, zostaną dodane do listy. Użyj tego, aby szybko porównać metryki dla modeli produkowanych do tej pory.

[Pulpit nawigacyjny szczegółów uruchamiania ![](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Wyświetl szczegóły przebiegu szkoleniowego

Przejdź do szczegółów dowolnego z zakończonych modeli, aby zobaczyć szczegóły przebiegu szkoleniowego, takie jak uruchamianie metryk na karcie **Szczegóły modelu** lub na wykresach wydajności na karcie **wizualizacje** . [Dowiedz się więcej na temat wykresów](how-to-understand-automated-ml.md).

[Szczegóły iteracji ![](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Wdrażanie modelu

Gdy optymalny model jest dostępny, można go wdrożyć jako usługę sieci Web, aby przewidzieć nowe dane.

Automatyczna ML pomaga wdrożyć model bez pisania kodu:

1. Istnieje kilka opcji wdrażania. 

    + Opcja 1: Aby wdrożyć najlepszy model (zgodnie ze zdefiniowanymi kryteriami metryki), wybierz pozycję Wdróż najlepszy model na karcie Szczegóły.

    + Opcja 2: Aby wdrożyć określoną iterację modelu z tego eksperymentu, przejdź do szczegółów modelu, aby otworzyć kartę Szczegóły modelu i wybierz pozycję Wdróż model.

1. Wypełnij okienko **Wdróż model** .

    Pole| Wartość
    ----|----
    Nazwa| Wprowadź unikatową nazwę wdrożenia.
    Opis| Wprowadź opis, aby lepiej zidentyfikować to wdrożenie.
    Typ obliczeń| Wybierz typ punktu końcowego, który chcesz wdrożyć: *Azure Kubernetes Service (AKS)* lub *Azure Container Instance (ACI)* .
    Nazwa| *Dotyczy tylko AKS:* Wybierz nazwę klastra AKS, w którym chcesz wdrożyć.
    Włączanie uwierzytelniania | Wybierz, aby zezwalać na uwierzytelnianie oparte na tokenach lub na podstawie klucza.
    Używanie niestandardowych zasobów wdrażania| Włącz tę funkcję, jeśli chcesz przekazać własny skrypt oceniania i plik środowiska. [Dowiedz się więcej o skryptach oceniania](how-to-deploy-and-where.md#script).

    >[!Important]
    > Nazwy plików muszą mieć długość 32 znaków i muszą zaczynać się i kończyć znakiem alfanumerycznym. Może zawierać łączniki, podkreślenia, kropki i znaki alfanumeryczne między. Spacje są niedozwolone.

    Menu *Zaawansowane* oferuje domyślne funkcje wdrażania, takie jak [gromadzenie danych](how-to-enable-app-insights.md) i ustawienia wykorzystania zasobów. Jeśli chcesz przesłonić te ustawienia domyślne, zrób to w menu.

1. Wybierz pozycję **Wdróż**. Wdrożenie może potrwać około 20 minut.

Teraz masz działającą usługę sieci Web do generowania prognoz! Możesz przetestować przewidywania, wykonując zapytania dotyczące usługi, korzystając z [Power BI wbudowanej Azure Machine Learning obsługi](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Następne kroki

* Wypróbuj kompleksowy samouczek dotyczący [tworzenia pierwszego zautomatyzowanego eksperymentu ml z Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 
* [Dowiedz się więcej o zautomatyzowanym uczeniu maszynowym](concept-automated-ml.md) i Azure Machine Learning.
* Zapoznaj się z [automatycznymi wynikami uczenia maszynowego](how-to-understand-automated-ml.md).
* [Dowiedz się, jak korzystać z usługi sieci Web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).

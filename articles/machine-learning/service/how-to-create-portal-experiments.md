---
title: Tworzenie i zapoznaj się z eksperymentów w portalu
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak tworzyć i zarządzać nimi automatyczne usługi machine learning eksperymenty w portalu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: a2a281fda9272fb794692becb0ca08f3cf791458
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990151"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Tworzenie i eksplorowanie zautomatyzowanych usługi machine learning eksperymentów w witrynie Azure portal (wersja zapoznawcza)

 W tym artykule dowiesz się, jak tworzenie, uruchamianie i eksplorowanie zautomatyzowanych usługi machine learning eksperymentów w witrynie Azure portal, bez jednego wiersza kodu. Uczenie maszynowe automatycznych automatyzuje proces wybierania najlepszego algorytmu do użycia dla określonych danych, dzięki czemu można szybko wygenerować model uczenia maszynowego. [Dowiedz się więcej na temat uczenia maszynowego automatycznych](concept-automated-ml.md).

 Jeśli wolisz bardziej oparte na kodzie środowisko, możesz również [skonfigurowanie automatycznych maszyny eksperymentów w języku Python uczenia](how-to-configure-auto-train.md) z [zestawu SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Utwórz obszar roboczy usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Rozpoczęcie pracy

Przejdź do okienka po lewej stronie obszaru roboczego. W sekcji Tworzenie (wersja zapoznawcza), wybierz opcję automatycznego uczenia maszynowego.

![W okienku nawigacji w portalu Azure](media/how-to-create-portal-experiments/nav-pane.png)

 Jeśli jest to sposób wszystkie eksperymenty przy użyciu zautomatyzowanego uczenia maszynowego po raz pierwszy, zostanie wyświetlone następujące czynności:

![Strona docelowa eksperymentu portalu Azure](media/how-to-create-portal-experiments/landing-page.png)

W przeciwnym razie zostanie wyświetlony pulpit nawigacyjny learning maszyny automatyczne przegląd wszystkich zautomatyzowanych maszyny uczenia eksperymenty, w tym te utworzone za pomocą zestawu SDK. W tym miejscu można filtrować i eksplorowanie przebiegów według daty, eksperymentowanie nazwę i stan uruchomienia.

![Pulpit nawigacyjny eksperymentu portalu Azure](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Tworzenie eksperymentu

Wybierz przycisk tworzenia eksperymentu, aby wypełnić następującą postać.

![Tworzenie formularza eksperymentu](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Wprowadź nazwę eksperymentu.

1. Wybierz zasoby obliczeniowe dla danych profilowania i zadania szkolenia. Lista swoje istniejące jednostki obliczeniowe jest dostępna na liście rozwijanej. Aby utworzyć nowe obliczenie, postępuj zgodnie z instrukcjami w kroku 3.

1. Wybierz pozycję Utwórz nowy przycisk obliczeń, aby otworzyć pod okienkiem i skonfiguruj swoje kontekstu obliczeniowego, w tym eksperymencie.

    ![Utwórz nowe zasoby obliczeniowe na potrzeby eksperymentu](media/how-to-create-portal-experiments/create-new-compute.png)

    Pole|Opis
    ---|---
    Nazwa środowiska obliczeniowego| Wprowadź unikatową nazwę, która identyfikuje Twoje kontekstu obliczeniowego.
    Rozmiar maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dotyczącymi obliczeń.
    Ustawienia dodatkowe| *Węzeł min*: Wprowadź minimalną liczbę węzłów obliczeń. Minimalna liczba węzłów obliczeniowych AML to 0. Aby włączyć profilowanie danych, musi mieć co najmniej 1 węzłów. <br> *Maksymalna liczba węzłów*: Wprowadź maksymalną liczbę węzłów obliczeń. Wartość domyślna to 6 węzły obliczeniowe AML.

      Aby rozpocząć tworzenie nowych zasobów obliczeniowych, wybierz opcję **Utwórz**. Może to potrwać kilka chwil.

      >[!NOTE]
      > Twoja nazwa obliczeniowego wskaże, czy zasoby obliczeniowe, wybierz opcję/tworzenia *włączono profilowanie*. (Patrz 7b, aby uzyskać więcej informacji na profilowaniu danych).

1. Wybierz konto magazynu danych. Publiczna wersja zapoznawcza obsługuje tylko konta usługi Azure Blob Storage i przekazanie pliku lokalnego.

1. Wybierz kontener magazynu.

1. Wybierz plik danych z kontenera magazynu, lub Przekaż plik z komputera lokalnego do kontenera.

    ![Wybierz plik danych do eksperymentu](media/how-to-create-portal-experiments/select-file.png)

1. Dodatkowo skonfigurować dane w tym eksperymencie, użyj karty podglądu i profilu.

    1. Na karcie Podgląd wskazują, jeśli dane zawierają nagłówki, a następnie wybierz pozycję Funkcje (kolumny) za pomocą szkolenia **uwzględnione** Przełącz przycisków w każdej kolumnie funkcji.

        ![Podgląd danych](media/how-to-create-portal-experiments/data-preview.png)

    1. Na karcie profil można wyświetlić [profil danych](#profile) funkcji, a także dystrybucji, typ i statystyki podsumowujące (średnia, mediana, maksymalna liczba na minutę itd.) każdego z nich.

        ![Karta Profil danych](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > Pojawi się następujący komunikat o błędzie w przypadku kontekstu obliczeniowego **nie** włączono profilowanie: *Profilowanie danych jest dostępna tylko dla celów obliczeń, które zostały już uruchomione*.

1. Wybierz typ zadania szkolenia: klasyfikacji, regresji lub prognozowania.

1. Wybieranie lokalizacji docelowej dla kolumny. Kolumna, której chcesz wykonać prognozy na.

1. Do prognozowania:
    1. Wybierz kolumnę, czas: Ta kolumna zawiera dane czasu do użycia.
    1. Wybierz horizon prognozowania: Wskazuje, ile jednostek czasu (minuty/godziny/dni/tygodni/miesięcy/lat) modelu będzie można przewidzieć przyszłości. Dodatkowo model jest wymagany do prognozowania w przyszłości, mniej dokładne stanie się. [Dowiedz się więcej na temat Prognozowanie i prognozy horizon](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment).

1. (Opcjonalnie) Ustawienia zaawansowane: dodatkowe ustawienia, można użyć do kontrolowania lepiej zadania szkolenia.

    Ustawienia zaawansowane|Opis
    ------|------
    Podstawowe metryki| Metryka główne używane do oceniania modelu. [Dowiedz się więcej o metrykach modelu](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics).
    Kryteria wyjścia| Gdy dowolne z poniższych kryteriów są spełnione, przed ukończeniem pełną kończy się zadania szkolenia. <br> *Szkolenie zadania czas (w minutach)*: Jak długo, aby zezwolić na uruchomienie zadania szkolenia.  <br> *Maksymalna liczba iteracji*: Maksymalna liczba potoków (iteracji) do testowania w ramach zadania szkolenia. Zadanie nie zostanie uruchomione więcej niż określoną liczbę iteracji. <br> *Metryki oceny próg*:  Minimalny wynik metryki dla wszystkich potoków. Daje to gwarancję, że jeśli Metryka zdefiniowanego celu, którą chcesz się połączyć, możesz nie poświęcić więcej czasu na zadania szkolenia, niż jest to konieczne.
    Przetwarzanie wstępne| Zaznacz, aby włączyć lub wyłączyć wstępnego przetwarzania wykonywane przez uczenie maszynowe automatycznych. Przetwarzanie wstępne obejmuje czyszczenia danych, przygotowywania i transformacji do generowania funkcji syntetycznych. [Dowiedz się więcej na temat przetwarzania wstępnego](#preprocess).
    Weryfikacja| Wybierz jedną z opcji krzyżowego sprawdzania poprawności do użycia w ramach zadania szkolenia. [Dowiedz się więcej o krzyżowego sprawdzania poprawności](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options).
    Współbieżność| Wybierz limity wielordzeniowych, którego chcesz używać podczas używania wielu rdzeni obliczeniowych.
    Algorytm zablokowane| Wybieranie algorytmów, które chcesz wykluczyć z zadania szkolenia.

   ![Formularz ustawień zaawansowanych](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Aby uzyskać więcej informacji na temat pól kliknij przycisk etykietka narzędzia informacji.

<a name="profile"></a>

### <a name="data-profiling"></a>Dane profilowania

Ogromna różne statystyki podsumowujące można uzyskać w twoim zestawie danych, aby sprawdzić, czy zestaw danych jest gotowa do użycia uczenia Maszynowego. Kolumny nieliczbową zawierają one tylko podstawowe statystyki, takie jak minimum, maksimum i liczba błędów. Kolumny liczbowe można także przejrzeć ich chwil statystycznych i szacowany quantiles. W szczególności naszych profil danych obejmuje:

* **Funkcja**: Nazwa kolumny, która jest podsumowywane.

* **Profil**: wizualizacji w tekście na podstawie typu wywnioskowane. Na przykład ciągi, wartości logiczne i daty mają liczby wartości podczas miejsca dziesiętne (wartości numeryczne) mają w przybliżeniu histogramów. Pozwala na szybkie zrozumieć dystrybucji danych.

* **Rozkład typu**: liczba wartości w wierszu typów w kolumnie. Wartości null są typem, więc tej wizualizacji jest przydatne do wykrywania nieparzysta lub brakujące wartości.

* **Typ**: wnioskowany typ kolumny. Możliwe wartości to: ciągi, wartości logicznych, dat i liczbę miejsc dziesiętnych.

* **Min**: minimalna wartość kolumny. Puste wpisy są wyświetlane dla funkcji, której typ nie ma właściwej kolejności (np. wartości logicznych).

* **Maksymalna liczba**: maksymalna wartość kolumny. Takich jak "min", wyświetlane są pozycje puste dla funkcji z typami nie ma znaczenia.

* **Liczba**: łączna liczba brakujących i Brak wpisów, w kolumnie.

* **Nie brakuje liczby**: liczba wpisów w kolumnie, które nie są brakujące. Należy pamiętać, że puste ciągi i błędy są traktowane jako wartości, więc nie wpływają one "licznik nie Brak."

* **Quantiles** (odstępach 0.1, 1, 5, 25, 50, 75, 95, 99 i 99,9%): przybliżonego wartości w każdym kwantyl, aby zapewnić poczucie dystrybucji danych. Puste wpisy są wyświetlane dla funkcji, korzystając z typów nie ma znaczenia.

* **Oznacza**: arytmetyczną kolumny. Puste wpisy są wyświetlane dla funkcji, korzystając z typów nie ma znaczenia.

* **Odchylenie standardowe**: odchylenie standardowe w kolumnie. Puste wpisy są wyświetlane dla funkcji, korzystając z typów nie ma znaczenia.

* **Wariancja**: odchylenie kolumny. Puste wpisy są wyświetlane dla funkcji, korzystając z typów nie ma znaczenia.

* **Skośność**: skośność kolumny. Puste wpisy są wyświetlane dla funkcji, korzystając z typów nie ma znaczenia.

* **Kurtoza**: kurtoza kolumny. Puste wpisy są wyświetlane dla funkcji, korzystając z typów nie ma znaczenia.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Zaawansowane przetwarzania wstępnego

Podczas konfigurowania eksperymentów, można włączyć ustawienie Zaawansowane `Preprocess`. Wykonanie tej tak oznacza, że następujące kroki przetwarzania wstępnego i cechowania danych są wykonywane automatycznie.

|Przetwarzanie wstępne&nbsp;kroki| Opis |
| ------------- | ------------- |
|Upuść dużej kardynalności lub żadne funkcje WARIANCJA|Usuń je z zestawów szkolenia i sprawdzania poprawności, w tym funkcje za pomocą wszystkich wartości Brak, tę samą wartość we wszystkich wierszach lub o bardzo dużej kardynalności, (na przykład, skróty, identyfikatory lub identyfikatory GUID).|
|Naliczenie brakujące wartości|W przypadku funkcji numerycznych przypisują ze średnią wartości w kolumnie.<br/><br/>W przypadku kategorii funkcji przypisują wartością najczęściej.|
|Generuj dodatkowe funkcje|Funkcje daty/godziny: Rok, miesiąc, dzień, dzień tygodnia, dnia rok, kwartał, tydzień roku, godzina, minuta, sekunda.<br/><br/>Funkcje tekstowe: Częstotliwość termin, na podstawie unigrams, bi gramy i tri znak g.|
|Przekształcanie i kodowanie |Funkcje numeryczne za pomocą niewielkiej liczbie unikatowych wartości są przekształcane w kategorii funkcji.<br/><br/>Jeden na gorąco kodowania odbywa się niski kardynalności podzielonych na kategorie; dla dużej kardynalności jeden hot-hash kodowania.|
|Osadzenia wyrazów|Featurized tekst konwertujący wektorów tokenów tekst na wektorów zdania przy użyciu wstępnie przeszkolonych modelu. Wektor osadzania każdego wyrazu w dokumencie są agregowane razem do utworzenia wektora funkcji dokumentu.|
|Docelowy kodowania|W przypadku kategorii funkcji mapuje każdej kategorii przy użyciu wartości uśrednionej docelowej problemów regresji oraz prawdopodobieństwo klasy dla każdej klasy klasyfikacji problemów. Na podstawie częstotliwości wagi i zwijania k krzyżowe sprawdzanie poprawności jest stosowana do zmniejszenia za pośrednictwem montażu mapowania i szumu spowodowane kategorii danych rozrzedzonych.|
|Kodowanie docelowe tekstu|Wprowadzania tekstu skumulowany modelu liniowego z typu "zbiór słów" jest używany do generowania prawdopodobieństwo, że każda klasa.|
|Waga dowodów (WoE)|Oblicza WoE jako środek korelacji podzielone na kategorie kolumny, aby kolumna docelowa. Zostanie ona obliczona jako dziennik stosunek prawdopodobieństwa limit klasy programu vs w swojej klasie. W tym kroku generuje jedna kolumna liczbowych w klasie i usuwa potrzebę jawnie przypisują brakujące wartości oraz traktowanie odstające.|
|Odległość klastra|Szkolenie modeli k średnich model klastrowania dla wszystkich kolumn liczbowych.  Dane wyjściowe k nowe funkcje, w jedną nową funkcję wartości liczbowych na klaster, zawierający odległość każda próbka środek strefy widocznego w każdym klastrze.|

## <a name="run-experiment-and-view-results"></a>Uruchom eksperyment, aby wyświetlić wyniki

Aby uruchomić eksperyment, kliknij przycisk Start. Eksperyment przygotowywanie proces trwa kilka minut.

### <a name="view-experiment-details"></a>Wyświetl szczegóły eksperymentu

Po zakończeniu fazy przygotowania eksperymentu, zobaczysz ekran szczegółów uruchomienia. Zapewnia pełną listę modele utworzone. Domyślnie model, który ocenia największe na podstawie Twojej parametrów znajduje się na górze listy. Zadania szkolenia spróbuje się kolejne modele, są one dodawane do listy iteracji i wykres. Użyj wykresu iteracji, aby uzyskać szybki porównanie metryki modele utworzone do tej pory.

Szkolenie zadań może wymagać trochę czasu dla każdego potoku na zakończenie działania.

![Szczegóły dotyczące uruchomienia pulpitu nawigacyjnego](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Szkolenie widok szczegółów uruchomienia

Przechodzenie do wszystkich modeli danych wyjściowych, aby wyświetlić szczegóły przebiegu, takich jak wykresy metryk i dystrybucji wydajności szkolenia. [Dowiedz się więcej na temat wykresów](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts).

![Szczegóły iteracji](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>Wdrażanie modelu

Po utworzeniu najlepszy model pod ręką, nadszedł czas na go wdrożyć jako usługę sieci web w celu przewidywania na nowych danych.

Automatyczne ML ułatwiają wdrażanie modelu bez konieczności pisania kodu:

1. Istnieje kilka opcji wdrożenia. 
    1. Jeśli chcesz wdrożyć najlepszy model na podstawie kryteriów oceniania metryki, należy ustawić w eksperymencie wybierz **wdrażanie najlepszy Model** z **szczegółów uruchomienia** strony.

        ![Wdrażanie modelu przycisku](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. Jeśli chcesz wdrożyć iteracji określonego modelu, przejść do szczegółów na podstawie modelu, aby otworzyć jego stronę szczegółów uruchomienia określonej i wybierz **wdrażanie modelu**.

        ![Wdrażanie modelu przycisku](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. Pierwszym krokiem jest zarejestrowanie w usłudze. Wybierz opcję "Register modelu" i poczekaj na ukończenie procesu rejestracji.

    ![Wdrażanie modelu bloku](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. Po zarejestrowaniu modelu będzie można pobrać skrypt oceniania (scoring.py) i skryptów środowiska (condaEnv.yml) do użycia podczas wdrażania.

1. Skrypt oceniania i skryptów środowiska są pobierane, przejdź do **zasoby** bloku, w okienku nawigacji po lewej stronie, a następnie wybierz pozycję **modeli**.

    ![Modele okienko nawigacji](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Wybierz model, który został zarejestrowany, a następnie wybierz pozycję "Utwórz obraz".

    Model może zidentyfikować na podstawie jego opis, który będzie zawierać identyfikator uruchomienia liczbę iteracji w następującym formacie: *< Run_ID > _ < Iteration_number > _modelu*

    ![Modele: Tworzenie obrazu](media/how-to-create-portal-experiments/model-create-image.png)

1. Wprowadź nazwę dla obrazu. 
1. Wybierz **Przeglądaj** przycisk obok pola "Plik oceniania", aby przekazać plik oceniania (scoring.py) został wcześniej pobrany.

1. Wybierz **Przeglądaj** przycisk obok pola "Conda plik", można przekazać pliku środowiska (condaEnv.yml), który został wcześniej pobrany.

    Można użyć własnych skrypt oceniania i plików conda, a także dodatkowe pliki do przekazania. [Dowiedz się więcej o skrypt oceniania](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#script).

      >[!Important]
      > Nazwy plików musi być w obszarze 32 znaków i musi rozpoczynać się i kończyć znaków alfanumerycznych. Może zawierać łączniki, podkreślenia, kropki i znaki alfanumeryczne, między. Spacje są niedozwolone.

    ![Tworzenie obrazu](media/how-to-create-portal-experiments/create-image.png)

1. Wybierz przycisk "Utwórz", aby rozpocząć tworzenie obrazu. To potrwa kilka minut, po zakończeniu, zostanie wyświetlony komunikat na górnym pasku.
1. Przejdź do karty "Obrazy", zaznacz pole wyboru obok obraz, który chcesz wdrożyć, a następnie wybierz pozycję "Utwórz wdrożenia". [Dowiedz się więcej na temat wdrożeń](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where).

    Istnieją 2 opcje wdrażania.
     + Wystąpienia kontenerów platformy Azure (ACI) — jest używana więcej do testowania, cel, a nie operacyjnych wdrożenia na dużą skalę. Upewnij się, że Podaj wartości dla co najmniej jednym rdzeniu na _pojemność rezerwowa procesora CPU_i co najmniej jednego gigabajta (GB) dla _pojemność rezerwowa pamięci_
     + Usługa Azure Kubernetes Service (AKS)) — ta opcja służy do wdrażania na dużą skalę. Musisz mieć gotowy obliczeniowe na podstawie usługi AKS.

     ![Obrazy: Tworzenie wdrożenia](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Po zakończeniu wybierz pozycję **Utwórz**. Wdrażanie modelu może potrwać kilka minut dla każdego potoku na zakończenie działania.

1. To wszystko! Masz usługę sieci web operational do generowania przewidywań.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat uczenia maszynowego automatycznych](concept-automated-ml.md) i Azure Machine Learning.
* [Informacje o sposobie korzystania z usługi internetowej](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).

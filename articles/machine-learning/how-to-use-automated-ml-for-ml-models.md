---
title: Tworzenie modeli & wdrażanie za pomocą automl
titleSuffix: Azure Machine Learning
description: Tworzenie, przeglądanie i wdrażanie zautomatyzowanych modeli uczenia maszynowego za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 0d6fa02578814c4c5d034be05cbc63093d70603b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257236"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Tworzenie, przeglądanie i wdrażanie zautomatyzowanych modeli uczenia maszynowego za pomocą usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym artykule dowiesz się, jak tworzyć, eksplorować i wdrażać zautomatyzowane modele uczenia maszynowego bez jednego wiersza kodu w interfejsie studio usługi Azure Machine Learning. Zautomatyzowane uczenie maszynowe to proces, w którym wybrany jest najlepszy algorytm uczenia maszynowego używany dla określonych danych. Ten proces umożliwia szybkie generowanie modeli uczenia maszynowego. [Dowiedz się więcej o zautomatyzowanym uczeniu maszynowym](concept-automated-ml.md).
 
W przykładzie end to end spróbuj [wykonać samouczek do tworzenia modelu klasyfikacji za pomocą zautomatyzowanego interfejsu ml usługi Azure Machine Learning.](tutorial-first-experiment-automated-ml.md) 

Aby uzyskać środowisko oparte na kodzie języka Python, [skonfiguruj eksperymenty automatycznego uczenia maszynowego](how-to-configure-auto-train.md) za pomocą sdk usługi Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszar roboczy usługi Azure Machine Learning z typem **wersji Enterprise**. Zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md).  Aby uaktualnić istniejący obszar roboczy do wersji Enterprise, zobacz [Uaktualnianie do wersji Enterprise](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Rozpoczęcie pracy

1. Zaloguj się do usługi https://ml.azure.comAzure Machine Learning w . 

1. Wybierz subskrypcję i obszar roboczy. 

1. Przejdź do lewego okienka. Wybierz **opcję Automatyczna ml** w sekcji **Autor.**

[![Okienko nawigacji studio usługi Azure Machine Learning](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Jeśli po raz pierwszy wykonujesz eksperymenty, zobaczysz pustą listę i linki do dokumentacji. 

W przeciwnym razie zobaczysz listę ostatnich eksperymentów automatycznego uczenia maszynowego, w tym eksperymentów utworzonych za pomocą zestawu SDK. 

## <a name="create-and-run-experiment"></a>Tworzenie i uruchamianie eksperymentu

1. Wybierz **+ Nowy automatyczny uruchom ml** i wypełnij formularz.

1. Wybierz zestaw danych z kontenera magazynu lub utwórz nowy zestaw danych. Zestawy danych można tworzyć na podstawie plików lokalnych, adresów URL sieci Web, magazynów danych lub otwartych zestawów danych platformy Azure. 

    >[!Important]
    > Wymagania dotyczące danych szkoleniowych:
    >* Dane muszą być w formie tabelarycznej.
    >* Wartość, którą chcesz przewidzieć (kolumna docelowa) musi być obecna w danych.

    1. Aby utworzyć nowy zestaw danych z pliku na komputerze lokalnym, wybierz pozycję **Przeglądaj,** a następnie wybierz plik. 

    1. Nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz **przycisk Dalej,** aby otworzyć **formularz datastore i wybór pliku**. W tym formularzu możesz wybrać miejsce przesyłania zestawu danych; domyślny kontener magazynu, który jest tworzony automatycznie z obszarem roboczym lub wybierz kontener magazynu, który ma być używany w eksperymencie. 

    1. Przejrzyj **formularz Ustawienia i podgląd,** aby uzyskać dokładność. Formularz jest inteligentnie wypełniany na podstawie typu pliku. 

        Pole| Opis
        ----|----
        Format pliku| Definiuje układ i typ danych przechowywanych w pliku.
        Ogranicznik| Jeden lub więcej znaków do określania granicy między oddzielnymi, niezależnymi regionami w postaci zwykłego tekstu lub innych strumieni danych.
        Kodowanie| Określa, jaki bit do tabeli schematu znaków do użycia do odczytu zestawu danych.
        Nagłówki kolumn| Wskazuje, jak nagłówki zestawu danych, jeśli istnieją, będą traktowane.
        Pomiń wiersze | Wskazuje, ile wierszy jest pomijanych w zestawie danych.
    
        Wybierz **pozycję Dalej**.

    1. Formularz **Schemat** jest inteligentnie wypełniany na podstawie wyborów w formularzu **Ustawienia i podgląd.** W tym miejscu skonfiguruj typ danych dla każdej kolumny, przejrzyj nazwy kolumn i wybierz kolumny, których **nie uwzględnić** w eksperymencie. 
            
        Wybierz **pozycję Dalej.**

    1. Formularz **Potwierdź szczegóły** jest podsumowaniem informacji wcześniej wypełnionych w **formularzach Podstawowe informacje** i ustawienia oraz **formularze podglądu.** Istnieje również możliwość utworzenia profilu danych dla zestawu danych przy użyciu obliczeń z włączoną profilem. Dowiedz się więcej o [profilowaniu danych](#profile).

        Wybierz **pozycję Dalej**.
1. Po wyświetleniu nowego zestawu danych wybierz nowo utworzony zestaw danych. Można również wyświetlić podgląd zestawu danych i przykładowych statystyk. 

1. W formularzu **Konfiguruj przebieg** wprowadź unikatową nazwę eksperymentu.

1. Wybierz kolumnę docelową; jest to kolumna, na której chcesz zrobić prognozy.

1. Wybierz obliczenia dla zadania profilowania danych i szkolenia. Lista istniejących obliczeń jest dostępna w rozwijanej. Aby utworzyć nowe obliczenia, postępuj zgodnie z instrukcjami w kroku 7.

1. Wybierz **pozycję Utwórz nowe obliczenia,** aby skonfigurować kontekst obliczeniowy dla tego eksperymentu.

    Pole|Opis
    ---|---
    Nazwa obliczeń| Wprowadź unikatową nazwę identyfikującą kontekst obliczeniowy.
    Rozmiar maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczeń.
    Min / Max węzłów (w ustawieniach zaawansowanych)| Aby dane profilu, należy określić 1 lub więcej węzłów. Wprowadź maksymalną liczbę węzłów dla obliczeń. Wartość domyślna to 6 węzłów dla obliczeń AML.
    
    Wybierz **pozycję Utwórz**. Utworzenie nowego obliczeń może potrwać kilka minut.

    >[!NOTE]
    > Nazwa obliczeń wskazuje, czy wybrane/utworzone obliczenie jest *włączone profilowanie*. (Więcej informacji można znaleźć w [sekcji profilowania danych).](#profile)

    Wybierz **pozycję Dalej**.

1. W formularzu **Typ zadania i ustawienia** wybierz typ zadania: klasyfikacja, regresja lub prognozowanie. Zobacz, [jak zdefiniować typy zadań, aby](how-to-define-task-type.md) uzyskać więcej informacji.

    1. Dla klasyfikacji można również włączyć uczenia głębokiego, który jest używany do featurizations tekstu.

    1. Do prognozowania:
        1. Wybierz kolumnę czasu: Ta kolumna zawiera dane czasu, które mają być używane.

        1. Wybierz horyzont prognozy: Wskaż, ile jednostek czasu (minuty/godziny/dni/tygodnie/miesiące/lata) będzie w stanie przewidzieć na przyszłość. Im dalej model jest wymagany do przewidywania w przyszłości, tym mniej dokładne stanie się. [Dowiedz się więcej o prognozowaniu i prognozowaniu horyzontu](how-to-auto-train-forecast.md).

1. (Opcjonalnie) Wyświetl ustawienia konfiguracji dodawania: dodatkowe ustawienia, których można użyć, aby lepiej kontrolować zadanie szkoleniowe. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych. 

    Dodatkowe konfiguracje|Opis
    ------|------
    Metryka podstawowa| Główna metryka używana do oceniania modelu. [Dowiedz się więcej o metrykach modelu](how-to-configure-auto-train.md#explore-model-metrics).
    Automatyczne featurization| Wybierz, aby włączyć lub wyłączyć przetwarzanie wstępne wykonywane przez zautomatyzowane uczenie maszynowe. Przetwarzanie wstępne obejmuje automatyczne oczyszczanie danych, przygotowywanie i przekształcanie w celu generowania funkcji syntetycznych. Nie jest obsługiwana dla typu zadania prognozowania szeregów czasowych. [Dowiedz się więcej o przetwarzaniem wstępnym](#featurization). 
    Wyjaśnij najlepszy model | Wybierz, aby włączyć lub wyłączyć, aby pokazać wytłumaczenia zalecanego najlepszego modelu
    Zablokowany algorytm| Wybierz algorytmy, które chcesz wykluczyć z zadania szkoleniowego.
    Kryterium wyjścia| Po spełnieniu któregokolwiek z tych kryteriów zadanie szkoleniowe zostaje zatrzymane. <br> *Czas pracy szkoleniowej (godziny)*: Jak długo można zezwolić na wykonywanie zadania szkoleniowego. <br> *Próg wyniku metryki:* minimalny wynik metryki dla wszystkich potoków. Gwarantuje to, że jeśli masz zdefiniowaną metrykę docelową, którą chcesz osiągnąć, nie spędzasz więcej czasu na pracy szkoleniowej niż jest to konieczne.
    Walidacja| Wybierz jedną z opcji sprawdzania poprawności krzyżowej do użycia w zadaniu szkoleniowym. [Dowiedz się więcej o weryfikacji krzyżowej](how-to-configure-auto-train.md).
    Współbieżność| *Maksymalna liczba równoczesnych iteracji:* Maksymalna liczba potoków (iteracji) do przetestowania w zadaniu szkoleniowym. Zadanie nie będzie działać więcej niż określona liczba iteracji.

1. (Opcjonalnie) Wyświetl ustawienia featurization: jeśli wybierzesz opcję **Automatyczna featurization** w formularzu **Dodatkowe ustawienia konfiguracji,** ten formularz jest, gdzie można określić, które kolumny do wykonania tych featurizations na i wybrać, które wartości statystycznej do użycia dla brakujących wartości imputacji.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Statystyki podsumowania profilowania danych &

Możesz uzyskać szeroką gamę statystyk podsumowania w zestawie danych, aby sprawdzić, czy zestaw danych jest gotowy do użycia w zestawie. W przypadku kolumn nielicznych zawierają one tylko podstawowe statystyki, takie jak min, max i liczba błędów. W przypadku kolumn liczbowych można również przeglądać ich statystyczne momenty i szacowane kwantyle. W szczególności nasz profil danych obejmuje:

>[!NOTE]
> Puste wpisy są wyświetlane dla operacji z typami nieistotnymi.

Statystyki|Opis
------|------
Funkcja| Nazwa kolumny, która jest sumowana.
Profil| Wizualizacja w wierszu na podstawie wywnioskowanego typu. Na przykład ciągi, wartości logiczne i daty będą miały liczbę wartości, podczas gdy liczby dziesiętne (numeryczne) mają przybliżone histogramy. Pozwala to na szybkie zrozumienie dystrybucji danych.
Dystrybucja typu| Liczba wartości w wierszu typów w kolumnie. Wartości null są ich własnym typem, więc ta wizualizacja jest przydatna do wykrywania wartości nieparzystych lub brakujących.
Typ|Wywnioskować typ kolumny. Możliwe wartości to: ciągi, wartości logiczne, daty i miejsca dziesiętne.
Min.| Minimalna wartość kolumny. Puste wpisy są wyświetlane dla obiektów, których typ nie ma właściwej kolejności (np. wartości logiczne).
Maks.| Maksymalna wartość kolumny. 
Liczba| Całkowita liczba brakujących i niewykończone wpisy w kolumnie.
Liczba niebrakujących| Liczba wpisów w kolumnie, których nie brakuje. Puste ciągi i błędy są traktowane jako wartości, więc nie przyczynią się do "nie brakuje liczby".
Kwantyle| Przybliżone wartości w każdym quantile, aby zapewnić poczucie rozkładu danych.
Średnia| Średnia arytmetyczna lub średnia kolumny.
Odchylenie standardowe| Zmierzyć ilość dyspersji lub zmienności danych tej kolumny.
Wariancja| Miara tego, jak daleko rozmieszcza się dane tej kolumny jest od jej średniej wartości. 
Skośność| Zmierz, jak różne są dane tej kolumny od rozkładu normalnego.
Kurtoza| Miara tego, jak mocno ogołoconych danych tej kolumny jest porównywana z rozkładem normalnym.

<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Zaawansowane opcje featurization

Zautomatyzowane uczenie maszynowe oferuje automatyczne przetwarzanie wstępne i barierki danych, aby ułatwić identyfikację potencjalnych problemów z danymi i zarządzanie nimi. 

### <a name="preprocessing"></a>Wstępnego przetwarzania

> [!NOTE]
> Jeśli planujesz wyeksportować modele utworzone automatycznie ml do [modelu ONNX,](concept-onnx.md)tylko opcje featurization wskazane w * są obsługiwane w formacie ONNX. Dowiedz się więcej o [konwersji modeli na ONNX](concept-automated-ml.md#use-with-onnx). 

|Kroki przetwarzania wstępnego&nbsp;| Opis |
| ------------- | ------------- |
|Upuść funkcje o wysokiej kardynalności lub bez wariancji* |Upuść je z zestawów szkoleniowych i sprawdzania poprawności, w tym funkcji z brakującymi wartościami, tą samą wartością we wszystkich wierszach lub z bardzo wysoką kardynalnością (na przykład skrótami, identyfikatorami lub identyfikatorami GUID).|
|Przypisywanie brakujących wartości* |W przypadku operacji liczbowych należy przypisać średnią wartości w kolumnie.<br/><br/>W przypadku cech kategorii przypisywaj najczęściej spotykaną wartość.|
|Generowanie dodatkowych funkcji* |Dla datetime funkcje: rok, miesiąc, dzień, dzień tygodnia, dzień w roku, kwartał, tydzień roku, godzina, minuta, drugi.<br/><br/>W przypadku funkcji tekstu: Częstotliwość terminów oparta na jednogramach, bi-gramach i trójznakowych gramach.|
|Przekształcanie i kodowanie *|Operacje numeryczne z kilkoma unikatowymi wartościami są przekształcane w funkcje kategoryczne.<br/><br/>Kodowanie na gorąco jest wykonywane dla klasyki niskiej kardynalności; dla wysokiej kardynalności, kodowanie jeden skrót.|
|Osadzanie wyrazów|Tekst featurizer, który konwertuje wektory tokenów tekstowych na wektory zdań przy użyciu wstępnie przeszkolonego modelu. Wektor osadzania każdego wyrazu w dokumencie jest agregowany razem w celu uzyskania wektora funkcji dokumentu.|
|Kodowania docelowe|W przypadku obiektów kategorii mapuje każdą kategorię ze uśrednianą wartością docelową problemów z regresją oraz prawdopodobieństwem klasy dla każdej klasy w przypadku problemów z klasyfikacją. Wagi oparte na częstotliwościach i walidacja krzyżowa k-fold są stosowane w celu zmniejszenia nadmiernego dopasowania mapowania i hałasu spowodowanego przez rzadkie kategorie danych.|
|Kodowanie docelowe tekstu|W przypadku wprowadzania tekstu skumulowany model liniowy z workiem słów jest używany do generowania prawdopodobieństwa każdej klasy.|
|Waga dowodów (BioE)|Oblicza nieszczęście jako miarę korelacji kolumn kategorii z kolumną docelową. Jest obliczana jako dziennik współczynnika w klasie vs pozaklasowych prawdopodobieństw. Ten krok wyprowadza jedną kolumnę funkcji numerycznej na klasę i eliminuje konieczność jawnego przypisywania brakujących wartości i leczenia odstający.|
|Odległość klastra|Trenuje k-oznacza klastrowania modelu na wszystkich kolumnach numerycznych.  Wyjścia k nowe funkcje, jedna nowa funkcja numeryczna na klaster, zawierająca odległość każdej próbki do centroidu każdego klastra.|

### <a name="data-guardrails"></a>Barierki ochronne danych

Barierki danych są stosowane, gdy automatyczne featurization jest włączona lub sprawdzanie poprawności jest ustawiona na auto. Barierki danych pomagają identyfikować potencjalne problemy z danymi (np. brakujące wartości, nierównowaga klas) i pomagają w podejmowaniu działań naprawczych w celu poprawy wyników. 

Użytkownicy mogą przeglądać barierki danych w studio w zakładce **Barierki** ```show_output=True``` danych automatycznego uruchamiania ml lub ustawiając podczas przesyłania eksperymentu przy użyciu zestawu SDK języka Python. 

#### <a name="data-guardrail-states"></a>Stany poręczy danych

Barierki danych będą wyświetlać jeden z trzech stanów: **Passed**, **Done**lub **Alerted**.

Stan| Opis
----|----
Przekazywane| Nie wykryto żadnych problemów z danymi i nie jest wymagana żadna akcja użytkownika. 
Gotowe| Zmiany zostały zastosowane do danych. Zachęcamy użytkowników do zapoznania się z działaniami naprawczymi podjętymi przez zautomatyzowane ml, aby upewnić się, że zmiany są zgodne z oczekiwanymi wynikami. 
Pogotowiu| Wykryto problem z danymi, których nie można było rozwiązać. Zachęcamy użytkowników do zmiany i rozwiązania problemu. 

>[!NOTE]
> Poprzednie wersje zautomatyzowanych eksperymentów ml wyświetlały czwarty stan: **Naprawiono**. Nowsze eksperymenty nie będą wyświetlać tego stanu, a wszystkie barierki, które wyświetlały stan **Stały,** będą teraz wyświetlane **gotowe**.   

W poniższej tabeli opisano barierki danych obecnie obsługiwane i skojarzone stany, które użytkownicy mogą natknąć podczas przesyłania eksperymentu.

Poręczy|Stan|Warunek&nbsp;&nbsp;wyzwalacza
---|---|---
Brakujące wartości operacji przypisanie |**Przekazywane** <br><br><br> **Gotowe**| W danych szkoleniowych nie wykryto brakujących wartości funkcji. Dowiedz się więcej o [brakuującym imputacji wartości.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Brakujące wartości funkcji zostały wykryte w danych szkoleniowych i przypisane.
Obsługa funkcji o wysokiej kardynalności |**Przekazywane** <br><br><br> **Gotowe**| Dane wejściowe zostały przeanalizowane i nie wykryto żadnych funkcji wysokiej kardynalności. Dowiedz się więcej o [wykrywaniu funkcji wysokiej kardynalności.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Funkcje wysokiej kardynalności zostały wykryte w danych wejściowych i zostały obsłużone.
Obsługa podziału sprawdzania poprawności |**Gotowe**| *Konfiguracja sprawdzania poprawności została ustawiona na "auto", a dane szkoleniowe zawierały **mniej** niż 20 000 wierszy.* <br> Każda iteracja uczonego modelu została zweryfikowana za pomocą krzyżowego sprawdzania poprawności. Dowiedz się więcej o [danych sprawdzania poprawności.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> *Konfiguracja sprawdzania poprawności została ustawiona na "auto", a dane szkoleniowe zawierały **ponad** 20 000 wierszy.* <br> Dane wejściowe zostały podzielone na zestaw danych szkoleniowych i zestaw danych sprawdzania poprawności do sprawdzania poprawności modelu.
Wykrywanie równoważenia klas |**Przekazywane** <br><br><br><br> **Pogotowiu** | Dane wejściowe zostały przeanalizowane, a wszystkie klasy są zrównoważone w danych szkoleniowych. Zestaw danych jest uważany za zrównoważony, jeśli każda klasa ma dobrą reprezentację w zestawie danych, mierzoną liczbą i współczynnikiem próbek. <br><br><br> Niezrównoważone klasy zostały wykryte w danych wejściowych. Aby naprawić błąd modelu, rozwiąż problem równoważenia. Dowiedz się więcej o [nieeksołkowych danych.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
Wykrywanie problemów z pamięcią |**Przekazywane** <br><br><br><br> **Gotowe** |<br> Przeanalizowano wybraną wartość {horyzont, opóźnienie, okno toczenia} i nie wykryto żadnych potencjalnych problemów poza pamięcią. Dowiedz się więcej o [konfiguracjach prognozowania szeregów czasowych.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) <br><br><br>Wybrane wartości {horyzont, opóźnienie, okno toczenia} zostały przeanalizowane i potencjalnie spowoduje, że eksperyment zabraknie pamięci. Konfiguracje opóźnień lub okien toczących się zostały wyłączone.
Wykrywanie częstotliwości |**Przekazywane** <br><br><br><br> **Gotowe** |<br> Seria czasowa została przeanalizowana, a wszystkie punkty danych są wyrównane z wykrytą częstotliwością. <br> <br> Przeanalizowano szeregi czasowe i wykryto punkty danych, które nie są zgodne z wykrytą częstotliwością. Te punkty danych zostały usunięte z zestawu danych. Dowiedz się więcej o [przygotowaniu danych do prognozowania szeregów czasowych.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="run-experiment-and-view-results"></a>Uruchamianie eksperymentu i wyświetlanie wyników

Wybierz **zakończ,** aby uruchomić eksperyment. Proces przygotowywania eksperymentu może potrwać do 10 minut. Zadania trenowania mogą zająć kolejne 2–3 minuty dla każdego potoku.

### <a name="view-experiment-details"></a>Wyświetlanie szczegółów eksperymentu

Zostanie otwarty ekran **Uruchom szczegóły** na karcie **Szczegóły.** Na tym ekranie przedstawiono podsumowanie przebiegu eksperymentu, w tym pasek stanu u góry obok numeru uruchomienia. 

Karta **Modele** zawiera listę utworzonych modeli uporządkowaną według wyników metryk. Domyślnie model, który dla wybranej metryki uzyska najlepszy wynik, znajduje się na początku listy. W miarę wypróbowywania przez zadanie trenowania kolejnych modeli są one dodawane do listy. Umożliwia ona szybkie porównanie metryk modeli wyprodukowanych do tej pory.

[![Uruchom pulpit nawigacyjny szczegółów](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Wyświetlanie szczegółów przebiegu szkolenia

Przechodzenie do szczegółów dowolnego z ukończonych modeli, aby wyświetlić szczegóły przebiegu szkolenia, takie jak metryki uruchamiania na karcie **Szczegóły modelu** lub wykresy wydajności na karcie **Wizualizacje.** [Dowiedz się więcej o wykresach](how-to-understand-automated-ml.md).

[![Szczegóły iteracji](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Wdrażanie modelu

Gdy masz najlepszy model pod ręką, nadszedł czas, aby wdrożyć go jako usługę sieci web, aby przewidzieć na nowe dane.

Automatyczne ml pomaga we wdrażaniu modelu bez pisania kodu:

1. Masz kilka opcji wdrożenia. 

    + Opcja 1: Aby wdrożyć najlepszy model (zgodnie z zdefiniowanymi kryteriami metryki), wybierz przycisk **Wdrażanie najlepszego modelu** na karcie **Szczegóły.**

    + Opcja 2: Aby wdrożyć określoną iterację modelu z tego eksperymentu, przejdź do szczegółów modelu, aby otworzyć kartę **Szczegóły modelu** i wybierz **pozycję Wdrażanie modelu**.

1. Wypełnij okienko **modelu wdrażania.**

    Pole| Wartość
    ----|----
    Nazwa| Wprowadź unikatową nazwę wdrożenia.
    Opis| Wprowadź opis, aby lepiej określić, do czego służy to wdrożenie.
    Typ obliczeń| Wybierz typ punktu końcowego, który chcesz wdrożyć: *Usługa Azure Kubernetes Service (AKS)* lub *Wystąpienie kontenera platformy Azure (ACI).*
    Nazwa obliczeń| *Dotyczy tylko AKS:* Wybierz nazwę klastra AKS, który chcesz wdrożyć.
    Włącz uwierzytelnianie | Wybierz, aby zezwolić na uwierzytelnianie oparte na tokenach lub kluczach.
    Korzystanie z niestandardowych zasobów wdrażania| Włącz tę funkcję, jeśli chcesz przekazać własny skrypt oceniania i plik środowiska. [Dowiedz się więcej o ocenianiu skryptów](how-to-deploy-and-where.md#script).

    >[!Important]
    > Nazwy plików muszą mieć mniej niż 32 znaki i muszą zaczynać się i kończyć alfanumeryką. Może zawierać kreski, podkreślenia, kropki i alfanumeryczne między nimi. Spacje nie są dozwolone.

    Menu *Zaawansowane* oferuje domyślne funkcje wdrażania, takie jak zbieranie [danych](how-to-enable-app-insights.md) i ustawienia wykorzystania zasobów. Jeśli chcesz zastąpić te wartości domyślne, zrób to w tym menu.

1. Wybierz pozycję **Wdróż**. Wdrożenie może potrwać około 20 minut.

Teraz masz operacyjną usługę internetową do generowania prognoz! Prognoz można przetestować, odpytując usługę z [wbudowanej usługi Azure Machine Learning.](how-to-consume-web-service.md#consume-the-service-from-power-bi)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak korzystać z usługi sieci Web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Poznaj wyniki automatycznego uczenia maszynowego](how-to-understand-automated-ml.md).
* [Dowiedz się więcej o zautomatyzowanym uczeniu maszynowym](concept-automated-ml.md) i usłudze Azure Machine Learning.

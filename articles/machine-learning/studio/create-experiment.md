---
title: Prosty eksperyment w usłudze Machine Learning Studio | Microsoft Docs
description: Ten samouczek uczenia maszynowego przeprowadzi Cię przez łatwy eksperyment dotyczący przetwarzania danych. Będziemy prognozować cenę samochodu, używając algorytmu regresji.
keywords: experiment,linear regression,machine learning algorithms,machine learning tutorial,predictive modeling techniques,data science experiment
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2017
ms.openlocfilehash: 7ee1df8c38ac2dbfc6618febd223d5c4bbf32be6
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425714"
---
# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Samouczek dotyczący uczenia maszynowego: tworzenie pierwszego eksperymentu związanego z przetwarzaniem danych w usłudze Azure Machine Learning Studio

Ten samouczek jest przeznaczony dla osób, którym nie zdarzyło się korzystać z usługi **Azure Machine Learning Studio**.

Ten samouczek zawiera opis kroków tworzenia eksperymentu uczenia maszynowego po raz pierwszy przy użyciu usługi Studio. Eksperyment ten polega na przetestowaniu modelu analitycznego, który prognozuje cenę samochodów na podstawie różnych zmiennych, takich jak marka i specyfikacja techniczna.

> [!NOTE]
> Podstawowe informacje przedstawione w tym samouczku obejmują przeciąganie modułów i upuszczanie ich w obszarze eksperymentu, łączenie modułów ze sobą, uruchamianie eksperymentu oraz przeglądanie wyników. Samouczek nie zawiera ogólnego omówienia uczenia maszynowego ani instrukcji dotyczących wybierania oraz używania ponad 100 wbudowanych algorytmów i modułów manipulowania danymi dostępnych w usłudze Studio.
>
>Jeśli nie zdarzyło Ci się korzystać z uczenia maszynowego, najlepiej rozpocząć od serii filmów wideo [Przetwarzanie danych dla początkujących](data-science-for-beginners-the-5-questions-data-science-answers.md). Seria ta stanowi znakomite wprowadzenie do uczenia maszynowego, przedstawione przy użyciu potocznego języka i codziennych pojęć.
>
>Jeśli znasz uczenie maszynowe, ale szukasz bardziej ogólnych informacji o usłudze Machine Learning Studio i udostępnianych przez nią algorytmach, oto kilka przydatnych zasobów:
>
- [Co to jest Machine Learning Studio?](what-is-ml-studio.md) — ogólne omówienie usługi Studio.
- [Podstawy uczenia maszynowego z przykładowymi algorytmami](basics-infographic-with-algorithm-examples.md) — ta grafika informacyjna ułatwia uzyskanie dodatkowych informacji o różnych typach algorytmów uczenia maszynowego dostępnych w usłudze Machine Learning Studio.
- [Przewodnik po usłudze Machine Learning](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1) — ten przewodnik zawiera podobne informacje co powyższa grafika informacyjna, ale przedstawione w interakcyjnym formacie.
- [Ściągawka dotycząca algorytmów uczenia maszynowego](algorithm-cheat-sheet.md) i [Jak wybierać algorytmy w usłudze Microsoft Azure Machine Learning](algorithm-choice.md) — te dostępne do pobrania materiały, obejmujące plakat i artykuł, zawierają szczegółowe omówienie algorytmów usługi Studio.
- [Usługa Machine Learning Studio: pomoc dotycząca algorytmów i modułów](https://msdn.microsoft.com/library/azure/dn905974.aspx) — pełna dokumentacja wszystkich modułów usługi Studio, w tym algorytmów uczenia maszynowego.



## <a name="how-does-machine-learning-studio-help"></a>W czym pomaga usługa Machine Learning Studio?

Usługa Machine Learning Studio ułatwia skonfigurowanie eksperymentu za pomocą modułów typu „przeciągnij i upuść” zaprogramowanych wstępnie metodami modelowania predykcyjnego.

Przy użyciu wizualnego interfejsu można przeciągać ***zestawy danych*** oraz ***moduły*** analiz i upuszczać je na interakcyjny obszar roboczy. Łącząc te elementy, można utworzyć ***eksperyment***, a następnie uruchomić go przy użyciu usługi Machine Learning Studio.
Sekwencja działań obejmuje ***tworzenie modelu***, ***uczenie modelu***, ***generowanie wyników i testowanie modelu***.

Można wykonywać iteracje projektu modelu, wielokrotnie edytując i uruchamiając eksperyment, do momentu uzyskania oczekiwanych wyników. Gotowy model można opublikować jako ***usługę sieci Web***. Pozwoli to innym osobom uzyskiwać prognozy na podstawie nowych danych.

## <a name="open-machine-learning-studio"></a>Otwieranie usługi Machine Learning Studio

Aby rozpocząć pracę z usługą Studio, przejdź do [https://studio.azureml.net](https://studio.azureml.net). Jeśli logujesz się w usłudze Machine Learning Studio nie po raz pierwszy, kliknij pozycję **Sign in** (Zaloguj się). W przeciwnym razie kliknij pozycję **Sign up here** (Zarejestruj się) i wybierz opcję darmową lub płatną.

![Logowanie do usługi Machine Learning Studio][sign-in-to-studio]
<br/>
***Logowanie do usługi Machine Learning Studio***

## <a name="five-steps-to-create-an-experiment"></a>Tworzenie eksperymentu w pięciu krokach

W tym samouczku dotyczącym uczenia maszynowego wykonamy pięć podstawowych kroków, aby uruchomić eksperyment w usłudze Machine Learning Studio obejmujący tworzenie i uczenie modelu oraz generowanie wyników:

- **Tworzenie modelu**
    - [Krok 1. Pobieranie danych]
    - [Krok 2. Przygotowanie danych]
    - [Krok 3. Definiowanie funkcji]
- **Uczenie modelu**
    - [Krok 4. Wybieranie i stosowanie algorytmu uczenia]
- **Generowanie wyników i testowanie modelu**
    - [Krok 5. Przewidywanie nowych cen samochodów]

[Krok 1. Pobieranie danych]: #step-1-get-data
[Krok 2. Przygotowanie danych]: #step-2-prepare-the-data
[Krok 3. Definiowanie funkcji]: #step-3-define-features
[Krok 4. Wybieranie i stosowanie algorytmu uczenia]: #step-4-choose-and-apply-a-learning-algorithm
[Krok 5. Przewidywanie nowych cen samochodów]: #step-5-predict-new-automobile-prices

> [!TIP] 
> Funkcjonalną kopię poniższego eksperymentu można znaleźć w [galerii sztucznej inteligencji platformy Azure](https://gallery.cortanaintelligence.com). Otwórz stronę **[Your first data science experiment - Automobile price prediction](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** (Pierwszy eksperyment z przetwarzaniem danych — prognozowanie cen samochodów) i kliknij przycisk **Open in Studio** (Otwórz w usłudze Studio), aby pobrać kopię eksperymentu do obszaru roboczego usługi Machine Learning Studio.


## <a name="step-1-get-data"></a>Krok 1. Pobieranie danych

Do przeprowadzenia uczenia maszynowego potrzebne są dane.
Usługa Machine Learning Studio udostępnia wiele przykładowych zestawów danych do wyboru. Dane można również importować z wielu źródeł. W tym scenariuszu będziemy używać przykładowego zestawu danych **Automobile price data (Raw)** (Nieprzetworzone dane z cenami samochodów), dołączonego do obszaru roboczego.
Zestaw ten zawiera dane różnych modeli samochodów, na przykład informacje dotyczące marki, ceny czy specyfikacji technicznej.

Poniżej przedstawiono procedurę dołączania zestawu danych do eksperymentu.

1. Utwórz nowy eksperyment, klikając pozycję **+NEW** (+NOWY) u dołu okna Machine Learning Studio i wybierz kolejno pozycje **EXPERIMENT** (EKSPERYMENT), **Blank Experiment** (Pusty eksperyment).

2. Eksperymentowi zostanie nadana domyślna nazwa, wyświetlana w górnej części obszaru roboczego. Zaznacz ten tekst i wpisz opisową nazwę, na przykład **Prognozowanie cen samochodów**. Nazwa nie musi być unikatowa.

    ![Zmienianie nazwy eksperymentu][rename-experiment]

2. Z lewej strony obszaru roboczego eksperymentu znajduje się paleta zawierająca zestawy danych i moduły. Wpisz **automobile** (samochód) w polu wyszukiwania w górnej części tej palety, aby znaleźć zestaw danych z etykietą **Automobile price data (Raw)** (Nieprzetworzone dane z cenami samochodów). Przeciągnij ten zestaw danych do obszaru roboczego eksperymentu.

    ![Znajdowanie zestawu danych dotyczących samochodów i przeciąganie go do obszaru roboczego eksperymentu][type-automobile]
    <br/>
    ***Znajdowanie zestawu danych dotyczących samochodów i przeciąganie go do obszaru roboczego eksperymentu***

Aby wyświetlić graficzną reprezentację danych, kliknij port wyjściowy w dolnej części zestawu danych dotyczących samochodów, a następnie wybierz pozycję **Visualize** (Wizualizacja).

![Klikanie portu wyjściowego i wybieranie polecenia „Visualize” (Wizualizacja)][select-visualize]
<br/>
***Klikanie portu wyjściowego i wybieranie polecenia „Visualize” (Wizualizacja)***

> [!TIP]
> Zestawy danych i moduły mają porty wejściowe i wyjściowe oznaczone małymi kołami — porty wejściowe znajdują się u góry, a wyjściowe u dołu.
Aby utworzyć przepływ danych w eksperymencie, połącz port wyjściowy danego modułu z portem wejściowym innego modułu.
W dowolnym momencie można kliknąć port wyjściowy zestawu danych lub modułu, aby wyświetlić dane w określonym punkcie przepływu danych.

W tym przykładowym zestawie danych poszczególne wystąpienia modeli samochodów są wyświetlane w postaci wierszy, a zmienne skojarzone z samochodami są wyświetlane jako kolumny. Na podstawie zmiennych dotyczących konkretnego samochodu spróbujemy przewidzieć cenę w skrajnej prawej kolumnie (kolumna 26 o nazwie „price” [cena]).

![Wyświetlanie danych samochodów w oknie wizualizacji danych][visualize-auto-data]
<br/>
***Wyświetlanie danych samochodów w oknie wizualizacji danych***

Aby zamknąć okno wizualizacji, kliknij znak „**x**” w prawym górnym rogu.

## <a name="step-2-prepare-the-data"></a>Krok 2. Przygotowanie danych

Zestawy danych zwykle wymagają przetworzenia wstępnego przed rozpoczęciem analizy. Na przykład może okazać się, że w przypadku niektórych wierszy w kolumnach brakuje wartości. Te brakujące wartości muszą zostać wyczyszczone, aby umożliwić modelowi wykonanie poprawnej analizy danych. W naszym przykładzie usuniemy wszystkie wiersze z brakującymi wartościami. Ponadto w kolumnie **normalized-losses** (znormalizowane straty) występuje wiele przypadków brakujących wartości, dlatego całkowicie wykluczymy tę kolumnę z modelu.

> [!TIP]
> Większość modułów wymaga wyczyszczenia brakujących wartości w danych wejściowych.

Najpierw dodamy moduł, który całkowicie usunie kolumnę **normalized-losses** (znormalizowane straty), a następnie inny moduł, który usunie wszystkie wiersze z brakującymi danymi.

1. Wpisz ciąg **select columns** (wybieranie kolumn) w polu wyszukiwania w górnej części palety modułów, aby znaleźć moduł [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych), a następnie przeciągnij go do obszaru roboczego eksperymentu. Ten moduł pozwala wybierać kolumny danych, które mają zostać dołączone do modelu lub wykluczone z niego.

2. Połącz port wyjściowy zestawu danych **Automobile price data (Raw)** (Nieprzetworzone dane z cenami samochodów) z portem wejściowym modułu [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych).

    ![Dodawanie modułu „Select Columns in Dataset” (Wybieranie kolumn w zestawie danych) do obszaru roboczego eksperymentu i tworzenie połączenia][type-select-columns]
    <br/>
    ***Dodawanie modułu „Select Columns in Dataset” (Wybieranie kolumn w zestawie danych) do obszaru roboczego eksperymentu i tworzenie połączenia***

3. Kliknij moduł [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych) i kliknij pozycję **Launch column selector** (Uruchom selektora kolumn) w okienku **Properties** (Właściwości).

    - Po lewej stronie kliknij pozycję **With rules** (Za pomocą reguł).
    - W obszarze **Begin With** (Rozpocznij od) kliknij pozycję **All columns** (Wszystkie kolumny). Spowoduje to przetworzenie wszystkich kolumn (z wyjątkiem tych wykluczonych) przez moduł [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych).
    - Z list rozwijanych wybierz pozycje **Exclude** (Wyklucz) i **column names** (nazwy kolumn), a następnie kliknij wewnątrz pola tekstowego. Zostanie wyświetlona lista kolumn. Wybierz pozycję **normalized-losses** (znormalizowane straty), aby dodać ją do pola tekstowego.
    - Kliknij przycisk znacznika wyboru (OK), aby zamknąć selektora kolumn (w prawym dolnym rogu).

    ![Uruchamianie selektora kolumn i wykluczanie kolumny „normalized-losses” (znormalizowane straty)][launch-column-selector]
    <br/>
    ***Uruchamianie selektora kolumn i wykluczanie kolumny „normalized-losses” (znormalizowane straty)***

    Zawartość okienka właściwości modułu **Select Columns in Dataset** (Wybieranie kolumn w zestawie danych) określa, że zostaną przetworzone wszystkie kolumny zestawu danych z wyjątkiem kolumny **normalized-losses** (znormalizowane straty).

    ![Zawartość okienka właściwości wskazuje na wykluczenie kolumny „normalized-losses” (znormalizowane straty)][showing-excluded-column]
    <br/>
    ***Zawartość okienka właściwości wskazuje na wykluczenie kolumny „normalized-losses” (znormalizowane straty)***

    > [!TIP]
    Aby dodać komentarz do modułu, kliknij dwukrotnie moduł i wpisz tekst. Pozwoli to od razu sprawdzić rolę modułu w eksperymencie. W tym przypadku kliknij dwukrotnie moduł [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych) i dodaj komentarz „Wykluczenie kolumny znormalizowanych strat”.

    ![Dwukrotne kliknięcie modułu umożliwia dodanie komentarza][add-comment]
    <br/>
    ***Dwukrotne kliknięcie modułu umożliwia dodanie komentarza***

3. Przeciągnij moduł [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych) do obszaru roboczego eksperymentu i połącz go z modułem [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych). W okienku **Properties** (Właściwości) w obszarze **Cleaning mode** (Tryb czyszczenia) wybierz pozycję **Remove entire row** (Usuń cały wiersz). Spowoduje to wyczyszczenie danych przez moduł [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych) — zostaną usunięte wiersze, w których brakuje wartości. Kliknij dwukrotnie moduł i wpisz komentarz „Usunięcie wierszy z brakującymi wartościami”.

    ![Ustawianie trybu czyszczenia na wartość „Remove entire row” (Usuń cały wiersz) w module „Clean Missing Data” (Czyszczenie brakujących danych)][set-remove-entire-row]
    <br/>
    ***Ustawianie trybu czyszczenia na wartość „Remove entire row” (Usuń cały wiersz) w module „Clean Missing Data” (Czyszczenie brakujących danych)***

4. Uruchom eksperyment, klikając pozycję **URUCHOM** u dołu strony.

    Po zakończeniu eksperymentu na wszystkich modułach są widoczne zielone znaczniki wyboru. Oznacza to, że działanie modułów zakończyło się pomyślnie. Zwróć również uwagę na informację o **zakończeniu działania** wyświetlaną w prawym górnym rogu.

![Gdy działanie eksperymentu zakończy się, powinien on wyglądać mniej więcej tak][early-experiment-run]
<br/>
***Gdy działanie eksperymentu zakończy się, powinien on wyglądać mniej więcej tak***

> [!TIP]
> Dlaczego teraz uruchomiliśmy eksperyment? Uruchomienie eksperymentu spowodowało przekazanie definicji kolumn danych z zestawu danych do modułów [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych) i [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych). Oznacza to, że informacje te będą dostępne dla wszystkich modułów połączonych z modułem [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych).

Na tym etapie nasz eksperyment obejmuje tylko czyszczenie danych. Jeśli chcesz wyświetlić oczyszczony zestaw danych, kliknij lewy port wyjściowy modułu [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych) i wybierz pozycję **Visualize** (Wizualizacja). Zwróć uwagę na to, że kolumna **normalized-losses** (znormalizowane straty) nie jest już uwzględniana i nie ma brakujących wartości.

Po oczyszczeniu danych można określić, jakie cechy zostaną użyte w modelu predykcyjnym.

## <a name="step-3-define-features"></a>Krok 3. Definiowanie cech

W uczeniu maszynowym *cechy* to poszczególne mierzalne właściwości określonych informacji. W naszym zestawie danych poszczególne wiersze odpowiadają różnym samochodom, a kolumny — cechom tych samochodów.

Znalezienie odpowiedniego zestawu cech, który ma służyć do utworzenia modelu predykcyjnego, wymaga eksperymentowania oraz dysponowania wiedzą na temat bieżącego problemu. Pewne cechy lepiej nadają się do prognozowania danych docelowych. Ponadto niektóre cechy są ściśle powiązane z innymi, dlatego można je usunąć. Na przykład dane dotyczące zużycia paliwa w mieście i w trasie mają bliski związek ze sobą, co sprawia, że usunięcie jednej z tych cech nie będzie miało zasadniczego wpływu na prognozę.

Utworzymy model, który korzysta z podzbioru cech zawartych w naszym zestawie danych. W poszukiwaniu lepszych wyników można uruchamiać eksperymenty oparte na różnych podzbiorach cech. Jednak aby rozpocząć, wypróbujmy następujące funkcje:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Przeciągnij kolejny moduł [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych) do obszaru roboczego eksperymentu. Połącz lewy port wyjściowy modułu [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych) z wejściem modułu [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych).

    ![Łączenie modułu „Select Columns in Dataset” (Wybieranie kolumn w zestawie danych) z modułem „Clean Missing Data” (Czyszczenie brakujących danych)][connect-clean-to-select]
    <br/>
    ***Łączenie modułu „Select Columns in Dataset” (Wybieranie kolumn w zestawie danych) z modułem „Clean Missing Data” (Czyszczenie brakujących danych)***

2. Kliknij dwukrotnie moduł i wpisz „Wybieranie cech w celu prognozowania”.

2. Kliknij pozycję **Launch column selector** (Uruchom selektora kolumn) w okienku **Properties** (Właściwości).

3. Kliknij pozycję **With rules** (Za pomocą reguł).

4. W obszarze **Begin With** (Rozpocznij od) kliknij pozycję **No columns** (Brak kolumn). W wierszu filtru wybierz pozycje **Include** (Dołącz) i **column names** (nazwy kolumn), a następnie wybierz nazwy kolumn w polu tekstowym. Spowoduje to nieprzekazywanie przez moduł żadnych kolumn (cech) z wyjątkiem tych, które zostały wybrane.

5. Kliknij przycisk znacznika wyboru (OK).

    ![Wybieranie kolumn (cech), które mają zostać uwzględnione w prognozie][select-columns-to-include]
    <br/>
    ***Wybieranie kolumn (cech), które mają zostać uwzględnione w prognozie***

Powstanie filtrowany zestaw danych zawierający tylko cechy, które chcemy przekazać do algorytmu uczenia. Algorytm ten zostanie użyty w następnym kroku. Możesz później wrócić do tego kroku i wybrać inny zbiór cech.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Krok 4. Wybieranie i stosowanie algorytmu uczenia

Po przygotowaniu danych można przystąpić do konstruowania modelu predykcyjnego, co obejmuje uczenie i testowanie. Użyjemy danych do nauczenia modelu, a następnie przetestujemy go, aby sprawdzić dokładność przewidywanych cen.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

Algorytmy *klasyfikacji* i *regresji* to dwa typy nadzorowanego uczenia maszynowego. Klasyfikacja przewiduje odpowiedź na podstawie zdefiniowanego zestawu kategorii, takich jak kolory (czerwony, niebieski lub zielony). Regresja służy do prognozowania liczby.

Ponieważ chcemy przewidzieć cenę, która jest liczbą, użyjemy algorytmu regresji. W tym przykładzie użyjemy prostego modelu *regresji liniowej*.

> [!TIP]
> Jeśli chcesz dowiedzieć się więcej o różnych typach algorytmów uczenia maszynowego i ich zastosowaniach, obejrzyj pierwszy film wideo z serii Przetwarzanie danych dla początkujących: [5 pytań, na które analiza danych daje odpowiedzi](data-science-for-beginners-the-5-questions-data-science-answers.md). Możesz też przyjrzeć się grafice informacyjnej [Podstawy uczenia maszynowego z przykładowymi algorytmami](basics-infographic-with-algorithm-examples.md) lub zapoznać się z artykułem [Ściągawka dotycząca algorytmów uczenia maszynowego](algorithm-cheat-sheet.md).

Uczenie modelu polega na przekazaniu mu zestawu danych zawierających cenę. Model skanuje dane i szuka korelacji między cechami samochodu a jego ceną. Następnym krokiem jest przetestowanie modelu — otrzyma on zestaw cech określonych modeli samochodów w celu przewidzenia ich z góry znanych cen. Prognozy zostaną porównane z rzeczywistymi cenami samochodów.

Dane dzielimy na dwa oddzielne zestawy, aby użyć ich do celów szkoleniowych i do testów.

1. Wybierz moduł [Split Data][split] (Podział danych) i przeciągnij go do obszaru roboczego eksperymentu, a następnie połącz go z ostatnim modułem [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych).

2. Wybierz moduł [Split Data][split] (Podział danych), klikając go. Znajdź opcję **Fraction of rows in the first output dataset** (Odsetek wierszy w pierwszym zestawie danych wyjściowych) (w okienku **Właściwości** po prawej stronie obszaru roboczego) i ustaw dla niej wartość 0,75. Dzięki temu 75% danych zostanie użytych do nauczenia modelu, a pozostałe 25% do testów (można eksperymentować, zmieniając wartość tej opcji).

    ![Ustawianie parametru podziału modułu „Split Data” (Podział danych) na wartość 0,75][set-split-data-percentage]
    <br/>
    ***Ustawianie parametru podziału modułu „Split Data” (Podział danych) na wartość 0,75***

    > [!TIP]
    > Zmieniając wartość parametru **Random seed** (Inicjator losowy) można uzyskać różne próbki losowe do celów szkoleniowych i testów. Ten parametr umożliwia sterowanie inicjacją pseudolosowego generatora liczb.

2. Uruchom eksperyment. Po uruchomieniu eksperymentu moduły [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych) i [Split Data][split] (Podział danych) przekażą definicje kolumn do modułów, które będą dodawane później.  

3. Aby wybrać algorytm uczenia, rozwiń kategorię **Machine Learning** (Uczenie maszynowe) na palecie modułów wyświetlanej z lewej strony obszaru roboczego, a następnie rozwiń węzeł **Initialize Model** (Inicjacja modelu). Zostaną wyświetlone różne kategorie modułów, których można użyć do zainicjowania algorytmów uczenia maszynowego. W tym eksperymencie wybierz moduł [Linear Regression][linear-regression] (Regresja liniowa) z kategorii **Regression** (Regresja) i przeciągnij go do obszaru roboczego eksperymentu.
(Możesz również znaleźć go, wpisując „linear regression” [regresja liniowa] w polu wyszukiwania palety).

4. Znajdź moduł [Train Model][train-model] (Uczenie modelu) i przeciągnij go do obszaru roboczego eksperymentu. Połącz wyjście modułu [Linear Regression][linear-regression] (Regresja liniowa) z lewym wejściem modułu [Train Model][train-model] (Uczenie modelu) i połącz wyjście danych szkoleniowych (lewy port) modułu [Split Data][split] (Podział danych) z prawym wejściem modułu [Train Model][train-model] (Uczenie modelu).

    ![Łączenie modułu „Train model” (Uczenie modelu) z modułami „Linear Regression” (Regresja liniowa) i „Split Data” (Podział danych)][connect-train-model]
    <br/>
    ***Łączenie modułu „Train model” (Uczenie modelu) z modułami „Linear Regression” (Regresja liniowa) i „Split Data” (Podział danych)***

5. Kliknij moduł [Train Model][train-model] (Uczenie modelu), kliknij pozycję **Launch column selector** (Uruchom selektora kolumn) w okienku **Properties** (Właściwości), a następnie wybierz kolumnę **price** (cena). Jest to wartość, która będzie prognozowana przez nasz model.

    Kolumnę **price** (cena) możesz wybrać, przenosząc ją z listy **Available columns** (Dostępne kolumny) do listy **Selected columns** (Wybrane kolumny) w selektorze kolumn.

    ![Wybieranie kolumny cen w module „Train model” (Uczenie modelu)][select-price-column]
    <br/>
    ***Wybieranie kolumny cen w module „Train model” (Uczenie modelu)***

6. Uruchom eksperyment.

W efekcie powstał nauczony model regresji, który może służyć do generowania wyników na podstawie nowych danych samochodów w celu prognozowania cen.

![Gdy działanie eksperymentu zakończy się, powinien on wyglądać mniej więcej tak][second-experiment-run]
<br/>
***Gdy działanie eksperymentu zakończy się, powinien on wyglądać mniej więcej tak***

## <a name="step-5-predict-new-automobile-prices"></a>Krok 5. Przewidywanie nowych cen samochodów

Gdy udało się nauczyć model przy użyciu 75% danych, można wygenerować wyniki dla pozostałych 25% danych, aby sprawdzić poprawność funkcjonowania modelu.

1. Znajdź moduł [Score Model][score-model] (Generowanie wyników przez model) i przeciągnij go do obszaru roboczego eksperymentu. Połącz wyjście modułu [Train Model][train-model] (Uczenie modelu) z lewym portem wejściowym modułu [Score Model][score-model] (Generowanie wyników przez model). Połącz wyjście danych testowych (prawy port) modułu [Split Data][split] (Podział danych) z prawym portem wejściowym modułu [Score Model][score-model] (Generowanie wyników przez model).

    ![Łączenie modułu „Score model” (Generowanie wyników przez model) z modułami „Train Model” (Uczenie modelu) i „Split Data” (Podział danych)][connect-score-model]
    <br/>
    ***Łączenie modułu „Score model” (Generowanie wyników przez model) z modułami „Train Model” (Uczenie modelu) i „Split Data” (Podział danych)***

2. Uruchom eksperyment, aby wyświetlić dane wyjściowe z modułu [Score Model][score-model] (Generowanie wyników przez model). W tym celu kliknij port wyjściowy modułu [Score Model][score-model] i wybierz pozycję **Visualize** (Wizualizacja). Dane wyjściowe zawierają przewidywane wartości cen oraz znane wartości pochodzące z danych testowych.  

    ![Dane wyjściowe modułu „Score model” (Generowanie wyników przez model)][score-model-output]
    <br/>
    ***Dane wyjściowe modułu „Score model” (Generowanie wyników przez model)***

3. Na koniec przetestujemy jakość wyników. Wybierz moduł [Evaluate Model][evaluate-model] (Ocena modelu) i przeciągnij go do obszaru roboczego eksperymentu, a następnie połącz wyjście modułu [Score Model][score-model] (Generowanie wyników przez model) z lewym wejściem modułu [Evaluate Model][evaluate-model] (Ocena modelu).

    > [!TIP]
    > Moduł [Evaluate Model][evaluate-model] (Ocena modelu) ma dwa porty wejściowe, ponieważ może służyć do porównywania dwóch modeli. Można później dodać do eksperymentu inny algorytm, a następnie sprawdzić, czy daje on lepsze wyniki za pomocą modułu [Evaluate Model][evaluate-model] (Ocena modelu).

4. Uruchom eksperyment.

Aby wyświetlić dane wyjściowe z modułu [Evaluate Model][evaluate-model] (Ocena modelu), kliknij port wyjściowy i wybierz pozycję **Visualize** (Wizualizacja).

![Wyniki oceny eksperymentu][evaluation-results]
<br/>
***Wyniki oceny eksperymentu***

Wyświetlane są następujące statystyki dla modelu:

- **Średni bezwzględny błąd** (MAE, Mean Absolute Error): wartość średnia bezwzględnych błędów (*błąd* odpowiada różnicy między wartością prognozowaną a wartością rzeczywistą).
- **Pierwiastek błędu średniokwadratowego** (RMSE, Root Mean Squared Error): pierwiastek kwadratowy ze średniej kwadratów błędów prognoz dla zestawu danych testowych.
- **Względny błąd absolutny**: iloraz średniej błędów absolutnych i bezwzględnej wartości różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
- **Błąd względny średniokwadratowy**: iloraz średniej kwadratów błędów i kwadratu różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
- **Współczynnik determinacji**: znany także jako **wartość R-kwadrat** jest miarą statystyczną jakości dopasowania modelu do danych.

W przypadku wszystkich powyższych statystyk mniejsze wartości oznaczają lepszą jakość modelu. Mniejsze wartości błędów wskazują na ściślejsze dopasowanie prognoz do rzeczywistych wartości. W przypadku **współczynnika determinacji** prognozy są tym lepsze, im jego wartość jest bliższa jedności (1,0).

## <a name="final-experiment"></a>Eksperyment końcowy

Końcowy eksperyment powinien wyglądać następująco:

![Eksperyment końcowy][complete-linear-regression-experiment]
<br/>
***Eksperyment końcowy***

## <a name="next-steps"></a>Następne kroki

Po ukończeniu pierwszego samouczka dotyczącego uczenia maszynowego i skonfigurowaniu eksperymentu można kontynuować ulepszanie modelu, a następnie wdrożyć go jako predykcyjną usługę sieci Web.

- **Powtarzanie eksperymentu w celu ulepszenia modelu** — można na przykład zmienić cechy używane na potrzeby prognozowania. Można też zmodyfikować właściwości algorytmu [regresji liniowej][linear-regression] lub użyć całkowicie innego algorytmu. Można również dodać do eksperymentu wiele algorytmów uczenia maszynowego i porównać dwa z nich przy użyciu modułu [Evaluate Model][evaluate-model] (Ocena modelu).
Przykładowe porównanie różnych modeli można znaleźć w eksperymencie [Compare Regressors](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5) (Porównywanie regresorów) w [galerii sztucznej inteligencji platformy Azure](https://gallery.cortanaintelligence.com).

    > [!TIP]
    > Aby skopiować dowolną iterację eksperymentu, użyj przycisku **SAVE AS** (ZAPISZ JAKO) wyświetlanego u dołu strony. Aby wyświetlić wszystkie iteracje eksperymentu, kliknij pozycję **VIEW RUN HISTORY** (WYŚWIETL HISTORIĘ PRZEBIEGÓW) u dołu strony. Aby uzyskać bardziej szczegółowe informacje, zobacz [Manage experiment iterations in Azure Machine Learning Studio][runhistory] (Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio).

[runhistory]: manage-experiment-iterations.md

- **Wdrożenie modelu jako predykcyjnej usługi sieci Web** — jeśli model spełnia Twoje oczekiwania, możesz go wdrożyć jako usługę sieci Web i używać jej do prognozowania cen samochodów na podstawie nowych danych. Aby uzyskać dodatkowe informacje, zobacz [Deploy an Azure Machine Learning web service][publish] (Wdrażanie usługi sieci Web Azure Machine Learning).

[publish]: publish-a-machine-learning-web-service.md

Chcesz dowiedzieć się więcej? Aby zapoznać się z bardziej rozbudowanym i szczegółowym przewodnikiem po procesie tworzenia, uczenia i wdrażania modelu po wygenerowaniu wyników, zobacz [Develop a predictive solution by using Azure Machine Learning][walkthrough] (Opracowywanie rozwiązania predykcyjnego przy użyciu usługi Azure Machine Learning).

[walkthrough]: walkthrough-develop-predictive-solution.md

<!-- Images -->
[sign-in-to-studio]: ./media/create-experiment/sign-in-to-studio.png
[rename-experiment]: ./media/create-experiment/rename-experiment.png
[visualize-auto-data]:./media/create-experiment/visualize-auto-data.png
[select-visualize]: ./media/create-experiment/select-visualize.png
[showing-excluded-column]:./media/create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/create-experiment/second-experiment-run.png
[connect-score-model]:./media/create-experiment/connect-score-model.png
[evaluation-results]:./media/create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[type-automobile]:./media/create-experiment/type-automobile.png
[type-select-columns]:./media/create-experiment/type-select-columns.png
[launch-column-selector]:./media/create-experiment/launch-column-selector.png
[add-comment]:./media/create-experiment/add-comment.png
[connect-clean-to-select]:./media/create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[connect-train-model]:./media/create-experiment/connect-train-model.png
[select-price-column]:./media/create-experiment/select-price-column.png

[score-model-output]:./media/create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

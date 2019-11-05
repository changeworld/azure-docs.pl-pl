---
title: 'Samouczek: test wsadowy — LUIS'
titleSuffix: Azure Cognitive Services
description: W tym samouczku przedstawiono sposób korzystania z testów wsadowych w celu znalezienia problemów przewidywania wypowiedź w aplikacji i ich rozwiązania.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: ac88931d79df6c2527a2a5fd72b440baeb463115
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499062"
---
# <a name="tutorial-batch-test-data-sets"></a>Samouczek: zestawy danych testów wsadowych

W tym samouczku przedstawiono sposób korzystania z testów wsadowych w celu znalezienia problemów przewidywania wypowiedź w aplikacji i ich rozwiązania.  

Testowanie wsadowe umożliwia zweryfikowanie aktywnego, przeszkolonego stanu modelu z znanym zestawem oznaczonym przez wyrażenia długości i jednostek. W pliku wsadowym w formacie JSON Dodaj wyrażenia długości i ustaw etykiety jednostek, które są wymagane w ramach wypowiedź. 

Wymagania dotyczące testowania wsadowego:

* Maksymalnie 1000 wyrażenia długości na test. 
* Brak duplikatów. 
* Dozwolone typy jednostek: tylko jednostki połączone maszynowo proste i złożone. Testowanie wsadowe jest przydatne tylko w przypadku zamiarowych i nieopartych na maszynie.

W przypadku korzystania z aplikacji innej niż ten *samouczek nie należy używać* przykładowej wyrażenia długości już dodanej do zamiaru. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Utwórz plik testu wsadowego 
> * Uruchom test wsadowy
> * Przejrzyj wyniki testu
> * Napraw błędy 
> * Przetestowanie partii

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Wykonaj następujące czynności:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `batchtest`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL. 

4. Przeszkol aplikację.

## <a name="batch-file"></a>Plik wsadowy

1. Utwórz `HumanResources-jobs-batch.json` w edytorze tekstu lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) go. 

2. W pliku wsadowym w formacie JSON Dodaj wyrażenia długości z **zamiarem** , który ma być przewidywany w teście. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Uruchom zadanie wsadowe

1. Na górnym pasku nawigacyjnym wybierz pozycję **test** . 

2. Wybierz pozycję **panel testowania partii** w panelu po prawej stronie. 

    [![zrzut ekranu aplikacji LUIS z wyróżnionym panelem testów wsadowych](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Wybierz pozycję **Importuj zestaw danych**.

    [![zrzut ekranu aplikacji LUIS z wyróżnionym zestawem danych](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Wybierz lokalizację pliku `HumanResources-jobs-batch.json`.

5. Nazwij zestaw danych `intents only` a następnie wybierz pozycję **gotowe**.

    ![Wybierz plik](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Wybierz przycisk **Uruchom**. 

7. Wybierz pozycję **Zobacz wyniki**.

8. Przejrzyj wyniki w grafie i legendzie.

    [![zrzut ekranu aplikacji LUIS z wynikami testów wsadowych](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Przejrzyj wyniki partii

Na wykresie wsadowym są wyświetlane cztery ćwiartki wyników. Na prawo od wykresu jest filtrem. Filtr zawiera intencje i jednostki. Po wybraniu [sekcji wykresu](luis-concept-batch-test.md#batch-test-results) lub punktu na wykresie, skojarzone wypowiedź są wyświetlane poniżej wykresu. 

Po umieszczeniu wskaźnika na wykresie kółkiem myszy może powiększyć lub zmniejszyć poziom wyświetlania na wykresie. Jest to przydatne w przypadku, gdy wiele punktów na wykresie jest ściśle klastrowane. 

Wykres znajduje się w czterech ćwiartkach z dwiema sekcjami wyświetlanymi w kolorze czerwonym. **Są to sekcje, na których należy się skoncentrować**. 

### <a name="getjobinformation-test-results"></a>Wyniki testu GetJobInformation

Wyniki testu **GetJobInformation** wyświetlane w filtrze pokazują, że 2 z czterech prognoz zakończyło się powodzeniem. Wybierz nazwę **false negatywną** w lewym górnym rogu, aby zobaczyć wyrażenia długości pod wykresem. 

Użyj klawiatury, Ctrl + E, aby przełączyć się do widoku etykiety, aby zobaczyć dokładny tekst wypowiedź użytkownika. 

Wypowiedź `Is there a database position open in Los Colinas?` jest oznaczona jako _GetJobInformation_ , ale bieżący model przewiduje wypowiedź jako _ApplyForJob_. 

Istnieją prawie trzy razy wiele przykładów dla **ApplyForJob** niż **GetJobInformation**. Taka nierówność przykładu wyrażenia długości jest ważona na rzecz intencji **ApplyForJob** , co powoduje nieprawidłowe prognozowanie. 

Zwróć uwagę, że oba intencje mają taką samą liczbę błędów. Nieprawidłowe prognozowanie w jednym zamiarze również ma wpływ na inne zamierzenia. Oba mają Błędy, ponieważ wyrażenia długości zostały nieprawidłowo przewidywalne dla jednego zamiaru, a także nieprawidłowo nieprzewidziane dla innego celu. 

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>Jak naprawić aplikację

Celem tej sekcji jest zaplanowanie wszystkich wyrażenia długości prawidłowo przewidzianych dla **GetJobInformation** przez naprawianie aplikacji. 

Pozornie szybka naprawa spowodowałaby dodanie tych plików wsadowych wyrażenia długości do poprawnego celu. To nie co chcesz zrobić. Chcesz, aby LUIS prawidłowo przewidzieć te wyrażenia długości bez dodawania ich jako przykładów. 

Możesz również zainteresować się usuwanie wyrażenia długości z **ApplyForJob** , dopóki liczba wypowiedź nie będzie taka sama jak **GetJobInformation**. Może to poprawić wyniki testu, ale utrudni LUIS przed przewidywaniam tego celu w następnym momencie. 

Poprawka polega na dodaniu więcej wyrażenia długości do **GetJobInformation**. Pamiętaj, aby zmienić wypowiedź długość, wybór wyrazów i rozmieszczenie wyrazów, zachowując znaczenie dla zamiaru szukania informacji o zadaniu, _nie_ ma zastosowania do tego zadania.

### <a name="add-more-utterances"></a>Dodaj więcej wyrażenia długości

1. Zamknij Panel test wsadowy, wybierając przycisk **test** w górnym panelu nawigacji. 

2. Wybierz pozycję **GetJobInformation** z listy intencje. 

3. Dodaj więcej wyrażenia długościów, które są zróżnicowane dla długości, wyboru wyrazu i rozmieszczenia wyrazów, pamiętając o uwzględnieniu warunków `resume`, `c.v.`i `apply`:

    |Przykład wyrażenia długości dla intencji **GetJobInformation**|
    |--|
    |Czy nowe zadanie w hurtowni Stocker wymaga, aby mam zastosowanie z życiorysem?|
    |Gdzie dzisiaj są zadania dotyczące podachu?|
    |Słyszę, że było to zadanie kodowania medycznego wymagające wznowienia.|
    |Podoba mi się zadanie, aby zapewnić dzieciom swoje c.v.s. |
    |Oto moje wznowienie i poszukiwanie nowego wpisu na uczelni społecznościowej przy użyciu komputerów.|
    |Jakie pozycje są dostępne w obszarze dzieci i opieka domowa?|
    |Czy istnieje dział InterNIC na gazetie?|
    |Moje C.v. pokazuje, że jest to dobre przy analizowaniu zakupów, budżetów i utraty pieniędzy. Czy istnieje coś dla tego typu pracy?|
    |Gdzie są teraz zadania wiercenia ziemi?|
    |Pracowałem 8 lat jako sterownik EMS. Nowe zadania?|
    |Nowe zadania obsługi żywności wymagają aplikacji?|
    |Ile nowych zadań służbowych jest dostępnych?|
    |Czy istnieje nowy wpis w usłudze HR dla relacji robocizny i negocjacji?|
    |Mam wzorce w zarządzaniu biblioteką i archiwum. Nowe pozycje?|
    |Czy istnieją jakieś zadania zajmowania dla 13-letnich Olds w miejscowości dzisiaj?|

    Nie oznaczaj jednostki **zadania** w wyrażenia długości. Ta część samouczka koncentruje się na tylko prognozie intencji.

4. Nauczenie aplikacji przez wybranie opcji **uczenie** w prawym górnym rogu.

## <a name="verify-the-new-model"></a>Weryfikowanie nowego modelu

Aby sprawdzić, czy wyrażenia długości w teście wsadowym został prawidłowo przewidziany, uruchom ponownie test wsadowy.

1. Na górnym pasku nawigacyjnym wybierz pozycję **test** . Jeśli wyniki partii są nadal otwarte, wybierz pozycję **Powrót do listy**.  

1. Wybierz przycisk wielokropka (***...***) z prawej strony nazwy partii i wybierz polecenie **Uruchom**. Poczekaj, aż test wsadowy zostanie wykonany. Zwróć uwagę, że przycisk **Zobacz wyniki** jest teraz zielony. Oznacza to, że cała partia została uruchomiona pomyślnie.

1. Wybierz pozycję **Zobacz wyniki**. Intencje powinny mieć zielony ikon z lewej strony nazw intencji. 

## <a name="create-batch-file-with-entities"></a>Utwórz plik wsadowy z jednostkami 

Aby sprawdzić jednostki w teście wsadowym, należy oznaczyć jednostki w pliku JSON w usłudze Batch. 

Zróżnicowanie jednostek dla łącznej liczby wyrazów ([token](luis-glossary.md#token)) może mieć wpływ na jakość przewidywania. Upewnij się, że dane szkoleniowe dostarczane do zamiaru z etykietą wyrażenia długości zawierają różne długości jednostek. 

Podczas pierwszego zapisywania i testowania plików wsadowych najlepiej zacząć korzystać z kilku wyrażenia długości i jednostek, które znasz, a także kilka niewłaściwie przewidzianych. Dzięki temu można szybko skupić się na obszarach problemów. Po przetestowaniu intencji **GetJobInformation** i **ApplyForJob** przy użyciu kilku różnych nazw zadań, które nie zostały przewidywalne, ten plik testu wsadowego został opracowany w celu sprawdzenia, czy występuje problem z przewidywaniam z określonymi wartościami dla jednostki **zadania** . 

Wartość jednostki **zadania** , która znajduje się w wyrażenia długości testu, jest zwykle jednym lub dwoma słowami, z kilkoma przykładami więcej wyrazów. Jeśli _własne_ aplikacje kadrowe zwykle zawierają nazwy zadań wielu wyrazów, przykład wyrażenia długości z jednostką **zadania** w tej aplikacji nie działa prawidłowo.

1. Utwórz `HumanResources-entities-batch.json` w edytorze tekstu, takim jak [programu vscode](https://code.visualstudio.com/) , lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) go.

2. W pliku wsadowym w formacie JSON Dodaj tablicę obiektów, które zawierają wyrażenia długości z **zamiarem** , który ma być przewidywany w teście, a także lokalizacje wszystkich jednostek w wypowiedź. Ponieważ jednostka jest oparta na tokenach, upewnij się, że uruchamiasz i zatrzymasz każdą jednostkę na znaku. Nie rozpoczynaj ani nie kończyj wypowiedź na miejscu. Powoduje to błąd podczas importowania pliku wsadowego.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Uruchamianie partii z jednostkami

1. Na górnym pasku nawigacyjnym wybierz pozycję **test** . 

2. Wybierz pozycję **panel testowania partii** w panelu po prawej stronie. 

3. Wybierz pozycję **Importuj zestaw danych**.

4. Wybierz lokalizację systemu plików `HumanResources-entities-batch.json` pliku.

5. Nazwij zestaw danych `entities` a następnie wybierz pozycję **gotowe**.

6. Wybierz przycisk **Uruchom**. Poczekaj, aż test zostanie wykonany.

7. Wybierz pozycję **Zobacz wyniki**.

## <a name="review-entity-batch-results"></a>Przejrzyj wyniki partii jednostek

Zostanie otwarty wykres ze wszystkimi intencjami, które zostały prawidłowo przewidywalne. Przewiń w dół do filtru po prawej stronie, aby znaleźć przewidywania jednostek z błędami. 

1. Wybierz jednostkę **zadania** w filtrze.

    ![Przewidywania jednostek błędów w filtrze](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    Wykres zmieni się, aby wyświetlić przewidywania jednostek. 

2. W dolnym lewym rogu wykresu wybierz opcję **fałszywa ujemna** . Następnie użyj kontrolki kombinacja kombinacji + E, aby przełączyć się do widoku tokenu. 

    [Widok tokenu ![prognoz jednostek](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Przeglądanie wyrażenia długości poniżej wykresu ujawnia spójny błąd, gdy nazwa zadania zawiera `SQL`. Przeglądając przykład wyrażenia długości i listę fraz zadania, SQL jest używany tylko raz i tylko jako część większej nazwy zadania, `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Popraw aplikację na podstawie wyników partii jednostek

Rozwiązanie aplikacji wymaga, aby LUIS prawidłowo określić wahania zadań SQL. Istnieje kilka opcji tej poprawki. 

* Jawnie Dodaj więcej przykładowych wyrażenia długości, które używają języka SQL i etykietowania tych słów jako jednostki zadania. 
* Jawnie Dodaj więcej zadań SQL do listy fraz

Te zadania są pozostawione do wykonania.

Dodanie [wzorca](luis-concept-patterns.md) przed prawidłową przewidywaniam jednostki nie powoduje rozwiązania problemu. Dzieje się tak, ponieważ wzorzec nie jest zgodny, dopóki nie zostaną wykryte wszystkie jednostki w wzorcu. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

Samouczek użył testu wsadowego, aby znaleźć problemy z bieżącym modelem. Model został rozwiązany i ponownie przetestowany przy użyciu pliku wsadowego, aby sprawdzić, czy zmiana była poprawna.

> [!div class="nextstepaction"]
> [Informacje o wzorcach](luis-tutorial-pattern.md)


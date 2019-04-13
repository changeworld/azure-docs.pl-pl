---
title: Test usługi Batch
titleSuffix: Azure Cognitive Services
description: Ten samouczek pokazuje, jak za pomocą testowania partii znaleźć wypowiedź prognozowania problemy w aplikacji i je rozwiązać.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: 391a5386a5ecc144b15c35a85d501dfb5ce2d172
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523141"
---
# <a name="tutorial-batch-test-data-sets"></a>Samouczek: Zestawy danych testowych usługi Batch

Ten samouczek pokazuje, jak za pomocą testowania partii znaleźć wypowiedź prognozowania problemy w aplikacji i je rozwiązać.  

Testowanie usługi Batch umożliwia zweryfikowanie aktywny, skonfigurowanych pod kątem stanu modelu przy użyciu znanego zestawu etykietami wypowiedzi i jednostek. W pliku wsadowym w formacie JSON Dodawanie wypowiedzi i ustawić etykiety jednostki, które należy przewidzieć wewnątrz wypowiedź. 

Wymagania dotyczące testowania usługi batch:

* Maksymalnie 1000 wypowiedzi na test. 
* Bez duplikatów. 
* Dozwolone typy jednostek: tylko jednostki obrabiane przedstawiono proste hierarchicznych (tylko do nadrzędnego) i złożone. Testowanie usługi Batch przydaje się tylko do obrabiane do opanowania intencje i podmioty.

Podczas korzystania z aplikacji innych niż w tym samouczku, czy *nie* Użyj wypowiedzi przykład już dodany do intencji. 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Utwórz plik wsadowy testu 
> * Uruchom test usługi batch
> * Przejrzyj wyniki testu
> * Usuń błędy 
> * Przetestowanie usługi batch

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Wykonaj następujące czynności:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `batchtest`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL. 

4. Przeszkol aplikację.

## <a name="batch-file"></a>Plik wsadowy

1. Tworzenie `HumanResources-jobs-batch.json` w edytorze tekstu lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) go. 

2. W pliku wsadowym w formacie JSON, dodawanie wypowiedzi z **intencji** mają dostęp do przewidywanych w teście. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Uruchom partię

1. Wybierz **testu** w górnym pasku nawigacyjnym. 

2. Wybierz **Batch testowania panelu** w panelu po prawej stronie. 

    [![Zrzut ekranu usługi LUIS aplikacji za pomocą panelu testu partii wyróżniony](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Wybierz **Importowanie zestawu danych**.

    [![Zrzut ekranu usługi LUIS aplikacji za pomocą zestawu danych importu wyróżniony](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Wybierz lokalizację pliku `HumanResources-jobs-batch.json` pliku.

5. Nazwij zestaw danych `intents only` i wybierz **gotowe**.

    ![Wybierz plik](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Wybierz przycisk **Uruchom**. 

7. Wybierz **wyniki**.

8. Przejrzyj wyniki w wykresu i legenda.

    [![Zrzut ekranu usługi LUIS aplikacji za pomocą usługi batch, wyników testów](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Przejrzyj wyniki usługi batch

Wykres batch przedstawia cztery quadrants firmy Gartner wyników. Z prawej strony wykresu jest filtrem. Domyślnie filtr jest równa pierwszej intencji na liście. Filtr zawiera intencje i tylko prosty, hierarchicznych (tylko do nadrzędnego) i złożone jednostek. Po wybraniu [sekcji wykresu](luis-concept-batch-test.md#batch-test-results) lub punkt w obrębie wykresu skojarzone utterance(s) wyświetlane pod wykresem. 

Podczas przesuwania wskaźnika w obrębie wykresu, kółka myszy można zwiększyć lub zmniejszyć wyświetlane na wykresie. Jest to przydatne, gdy istnieje wiele punktów na wykresie klastrowane ściśle ze sobą. 

Wykres jest w czterech quadrants firmy Gartner, przy użyciu dwóch sekcjach wyświetlane na czerwono. **Są to sekcje, aby skoncentrować się na**. 

### <a name="getjobinformation-test-results"></a>Wyniki testu GetJobInformation

**GetJobInformation** wyniki testów wyświetlane w filtrze pokazują, czy 2 cztery prognozy zakończyły się pomyślnie. Wybierz nazwę **wynik fałszywie dodatni** powyżej górnej quadrant prawo wyświetlić wypowiedzi pod wykresem. 

![Usługa LUIS partii testów wypowiedzi](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

Dlaczego są dwa wypowiedzi przewidzieć jako **ApplyForJob**, zamiast poprawne intencji **GetJobInformation**? Dwie opcje są bardzo pokrewne pod względem wybranego programu word i rozmieszczenie programu word. Ponadto są prawie trzy razy więcej przykładów dla **ApplyForJob** niż **GetJobInformation**. Ta nierówności wypowiedzi przykład zadowalająco uwzględni wagi **ApplyForJob** Preferuj jego przeznaczenie. 

Należy zauważyć, że zarówno intencji mają ten sam liczbę błędów. Niepoprawne prognozowania w jednym profilu konwersji ma wpływ na inne zamiar także. Oba zawierają błędy, ponieważ wypowiedzi zostały nieprawidłowo przewidywany intencji jeden, a także niepoprawnie nie przewidywany inną intencji. 

![Błędy filtrów testu partii usługi LUIS](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

Wypowiedzi odpowiadającego górnej do punktu w **wynik fałszywie dodatni** sekcji są `Can I apply for any database jobs with this resume?` i `Can I apply for any database jobs with this resume?`. Dla pierwszego wypowiedź, wyraz `resume` tylko została użyta w **ApplyForJob**. Dla drugiego wypowiedź, wyraz `apply` tylko została użyta w **ApplyForJob** intencji.

## <a name="fix-the-app"></a>Usuń aplikację

Celem tej sekcji jest wszystkie wypowiedzi poprawnie przewidzieć dla **GetJobInformation** Napraw aplikację. 

Pozornie szybka poprawka będzie można dodać te wypowiedzi pliku wsadowego na intencje poprawne. To nie co chcesz zrobić, mimo że. Chcesz, aby usługa LUIS można poprawnie przewidzieć te wypowiedzi bez dodawania ich przykłady. 

Również być może zastanawiasz się o usuwaniu wypowiedzi z **ApplyForJob** aż ilość wypowiedź jest taka sama jak **GetJobInformation**. Który może naprawić wyniki testu, ale utrudniłoby usługi LUIS z precyzyjne Prognozowanie tym przeznaczeniem następnym razem. 

Pierwsze rozwiązanie polega na dodawanie wypowiedzi więcej do **GetJobInformation**. Drugi poprawka jest zmniejszenie wagi wyrazy, takie jak `resume` i `apply` kierunku **ApplyForJob** intencji. 

### <a name="add-more-utterances"></a>Dodawanie wypowiedzi więcej

1. Zamknij panel testu usługi batch, wybierając **Test** w górnym menu nawigacyjnym panelu. 

2. Wybierz **GetJobInformation** z listy opcji. 

3. Dodawanie wypowiedzi więcej, które są zróżnicowane dla długości, wybór programu word i rozmieszczenie programu word, upewniając się objął `resume`, `c.v.`, i `apply`:

    |Przykład wypowiedzi dla **GetJobInformation** intencji|
    |--|
    |Nowe zadanie w magazynie stocker wymaga czy mogę stosować z życiorysu?|
    |Gdzie dziś znajdują się zadania konstrukcji dachów?|
    |Podobno, że było medycznych zadanie kodowania, które wymaga życiorysu.|
    |Chcę otrzymywać zadania, pomagając dzieci college zapisu ich c.v.s. |
    |Oto życiorysem szukasz nowego wpisu w policealnej na komputerach.|
    |Pozycje, które są dostępne w opiekę podrzędnych i w domu?|
    |W gazet jest technicznej serii?|
    |Moje doświadczeń Pokazuje, że jestem dobrze wykonywać analizy zakupów, budżetów i utratę pieniędzy. Czy jest coś dla tego typu elementu roboczego?|
    |Gdzie są ziemi przechodzenia do szczegółów zadań teraz?|
    |Prócz tego pracowałam 8 lat jako sterownik pakietu EMS. Żadne nowe zadania?|
    |Nowe zadania obsługi żywności wymaga aplikacji?|
    |Jak wiele nowych zadań roboczych yard są dostępne?|
    |Dla negocjacji i relacje pracy jest opublikowany nowy wpis HR?|
    |Mam wzorców zarządzania biblioteką i archiwum. Wszelkie nowe pozycje?|
    |Dostępne są wszystkie zadania babysitting dla 13 wieku roku w mieście już dzisiaj?|

    Etykieta nie **zadania** jednostki w wypowiedzi. Ta część samouczka koncentruje się na tylko intencji prognozy.

4. Uczenie aplikacji, wybierając **Train** w górnym kierunku prawego paska nawigacyjnego.

## <a name="verify-the-new-model"></a>Sprawdź nowy model

Aby sprawdzić, czy oczekuje wypowiedzi w teście usługi batch, należy ponownie uruchomić test usługi batch.

1. Wybierz **testu** w górnym pasku nawigacyjnym. Jeśli wyniki przetwarzania wsadowego są wciąż otwarty, wybierz opcję **powrót do listy**.  

2. Wybierz przycisk wielokropka (***...*** ) znajdujący się na prawo od nazwy usługi batch i wybierz **Uruchom zestaw danych**. Zaczekaj, aż odbywa się badanie usługi batch. Należy zauważyć, że **wyniki** kolor zielony ma teraz przycisk. Oznacza to, że całą partię został uruchomiony pomyślnie.

3. Wybierz **wyniki**. Intencji powinien mieć zielony ikon po lewej stronie nazwy metody konwersji. 

    ![Zrzut ekranu usługi LUIS z wyróżnionym przyciskiem wyniki usługi batch](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)

## <a name="create-batch-file-with-entities"></a>Utwórz plik wsadowy z jednostkami 

Aby sprawdzić, czy jednostki w teście usługi batch, jednostki muszą oznaczone etykietą w pliku JSON usługi batch. Używane są tylko maszyny do opanowania jednostki: prosty, hierarchiczne (tylko do nadrzędnego) i złożone jednostek. Nie należy dodawać jednostki przedstawiono maszyny, ponieważ one zawsze znajdują się za pomocą wyrażeń regularnych lub jawnego tekstu.

Zmiany jednostki dla programu word całkowita ([tokenu](luis-glossary.md#token)) liczby mogą mieć wpływ na jakość prognozy. Upewnij się, że dane szkoleniowe dostarczane na intencje z etykietami wypowiedzi zawiera różne długości jednostki. 

Po pierwsze pisaniu i testowaniu plików wsadowych, zaleca się rozpoczynać kilka wypowiedzi i jednostek, które znasz pracować, a także kilka namysłu może niepoprawnie przewidzieć. Ten pozwala skoncentrować się w sprawie obszarów problemów szybko. Po przetestowaniu **GetJobInformation** i **ApplyForJob** intencji przy użyciu kilku różnych zadań nazwy, które nie zostały przewidzieć, ten plik wsadowy w test został opracowany, aby sprawdzić, czy jest problem prognoz przy użyciu określonych wartości dla **zadania** jednostki. 

Wartość **zadania** jednostki, podany w wypowiedzi testu jest zazwyczaj jednego lub dwóch słów o kilka przykładów jest więcej słów. Jeśli _własne_ aplikacji kadrowej zwykle zawiera nazwy zadania wiele słów, z etykietą wypowiedzi przykład **zadania** jednostki w tej aplikacji nie będzie działać poprawnie.

1. Tworzenie `HumanResources-entities-batch.json` w edytorze tekstu, takie jak [VSCode](https://code.visualstudio.com/) lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) go.


2. W pliku wsadowym w formacie JSON, Dodaj tablicę obiektów, które zawierają wypowiedzi z **intencji** mają dostęp do przewidywanych w test, a także lokalizacje wszystkie jednostki w wypowiedź. Ponieważ jednostka jest oparte na tokenie, upewnij się, uruchamianie i zatrzymywanie każdej jednostki na znak. Nie rozpoczyna się lub kończy wypowiedź w miejscu. Powoduje to błąd podczas importowania pliku wsadowego.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Uruchom partię z jednostkami

1. Wybierz **testu** w górnym pasku nawigacyjnym. 

2. Wybierz **Batch testowania panelu** w panelu po prawej stronie. 

3. Wybierz **Importowanie zestawu danych**.

4. Wybierz lokalizację systemu plików `HumanResources-entities-batch.json` pliku.

5. Nazwij zestaw danych `entities` i wybierz **gotowe**.

6. Wybierz przycisk **Uruchom**. Zaczekaj, aż testu jest wykonywane.

7. Wybierz **wyniki**.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="review-entity-batch-results"></a>Przejrzyj wyniki partii jednostek

Wykres zostanie otwarty przy użyciu wszystkich intencji poprawnie przewidzieć. Przewiń w dół w filtrze po prawej stronie można znaleźć erroring prognozy jednostki. 

1. Wybierz **zadania** jednostki w filtrze.

    ![Prognozy jednostki erroring w filtrze](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    Aby wyświetlić prognozy jednostki zmiany wykresu. 

2. Wybierz **fałszywie ujemny** w dolnym lewym quadrant wykresu. Następnie użyć klawiatury kombinacji control + E, aby przełączyć do widoku tokenu. 

    [![Token widoku prognoz jednostki](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Przeglądanie wypowiedzi pod wykresem, co spowoduje wyświetlenie błędu spójne gdy nazwa zadania zawiera `SQL`. Przeglądanie wypowiedzi przykład i Job list frazy, SQL jest tylko użyty raz i tylko jako część nazwę zadania większych `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Usuń aplikację na podstawie jednostki usługi batch wyników

Naprawianie aplikacji wymaga usługi LUIS do poprawnie określania wariantów zadań SQL. Dostępnych jest kilka opcji tej poprawki. 

* Jawnie dodać więcej wypowiedzi przykład, w których korzystanie z programu SQL i oznaczać je jako element zadania. 
* Jawnie dodać więcej zadań SQL do listy fraz

Te zadania są pozostawiane należy wykonać.

Dodawanie [wzorzec](luis-concept-patterns.md) przed jednostki jest poprawnie przewidzieć, nie będzie można rozwiązać ten problem. Jest to spowodowane wzorzec nie będzie zgodne, dopóki nie zostaną wykryte wszystkich jednostek w wzorzec. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

Samouczek umożliwia wyszukiwanie problemów z bieżącym modelem testu usługi batch. Model został rozwiązany i powtórnie testowane z pliku wsadowego, aby sprawdzić, czy zmiana była poprawna.

> [!div class="nextstepaction"]
> [Więcej informacji na temat wzorców](luis-tutorial-pattern.md)


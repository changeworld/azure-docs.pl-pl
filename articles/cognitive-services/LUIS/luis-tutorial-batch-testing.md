---
title: Umożliwia testowanie partii poprawić prognoz usługi LUIS | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Batch test obciążeniowy, przejrzyj wyniki i poprawy usługi LUIS prognozy ze zmianami.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/06/2018
ms.author: v-geberr
ms.openlocfilehash: b695783c6d68876d39482ed5abec24f45087603d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054866"
---
# <a name="improve-app-with-batch-test"></a>Ulepszaniu aplikacji z testem usługi batch

Ten samouczek pokazuje, jak na potrzeby testowania partii Znajdź wypowiedź prognozowania problemy.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

<!-- green checkmark -->
> [!div class="checklist"]
* Utwórz plik wsadowy testu 
* Uruchom test usługi batch
* Przejrzyj wyniki testu
* Napraw błędy do intencji
* Przetestowanie usługi batch

Na potrzeby tego artykułu jest wymagane bezpłatne konto usługi [LUIS](luis-reference-regions.md#luis-website), które umożliwia utworzenie aplikacji usługi LUIS.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz zarządzania zasobami ludzkimi firmy [Przejrzyj wypowiedzi punktu końcowego](luis-tutorial-review-endpoint-utterances.md) samouczek, [zaimportować](luis-how-to-start-new-app.md#import-new-app) dane JSON do nowej aplikacji w [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web. Aplikacja do zaimportowania znajduje się w repozytorium [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-review-HumanResources.json) usługi Github.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `batchtest`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

## <a name="purpose-of-batch-testing"></a>Celem testowanie usługi batch
Testowanie usługi Batch umożliwia zweryfikowanie stanu modelu przy użyciu znanego zestawu testów wypowiedzi i nazwie jednostki. W pliku wsadowym w formacie JSON Dodawanie wypowiedzi i ustawić etykiety jednostki, które należy przewidzieć wewnątrz wypowiedź. 

Test zalecanych strategii LUIS używa trzech oddzielnych zestawów danych: przykład wypowiedzi do modelu, wypowiedzi testu usługi batch i wypowiedzi punktu końcowego. Na potrzeby tego samouczka upewnij się, że nie używasz wypowiedzi z obu wypowiedzi przykład (dodawane do intencji) lub wypowiedzi punktu końcowego. 

Aby sprawdzić swoje wypowiedzi testu batch przed przykład wypowiedzi i wypowiedzi punktu końcowego [wyeksportować](luis-how-to-start-new-app.md#export-app) aplikacji i [Pobierz](luis-how-to-start-new-app.md#export-endpoint-logs) dziennik zapytań. Porównaj wypowiedź przykład aplikacji i wypowiedzi dziennika zapytań do wypowiedzi testu usługi batch. 

Wymagania dotyczące testowania usługi batch:

* 1000 wypowiedzi na test. 
* Bez duplikatów. 
* Dozwolone typy jednostek: proste i złożone.

## <a name="create-a-batch-file-with-utterances"></a>Utwórz plik wsadowy z wypowiedzi
1. Tworzenie `HumanResources-jobs-batch.json` w edytorze tekstu, takie jak [VSCode](https://code.visualstudio.com/). 

2. W pliku wsadowym w formacie JSON, dodawanie wypowiedzi z **intencji** mają dostęp do przewidywanych w teście. 

    ```JSON
    [
        {
        "text": "Are there any janitorial jobs currently open?",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "I would like a fullstack typescript programming with azure job",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "Is there a database position open in Los Colinas?",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "Can I apply for any database jobs with this resume?",
        "intent": "GetJobInformation",
        "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Uruchom partię

1. Wybierz **testu** w górnym pasku nawigacyjnym. 

    [ ![Zrzut ekranu usługi LUIS aplikacji za pomocą testu wyróżniona na pasku nawigacyjnym z góry, prawej](./media/luis-tutorial-batch-testing/hr-first-image.png)](./media/luis-tutorial-batch-testing/hr-first-image.png#lightbox)

2. Wybierz **Batch testowania panelu** w panelu po prawej stronie. 

    [ ![Zrzut ekranu usługi LUIS aplikacji za pomocą panelu testu partii wyróżniony](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Wybierz **Importowanie zestawu danych**.

    [ ![Zrzut ekranu usługi LUIS aplikacji za pomocą zestawu danych importu wyróżniony](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Wybierz lokalizację systemu plików `HumanResources-jobs-batch.json` pliku.

5. Nazwij zestaw danych `intents only` i wybierz **gotowe**.

    ![Wybierz plik](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Wybierz przycisk **Uruchom**. Zaczekaj, aż testu jest wykonywane.

    [ ![Zrzut ekranu usługi LUIS aplikacji z wyróżnioną pozycją uruchomiony](./media/luis-tutorial-batch-testing/hr-run-button.png)](./media/luis-tutorial-batch-testing/hr-run-button.png#lightbox)

7. Wybierz **wyniki**.

8. Przejrzyj wyniki w wykresu i legenda.

    [ ![Zrzut ekranu usługi LUIS aplikacji za pomocą usługi batch, wyników testów](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Przejrzyj wyniki usługi batch
Wykres batch przedstawia cztery quadrants firmy Gartner wyników. Z prawej strony wykresu jest filtrem. Domyślnie filtr jest równa pierwszej intencji na liście. Filtr zawiera intencje i tylko prosty, hierarchicznych (tylko do nadrzędnego) i złożone jednostek. Po wybraniu sekcji wykresu lub punkt w obrębie wykresu, skojarzone utterance(s) wyświetlane pod wykresem. 

Podczas przesuwania wskaźnika w obrębie wykresu, kółka myszy można zwiększyć lub zmniejszyć wyświetlane na wykresie. Jest to przydatne, gdy istnieje wiele punktów na wykresie klastrowane ściśle ze sobą. 

Wykres jest w czterech quadrants firmy Gartner, przy użyciu dwóch sekcjach wyświetlane na czerwono. **Są to sekcje, aby skoncentrować się na**. 

### <a name="applyforjob-test-results"></a>Wyniki testu ApplyForJob
**ApplyForJob** wyniki testów wyświetlane w filtrze pokazują, czy 1 cztery prognozy zakończyła się pomyślnie. Wybierz nazwę **wynik fałszywie dodatni** powyżej górnej quadrant prawo wyświetlić wypowiedzi pod wykresem. 

![Usługa LUIS partii testów wypowiedzi](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

Trzy wypowiedzi miał najważniejsze celem **ApplyForJob**. Opcje określone w pliku wsadowym ma wynik niższy. Dlaczego to się stało? Dwie opcje są bardzo pokrewne pod względem wybranego programu word i rozmieszczenie programu word. Ponadto są prawie trzy razy więcej przykładów dla **ApplyForJob** niż **GetJobInformation**. Ta nierówności wypowiedzi przykład zadowalająco uwzględni wagi **ApplyForJob** Preferuj jego przeznaczenie. 

Zwróć uwagę, że zarówno intencji mają ten sam liczba błędów: 

![Błędy filtrów testu partii usługi LUIS](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

Wypowiedź odpowiadającego górnego punktu w **wynik fałszywie dodatni** sekcja `Can I apply for any database jobs with this resume?`. Wyraz `resume` tylko została użyta w **ApplyForJob**. 

Dwa punkty na wykresie ma znacznie niższe wyniki dla niewłaściwego przeznaczenie, co oznacza, że są one bliżej poprawne intencji. 

## <a name="fix-the-app-based-on-batch-results"></a>Usuń aplikację na podstawie wyników usługi batch
Celem tej sekcji jest trzech wypowiedzi zostały nieprawidłowo przewidywany **ApplyForJob** do można poprawnie przewidzieć dla **GetJobInformation**po aplikacji zostanie usunięty. 

Pozornie szybka poprawka będzie można dodać te wypowiedzi pliku wsadowego na intencje poprawne. To nie co chcesz zrobić, mimo że. Chcesz, aby usługa LUIS można poprawnie przewidzieć te wypowiedzi bez dodawania ich przykłady. 

Również być może zastanawiasz się o usuwaniu wypowiedzi z **ApplyForJob** aż ilość wypowiedź jest taka sama jak **GetJobInformation**. Który może naprawić wyniki testu, ale utrudniłoby usługi LUIS z precyzyjne Prognozowanie tym przeznaczeniem następnym razem. 

Pierwsze rozwiązanie polega na dodawanie wypowiedzi więcej do **GetJobInformation**. Drugi obejście polega na zmniejszenie wagi wyraz `resume` kierunku **ApplyForJob** intencji. 

### <a name="add-more-utterances-to-getjobinformation"></a>Dodawanie wypowiedzi więcej do **GetJobInformation**
1. Zamknij panel testu usługi batch, wybierając **Test** w górnym menu nawigacyjnym panelu. 

    [ ![Zrzut ekranu usługi LUIS z wyróżnionym przyciskiem testu](./media/luis-tutorial-batch-testing/hr-close-test-panel.png)](./media/luis-tutorial-batch-testing/hr-close-test-panel.png#lightbox)

2. Wybierz **GetJobInformation** z listy opcji. 

    [ ![Zrzut ekranu usługi LUIS z wyróżnionym przyciskiem testu](./media/luis-tutorial-batch-testing/hr-select-intent-to-fix-1.png)](./media/luis-tutorial-batch-testing/hr-select-intent-to-fix-1.png#lightbox)

3. Dodawanie wypowiedzi więcej, które są zróżnicowane dla długości, wybór programu word i rozmieszczenie programu word, upewniając się objął `resume` i `c.v.`:

    ```JSON
    Is there a new job in the warehouse for a stocker?
    Where are the roofing jobs today?
    I heard there was a medical coding job that requires a resume.
    I would like a job helping college kids write their c.v.s. 
    Here is my resume, looking for a new post at the community college using computers.
    What positions are available in child and home care?
    Is there an intern desk at the newspaper?
    My C.v. shows I'm good at analyzing procurement, budgets, and lost money. Is there anything for this type of work?
    Where are the earth drilling jobs right now?
    I've worked 8 years as an EMS driver. Any new jobs?
    New food handling jobs?
    How many new yard work jobs are available?
    Is there a new HR post for labor relations and negotiations?
    I have a masters in library and archive management. Any new positions?
    Are there any babysitting jobs for 13 year olds in the city today?
    ```

4. Uczenie aplikacji, wybierając **Train** w górnym kierunku prawego paska nawigacyjnego.

## <a name="verify-the-fix-worked"></a>Sprawdzić zadziałała poprawki
Aby sprawdzić, czy oczekuje wypowiedzi w teście usługi batch, należy ponownie uruchomić test usługi batch.

1. Wybierz **testu** w górnym pasku nawigacyjnym. Jeśli wyniki przetwarzania wsadowego są wciąż otwarty, wybierz opcję **powrót do listy**.  

2. Wybierz przycisk wielokropka (***...*** ) znajdujący się na prawo od nazwy usługi batch i wybierz **Uruchom zestaw danych**. Zaczekaj, aż odbywa się badanie usługi batch. Należy zauważyć, że **wyniki** kolor zielony ma teraz przycisk. Oznacza to, że całą partię został uruchomiony pomyślnie.

3. Wybierz **wyniki**. Intencji powinien mieć zielony ikon po lewej stronie nazwy metody konwersji. 

    [ ![Zrzut ekranu usługi LUIS z wyróżnionym przyciskiem wyniki usługi batch](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png#lightbox)


## <a name="what-has-this-tutorial-accomplished"></a>Co to jest wykonywane w tym samouczku?
Dokładność prognozowania tej aplikacji został zwiększony, znajdowanie błędów w partii i poprawianie modelu, dodając więcej wypowiedzi przykład poprawne intencji i szkolenia. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Wybierz pozycję **My apps** (Moje aplikacje) z menu w lewym górnym rogu. Wybierz przycisk wielokropka **...**  po prawej stronie nazwy aplikacji, na liście aplikacji wybierz **Usuń**. W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Więcej informacji na temat wzorców](luis-tutorial-pattern.md)


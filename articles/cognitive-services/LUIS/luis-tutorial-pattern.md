---
title: Samouczek przy użyciu wzorców w celu przewidywania usługi LUIS — Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: W tym samouczku należy użyć wzorca dla opcji, aby zwiększyć prognozy intencji i jednostki usługi LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 12105829f62b988760d3bbf18000466fd27b9aff
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888335"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Samouczek: Używanie wzorców w celu przewidywania

W tym samouczku Użyj wzorce, aby zwiększyć prognozowania intencji i jednostek.  

> [!div class="checklist"]
* Jak zidentyfikować, że wzorzec może pomóc Twojej aplikacji
* Jak utworzyć wzorca 
* Jak używać wbudowanych i niestandardowych jednostek w wzorzec 
* Jak zweryfikować ulepszenia prognozowania wzorzec
* Jak dodać roli do jednostki w celu znalezienia kontekstowe na podstawie jednostki
* Jak dodać Pattern.any można znaleźć jednostki w dowolnej postaci

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS](luis-reference-regions.md) w celu tworzenia aplikacji LUIS.

## <a name="import-humanresources-app"></a>Importuj kadry aplikacji
W tym samouczku importuje aplikacja kadry. Aplikacja ma trzy opcje: Brak, GetEmployeeOrgChart, GetEmployeeBenefits. Aplikacja ma dwie jednostki: liczba wbudowanych i pracowników. Jednostka pracowników jest proste jednostki można wyodrębnić nazwy pracownika. 

1. Utwórz nowy plik aplikacji usługi LUIS i nadaj mu nazwę `HumanResources.json`. 

2. Skopiuj poniższą definicję aplikacji do pliku:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. Luis **aplikacje** wybierz opcję **importowania Nowa aplikacja**. 

4. W **importowania Nowa aplikacja** okno dialogowe, wybierz opcję `HumanResources.json` pliku utworzonego w kroku 1.

5. Wybierz **GetEmployeeOrgChart** przeznaczenie, a następnie zmień z **widoku jednostek** do **tokenów widoku**. Kilka wypowiedzi w przykładzie są wyświetlane. Każdy wypowiedź zawiera nazwę, która jest jednostką pracownika. Należy zauważyć, że każda nazwa różni się i że rozmieszczenie treść różni się dla każdego wypowiedź. Ta różnorodność pomaga Dowiedz się, szerokiej gamy wypowiedzi usługi LUIS.

    ![Zrzut ekranu z zamiarem strony przy użyciu widoku jednostek przełączać](media/luis-tutorial-pattern/utterances-token-view.png)

6. Wybierz **szkolenie** w górnym pasku nawigacyjnym to w opracowywaniu aplikacji. Poczekaj, aż pasek Powodzenie zielony.

7. Wybierz **testu** w górnym panelu. Wprowadź `Who does Patti Owens report to?` a następnie wybierz enter. Wybierz **Sprawdź** w obszarze wypowiedź, aby uzyskać więcej informacji o testu.
    
    W przykładzie wypowiedź jeszcze nie używano nazwiska pracownika Patti Owens. To jest test, aby zobaczyć, jak dobrze LUIS pokazaliśmy, jest to wypowiedź `GetEmployeeOrgChart` intencji i jednostki pracowników powinny być `Patti Owens`. Wynik powinno być niższe niż 50% (. 50) dla `GetEmployeeOrgChart` intencji. Celem jest poprawny, wynik jest niska. Jednostki pracowników jest również prawidłowo identyfikowana jako `Patti Owens`. Wzorce zwiększyć ten wynik początkowej prognozy. 

    ![Zrzut ekranu testu panelu](media/luis-tutorial-pattern/original-test.png)

8. Zamknij panel testu, wybierając **Test** przycisku w górnym menu nawigacyjnym. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Wzorce uczyć LUIS wspólnej wypowiedzi wraz z przykładami mniej
Ze względu na charakter domeny zarządzania zasobami ludzkimi istnieje kilka typowych sposobów z pytaniem o relacjach pracowników w organizacji. Na przykład:

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Te wypowiedzi są zbyt Zamknij, aby określić kontekstowych unikatowości każdego bez podawania wiele przykładów wypowiedź. Dodając wzorzec intencji, LUIS uczy się typowe wzorce wypowiedź intencji bez podawania wiele przykładów wypowiedź. 

Przykładowy szablon wypowiedzi konwersji między innymi:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Wzorzec jest kombinacją Dopasowywanie wyrażeń regularnych i uczenia maszynowego. Następnie należy podać niektóre szablonu wypowiedź przykłady użycia usługi LUIS dowiedzieć się więcej ze wzorcem. Te przykłady, wraz z intencji wypowiedzi zapewniają LUIS lepiej zrozumieć, z jakiego wypowiedzi Dopasuj intencji i where, w ramach wypowiedź, jednostka istnieje. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>Dodawanie wypowiedzi szablonu

1. W lewym obszarze nawigacji w obszarze **lepsza wydajność aplikacji**, wybierz opcję **wzorców** w lewym obszarze nawigacji.

2. Wybierz **GetEmployeeOrgChart** przeznaczenie, wprowadź następujące wypowiedzi szablonu, po kolei, wybranie enter po każdym wypowiedź szablonu:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    `{Employee}` Składni oznacza lokalizację jednostki w ramach wypowiedź szablonu jako jest również jako jakiej encji. 

    ![Zrzut ekranu przedstawiający wprowadzanie wypowiedzi szablonu dla intencji](./media/luis-tutorial-pattern/enter-pattern.png)

3. Wybierz **Train** w górnym pasku nawigacyjnym. Poczekaj, aż pasek Powodzenie zielony.

4. Wybierz **testu** w górnym panelu. Wprowadź `Who does Patti Owens report to?` w polu tekstowym. Wybierz opcję Wprowadź. Jest to ten sam wypowiedź, przetestowane w poprzedniej sekcji. Wynik powinien być wyższe `GetEmployeeOrgChart` intencji. 

    Wynik jest teraz znacznie lepiej. Usługa LUIS przedstawiono wzorzec dotyczą zamiar bez podawania wiele przykładów.

    ![Zrzut ekranu testu panel Wysoka ocena wyniku](./media/luis-tutorial-pattern/high-score.png)

    Jednostki znajduje się najpierw, a następnie zostanie odnaleziony wzorzec, wskazując intencji. Jeśli wynik testu, w którym jednostki nie zostanie wykryty, a w związku z tym nie zostanie odnaleziony wzorzec, należy dodać więcej wypowiedzi przykład na intencji (nie wzorzec). 

5. Zamknij panel testu, wybierając **Test** przycisku w górnym menu nawigacyjnym.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Jednostki za pomocą roli we wzorcu
Aplikacja usługi LUIS umożliwiają przenoszenie pracowników z jednej lokalizacji do innej. Przykład wypowiedź jest `Move Bob Jones from Seattle to Los Colinas`. Każda lokalizacja w wypowiedź ma inne znaczenie. Seattle to lokalizacja, źródłowy i Los Colinas jest lokalizacja docelowa na czas przeprowadzki. W celu dokonania rozróżnienia tych lokalizacjach we wzorcu, w poniższych sekcjach utworzysz prostą jednostki dla lokalizacji przy użyciu dwóch ról: początkowe i docelowe. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Utwórz nowy opcję dotyczące przenoszenia osób i zasoby
Utwórz nowy przeznaczenie wypowiedzi, które są dotyczące przenoszenia osób lub zasobów.

1. Wybierz **intencji** nawigacji po lewej stronie
2. Wybierz **tworzenie nowych intencji**
3. Nazwa nowego intencji `MoveAssetsOrPeople`
4. Dodawanie wypowiedzi przykładu:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Zrzut ekranu przedstawiający wypowiedź przykład dla MoveAssetsOrPeople intencji](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    Przykład wypowiedzi ma na celu wystarczająco dużo przykłady. Jeśli później w teście nie zostanie wykryte jednostki lokalizacji i w związku z tym wzorcu nie zostanie wykryte, wróć do tego kroku, a następnie dodaj więcej przykładów. Następnie szkolenie i ponownie przetestuj. 

5. Oznacz jednostek w przykładzie wypowiedzi z jednostką pracowników, wybierając imię następnie nazwisko w wypowiedź, a następnie wybierając jednostki pracowników na liście.

    ![Zrzut ekranu przedstawiający wypowiedzi w MoveAssetsOrPeople oznaczone jednostki pracownika](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Zaznacz tekst `portland` w wypowiedź `move travis hinton from portland to orlando`. W oknie podręcznym wprowadź nową nazwę jednostki `Location`i wybierz **Utwórz nową jednostkę**. Wybierz **proste** typu jednostki, a następnie wybierz **gotowe**.

    ![Zrzut ekranu przedstawiający tworzenie nowej jednostki lokalizacji](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Oznacz pozostałą część nazwy lokalizacji, w wypowiedzi. 

    ![Zrzut ekranu przedstawiający wszystkie jednostki oznaczone](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    Wzorzec word wybór i kolejność jest oczywiste na poprzedniej ilustracji. Gdyby **nie** przy użyciu wzorców i wypowiedzi na celu oczywiste wzorzec, jest dobrym wskaźnikiem wzorców powinien być używany. 

    Jeśli oczekujesz, szerokiej gamy wypowiedzi zamiast wzorzec będą to wypowiedzi przykład problem. W takim przypadku należy różnorodny wypowiedzi w termin lub wybór programu word, długość wypowiedź i umieszczania jednostki. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Dodaj rolę do lokalizacji jednostki 
Role można używać tylko pod kątem wzorców. Dodawanie ról początkowego i docelowego do jednostki lokalizacji. 

1. Wybierz **jednostek** w nawigacji po lewej stronie, a następnie **lokalizacji** z listy jednostek.

2. Dodaj `Origin` i `Destination` ról do jednostki.

    ![Zrzut ekranu przedstawiający nowej jednostki przy użyciu ról](./media/luis-tutorial-pattern/location-entity.png)

    Role nie są oznaczone na stronie intencji MoveAssetsOrPeople, ponieważ ról nie istnieje na wypowiedzi intencji. Istnieją one tylko na wypowiedzi szablonu wzorca. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Dodawanie wypowiedzi szablonu, używanych ról lokalizacji i miejsce docelowe
Dodawanie wypowiedzi szablonu, które używają nowej jednostki.

1. Wybierz **wzorców** w lewym obszarze nawigacji.

2. Wybierz **MoveAssetsOrPeople** intencji.

3. Wprowadź nowy wypowiedź szablonu przy użyciu nowej jednostki `Move {Employee} from {Location:Origin} to {Location:Destination}`. Składnia dla jednostki i roli wewnątrz wypowiedź szablonu jest `{entity:role}`.

    ![Zrzut ekranu przedstawiający nowej jednostki przy użyciu ról](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Uczenie aplikacji dla nowych intencji, jednostki i wzorzec.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Testowanie nowego wzorca do wyodrębnienia danych roli
Sprawdzanie poprawności nowego wzorca z testem.

1. Wybierz **testu** w górnym panelu. 
2. Wprowadź wypowiedź `Move Tammi Carlson from Bellingham to Winthrop`.
3. Wybierz **Sprawdź** w obszarze wynik, aby zobaczyć wyniki testu jednostki i przeznaczenie.

    ![Zrzut ekranu przedstawiający nowej jednostki przy użyciu ról](./media/luis-tutorial-pattern/test-with-roles.png)

    Jednostki znajdują się najpierw, a następnie zostanie odnaleziony wzorzec, wskazując intencji. Jeśli wynik testu, w którym obiekty nie są wykrywane, a w związku z tym nie zostanie odnaleziony wzorzec, należy dodać więcej wypowiedzi przykład na intencji (nie wzorzec). 

4. Zamknij panel testu, wybierając **Test** przycisku w górnym menu nawigacyjnym.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Użyj jednostki Pattern.any, aby znaleźć jednostki dowolnych we wzorcu
Ta aplikacja kadry pomaga również pracownikom Znajdź formy firmy. Wiele form ma tytułów, które są różnej długości. Różnej długości obejmuje fraz, które mogą mylić LUIS, o którym kończy się nazwa formularza. Za pomocą **Pattern.any** jednostek w wzorzec pozwala określić początek i koniec nazwa formularza tak LUIS poprawnie wyodrębnia nazwę formularza. 

### <a name="create-a-new-intent-for-the-form"></a>Utwórz nowy opcję dla formularza
Utwórz nowy przeznaczenie wypowiedzi szukasz formularzy.

1. Wybierz **intencji** nawigacji po lewej stronie.

2. Wybierz pozycję**Create new intent** (Utwórz nową intencję).

3. Nazwa nowego intencji `FindForm`.

4. Dodaj wypowiedź przykładu.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Zrzut ekranu przedstawiający nowej jednostki przy użyciu ról](./media/luis-tutorial-pattern/intent-findform.png)

    Tytuł formularza jest `What to do when a fire breaks out in the Lab`. Wypowiedź pyta o lokalizację w postaci, a także prosi kogo należy podpisać go sprawdzania poprawności pracownik go odczytać. Bez jednostka Pattern.any będzie trudne do zrozumienia, których tytuł formularza kończy i Wyodrębnij tytuł formularza jako jednostka wypowiedź.

### <a name="create-a-patternany-entity-for-the-form-title"></a>Tworzenie jednostki Pattern.any tytuł formularza
Jednostka Pattern.any umożliwia dla jednostek o różnej długości. Działa tylko we wzorcu ponieważ wzorca oznacza początek i koniec jednostki. Jeśli zauważysz, że Twoje wzorzec, gdy zawiera on Pattern.any, wyodrębnia jednostek, użyj [jawną listę](luis-concept-patterns.md#explicit-lists) Aby rozwiązać ten problem. 

1. Wybierz **jednostek** w nawigacji po lewej stronie.

2. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę). 

3. Nazwa jednostki `FormName` z typem **Pattern.any**. W tym samouczku określonych jest konieczne dodanie wszelkich ról do jednostki.

    ![Obraz okna dialogowego nazwy jednostek i typ jednostki](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Dodawanie wzorca, który używa Pattern.any

1. Wybierz **wzorców** w lewym obszarze nawigacji.

2. Wybierz **FindForm** intencji.

3. Wprowadź wypowiedź szablonu, za pomocą nowej jednostki `Where is the form {FormName} and who needs to sign it after I read it?`

    ![Zrzut ekranu przedstawiający wypowiedź szablonu pattern.any jednostki](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Uczenie aplikacji dla nowych intencji, jednostki i wzorzec.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testowanie nowego wzorca do wyodrębnienia dowolnych danych
1. Wybierz **Test** z paskiem górnym, aby otworzyć panel testu. 

2. Wprowadź wypowiedź `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Wybierz **Sprawdź** w obszarze wynik, aby zobaczyć wyniki testu jednostki i przeznaczenie.

    ![Zrzut ekranu przedstawiający wypowiedź szablonu pattern.any jednostki](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    Jednostki znajduje się najpierw, a następnie zostanie odnaleziony wzorzec, wskazując intencji. Jeśli wynik testu, w którym obiekty nie są wykrywane, a w związku z tym nie zostanie odnaleziony wzorzec, należy dodać więcej wypowiedzi przykład na intencji (nie wzorzec).

4. Zamknij panel testu, wybierając **Test** przycisku w górnym menu nawigacyjnym.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz przycisk wielokropka (***...*** ) po prawej stronie nazwy aplikacji, na liście aplikacji wybierz **Usuń**. W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poznaj najlepsze rozwiązania dotyczące aplikacji usługi LUIS](luis-concept-best-practices.md)
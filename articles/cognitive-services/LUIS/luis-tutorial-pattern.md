---
title: Samouczek przy użyciu wzorców zwiększające prognoz LUIS - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: W tym samouczku za pomocą wzorca dla intencje zwiększyć LUIS prognoz zamiar i jednostek.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: d2d680a1e2749c6e0a5b17876915c5c0d86eb420
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356091"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Samouczek: Użycie wzorców w celu zwiększenia prognoz

W tym samouczku użyć wzorców zwiększające prognozowania zamiar i jednostek.  

> [!div class="checklist"]
* Jak zidentyfikować, że wzorzec może pomóc w aplikacji
* Jak utworzyć wzorca 
* Jak używać wbudowane i niestandardowe jednostki w układzie 
* Jak sprawdzić ulepszenia prognozowania wzorca
* Jak dodać roli do jednostki, aby znaleźć kontekstowej na podstawie jednostek
* Jak dodać Pattern.any można znaleźć jednostki w dowolnych

W tym artykule należy bezpłatny [LUIS] [ LUIS] konta, aby można było tworzyć LUIS aplikacji.

## <a name="import-humanresources-app"></a>Importuj kadry aplikacji
W tym samouczku importuje aplikacji brzmi HumanResources. Aplikacja ma trzy opcje: Brak, GetEmployeeOrgChart, GetEmployeeBenefits. Aplikacja ma dwie jednostki: numer wbudowane i pracownika. Jednostka pracownika jest proste jednostki można wyodrębnić nazwy pracownika. 

1. Utwórz nowy plik LUIS aplikacji i nadaj mu nazwę `HumanResources.json`. 

2. Skopiuj następujący definicji aplikacji w pliku:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. Na LUIS **aplikacje** wybierz pozycję **importu nowej aplikacji**. 

4. W **importu nowej aplikacji** okno dialogowe, wybierz opcję `HumanResources.json` pliku utworzonego w kroku 1.

5. Wybierz **GetEmployeeOrgChart** opcje, a następnie zmień z **widoku jednostek** do **tokeny widoku**. Kilka zniesławiających przykładzie są wyświetlane. Każdy utterance zawiera nazwę, która jest jednostką pracownika. Zwróć uwagę, że każda nazwa jest inna i czy układ treść jest różne dla każdego utterance. Ta różnorodność pomaga LUIS szeroką gamę zniesławiających informacje.

    ![Zrzut ekranu zamiar strony z widokiem jednostek przełączane](media/luis-tutorial-pattern/utterances-token-view.png)

6. Wybierz **uczenia** w górnym pasku nawigacyjnym w celu przeszkolenia aplikacji. Poczekaj na pasku zielony Powodzenie.

7. Wybierz **testu** w górnym panelu. Wprowadź `Who does Patti Owens report to?` , a następnie wybierz opcję Wprowadź. Wybierz **inspekcję** w obszarze utterance, aby uzyskać więcej informacji na temat narzędzia test.
    
    W przykładzie utterance jeszcze nie użyto nazwy pracownika, Patti Owens. To jest test, aby zobaczyć, jak LUIS rozpoznane dotyczy to utterance `GetEmployeeOrgChart` zamiar i jednostki pracownika powinny być `Patti Owens`. Wynik powinien być niższy niż 50% (. 50) dla `GetEmployeeOrgChart` celem. Celem jest poprawny, wynik jest niski. Jednostka pracownika jest również poprawnie zidentyfikowane jako `Patti Owens`. Wzorce zwiększyć ten wynik początkowej prognozowania. 

    ![Zrzut ekranu testu panelu](media/luis-tutorial-pattern/original-test.png)

8. Zamknij panel testu wybierając **Test** przycisk w górnym menu nawigacyjnym. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Wzorce uczy LUIS wspólnej zniesławiających wraz z przykładami mniej
Z powodu charakter domeny zasobów ludzkich istnieją na kilka sposobów wspólnego z pytaniem o relacjach pracowników w organizacji. Na przykład:

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Te zniesławiających są zbyt Zamknij, aby określić kontekstowe unikatowości każdego bez podawania wiele przykładów utterance. Dodając wzorzec celem LUIS uzyskuje informacje o typowych wzorców utterance dla celem bez podawania wiele przykładów utterance. 

Przykład zniesławiających szablonu dla tej konwersji include:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Wzorzec jest kombinacją Dopasowywanie wyrażeń regularnych i uczenia maszynowego. Następnie podaj niektórych szablon utterance przykłady LUIS dowiedzieć się, ze wzorcem. Te przykłady, wraz z konwersji zniesławiających, dają LUIS lepiej zrozumieć, z jakiego zniesławiających dopasowania zamiar i w przypadku gdy w ramach utterance, jednostka istnieje. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>Dodaj zniesławiających szablonu

1. W lewym obszarze nawigacji w obszarze **poprawić wydajność aplikacji**, wybierz pozycję **wzorce** z lewym pasku nawigacyjnym.

2. Wybierz **GetEmployeeOrgChart** zamiar, wprowadź następujące zniesławiających szablonu, pojedynczo, wybranie enter po każdym utterance szablonu:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    `{Employee}` Składni oznacza lokalizacji jednostki w utterance szablonu jako jest również jako jakiej encji. 

    ![Zrzut ekranu przedstawiający wprowadzanie zniesławiających szablonu dla zamiar](./media/luis-tutorial-pattern/enter-pattern.png)

3. Wybierz **pociągu** w górnym pasku nawigacyjnym. Poczekaj na pasku zielony Powodzenie.

4. Wybierz **testu** w górnym panelu. Wprowadź `Who does Patti Owens report to?` w polu tekstowym. Wybierz opcję Wprowadź. Jest to sam utterance testowane w poprzedniej sekcji. Wynik powinien być wyższą, aby `GetEmployeeOrgChart` celem. 

    Wynik jest teraz znacznie poprawia. LUIS przedstawiono istotne zamiar wzorzec bez podawania wiele przykładów.

    ![Zrzut ekranu testu panel z najlepszy wynik wyniku](./media/luis-tutorial-pattern/high-score.png)

    Jednostka znajduje się najpierw, a następnie zostanie odnaleziony wzorzec, wskazując celem. Jeśli wynik testu, w którym nie wykryto jednostki, i dlatego nie zostanie odnaleziony wzorzec, należy dodać więcej zniesławiających przykład na zamiar (nie wzorzec). 

5. Zamknij panel testu wybierając **Test** przycisk w górnym menu nawigacyjnym.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Użyj jednostki z rolą we wzorcu
Aplikacja LUIS służy do przenoszenia pracowników z jednej lokalizacji do innej. Przykład utterance jest `Move Bob Jones from Seattle to Los Colinas`. Każda lokalizacja w utterance ma inne znaczenie. Seattle to miejsce pochodzenia i Los Colinas jest lokalizacja docelowa do przeprowadzenia migracji. W celu odróżnienia tych lokalizacjach we wzorcu w poniższych sekcjach tworzenia prostego jednostki dla lokalizacji z dwie role: źródło i miejsce docelowe. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Utwórz nowe opcje przenoszenia osoby i zasoby
Utwórz nowe opcje dla dowolnego zniesławiających będących dotyczące przenoszenia osób lub zasobów.

1. Wybierz **intencje** z nawigacji po lewej stronie
2. Wybierz **utworzyć nowe opcje**
3. Nazwa nowej celem `MoveAssetsOrPeople`
4. Dodaj zniesławiających przykładzie:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Zrzut ekranu przedstawiający przykład utterance dla zamiar MoveAssetsOrPeople](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    Przykład zniesławiających ma na celu znajdują się przykłady wystarczającej liczby. Jeśli później w teście, jednostki lokalizacja nie zostanie wykryte, i w związku z tym wzorcu nie zostanie wykryte, wróć do tego kroku, a następnie dodaj więcej przykładów. Następnie nauczenia i ponownie przetestuj. 

5. Oznacz jednostek w przykładzie zniesławiających z jednostką pracowników przez wybranie imię następnie nazwisko w utterance, a następnie wybierając jednostkę pracowników z listy.

    ![Zrzut ekranu przedstawiający zniesławiających w MoveAssetsOrPeople oznaczonej jako jednostki pracownika](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Zaznacz tekst `portland` w utterance `move travis hinton from portland to orlando`. W oknie podręcznym wprowadź nową nazwę jednostki `Location`i wybierz **utworzyć nową jednostkę**. Wybierz **proste** typu jednostki i wybierz **gotowe**.

    ![Zrzut ekranu przedstawiający tworzenie nowej jednostki lokalizacji](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Oznacz pozostałej części nazwy lokalizacji zniesławiających. 

    ![Zrzut ekranu przedstawiający wszystkich jednostek oznaczone](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    Wzorzec word wybór i kolejność jest widoczne na poprzedniej ilustracji. Gdyby **nie** przy użyciu wzorców i zniesławiających na zamiar oczywiste wzorcem, wskazuje wzorce powinien być używany. 

    Jeśli oczekujesz szerokiej gamy zniesławiających, zamiast wzorzec będą zniesławiających przykład niewłaściwy. W takim przypadku odpowiedni będzie powszechnie zróżnicowanie zniesławiających termin lub wybór word, długość utterance i umieszczania jednostki. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Dodaj rolę lokalizacji jednostki 
Ról można używać tylko dla wzorców. Dodawanie ról pochodzenia i docelowego do jednostki lokalizacji. 

1. Wybierz **jednostek** na lewym pasku nawigacyjnym, następnie **lokalizacji** na liście jednostek.

2. Dodaj `Origin` i `Destination` ról do jednostki.

    ![Zrzut ekranu przedstawiający nowy obiekt z rolami](./media/luis-tutorial-pattern/location-entity.png)

    Role nie są oznaczone na stronie MoveAssetsOrPeople konwersji, ponieważ role nie istnieje na zniesławiających konwersji. Istnieją one tylko na wzorzec zniesławiających szablonu. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Dodaj szablon zniesławiających, używane role lokalizacji i docelowego
Dodaj szablon zniesławiających, które używają nowej jednostki.

1. Wybierz **wzorce** z lewym pasku nawigacyjnym.

2. Wybierz **MoveAssetsOrPeople** celem.

3. Wprowadź nowy utterance szablonu, za pomocą nowego obiektu `Move {Employee} from {Location:Origin} to {Location:Destination}`. Składnia dla jednostki i roli wewnątrz utterance szablonu jest `{entity:role}`.

    ![Zrzut ekranu przedstawiający nowy obiekt z rolami](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Szkolenie w aplikacji dla nowej zamiar, jednostki i wzorzec.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Testowanie nowy wzorzec do wyodrębniania danych roli
Sprawdź poprawność nowy wzorzec z testem.

1. Wybierz **testu** w górnym panelu. 
2. Wprowadź utterance `Move Tammi Carlson from Bellingham to Winthrop`.
3. Wybierz **inspekcję** poniżej wyniku, aby wyświetlić wyniki testu dla jednostki i celem.

    ![Zrzut ekranu przedstawiający nowy obiekt z rolami](./media/luis-tutorial-pattern/test-with-roles.png)

    Jednostek znajdują się najpierw, a następnie zostanie odnaleziony wzorzec, wskazując celem. Jeśli wynik testu, w którym jednostek nie są wykrywane i dlatego nie zostanie odnaleziony wzorzec, należy dodać więcej zniesławiających przykład na zamiar (nie wzorzec). 

4. Zamknij panel testu wybierając **Test** przycisk w górnym menu nawigacyjnym.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Użyj jednostki Pattern.any można znaleźć jednostki w dowolnych we wzorcu
Ta aplikacja kadry pomaga również pracownikom Znajdź formy firmy. Wiele form ma tytułów, które są różne długości. Różne długości wlicza wyrażeń, które może mylić LUIS o którym kończy się nazwa formularza. Przy użyciu **Pattern.any** jednostki w układzie pozwala na określenie początku i na końcu nazwy formularza tak LUIS poprawnie wyodrębnia nazwa formularza. 

### <a name="create-a-new-intent-for-the-form"></a>Utwórz nowe opcje dla formularza
Utwórz nowe opcje zniesławiających, których szukasz formularzy.

1. Wybierz **intencje** z nawigacji po lewej stronie.

2. Wybierz **utworzyć nowe opcje**.

3. Nazwa nowej zamiar `FindForm`.

4. Dodaj utterance przykład.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Zrzut ekranu przedstawiający nowy obiekt z rolami](./media/luis-tutorial-pattern/intent-findform.png)

    Tytuł formularza jest `What to do when a fire breaks out in the Lab`. Utterance pyta o lokalizację w postaci, a także prosi kogo należy podpisać ją sprawdzanie poprawności pracownika go odczytać. Bez podmiot Pattern.any będzie trudne do zrozumienia, gdzie tytuł formularza kończy się i wyodrębniać tytuł formularza jako jednostka utterance.

### <a name="create-a-patternany-entity-for-the-form-title"></a>Utwórz jednostkę Pattern.any tytułu formularza
Jednostka Pattern.any umożliwia dla jednostek o różnej długości. Tylko działa we wzorcu ponieważ wzorzec oznacza początek i koniec jednostki. Jeśli stwierdzisz, że deseniu, gdy zawiera on Pattern.any, jednostek wyodrębnia nieprawidłowo, użyj [jawną listę](luis-concept-patterns.md#explicit-lists) Aby rozwiązać ten problem. 

1. Wybierz **jednostek** na lewym pasku nawigacyjnym.

2. Wybierz **utworzyć nową jednostkę**. 

3. Nazwa jednostki `FormName` z typem **Pattern.any**. Do określonych celów tego samouczka nie trzeba dodawać żadnych ról z jednostką.

    ![Obraz okna dialogowego nazwy jednostki i typu jednostki](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Dodaj wzorzec, który używa Pattern.any

1. Wybierz **wzorce** z lewym pasku nawigacyjnym.

2. Wybierz **FindForm** celem.

3. Wprowadź utterance szablonu, za pomocą nowego obiektu `Where is the form {FormName} and who needs to sign it after I read it?`

    ![Zrzut ekranu przedstawiający utterance szablonu przy użyciu pattern.any jednostki](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Szkolenie w aplikacji dla nowej zamiar, jednostki i wzorzec.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testowanie nowy wzorzec do wyodrębnienia dowolnych danych
1. Wybierz **Test** z górnym pasku, aby otworzyć panel testu. 

2. Wprowadź utterance `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Wybierz **inspekcję** poniżej wyniku, aby wyświetlić wyniki testu dla jednostki i celem.

    ![Zrzut ekranu przedstawiający utterance szablonu przy użyciu pattern.any jednostki](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    Jednostka znajduje się najpierw, a następnie zostanie odnaleziony wzorzec, wskazując celem. Jeśli wynik testu, w którym jednostek nie są wykrywane i dlatego nie zostanie odnaleziony wzorzec, należy dodać więcej zniesławiających przykład na zamiar (nie wzorzec).

4. Zamknij panel testu wybierając **Test** przycisk w górnym menu nawigacyjnym.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie są już potrzebne, usunięcie aplikacji LUIS. Aby to zrobić, należy wybrać menu trzy kropki (...), z prawej strony nazwy aplikacji na liście aplikacji zaznacz **usunąć**. W wyskakującym oknie dialogowym **aplikacji Usuń?**, wybierz pozycję **Ok**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Użyj frazy listy w celu poprawienia prognozowania](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: luis-reference-regions.md
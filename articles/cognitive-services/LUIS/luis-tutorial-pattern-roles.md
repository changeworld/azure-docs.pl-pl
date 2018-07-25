---
title: Samouczek przy użyciu ról wzorca usprawniających prognoz usługi LUIS — Azure | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: W tym samouczku należy użyć ról wzorca kontekstowe z powiązanymi obiektami, aby zwiększyć prognoz usługi LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: 80f2d482a660992011549fe8dc76a09740da6ab1
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237829"
---
# <a name="tutorial-improve-app-with-pattern-roles"></a>Samouczek: Ulepszaniu aplikacji przy użyciu ról wzorca

W tym samouczku Użyj prostego jednostki przy użyciu ról w połączeniu z wzorców, aby zwiększyć prognozowania intencji i jednostek.  Korzystając z wzorców, potrzebne są mniej wypowiedzi przykład intencji.

> [!div class="checklist"]
* Omówienie ról wzorca
* Proste jednostki za pomocą ról 
* Tworzenie wzorca dla wypowiedzi przy użyciu prostego jednostki przy użyciu ról
* Jak zweryfikować ulepszenia prognozowania wzorzec

Na potrzeby tego artykułu jest wymagane bezpłatne konto usługi [LUIS](luis-reference-regions.md), które umożliwia utworzenie aplikacji usługi LUIS.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz zarządzania zasobami ludzkimi firmy [wzorzec](luis-tutorial-pattern.md) samouczek, [zaimportować](luis-how-to-start-new-app.md#import-new-app) dane JSON do nowej aplikacji w [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web. Aplikację do zaimportowania znajduje się w [przykłady LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-patterns-HumanResources-v2.json) repozytorium GitHub.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `roles`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

## <a name="the-purpose-of-roles"></a>Celem ról
Role ma na celu wyodrębnienia kontekstowe powiązanych jednostek w wypowiedź. W polu wypowiedź `Move new employee Robert Williams from Sacramento and San Francisco`, Miasto źródła i miasta docelowego wartości są ze sobą powiązane i używać języka wspólnego do oznaczania każdej lokalizacji. 

Korzystając z wzorców, można wykryć żadnych jednostek w wzorzec _przed_ wypowiedź pasuje do wzorca. 

Podczas tworzenia wzorca pierwszym krokiem jest wybranie przeznaczenie wzorzec. Wybierając intencji, jeśli pasuje do wzorca, poprawny celem jest zawsze zwracany za pomocą Wysoka ocena (zazwyczaj 99 100%). 

### <a name="compare-hierarchical-entity-to-simple-entity-with-roles"></a>Porównaj hierarchiczne jednostki do prostych jednostki przy użyciu ról

W [hierarchiczne samouczek](luis-quickstart-intent-and-hier-entity.md), **MoveEmployee** intencji wykryte, gdy przenieść istniejące pracownika z jednym budynku i pakietu office. Wyrażenia o przykład obejmowała początkowe i docelowe lokalizacje, ale nie użyto ról. Zamiast tego początkowego i docelowego zostały dzieci hierarchiczne jednostki. 

W tym samouczku aplikacja zarządzania zasobami ludzkimi wykrywa wypowiedzi informacje o przenoszeniu nowi pracownicy z jedno z miast na inny. Te dwa rodzaje wypowiedzi są podobne, ale zostaną rozwiązane za pomocą różnych możliwości usługi LUIS.

|Samouczek|Przykład wypowiedź|Początkowe i docelowe lokalizacje|
|--|--|--|
|[Hierarchiczne (nie role)](luis-quickstart-intent-and-hier-entity.md)|mV Jill Jones z **a-2349** do **b-1298**|a-2349 b-1298|
|Ten samouczek (przy użyciu ról)|Przenieś Billy Patterson z **Yuma** do **Denver**.|Yuma, Denver|

Nie można użyć hierarchicznej jednostki we wzorcu, ponieważ tylko hierarchiczne nadrzędnych są używane w elementy nadrzędne. Aby można było powrócić nazwane lokalizacje źródła i przeznaczenia, muse Użyj wzorca.

### <a name="simple-entity-for-new-employee-name"></a>Proste jednostki dla nowej nazwy pracownika
Nazwa nowego pracownika, Billy Patterson nie jest częścią obiektami listy **pracowników** jeszcze. Nowa nazwa pracowników jest wyodrębniany najpierw wysłać nazwę do systemu zewnętrznego do tworzenia poświadczeń firmowych. Po utworzeniu poświadczeń firmowych poświadczeń pracowników są dodawane do listy jednostki **pracowników**.

**Pracowników** lista została utworzona w [samouczek](luis-quickstart-intent-and-list-entity.md).

**Nowy_pracownik** jednostka jest jednostką proste bez ról. 

### <a name="simple-entity-with-roles-for-relocation-cities"></a>Proste jednostki przy użyciu ról dla miast relokacji
Nowych pracowników i rodziny muszą zostać przeniesiona z bieżącego miasta, Miasto, w którym znajduje się fikcyjnej firmy. Ponieważ nowych pracownika mogą pochodzić z dowolnego miasta, lokalizacje konieczne ich odnalezienie. Listy zestawu, na przykład obiektami listy nie będzie działać, ponieważ będzie można wyodrębnić tylko miasta na liście.

Nazwy ról, skojarzone z miast początkowe i docelowe muszą być unikatowa wśród wszystkich jednostek. Prosty sposób, aby upewnić się, że role są unikatowe, jest aby powiązać je względem jednostki zawartości za pośrednictwem strategia nazewnictwa. **NewEmployeeRelocation** jednostka jest jednostką proste przy użyciu dwóch ról: **NewEmployeeReloOrigin** i **NewEmployeeReloDestination**.

### <a name="simple-entities-need-enough-examples-to-be-detected"></a>Proste jednostki muszą wystarczająco dużo przykłady, aby zostało wykryte
Ponieważ wypowiedź przykład `Move new employee Robert Williams from Sacramento and San Francisco` obejmuje tylko maszyny do opanowania jednostek, jest zapewnienie wystarczającej ilości wypowiedzi przykład do intencji, dlatego jednostki są wykrywane.  

**Gdy wzorce umożliwiają mniej wypowiedzi przykład, jeśli obiekty nie są wykrywane, wzorzec jest niezgodny.**

Jeśli masz trudności z wykrywaniem proste jednostki, ponieważ jest to nazwa miasta, Rozważ dodanie frazy listę wartości podobne. Ułatwia to wykrywanie nazwę miejscowości, zapewniając LUIS sygnał dodatkowe o danym typie wyrazu lub frazy. Wyświetla frazy tylko pomoc wzorzec, co ułatwia wykrycie jednostki, co jest niezbędne do wzorzec do dopasowania. 

## <a name="create-new-entities"></a>Tworzenie nowych jednostek
1. Wybierz **kompilacji** w górnym menu.

2. Wybierz **jednostek** w lewym obszarze nawigacji. 

3. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę).

4. W oknie podręcznym wprowadź `NewEmployee` jako **proste** jednostki.

5. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę).

6. W oknie podręcznym wprowadź `NewEmployeeRelocation` jako **proste** jednostki.

7. Wybierz **NewEmployeeRelocation** z listy jednostek. 

8. Wprowadź pierwszą rolę `NewEmployeeReloOrigin` i wprowadź select.

9. Wprowadź druga rola jako `NewEmployeeReloDestination` i wprowadź select.

## <a name="create-new-intent"></a>Utwórz nowy opcję
Etykietowanie jednostek w ramach tej procedury może być łatwiejsze, jeśli keyPhrase wstępnie utworzone jednostki zostanie usunięty przed rozpoczęciem dodawane po wykonaniu czynności opisane w tej sekcji. 

1. Wybierz **intencji** w lewym obszarze nawigacji.

2. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

3. Wprowadź `NewEmployeeRelocationProcess` jako nazwę metody konwersji w podręcznym oknie dialogowym.

4. Wprowadź następujące wypowiedzi o przykład etykietowania nowe jednostki. Wartości jednostek i rola są pogrubione. Pamiętaj przełączyć się do **widoku tokenów** Jeśli możesz łatwiej tekstu etykiety. 

    Podczas etykietowania w zamiar nie określisz rolę jednostki. Można to zrobić później podczas tworzenia wzorca. 

    |Wypowiedź|Nowy_pracownik|NewEmployeeRelocation|
    |--|--|--|
    |Przenieś **Bob Jones** z **Seattle** do **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Przenieś **Dave C. Cooper** z **Redmond** do **New York City**|Dave C. Cooper|Miejscowości Redmond w stanie Nowy Jork|
    |Przenieś **Jan Paul Smith** z **Toronto** do **Vancouver zachodnie**|Jan Paul Smith|Toronto, Vancouver zachodnie|
    |Przenieś **J. Benson** z **Boston** do **Staines po Thames**|"J". Benson|Boston, Staines po Thames|
    |Przenieś **rozwiązania Travis "Trav" Hinton** z **Castelo Branco** do **Orlando**|Rozwiązania Travis "Trav" Hinton|Castelo Branco Orlando|
    |Przenieś **Trevor Nottington III** z **Aranda de Duero** do **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Przenieś **odzyskiwania po awarii. Greg Williams** z **Orlando** do **Ellicott miasta**|Dr Greg Williams|Orlando, Miasto Ellicott|
    |Przenieś **Robert "Dominika" Gregson** z **Kansas City** do **Capistrano Juan sieci San**|Robert "Dominika" Gregson|Kansas Miasto, sieć San Juan Capistrano|
    |Przenieś **Patti Owens** z **Bellevue** do **Rockford**|Patti Owens|Bellevue, Rockford|
    |Przenieś **Janet Bartlet** z **Tuscan** do **Santa Fe**|Janet Bartlet|Tuscan, Santa Fe|

    Nazwa pracowników ma szereg prefiksu, wyrazów, składni i sufiks. Jest to ważne w przypadku usługi LUIS do zrozumienia odmiany nazwy nowego pracownika. Nazwy miast również mieć wiele wyrazów i składni. To różne ważne jest, aby uczyć LUIS, jak te jednostki może występować w wypowiedź użytkownika. 
    
    Jeśli jednostki miały ten sam wyrazów i nie występują żadne inne zmiany, czy przekazujesz LUIS czy ta jednostka ma tylko tej liczby program word i nie występują żadne inne zmiany. Usługa LUIS nie będzie mogła poprawnie przewidzieć szerszy zestaw zmian, ponieważ nie został wyświetlony dowolny. 

    Jeśli usunięto jednostki keyPhrase, dodaj je do aplikacji teraz.

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Nowe opcje i wypowiedzi wymagają szkolenia. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego
Aby uzyskać przewidywania usługi LUIS w czatbocie lub innej aplikacji, należy opublikować aplikację. 

1. W górnej części witryny usługi LUIS po prawej stronie wybierz przycisk **Publish** (Publikuj). 

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-without-pattern"></a>Punkt końcowy bez wzorzec zapytania
1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Move Wayne Berry from Miami to Mount Vernon`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

    ```JSON
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

Wynik konwersji prognoz jest tylko około 50%. Jeśli Twoja aplikacja kliencka wymaga większa liczba zostanie podana, musi on zostać naprawione. Jednostki nie były albo przewidzieć.

Wzorców pomoże wynik prognozowania, jednak jednostki musi można poprawnie przewidzieć przed wypowiedź pasuje do wzorca. 

## <a name="add-a-pattern-that-uses-roles"></a>Dodawanie wzorca, który używa ról
1. Wybierz **kompilacji** w górnym menu nawigacyjnym.

2. Wybierz **wzorców** w nawigacji po lewej stronie.

3. Wybierz **NewEmployeeRelocationProcess** z **wybierz intencji** listy rozwijanej. 

4. Wprowadź następujący wzór: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Jeśli uczenie i publikowanie i zapytania punktu końcowego może być rozczarować zobaczyć, że obiekty nie zostaną znalezione, więc wzorca nie są zgodne, dlatego nie poprawić Prognozowanie. Jest to konsekwencją nie ma wystarczającej ilości wypowiedzi przykład z etykietami jednostkami. Zamiast opcji dodawania więcej przykładów, dodać listy frazy, aby rozwiązać ten problem.

## <a name="create-a-phrase-list-for-cities"></a>Utwórz listę frazy dla miast
Miast, np. imion i nazwisk się trudne, że ich dowolnej kombinacji wyrazów i znaki interpunkcyjne. Ale miast w regionie świata są znane, dlatego usługa LUIS musi frazy listę miast, aby zapoznać się z. 

1. Wybierz **listy fraz** z **lepsza wydajność aplikacji** części menu po lewej stronie. 

2. Nadaj liście nazwę `Cities` i dodaj następującą `values` listy:

    |Wartości listy fraz|
    |--|
    |Seattle|
    |San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Nie należy dodawać każdego miasta w świecie lub nawet co Miasto, w tym regionie. Usługa LUIS musi być możliwe do uogólnienia jakim mieście jest na liście. 

    Upewnij się zachować **te wartości są wymienne** wybrane. To ustawienie oznacza, że wyrazy na liście na traktowane jako synonimów. Jest to dokładnie, jak powinny być traktowane w strukturze.

    Należy pamiętać, [ostatniego](luis-quickstart-primary-and-secondary-data.md) tej serii samouczka utworzona lista frazy została również zwiększyć wykrywania jednostki proste jednostki.  

3. Uczenie i publikowanie aplikacji.

## <a name="query-endpoint-for-pattern"></a>Końcowy zapytania dla wzorca
1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Move wayne berry from miami to mount vernon`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

    ```JSON
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
}
    ```

Wynik konwersji jest teraz znacznie wyższa i nazwy ról są częścią odpowiedzi jednostki.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz przycisk wielokropka (***...*** ) po prawej stronie nazwy aplikacji, na liście aplikacji wybierz **Usuń**. W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poznaj najlepsze rozwiązania dotyczące aplikacji usługi LUIS](luis-concept-best-practices.md)
---
title: 'Samouczek 4: Dane dotyczące ról wzorca dla kontekstu'
titleSuffix: Azure Cognitive Services
description: Aby wyodrębnić dane z wypowiedź prawidłowo sformatowaną szablonu, należy użyć wzorca. Wypowiedź szablon używa prostego jednostki i role można wyodrębnić powiązanych danych, takie jak lokalizacja pochodzenia i lokalizację docelową.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 2c3705d28d6496c3d20999231de98572bc26e3be
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160251"
---
# <a name="tutorial-4-extract-contextually-related-patterns"></a>Samouczek 4: Wyodrębnianie kontekstowe powiązane wzorów

W tym samouczku należy użyć wzorca do wyodrębniania danych z wypowiedź prawidłowo sformatowaną szablonu. Wypowiedź szablon używa prostego jednostki i role można wyodrębnić powiązanych danych, takie jak lokalizacja pochodzenia i lokalizację docelową.  Korzystając z wzorców, potrzebne są mniej wypowiedzi przykład intencji.

Role ma na celu wyodrębnienia kontekstowe powiązanych jednostek w wypowiedź. W polu wypowiedź `Move new employee Robert Williams from Sacramento and San Francisco`, Miasto źródła i miasta docelowego wartości są ze sobą powiązane i używać języka wspólnego do oznaczania każdej lokalizacji. 


Nazwa nowego pracownika, Billy Patterson nie jest częścią obiektami listy **pracowników** jeszcze. Nowa nazwa pracowników jest wyodrębniany najpierw wysłać nazwę do systemu zewnętrznego do tworzenia poświadczeń firmowych. Po utworzeniu poświadczeń firmowych poświadczeń pracowników są dodawane do listy jednostki **pracowników**.

Nowych pracowników i rodziny muszą zostać przeniesiona z bieżącego miasta, Miasto, w którym znajduje się fikcyjnej firmy. Ponieważ nowych pracownika mogą pochodzić z dowolnego miasta, lokalizacje konieczne ich odnalezienie. Listy zestawu, na przykład obiektami listy nie będzie działać, ponieważ będzie można wyodrębnić tylko miasta na liście.

Nazwy ról, skojarzone z miast początkowe i docelowe muszą być unikatowa wśród wszystkich jednostek. Prosty sposób, aby upewnić się, że role są unikatowe, jest aby powiązać je względem jednostki zawartości za pośrednictwem strategia nazewnictwa. **NewEmployeeRelocation** jednostka jest jednostką proste przy użyciu dwóch ról: **NewEmployeeReloOrigin** i **NewEmployeeReloDestination**. Relokacji bazy jest mała w przypadku przenoszenia.

Ponieważ wypowiedź przykład `Move new employee Robert Williams from Sacramento and San Francisco` obejmuje tylko maszyny do opanowania jednostek, jest zapewnienie wystarczającej ilości wypowiedzi przykład do intencji, dlatego jednostki są wykrywane.  

**Gdy wzorce umożliwiają mniej wypowiedzi przykład, jeśli obiekty nie są wykrywane, wzorzec jest niezgodny.**

Jeśli masz trudności z wykrywaniem proste jednostki, ponieważ jest to nazwa miasta, Rozważ dodanie frazy listę wartości podobne. Ułatwia to wykrywanie nazwę miejscowości, zapewniając LUIS sygnał dodatkowe o danym typie wyrazu lub frazy. Wyświetla frazy tylko pomoc wzorzec, co ułatwia wykrycie jednostki, co jest niezbędne do wzorzec do dopasowania. 

**W tym samouczku dowiesz się, jak:**

> [!div class="checklist"]
> * Użyj istniejącego samouczek aplikacji
> * Tworzenie nowych jednostek
> * Utwórz nowy opcję
> * Szkolenie
> * Publikowanie
> * Pobierz intencje i podmioty z punktu końcowego
> * Tworzenie wzorca przy użyciu ról
> * Utwórz wyrażenie listę miast
> * Pobierz intencje i podmioty z punktu końcowego

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Użyj istniejącej aplikacji
Kontynuuj aplikację utworzoną w samouczku ostatni o nazwie **kadry**. 

Jeśli nie masz aplikacji kadry z poprzedniego samouczka, należy użyć następujących czynności:

1.  Pobierz i Zapisz [pliku JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json).

2. Importuj dane JSON do nowej aplikacji.

3. Z **Zarządzaj** sekcji na **wersji** kartę, klonowanie wersji i nadaj mu nazwę `roles`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używany jako część trasy adresu URL, nazwa nie może zawierać żadnych znaków, które nie są prawidłowe w adresie URL.

## <a name="create-new-entities"></a>Tworzenie nowych jednostek

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

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

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobierz intencje i podmioty z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

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

Wyodrębniono jednej z lokalizacji, ale nie w innej lokalizacji. 

Wzorców pomoże wynik prognozowania, jednak jednostki musi można poprawnie przewidzieć przed wypowiedź pasuje do wzorca. 

## <a name="pattern-with-roles"></a>Wzorzec przy użyciu ról

1. Wybierz **kompilacji** w górnym menu nawigacyjnym.

2. Wybierz **wzorców** w nawigacji po lewej stronie.

3. Wybierz **NewEmployeeRelocationProcess** z **wybierz intencji** listy rozwijanej. 

4. Wprowadź następujący wzór: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Jeśli uczenie i publikowanie i zapytania punktu końcowego może być rozczarować zobaczyć, że obiekty nie zostaną znalezione, więc wzorca nie są zgodne, dlatego nie poprawić Prognozowanie. Jest to konsekwencją nie ma wystarczającej ilości wypowiedzi przykład z etykietami jednostkami. Zamiast opcji dodawania więcej przykładów, dodać listy frazy, aby rozwiązać ten problem.

## <a name="cities-phrase-list"></a>Lista fraz miast
Miast, np. imion i nazwisk się trudne, że ich dowolnej kombinacji wyrazów i znaki interpunkcyjne. Miast, w regionie świata są znane, dlatego usługa LUIS musi frazy listę miast, aby zapoznać się z. 

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

    Nie należy dodawać każdego miasta w świecie lub nawet co Miasto, w tym regionie. Usługa LUIS musi być możliwe do uogólnienia jakim mieście jest na liście. Upewnij się zachować **te wartości są wymienne** wybrane. To ustawienie oznacza, że wyrazy na liście na traktowane jako synonimów. 

3. Uczenie i publikowanie aplikacji.

## <a name="get-intent-and-entities-from-endpoint"></a>Pobierz intencje i podmioty z punktu końcowego

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

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

## <a name="hierarchical-entities-versus-roles"></a>Hierarchiczna jednostki i role

W [hierarchiczne samouczek](luis-quickstart-intent-and-hier-entity.md), **MoveEmployee** intencji wykryte, gdy przenieść istniejące pracownika z jednym budynku i pakietu office. Wyrażenia o przykład obejmowała początkowe i docelowe lokalizacje, ale nie użyto ról. Zamiast tego początkowego i docelowego zostały dzieci hierarchiczne jednostki. 

W tym samouczku aplikacja zarządzania zasobami ludzkimi wykrywa wypowiedzi informacje o przenoszeniu nowi pracownicy z jedno z miast na inny. Te dwa rodzaje wypowiedzi są takie same, ale zostaną rozwiązane za pomocą różnych możliwości usługi LUIS.

|Samouczek|Przykład wypowiedź|Początkowe i docelowe lokalizacje|
|--|--|--|
|[Hierarchiczne (nie role)](luis-quickstart-intent-and-hier-entity.md)|mV Jill Jones z **a-2349** do **b-1298**|a-2349 b-1298|
|Ten samouczek (przy użyciu ról)|Przenieś Billy Patterson z **Yuma** do **Denver**.|Yuma, Denver|

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku dodać jednostki przy użyciu ról i przeznaczenie z wypowiedzi przykład. Pierwszy prognozowania punktu końcowego przy użyciu jednostki poprawnie przewidzieć intencji ale z oceną zaufania niski. Wykryto tylko jeden z dwoma jednostkami. Następnie samouczka dodane wzorzec, który umożliwia zwiększenie wartości nazwy miast w wypowiedzi ról jednostki i listy fraz. Drugi prognozowania punktu końcowego zwrócony wynik o wysokim poziomie pewności i znaleźć obiema rolami jednostki. 

> [!div class="nextstepaction"]
> [Poznaj najlepsze rozwiązania dotyczące aplikacji usługi LUIS](luis-concept-best-practices.md)

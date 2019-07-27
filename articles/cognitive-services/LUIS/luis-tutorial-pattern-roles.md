---
title: Role wzorców — LUIS
titleSuffix: Azure Cognitive Services
description: Wzorce wyodrębniają dane z dobrze sformatowanego szablonu wyrażenia długości. Wypowiedź szablonowa wykorzystuje prostą jednostkę i rolę, aby wyodrębnić powiązane dane, na przykład lokalizację początkową i docelową.
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7586a81eac95a2e4a08b045b3a2826132d9919f7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560031"
---
# <a name="tutorial-extract-contextually-related-patterns-using-roles"></a>Samouczek: Wyodrębnij wzorce powiązane z kontekstem przy użyciu ról

W tym samouczku należy użyć wzorca, aby wyodrębnić dane z dobrze sformatowanej wypowiedzi szablonowej. Wypowiedź szablonu używa prostej [jednostki](luis-concept-entity-types.md#simple-entity) i [ról](luis-concept-roles.md) do wyodrębniania powiązanych danych, takich jak lokalizacja pochodzenia i lokalizacja docelowa.  Gdy korzystasz z wzorców, do celów intencji potrzebnych jest mniej wypowiedzi.


**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Tworzenie nowych jednostek
> * Tworzenie nowej intencji
> * Szkolenie
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego
> * Tworzenie wzorca z rolami
> * Tworzenie listy fraz Cities (Miasta)
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-roles-in-patterns"></a>Używanie ról we wzorcach

Celem ról jest wyodrębnienie jednostek powiązanych z kontekstem w wypowiedź. W wypowiedzi `Move new employee Robert Williams from Sacramento and San Francisco` wartości miasta początkowego i docelowego są ze sobą powiązane, a każda z tych lokalizacji jest określona za pomocą wspólnego języka. 


Nazwisko nowego pracownika, Billy Patterson, nie jest jeszcze częścią jednostki listy **Employee** (Pracownik). Nazwisko nowego pracownika jest wyodrębniane w pierwszej kolejności w celu wysłania nazwiska do systemu zewnętrznego i utworzenia poświadczeń firmowych. Po utworzeniu poświadczeń firmowych poświadczenia pracowników są dodawane do listy jednostki **Employee**.

Nowy pracownik i jego rodzina muszę zostać przeniesieni z miasta, gdzie obecnie mieszkają, do miasta, w którym jest zlokalizowana fikcyjna firma. Ponieważ nowy pracownik może pochodzić z dowolnego miasta, trzeba odnaleźć lokalizacje. Lista ustawień, na przykład jednostka listy, nie jest tutaj dobrym rozwiązaniem, ponieważ wyodrębnione zostałyby tylko miasta uwzględnione na liście.

Nazwy ról skojarzonych z miastem początkowym i docelowym muszą być unikatowe dla wszystkich jednostek. Jeśli chcesz upewnić się, że role są unikatowe, warto powiązać je z jednostką zawartości, korzystając ze strategii nazywania. Jednostka **NewEmployeeRelocation** jest prostą jednostką mającą dwie role: **NewEmployeeReloOrigin** i **NewEmployeeReloDestination**. „Relo” to skrót od słowa „relocation” (relokacja).

Ponieważ przykładowa wypowiedź `Move new employee Robert Williams from Sacramento and San Francisco` obejmuje tylko jednostki korzystające z uczenia maszynowego, należy dostarczyć dostatecznie dużo wypowiedzi przykładowych do intencji, aby możliwe było wykrycie jednostek.  

**Wzorce umożliwiają dostarczenie mniejszej liczby wypowiedzi przykładowych, jednak jeśli jednostki nie zostaną wykryte, nie zostanie znalezione dopasowanie dla wzorca.**

Jeśli masz problem z wykrywaniem prostych jednostek, ponieważ określają one nazwę, na przykład nazwę miasta, rozważ dodanie listy fraz zawierających podobne wartości. Pomaga to w wykrywaniu nazwy miasta poprzez dostarczenie usłudze LUIS dodatkowego sygnału dotyczącego tego rodzaju słowa lub frazy. Listy fraz ułatwiają działanie wzorca tylko w zakresie wykrywania jednostek, co jest konieczne do uzyskania dopasowania. 

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej
Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Wykonaj następujące czynności:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `roles`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="create-new-entities"></a>Tworzenie nowych jednostek

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Wybierz pozycję **Entities** (Jednostki) w obszarze nawigacji po lewej stronie. 

3. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę).

4. W oknie podręcznym wprowadź ciąg `NewEmployee` jako jednostkę typu **Simple**.

5. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę).

6. W oknie podręcznym wprowadź ciąg `NewEmployeeRelocation` jako jednostkę typu **Simple**.

7. Wybierz pozycję **NewEmployeeRelocation** z listy jednostek. 

8. Jako pierwszą rolę wprowadź ciąg `NewEmployeeReloOrigin` i wybierz klawisz Enter.

9. Jako drugą rolę wprowadź ciąg `NewEmployeeReloDestination` i wybierz klawisz Enter.

## <a name="create-new-intent"></a>Tworzenie nowej intencji
Oznaczanie jednostek etykietami podczas wykonywania tych czynności może być łatwiejsze, jeśli przed rozpoczęciem usuniesz wstępnie utworzoną jednostkę keyPhrase, a następnie dodasz ją ponownie po wykonaniu kroków opisanych w tej sekcji. 

1. Wybierz pozycję **Intents** (Intencje) w obszarze nawigacji po lewej stronie.

2. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

3. Wprowadź ciąg `NewEmployeeRelocationProcess` jako nazwę intencji w oknie dialogowym.

4. Wprowadź następujące wypowiedzi przykładowe, oznaczając etykietami nowe jednostki. Wartości jednostki i roli są wyświetlone pogrubioną czcionką. Pamiętaj, aby przełączyć się na widok **Tokens View** (Widok tokenów), jeśli w ten sposób łatwiej jest oznaczać tekst etykietami. 

    Podczas oznaczania w intencji nie musisz określać roli jednostki. Należy zrobić to później, na etapie tworzenia wzorca. 

    |Wypowiedź|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Move **Bob Jones** from **Seattle** to **Los Colinas** (Przenieś osobę o nazwisku Bob Jones z miasta Seattle do miasta Los Colinas)|Bob Jones|Seattle, Los Colinas|
    |Move **Dave C. Cooper** from **Redmond** to **New York City** (Przenieś osobę o nazwisku Dave C. Cooper z miasta Redmond do miasta Nowy Jork)|Dave C. Cooper|Redmond, New York City|
    |Move **Jim Paul Smith** from **Toronto** to **West Vancouver** (Przenieś osobę o nazwisku Jim Paul Smith z miasta Toronto do miasta West Vancouver)|Jim Paul Smith|Toronto, West Vancouver|
    |Move **J. Benson** from **Boston** to **Staines-upon-Thames** (Przenieś osobę o nazwisku J. Benson z miasta Boston do miasta Staines-upon-Thames)|J. Benson|Boston, Staines-upon-Thames|
    |Move **Travis „Trav” Hinton** from **Castelo Branco** to **Orlando** (Przenieś osobę o nazwisku Travis „Trav” Hinton z miasta Castelo Branco do miasta Orlando)|Travis „Trav” Hinton|Castelo Branco, Orlando|
    |Move **Trevor Nottington III** from **Aranda de Duero** to **Boise** (Przenieś osobę o nazwisku Trevor Nottington III z miasta Aranda de Duero do miasta Boise)|Trevor Nottington III|Aranda de Duero, Boise|
    |Move **Dr. Greg Williams** from **Orlando** to **Ellicott City** (Przenieś osobę o nazwisku dr Greg Williams z miasta Orlando do miasta Ellicott City)|Dr. Greg Williams|Orlando, Ellicott City|
    |Move **Robert „Bobby” Gregson** from **Kansas City** to **San Juan Capistrano** (Przenieś osobę o nazwisku Robert „Bobby” Gregson z miasta Kansas City do miasta San Juan Capistrano)|Robert „Bobby” Gregson|Kansas City, San Juan Capistrano|
    |Move **Patti Owens** from **Bellevue** to **Rockford** (Przenieś osobę o nazwisku Patti Owens z miasta Bellevue do miasta Rockford)|Patti Owens|Bellevue, Rockford|
    |Move **Janet Bartlet** from **Tuscan** to **Santa Fe** (Przenieś osobę o nazwisku Janet Bartlet z miasta Tuscan do miasta Santa Fe)|Janet Bartlet|Tuscan, Santa Fe|

    Imię i nazwisko nowego pracownika to połączenie różnych prefiksów, liczby słów, składni i sufiksów. Jest to ważne w kontekście rozpoznawania odmian imienia i nazwiska nowego pracownika przez usługę LUIS. Nazwy miast również zawierają różną liczbę słów i mają różną składnię. Ta różnorodność pozwala nauczyć usługę LUIS, w jaki sposób te jednostki mogą się pojawić w wypowiedzi użytkownika. 
    
    Jeśli któraś z jednostek ma tę samą liczbę słów i nie ma innych odmian, należy nauczyć usługę LUIS, że ta jednostka ma tylko tę określoną liczbę słów i nie ma żadnych innych odmian. Usługa LUIS nie będzie w stanie prawidłowo przewidzieć szerszego zestawu odmian, bo żadne nie zostały jej przedstawione. 

    Jeśli usunięto jednostkę keyPhrase, należy teraz dodać ją do aplikacji.

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Move Wayne Berry from Miami to Mount Vernon`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

    ```json
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

Wynik przewidywania intencji wynosi tylko około 50%. Jeśli Twoja aplikacja kliencka wymaga wyższego wyniku, należy to poprawić. Jednostki również nie zostały przewidziane.

Jedna z lokalizacji została wyodrębniona, ale druga nie. 

Wzorce mogą pomóc poprawić wynik przewidywania, ale jednostki muszą być prawidłowo przewidywane, zanim wzorzec zostanie dopasowany do wypowiedzi. 

## <a name="pattern-with-roles"></a>Wzorzec z rolami

1. Wybierz opcję **Build** (Opracuj) w górnym menu nawigacyjnym.

2. Wybierz pozycję **Patterns** (Wzorce) w obszarze nawigacji po lewej stronie.

3. Wybierz ciąg **NewEmployeeRelocationProcess** z listy rozwijanej **Select an intent** (Wybierz intencję). 

4. Wprowadź następujący wzorzec: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Nawet jeśli przeszkolisz i opublikujesz punkt końcowy oraz wyślesz do niego zapytanie, może okazać się, że nie odnaleziono jednostek, a wiec wzorzec nie został dopasowany, i dlatego wynik przewidywania nie został poprawiony. Wynika to z niedostatecznej liczby wypowiedzi przykładowych z jednostkami oznaczonymi etykietami. Zamiast dodawać więcej przykładów, dodaj listę fraz, aby rozwiązać ten problem.

## <a name="cities-phrase-list"></a>Lista fraz zawierająca nazwy miast
Miasta, tak jak nazwiska, są problematyczne, ponieważ również mogą zawierać różne kombinacje słów i znaków interpunkcyjnych. Miasta w regionie i na świecie są znane, a więc usługa LUIS musi otrzymać listę fraz zawierającą nazwy miast, aby rozpocząć naukę. 

1. Wybierz pozycję **Phrase list** (Lista fraz) w sekcji **Improve app performance** (Popraw wydajność aplikacji) w menu po lewej stronie. 

2. Nadaj liście nazwę `Cities` i dodaj następujące wartości `values` do listy:

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

    Nie musisz dodawać wszystkich miast na świecie, ani nawet wszystkich miast w regionie. Na podstawie listy usługa LUIS musi nauczyć się, jak ogólnie określić, czym jest miasto. Upewnij się, że zaznaczono opcję **These values are interchangeable** (Te wartości są wymienne). To ustawienie oznacza, że słowa na liście są traktowane jako synonimy. 

3. Przeszkol i opublikuj aplikację.

## <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Move wayne berry from miami to mount vernon`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

    ```json
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

Wynik intencji jest teraz znacznie wyższy, a nazwy roli są częścią odpowiedzi jednostki.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano jednostkę z rolami i intencją w wypowiedziach przykładowych. Pierwsze przewidywanie punktu końcowego przy użyciu jednostki prawidłowo przewidziało intencję, ale współczynnik ufności był niski. Tylko jedna z dwóch jednostek została wykryta. Następnie w samouczku dodano wzorzec, który wykorzystywał role jednostek oraz listę fraz, aby poprawić wartość nazw miast w wypowiedziach. Drugie przewidywanie punktu końcowego zwróciło wynik o wyższym współczynniku ufności i odnalazło obie role jednostki. 

> [!div class="nextstepaction"]
> [Learn best practices for LUIS apps (Dowiedz się więcej na temat najlepszych rozwiązań dotyczących aplikacji usługi LUIS)](luis-concept-best-practices.md)

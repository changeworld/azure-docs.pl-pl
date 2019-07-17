---
title: Wzorce
titleSuffix: Azure Cognitive Services
description: Aby zwiększyć przewidywanie intencji i jednostek przy jednoczesnym zapewnieniu mniejszej liczby przykładowych wypowiedzi, należy użyć wzorców. Wzorzec jest dostarczany za pomocą przykładowej wypowiedzi szablonu, która zawiera składnię służącą do identyfikowania jednostki i tekstu możliwego do zignorowania.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: e559d4a3366c45bd054cbf3a235805e048de3493
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276044"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>Samouczek: dodawanie wspólnego szablonu wzorca formatów wypowiedzi

W ramach tego samouczka w celu zwiększenia przewidywania intencji i jednostek przy jednoczesnym zapewnieniu mniejszej liczby przykładowych wypowiedzi należy użyć wzorców. Wzorzec jest dostarczany za pomocą przykładowej wypowiedzi szablonu, która zawiera składnię służącą do identyfikowania jednostki i tekstu możliwego do zignorowania. Wzorzec jest kombinacją dopasowywania wyrażeń i uczenia maszynowego.  Przykładowa wypowiedź szablonu wraz z wypowiedziami intencji pozwala usłudze LUIS lepiej zrozumieć, jakie wypowiedzi pasują do intencji. 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Importowanie aplikacji przykładowej 
> * Tworzenie intencji
> * Szkolenie
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego
> * Tworzenie wzorca
> * Sprawdzanie ulepszeń przewidywania wzorców
> * Oznaczanie tekstu jako możliwego do zignorowania i zagnieżdżanie we wzorcu
> * Używanie panelu testowego do sprawdzania powodzenia wzorca

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Wykonaj następujące czynności:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `patterns`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="create-new-intents-and-their-utterances"></a>Tworzenie nowych intencji i ich wypowiedzi

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na stronie **Intents** (Intencje) wybierz pozycję **Create new intent** (Utwórz nową intencję). 

3. Wprowadź ciąg `OrgChart-Manager` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

    ![Okno podręczne Create new message (Tworzenie nowej wiadomości)](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |Who is John W. Smith the subordinate of? (Czyim podwładnym jest John W. Smith?)|
    |Who does John W. Smith report to? (Kto jest przełożonym Johna W. Smitha?)|
    |Who is John W. Smith's manager? (Kto jest menedżerem Johna W. Smitha?)|
    |Who does Jill Jones directly report to? (Kto jest bezpośrednim przełożonym Jill Jones?)|
    |Who is Jill Jones supervisor? (Kto jest przełożonym Jill Jones?)|

    [![Zrzut ekranu przedstawiający dodawanie nowych wypowiedzi do intencji w usłudze LUIS](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Zrzut ekranu przedstawiający dodawanie nowych wypowiedzi do intencji w usłudze LUIS")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Nie martw się, jeśli jednostka keyPhrase jest oznaczona etykietą w wypowiedziach intencji zamiast jednostki pracownika. Oba elementy są poprawnie przewidywane w okienku testu i w punkcie końcowym. 

5. Wybierz pozycję **Intents** (Intencje) na lewym pasku nawigacyjnym.

6. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

7. Wprowadź ciąg `OrgChart-Reports` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

8. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |Who are John W. Smith's subordinates? (Kim są podwładni Johna W. Smitha?)|
    |Who reports to John W. Smith? (Kto podlega Johnowi W. Smithowi?)|
    |Who does John W. Smith manage? (Kim zarządza John W. Smith?)|
    |Who are Jill Jones direct reports? (Kim są bezpośredni podwładni Jill Jones?)|
    |Who does Jill Jones supervise? (Kogo nadzoruje Jill Jones?)|

## <a name="caution-about-example-utterance-quantity"></a>Ostrzeżenie o liczbie przykładowych wypowiedzi

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Who is the boss of Jill Jones?`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Czy to zapytanie się powiodło? Dla tego cyklu szkolenia przebiegło pomyślnie. Wyniki dwóch najważniejszych intencji są zbliżone. Ponieważ uczenie usługi LUIS nie jest dokładnie takie samo za każdym razem, istnieje pewna zmienność i te dwa wyniki mogą się odwrócić w następnym cyklu szkoleniowym. Wynikiem jest to, że może zostać zwrócona nieprawidłowa intencja. 

Aby poprawny wynik intencji był znacznie wyższy procentowo i bardziej oddalony od następnego najwyższego wyniku, należy użyć wzorców. 

Pozostaw drugie okno przeglądarki otwarte. Będzie potrzebne w dalszej części samouczka. 

## <a name="template-utterances"></a>Wypowiedzi szablonu
Ze względu na charakter domeny zarządzania zasobami ludzkimi istnieje kilka typowych sposobów pytania o relacje pracowników w organizacji. Na przykład:

|Wypowiedzi|
|--|
|Who does Jill Jones report to? (Kto jest przełożonym Jill Jones?)|
|Who reports to Jill Jones? (Kto podlega Jill Jones?)|

Te wypowiedzi są za bardzo zbliżone, aby określić kontekstową unikatowość każdej bez podawania wielu przykładowych wypowiedzi. Dodając wzorzec do intencji, usługa LUIS uczy się typowych wzorców wypowiedzi dla intencji bez podawania wielu przykładowych wypowiedzi. 

Przykładowe wypowiedzi szablonu dla tej intencji obejmują:

|Przykładowe wypowiedzi szablonu|Znaczenie składni|
|--|--|
|Who does {Employee} report to[?] (Kto jest przełożonym {Pracownik}[?])|jednostka {Employee} wymienna, ignoruj [?]}|
|Who reports to {Employee}[?] (Kto podlega {Pracownikowi}[?])|jednostka {Employee} wymienna, ignoruj [?]}|

Składnia `{Employee}` oznacza lokalizację jednostki w ramach wypowiedzi szablonu oraz to, która to jednostka. Opcjonalna składnia `[?]` oznacza słowa lub znaki interpunkcyjne, które są opcjonalne. Usługa LUIS dopasowuje wypowiedź, ignorując opcjonalny tekst w nawiasie.

Mimo że składnia wygląda jak wyrażenia regularne, nie jest wyrażeniem regularnym. Obsługiwana jest tylko składnia w nawiasie klamrowym, `{}`, i nawiasie kwadratowym, `[]`. Mogą być one zagnieżdżone na najwyżej dwóch poziomach.

Aby wzorzec mógł zostać dopasowany do wypowiedzi, jednostki w ramach wypowiedzi muszą najpierw odpowiadać jednostkom w wypowiedzi szablonu. Jednak szablon nie pomaga w przewidywaniu jednostek, tylko intencji. 

**Wzorce umożliwiają dostarczenie mniejszej liczby wypowiedzi przykładowych, jednak jeśli jednostki nie zostaną wykryte, nie zostanie znalezione dopasowanie dla wzorca.**

## <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Dodaj wzorce intencji Menedżera schematu organizacyjnego

1. Wybierz pozycję **Build** (Kompiluj) w górnym menu.

2. Na lewym pasku nawigacyjnym w obszarze **Improve app performance** (Popraw wydajność aplikacji) wybierz opcję **Patterns** (Wzorce).

3. Wybierz intencję **OrgChart-Manager** (Schemat organizacyjny — menedżer), a następnie wprowadź następujące wypowiedzi szablonu:

    |Wypowiedzi szablonu|
    |:--|
    |Who is {Employee} the subordinate of[?] (Czyim podwładnym jest {Pracownik}[?])|
    |Who does {Employee} report to[?] (Kto jest przełożonym {Pracownik}[?])|
    |Who is {Employee}['s] manager[?] (Kto jest menedżerem {Pracownik}[?])|
    |Who does {Employee} directly report to[?] (Komu bezpośrednio podlega {Pracownik}[?])|
    |Who is {Employee}['s] supervisor[?] (Kto jest przełożonym {Pracownik}[?])|
    |Who is the boss of {Employee}[?] (Kto jest szefem {Pracownik}[?])|

    Jednostki z rolami używają składni, która zawiera nazwę roli, i są objęte [oddzielnym samouczkiem dotyczącym ról](luis-tutorial-pattern-roles.md). 

    Jeśli wpiszesz wypowiedź szablonu, usługa LUIS pomoże w wypełnieniu jednostki po wprowadzeniu lewego nawiasu klamrowego `{`.

    [![Zrzut ekranu przedstawiający wprowadzanie wypowiedzi szablonu dla intencji](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Mając nadal na stronie wzorców, wybierz **schemat organizacyjny — raporty** przeznaczenie, wprowadź następujące wypowiedzi szablonu:

    |Wypowiedzi szablonu|
    |:--|
    |Who are {Employee}['s] subordinates[?] (Kim są podwładni {Pracownika}[?])|
    |Who reports to {Employee}[?] (Kto podlega {Pracownikowi}[?])|
    |Who does {Employee} manage[?] (Kim zarządza {Pracownik}[?])|
    |Who are {Employee} direct reports[?] (Kim są bezpośredni podwładni {Pracownika}[?])|
    |Who does {Employee} supervise[?] (Kogo nadzoruje {Pracownik}[?])|
    |Who does {Employee} boss[?] (Czyim szefem jest {Pracownik}[?])|

## <a name="query-endpoint-when-patterns-are-used"></a>Punkt końcowy zapytania, gdy są używane wzorce

Teraz, wzorce są dodawane do aplikacji, szkolenie, publikowanie i zapytania aplikacji w punkcie końcowym środowiska uruchomieniowego prognozowania.

1. Przeszkol i ponownie opublikuj aplikację.

1. Zmień karty przeglądarki z powrotem na kartę adresu URL punktu końcowego.

1. Przejdź na koniec tego adresu URL i wprowadź ciąg `Who is the boss of Jill Jones?` jako wypowiedź. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Funkcja prognozowania jest teraz znacznie większą pewność.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Praca z tekstem opcjonalnym i wstępnie skompilowanymi jednostkami

Poprzednie wypowiedzi szablonu wzorca w tym samouczku miały kilka przykładów opcjonalnego tekstu, np. użycie formy dzierżawczej pod postacią litery s `'s` (dotyczy języka angielskiego) i stosowanie znaku zapytania `?`. Załóżmy, że należy zezwolić na bieżące i przyszłe daty w tekście wypowiedź.

Przykładowe wypowiedzi:

|Intencja|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Każdy z tych przykładów używa czasowników w różnych czasach: `was`, `is`, `will be`, a także daty: `March 3`, `now` i `in a month`, których usługa LUIS potrzebuje do poprawnego przewidywania. Należy zauważyć, że w ostatnich dwóch przykładach użyto prawie tego samego tekstu oprócz `in` i `on`.

Przykład wypowiedzi szablonu, które pozwalają na tych informacji opcjonalnych: 

|Intencja|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


Korzystanie z opcjonalnych składni z nawiasami kwadratowymi, `[]`, sprawia, że ten opcjonalny tekst jest łatwy do dodania do wypowiedzi szablonu i może być zagnieżdżony najwyżej do drugiego poziomu, `[[]]`, a także może zawierać tekst lub jednostki.


**Pytanie: dlaczego wszystkie wypowiedzi szablonu zaczynają się małą literą `w`? Nie powinny one być opcjonalnie duże lub małe?** Wypowiedź przesłana do punktu końcowego zapytania przez aplikację kliencką jest konwertowana na małe litery. Wypowiedź szablonu może zaczynać się dużą lub małą literą, podobnie jak wypowiedź punktu końcowego. Porównanie odbywa się zawsze po konwersji na małe litery.

**Pytanie: dlaczego wstępnie skompilowany numer nie jest częścią wypowiedzi szablonu, jeśli March 3 (3 marca) jest przewidziany zarówno jako liczba `3`, jak i data `March 3`?** Wypowiedź szablonu kontekstowo używa daty — albo dosłownie jak w wypowiedzi `March 3`, albo abstrakcyjnie jak w wypowiedzi `in a month`. Data może zawierać liczbę, ale liczba nie musi być zawsze postrzegana jako data. Należy zawsze używać jednostki najlepiej reprezentującej typ, który ma być zwracany w wynikach przewidywania JSON.  

**Pytanie: co z niepoprawnie zapisanymi wypowiedziami, takimi jak `Who will {Employee}['s] manager be on March 3?`.** Gramatycznie różne czasy czasowników, takie jak tu, gdzie `will` i `be` są rozdzielone, muszą być nową wypowiedzią szablonu. Istniejąca wypowiedź szablonu nie będzie z tym zgodna. Mimo że intencja wypowiedzi nie została zmieniona, zmieniło się umieszczenie słowa w wypowiedzi. Ta zmiana ma wpływ na przewidywanie w usłudze LUIS. Możesz [grupy i](#use-the-or-operator-and-groups) czasowników połączyć te wypowiedzi. 

**Pamiętaj: najpierw znajdowane są jednostki, a następnie dopasowywany jest wzorzec.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Edytowanie istniejącej wypowiedzi szablonu wzorca

1. W witrynie usługi LUIS wybierz pozycję **Build** (Kompiluj) w górnym menu, a następnie wybierz pozycję **Patterns** (Wzorce) w menu po lewej stronie. 

1. Wyszukiwanie istniejących wypowiedź szablonu, `Who is {Employee}['s] manager[?]`i wybierz przycisk wielokropka (***...*** ) po prawej stronie, następnie wybierz pozycję **Edytuj** z menu podręcznego. 

1. Zmień wypowiedź szablonu na: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

## <a name="add-new-pattern-template-utterances"></a>Dodawanie nowych wypowiedzi szablonu wzorca

1. W sekcji **Patterns** (Wzorce) części **Build** (Kompiluj) dodaj kilka nowych wypowiedzi szablonu wzorca. Wybierz pozycję **OrgChart-Manager** (Schemat organizacyjny — menedżer) z menu rozwijanego Intent (Intencja) i wprowadź każdą z następujących wypowiedzi szablonu:

    |Intencja|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Przeszkol aplikację.

3. Wybierz pozycję **Test** (Testuj) w górnej części panelu, aby otworzyć panel testowania. 

4. Wprowadź kilka testowych wypowiedzi, aby sprawdzić, czy wzorzec jest dopasowany i czy wynik intencji jest znacząco wysoki. 

    Po wprowadzeniu pierwszej wypowiedzi wybierz polecenie **Inspect** (Sprawdź) w obszarze wyniku, dzięki czemu można zobaczyć wszystkie wyniki przewidywania. Każdy wypowiedź powinny mieć **schemat organizacyjny — Manager** intencji i należy wyodrębnić wartości dla jednostek pracowników i datetimeV2.

    |Wypowiedź|
    |--|
    |Who will be Jill Jones manager (Kto będzie menedżerem Jill Jones)|
    |who will be jill jones's manager (kto będzie menedżerem jill jones)|
    |Who will be Jill Jones's manager? (Kto będzie menedżerem Jill Jones?)|
    |who will be Jill jones manager on March 3 (kto będzie menedżerem Jill jones 3 marca)|
    |Who will be Jill Jones manager next Month (Kto będzie menedżerem Jill Jones w następnym miesiącu)|
    |Who will be Jill Jones manager in a month? (Kto będzie menedżerem Jill Jones za miesiąc?)|

Wszystkie te wypowiedzi znalazły jednostki wewnątrz, dlatego pasują do tego samego wzorca i mają wysoki wynik przewidywania.

## <a name="use-the-or-operator-and-groups"></a>Użyj operatora OR i grup

Niektóre z poprzednim wypowiedzi szablonu są bardzo Zamknij. Użyj **grupy** `()` i **lub** `|` składni, aby zmniejszyć wypowiedzi szablonu. 

Następujące wzorce 2 można łączyć w jednym wzorca za pomocą tej grupy `()` i `|` składni.

|Intencja|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Nowe wypowiedź szablon będzie: 

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`. 

Ta metoda korzysta z **grupy** wokół czasu teraźniejszego zlecenie wymagane i opcjonalne `in` i `on` z **lub** potoku między nimi. 

1. Na **wzorców** wybierz opcję **schemat organizacyjny — Manager** filtru. Ograniczanie listy, wyszukując `manager`. 

    ![Wyszukaj termin "manager" we wzorcach intencji Menedżera schematu organizacyjnego](./media/luis-tutorial-pattern/search-patterns.png)

1. Zachowaj jedną wersję wypowiedź szablonu (w celu edycji w następnym kroku) i usuwania innych zmian. 

1. Zmień wypowiedź szablonu, aby: 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

1. Przeszkol aplikację.

1. Okienko testowania należy użyć do testowania wersji wypowiedź:

    |Wypowiedzi wprowadzić w okienko testowania|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|    
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|


## <a name="use-the-utterance-beginning-and-ending-anchors"></a>Użyj wypowiedź rozpoczęcia i zakończenia zakotwiczenia

Składnia wzorca zapewnia rozpoczęcia i zakończenia zakotwiczenia składnia wypowiedź daszek, `^`. Początkowe i końcowe kotwic wypowiedź można ze sobą wypowiedź bardzo szczegółowych i prawdopodobnie literał docelowego lub oddzielnie umożliwia intencji docelowego. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodajemy dwie intencje do wypowiedzi trudnych do przewidzenia z wysoką dokładnością bez konieczności podawania wielu przykładowych wypowiedzi. Dodanie do tego wzorców pozwoliło usłudze LUIS na lepsze przewidywanie intencji ze znacznie wyższym wynikiem. Oznaczanie jednostek i tekst możliwy do zignorowania pozwoliły usłudze LUIS na zastosowanie wzorca do szerszego zakresu wypowiedzi.

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać ról ze wzorcem](luis-tutorial-pattern-roles.md)

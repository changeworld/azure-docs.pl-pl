---
title: 'Samouczek 3: Wzorce ulepszania prognoz usługi LUIS'
titleSuffix: Azure Cognitive Services
description: Aby zwiększyć intencji i jednostek prognozowania przy jednoczesnym zapewnieniu mniej wypowiedzi przykładu, należy użyć wzorców. Wzorzec jest dostarczany za pomocą przykładu wypowiedź szablonu, który zawiera Składnia służąca do identyfikowania jednostki i tekstu można zignorować.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: ec006d8941d8832d139a09b1e8af13000ff5a43e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040707"
---
# <a name="tutorial-3-add-common-utterance-formats"></a>Samouczek 3. Dodawanie typowych formatów wypowiedzi

W ramach tego samouczka Aby zwiększyć intencji i jednostek prognozowania przy jednoczesnym zapewnieniu mniej wypowiedzi przykładu należy użyć wzorców. Wzorzec jest dostarczany za pomocą przykładu wypowiedź szablonu, który zawiera Składnia służąca do identyfikowania jednostki i tekstu można zignorować. Wzorzec jest kombinacją Dopasowywanie wyrażeń i uczenia maszynowego.  Przykład wypowiedź szablonu, wraz z intencji wypowiedzi zapewniają LUIS lepiej zrozumieć, z jakiego wypowiedzi Dopasuj intencji. 

**W tym samouczku dowiesz się, jak:**

> [!div class="checklist"]
> * Użyj istniejącego samouczek aplikacji 
> * Utwórz opcję
> * Szkolenie
> * Publikowanie
> * Pobierz intencje i podmioty z punktu końcowego
> * Tworzenie wzorca
> * Sprawdź ulepszenia prognozowania wzorzec
> * Można zignorować, tekstu i zagnieździć w ramach wzorzec
> * Użyj panelu test, aby sprawdzić, czy wzorzec

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Użyj istniejącej aplikacji

Kontynuuj aplikację utworzoną w samouczku ostatni o nazwie **kadry**. 

Jeśli nie masz aplikacji kadry z poprzedniego samouczka, należy użyć następujących czynności:

1.  Pobierz i Zapisz [pliku JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. Importuj dane JSON do nowej aplikacji.

3. Z **Zarządzaj** sekcji na **wersji** kartę, klonowanie wersji i nadaj mu nazwę `patterns`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używany jako część trasy adresu URL, nazwa nie może zawierać żadnych znaków, które nie są prawidłowe w adresie URL.

## <a name="create-new-intents-and-their-utterances"></a>Utwórz nowy intencje i ich wypowiedzi

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na stronie **Intents** (Intencje) wybierz pozycję **Create new intent** (Utwórz nową intencję). 

3. Wprowadź ciąg `OrgChart-Manager` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

    ![Utwórz nowe okno podręczne wiadomości](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |Jan Kowalski W. kto jest podwładnym?|
    |Kto Jan Kowalski W. zgłosić do?|
    |Kto jest Menedżer Jan W. Kowalski?|
    |Kto Jill Jones raporty bezpośrednio?|
    |Kto jest Jill Jones, kierownik?|

    [![Zrzut ekranu dodawania nowej wypowiedzi na intencje LUIS](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "zrzut ekranu dodawania nowej wypowiedzi na intencje LUIS")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Nie martw się, jeśli jednostka keyPhrase jest oznaczona etykietą w wypowiedzi intencji zamiast jednostki pracownika. Oba są poprawnie przewidzieć, w okienku testu i w punkcie końcowym. 

5. Wybierz pozycję **Intents** (Intencje) na lewym pasku nawigacyjnym.

6. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

7. Wprowadź ciąg `OrgChart-Reports` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

8. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |Kim są podwładnych Jan W. Kowalski?|
    |Kto raporty do Jan Kowalski W.?|
    |Kto zarządza Jan Kowalski W.|
    |Kim są Jill Jones bezpośrednich podwładnych?|
    |Kto nadzorowania Jill Jones?|

## <a name="caution-about-example-utterance-quantity"></a>Ostrzeżenie o ilości wypowiedź przykład

[!include[Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobierz intencje i podmioty z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Who is the boss of Jill Jones?`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

    ```JSON
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

To zapytanie pomyślnie? Dla tego cyklu szkolenia pomyślnie. Zamknij są wyniki dwa najważniejsze intencji. Ponieważ usługa LUIS szkolenia nie jest dokładnie taka sama każdorazowo, jest nieco odmiany, te dwa wyniki można odwrócić w następnym cyklu szkolenia. Wynik jest nieprawidłowy zamiar mogą być zwracane. 

Aby wynik poprawne intencji znacznie wyższe w postaci wartości procentowej i dalej od następnego najwyższym wynikiem, należy użyć wzorców. 

Pozostaw to drugie okno przeglądarki jest otwarte. Możesz użyć później w samouczku. 

## <a name="template-utterances"></a>Szablon wypowiedzi
Ze względu na charakter domeny zarządzania zasobami ludzkimi istnieje kilka typowych sposobów z pytaniem o relacjach pracowników w organizacji. Na przykład:

|Wypowiedzi|
|--|
|Kto Jill Jones zgłosić do?|
|Kto raporty do Jill Jones?|

Te wypowiedzi są zbyt Zamknij, aby określić kontekstowych unikatowości każdego bez podawania wiele przykładów wypowiedź. Dodając wzorzec intencji, LUIS uczy się typowe wzorce wypowiedź intencji bez podawania wiele przykładów wypowiedź. 

Przykłady wypowiedź szablonu konwersji między innymi:

|Przykłady wypowiedzi szablonów|znaczenie składni|
|--|--|
|Tego, kto wykonuje {pracowników} raportować do [?]|wymienne {pracowników}, ignorowanie [?]}|
|Kto raporty pracownikowi {} [?]|wymienne {pracowników}, ignorowanie [?]}|

`{Employee}` Składni oznacza lokalizację jednostki w ramach wypowiedź szablonu jako jest również jako jakiej encji. Opcjonalnych składni `[?]`, oznacza słowa lub znaki interpunkcyjne, które są opcjonalne. Usługa LUIS dopasowuje wypowiedź, ignorowanie opcjonalny tekst w nawiasie.

Gdy składnia wygląda wyrażeń regularnych, nie jest wyrażeń regularnych. Tylko nawias klamrowy `{}`, a nawias kwadratowy `[]`, składnia jest obsługiwana. One może być zagnieżdżona w maksymalnie dwa poziomy.

Aby wzorzec można dopasować do wypowiedź jednostek w ramach wypowiedź musi odpowiadać jednostek w wypowiedź szablon najpierw. Jednak szablonu nie ułatwiania prognozowania jednostki, tylko intencji. 

**Gdy wzorce umożliwiają mniej wypowiedzi przykład, jeśli obiekty nie są wykrywane, wzorzec jest niezgodny.**

W tym samouczku należy dodać dwa nowe intencji: `OrgChart-Manager` i `OrgChart-Reports`. 

|Intencja|Wypowiedź|
|--|--|
|Schemat organizacyjny — Manager|Kto Jill Jones zgłosić do?|
|Schemat organizacyjny — raporty|Kto raporty do Jill Jones?|

Gdy usługa LUIS zwraca prognozowania do aplikacji klienckiej intencji nazwy mogą być używane jako nazwy funkcji w aplikacji klienckiej, a jednostka pracownik może być używana jako parametr do tej funkcji.

```Javascript
OrgChartManager(employee){
    ///
}
```

Należy pamiętać, że pracownicy zostały utworzone w [jednostki samouczek](luis-quickstart-intent-and-list-entity.md).

1. Wybierz **kompilacji** w górnym menu.

2. W lewym obszarze nawigacji w obszarze **lepsza wydajność aplikacji**, wybierz opcję **wzorców** w lewym obszarze nawigacji.

3. Wybierz **schemat organizacyjny — Manager** przeznaczenie, wprowadź następujące wypowiedzi szablonu:

    |Szablon wypowiedzi|
    |:--|
    |Kto jest podwładnym [?] {pracowników}|
    |Tego, kto wykonuje {pracowników} raportować do [?]|
    |Kto jest menedżera pracownika {} [w] [?]|
    |Tego, kto wykonuje {pracowników} raporty bezpośrednio [?]|
    |Kto jest {} [w] przełożonego [?]|
    |Kto jest szefa {pracownika} [?]|

    Jednostki przy użyciu ról należy użyć składni, która zawiera nazwę roli i są objęte [oddzielne samouczek dotyczący ról](luis-tutorial-pattern-roles.md). 

    Jeśli wpiszesz wypowiedź szablonu, pomaga LUIS wypełnienie w jednostce po wprowadzeniu lewy nawias klamrowy `{`.

    [![Zrzut ekranu przedstawiający wprowadzanie wypowiedzi szablonu dla intencji](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Wybierz **schemat organizacyjny — raporty** przeznaczenie, wprowadź następujące wypowiedzi szablonu:

    |Szablon wypowiedzi|
    |:--|
    |Kim są {pracowników} [w] podwładnych [?]|
    |Kto raporty pracownikowi {} [?]|
    |Tego, kto wykonuje {pracowników} Zarządzanie [?]|
    |Kim są {pracowników} bezpośrednich podwładnych [?]|
    |Tego, kto wykonuje {pracowników} nadzorowania [?]|
    |Tego, kto wykonuje {pracowników} szefa [?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Zapytania punktu końcowego, gdy są używane wzorce

1. Nauczanie i ponownie Opublikuj aplikację.

2. Karty przeglądarki przejdź do karty adres URL punktu końcowego.

3. Przejdź na końcu adresu URL w adres, a następnie wprowadź `Who is the boss of Jill Jones?` jako wypowiedź. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

    ```JSON
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

Funkcja prognozowania jest teraz znacznie wyższa.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Praca z opcjonalnego tekstu i wstępnie utworzonych jednostek

Poprzednie wypowiedzi szablonu wzorca, w tym samouczku ma kilka przykładów opcjonalnego tekstu, takie jak użycie dzierżawczego literę s, `'s`i stosowania znaku zapytania `?`. Załóżmy, że wypowiedzi punktu końcowego pokazują, że menedżerów i przedstawiciele kadry szukasz danych historycznych także planowane przenosi pracowników w firmie, dzieje się w przyszłości.

Przykład wypowiedzi są następujące:

|Intencja|Przykład wypowiedzi za pomocą opcjonalnego tekstu i wstępnie utworzonych jednostek|
|:--|:--|
|Schemat organizacyjny — Manager|`Who was Jill Jones manager on March 3?`|
|Schemat organizacyjny — Manager|`Who is Jill Jones manager now?`|
|Schemat organizacyjny — Manager|`Who will be Jill Jones manager in a month?`|
|Schemat organizacyjny — Manager|`Who will be Jill Jones manager on March 3?`|

Każda z tych przykładów używa czasu teraźniejszego zlecenie, `was`, `is`, `will be`, a także datę, `March 3`, `now`, i `in a month`, wymagającym usługi LUIS do przewidzenia poprawnie. Należy zauważyć, że w ostatnich dwóch przykładach użyto prawie ten sam tekst z wyjątkiem `in` i `on`.

Przykład wypowiedzi szablonu:
|Intencja|Przykład wypowiedzi za pomocą opcjonalnego tekstu i wstępnie utworzonych jednostek|
|:--|:--|
|Schemat organizacyjny — Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|Schemat organizacyjny — Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|Schemat organizacyjny — Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|Schemat organizacyjny — Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Korzystanie z opcjonalnych składni z nawiasami kwadratowymi `[]`, sprawia, że ten opcjonalny tekst jest łatwe do dodania do wypowiedź szablonu i mogą być zagnieżdżane do drugiego poziomu `[[]]`i zawierają tekst lub jednostki.

**Pytanie: Dlaczego nie wypowiedzi ostatnie dwa przykładowe połączyć w wypowiedź pojedynczy szablon?** Szablon wzorca nie obsługuje składni OR. Aby przechwycić zarówno `in` wersji i `on` wersji, każdy z nich musi być wypowiedź oddzielne szablonu.

**Pytanie: Dlaczego są wszystkie `w` liter, pierwsza litera w każdym wypowiedź szablonu, małe litery? Nie powinny one być opcjonalnie wielkich lub małych?** Wypowiedź przesłane do endpoint zapytania, aplikacja kliencka jest konwertowane na małe litery. Wypowiedź szablonu może być wielkimi lub małymi literami i wypowiedź punktu końcowego może również być. Porównanie odbywa się zawsze po konwersji na małe litery.

**Pytanie: Dlaczego nie ma wbudowanych numer części szablonu wypowiedź przypadku 3 marca przewidzieć zarówno jako liczba `3` Data i godzina `March 3`?** Wypowiedź szablonu kontekstowe używa datę, albo dosłownie w `March 3` lub abstrakcyjną jako `in a month`. Wartość typu date może zawierać wiele, ale liczbą nie może być zawsze widoczne jako data. Zawsze używaj jednostki, która najlepiej reprezentuje typ, które mają być zwracane w wynikach JSON prognozy.  

**Pytanie: Jak wygląda niewłaściwie ma inną pisownię wypowiedzi takich jak `Who will {Employee}['s] manager be on March 3?`.** Gramatycznie różnych czasowników, takie jak ta gdzie `will` i `be` są rozdzielone muszą być nowe wypowiedź szablonu. Istniejące wypowiedź szablonu nie będą zgodne go. Gdy celem wypowiedź nie została zmieniona, umieszczania programu word w wypowiedź został zmieniony. Ta zmiana ma wpływ na prognozowania w usługi LUIS.

**Pamiętaj: jednostki znajdują się najpierw, a następnie odbywa się dopasowanie.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Edytuj istniejący wypowiedź szablonu wzorca

1. W witrynie usługi LUIS wybierz **kompilacji** w górnym menu wybierz **wzorców** w menu po lewej stronie. 

2. Znajdź istniejący wypowiedź szablonu, `Who is {Employee}['s] manager[?]`i wybierz przycisk wielokropka (***...*** ) po prawej stronie. 

3. Wybierz **Edytuj** z menu podręcznego. 

4. Zmień wypowiedź szablonu, aby: `who is {Employee}['s] manager [[on]{datetimeV2}?]]`

## <a name="add-new-pattern-template-utterances"></a>Dodawanie nowego wzorca szablonu wypowiedzi

1. W **wzorców** części **kompilacji**, dodaj kilka nowych wzorca wypowiedzi szablonu. Wybierz **schemat organizacyjny — Manager** z menu rozwijanego intencji i wprowadź każdego z następujących wypowiedzi szablonu:

    |Intencja|Przykład wypowiedzi za pomocą opcjonalnego tekstu i wstępnie utworzonych jednostek|
    |--|--|
    |Schemat organizacyjny — Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |Schemat organizacyjny — Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |Schemat organizacyjny — Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |Schemat organizacyjny — Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Uczenie aplikacji.

3. Wybierz **testu** w górnej części panelu, aby otworzyć panel testowania. 

4. Wprowadź kilka wypowiedzi test, aby sprawdzić, czy odbywa się dopasowanie i wynik konwersji jest znacznie wysoka. 

    Po wprowadzeniu pierwszego wypowiedź, wybierz **Sprawdź** w wyniku, dzięki czemu można zobaczyć wszystkie wyniki prognozy.

    |Wypowiedź|
    |--|
    |Dla kogo jest Jill Jones, Menedżer|
    |dla kogo jest jill jones, Menedżer|
    |Dla kogo jest Jill Jones, Menedżer?|
    |dla kogo jest Menedżer jones Jill 3 marca|
    |Dla kogo jest Jill Jones, Menedżer następnego miesiąca|
    |Dla kogo jest Jill Jones, Menedżer w ciągu miesiąca?|

Wszystkie te wypowiedzi znaleziono jednostek wewnątrz, w związku z tym one pasuje do tego samego wzorca i mają wynik wysokiej prognozy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku dodaje dwa intencji dla wypowiedzi były trudne do prognozowania o wysokiej dokładności bez konieczności wiele wypowiedzi przykład. Dodawanie wzorce te dozwolone LUIS, aby lepiej przewiduj intencji za pomocą znacznie więcej punktów. Oznaczanie jednostek i można zignorować tekst dozwolone LUIS zastosować wzorzec do szerszego zakresu wypowiedzi.

> [!div class="nextstepaction"]
> [Dowiedz się, jak użyć ról z wzorcem](luis-tutorial-pattern-roles.md)
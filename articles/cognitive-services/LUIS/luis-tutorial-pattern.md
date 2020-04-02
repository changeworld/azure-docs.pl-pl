---
title: 'Samouczek: Wzorce - LUIS'
description: Użyj wzorców, aby zwiększyć intencji i przewidywania jednostki, zapewniając mniej wypowiedzi przykład w tym samouczku. Wzorzec jest dostarczany jako przykład wypowiedź szablonu, który zawiera składnię do identyfikowania jednostek i tekstu ignorowanego.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 10f0ade45dedb3413887cc4b4dea89e857c1bde7
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545988"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Samouczek: Dodawanie typowych formatów wypowiedź szablonu wzorca w celu poprawy prognoz

W tym samouczku użyj wzorców, aby zwiększyć intencji i przewidywania jednostki, co pozwala na zapewnienie mniej wypowiedzi przykład. Wzorzec jest wypowiedź szablonu przypisany do intencji, która zawiera składnię do identyfikowania jednostek i tekstu ignorowane.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie wzorca
> * Sprawdzanie ulepszeń przewidywania wzorców
> * Oznaczanie tekstu jako możliwego do zignorowania i zagnieżdżanie we wzorcu
> * Używanie panelu testowego do sprawdzania powodzenia wzorca

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Wypowiedzi w intencji i wzorzec

Istnieją dwa typy wypowiedzi przechowywane w aplikacji usługi LUIS:

* Przykładowe wypowiedzi w intencji
* Wypowiedzi szablonu we wzorcu

Dodawanie wypowiedzi szablonu jako wzorca pozwala podać mniej wypowiedzi przykład ogólnej do intencji.

Wzorzec jest stosowany jako kombinacja dopasowywania tekstu i uczenia maszynowego.  Wypowiedź szablonu we wzorcu, wraz z wypowiedzi przykład w intencji, dać usługi LUIS lepsze zrozumienie, jakie wypowiedzi pasują do intencji.

## <a name="import-example-app-and-clone-to-new-version"></a>Importowanie przykładowej aplikacji i klonowanie do nowej wersji

Wykonaj następujące czynności:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json?raw=true).

1. Zaimportuj JSON do nowej aplikacji do [portalu usługi LUIS w wersji zapoznawczej](https://preview.luis.ai). Na stronie **Moje aplikacje** wybierz pozycję **+ Nowa aplikacja do konwersacji**, a następnie wybierz pozycję **Importuj jako JSON**. Wybierz plik pobrany w poprzednim kroku.

1. W sekcji **Zarządzanie** na karcie **Wersje** wybierz aktywną wersję, a następnie wybierz pozycję **Klonuj**. Nazwij sklonowaną wersję `patterns`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="create-new-intents-and-their-utterances"></a>Tworzenie nowych intencji i ich wypowiedzi

Dwie intencje znaleźć menedżera lub bezpośredniego menedżera raportów, na podstawie tekstu wypowiedź. Trudność polega na tym, że te dwa zamiary _oznaczają_ różne rzeczy, ale większość słów jest taka sama. Tylko kolejność słów jest inna. Aby intencja została poprawnie przewidzieć, musiałaby mieć wiele przykładów.

1. Wybierz **pozycję Zbuduj** z paska nawigacyjnego.

1. Na stronie **Intencje** wybierz pozycję **+ Utwórz,** aby utworzyć nową intencję.

1. Wprowadź ciąg `OrgChart-Manager` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

    ![Okno podręczne Create new message (Tworzenie nowej wiadomości)](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Dodaj przykładowe wypowiedzi do intencji. Te wypowiedzi nie są _dokładnie_ podobne, ale mają wzorzec, który można wyodrębnić.

    |Przykładowe wypowiedzi|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

    Nie martw się, jeśli jednostka keyPhrase jest oznaczona etykietą w wypowiedziach intencji zamiast jednostki pracownika. Oba elementy są poprawnie przewidywane w okienku testu i w punkcie końcowym.

1. Wybierz pozycję **Intents** (Intencje) na lewym pasku nawigacyjnym.

1. Wybierz **+ Utwórz,** aby utworzyć nową intencję. Wprowadź ciąg `OrgChart-Reports` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

1. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Ostrzeżenie o liczbie przykładowych wypowiedzi

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Trenowanie aplikacji przed testowaniem lub publikowaniem

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikowanie aplikacji w celu wysyłania zapytań z punktu końcowego

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu i `Who is the boss of Jill Jones?`zastąp _YOUR_QUERY_HERE:_ .

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.313054234
                },
                "OrgChart-Reports": {
                    "score": 0.2462688
                },
                "EmployeeFeedback": {
                    "score": 0.0488328524
                },
                "GetJobInformation": {
                    "score": 0.0156933
                },
                "MoveEmployee": {
                    "score": 0.011265873
                },
                "Utilities.StartOver": {
                    "score": 0.003065792
                },
                "Utilities.Stop": {
                    "score": 0.00300148362
                },
                "Utilities.Cancel": {
                    "score": 0.00271081156
                },
                "None": {
                    "score": 0.00212835032
                },
                "ApplyForJob": {
                    "score": 0.0020669254
                },
                "Utilities.Confirm": {
                    "score": 0.00200891262
                },
                "FindForm": {
                    "score": 0.00194145238
                },
                "Utilities.Help": {
                    "score": 0.00182301877
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Wyniki dwóch głównych intencji są bliskie, ale najwyższy zamiar nie jest znacząco wysoki (ponad 60%) i nie jest wystarczająco daleko powyżej następnego zamiaru wynik.

Ponieważ szkolenie usługi LUIS nie jest dokładnie taka sama za każdym razem (istnieje trochę zmienności), te dwa najlepsze wyniki można odwrócić w następnym cyklu szkolenia. Wynikiem jest to, że może zostać zwrócona nieprawidłowa intencja.

Aby poprawny wynik intencji był znacznie wyższy procentowo i bardziej oddalony od następnego najwyższego wyniku, należy użyć wzorców.

Pozostaw drugie okno przeglądarki otwarte. Będzie potrzebne w dalszej części samouczka.

## <a name="template-utterances"></a>Wypowiedzi szablonu
Ze względu na charakter domeny tematu zasobów ludzkich istnieje kilka typowych sposobów zadawania pytań o relacje z pracownikami w organizacjach. Przykład:

|Wypowiedzi|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Te wypowiedzi są za bardzo zbliżone, aby określić kontekstową unikatowość każdej bez podawania wielu przykładowych wypowiedzi. Dodając wzorzec do intencji, usługa LUIS uczy się typowych wzorców wypowiedzi dla intencji bez podawania wielu przykładowych wypowiedzi.

Przykładowe wypowiedzi szablonu dla tej intencji obejmują:

|Przykładowe wypowiedzi szablonu|Znaczenie składni|
|--|--|
|`Who does {Employee} report to[?]`|Wymienne`{Employee}`<br>Ignoruj`[?]`|
|`Who reports to {Employee}[?]`|Wymienne`{Employee}`<br>Ignoruj`[?]`|

Składnia `{Employee}` oznacza lokalizację jednostki w ramach wypowiedzi szablonu oraz to, która to jednostka. Opcjonalna składnia `[?]`, oznacza wyrazy lub znaki interpunkcyjne, które są opcjonalne. Usługa LUIS dopasowuje wypowiedź, ignorując opcjonalny tekst w nawiasie.

Chociaż składnia wygląda jak wyrażenie regularne, nie jest wyrażeniem regularnym. Obsługiwana jest tylko składnia w nawiasie klamrowym, `{}`, i nawiasie kwadratowym, `[]`. Mogą być one zagnieżdżone na najwyżej dwóch poziomach.

Aby wzorzec, który ma być dopasowany do wypowiedź, _najpierw_ jednostki w wypowiedź muszą odpowiadać jednostek w wypowiedź szablonu. Oznacza to, że jednostki muszą mieć wystarczającą liczbę przykładów w wypowiedziach przykładowych z wysokim stopniem przewidywania, zanim wzorce z jednostkami zakończy się pomyślnie. Jednak szablon nie pomaga w przewidywaniu jednostek, tylko intencji.

**Wzorce umożliwiają dostarczenie mniejszej liczby wypowiedzi przykładowych, jednak jeśli jednostki nie zostaną wykryte, nie zostanie znalezione dopasowanie dla wzorca.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Dodawanie wzorców intencji programu OrgChart-Manager

1. Wybierz pozycję **Build** (Kompiluj) w górnym menu.

1. Na lewym pasku nawigacyjnym w obszarze **Improve app performance** (Popraw wydajność aplikacji) wybierz opcję **Patterns** (Wzorce).

1. Wybierz intencję **OrgChart-Manager** (Schemat organizacyjny — menedżer), a następnie wprowadź następujące wypowiedzi szablonu:

    |Wypowiedzi szablonu|
    |:--|
    |`Who is {Employee} the subordinate of[?]`|
    |`Who does {Employee} report to[?]`|
    |`Who is {Employee}['s] manager[?]`|
    |`Who does {Employee} directly report to[?]`|
    |`Who is {Employee}['s] supervisor[?]`|
    |`Who is the boss of {Employee}[?]`|

    Te wypowiedzi szablonu obejmują **Employee** jednostki z notacji nawiasu kręcone.

1. Podczas gdy nadal na wzorce strony, wybierz **OrgChart-Reports** intencji, a następnie wprowadź następujące wypowiedzi szablonu:

    |Wypowiedzi szablonu|
    |:--|
    |`Who are {Employee}['s] subordinates[?]`|
    |`Who reports to {Employee}[?]`|
    |`Who does {Employee} manage[?]`|
    |`Who are {Employee} direct reports[?]`|
    |`Who does {Employee} supervise[?]`|
    |`Who does {Employee} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Punkt końcowy zapytania, gdy są używane wzorce

Teraz, gdy wzorce są dodawane do aplikacji, pociągu, publikowania i kwerendy aplikacji w punkcie końcowym środowiska wykonawczego przewidywanie.

1. Wybierz **pociąg**. Po zakończeniu szkolenia wybierz **pozycję Publikuj** i wybierz miejsce **produkcyjne,** a następnie wybierz opcję **Gotowe**.

1. Po zakończeniu publikowania przełącz karty przeglądarki z powrotem do karty adresu URL punktu końcowego.

1. Przejdź na koniec adresu URL na pasku adresu i zastąp _YOUR_QUERY_HERE:_`Who is the boss of Jill Jones?`

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999997854
                },
                "OrgChart-Reports": {
                    "score": 6.13748343E-05
                },
                "EmployeeFeedback": {
                    "score": 8.052567E-06
                },
                "GetJobInformation": {
                    "score": 1.18197136E-06
                },
                "MoveEmployee": {
                    "score": 7.65549657E-07
                },
                "None": {
                    "score": 3.975E-09
                },
                "Utilities.StartOver": {
                    "score": 1.53E-09
                },
                "Utilities.Confirm": {
                    "score": 1.38181822E-09
                },
                "Utilities.Help": {
                    "score": 1.38181822E-09
                },
                "Utilities.Stop": {
                    "score": 1.38181822E-09
                },
                "Utilities.Cancel": {
                    "score": 1.25833333E-09
                },
                "FindForm": {
                    "score": 1.15384613E-09
                },
                "ApplyForJob": {
                    "score": 5.26923061E-10
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Przewidywanie intencji jest teraz znacznie bardziej pewny siebie, a następny najwyższy wynik intencji jest znacznie niższy. Te dwa zamiary nie będzie flip-flop podczas treningu.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Praca z tekstem opcjonalnym i wstępnie skompilowanymi jednostkami

Poprzednie wypowiedzi szablonu wzorca w tym samouczku miały kilka przykładów opcjonalnego tekstu, np. użycie formy dzierżawczej pod postacią litery s `'s` (dotyczy języka angielskiego) i stosowanie znaku zapytania `?`. Załóżmy, że należy zezwolić na bieżące i przyszłe daty w tekście wypowiedź.

Przykładowe wypowiedzi:

|Intencja|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Każdy z tych przykładów używa czasowników w różnych czasach: `was`, `is`, `will be`, a także daty: `March 3`, `now` i `in a month`, których usługa LUIS potrzebuje do poprawnego przewidywania. Należy zauważyć, że dwa ostatnie przykłady w tabeli używają prawie tego samego tekstu z wyjątkiem `in` i `on`.

Przykładowe wypowiedzi szablonu, które pozwalają na te opcjonalne informacje:

|Intencja|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


Korzystanie z opcjonalnych składni z nawiasami kwadratowymi, `[]`, sprawia, że ten opcjonalny tekst jest łatwy do dodania do wypowiedzi szablonu i może być zagnieżdżony najwyżej do drugiego poziomu, `[[]]`, a także może zawierać tekst lub jednostki.


**Pytanie: Dlaczego wszystkie `w` litery, pierwsza litera w każdej wypowiedź szablonu, małe litery? Czy nie powinny być opcjonalnie wielkie lub małe litery?** Wypowiedź przesłana do punktu końcowego zapytania przez aplikację kliencką jest konwertowana na małe litery. Wypowiedź szablonu może zaczynać się dużą lub małą literą, podobnie jak wypowiedź punktu końcowego. Porównanie odbywa się zawsze po konwersji na małe litery.

**Pytanie: dlaczego wstępnie skompilowany numer nie jest częścią wypowiedzi szablonu, jeśli March 3 (3 marca) jest przewidziany zarówno jako liczba `3`, jak i data `March 3`?** Wypowiedź szablonu kontekstowo używa daty — albo dosłownie jak w wypowiedzi `March 3`, albo abstrakcyjnie jak w wypowiedzi `in a month`. Data może zawierać liczbę, ale liczba nie musi być zawsze postrzegana jako data. Należy zawsze używać jednostki najlepiej reprezentującej typ, który ma być zwracany w wynikach przewidywania JSON.

**Pytanie: co z niepoprawnie zapisanymi wypowiedziami, takimi jak `Who will {Employee}['s] manager be on March 3?`.** Gramatycznie różne czasy czasowników, takie jak tu, gdzie `will` i `be` są rozdzielone, muszą być nową wypowiedzią szablonu. Istniejąca wypowiedź szablonu nie będzie z tym zgodna. Mimo że intencja wypowiedzi nie została zmieniona, zmieniło się umieszczenie słowa w wypowiedzi. Ta zmiana ma wpływ na przewidywanie w usłudze LUIS. Można [grupować i lub](#use-the-or-operator-and-groups) czasowników, aby połączyć te wypowiedzi.

**Pamiętaj: najpierw znajdowane są jednostki, a następnie dopasowywany jest wzorzec.**

### <a name="add-new-pattern-template-utterances"></a>Dodawanie nowych wypowiedzi szablonu wzorca

1. W sekcji **Patterns** (Wzorce) części **Build** (Kompiluj) dodaj kilka nowych wypowiedzi szablonu wzorca. Wybierz pozycję **OrgChart-Manager** (Schemat organizacyjny — menedżer) z menu rozwijanego Intent (Intencja) i wprowadź każdą z następujących wypowiedzi szablonu:

    |Intencja|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Wybierz **pozycję Trenuj** na pasku nawigacyjnym, aby szkolić aplikację.

3. Po zakończeniu szkolenia wybierz **test** w górnej części panelu, aby otworzyć panel testowy.

4. Wprowadź kilka testowych wypowiedzi, aby sprawdzić, czy wzorzec jest dopasowany i czy wynik intencji jest znacząco wysoki.

    Po wprowadzeniu pierwszej wypowiedzi wybierz polecenie **Inspect** (Sprawdź) w obszarze wyniku, dzięki czemu można zobaczyć wszystkie wyniki przewidywania. Każda wypowiedź powinna mieć intencji **OrgChart-Manager** i należy wyodrębnić wartości dla jednostek Pracownika i datetimeV2.

    |Wypowiedź|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Wszystkie te wypowiedzi znalazły jednostki wewnątrz, dlatego pasują do tego samego wzorca i mają wysoki wynik przewidywania. Dodano kilka wzorców, które będą pasować do wielu odmian wypowiedzi. Nie trzeba dodawać żadnych wypowiedzi przykład w intencji, aby wypowiedź szablonu pracy we wzorcu.

To wykorzystanie wzorów pod warunkiem:
* Wyższe wyniki prognoz
* Z tych samych wypowiedzi przykład w intencji
* Z zaledwie kilkoma dobrze skonstruowanymi wypowiedziami szablonu we wzorcu

### <a name="use-the-or-operator-and-groups"></a>Użyj operatora i grup OR

Kilka z poprzednich wypowiedzi szablonu są bardzo blisko. Użyj **grupy** `()` i **składni OR,** `|` aby zmniejszyć wypowiedzi szablonu.

Następujące wzorce 2 można połączyć w `()` jeden `|` wzorzec przy użyciu grupy i składni OR.

|Intencja|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Nowa wypowiedź szablonu będzie:

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

Używa **grupy** wokół wymaganego czasu czasownika `in` i `on` opcjonalne i **z lub** rury między nimi.

1. Na stronie **Wzorce** wybierz filtr **OrgChart-Manager.** Zawęź listę, wyszukując `manager`.

1. Zachowaj jedną wersję wypowiedź szablonu (do edycji w następnym kroku) i usuń inne odmiany.

1. Zmień wypowiedź szablonu na: 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`

2. Wybierz **pozycję Trenuj** na pasku nawigacyjnym, aby szkolić aplikację.

3. Po zakończeniu szkolenia wybierz **test** w górnej części panelu, aby otworzyć panel testowy.

    Użyj okienka testowego, aby przetestować wersje wypowiedź:

    |Wypowiedzi do wprowadzenia w okienku testowym|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Za pomocą większej składni wzorca, można zmniejszyć liczbę wypowiedzi szablonu, które mają być utrzymywane w aplikacji, a jednocześnie o wysoki wynik prognozowania.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Używanie zakotwiczenia początkowego i końcowego wypowiedź

Składnia wzorca zawiera początkową i końcową składnię `^`zakotwiczenia wypowiedzi edyty, . Zakotwiczenia wypowiedź początku i zakończenia mogą być używane razem do kierowania bardzo konkretne i ewentualnie dosłowne wypowiedź lub używane oddzielnie do intencji docelowej.

## <a name="using-patternany-entity"></a>Używanie jednostki Pattern.any

Jednostka Pattern.any umożliwia wyszukiwanie danych o swobodnym formacie, gdzie treść jednostki sprawia, że trudno rozróżnić koniec jednostki od reszty wypowiedzi.

Ta aplikacja zarządzania zasobami ludzkimi pomaga pracownikom znajdować formularze firmy.

|Wypowiedź|
|--|
|Where is **HRF-123456**? (Gdzie jest formularz HRF-123456?)|
|Who authored **HRF-123234**? (Kto jest autorem formularza HRF-123456?)|
|**HRF-456098** is published in French? (Czy formularz HRF-123456 jest publikowany w języku francuskim?)|

Jednak każdy formularz ma zarówno nazwę sformatowaną, używaną w powyższej tabeli, jak i nazwę przyjazną, taką jak `Request relocation from employee new to the company 2018 version 5`.

Wypowiedzi z przyjazną formą wyglądają następująco:

|Wypowiedź|
|--|
|Where is **Request relocation from employee new to the company 2018 version 5**? (Gdzie jest formularz Żądanie przeniesienia nowego pracownika do firmy 2018 wersja 5?)|
|Who authored **Request relocation from employee new to the company 2018 version 5**? (Kto jest autorem formularza Żądanie przeniesienia nowego pracownika do firmy 2018 wersja 5?)|
|**Request relocation from employee new to the company 2018 version 5** is published in French? (Czy formularz Żądanie przeniesienia nowego pracownika do firmy 2018 wersja 5 jest publikowany w języku francuskim?)|

Różna długość zawiera wyrazy, które mogą wprowadzać usługę LUIS w błąd co do tego, gdzie kończy się dana jednostka. Użycie jednostki Pattern.any we wzorcu umożliwia określenie początku i końca nazwy formularza, dzięki czemu usługa LUIS poprawnie wyodrębnia nazwę formularza.

|Przykładowa wypowiedź szablonu|
|--|
|Where is {FormName}[?] (Gdzie jest {Nazwa_formularza}[?])|
|Who authored {FormName}[?] (Kto jest autorem {Nazwa_formularza}[?])|
|{FormName} is published in French[?] (Czy {Nazwa_formularza} jest publikowany w języku francuskim [?])|

### <a name="add-example-utterances-with-patternany"></a>Dodawanie przykładowych wypowiedzi za pomocą pattern.any

1. Wybierz pozycję **Build** (Kompiluj) na górnym pasku nawigacyjnym i wybierz pozycję **Intents** (Intencje) na lewym pasku nawigacyjnym.

1. Z listy intencji wybierz pozycję **FindForm** (Znajdź formularz).

1. Dodaj kilka przykładowych wypowiedzi. Tekst, który powinien być przewidywany jako Pattern.any jest **pogrubioną czcionką**. Nazwa formularza jest trudne do określenia z innych słów wokół niego w wypowiedź. Pattern.any pomoże, zaznaczając granice jednostki.

    |Przykładowa wypowiedź|Nazwa formularza|
    |--|--|
    |Where is the form **What to do when a fire breaks out in the Lab** and who needs to sign it after I read it? (Gdzie jest formularz Postępowanie w razie pożaru w laboratorium i kto musi go podpisać, gdy go przeczytam?)|Co zrobić, gdy w laboratorium wybuchnie pożar
    |Where is **Request relocation from employee new to the company 2018 version 5** on the server? (Gdzie jest formularz Żądanie przeniesienia nowego pracownika do firmy 2018 wersja 5 na serwerze?)|Wniosek o przeniesienie pracownika do firmy|
    |Who authored "**Health and wellness requests on the main campus**" and what is the most current version? (Kto jest autorem formularza „Wymagania dotyczące zdrowia i dobrego samopoczucia na głównym kampusie” i jaka jest najbardziej aktualna wersja?)|Wnioski o zdrowie i dobre samopoczucie na głównym kampusie|
    |I'm looking for the form named "**Office move request including physical assets**". (Szukam formularza o nazwie „Żądanie przeniesienia biura łącznie z zasobami fizycznymi”.) |Żądanie przeniesienia pakietu Office, w tym zasoby fizyczne|

    Bez jednostki Pattern.any usługa LUIS może mieć trudność ze zrozumieniem, gdzie kończy się tytuł formularza ze względu na wiele różnych nazw formularzy.

### <a name="create-a-patternany-entity"></a>Tworzenie jednostki Pattern.any
Jednostka Pattern.any wyodrębnia jednostki o różnej długości. Działa tylko we wzorcu, ponieważ wzorzec oznacza początek i koniec elementu ze składnią.

1. Wybierz pozycję **Entities** (Jednostki) na lewym pasku nawigacyjnym.

1. Wybierz **+ Utwórz** `FormName`, wprowadź nazwę i wybierz **Deseń.any** jako typ. Wybierz **pozycję Utwórz**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Dodawanie wzorca, który używa jednostki Pattern.any

1. Wybierz pozycję **Patterns** (Wzorce) na lewym pasku nawigacyjnym.

1. Wybierz intencję **FindForm** (Znajdź formularz).

1. Wprowadź następujące wypowiedzi szablonu, które używają nowej jednostki:

    |Wypowiedzi szablonu|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Przeszkol aplikację.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testowanie nowego wzorca do wyodrębnienia danych o formacie swobodnym
1. Na górnym pasku wybierz pozycję **Test** (Testuj), aby otworzyć panel testu.

1. Wprowadź następującą wypowiedź:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. W obszarze wyniku wybierz pozycję **Inspect** (Sprawdź), aby zobaczyć wyniki testu jednostki i intencji.

    Najpierw znaleziono jednostkę `FormName`, następnie znaleziono wzorzec określający intencję. Jeśli otrzymano wynik testu, w którym jednostki nie zostały wykryte, a w związku z tym nie znaleziono wzorca, należy dodać więcej przykładowych wypowiedzi do intencji (nie wzorca).

1. Zamknij panel testu, wybierając przycisk **Test** (Testuj) na górnym pasku nawigacyjnym.

### <a name="using-an-explicit-list"></a>Korzystanie z listy jawnej

Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](reference-pattern-syntax.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem.

## <a name="what-did-this-tutorial-accomplish"></a>Co ten poradnik osiągnąć?

W tym samouczku dodano wzorce, aby pomóc usługi LUIS przewidzieć intencji ze znacznie wyższy wynik bez konieczności dodawania więcej wypowiedzi przykład. Oznaczanie jednostek i tekst możliwy do zignorowania pozwoliły usłudze LUIS na zastosowanie wzorca do szerszego zakresu wypowiedzi.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki


> [!div class="nextstepaction"]
> [Dowiedz się, jak używać ról ze wzorcem](luis-tutorial-pattern.md)

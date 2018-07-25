---
title: Samouczek przy użyciu wzorców w celu przewidywania usługi LUIS — Azure | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: W tym samouczku należy użyć wzorca dla opcji, aby zwiększyć prognozy intencji i jednostki usługi LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: 9ad1d9e1543c3d9a74025fb23bd1767478b53b4b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238458"
---
# <a name="tutorial-improve-app-with-patterns"></a>Samouczek: Ulepszaniu aplikacji wzorami

W tym samouczku Użyj wzorce, aby zwiększyć prognozowania intencji i jednostek.  

> [!div class="checklist"]
* Jak zidentyfikować, że wzorzec może pomóc Twojej aplikacji
* Jak utworzyć wzorca 
* Jak zweryfikować ulepszenia prognozowania wzorzec

Na potrzeby tego artykułu jest wymagane bezpłatne konto usługi [LUIS](luis-reference-regions.md), które umożliwia utworzenie aplikacji usługi LUIS.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz zarządzania zasobami ludzkimi firmy [partii testów](luis-tutorial-batch-testing.md) samouczek, [zaimportować](luis-how-to-start-new-app.md#import-new-app) dane JSON do nowej aplikacji w [usługi LUIS](luis-reference-regions.md#luis-website) witryny sieci Web. Aplikację do zaimportowania znajduje się w [przykłady LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json) repozytorium GitHub.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `patterns`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Wzorce uczyć LUIS wspólnej wypowiedzi wraz z przykładami mniej
Ze względu na charakter domeny zarządzania zasobami ludzkimi istnieje kilka typowych sposobów z pytaniem o relacjach pracowników w organizacji. Na przykład:

```
Who does Jill Jones report to?
Who reports to Jill Jones? 
```

Te wypowiedzi są zbyt Zamknij, aby określić kontekstowych unikatowości każdego bez podawania wiele przykładów wypowiedź. Dodając wzorzec intencji, LUIS uczy się typowe wzorce wypowiedź intencji bez podawania wiele przykładów wypowiedź. 

Przykładowy szablon wypowiedzi konwersji między innymi:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Wzorzec jest dostarczany za pomocą przykładu wypowiedź szablonu, który zawiera Składnia służąca do identyfikowania jednostki i tekstu można zignorować. Wzorzec jest kombinacją Dopasowywanie wyrażeń regularnych i uczenia maszynowego.  Przykład wypowiedź szablonu, wraz z intencji wypowiedzi zapewniają LUIS lepiej zrozumieć, z jakiego wypowiedzi Dopasuj intencji.

Aby wzorzec można dopasować do wypowiedź jednostek w ramach wypowiedź musi odpowiadać jednostek w wypowiedź szablon najpierw. Jednak szablonu nie ułatwiania prognozowania jednostki, tylko intencji. 

**Gdy wzorce umożliwiają mniej wypowiedzi przykład, jeśli obiekty nie są wykrywane, wzorzec jest niezgodny.**

Należy pamiętać, że pracownicy zostały utworzone w [jednostki samouczek](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Utwórz nowy intencje i ich wypowiedzi
Dodawanie dwóch nowych intencji: `OrgChart-Manager` i `OrgChart-Reports`. Gdy usługa LUIS zwraca prognozowania do aplikacji klienckiej intencji nazwy mogą być używane jako nazwy funkcji w aplikacji klienckiej, a jednostka pracownik może być używana jako parametr do tej funkcji.

```
OrgChart-Manager(employee){
    ///
}
```

1. Upewnij się, że aplikacja Human Resources znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

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

    [![](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Zrzut ekranu dodawania nowej wypowiedzi na intencje LUIS")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

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
Ilość przykład wypowiedzi w tych intencji nie wystarcza do nauczenia usługi LUIS prawidłowo. W przypadku aplikacji rzeczywistych każdego intencji powinien mieć co najmniej 15 wypowiedzi zapewnia szeroki wybór i wypowiedź długość słowa. Te kilka wypowiedzi są wybrane specjalnie w celu wyróżnienia wzorców. 

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Nowe opcje i wypowiedzi wymagają szkolenia. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Obraz przycisku uczenia](./media/luis-tutorial-pattern/hr-train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Obraz paska powiadomień powodzenia](./media/luis-tutorial-pattern/hr-trained-inline.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego
Aby uzyskać przewidywania usługi LUIS w czatbocie lub innej aplikacji, należy opublikować aplikację. 

1. W górnej części witryny usługi LUIS po prawej stronie wybierz przycisk **Publish** (Publikuj). 

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    [ ![Zrzut ekranu publikowanie strony przy użyciu funkcji Publikuj, aby wyróżnionym przyciskiem miejsce produkcyjne](./media/luis-tutorial-pattern/hr-publish-to-production.png)](./media/luis-tutorial-pattern/hr-publish-to-production.png#lightbox)

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi
1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    [ ![Zrzut ekranu publikowanie strony z wyróżnioną pozycją adres URL punktu końcowego](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png)](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png#lightbox)


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

## <a name="add-the-template-utterances"></a>Dodawanie wypowiedzi szablonu

1. Wybierz **kompilacji** w górnym menu.

2. W lewym obszarze nawigacji w obszarze **lepsza wydajność aplikacji**, wybierz opcję **wzorców** w lewym obszarze nawigacji.

3. Wybierz **schemat organizacyjny — Manager** przeznaczenie, wprowadź następujące wypowiedzi szablonu, po kolei, wybranie enter po każdym wypowiedź szablonu:

    |Szablon wypowiedzi|
    |:--|
    |Kto jest podwładnym [?] {pracowników}|
    |Tego, kto wykonuje {pracowników} raportować do [?]|
    |Kto jest menedżera pracownika {} [w] [?]|
    |Tego, kto wykonuje {pracowników} raporty bezpośrednio [?]|
    |Kto jest {} [w] przełożonego [?]|
    |Kto jest szefa {pracownika} [?]|

    `{Employee}` Składni oznacza lokalizację jednostki w ramach wypowiedź szablonu jako jest również jako jakiej encji. 
    
    Jednostki przy użyciu ról należy użyć składni, która zawiera nazwę roli i są objęte [oddzielne samouczek dotyczący ról](luis-tutorial-pattern-roles.md). 

    Opcjonalnych składni `[]`, oznacza słowa lub znaki interpunkcyjne, które są opcjonalne. Usługa LUIS dopasowuje wypowiedź, ignorowanie opcjonalny tekst w nawiasie.

    Jeśli wpiszesz wypowiedź szablonu, pomaga LUIS wypełnienie w jednostce po wprowadzeniu lewy nawias klamrowy `{`.

    [ ![Zrzut ekranu przedstawiający wprowadzanie wypowiedzi szablonu dla intencji](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)



4. Wybierz **schemat organizacyjny — raporty** przeznaczenie, wprowadź następujące wypowiedzi szablonu, po kolei, wybranie enter po każdym wypowiedź szablonu:

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

2. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

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

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz przycisk wielokropka (***...*** ) po prawej stronie nazwy aplikacji, na liście aplikacji wybierz **Usuń**. W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak użyć ról z wzorcem](luis-tutorial-pattern-roles.md)
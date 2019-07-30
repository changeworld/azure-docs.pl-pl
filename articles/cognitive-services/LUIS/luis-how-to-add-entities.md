---
title: Dodawanie jednostek — LUIS
titleSuffix: Azure Cognitive Services
description: Utwórz jednostki, aby wyodrębnić dane z wyrażenia długości użytkownika w aplikacjach Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 1b42ab9155f5b9719ef6477934722021e3fbac99
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638241"
---
# <a name="create-entities-without-utterances"></a>Tworzenie jednostek bez wypowiedzi

Jednostka reprezentuje wyraz lub frazę w polu wypowiedź, który ma zostać wyodrębniony. Jednostka reprezentuje klasę zawierającą kolekcję podobnych obiektów (miejsc, rzeczy, osób, zdarzeń lub koncepcji). Jednostki opisują informacje istotne dla intencji i czasami są istotne dla aplikacji w celu wykonania swojego zadania. Można utworzyć jednostki po dodaniu wypowiedź do obiektu zamiaru lub od (przed lub po) dodania wypowiedź do zamiaru.

Dodawanie, edytowanie lub usuwanie jednostek w aplikacji usługi LUIS za pośrednictwem **listy jednostek** na **jednostek** strony. Usługa LUIS oferuje dwa główne rodzaje jednostek: [ze wstępnie utworzonych jednostek](luis-reference-prebuilt-entities.md)i Twoje [jednostek niestandardowych](luis-concept-entity-types.md#types-of-entities).

Po utworzeniu jednostki, która jest tworzona, należy oznaczyć tę jednostkę we wszystkich przykładach wypowiedź wszystkich zamiarów, w których się znajduje.

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Dodawanie wstępnie skompilowanej jednostki do aplikacji

Typowe ze wstępnie utworzonych jednostek dodanych do aplikacji są *numer* i *datetimeV2*. 

1. W swojej aplikacji z **kompilacji** zaznacz **jednostek** w panelu po lewej stronie.
 
1. Na **jednostek** wybierz opcję **Dodaj ze wstępnie utworzonych jednostek**.

1. W **Dodaj ze wstępnie utworzonych jednostek** okno dialogowe, wybierz opcję **numer** i **datetimeV2** ze wstępnie utworzonych jednostek. Następnie wybierz pozycję **Done** (Gotowe).

    ![Zrzut ekranu Dodaj okno dialogowe wstępnie utworzone jednostki](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Dodawanie prostych jednostek dla pojedynczych koncepcji

Proste jednostki w tym artykule opisano pojęcia pojedynczego. Użyj poniższej procedury, aby utworzyć jednostki, która wyodrębnia nazwy działów firmy, takich jak *zarządzania zasobami ludzkimi* lub *operacji*.   

1. W swojej aplikacji, wybierz **kompilacji** sekcji, a następnie wybierz **jednostek** w panelu po lewej stronie, a następnie zaznacz **Utwórz nową jednostkę**.

1. W podręcznym oknie dialogowym, wpisz `Location` w **nazwa jednostki** wybierz opcję **proste** z **typu jednostki** listy, a następnie wybierz **gotowe**.

    Po utworzeniu tej jednostki, przejdź do wszystkich intencji, mających wypowiedzi przykładu, które zawierają jednostki. Zaznacz tekst w polu wypowiedź przykład i oznaczanie tekstu jako jednostka. 

    A [listy fraz](luis-concept-feature.md) jest najczęściej używany do zwiększania sygnał proste jednostki.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Dodawanie jednostek wyrażenia regularnego w celu uzyskania wysoce strukturalnych koncepcji

Jednostka wyrażenia regularnego umożliwia wyodrębnienie danych z wypowiedź, w oparciu o podane wyrażenie regularne. 

1. W swojej aplikacji, wybierz **jednostek** z nawigacji po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

1. W podręcznym oknie dialogowym, wprowadź `Human resources form name` w **nazwa jednostki** wybierz opcję **wyrażenia regularnego** z **typu jednostki** listy, wprowadź wyrażenie regularne `hrf-[0-9]{6}`, a następnie wybierz pozycję **gotowe**. 

    To wyrażenie regularne dopasowuje znaki `hrf-`literału, a następnie 6 cyfr, aby reprezentować numer formularza dla formularza zasobów ludzkich.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Dodawanie jednostek złożonych do grupowania w relacji nadrzędny-podrzędny

Możesz zdefiniować relacje między jednostkami w różnych typów, tworząc obiekt złożony. W poniższym przykładzie jednostka zawiera wyrażenie regularne i wstępnie utworzone jednostki o nazwie.  

W polu wypowiedź `Send hrf-123456 to John Smith`, tekst `hrf-123456` jest dopasowywany do zarządzania zasobami ludzkimi [wyrażenia regularnego](#add-regular-expression-entities) i `John Smith` jest wyodrębniany z personName wstępnie utworzone jednostki. Każda jednostka jest częścią większej, jednostka nadrzędna. 

1. W swojej aplikacji, wybierz **jednostek** w lewym obszarze nawigacji **kompilacji** sekcji, a następnie wybierz **Dodawanie wstępnie utworzone jednostki**.

1. Dodawanie wstępnie utworzone jednostki **PersonName**. Aby uzyskać instrukcje, zobacz [Dodaj ze wstępnie utworzonych jednostek](#add-prebuilt-entity). 

1. Wybierz **jednostek** z nawigacji po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

1. W podręcznym oknie dialogowym, wprowadź `SendHrForm` w **nazwa jednostki** polu, a następnie wybierz **złożonego** z **typu jednostki** listy.

1. Wybierz **Dodaj element podrzędny** można dodać nowego elementu podrzędnego.

1. W **podrzędnych nr 1**, wybierz jednostkę **numer** z listy.

1. W **podrzędnych nr 2**, wybierz jednostkę **zarządzania zasobami ludzkimi tworzenia nazwy** z listy. 

1. Wybierz pozycję **Done** (Gotowe).

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Dodaj wzorzec. wszystkie jednostki do przechwytywania jednostek o dowolnej postaci

[Pattern.any](luis-concept-entity-types.md) jednostek obowiązują tylko w [wzorców](luis-how-to-model-intent-pattern.md), nie intencji. Ten typ jednostki pomaga LUIS Znajdź koniec jednostki o różnej długości i wybór programu word. Ponieważ tej jednostki są używane we wzorcu, LUIS wie, gdzie jest koniec jednostki w szablonie wypowiedź.

Jeśli aplikacja ma `FindHumanResourcesForm` intencji, tytuł formularza wyodrębnione może zakłócać intencji prognozy. W celu wyjaśnienia, które wyrazy są w tytule formularza, należy użyć Pattern.any we wzorcu. Prognozy usługi LUIS rozpoczyna się od wypowiedź. Po pierwsze wypowiedź jest zaznaczone, a takie samo dla jednostki, gdy jednostki zostaną znalezione, a następnie wzorzec jest zaznaczone, a dopasowane. 

W polu wypowiedź `Where is Request relocation from employee new to the company on the server?`, tytuł formularza jest trudne, ponieważ nie jest kontekstowe oczywiste, gdzie kończy się tytuł i gdzie rozpoczyna się w pozostałej części wypowiedź. Tytuły może być w dowolnej kolejności, łącznie z pojedynczego wyrazu wyrazów złożonych wyrażeń przy użyciu znaków interpunkcyjnych i pozbawiona sensu kolejność słów. Wzorzec pozwala na tworzenie jednostki gdzie można wyodrębnić jednostki pełne i dokładne. Po znalezieniu tytuł `FindHumanResourcesForm` intencji przewiduje się, ponieważ jest celem dla wzorca.

1. Z **kompilacji** zaznacz **jednostek** w panelu po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

1. W **Dodaj jednostkę** okna dialogowego wprowadź `HumanResourcesFormTitle` w **nazwa jednostki** i zaznacz **Pattern.any** jako **typu jednostki**.

    Aby użyć jednostki pattern.any, dodawanie wzorca na **wzorców** strony w **lepsza wydajność aplikacji** sekcji ze składnią poprawne nawiasów klamrowych, takich jak `Where is **{HumanResourcesFormTitle}** on the server?`.

    Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](luis-concept-patterns.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Dodawanie roli w celu rozróżnienia różnych kontekstów

Rola jest nazwanym podtypem na podstawie kontekstu. Jest ona dostępna we wszystkich jednostkach, w tym wstępnie utworzonych i nieopartych na maszynach jednostkach. 

Składnia dla roli polega **`{Entityname:Rolename}`** na tym, że nazwa jednostki występuje dwukropek, a następnie nazwa roli. Na przykład `Move {personName} from {Location:Origin} to {Location:Destination}`.

1. Z **kompilacji** zaznacz **jednostek** w panelu po lewej stronie.

1. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę). Wprowadź nazwę `Location`. Wybierz typ **proste** i wybierz **gotowe**. 

1. Wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz nową **lokalizację** jednostki utworzoną w poprzednim kroku.

1. W **nazwy roli** polu tekstowym wprowadź nazwę roli `Origin` i wprowadzić. Dodaj drugi nazwę roli programu `Destination`. 

    ![Zrzut ekranu przedstawiający Dodawanie roli źródła do lokalizacji jednostki](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Dodaj jednostki listy dla dokładnych dopasowań

Lista jednostek reprezentują stały, zamknięte zbiór powiązanych słów. 

W przypadku aplikacji zarządzania zasobami ludzkimi może mieć listę wszystkich działów wraz z dowolnym synonimy dla działów. Nie trzeba znać wszystkie wartości, po utworzeniu jednostki. Możesz dodać więcej po zapoznaniu się z wypowiedzi rzeczywistego użytkownika przy użyciu synonimów.

1. Z **kompilacji** zaznacz **jednostek** w panelu po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

1. W **Dodaj jednostkę** okno dialogowe, typ `Department` w **nazwa jednostki** pole, a następnie wybierz **listy** jako **typu jednostki**. Wybierz pozycję **Done** (Gotowe).
  
1. Strona jednostki listy umożliwia dodawanie nazw znormalizowana. W **wartości** polu tekstowym wprowadź nazwę działu do listy, takich jak `HumanResources` naciśnij klawisz Enter na klawiaturze. 

1. Z prawej strony wartość znormalizowana Wprowadź synonimy, naciskając klawisz Enter na klawiaturze, po każdym elemencie.

1. Więcej znormalizowane elementów na liście, zaznacz **zaleca się** Aby wyświetlić opcje z [semantycznego słownika](luis-glossary.md#semantic-dictionary).

    ![Zrzut ekranu przedstawiający wybór funkcji zalecamy, aby wyświetlić opcje](./media/add-entities/hr-list-2.png)


1. Wybierz element na liście zalecane, aby dodać je jako znormalizowaną wartość lub wybierz **Dodaj wszystkie** można dodać wszystkich elementów. 
    Wartości można zaimportować do istniejącej jednostki listy przy użyciu następującego formatu JSON:

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>Nie zmieniaj typu jednostki

Usługa LUIS nie pozwala zmienić typ jednostki, ponieważ nie ma informacji, co należy dodać lub usunąć do skonstruowania tej jednostki. Aby zmienić typ, lepiej jest utworzyć nową jednostkę poprawnego typu o nazwie nieco inne. Po utworzeniu jednostki w każdym wypowiedź usuń starą nazwę jednostki oznaczone i dodać nową nazwę jednostki. Gdy ma zostać relabeled wszystkich wypowiedzi, należy usunąć stare jednostki. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Tworzenie wzorca na podstawie przykładu wypowiedź

Zobacz [Dodaj wzorca od istniejących wypowiedź przeznaczenie lub jednostki strony](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Uczenie aplikacji po zmianie modelu z jednostkami

Po dodaniu, edytować lub usunąć jednostki, [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wstępnie utworzonych jednostek, zobacz [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text) projektu. 

Aby uzyskać informacji na temat sposobu wyświetlania jednostki w odpowiedzi na zapytanie punkt końcowy JSON, zobacz [wyodrębnianie danych](luis-concept-data-extraction.md)

Teraz, że dodano intencje, wypowiedzi i podmioty, masz podstawową aplikację usługi LUIS. Dowiedz się, jak [szkolenie](luis-how-to-train.md), [test](luis-interactive-test.md), i [publikowania](luis-how-to-publish-app.md) aplikacji.
 

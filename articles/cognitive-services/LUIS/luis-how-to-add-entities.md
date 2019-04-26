---
title: Dodawanie jednostek
titleSuffix: Language Understanding - Azure Cognitive Services
description: Tworzenie jednostek, aby wyodrębnić dane kluczowych z wypowiedzi użytkowników w aplikacjach Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 0044cbc9e6142989a57e79de5fd1e78e999bb5e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196136"
---
# <a name="create-entities-without-utterances"></a>Tworzenie jednostek bez wypowiedzi

Jednostka reprezentuje wyraz lub frazę w polu wypowiedź, który ma zostać wyodrębniony. Jednostka reprezentuje klasę, łącznie z kolekcji podobnych obiektów (miejsc, rzeczy, osoby, zdarzenia lub pojęcia). Jednostki opisują informacje istotne dla intencji i czasami są istotne dla aplikacji w celu wykonania swojego zadania. Można utworzyć jednostki, po dodaniu wypowiedź intencji lub od siebie z (przed lub po) dodawanie wypowiedź do intencji.

Dodawanie, edytowanie lub usuwanie jednostek w aplikacji usługi LUIS za pośrednictwem **listy jednostek** na **jednostek** strony. Usługa LUIS oferuje dwa główne rodzaje jednostek: [ze wstępnie utworzonych jednostek](luis-reference-prebuilt-entities.md)i Twoje [jednostek niestandardowych](luis-concept-entity-types.md#types-of-entities).

Po utworzeniu jednostki z maszyny do opanowania, musisz oznaczyć wszystkie wypowiedź przykład intencji, które znajduje się w tej jednostce.

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Dodawanie wstępnie utworzone jednostki do aplikacji

Typowe ze wstępnie utworzonych jednostek dodanych do aplikacji są *numer* i *datetimeV2*. 

1. W swojej aplikacji z **kompilacji** zaznacz **jednostek** w panelu po lewej stronie.
 
1. Na **jednostek** wybierz opcję **Dodaj ze wstępnie utworzonych jednostek**.

1. W **Dodaj ze wstępnie utworzonych jednostek** okno dialogowe, wybierz opcję **numer** i **datetimeV2** ze wstępnie utworzonych jednostek. Następnie wybierz pozycję **Done** (Gotowe).

    ![Zrzut ekranu Dodaj okno dialogowe wstępnie utworzone jednostki](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Dodaj prostą jednostki dla pojedynczego pojęcia

Proste jednostki w tym artykule opisano pojęcia pojedynczego. Użyj poniższej procedury, aby utworzyć jednostki, która wyodrębnia nazwy działów firmy, takich jak *zarządzania zasobami ludzkimi* lub *operacji*.   

1. W swojej aplikacji, wybierz **kompilacji** sekcji, a następnie wybierz **jednostek** w panelu po lewej stronie, a następnie zaznacz **Utwórz nową jednostkę**.

1. W podręcznym oknie dialogowym, wpisz `Location` w **nazwa jednostki** wybierz opcję **proste** z **typu jednostki** listy, a następnie wybierz **gotowe**.

    Po utworzeniu tej jednostki, przejdź do wszystkich intencji, mających wypowiedzi przykładu, które zawierają jednostki. Zaznacz tekst w polu wypowiedź przykład i oznaczanie tekstu jako jednostka. 

    A [listy fraz](luis-concept-feature.md) jest najczęściej używany do zwiększania sygnał proste jednostki.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Dodaj jednostki wyrażenia regularnego uporządkowany pojęcia dotyczące

Jednostka wyrażenia regularnego umożliwia wyodrębnienie danych z wypowiedź, w oparciu o podane wyrażenie regularne. 

1. W swojej aplikacji, wybierz **jednostek** z nawigacji po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

1. W podręcznym oknie dialogowym, wprowadź `Human resources form name` w **nazwa jednostki** wybierz opcję **wyrażenia regularnego** z **typu jednostki** listy, wprowadź wyrażenie regularne `hrf-[0-9]{6}`, a następnie wybierz pozycję **gotowe**. 

    To wyrażenie regularne dopasowuje znaki literału `hrf-`, następnie 6 cyfr do reprezentowania formularza numer formularza zasobów ludzkich.

## <a name="add-hierarchical-entities"></a>Dodaj jednostki hierarchicznych

Hierarchiczna jednostka jest kategoria kontekstowe nauczony i pod względem koncepcyjnym powiązanych jednostek. W poniższym przykładzie jednostki zawiera początkowe i docelowe lokalizacje. 

W polu wypowiedź `Move John Smith from Seattle to Cairo`, Seattle, jest to lokalizacja źródła i Kair jest lokalizacja docelowa. Każda lokalizacja jest kontekstowe różnych i nauczony kolejność słów i wybór o słowo w wypowiedź.

Aby dodać jednostki hierarchiczne, wykonaj następujące czynności: 

1. W swojej aplikacji, wybierz **jednostek** z nawigacji po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

1. W podręcznym oknie dialogowym, wpisz `Location` w **nazwa jednostki** , a następnie wybierz **hierarchiczne** z **typu jednostki** listy.

    ![Dodawanie jednostki hierarchicznej](./media/add-entities/hier-location-entity-creation.png)

1. Wybierz **Dodaj element podrzędny**, a następnie wprowadź `Origin` w **podrzędnych nr 1** pole. 

1. Wybierz **Dodaj element podrzędny**, a następnie wprowadź `Destination` w **podrzędnych nr 2** pole. Wybierz pozycję **Done** (Gotowe).

    >[!CAUTION]
    >Nazwy jednostek podrzędny musi być unikatowa we wszystkich jednostek w jednej aplikacji. Dwoma obiektami hierarchiczne nie może zawierać jednostki podrzędne o takiej samej nazwie. 

    Po utworzeniu tej jednostki, przejdź do wszystkich intencji, mających wypowiedzi przykładu, które zawierają jednostki. Zaznacz tekst w polu wypowiedź przykład i oznaczanie tekstu jako jednostka. 

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Dodaj jednostki złożonego do grupowania w relacji nadrzędny podrzędny

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

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Dodaj jednostki Pattern.any do przechwytywania dowolnych jednostek

[Pattern.any](luis-concept-entity-types.md) jednostek obowiązują tylko w [wzorców](luis-how-to-model-intent-pattern.md), nie intencji. Ten typ jednostki pomaga LUIS Znajdź koniec jednostki o różnej długości i wybór programu word. Ponieważ tej jednostki są używane we wzorcu, LUIS wie, gdzie jest koniec jednostki w szablonie wypowiedź.

Jeśli aplikacja ma `FindHumanResourcesForm` intencji, tytuł formularza wyodrębnione może zakłócać intencji prognozy. W celu wyjaśnienia, które wyrazy są w tytule formularza, należy użyć Pattern.any we wzorcu. Prognozy usługi LUIS rozpoczyna się od wypowiedź. Po pierwsze wypowiedź jest zaznaczone, a takie samo dla jednostki, gdy jednostki zostaną znalezione, a następnie wzorzec jest zaznaczone, a dopasowane. 

W polu wypowiedź `Where is Request relocation from employee new to the company on the server?`, tytuł formularza jest trudne, ponieważ nie jest kontekstowe oczywiste, gdzie kończy się tytuł i gdzie rozpoczyna się w pozostałej części wypowiedź. Tytuły może być w dowolnej kolejności, łącznie z pojedynczego wyrazu wyrazów złożonych wyrażeń przy użyciu znaków interpunkcyjnych i pozbawiona sensu kolejność słów. Wzorzec pozwala na tworzenie jednostki gdzie można wyodrębnić jednostki pełne i dokładne. Po znalezieniu tytuł `FindHumanResourcesForm` intencji przewiduje się, ponieważ jest celem dla wzorca.

1. Z **kompilacji** zaznacz **jednostek** w panelu po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

1. W **Dodaj jednostkę** okna dialogowego wprowadź `HumanResourcesFormTitle` w **nazwa jednostki** i zaznacz **Pattern.any** jako **typu jednostki**.

    Aby użyć jednostki pattern.any, dodawanie wzorca na **wzorców** strony w **lepsza wydajność aplikacji** sekcji ze składnią poprawne nawiasów klamrowych, takich jak `Where is **{HumanResourcesFormTitle}** on the server?`.

    Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](luis-concept-patterns.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Dodaj rolę, aby odróżnić różne konteksty

Rola jest podtypem nazwany na podstawie kontekstu. Jest dostępny w wszystkich jednostek w tym jednostki wbudowanych i przedstawiono maszyny. 

Przy użyciu tego samego przykładu jako hierarchiczna jednostki pochodzenia i miasta docelowego, różnica jest, że roli nosi nazwę źródła zamiast hierarchiczne podrzędnych. 

Składnia dla roli jest **{Entityname:Rolename}** gdzie nazwa jednostki następuje dwukropek, a następnie nazwę roli. Na przykład `Move {personName} from {LocationUsingRoles:Origin} to {LocationUsingRoles:Destination}`.

1. Z **kompilacji** zaznacz **jednostek** w panelu po lewej stronie.

1. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę). Wprowadź nazwę `LocationUsingRoles`. Wybierz typ **proste** i wybierz **gotowe**. 

1. Wybierz **jednostek** z panelu po lewej stronie, a następnie wybierz nową jednostkę **LocationUsingRoles** utworzony w poprzednim kroku.

1. W **nazwy roli** polu tekstowym wprowadź nazwę roli `Origin` i wprowadzić. Dodaj drugi nazwę roli programu `Destination`. 

    ![Zrzut ekranu przedstawiający Dodawanie roli źródła do lokalizacji jednostki](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Dodawanie listy jednostek dokładnego dopasowania

Lista jednostek reprezentują stały, zamknięte zbiór powiązanych słów. 

W przypadku aplikacji zarządzania zasobami ludzkimi może mieć listę wszystkich działów wraz z dowolnym synonimy dla działów. Nie trzeba znać wszystkie wartości, po utworzeniu jednostki. Możesz dodać więcej po zapoznaniu się z wypowiedzi rzeczywistego użytkownika przy użyciu synonimów.

1. Z **kompilacji** zaznacz **jednostek** w panelu po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

1. W **Dodaj jednostkę** okno dialogowe, typ `Department` w **nazwa jednostki** pole, a następnie wybierz **listy** jako **typu jednostki**. Wybierz pozycję **Done** (Gotowe).
  
1. Strona jednostki listy umożliwia dodawanie nazw znormalizowana. W **wartości** polu tekstowym wprowadź nazwę działu do listy, takich jak `HumanResources` naciśnij klawisz Enter na klawiaturze. 

1. Z prawej strony wartość znormalizowana Wprowadź synonimy, naciskając klawisz Enter na klawiaturze, po każdym elemencie.

1. Więcej znormalizowane elementów na liście, zaznacz **zaleca się** Aby wyświetlić opcje z [semantycznego słownika](luis-glossary.md#semantic-dictionary).

    ![Zrzut ekranu przedstawiający Wybieranie funkcji zaleca się, aby wyświetlić opcje](./media/add-entities/hr-list-2.png)


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

## <a name="do-not-change-entity-type"></a>Nie zmieniaj typ jednostki

Usługa LUIS nie pozwala zmienić typ jednostki, ponieważ nie ma informacji, co należy dodać lub usunąć do skonstruowania tej jednostki. Aby zmienić typ, lepiej jest utworzyć nową jednostkę poprawnego typu o nazwie nieco inne. Po utworzeniu jednostki w każdym wypowiedź usuń starą nazwę jednostki oznaczone i dodać nową nazwę jednostki. Gdy ma zostać relabeled wszystkich wypowiedzi, należy usunąć stare jednostki. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Tworzenie wzorca z wypowiedź przykład

Zobacz [Dodaj wzorca od istniejących wypowiedź przeznaczenie lub jednostki strony](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Uczenie aplikacji po zmianie modelu z jednostkami

Po dodaniu, edytować lub usunąć jednostki, [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wstępnie utworzonych jednostek, zobacz [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text) projektu. 

Aby uzyskać informacji na temat sposobu wyświetlania jednostki w odpowiedzi na zapytanie punkt końcowy JSON, zobacz [wyodrębnianie danych](luis-concept-data-extraction.md)

Teraz, że dodano intencje, wypowiedzi i podmioty, masz podstawową aplikację usługi LUIS. Dowiedz się, jak [szkolenie](luis-how-to-train.md), [test](luis-interactive-test.md), i [publikowania](luis-how-to-publish-app.md) aplikacji.
 

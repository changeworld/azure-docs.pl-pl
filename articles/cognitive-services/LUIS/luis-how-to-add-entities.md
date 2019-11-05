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
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 54c9d79c62052daeee76de5dffb1099dc7d75180
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467720"
---
# <a name="create-entities-without-utterances"></a>Utwórz jednostki bez wyrażenia długości

Jednostka reprezentuje słowo lub frazę w wypowiedź, które mają zostać wyodrębnione. Jednostka reprezentuje klasę zawierającą kolekcję podobnych obiektów (miejsc, rzeczy, osób, zdarzeń lub koncepcji). Jednostki opisują informacje istotne dla zamiaru i czasami są niezbędne do wykonania zadania przez aplikację. Można utworzyć jednostki po dodaniu wypowiedź do obiektu zamiaru lub od (przed lub po) dodania wypowiedź do zamiaru.

Możesz dodawać, edytować lub usuwać jednostki w aplikacji LUIS za pomocą **listy jednostki** na stronie **jednostki** . LUIS oferuje dwa główne typy jednostek: [wstępnie utworzone jednostki](luis-reference-prebuilt-entities.md)i własne [jednostki niestandardowe](luis-concept-entity-types.md#types-of-entities).

Po utworzeniu jednostki, która jest tworzona, należy oznaczyć tę jednostkę we wszystkich przykładach wypowiedź wszystkich zamiarów, w których się znajduje.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Dodawanie wstępnie skompilowanej jednostki do aplikacji

Typowe wstępnie skompilowane jednostki dodane do aplikacji to *Number* i *datetimeV2*. 

1. W aplikacji w sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu.
 
1. Na stronie **jednostki** wybierz pozycję **Dodaj wstępnie skompilowane jednostki**.

1. W oknie dialogowym **Dodawanie wstępnie utworzonych jednostek** wybierz **liczbę** i **datetimeV2** wstępnie skompilowanych jednostek. Następnie wybierz pozycję **Done** (Gotowe).

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie wstępnie skompilowanej jednostki](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Dodawanie prostych jednostek dla pojedynczych koncepcji

Prosta Jednostka opisuje pojedyncze koncepcje. Poniższa procedura umożliwia utworzenie jednostki, która wyodrębnia nazwy działu firmy, takie jak *zasoby ludzkie* lub *operacje*.   

1. W aplikacji wybierz sekcję **kompilacja** , a następnie wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **Utwórz nową jednostkę**.

1. W podręcznym oknie dialogowym wpisz `Location` w polu **Nazwa jednostki** wybierz pozycję **prosty** z listy **Typ jednostki** , a następnie wybierz pozycję **gotowe**.

    Po utworzeniu tej jednostki przejdź do wszystkich intencji, które mają przykład wyrażenia długości, który zawiera jednostkę. Zaznacz tekst w przykładzie wypowiedź i Oznacz tekst jako jednostkę. 

    [Lista fraz](luis-concept-feature.md) jest często używana do podwyższania poziomu sygnału prostej jednostki.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Dodawanie jednostek wyrażenia regularnego w celu uzyskania wysoce strukturalnych koncepcji

Jednostka wyrażenia regularnego służy do ściągania danych z wypowiedź na podstawie podanego wyrażenia regularnego. 

1. W aplikacji wybierz pozycję **jednostki** w lewym okienku nawigacji, a następnie wybierz pozycję **Utwórz nową jednostkę**.

1. W podręcznym oknie dialogowym wprowadź `Human resources form name` w polu **Nazwa jednostki** wybierz pozycję **wyrażenie regularne** z listy **Typ jednostki** , wprowadź `hrf-[0-9]{6}`wyrażenie regularne, a następnie wybierz pozycję **gotowe**. 

    To wyrażenie regularne dopasowuje znaki literału `hrf-`, a następnie 6 cyfr, aby reprezentować numer formularza dla formularza zasobów ludzkich.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Dodawanie jednostek złożonych do grupowania w relacji nadrzędny-podrzędny

Istnieje możliwość zdefiniowania relacji między jednostkami różnych typów przez utworzenie jednostki złożonej. W poniższym przykładzie jednostka zawiera wyrażenie regularne i prekompilowaną jednostkę nazwy.  

W `Send hrf-123456 to John Smith`wypowiedź tekst `hrf-123456` jest dopasowywany do [wyrażenia regularnego](#add-regular-expression-entities) zasobów ludzkich, a `John Smith` jest wyodrębniany przy użyciu wstępnie skompilowanej jednostki PersonName. Każda jednostka jest częścią większej jednostki nadrzędnej. 

1. W aplikacji wybierz pozycję **jednostki** z lewego obszaru nawigacji sekcji **kompilacja** , a następnie wybierz pozycję **Dodaj prekompilowaną jednostkę**.

1. Dodaj wstępnie utworzoną jednostkę **PersonName**. Aby uzyskać instrukcje, zobacz [Dodawanie wstępnie utworzonych jednostek](#add-prebuilt-entity). 

1. Wybierz pozycję **jednostki** na lewym pasku nawigacyjnym, a następnie wybierz pozycję **Utwórz nową jednostkę**.

1. W podręcznym oknie dialogowym wprowadź `SendHrForm` w polu **Nazwa jednostki** , a następnie wybierz pozycję **Composite** z listy **Typ jednostki** .

1. Wybierz pozycję **Dodaj element podrzędny** , aby dodać nowy element podrzędny.

1. W **#1 podrzędnej**wybierz **numer** jednostki z listy.

1. W **#2 podrzędnym**wybierz z listy **nazwę formularza jednostki kadrowe** . 

1. Wybierz pozycję **Done** (Gotowe).

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Dodaj wzorzec. wszystkie jednostki do przechwytywania jednostek o dowolnej postaci

[Wzorzec. wszystkie](luis-concept-entity-types.md) jednostki są prawidłowe tylko w [wzorcach](luis-how-to-model-intent-pattern.md), a nie w zamiarach. Ten typ jednostki ułatwia LUIS znalezienie końca jednostek o różnej długości i wyborze wyrazu. Ponieważ ta jednostka jest używana we wzorcu, LUIS wie, gdzie koniec jednostki znajduje się w szablonie wypowiedź.

Jeśli aplikacja ma zamiar `FindHumanResourcesForm`, wyodrębniony tytuł formularza może zakłócać prognozowanie intencji. Aby wyjaśnić, które wyrazy znajdują się w tytule formularza, użyj wzorca. dowolne w obrębie wzorca. Prognoza LUIS rozpoczyna się od wypowiedź. Po pierwsze, wypowiedź jest sprawdzana i dopasowywany dla jednostek, po znalezieniu obiektów, wzorzec jest sprawdzany i dopasowywany. 

W `Where is Request relocation from employee new to the company on the server?`wypowiedź tytuł formularza jest zalewany, ponieważ nie jest kontekstowy oczywisty, gdy tytuł się skończy i gdzie rozpoczyna się reszta wypowiedź. Tytuły mogą być dowolną kolejnością wyrazów, w tym pojedynczy wyraz, złożone frazy z interpunkcją i bezsensownie porządkowanie wyrazów. Wzorzec umożliwia utworzenie jednostki, w której można wyodrębnić pełną i dokładną jednostkę. Po znalezieniu tytułu zamiar `FindHumanResourcesForm` zostanie przewidywalna, ponieważ jest to intencja wzorca.

1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **Utwórz nową jednostkę**.

1. W oknie dialogowym **Dodawanie jednostki** wprowadź `HumanResourcesFormTitle` w polu **Nazwa jednostki** i wybierz opcję **wzorzec. dowolny** jako **Typ jednostki**.

    Aby użyć wzorca. dowolna jednostka, Dodaj wzorzec na stronie **wzorców** , w sekcji **Poprawianie wydajności aplikacji** z poprawną składnią nawiasów klamrowych, taką jak `Where is **{HumanResourcesFormTitle}** on the server?`.

    Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](luis-concept-patterns.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Dodawanie roli w celu rozróżnienia różnych kontekstów

Rola jest nazwanym podtypem na podstawie kontekstu. Jest ona dostępna we wszystkich jednostkach, w tym wstępnie utworzonych i nieopartych na maszynach jednostkach. 

Składnia dla roli jest **`{Entityname:Rolename}`** , gdzie nazwa jednostki występuje dwukropek, a następnie nazwa roli. Na przykład `Move {personName} from {Location:Origin} to {Location:Destination}`.

1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu.

1. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę). Wprowadź nazwę `Location`. Wybierz typ **prosty** i wybierz pozycję **gotowe**. 

1. Wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz nową **lokalizację** jednostki utworzoną w poprzednim kroku.

1. W polu tekstowym **Nazwa roli** wprowadź nazwę roli `Origin` a następnie wprowadź. Dodaj drugą nazwę roli `Destination`. 

    ![Zrzut ekranu przedstawiający Dodawanie roli pochodzenia do jednostki lokalizacji](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Dodaj jednostki listy dla dokładnych dopasowań

Jednostki listy reprezentują usunięty, zamknięty zestaw powiązanych wyrazów. 

W przypadku aplikacji kadrowej można wyświetlić listę wszystkich działów wraz ze wszystkimi synonimami dla działów. Nie musisz znać wszystkich wartości podczas tworzenia jednostki. Możesz dodać więcej po przejrzeniu prawdziwych wyrażenia długości użytkowników przy użyciu synonimów.

1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **Utwórz nową jednostkę**.

1. W oknie dialogowym **Dodaj jednostkę** wpisz `Department` w polu **Nazwa jednostki** i wybierz pozycję **Lista** jako **Typ jednostki**. Wybierz pozycję **Done** (Gotowe).
  
1. Strona Lista jednostek umożliwia dodawanie znormalizowanych nazw. W polu tekstowym **wartości** wprowadź nazwę działu dla listy, na przykład `HumanResources` a następnie naciśnij klawisz Enter na klawiaturze. 

1. Na prawo od znormalizowanej wartości wprowadź synonimy, naciskając klawisz Enter na klawiaturze po każdym elemencie.

1. Jeśli chcesz bardziej znormalizować elementy listy, wybierz pozycję **zalecamy** , aby wyświetlić opcje z [słownika semantycznego](luis-glossary.md#semantic-dictionary).

    ![Zrzut ekranu przedstawiający wybór funkcji zalecamy, aby wyświetlić opcje](./media/add-entities/hr-list-2.png)


1. Wybierz element z zalecanej listy, aby dodać go jako znormalizowaną wartość, lub wybierz pozycję **Dodaj wszystko** , aby dodać wszystkie elementy. 
    Możesz zaimportować wartości do istniejącej jednostki listy przy użyciu następującego formatu JSON:

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

LUIS nie pozwala na zmianę typu jednostki, ponieważ nie wie, co należy dodać lub usunąć, aby utworzyć tę jednostkę. Aby zmienić typ, lepiej jest utworzyć nową jednostkę poprawnego typu przy użyciu nieco innej nazwy. Po utworzeniu jednostki w każdym wypowiedź Usuń starą nazwę jednostki oznaczonej etykietą i Dodaj nową nazwę jednostki. Gdy wszystkie wyrażenia długości zostały ponownie oznaczone etykietami, usuń starą jednostkę. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Tworzenie wzorca na podstawie przykładu wypowiedź

Zobacz [Dodaj wzorzec z istniejącej wypowiedź na stronie intencji lub jednostki](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Uczenie aplikacji po zmianie modelu z jednostkami

Po dodaniu, zmodyfikowaniu lub usunięciu jednostek możesz [nauczyć](luis-how-to-train.md) się i [opublikować](luis-how-to-publish-app.md) aplikację, aby zmiany dotyczyły zapytań punktów końcowych. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wstępnie utworzonych jednostek, zobacz [rozpoznawanie tekstu](https://github.com/Microsoft/Recognizers-Text) . 

Aby uzyskać informacje o sposobie wyświetlania jednostki w odpowiedzi na punkt końcowy JSON, zobacz [wyodrębnianie danych](luis-concept-data-extraction.md)

Teraz, po dodaniu intencji, wyrażenia długości i jednostek, masz podstawową aplikację LUIS. Dowiedz się, jak [wyszkolić](luis-how-to-train.md), [przetestować](luis-interactive-test.md)i [opublikować](luis-how-to-publish-app.md) aplikację.
 

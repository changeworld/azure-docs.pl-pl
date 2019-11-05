---
title: Dodaj przykład wyrażenia długości-LUIS
titleSuffix: Azure Cognitive Services
description: Przykład wyrażenia długości to tekstowe przykłady pytań lub poleceń użytkownika. Aby nauczyć się Language Understanding (LUIS), musisz dodać przykład wyrażenia długości do zamiaru.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: ed6321963422cf17c858f43593092f8ffa4a1119
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467732"
---
# <a name="add-an-entity-to-example-utterances"></a>Dodawanie jednostki do przykładu wyrażenia długości 

Przykład wyrażenia długości to tekstowe przykłady pytań lub poleceń użytkownika. Aby nauczyć się Language Understanding (LUIS), musisz dodać [przykład wyrażenia długości](luis-concept-utterance.md) do [zamiaru](luis-concept-intent.md).


Zwykle najpierw należy dodać przykład wypowiedź do celu, a następnie utworzyć jednostki i etykietę wyrażenia długości na stronie **intencje** . Jeśli wolisz najpierw utworzyć jednostki, zobacz [Dodawanie jednostek](luis-how-to-add-entities.md).

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="marking-entities-in-example-utterances"></a>Oznaczanie jednostek w przykładowej wyrażenia długości

Po wybraniu tekstu w przykładzie wypowiedź do oznaczenia dla jednostki pojawia się menu rozwijane w miejscu. Użyj tego menu, aby utworzyć lub wybrać jednostkę. 

Niektóre typy jednostek, takie jak wstępnie skompilowane jednostki i jednostki wyrażenia regularnego, nie mogą być otagowane w przykładzie wypowiedź, ponieważ są otagowane automatycznie. 

## <a name="add-a-simple-entity"></a>Dodawanie prostej jednostki

Poniższa procedura polega na utworzeniu i oznaczeniu jednostki niestandardowej w ramach następujących wypowiedź na stronie **intencje** :

```text
Are there any SQL server jobs?
```

1. Wybierz `SQL server` w wypowiedź, aby oznaczyć go jako jednostkę prostą. W wyświetlonym polu rozwijanym jednostka możesz wybrać istniejącą jednostkę lub dodać nową jednostkę. Aby dodać nową jednostkę, wpisz jej nazwę `Job` w polu tekstowym, a następnie wybierz pozycję **Utwórz nową jednostkę**.

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy jednostki](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Po wybraniu słów do tagów jako jednostek:
    > * Dla pojedynczego wyrazu zaznacz go. 
    > * W przypadku zestawu dwóch lub więcej wyrazów wybierz pierwszy wyraz, a następnie końcowy wyraz.

1. W obszarze **jaki typ jednostki chcesz utworzyć?** okno podręczne, Sprawdź nazwę jednostki i wybierz typ jednostki **prostej** , a następnie wybierz pozycję **gotowe**.

    [Lista fraz](luis-concept-feature.md) jest często używana do podwyższania poziomu sygnału prostej jednostki.

## <a name="add-a-list-entity"></a>Dodawanie jednostki listy

Jednostki listy reprezentują zestaw dokładnie pasujących do wyrazów słów w Twoim systemie. 

W przypadku listy działów firmy można mieć znormalizowane wartości: `Accounting` i `Human Resources`. Każda znormalizowana nazwa ma synonimy. W przypadku działu te synonimy mogą obejmować wszystkie akronimy, liczby lub żargonuy działów. Nie musisz znać wszystkich wartości podczas tworzenia jednostki. Możesz dodać więcej po przejrzeniu prawdziwych wyrażenia długości użytkowników przy użyciu synonimów.

1. Na przykład wypowiedź na stronie **intencje** Wybierz słowo lub frazę, która ma się pojawić na nowej liście. Gdy pojawi się lista rozwijana jednostka, wprowadź nazwę nowej jednostki listy w górnym polu tekstowym, a następnie wybierz pozycję **Utwórz nową jednostkę**.   

1. W obszarze **jaki typ jednostki chcesz utworzyć?** wyskakujące okienko, nazwij jednostkę i wybierz pozycję **Lista** jako typ. Dodaj synonimy tego elementu listy, a następnie wybierz pozycję **gotowe**. 

    ![Zrzut ekranu przedstawiający wprowadzanie synonimów jednostki listy](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Możesz dodać więcej elementów listy lub więcej synonimów elementów, etykietować inne wyrażenia długości lub edytując jednostkę z **jednostek** w lewym okienku nawigacji. [Edytowanie](luis-how-to-add-entities.md#add-list-entities) jednostek zapewnia Opcje wprowadzania dodatkowych elementów z odpowiednimi synonimami lub importując listę. 

## <a name="add-a-composite-entity"></a>Dodawanie jednostki złożonej

Jednostki złożone są tworzone na podstawie istniejących **jednostek** w celu utworzenia jednostki nadrzędnej. 

Przy założeniu, że wypowiedź `Does John Smith work in Seattle?`, złożone wypowiedź może zwrócić informacje o jednostce `John Smith`, a lokalizacja `Seattle` w jednostce złożonej. Jednostki podrzędne muszą już istnieć w aplikacji i być oznaczone w przykładzie wypowiedź przed utworzeniem jednostki złożonej.

1. Aby otoczyć jednostki podrzędne do jednostki złożonej, wybierz **pierwszą** etykietę jednostki (z lewej strony) w wypowiedź jednostki złożonej. Zostanie wyświetlona lista rozwijana, aby wyświetlić opcje wyboru dla tego zaznaczenia.

1. Wybierz opcję **Zawijaj w jednostce złożonej** z listy rozwijanej. 

1. Wybierz ostatni wyraz jednostki złożonej (z prawej strony). Zwróć uwagę, że zielona linia jest zgodna z jednostką złożoną. Jest to wskaźnik wizualizacji jednostki złożonej i powinien znajdować się we wszystkich słowach w jednostce złożonej od lewej do najbardziej podrzędnej jednostki podrzędnej.

1. Wprowadź nazwę jednostki złożonej na liście rozwijanej.

    Gdy obiekty są zawijane prawidłowo, Zielona linia jest w całej frazie.

1. Sprawdź poprawność szczegółowych informacji o typie jednostki, która **ma zostać utworzona?** wyskakujące okienko, a następnie wybierz pozycję **gotowe**.

    ![Zrzut ekranu przedstawiający okno podręczne szczegółów jednostki](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Jednostka złożona jest wyświetlana ze niebieskimi wyróżnieniami dla poszczególnych jednostek i zieloną podkreśleniem dla całej jednostki złożonej. 

    ![Zrzut ekranu strony szczegółów intencji z jednostką złożoną](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Dodaj rolę jednostki do wypowiedź

Rola to nazwany podtyp jednostki, określony przez kontekst wypowiedź. Można oznaczyć jednostkę w ramach wypowiedź jako jednostkę lub wybrać rolę w tej jednostce. Każda jednostka może mieć role, w tym jednostki niestandardowe, które są obsługiwane przez maszynę (jednostki proste i jednostki złożone), nie są obsługiwane przez maszynę (wstępnie zbudowane jednostki, jednostki wyrażeń regularnych, jednostki listy). 

Dowiedz się [, jak oznaczyć wypowiedź z rolami jednostek](tutorial-entity-roles.md) z samouczka praktycznego. 

## <a name="entity-status-predictions"></a>Przewidywania stanu jednostki

Po wprowadzeniu nowego wypowiedź w portalu LUIS, wypowiedź może mieć błędy przewidywania jednostek. Błąd przewidywania jest różnicą między tym, w jaki sposób etykieta jednostki jest porównywana z LUISą jednostki. 

Różnica ta jest wizualnie reprezentowana w portalu LUIS z czerwonym podkreśleniem w wypowiedź. Czerwona podkreślenie może pojawić się w nawiasach jednostek lub poza nawiasami. 

![Zrzut ekranu przedstawiający rozbieżność prognozowania stanu jednostki](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Wybierz wyrazy, które są podkreślone na czerwono w wypowiedź. 

Jeśli występuje rozbieżność prognoz, w polu Jednostka zostanie wyświetlony **stan jednostki** z czerwonym wykrzyknikiem. Aby wyświetlić stan jednostki z informacjami o różnicy między obiektami z etykietą i przewidywaniami, wybierz pozycję **stan jednostki** , a następnie wybierz element z prawej strony.

![Zrzut ekranu przedstawiający wybór stanu jednostki](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

Czerwona linia może pojawić się w dowolnym z następujących godzin:

   * Gdy wypowiedź jest wprowadzony, ale przed oznaczeniem jednostki
   * Gdy jest stosowana etykieta jednostki
   * Gdy etykieta jednostki zostanie usunięta
   * Gdy przewidywalna jest więcej niż jedna etykieta jednostki dla tego tekstu 

Poniższe rozwiązania ułatwiają rozwiązanie rozbieżności przewidywania jednostek:

|Jednostka|Wskaźnik wizualizacji|Przewidując|Rozwiązanie|
|--|--|--|--|
|Wypowiedź wprowadzono, jednostka nie jest jeszcze oznaczona etykietą.|czerwona podkreślenie|Prognoza jest poprawna.|Oznacz jednostkę wartością przewidywaną.|
|Tekst bez etykiety|czerwona podkreślenie|Nieprawidłowe prognozowanie|Bieżący wyrażenia długości przy użyciu tej nieprawidłowej jednostki musi być przeglądany we wszystkich intencjach. Bieżące wyrażenia długości mają nieLUISy, że ten tekst jest przewidywaną jednostką.
|Poprawnie etykietowany tekst|wyróżniona niebieska jednostka, czerwona podkreślenie|Nieprawidłowe prognozowanie|Podaj więcej wyrażenia długości z prawidłową etykietą jednostki w różnych miejscach i użyciach. Bieżąca wyrażenia długości nie jest wystarczająca do uczenia LUIS, że jest to jednostka lub podobne jednostki pojawiają się w tym samym kontekście. Podobna jednostka powinna być połączona z pojedynczą jednostką, aby nie mylić LUIS. Innym rozwiązaniem jest dodanie listy frazy w celu zwiększenia znaczenia wyrazów. |
|Niepoprawnie etykietowany tekst|wyróżniona niebieska jednostka, czerwona podkreślenie|Prawidłowe prognozowanie| Podaj więcej wyrażenia długości z prawidłową etykietą jednostki w różnych miejscach i użyciach. 

## <a name="other-actions"></a>Inne akcje

Możesz wykonywać akcje na przykład wyrażenia długości jako wybraną grupę lub jako pojedynczy element. Grupy wybranych przykład wyrażenia długości zmienić menu kontekstowe nad listą. Pojedyncze elementy mogą korzystać z menu kontekstowego powyżej listy i do poszczególnych wielokropka kontekstowego na końcu każdego wiersza wypowiedź. 

### <a name="remove-entity-labels-from-utterances"></a>Usuń etykiety jednostek z wyrażenia długości

Na stronie intencje można usunąć etykiety jednostek z wypowiedźą. Jeśli jednostka nie jest wyuczenia maszynowego, nie można jej usunąć z wypowiedź. Jeśli konieczne jest usunięcie jednostki, która nie jest pouczenia maszynowego z wypowiedź, należy usunąć jednostkę z całej aplikacji. 

Aby usunąć etykietę jednostki uczenia maszynowego z wypowiedź, wybierz jednostkę w wypowiedź. Następnie wybierz pozycję **Usuń etykietę** w wyświetlonym polu rozwijanym jednostka.

### <a name="add-a-prebuilt-entity-label"></a>Dodaj wstępnie utworzoną etykietę jednostki

Po dodaniu wstępnie utworzonych jednostek do aplikacji LUIS nie ma potrzeby znakowania wyrażenia długości z tymi jednostkami. Aby dowiedzieć się więcej o wstępnie utworzonych jednostkach i sposobach ich dodawania, zobacz [Dodawanie jednostek](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-a-regular-expression-entity-label"></a>Dodaj etykietę jednostki wyrażenia regularnego

Jeśli dodasz jednostki wyrażenia regularnego do aplikacji LUIS, nie musisz oznaczać wyrażenia długości tymi jednostkami. Aby dowiedzieć się więcej o jednostkach wyrażeń regularnych i sposobach ich dodawania, zobacz [Dodawanie jednostek](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Tworzenie wzorca na podstawie wypowiedź

Zobacz [Dodaj wzorzec z istniejącej wypowiedź na stronie intencji lub jednostki](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Dodaj wzorzec. dowolna jednostka

W przypadku dodania wzorca. wszelkie jednostki do aplikacji LUIS nie można oznaczyć wyrażenia długości tymi jednostkami. Są one prawidłowe tylko w wzorcach. Aby dowiedzieć się więcej o wzorcu. dowolnych jednostek i sposobach ich dodawania, zobacz [Dodawanie jednostek](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Uczenie aplikacji po zmianie modelu za pomocą wyrażenia długości

Po dodaniu, zmodyfikowaniu lub usunięciu wyrażenia długości, [nauczenie](luis-how-to-train.md) i [opublikowanie](luis-how-to-publish-app.md) aplikacji w celu uwzględnienia zmian w zapytaniach punktów końcowych. 

## <a name="next-steps"></a>Następne kroki

Po etykietowaniu wyrażenia długości w Twoich **intencjach**możesz teraz utworzyć [jednostkę złożoną](luis-how-to-add-entities.md).

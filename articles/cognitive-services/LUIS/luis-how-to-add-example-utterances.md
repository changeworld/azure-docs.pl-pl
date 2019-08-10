---
title: Dodaj przykład wyrażenia długości-LUIS
titleSuffix: Azure Cognitive Services
description: Przykład wypowiedzi są przykładami tekst pytania użytkownika lub poleceń. Aby nauczyć Language Understanding (LUIS), należy dodać wypowiedzi przykład do intencji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 1e170b86f573112cc5bc8dddd6f080921ef29d2d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932948"
---
# <a name="add-an-entity-to-example-utterances"></a>Dodawanie jednostki do przykładu wypowiedzi 

Przykład wypowiedzi są przykładami tekst pytania użytkownika lub poleceń. Do nauki Language Understanding (LUIS), musisz dodać [wypowiedzi przykład](luis-concept-utterance.md) do [intencji](luis-concept-intent.md).

Zwykle najpierw należy dodać przykład wypowiedź do celu, a następnie utworzyć jednostki i etykietę wyrażenia długości na stronie **intencje** . Jeśli czy raczej najpierw utworzyć jednostki, zobacz [Dodaj jednostki](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Oznaczanie jednostek w przykładzie wypowiedzi

Po wybraniu tekstu w wypowiedź przykładu, aby oznaczyć dla jednostki, pojawi się podręczne menu w miejscu. Użyj tego menu, aby utworzyć lub wybrać jednostkę. 

Niektórych typów jednostek, takich jak wstępnie utworzonych jednostek i obiektów wyrażeń regularnych nie może być oznaczane w wypowiedź przykład, ponieważ są automatycznie oznaczone. 

## <a name="add-a-simple-entity"></a>Dodaj jednostkę prosty

Poniższa procedura polega na utworzeniu i oznaczeniu jednostki niestandardowej w ramach następujących wypowiedź na stronie **intencje** :

```text
Are there any SQL server jobs?
```

1. Wybierz `SQL server` w wypowiedź, aby oznaczyć go jako prosty jednostki. W polu listy rozwijanej jednostki, które pojawia się możesz wybrać istniejącej jednostki lub Dodaj nową jednostkę. Aby dodać nową jednostkę, wpisz jej nazwę `Job` w polu tekstowym, a następnie wybierz pozycję **Utwórz nową jednostkę**.

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy jednostki](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Podczas wybierania słów w tagu jako jednostki:
    > * Dla pojedynczego słowa po prostu wybierz ją. 
    > * W przypadku zestawu dwóch lub więcej wyrazów wybierz pierwszy wyraz, a następnie końcowy wyraz.

1. W **jakiego typu jednostki, czy chcesz utworzyć?** okno podręczne, sprawdź nazwę jednostki i wybierz **proste** typu jednostki, a następnie wybierz **gotowe**.

    A [listy fraz](luis-concept-feature.md) jest najczęściej używany do zwiększania sygnał proste jednostki.

## <a name="add-a-list-entity"></a>Dodaj jednostkę listy

Jednostki listy reprezentują zestaw dokładnie pasujących do wyrazów słów w Twoim systemie. 

Wykaz działu firmy mogą mieć znormalizowane wartości: `Accounting` i `Human Resources`. Nazwa każdego znormalizowane ma synonimów. Dla działu dodawanie tych synonimów może zawierać żadnych akronimów działu, liczby lub żargonu. Nie trzeba znać wszystkie wartości, po utworzeniu jednostki. Możesz dodać więcej po zapoznaniu się z wypowiedzi rzeczywistego użytkownika przy użyciu synonimów.

1. Na przykład wypowiedź na stronie **intencje** Wybierz słowo lub frazę, która ma się pojawić na nowej liście. Gdy pojawi się lista rozwijana jednostka, wprowadź nazwę nowej jednostki listy w górnym polu tekstowym, a następnie wybierz pozycję **Utwórz nową jednostkę**.   

1. W **jakiego typu jednostki, czy chcesz utworzyć?** okno podręczne, nazwa podmiotu i wybierz **listy** jako typu. Dodawanie synonimów ten element listy, a następnie wybierz **gotowe**. 

    ![Zrzut ekranu przedstawiający wprowadzanie listy jednostek synonimy](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Można dodawać więcej elementów listy lub więcej elementów synonimy etykietowania innych wypowiedzi lub edytowania jednostek z **jednostek** w nawigacji po lewej stronie. [Edytowanie](luis-how-to-add-entities.md#add-list-entities) jednostek oferuje opcje wprowadzania dodatkowych elementów z odpowiadającego synonimy lub importowania listy. 

## <a name="add-a-composite-entity"></a>Dodawanie jednostki złożonej

Jednostki złożone są tworzone na podstawie istniejących **jednostek** w celu utworzenia jednostki nadrzędnej. 

Przy założeniu wypowiedź `Does John Smith work in Seattle?`, złożone wypowiedź może zwrócić informacje o jednostce dla nazwy `John Smith`pracownika i lokalizacji `Seattle` w jednostce złożonej. Jednostki podrzędne muszą już istnieć w aplikacji i być oznaczone w przykładzie wypowiedź przed utworzeniem jednostki złożonej.

1. Aby otoczyć jednostki podrzędne do jednostki złożonej, wybierz **pierwszą** etykietę jednostki (z lewej strony) w wypowiedź jednostki złożonej. Zostanie wyświetlona lista rozwijana, aby wyświetlić opcje wyboru dla tego zaznaczenia.

1. Wybierz opcję **Zawijaj w jednostce złożonej** z listy rozwijanej. 

1. Wybierz ostatni wyraz złożony jednostki (najdalej z prawej strony). Należy zauważyć, że zieloną linię zgodna złożonego jednostki. Jest to wskaźnik wizualizacji jednostki złożonej i powinien znajdować się we wszystkich słowach w jednostce złożonej od lewej do najbardziej podrzędnej jednostki podrzędnej.

1. Wprowadź nazwę jednostki złożonego na liście rozwijanej.

    Jednostki są poprawnie zawijać, zieloną linię podlega cała fraza.

1. Sprawdź szczegóły jednostki złożonego przy **jakiego typu jednostki, czy chcesz utworzyć?** oknie podręcznym polu Wybierz **gotowe**.

    ![Zrzut ekranu jednostki szczegóły wyskakujących](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Złożone jednostki wyświetla zarówno niebieski wyróżnienia poszczególnymi jednostkami i zielonym podkreśleniem dla całego obiektu złożonego. 

    ![Strona Szczegóły zrzut ekranu z opcjami, z jednostką złożone](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Dodaj rolę jednostki do wypowiedź

Rola to nazwany podtyp jednostki, określony przez kontekst wypowiedź. Można oznaczyć jednostkę w ramach wypowiedź jako jednostkę lub wybrać rolę w tej jednostce. Każda jednostka może mieć role, w tym jednostki niestandardowe, które są obsługiwane przez maszynę (jednostki proste i jednostki złożone), nie są obsługiwane przez maszynę (wstępnie zbudowane jednostki, jednostki wyrażeń regularnych, jednostki listy). 

Dowiedz się [, jak oznaczyć wypowiedź z rolami jednostek](tutorial-entity-roles.md) z samouczka praktycznego. 

## <a name="entity-status-predictions"></a>Prognozy stanu jednostki

Po wprowadzeniu nowych wypowiedź w portalu usługi LUIS wypowiedź mogą występować błędy prognozowania jednostki. Błąd prognozowania różnią się od sposobu jednostki jest oznaczona etykietą w porównaniu z jak przewidziało jednostki usługi LUIS. 

Różnica ta jest przedstawiane w portalu usługi LUIS czerwoną linią wypowiedź. Czerwonym podkreśleniem, może pojawić się w nawiasach jednostki lub na zewnątrz nawiasy kwadratowe. 

![Zrzut ekranu jednostki Stan prognozowania rozbieżność](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Wybierz wyrazy, które zostaną podkreślone na czerwono na wypowiedź. 

Wyświetla pole jednostki **stan jednostki** z czerwony wykrzyknik różnic prognozy. Aby wyświetlić stan jednostki przy użyciu informacji na temat różnic między jednostkami etykietami oraz dostęp do przewidywanych, wybierz **stan jednostki** wybierz element z prawej strony.

![Zrzut ekranu przedstawiający wybór stanu jednostki](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

Czerwona linia może znajdować się w miejscach występowania dowolnego z następujących godzinach:

   * Po wprowadzeniu wypowiedź, ale przed jednostki jest oznaczona etykietą
   * Po zastosowaniu etykiety jednostki
   * Po usunięciu etykieta jednostki
   * Gdy przewiduje więcej niż jednej etykiety jednostek dla tego tekstu 

Następujące rozwiązania pomóc rozwiązać za pomocą jednostki prognozowania:

|Jednostka|Wizualny wskaźnik informujący|Prognozy|Rozwiązanie|
|--|--|--|--|
|Wypowiedź wprowadzona, jednostki nie jest oznaczony jako jeszcze.|czerwonym podkreśleniem|Prognozowanie jest poprawna.|Etykieta jednostki z wartością prognozowaną.|
|Bez etykiety tekstowe|czerwonym podkreśleniem|Niepoprawne prognoz|Bieżący wypowiedzi korzystające z tej jednostki niepoprawne konieczne przeglądane we wszystkich intencji. Bieżącego wypowiedzi mają mistaught LUIS, czy ten tekst jest przewidywana jednostki.
|Niepoprawnie oznaczone tekstu|Zaznacz jednostki niebieski, czerwoną linią|Niepoprawne prognoz|Podaj więcej wypowiedzi z jednostką poprawnie oznaczony w różnych miejscach i użycia. Bieżący wypowiedzi są nie są wystarczające do nauki LUIS, że to jest jednostka lub podobne jednostki są wyświetlane w tym samym kontekście. Podobne jednostki należy połączyć w pojedynczej jednostki, dzięki czemu usługa LUIS jest mylące. Innym rozwiązaniem jest dodanie listy frazę do poprawienia znaczenie słowa. |
|Niepoprawnie oznaczone tekstu|Zaznacz jednostki niebieski, czerwoną linią|Poprawne prognoz| Podaj więcej wypowiedzi z jednostką poprawnie oznaczony w różnych miejscach i użycia. 

## <a name="other-actions"></a>Inne akcje

Na przykład wypowiedzi można wykonać akcje, jako wybraną grupę lub jako pojedynczy element. Grupy wybranych przykład wypowiedzi zmienić menu kontekstowe powyżej listy. Pojedyncze elementy może używać zarówno menu kontekstowe powyżej listy i poszczególnych wielokropka kontekstowych na końcu każdego wiersza wypowiedź. 

### <a name="remove-entity-labels-from-utterances"></a>Usuń jednostki etykiety wypowiedzi

Na stronie intencje można usunąć etykiety jednostek z wypowiedźą. Jeśli jednostka nie jest maszyny do opanowania, nie można usunąć z wypowiedź. Jeśli musisz usunąć jednostka przedstawiono maszyny z wypowiedź musisz usunąć jednostkę z całej aplikacji. 

Aby usunąć etykietę maszyny do opanowania jednostki wypowiedź, wybrać jednostkę wypowiedź. Następnie wybierz pozycję **Usuń etykietę** w wyświetlonym polu listy rozwijanej jednostki.

### <a name="add-a-prebuilt-entity-label"></a>Dodaj wstępnie utworzoną etykietę jednostki

Po dodaniu ze wstępnie utworzonych jednostek z aplikacją usługi LUIS, nie trzeba wypowiedzi tagu z tymi jednostkami. Aby dowiedzieć się więcej na temat wstępnie utworzonych jednostek i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-a-regular-expression-entity-label"></a>Dodaj etykietę jednostki wyrażenia regularnego

Jeśli dodasz jednostek wyrażenia regularnego z aplikacją usługi LUIS, nie trzeba wypowiedzi tagu z tymi jednostkami. Aby dowiedzieć się więcej na temat jednostek wyrażenia regularnego i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Tworzenie wzorca z wypowiedź

Zobacz [Dodaj wzorca od istniejących wypowiedź przeznaczenie lub jednostki strony](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Dodaj wzorzec. dowolna jednostka

Jeśli dodasz jednostek pattern.any z aplikacją usługi LUIS nie etykiety wypowiedzi z tymi jednostkami. Tylko są prawidłowe we wzorcach. Aby dowiedzieć się więcej na temat jednostek pattern.any i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Uczenie aplikacji po zmianie modelu z wypowiedzi

Po Dodawanie, edytowanie lub usuwanie wypowiedzi, [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Następne kroki

Po etykietowaniu wyrażenia długości w Twoich **intencjach**możesz teraz utworzyć [jednostkę złożoną](luis-how-to-add-entities.md).

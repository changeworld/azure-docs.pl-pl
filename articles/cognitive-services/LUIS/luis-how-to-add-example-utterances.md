---
title: Dodawanie przykładowych wypowiedzi
titleSuffix: Language Understanding - Azure Cognitive Services
description: Przykład wypowiedzi są przykładami tekst pytania użytkownika lub poleceń. Aby nauczyć Language Understanding (LUIS), należy dodać wypowiedzi przykład do intencji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 0d3123b1e0238a1907b5ad3d487b92a7919ff181
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524263"
---
# <a name="add-an-entity-to-example-utterances"></a>Dodawanie jednostki do przykładu wypowiedzi 

Przykład wypowiedzi są przykładami tekst pytania użytkownika lub poleceń. Do nauki Language Understanding (LUIS), musisz dodać [wypowiedzi przykład](luis-concept-utterance.md) do [intencji](luis-concept-intent.md).

Zwykle najpierw Dodaj wypowiedź przykład intencji, a następnie tworzenia jednostek i wypowiedzi etykietę, na stronie opcji. Jeśli czy raczej najpierw utworzyć jednostki, zobacz [Dodaj jednostki](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Oznaczanie jednostek w przykładzie wypowiedzi

Po wybraniu tekstu w wypowiedź przykładu, aby oznaczyć dla jednostki, pojawi się podręczne menu w miejscu. Użyj tego menu, aby utworzyć lub wybrać jednostkę. 

Niektórych typów jednostek, takich jak wstępnie utworzonych jednostek i obiektów wyrażeń regularnych nie może być oznaczane w wypowiedź przykład, ponieważ są automatycznie oznaczone. 

## <a name="add-a-simple-entity"></a>Dodaj jednostkę prosty

Poniższa procedura służy do tworzenia i tag jednostki niestandardowej w ramach następujących wypowiedź na stronie opcji:

```text
Are there any SQL server jobs?
```

1. Wybierz `SQL server` w wypowiedź, aby oznaczyć go jako prosty jednostki. W polu listy rozwijanej jednostki, które pojawia się możesz wybrać istniejącej jednostki lub Dodaj nową jednostkę. Aby dodać nową jednostkę, wpisz jego nazwę `Job` w polu tekstowym, a następnie wybierz pozycję **Utwórz nową jednostkę**.

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy jednostki](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Podczas wybierania słów w tagu jako jednostki:
    > * Dla pojedynczego słowa po prostu wybierz ją. 
    > * Aby uzyskać zestaw dwóch lub więcej wyrazów wybierz na początku, a następnie na końcu zestawu.

1. W **jakiego typu jednostki, czy chcesz utworzyć?** okno podręczne, sprawdź nazwę jednostki i wybierz **proste** typu jednostki, a następnie wybierz **gotowe**.

    A [listy fraz](luis-concept-feature.md) jest najczęściej używany do zwiększania sygnał proste jednostki.

## <a name="add-a-list-entity"></a>Dodaj jednostkę listy

Lista jednostek reprezentują zestaw dopasowania tekstu do dokładnego dopasowania słów powiązane w Twoim systemie. 

Wykaz działu firmy mogą mieć znormalizowane wartości: `Accounting` i `Human Resources`. Nazwa każdego znormalizowane ma synonimów. Dla działu dodawanie tych synonimów może zawierać żadnych akronimów działu, liczby lub żargonu. Nie trzeba znać wszystkie wartości, po utworzeniu jednostki. Możesz dodać więcej po zapoznaniu się z wypowiedzi rzeczywistego użytkownika przy użyciu synonimów.

1. W polu wypowiedź przykład na **intencji** wybierz wyraz lub frazę, która ma nowej listy. Gdy pojawi się lista rozwijana jednostki, wprowadź nazwę dla nowej jednostki listy w górnym polu tekstowym, a następnie wybierz **Utwórz nową jednostkę**.   

1. W **jakiego typu jednostki, czy chcesz utworzyć?** okno podręczne, nazwa podmiotu i wybierz **listy** jako typu. Dodawanie synonimów ten element listy, a następnie wybierz **gotowe**. 

    ![Zrzut ekranu przedstawiający wprowadzanie listy jednostek synonimy](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Można dodawać więcej elementów listy lub więcej elementów synonimy etykietowania innych wypowiedzi lub edytowania jednostek z **jednostek** w nawigacji po lewej stronie. [Edytowanie](luis-how-to-add-entities.md#add-list-entities) jednostek oferuje opcje wprowadzania dodatkowych elementów z odpowiadającego synonimy lub importowania listy. 

## <a name="add-composite-entity"></a>Dodawanie jednostki złożonej

Złożone jednostki są tworzone na podstawie istniejących **jednostek** do obiektu nadrzędnego. 

Zakładając, że wypowiedź `Does John Smith work in Seattle?`, złożonego wypowiedź może zwrócić informacje o jednostce o nazwie pracowników `John Smith`i lokalizację `Seattle` w jednostce złożone. Jednostki podrzędne musi już istnieć w aplikacji i oznaczane w wypowiedź przykład przed utworzeniem jednostki złożonego.

1. Aby zawijać obiektów podrzędnych do złożonych jednostki, wybierz pozycję **pierwszy** etykietą jednostki (skrajnie po lewej) w polu wypowiedź dla obiektu złożonego. Listy rozwijanej pojawi się opcji do wyboru.

1. Wybierz **opakować w jednostce złożone** z listy rozwijanej. 

1. Wybierz ostatni wyraz złożony jednostki (najdalej z prawej strony). Należy zauważyć, że zieloną linię zgodna złożonego jednostki. To jest wizualny wskaźnik informujący złożonego jednostki i powinny znajdować się w wszystkie wyrazy w jednostce złożone z jednostki podrzędne skrajnie po lewej na jednostce podrzędnej najdalej z prawej strony.

1. Wprowadź nazwę jednostki złożonego na liście rozwijanej.

    Jednostki są poprawnie zawijać, zieloną linię podlega cała fraza.

1. Sprawdź szczegóły jednostki złożonego przy **jakiego typu jednostki, czy chcesz utworzyć?** oknie podręcznym polu Wybierz **gotowe**.

    ![Zrzut ekranu jednostki szczegóły wyskakujących](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Złożone jednostki wyświetla zarówno niebieski wyróżnienia poszczególnymi jednostkami i zielonym podkreśleniem dla całego obiektu złożonego. 

    ![Strona Szczegóły zrzut ekranu z opcjami, z jednostką złożone](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>Dodawanie jednostki hierarchicznej

**Hierarchiczna jednostki po pewnym czasie zostaną wycofane. Użyj [ról jednostki](luis-concept-roles.md) Aby określić podtypy jednostki, zamiast hierarchiczne jednostek.**

Hierarchiczna jednostka jest kategoria kontekstowe nauczony i pod względem koncepcyjnym powiązanych jednostek. W poniższym przykładzie jednostki zawiera początkowe i docelowe lokalizacje. 

W polu wypowiedź `Move John Smith from Seattle to Cairo`, Seattle, jest to lokalizacja źródła i Kair jest lokalizacja docelowa. Każda lokalizacja jest kontekstowe różnych i nauczony kolejność słów i wybór o słowo w wypowiedź.

1. Na stronie intencji wypowiedź, wybierz `Seattle`, wprowadź nazwę jednostki `Location`, a następnie naciśnij klawisz Enter na klawiaturze.

1. W **jakiego typu jednostki, czy chcesz utworzyć?** podręcznym wybierz pozycję _hierarchiczne_ dla **typu jednostki**, następnie dodać `Origin` i `Destination` jako elementy podrzędne, a następnie wybierz **gotowe**.

    ![Strona Szczegóły zrzut ekranu z opcjami, z jednostką ToLocation wyróżniony](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. Wyraz wypowiedź został oznaczony za pomocą hierarchicznych obiektu nadrzędnego. Musisz przypisać słowo na jednostce podrzędnej. Wróć do wypowiedź na stronie szczegółów intencji. Zaznacz wyraz, a następnie z listy rozwijanej wybierz nazwę jednostki, utworzonej i postępuj zgodnie z menu po prawej stronie wybranie jednostki podrzędne poprawne.

    >[!CAUTION]
    >Nazwy jednostek podrzędny musi być unikatowa we wszystkich jednostek w jednej aplikacji. Dwoma obiektami hierarchiczne nie może zawierać jednostki podrzędne o takiej samej nazwie. 

## <a name="add-entitys-role-to-utterance"></a>Dodaj rolę jednostki wypowiedź

Rola jest podtypem o nazwie podmiotu, który jest ustalany na podstawie kontekstu wypowiedź. Zaznacz jednostkę w ramach wypowiedź jako jednostki lub wybierz rolę w ramach tej jednostki. Każda jednostka może mieć ról w tym jednostek niestandardowych, które są przedstawiono maszyny (jednostek proste i złożone jednostek), nie omówiono maszyny (wstępnie utworzonych jednostek, jednostek wyrażenia regularnego listy jednostek). 

Dowiedz się, [sposobu oznaczania wypowiedź z rolami jednostki](tutorial-entity-roles.md) z praktycznego samouczka. 

## <a name="entity-status-predictions"></a>Prognozy stanu jednostki

Po wprowadzeniu nowych wypowiedź w portalu usługi LUIS wypowiedź mogą występować błędy prognozowania jednostki. Błąd prognozowania różnią się od sposobu jednostki jest oznaczona etykietą w porównaniu z jak przewidziało jednostki usługi LUIS. 

Różnica ta jest przedstawiane w portalu usługi LUIS czerwoną linią wypowiedź. Czerwonym podkreśleniem, może pojawić się w nawiasach jednostki lub na zewnątrz nawiasy kwadratowe. 

![Zrzut ekranu jednostki Stan prognozowania rozbieżność](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Wybierz wyrazy, które zostaną podkreślone na czerwono na wypowiedź. 

Wyświetla pole jednostki **stan jednostki** z czerwony wykrzyknik różnic prognozy. Aby wyświetlić stan jednostki przy użyciu informacji na temat różnic między jednostkami etykietami oraz dostęp do przewidywanych, wybierz **stan jednostki** wybierz element z prawej strony.

![Zrzut ekranu jednostki stan zaznaczenia](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

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

> [!Note]
> Po czerwoną otoczkę wokół etykietami zamiar w wierszu wypowiedź przykładu, [Błąd prognozowania intencji](luis-how-to-add-intents.md#intent-prediction-discrepancy-errors) wystąpił. Należy go poprawić. 

## <a name="other-actions"></a>Inne akcje

Na przykład wypowiedzi można wykonać akcje, jako wybraną grupę lub jako pojedynczy element. Grupy wybranych przykład wypowiedzi zmienić menu kontekstowe powyżej listy. Pojedyncze elementy może używać zarówno menu kontekstowe powyżej listy i poszczególnych wielokropka kontekstowych na końcu każdego wiersza wypowiedź. 

### <a name="remove-entity-labels-from-utterances"></a>Usuń jednostki etykiety wypowiedzi

Usuń z etykiety maszyny do opanowania jednostki, z wypowiedź na stronie opcji. Jeśli jednostka nie jest maszyny do opanowania, nie można usunąć z wypowiedź. Jeśli musisz usunąć jednostka przedstawiono maszyny z wypowiedź musisz usunąć jednostkę z całej aplikacji. 

Aby usunąć etykietę maszyny do opanowania jednostki wypowiedź, wybrać jednostkę wypowiedź. Następnie wybierz pozycję **Usuń etykietę** w wyświetlonym polu listy rozwijanej jednostki.

### <a name="add-prebuilt-entity-label"></a>Dodaj etykietę wstępnie utworzone jednostki

Po dodaniu ze wstępnie utworzonych jednostek z aplikacją usługi LUIS, nie trzeba wypowiedzi tagu z tymi jednostkami. Aby dowiedzieć się więcej na temat wstępnie utworzonych jednostek i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-regular-expression-entity-label"></a>Dodaj etykietę jednostki wyrażeń regularnych

Jeśli dodasz jednostek wyrażenia regularnego z aplikacją usługi LUIS, nie trzeba wypowiedzi tagu z tymi jednostkami. Aby dowiedzieć się więcej na temat jednostek wyrażenia regularnego i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Tworzenie wzorca z wypowiedź

Zobacz [Dodaj wzorca od istniejących wypowiedź przeznaczenie lub jednostki strony](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Dodaj jednostkę pattern.any

Jeśli dodasz jednostek pattern.any z aplikacją usługi LUIS nie etykiety wypowiedzi z tymi jednostkami. Tylko są prawidłowe we wzorcach. Aby dowiedzieć się więcej na temat jednostek pattern.any i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Uczenie aplikacji po zmianie modelu z wypowiedzi

Po Dodawanie, edytowanie lub usuwanie wypowiedzi, [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

Po etykietowanie wypowiedzi w Twoje intencje, możesz teraz utworzyć [złożonego jednostki](luis-how-to-add-entities.md).

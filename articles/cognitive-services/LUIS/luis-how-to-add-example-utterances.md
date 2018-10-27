---
title: Dodawanie wypowiedzi przykładu w aplikacjach usługi LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dodawanie wypowiedzi w aplikacjach Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/24/2018
ms.author: diberry
ms.openlocfilehash: caf8628569481149ee41e8253f4759e15e49150f
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139127"
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
Does John Smith work in Seattle?
```

1. Wybierz `Seattle` w wypowiedź, aby oznaczyć go jako prosty jednostki.

    [![Zrzut ekranu przedstawiający zaznaczania tekstu w wypowiedź proste jednostki](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)

    > [!NOTE]
    > Podczas wybierania słów w tagu jako jednostki:
    > * Dla pojedynczego słowa po prostu wybierz ją. 
    > * Aby uzyskać zestaw dwóch lub więcej wyrazów wybierz na początku, a następnie na końcu zestawu.

1. W polu listy rozwijanej jednostki, które pojawia się możesz wybrać istniejącej jednostki lub Dodaj nową jednostkę. Aby dodać nową jednostkę, wpisz jego nazwę w polu tekstowym, a następnie wybierz **Utwórz nową jednostkę**. 

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy jednostki](./media/luis-how-to-add-example-utterances/hr-create-simple-2.png)

1. W **jakiego typu jednostki, czy chcesz utworzyć?** okno podręczne, sprawdź nazwę jednostki i wybierz **proste** typu jednostki, a następnie wybierz **gotowe**.

    A [listy fraz](luis-concept-feature.md) jest najczęściej używany do zwiększania sygnał proste jednostki.

## <a name="add-a-list-entity"></a>Dodaj jednostkę listy

Lista jednostek reprezentują stały, zamknięte zestaw (pasuje do tekstu do dokładnego dopasowania) powiązanych słów w Twoim systemie. 

Wykaz działu firmy mogą mieć znormalizowane wartości: `Accounting` i `Human Resources`. Nazwa każdego znormalizowane ma synonimów. Dla działu dodawanie tych synonimów może zawierać żadnych akronimów działu, liczby lub żargonu. Nie trzeba znać wszystkie wartości, po utworzeniu jednostki. Możesz dodać więcej po zapoznaniu się z wypowiedzi rzeczywistego użytkownika przy użyciu synonimów.

1. Na liście wypowiedź przykład dla określonych wypowiedź zaznacz wyraz lub frazę w nowej listy. Następnie wprowadź nazwę listy w górnym polu tekstowym, a następnie wybierz **Utwórz nową jednostkę**.   

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy jednostki listy](./media/luis-how-to-add-example-utterances/hr-create-list-1.png)


1. W **jakiego typu jednostki, czy chcesz utworzyć?** okno podręczne, nazwa podmiotu i wybierz **listy** jako typu. Dodawanie synonimów ten element listy, a następnie wybierz **gotowe**. 

    ![Zrzut ekranu przedstawiający wprowadzanie listy jednostek synonimy](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Można dodawać więcej elementów listy lub więcej elementów synonimy etykietowania innych wypowiedzi lub edytowania jednostek z **jednostek** w nawigacji po lewej stronie. [Edytowanie](luis-how-to-add-entities.md#add-list-entities) jednostek oferuje opcje wprowadzania dodatkowych elementów z odpowiadającego synonimy lub importowania listy. 

## <a name="add-composite-entity"></a>Dodawanie jednostki złożonej

Złożone jednostki są tworzone na podstawie istniejących **jednostek** do obiektu nadrzędnego. 

Zakładając, że wypowiedź `Does John Smith work in Seattle?`, obiekt nadrzędny pojedynczego złożonego wypowiedź mogą być zwracane informacje jednostki nazwisko pracownika i lokalizacji. 

Nazwisko pracownika, Jan Kowalski jest wstępnie [personName](luis-reference-prebuilt-person.md) jednostki. Lokalizacja, Seattle, jest proste jednostki niestandardowej. Po tych dwóch jednostek są tworzone i oznaczone w przykładzie wypowiedź, te jednostki może zostać zawinięty w jednostce złożone. 

1. Aby zawijać poszczególne jednostki do złożonego, wybierz pozycję **pierwszy** etykietą jednostki (skrajnie po lewej) w polu wypowiedź dla obiektu złożonego. Zostanie wyświetlona lista listy rozwijanej wyświetlane opcje do wyboru.

1. Wybierz **opakować złożonego jednostki** z listy rozwijanej. 

    ![Zrzut ekranu wybierz "Wrap w jednostce złożone"](./media/luis-how-to-add-example-utterances/hr-create-composite-1.png)

1. Wybierz ostatni wyraz złożony jednostki (najdalej z prawej strony). Należy zauważyć, że zieloną linię zgodna złożonego jednostki.

1. Wprowadź nazwę jednostki złożonego na liście rozwijanej.

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy jednostki](./media/luis-how-to-add-example-utterances/hr-create-composite-2.png)

    Jednostki są poprawnie zawijać, zieloną linię podlega cała fraza.

1. Sprawdź szczegóły jednostki złożonego przy **jakiego typu jednostki, czy chcesz utworzyć?** oknie podręcznym polu Wybierz **gotowe**.

    ![Zrzut ekranu jednostki szczegóły wyskakujących](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Złożone jednostki wyświetla zarówno niebieski wyróżnienia poszczególnymi jednostkami i zielonym podkreśleniem dla całego obiektu złożonego. 

    ![Strona Szczegóły zrzut ekranu z opcjami, z jednostką złożone](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>Dodawanie jednostki hierarchicznej

Hierarchiczna jednostka jest kategoria kontekstowe nauczony i pod względem koncepcyjnym powiązanych jednostek. W poniższym przykładzie jednostki zawiera początkowe i docelowe lokalizacje. 

W polu wypowiedź `Move John Smith from Seattle to Cairo`, Seattle, jest to lokalizacja źródła i Kair jest lokalizacja docelowa. Każda lokalizacja jest kontekstowe różnych i nauczony kolejność słów i wybór o słowo w wypowiedź.

1. Na stronie intencji wypowiedź, wybierz `Seattle`, wprowadź nazwę jednostki `Location`, a następnie naciśnij klawisz Enter na klawiaturze.

    ![Zrzut ekranu z utworzyć hierarchiczne jednostki etykietowania okno dialogowe](./media/luis-how-to-add-example-utterances/hr-hier-1.png)

1. W **jakiego typu jednostki, czy chcesz utworzyć?** podręcznym wybierz pozycję _hierarchiczne_ dla **typu jednostki**, następnie dodać `Origin` i `Destination` jako elementy podrzędne, a następnie wybierz **gotowe**.

    ![Strona Szczegóły zrzut ekranu z opcjami, z jednostką ToLocation wyróżniony](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. Wyraz wypowiedź został oznaczony za pomocą hierarchicznych obiektu nadrzędnego. Musisz przypisać słowo na jednostce podrzędnej. Wróć do wypowiedź na stronie szczegółów intencji. Zaznacz wyraz, a następnie z listy rozwijanej wybierz nazwę jednostki, utworzonej i postępuj zgodnie z menu po prawej stronie wybranie jednostki podrzędne poprawne.

    ![Strona Szczegóły zrzut ekranu z opcjami, z jednostką ToLocation wyróżniony](./media/luis-how-to-add-example-utterances/hr-hier-3.png)

    >[!CAUTION]
    >Nazwy jednostek podrzędny musi być unikatowa we wszystkich jednostek w jednej aplikacji. Dwoma obiektami hierarchiczne nie może zawierać jednostki podrzędne o takiej samej nazwie. 

## <a name="entity-status-predictions"></a>Prognozy stanu jednostki

Po wprowadzeniu nowych wypowiedź w portalu usługi LUIS wypowiedź mogą występować błędy prognozowania jednostki. Błąd prognozowania różnią się od sposobu jednostki jest oznaczona etykietą w porównaniu z jak przewidziało jednostki usługi LUIS. 

Różnica ta jest przedstawiane w portalu usługi LUIS czerwoną linią wypowiedź. Czerwonym podkreśleniem, może pojawić się w nawiasach jednostki lub na zewnątrz nawiasy kwadratowe. 

![Zrzut ekranu jednostki Stan prognozowania rozbieżność](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Wybierz wyrazy, które zostaną podkreślone na czerwono na wypowiedź. 

Wyświetla pole jednostki **stan jednostki** z czerwony wykrzyknik różnic prognozy. Aby wyświetlić stan jednostki przy użyciu informacji na temat różnic między jednostkami etykietami oraz dostęp do przewidywanych, wybierz **stan jednostki** wybierz element z prawej strony.

![Zrzut ekranu jednostki Stan prognozowania rozbieżność](./media/luis-how-to-add-example-utterances/entity-status.png)

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

Usuń z etykiety maszyny do opanowania jednostki, z wypowiedź na stronie opcji. Jeśli jednostka nie jest maszyny do opanowania, nie można usunąć z wypowiedź. Jeśli musisz usunąć jednostka przedstawiono maszyny z wypowiedź musisz usunąć jednostkę z całej aplikacji. 

Aby usunąć etykietę maszyny do opanowania jednostki wypowiedź, wybrać jednostkę wypowiedź. Następnie wybierz pozycję **Usuń etykietę** w wyświetlonym polu listy rozwijanej jednostki.

![Zrzut ekranu z opcjami strony szczegółów, usunąć etykietę, wyróżniony](./media/luis-how-to-add-example-utterances/remove-label.png) 

### <a name="add-prebuilt-entity-label"></a>Dodaj etykietę wstępnie utworzone jednostki

Po dodaniu ze wstępnie utworzonych jednostek z aplikacją usługi LUIS, nie trzeba wypowiedzi tagu z tymi jednostkami. Aby dowiedzieć się więcej na temat wstępnie utworzonych jednostek i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-prebuilt-entity).

### <a name="add-regular-expression-entity-label"></a>Dodaj etykietę jednostki wyrażeń regularnych

Jeśli dodasz jednostek wyrażenia regularnego z aplikacją usługi LUIS, nie trzeba wypowiedzi tagu z tymi jednostkami. Aby dowiedzieć się więcej na temat jednostek wyrażenia regularnego i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-regular-expression-entities).


### <a name="create-a-pattern-from-an-utterance"></a>Tworzenie wzorca z wypowiedź

Zobacz [Dodaj wzorca od istniejących wypowiedź przeznaczenie lub jednostki strony](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Dodaj jednostkę pattern.any

Jeśli dodasz jednostek pattern.any z aplikacją usługi LUIS nie etykiety wypowiedzi z tymi jednostkami. Tylko są prawidłowe we wzorcach. Aby dowiedzieć się więcej na temat jednostek pattern.any i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-patternany-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Uczenie aplikacji po zmianie modelu z wypowiedzi

Po Dodawanie, edytowanie lub usuwanie wypowiedzi, [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

Po etykietowanie wypowiedzi w Twoje intencje, możesz teraz utworzyć [złożonego jednostki](luis-how-to-add-entities.md).

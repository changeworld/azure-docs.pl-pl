---
title: Dodawanie intencji w aplikacjach usługi LUIS
titleSuffix: Azure Cognitive Services
description: Dodawanie intencji z aplikacją usługi LUIS do identyfikowania grup pytania lub polecenia, które mają ten sam intencji.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 687cbad9d1e493e07fc5f813fdd7c52dee3b97d4
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634629"
---
# <a name="manage-intents"></a>Zarządzanie opcjami 
Dodaj [intencji](luis-concept-intent.md) z aplikacją usługi LUIS do identyfikowania grup pytania lub polecenia, które mają ten sam intencji. 

Intencji odbywa się z **kompilacji** sekcji na górnym pasku narzędzi. Dodaj i Zarządzaj Twoje intencje z **intencji** strony, dostępne w panelu po lewej stronie. 

W poniższej procedurze przedstawiono sposób dodawania celem "Bookflight" w aplikacji TravelAgent.

## <a name="add-intent"></a>Dodawanie intencji

1. Otwórz aplikację (na przykład TravelAgent), klikając jego nazwę **Moje aplikacje** strony, a następnie kliknij przycisk **intencji** w panelu po lewej stronie. 
2. Na **intencji** kliknij **Utwórz nowy opcję**.

3. W **Utwórz nowy opcję** okno dialogowe, typ zamiar nazwę "BookFlight" i kliknij przycisk **gotowe**.

    ![Dodawanie intencji](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Na stronie szczegółów elementu intent nowo dodanych zamiar [Dodawanie wypowiedzi](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Zmień nazwę intencji

1. Na **intencji** kliknij ikona zmiany nazwy ![celem zmiany nazwy](./media/luis-how-to-add-intents/Rename-Intent-btn.png) obok nazwy metody konwersji. 

2. Na **intencji** bieżąca nazwa intencji stronie jest wyświetlany w oknie dialogowym. Edytuj nazwę metody konwersji, a następnie naciśnij klawisz enter. Nowa nazwa zapisać i wyświetlić na stronie opcji.

    ![Edytuj opcje](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Usuń intencji
Podczas usuwania intencji innego niż None intencji, można dodać wszystkie wypowiedzi intencji None. Jest to przydatne, jeśli musisz przenieść wypowiedzi zamiast ich usuwania.   

1. Na **intencji** kliknij **zamiar usunięcia** przycisk znajdujący się obok z prawej strony nazwy metody konwersji. 

    ![Usuwanie przycisku opcji](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Kliknij przycisk "Ok" w oknie dialogowym potwierdzenia.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Dodaj wypowiedź na stronie intencji

Na stronie intencji, wprowadź odpowiednie wypowiedź, można oczekiwać od użytkowników, takie jak `book 2 adult business tickets to Paris tomorrow on Air France` w polu tekstowym pod nazwę metody konwersji, a następnie naciśnij klawisz Enter. 
 
>[!NOTE]
>Usługa LUIS konwertuje wszystkie wypowiedzi na małe litery.

![Strona Szczegóły zrzut ekranu z opcjami, z wyróżnioną pozycją wypowiedź](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Wypowiedzi są dodawane do listy wypowiedzi dla bieżącego intencji. Po dodaniu wypowiedź [etykietę dowolnej jednostki](luis-how-to-add-example-utterances.md) w ramach wypowiedzi i [szkolenie](luis-how-to-train.md) aplikacji. 

## <a name="create-a-pattern-from-an-utterance"></a>Tworzenie wzorca z wypowiedź
Zobacz [Dodaj wzorca od istniejących wypowiedź przeznaczenie lub jednostki strony](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Edytuj wypowiedź na stronie intencji

Aby edytować wypowiedź, wybierz przycisk wielokropka (***...*** ) przycisk po prawej stronie wiersza dla tego wypowiedź, a następnie wybierz **Edytuj**. Modyfikowanie tekstu, a następnie naciśnij klawisz Enter na klawiaturze.

![Strona Szczegóły zrzut ekranu z opcjami, z wyróżnionym przyciskiem wielokropka](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>Ponowne przypisywanie wypowiedzi na stronie intencji
Celem co najmniej jeden wypowiedzi można zmienić, przypisując im inną intencji. 

Aby ponownie przypisać jednego wypowiedź do innego zamiaru na prawym końcu wiersza wypowiedź wybierz poprawną nazwę metody konwersji, w obszarze **etykietą intencji** kolumny. Wypowiedź jest usuwany z bieżącym celem wypowiedź listy. 

![Zrzut ekranu BookFlight intencji strony z zamiarem wypowiedź w kolumnie Labeled intencji wybrane](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Aby zmienić celem kilka wypowiedzi, zaznacz pola wyboru po lewej stronie wypowiedzi, a następnie wybierz **ponownie przypisać intencji**. Wybierz prawidłowe opcje z listy.

![Zrzut ekranu BookFlight intencji strony wypowiedź zaznaczone i wyróżnionym przyciskiem intencji ponownego przypisania](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>Usuń wypowiedzi na stronie intencji

Aby usunąć wypowiedź, wybierz przycisk wielokropka (***...*** ) przycisk po prawej stronie wiersza dla tego wypowiedź, a następnie wybierz **Usuń**. Wypowiedź jest usuwany z listy i aplikacji usługi LUIS.

![Strona Szczegóły zrzut ekranu z opcjami, z podświetloną opcją Delete](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Aby usunąć kilka wypowiedzi:

1. Zaznacz pola wyboru po lewej stronie wypowiedzi, a następnie wybierz **usuwania wypowiedzi (s)**. 

    ![Strona szczegółów zrzut ekranu z opcjami, z wypowiedzi zaznaczone, a także usunąć utterance(s) wyróżnionym](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Wybierz **gotowe** w **usuwania wypowiedzi?** wyskakującego okna dialogowego.

## <a name="search-in-utterances-on-intent-page"></a>Wyszukiwanie w wypowiedzi na stronie intencji
Możesz wyszukać wypowiedzi, które zawierają tekst (słów i fraz) na liście wypowiedź intencji. Na przykład można zauważyć błąd, który obejmuje określony wyraz i chcesz znaleźć wszystkie przykłady, które zawierają konkretnego wyrazu. 

1. Na pasku narzędzi, wybierz ikonę lupy.

    ![Zrzut ekranu z opcjami strony wyszukiwania ikonę lupy wyróżniony](./media/luis-how-to-add-intents/magnifying-glass.png)

2. Pojawi się pole tekstowe wyszukiwania. Wpisz wyraz lub frazę w polu wyszukiwania w prawym górnym rogu listy wypowiedzi. Wypowiedzi listę aktualizacji, aby wyświetlić tylko wypowiedzi zawierające szukany tekst. 

    ![Zrzut ekranu z opcjami zawierającej wyróżnione pole tekstowe wyszukiwania](./media/luis-how-to-add-intents/search-textbox.png)

    Aby anulować wyszukiwania i przywrócić pełną listę wypowiedzi, Usuń został wpisany tekst wyszukiwania. Aby zamknąć pole tekstowe wyszukiwania, ponownie wybierz ikonę lupy w pasku narzędzi.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Błędy niezgodności prognoz na stronie intencji
Wypowiedź w intencji może być rozbieżności między metodę konwersji i ocena prognozy. Usługa LUIS wskazuje tę rozbieżność z czerwoną otoczkę wokół pozycji wynik. 

![Zrzut ekranu BookFlight intencji zawierającej wynik rozbieżność prognozowania wyróżniony](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Filtruj według intencji prognozowania rozbieżność błędy na stronie intencji
Aby filtrować listę wypowiedź do tylko wypowiedzi z rozbieżność intencji prognozowania, Przełącz z **Pokaż wszystko** do **tylko błędy** na pasku narzędzi. 

## <a name="filter-by-entity-type-on-intent-page"></a>Filtruj według typu jednostki, na stronie intencji
Użyj **filtry jednostki** listy rozwijanej na pasku narzędzi, aby filtrować wypowiedzi przez jednostkę. 

![Zrzut ekranu z opcjami zawierającej wyróżnione filtr typu jednostki](./media/luis-how-to-add-intents/filter-by-entities.png) 

Aby usunąć filtr, zaznacz pole filtru niebieski za pomocą tego wyrazu lub frazy w pasku narzędzi.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>Przełącz na widok tokenów na stronie intencji
Przełącz **widoku tokenów** do wyświetlania tokeny zamiast nazw typu jednostki. Na klawiaturze, można również użyć **formantu + E** do przełączenia widoku. 

![Zrzut ekranu BookFlight celem przy użyciu tokenu widoku wyróżniony](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Uczenie aplikacji po zmianie modelu z opcjami
Po Dodawanie, edytowanie lub usuwanie intencji, [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

Po dodaniu intencji do swojej aplikacji, z kolejnym krokiem jest rozpocząć dodawanie [wypowiedzi przykład](luis-how-to-add-example-utterances.md) dla intencji zostały dodane. 

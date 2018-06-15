---
title: Dodaj intencje w aplikacjach LUIS | Dokumentacja firmy Microsoft
description: Opis języka (LUIS) umożliwia dodawanie opcje ułatwiające aplikacje zrozumieć żądania użytkownika i szybkiego reagowania na poprawnie.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.service: cognitive-services
ms.openlocfilehash: 6e013e994a3bcb60c3104aa10cd7bad1535706f1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349793"
---
# <a name="manage-intents"></a>Opcje zarządzania 
Dodaj [intencje](luis-concept-intent.md) do aplikacji LUIS do identyfikowania grup pytania lub polecenia, które mają tego samego cele. 

Dodawanie i zarządzanie nimi z lokalizacji docelowych z **intencje** strony, dostępnej w sklepie **intencje** w LUIS w lewym panelu. 

W poniższej procedurze przedstawiono sposób dodawania aplikacji TravelAgent celem "Bookflight".

## <a name="add-intent"></a>Próba dodania

1. Otwórz aplikację (na przykład TravelAgent), klikając jej nazwę na **Moje aplikacje** , a następnie kliknij przycisk **intencje** w lewym panelu. 
2. Na **intencje** kliknij przycisk **utworzyć nowe opcje**.

    ![Lista lokalizacji docelowych](./media/luis-how-to-add-intents/IntentsList.png)
3. W **utworzyć nowe opcje** okno dialogowe, typ zamiar nazwy "BookFlight" i kliknij przycisk **gotowe**.

    ![Próba dodania](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Na stronie Szczegóły konwersji założeń nowo dodanego [dodać zniesławiających](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Zmień nazwę zamiar

1. Na **zamiar** strony, kliknij ikonę zmiany nazwy ![próba zmiany nazwy](./media/luis-how-to-add-intents/Rename-Intent-btn.png) obok nazwy metody konwersji. 

2. Na **zamiar** konwersji nazwę bieżącej strony, jest wyświetlany w oknie dialogowym. Edytuj nazwę konwersji, a następnie naciśnij klawisz enter. Nowa nazwa jest zapisany i wyświetlane na stronie konwersji.

    ![Edytuj opcje](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Opcje usuwania
Podczas usuwania celem innych niż Brak konwersji, można dodać wszystkich zniesławiających konwersji None. Jest to przydatne, jeśli musisz przenieść zniesławiających, zamiast ich usuwania.   

1. Na **zamiar** kliknij przycisk **zamiar usunąć** przycisk Dalej, aby po prawej stronie nazwy metody konwersji. 

    ![Usuwanie przycisku konwersji](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Kliknij przycisk "Ok" w oknie dialogowym potwierdzenia.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Dodaj utterance na stronie konwersji

Na stronie konwersji, wprowadź odpowiednie utterance oczekiwać od użytkowników, takich jak `book 2 adult business tickets to Paris tomorrow on Air France` w polu tekstowym pod konwersji nazwy, a następnie naciśnij klawisz Enter. 
 
>[!NOTE]
>LUIS konwertuje wszystkie zniesławiających na małe litery.

![Zrzut ekranu opcji strony szczegółów, z wyróżnioną pozycją utterance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Zniesławiających zostaną dodane do listy zniesławiających dla bieżącego celem. Po dodaniu utterance [etykietę żadnych jednostek](luis-how-to-add-example-utterances.md) w zniesławiających i [uczenia](luis-how-to-train.md) aplikacji. 

## <a name="create-a-pattern-from-an-utterance"></a>Tworzenie wzorca z utterance
Zobacz [Dodaj wzorca z istniejących utterance na stronie Opcje lub jednostki](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Edytuj utterance na stronie konwersji

Aby edytować utterance, wybierz ikonę wielokropek (...) na prawym końcu wiersza dla tego utterance, a następnie wybierz **Edytuj**. Zmodyfikuj go, a następnie naciśnij klawisz Enter na klawiaturze.

![Zrzut ekranu opcji strony szczegółów, z ikoną wielokropek wyróżnione](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>Ponownie przypisać zniesławiających na stronie konwersji
Aby zmienić celem zniesławiających co najmniej jednego, przypisani do innego zamiaru. 

Aby ponownie przypisać jednego utterance do innego zamiaru na prawym końcu wiersza utterance wybierz poprawną nazwę metody konwersji w obszarze **etykietą zamiar** kolumny. Utterance jest usuwany z bieżącym celem utterance listy. 

![Zrzut ekranu BookFlight konwersji strony z zamiarem utterance Labeled kolumnie konwersji wybrano](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Aby zmienić celem kilka zniesławiających, zaznacz pola wyboru po lewej zniesławiających, a następnie wybierz **ponownie przypisać zamiar**. Wybierz prawidłowe opcje z listy.

![Zrzut ekranu BookFlight strona konwersji z utterance zaznaczone i wyróżnionym przyciskiem konwersji ponownego przypisania](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>Usuń zniesławiających na stronie konwersji

Aby usunąć utterance, wybierz ikonę wielokropek (...) na prawym końcu wiersza dla tego utterance, a następnie wybierz **usunąć**. Utterance zostanie usunięty z listy i LUIS aplikacji.

![Zrzut ekranu opcji strony szczegółów, z wyróżnioną opcją Delete](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Aby usunąć kilka zniesławiających:

1. Zaznacz pola wyboru po lewej zniesławiających, a następnie wybierz **usunąć zniesławiających (s)**. 

    ![Zrzut ekranu opcji strony szczegółów, z zniesławiających zaznaczone i usunąć podświetlony przycisk utterance(s)](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Wybierz **gotowe** w **usunąć zniesławiających?** podręczne okno dialogowe.

## <a name="search-in-utterances-on-intent-page"></a>Wyszukiwanie zniesławiających na stronie konwersji
Celem można wyszukiwać zniesławiających, zawierających tekst (słów ani fraz). Na przykład można zauważyć błąd, który obejmuje określonego programu word i chcesz znaleźć wszystkie przykłady, które obejmują określonego wyrazie. 

1. Wybierz ikonę lupy na pasku narzędzi.

    ![Zrzut ekranu opcji Strona, z wyszukiwania ikonę lupy wyróżnione](./media/luis-how-to-add-intents/magnifying-glass.png)

2. Zostanie wyświetlone pole tekstowe wyszukiwania. Wpisz wyraz lub frazę w polu wyszukiwania w prawym górnym rogu listy zniesławiających. Zniesławiających listę aktualizacji, aby wyświetlić tylko zniesławiających, które zawierają tekst do wyszukania. 

    ![Zrzut ekranu opcji Strona, z polem tekstowym wyszukiwania wyróżnione](./media/luis-how-to-add-intents/search-textbox.png)

    Aby anulować wyszukiwania i przywrócić pełną listę zniesławiających, Usuń został wpisany tekst wyszukiwania. Aby zamknąć polu tekstowym wyszukiwania, ponownie wybierz ikonę lupy na pasku narzędzi.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Błędy niezgodności prognozowania na stronie konwersji
Utterance w celem może być rozbieżności między wybrane opcje i wynik prognozowania. LUIS wskazuje tę rozbieżność z czerwonym prostokątem wokół wynik. 

![Zrzut ekranu BookFlight zamiar strony, wynik rozbieżność prognozowania wyróżnione](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Filtrowanie według błędów niezgodności prognozowania konwersji na stronie konwersji
Aby filtrować listę utterance do tylko zniesławiających z rozbieżność konwersji prognozowania, Przełącz z **Pokaż wszystko** do **tylko błędy** na pasku narzędzi. 

## <a name="filter-by-entity-type-on-intent-page"></a>Filtruj według typu jednostki, na stronie konwersji
Użyj **filtry jednostki** listy rozwijanej na pasku narzędzi, aby filtrować zniesławiających przez jednostkę. 

![Zrzut ekranu opcji Strona, z wyróżnionym filtr typu jednostki](./media/luis-how-to-add-intents/filter-by-entities.png) 

Aby usunąć filtr, zaznacz pole niebieski filtr z tym wyraz lub frazę w pasku narzędzi.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>Przełącz do widoku token na stronie konwersji
Przełącz **widoku tokenów** Aby wyświetlić tokeny zamiast nazwy typu jednostki. Na klawiaturze, można również użyć **kontroli + E** Aby przełączyć do widoku. 

![Zrzut ekranu BookFlight przeznaczeniu z widokiem tokenu wyróżnione](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Szkolenie aplikacji po zmianie modelu z lokalizacji docelowych
Po Dodawanie, edytowanie lub usuwanie lokalizacji docelowych, [uczenia](luis-how-to-train.md) i [publikowania](PublishApp.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

Po dodaniu intencje do aplikacji, jest kolejnego zadania, aby rozpocząć dodawanie [zniesławiających przykład](luis-how-to-add-example-utterances.md) dla intencje dodane. 

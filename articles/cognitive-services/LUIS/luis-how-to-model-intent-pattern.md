---
title: Wzorzec Dodawanie dokładności — LUIS
titleSuffix: Azure Cognitive Services
description: Dodawanie szablonów wzorców w celu poprawy dokładności przewidywania w aplikacjach Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 7b6c8ba1517de44d01ffbceec812749403465e63
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638093"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak dodać wzorce, aby poprawić dokładność przewidywania
Gdy aplikacja LUIS otrzymuje punkt końcowy wyrażenia długości, użyj [wzorca](luis-concept-patterns.md) , aby poprawić dokładność przewidywania dla wyrażenia długości, które ujawnia wzorzec w kolejności słów i wyborze wyrazu. Wzorce używają określonej [składni](luis-concept-patterns.md#pattern-syntax) , aby wskazać lokalizację: [jednostki](luis-concept-entity-types.md), [role](luis-concept-roles.md)jednostek i tekst opcjonalny.

## <a name="add-template-utterance-to-create-pattern"></a>Dodaj wypowiedź szablonu, aby utworzyć wzorzec
1. Otwórz aplikację, wybierając jego nazwę na **Moje aplikacje** strony, a następnie wybierz pozycję **wzorców** w lewym panelu w obszarze **lepsza wydajność aplikacji**.

    ![Zrzut ekranu przedstawiający listę wzorców](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Wybierz prawidłowe opcje dla wzorca. 

    ![Wybierz opcje](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. W polu tekstowym szablonu wpisz wypowiedź szablonu, a następnie naciśnij klawisz Enter. Jeśli chcesz wprowadzić nazwę jednostki, należy użyć składni jednostki prawidłowy wzorzec. Rozpocznij składni jednostki za pomocą `{`. Lista zawiera jednostki. Wybierz prawidłowe jednostki, a następnie naciśnij klawisz Enter. 

    ![Zrzut ekranu przedstawiający jednostkę do wzorca](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Jeśli jednostka zawiera [rolę](luis-concept-roles.md), wskaż rolę z pojedynczym dwukropkiem, `:`po nazwie jednostki, takiej jak. `{Location:Origin}` Wyświetla listę ról dla obiektów w postaci listy. Wybierz rolę, a następnie naciśnij klawisz Enter. 

    ![Zrzut ekranu przedstawiający jednostki z rolą](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po wybraniu prawidłowe jednostki, Zakończ wprowadzanie wzorzec, a następnie naciśnij klawisz Enter. Po zakończeniu wprowadzania wzorców [szkolenie](luis-how-to-train.md) aplikacji.

    ![Zrzut ekranu przedstawiający wzorzec wprowadzone przy użyciu obu typów jednostek](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Uczenie aplikacji po zmianie modelu wzorami
Po dodaniu, edytować, usunąć lub ponownie przypisać wzorzec [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Użyj kontekstowego paska narzędzi

Kontekstowy pasek narzędzi powyżej listy wzorców pozwala:

* Wyszukaj wzorce
* Edytuj wzorzec
* Ponowne przypisywanie poszczególnych wzorzec do innego zamiaru
* Ponowne przypisywanie kilka wzorców do innego zamiaru
* Delete-a-Single-wzorzec
* Usuwanie wielu wzorców
* Filtrowanie listy wzorzec przez jednostkę
* Filtr-wzorzec-lista według intencji
* Usuwanie jednostki lub filtr elementu intent
* Dodawanie wzorca z istniejących wypowiedź przeznaczenie lub jednostki strony

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć wzorzec](luis-tutorial-pattern.md) ze wzorcem. dowolne role i z samouczkiem.
* Dowiedz się, jak [szkolenie](luis-how-to-train.md) aplikacji.

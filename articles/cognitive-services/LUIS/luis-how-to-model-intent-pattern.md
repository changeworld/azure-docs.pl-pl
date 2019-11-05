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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: cbd8ad73ff4a03790dd6b22d5ce33acf09a2b125
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491367"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak dodać wzorce, aby poprawić dokładność przewidywania
Gdy aplikacja LUIS otrzymuje punkt końcowy wyrażenia długości, użyj [wzorca](luis-concept-patterns.md) , aby poprawić dokładność przewidywania dla wyrażenia długości, które ujawnia wzorzec w kolejności słów i wyborze wyrazu. Wzorce używają określonej [składni](luis-concept-patterns.md#pattern-syntax) , aby wskazać lokalizację: [jednostki](luis-concept-entity-types.md), [role](luis-concept-roles.md)jednostek i tekst opcjonalny.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-template-utterance-to-create-pattern"></a>Dodawanie szablonu wypowiedź do tworzenia wzorców
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** , a następnie wybierz pozycję **wzorce** w lewym panelu, w obszarze **Popraw wydajność aplikacji**.

    ![Zrzut ekranu przedstawiający listę wzorców](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Wybierz odpowiedni cel dla wzorca. 

    ![Wybór opcji](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. W polu tekstowym szablon wpisz wypowiedź szablonu i wybierz ENTER. Jeśli chcesz wprowadzić nazwę jednostki, użyj poprawnej składni jednostki wzorca. Rozpocznij składnię jednostki za pomocą `{`. Zostanie wyświetlona lista jednostek. Wybierz poprawną jednostkę, a następnie wybierz klawisz ENTER. 

    ![Zrzut ekranu jednostki dla wzorca](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Jeśli jednostka zawiera [rolę](luis-concept-roles.md), wskaż rolę z pojedynczym dwukropkiem, `:`po nazwie jednostki, takiej jak `{Location:Origin}`. Lista ról dla jednostek zostanie wyświetlona na liście. Wybierz rolę, a następnie wybierz klawisz ENTER. 

    ![Zrzut ekranu jednostki z rolą](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po wybraniu prawidłowej jednostki Zakończ wprowadzanie wzorca, a następnie wybierz klawisz ENTER. Gdy skończysz wprowadzać wzorce, [uczenie](luis-how-to-train.md) aplikacji.

    ![Zrzut ekranu przedstawiający wprowadzony wzorzec z obydwoma typami jednostek](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Uczenie aplikacji po zmianie modelu ze wzorcami
Po dodaniu, edycji, usunięciu lub ponownym przypisaniu wzorca należy [pouczenie](luis-how-to-train.md) i [opublikować](luis-how-to-publish-app.md) aplikację, aby zmiany dotyczyły zapytań punktów końcowych. 

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
* Edytowanie wzorca
* Przypisz ponownie indywidualny wzorzec do innego celu
* Przypisz ponownie kilka wzorców do innego celu
* Delete-a-Single-wzorzec
* Usuń kilka wzorców
* Filtruj listę wzorców według jednostek
* Filtr-wzorzec-lista według intencji
* Usuń filtr jednostki lub celu
* Dodaj wzorzec z istniejącej wypowiedź na stronie intencji lub jednostki

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć wzorzec](luis-tutorial-pattern.md) ze wzorcem. dowolne role i z samouczkiem.
* Dowiedz się, jak [szkolić](luis-how-to-train.md) aplikację.

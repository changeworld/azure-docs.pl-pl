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
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: eb3e473535c394818772ac949808023254087555
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448049"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak dodać wzorce, aby poprawić dokładność przewidywania
Gdy aplikacja LUIS otrzymuje punkt końcowy wyrażenia długości, użyj [wzorca](luis-concept-patterns.md) , aby poprawić dokładność przewidywania dla wyrażenia długości, które ujawnia wzorzec w kolejności słów i wyborze wyrazu. Wzorce używają określonej [składni](luis-concept-patterns.md#pattern-syntax) , aby wskazać lokalizację: [jednostki](luis-concept-entity-types.md), [role](luis-concept-roles.md)jednostek i tekst opcjonalny.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Wzorce zawierają tylko obiekty nadrzędne, które są obsługiwane przez maszynę, a nie podskładniki.

## <a name="adding-example-utterances-as-pattern"></a>Dodawanie przykładu wyrażenia długości jako wzorca

Jeśli chcesz dodać wzorzec dla jednostki, _najprostszym_ sposobem jest utworzenie wzorca na stronie szczegółów obiektu. Dzięki temu składnia pasuje do przykładu wypowiedź.

1. W [portalu Luis w wersji zapoznawczej](https://preview.luis.ai)wybierz aplikację ze strony **Moje aplikacje** .
1. Na stronie listy **intencje** wybierz nazwę zamiaru przykładu wypowiedź, z którego chcesz utworzyć szablon wypowiedź.
1. Na stronie Szczegóły zamiaru wybierz wiersz dla przykładowej wypowiedź, który ma być używany jako szablon wypowiedź, a następnie wybierz pozycję **+ Dodaj jako wzorzec** na pasku narzędzi kontekstu.

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu przedstawiający wybór przykładowej wypowiedź jako wzorzec szablonu na stronie Szczegóły intencji.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. W oknie podręcznym wybierz pozycję **gotowe** na stronie **Potwierdzanie wzorców** . Nie musisz definiować podskładników, ograniczeń ani deskryptorów jednostek. Wystarczy tylko wyświetlić listę jednostek, których dotyczy dana maszyna.

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu przedstawiający przykładowy przykład wypowiedź jako wzorzec szablonu na stronie Szczegóły intencji.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Jeśli musisz edytować szablon, na przykład wybierając opcję tekst jako opcjonalny, z nawiasami `[]` (kwadratowymi), musisz wprowadzić tę edycję na stronie **wzorce** .

1. Na pasku nawigacyjnym wybierz pozycję **szkolenie** , aby nauczyć aplikację z nowym wzorcem.

## <a name="add-template-utterance-using-correct-syntax"></a>Dodaj wypowiedź szablonu przy użyciu poprawnej składni

1. Otwórz aplikację, wybierając jego nazwę na **Moje aplikacje** strony, a następnie wybierz pozycję **wzorców** w lewym panelu w obszarze **lepsza wydajność aplikacji**.

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu przedstawiający listę wzorców](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Wybierz prawidłowe opcje dla wzorca.

1. W polu tekstowym szablonu wpisz wypowiedź szablonu, a następnie naciśnij klawisz Enter. Jeśli chcesz wprowadzić nazwę jednostki, należy użyć składni jednostki prawidłowy wzorzec. Rozpocznij składni jednostki za pomocą `{`. Lista zawiera jednostki. Wybierz poprawną jednostkę.

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu jednostki dla wzorca](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Jeśli jednostka zawiera [rolę](luis-concept-roles.md), wskaż rolę z pojedynczym dwukropkiem, `:`po nazwie jednostki, takiej jak `{Location:Origin}`. Wyświetla listę ról dla obiektów w postaci listy. Wybierz rolę, a następnie naciśnij klawisz Enter.

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu jednostki z rolą](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po wybraniu prawidłowe jednostki, Zakończ wprowadzanie wzorzec, a następnie naciśnij klawisz Enter. Po zakończeniu wprowadzania wzorców [szkolenie](luis-how-to-train.md) aplikacji.

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu przedstawiający wprowadzony wzorzec z obydwoma typami jednostek](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

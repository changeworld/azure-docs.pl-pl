---
title: Wzorce zwiększają dokładność — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Dodaj szablony wzorców, aby zwiększyć dokładność przewidywania w aplikacjach rozumienia języka (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311720"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak dodać wzorce, aby poprawić dokładność przewidywania
Po aplikacji usługi LUIS odbiera wypowiedzi punktu końcowego, użyj [wzorca,](luis-concept-patterns.md) aby poprawić dokładność przewidywania wypowiedzi, które ujawniają wzorzec w kolejności wyrazów i wyboru wyrazu. Wzorce używają określonej [składni,](luis-concept-patterns.md#pattern-syntax) aby wskazać lokalizację: [encji,](luis-concept-entity-types.md) [ról](luis-concept-roles.md)encji i tekstu opcjonalnego.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Wzorce obejmują tylko jednostki uzownie maszynowe, a nie podskładne.

## <a name="adding-example-utterances-as-pattern"></a>Dodawanie przykładowych wypowiedzi jako wzorca

Jeśli chcesz dodać wzorzec dla jednostki, _najprostszym_ sposobem jest utworzenie wzorca na stronie Szczegóły intencji. Dzięki temu składnia pasuje do wypowiedź przykład.

1. W portalu usługi LUIS w [wersji zapoznawczej](https://preview.luis.ai)wybierz aplikację ze strony **Moje aplikacje.**
1. Na stronie listy **Intencje** wybierz nazwę intencji wypowiedź przykład, który chcesz utworzyć wypowiedź szablonu z.
1. Na stronie Szczegóły intencji wybierz wiersz dla wypowiedzi przykład, który ma być używany jako wypowiedź szablonu, a następnie wybierz **+ Dodaj jako wzorzec** z paska narzędzi kontekstu.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wybranie przykładowego wypowiedź jako wzorca szablonu na stronie Szczegóły intencji.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. W wyskakującym okienku wybierz pozycję **Gotowe** na stronie **Potwierdź wzorce.** Nie trzeba definiować podskładników, ograniczeń ani deskryptorów jednostek. Wystarczy tylko wyświetlić listę jednostki nauczona maszynowo.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający potwierdzenie wypowiedź przykładu jako wzorca szablonu na stronie Szczegóły intencji.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Jeśli chcesz edytować szablon, na przykład zaznaczanie tekstu `[]` jako opcjonalnego, z nawiasami (kwadratowymi), musisz dokonać tej edycji na stronie **Wzorce.**

1. Na pasku nawigacyjnym wybierz **pozycję Trenuj,** aby trenować aplikację z nowym wzorcem.

## <a name="add-template-utterance-using-correct-syntax"></a>Dodawanie wypowiedź szablonu przy użyciu poprawnej składni

1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje,** a następnie wybierz pozycję **Wzorce** w lewym panelu w obszarze **Poprawianie wydajności aplikacji**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający listę wzorców](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Wybierz prawidłową intencję dla wzorca.

1. W pola tekstowym szablonu wpisz wypowiedź szablonu i wybierz pozycję Enter. Aby wprowadzić nazwę encji, należy użyć poprawnej składni encji wzorca. Rozpocznij składnię `{`encji za pomocą pliku . Zostanie wyświetlona lista encji. Wybierz właściwą encję.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający encję dla wzorca](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Jeśli encja zawiera [rolę](luis-concept-roles.md), wskazać rolę `:`z jednym dwukropkiem, po nazwie jednostki, takich jak `{Location:Origin}`. Lista ról dla jednostek jest wyświetlana na liście. Wybierz rolę, a następnie wybierz pozycję Enter.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający jednostkę z rolą](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po wybraniu właściwego elementu zakończ wprowadzanie wzorca, a następnie wybierz pozycję Wprowadź. Po zakończeniu wprowadzania wzorców [trenuj](luis-how-to-train.md) aplikację.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wprowadzony wzorzec z obu typami elementów](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Trenuj aplikację po zmianie modelu za pomocą wzorców
Po dodaniu, edycji, usuwania lub ponownego przypisywania wzorca, [trenuj](luis-how-to-train.md) i [publikuj](luis-how-to-publish-app.md) aplikację, aby zmiany miały wpływ na zapytania dotyczące punktów końcowych.

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

## <a name="use-contextual-toolbar"></a>Używanie kontekstowego paska narzędzi

Kontekstowy pasek narzędzi nad listą wzorców umożliwia:

* Wyszukiwanie wzorców
* Edytowanie wzoru
* Ponowne przypisywanie pojedynczego wzorca do różnych intencji
* Ponowne przypisywanie kilku wzorców do różnych intencji
* Usuń pojedynczy wzór
* Usuwanie kilku wzorców
* Lista wzorców filtrowania według encji
* Filtr-wzór-lista według intencji
* Usuwanie encji lub filtru intencji
* Dodawanie wzorca z istniejącego wypowiedź na stronie intencji lub encji

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć wzorzec](luis-tutorial-pattern.md) z pattern.any i ról z samouczka.
* Dowiedz się, jak [trenować](luis-how-to-train.md) aplikację.

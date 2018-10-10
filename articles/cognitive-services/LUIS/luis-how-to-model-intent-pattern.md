---
title: Dodawanie szablonów wzorzec zamiast więcej wypowiedzi w aplikacjach usługi LUIS
titleSuffix: Azure Cognitive Services
description: Informacje dotyczące dodawania szablonów wzorzec Language Understanding (LUIS) aplikacji w celu zwiększenia dokładności prognozy.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 6c15fc256a330c7de154c551c1ef4c78913f470a
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885890"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak dodać wzorców w celu zwiększenia dokładności prognozy
Gdy aplikacją usługi LUIS otrzyma wypowiedzi punktu końcowego, należy użyć [koncepcji](luis-concept-patterns.md) wzorców w celu zwiększenia dokładności prognozy dla wypowiedzi zawiera wzorca w porządku program word i wybór programu word. Wzorce użycia [jednostek](luis-concept-entity-types.md) i ich ról, aby wyodrębnić dane przy użyciu składni z określonym wzorcem. 

## <a name="add-template-utterance-to-create-pattern"></a>Dodaj wypowiedź szablonu, aby utworzyć wzorzec
1. Otwórz aplikację, wybierając jego nazwę na **Moje aplikacje** strony, a następnie wybierz pozycję **wzorców** w lewym panelu w obszarze **lepsza wydajność aplikacji**.

    ![Zrzut ekranu przedstawiający listę wzorców](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Wybierz prawidłowe opcje dla wzorca. 

    ![Wybierz opcje](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. W polu tekstowym szablonu wpisz wypowiedź szablonu, a następnie naciśnij klawisz Enter. Jeśli chcesz wprowadzić nazwę jednostki, należy użyć składni jednostki prawidłowy wzorzec. Rozpocznij składni jednostki za pomocą `{`. Lista zawiera jednostki. Wybierz prawidłowe jednostki, a następnie naciśnij klawisz Enter. 

    ![Zrzut ekranu przedstawiający jednostkę do wzorca](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Jeśli jednostka zawiera roli, określać rolę z dwukropkiem pojedynczego `:`po nazwę jednostki, takie jak `{Location:Origin}`. Wyświetla listę ról dla obiektów w postaci listy. Wybierz rolę, a następnie naciśnij klawisz Enter. 

    ![Zrzut ekranu przedstawiający jednostki z rolą](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po wybraniu prawidłowe jednostki, Zakończ wprowadzanie wzorzec, a następnie naciśnij klawisz Enter. Po zakończeniu wprowadzania wzorców [szkolenie](luis-how-to-train.md) aplikacji.

    ![Zrzut ekranu przedstawiający wzorzec wprowadzone przy użyciu obu typów jednostek](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Wzorców wyszukiwania
Wyszukiwanie umożliwia wyszukiwanie wzorców, które zawierają dany tekst.  

1. Wybierz ikonę lupy.

    ![Zrzut ekranu wzorców strony z wyróżnioną ikoną narzędzia wyszukiwania](./media/luis-how-to-model-intent-pattern/search-icon.png)

    Wpisz wyszukiwany tekst w polu wyszukiwania w prawym górnym rogu listy wzorców, a następnie naciśnij klawisz Enter. Lista wzorców jest aktualizowana do wyświetlenia wyłącznie wzorce, w tym tekst wyszukiwania.

    ![Zrzut ekranu wzorców strony wyszukiwany tekst w polu wyszukiwania, wyróżniony](./media/luis-how-to-model-intent-pattern/search-text.png)

    Aby anulować wyszukiwania i przywrócić pełną listę wzorców, Usuń został wpisany tekst wyszukiwania.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>Edytuj wzorzec
1. Aby edytować wzorzec, wybierz przycisk wielokropka (***...*** ) przycisk po prawej stronie wiersza dla tego wzorca, a następnie wybierz **Edytuj**. 

    ![Zrzut ekranu edycji elementu menu, w wierszu wzorzec](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. Wprowadź wszelkie zmiany w polu tekstowym. Gdy wszystko będzie gotowe, wybierz opcję Wprowadź. Po zakończeniu edycji wzorców [szkolenie](luis-how-to-train.md) aplikacji.

    ![Zrzut ekranu przedstawiający wzorzec do edycji](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Ponowne przypisywanie poszczególnych wzorzec do innego zamiaru

Aby ponownie przypisać jednego wzorzec na potrzeby innego zamiaru, zaznacz pole opcji listy po prawej stronie tekstu wzorca i wybierz innego zamiaru.

![Zrzut ekranu przedstawiający ponowne przypisywanie poszczególnych wzorzec do innego zamiaru](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Ponowne przypisywanie kilka wzorców do innego zamiaru

Do ponownego przypisania kilka wzorców do innego zamiaru, zaznacz pole wyboru na lewo od każdego wzorca, lub zaznacz pole wyboru najważniejsze. **Ponownie przypisać intencji** opcji powoduje wyświetlenie na pasku narzędzi. Wybierz prawidłowe przeznaczenie wzorców. 

![Zrzut ekranu przedstawiający ponowne przypisywanie kilka wzorców do innego zamiaru](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Usuwanie pojedynczej wzorzec

1. Aby usunąć wzorzec, wybierz przycisk wielokropka (***...*** ) przycisk po prawej stronie wiersza dla tego wzorca, a następnie wybierz **Usuń**. 

    ![Zrzut ekranu Usuń wypowiedź](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Wybierz **Ok** o potwierdzenie usunięcia.

    ![Zrzut ekranu Usuń potwierdzenia](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Usuwanie wielu wzorców

1. Aby usunąć kilka wzorców, zaznacz pole wyboru na lewo od każdego wzorca, lub zaznacz pole wyboru w górnej. **Usuń wzorców (s)** opcji powoduje wyświetlenie na pasku narzędzi. Wybierz **Usuń wzorców (s)**.  

    ![Zrzut ekranu przedstawiający usuwanie wielu wzorców](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. **Usuń wzorców** pojawi się okno dialogowe potwierdzenia. Wybierz **Ok** na zakończenie usuwania.

    ![Zrzut ekranu przedstawiający usuwanie wielu wzorców](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Filtrowanie listy wzorzec przez jednostkę

Aby filtrować listę wzorców według określonej jednostki, wybierz **filtry jednostki** na pasku narzędzi powyżej wzorców. 

![Zrzut ekranu przedstawiający filtrowanie wzorców przez jednostkę](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

Po zastosowaniu filtru nazwa jednostki jest wyświetlana poniżej paska narzędzi. 

## <a name="filter-pattern-list-by-intent"></a>Filtrowanie listy wzorzec według intencji

Aby filtrować listę wzorców przez określone przeznaczenie, wybierz **filtry intencji** na pasku narzędzi powyżej wzorców. 

![Zrzut ekranu przedstawiający filtrowanie wzorców według intencji](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

Po zastosowaniu filtru intencji nazwa jest wyświetlana poniżej paska narzędzi. 

## <a name="remove-entity-or-intent-filter"></a>Usuwanie jednostki lub filtr elementu intent
Gdy wzorzec lista jest filtrowana, jednostki lub intencji nazwa pojawia się pod paskiem narzędzi. Aby usunąć filtr, wybierz nazwę.

![Zrzut ekranu przedstawiający wzorców filtrowane przez jednostkę](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

Filtr zostanie usunięty, i wyświetlić wszystkie wzorce. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Dodawanie wzorca z istniejących wypowiedź przeznaczenie lub jednostki strony
Wzorzec można utworzyć na podstawie istniejących wypowiedź w dowolnym **intencji** lub **jednostki** strony. Wszystkie wypowiedzi na dowolnej stronie przeznaczenie lub jednostki są wyświetlane na liście z prawej kolumnie tabeli, takich jak dające dostęp do opcji na poziomie wypowiedź **Edytuj**, **Usuń**, i **Dodaj jako wzorzec**.

1. W zaznaczonym wierszu wypowiedź wybierz przycisk wielokropka (***...*** ) znajdujący się po prawej stronie wypowiedź i wybierz **Dodaj jako wzorca**.

    [![](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "Zrzut ekranu przedstawiający wypowiedzi tabeli za pomocą wzorca Dodaj wyróżnione w menu Opcje")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. Modyfikowanie wzorzec zgodnie z opisem w [reguły składni](luis-concept-patterns.md#pattern-syntax). Wypowiedź, wybrane jest oznaczona przy użyciu jednostek, te jednostki są już we wzorcu z poprawną składnię.

    ![Zrzut ekranu przedstawiający wzorców filtrowane przez jednostkę](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Uczenie aplikacji po zmianie modelu wzorami
Po dodaniu, edytować, usunąć lub ponownie przypisać wzorzec [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [kompilacji wzorzec](luis-tutorial-pattern.md) pattern.any i ról.
* Dowiedz się, jak [szkolenie](luis-how-to-train.md) aplikacji.
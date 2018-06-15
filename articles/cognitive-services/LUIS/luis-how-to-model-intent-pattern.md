---
title: Dodaj wzorzec szablonów w aplikacjach LUIS | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak dodać szablony wzorzec opis języka (LUIS) aplikacji w celu zwiększenia dokładności prognozy.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 68c0ea1fd3f2e60e0adec631f33c8bd09a3d9960
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35356388"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak dodać wzorców w celu zwiększenia dokładności prognozy
Gdy aplikacja LUIS otrzyma zniesławiających punktu końcowego, użyj [koncepcji](luis-concept-patterns.md) wzorców w celu zwiększenia dokładności prognozy dla zniesławiających, które zawiera wzorca w kolejności word i wybór programu word. Wzorce użycia [jednostek](luis-concept-entity-types.md) i ich role, aby wyodrębnić dane przy użyciu składni z określonym wzorcem. 

## <a name="add-template-utterance-to-create-pattern"></a>Dodaj utterance szablonu do utworzenia wzorca
1. Otwórz aplikację, wybierając jego nazwę na **Moje aplikacje** , a następnie wybierz **wzorce** w lewym panelu w obszarze **poprawić wydajność aplikacji**.

    ![Zrzut ekranu przedstawiający listę wzorców](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Wybierz prawidłowe opcje wzorzec. 

    ![Wybierz opcje](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. W polu tekstowym szablonu typu utterance szablonu, a następnie wybierz Enter. Należy wprowadzić nazwę jednostki, należy użyć składni jednostki poprawnego wzorca. Rozpocznij składni jednostki z `{`. Lista przedstawia jednostek. Wybierz prawidłowe jednostki, a następnie wybierz Enter. 

    ![Zrzut ekranu przedstawiający jednostki dla wzorca](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Jeśli jednostka obejmuje rolę, wskazać roli z dwukropkiem pojedynczego `:`po nazwa jednostki, takich jak `{Location:Origin}`. Wyświetla listę ról dla jednostek na liście. Wybierz rolę, a następnie wybierz Enter. 

    ![Zrzut ekranu przedstawiający jednostki z rolą](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po wybraniu jednostki poprawne zakończyć wprowadzanie wzorzec, a następnie wybierz Enter. Po zakończeniu wprowadzania wzorce [uczenia](luis-how-to-train.md) aplikacji.

    ![Zrzut ekranu przedstawiający wprowadzony wzorzec z oboma typami jednostek](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Wzorce wyszukiwania
Wyszukiwanie umożliwia wyszukiwanie wzorców, które zawierają dany tekst.  

1. Wybierz ikonę lupy.

    ![Zrzut ekranu wzorce strony z wyróżnionym ikonę narzędzia wyszukiwania](./media/luis-how-to-model-intent-pattern/search-icon.png)

    Wpisz wyszukiwany tekst w polu wyszukiwania w prawym górnym rogu listy wzorców i wybierz Enter. Lista wzorców jest aktualizowana do wyświetlenia wzorce, w tym tekst do wyszukania.

    ![Zrzut ekranu wzorce strony z tekstem wyszukiwania w polu wyszukiwania wyróżnione](./media/luis-how-to-model-intent-pattern/search-text.png)

    Aby anulować wyszukiwanie i Przywróć pełną listę wzorców, Usuń wpisany tekst wyszukiwania.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>Edytuj wzorzec
1. Aby edytować wzorzec, wybierz ikonę wielokropek (...) na prawym końcu wiersza dla tego wzorca, a następnie wybierz **Edytuj**. 

    ![Zrzut ekranu edycji elementu menu, w wierszu wzorca](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. Wprowadź wszelkie zmiany w polu tekstowym. Gdy wszystko będzie gotowe, wybierz opcję Wprowadź. Po zakończeniu edycji wzorce [uczenia](luis-how-to-train.md) aplikacji.

    ![Zrzut ekranu przedstawiający edycji wzorca](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Ponowne przypisanie poszczególnych wzorzec do innego zamiaru

Do ponownego przypisania pojedynczy wzorzec do innego zamiaru, wybierz pole listy konwersji wzorzec tekstu z prawej strony, a następnie wybierz innego zamiaru.

![Zrzut ekranu przedstawiający ponowne przypisywanie poszczególnych wzorzec do innego zamiaru](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Ponownie przypisać kilka wzorców do innego zamiaru

Do ponownego przypisania kilka wzorców do innego zamiaru, zaznacz pole wyboru z lewej strony każdego wzorca lub zaznacz pole wyboru top. **Ponownie przypisać zamiar** opcja powoduje wyświetlenie na pasku narzędzi. Wybierz prawidłowe opcje wzorce. 

![Zrzut ekranu przedstawiający ponowne przypisywanie kilka wzorców do innego zamiaru](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Usuń pojedynczy wzorzec

1. Aby usunąć wzorzec, wybierz ikonę wielokropek (...) na prawym końcu wiersza dla tego wzorca, a następnie wybierz **usunąć**. 

    ![Zrzut ekranu usunąć utterance](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Wybierz **Ok** aby potwierdzić usunięcie.

    ![Zrzut ekranu usunąć potwierdzenia](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Usuń kilka wzorców

1. Aby usunąć kilka wzorców, zaznacz pole wyboru z lewej strony każdego wzorca lub zaznacz pole wyboru top. **Usunąć wzorce (s)** opcja powoduje wyświetlenie na pasku narzędzi. Wybierz **usunąć wzorce (s)**.  

    ![Zrzut ekranu przedstawiający usunięcie kilku wzorców](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. **Usunąć wzorce** zostanie wyświetlone okno dialogowe potwierdzenia. Wybierz **Ok** aby zakończyć usuwanie.

    ![Zrzut ekranu przedstawiający usunięcie kilku wzorców](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Lista filtrów wzorzec przez jednostkę

Aby filtrować listę wzorców według określonej jednostki, wybierz **filtry jednostki** na pasku narzędzi nad wzorce. 

![Zrzut ekranu przedstawiający filtrowania wzorce przez jednostkę](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

Po zastosowaniu filtru nazwa jednostki pojawia się poniżej paska narzędzi. 

## <a name="filter-pattern-list-by-intent"></a>Lista filtrów wzorzec przez zamiar

Aby filtrować listę wzorców w określonym celem, wybierz **filtruje zamiar** na pasku narzędzi nad wzorce. 

![Zrzut ekranu przedstawiający filtrowanie wzorców celem](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

Po zastosowaniu filtru konwersji nazwa jest wyświetlana poniżej paska narzędzi. 

## <a name="remove-entity-or-intent-filter"></a>Usuń jednostki lub konwersji filtru
Gdy lista wzorzec jest filtrowana, jednostki lub konwersji nazwa pojawia się poniżej paska narzędzi. Aby usunąć filtr, wybierz nazwę.

![Zrzut ekranu przedstawiający wzorce filtrowane przez jednostkę](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

Filtr zostanie usunięty i Wyświetl wszystkich wzorców. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Dodawanie wzorca z istniejących utterance zamiar lub jednostki strony
Wzorzec można utworzyć na podstawie istniejących utterance albo **zamiar** lub **jednostki** strony. Wszystkie zniesławiających na każdej stronie zamiar lub jednostki są wyświetlane na liście z prawej kolumnie podania dostęp do opcji utterance poziomu, takich jak **Edytuj**, **usunąć**, i **Dodaj jako wzorzec**.

1. Na wybranego wiersza utterance, wybierz wielokropek (...) po prawej stronie utterance, a następnie wybierz **Dodaj jako wzorzec**.

    [![](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "Zrzut ekranu przedstawiający zniesławiających tabeli z wzorcem Dodaj wyróżnione menu opcji")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. Zmodyfikuj wzorzec zgodnie z [reguły składni](luis-concept-patterns.md#pattern-syntax). Uzyskuje utterance, wybranych jednostek, podmioty są już we wzorcu o poprawnej składni.

    ![Zrzut ekranu przedstawiający wzorce filtrowane przez jednostkę](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Szkolenie aplikacji po zmianie modelu wzorami
Po dodaniu, edytować, usunąć lub ponownie przypisać wzorzec [uczenia](luis-how-to-train.md) i [publikowania](PublishApp.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [kompilacji wzorca](luis-tutorial-pattern.md) pattern.any i ról.
* Dowiedz się, jak [uczenia](luis-how-to-train.md) aplikacji.
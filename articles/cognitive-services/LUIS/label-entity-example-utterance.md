---
title: Wypowiedź przykładowej jednostki etykiety
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak oznaczyć jednostkę nauczoną maszyną podskładnością w przykładowym wypowiedź na stronie szczegółów intencji portalu usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: 5499e8362e1107a18b997a6a485e11343c328d0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898376"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Etykieta jednostki nauczanej maszynowo w wypowiedź przykład

Etykietowanie jednostki w wypowiedź przykład daje usługi LUIS przykład jednostki jest i gdzie jednostka może pojawić się w wypowiedź.

## <a name="labeling-machine-learned-entity"></a>Urządzenie do etykietowania maszynowego

Należy wziąć `hi, please I want a cheese pizza in 20 minutes`pod uwagę wyrażenie, .

1. Zaznacz tekst znajdujący się w lewym po lewej stronie, a następnie zaznacz tekst encji znajdujący się po prawej stronie, a następnie wybierz encję, którą chcesz oznaczyć, w tym przypadku Wypełnij zamówienie. _Pełna kolejność_ jest oznaczona na poniższej ilustracji.

    > [!div class="mx-imgBorder"]
    > ![Etykieta kompletna jednostka uczenia się maszynowego](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Wybierz encję z okna podręcznego. Oznaczona jako pełna jednostka zamówienia pizzy zawiera wszystkie wyrazy (od lewej do prawej w języku angielskim), które są oznaczone etykietą.

## <a name="review-labeled-text"></a>Przeglądanie tekstu oznaczonego etykietą

Po etykietowaniu przejrzyj wypowiedź przykładu i upewnij się, że wybrany zakres tekstu został podkreślony za pomocą wybranej jednostki. Linia ciągła wskazuje, że tekst został oznaczony etykietą.

> [!div class="mx-imgBorder"]
> ![Oznaczona jako kompletna jednostka, której nauczona jest maszyna](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Potwierdź przewidywaną encję

Jeśli wokół zakresu tekstu znajduje się pole z kropkami, a nazwa encji znajduje się powyżej wypowiedź, oznacza to, że tekst jest przewidywany, ale _nie jest jeszcze oznaczony etykietą_. Aby przekształcić przewidywanie w etykietę, wybierz wiersz wypowiedź, a następnie wybierz pozycję **Potwierdź prognozowanie encji**.

> [!div class="mx-imgBorder"]
> ![Przewidywanie kompletnej jednostki nauczającej maszyną](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Alternatywnie można wybrać nazwę jednostki nad tekstem, a następnie wybrać **opcję Potwierdź przewidywanie** z menu, które się pojawi.

> [!div class="mx-imgBorder"]
> ![Przewidywanie kompletnej jednostki nauczającej maszynowo z menu](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Etykietuj encję przez malowanie kursorem palety encji

Paleta jednostek oferuje alternatywę dla poprzedniego środowiska etykietowania. Pozwala na szczotkowanie tekstu, aby natychmiast oznaczyć go za pomocą encji.

1. Otwórz paletę encji, wybierając ikonę zakreślacz w prawym górnym rogu tabeli wypowiedź.

    > [!div class="mx-imgBorder"]
    > ![Paleta jednostek dla jednostki nauczanych maszynowo](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Wybierz komponent elementu. Ta akcja jest wizualnie wskazywana za pomocą nowego kursora. Kursor podąża za myszą podczas poruszania się w portalu.

    > [!div class="mx-imgBorder"]
    > ![Paleta jednostek dla jednostki nauczanych maszynowo](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. W wypowiedź przykład _malować_ jednostki kursorem.

    > [!div class="mx-imgBorder"]
    > ![Paleta jednostek dla jednostki nauczanych maszynowo](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Etykietowanie podskładów jednostki wyuczonej maszyny

Podskładne w jednostkach są oznaczone dokładnie tak samo jak jednostki najwyższego poziomu. Podczas zaznaczania tekstu encje dostępne w wyskakującym oknie są względem kontekstu, w którym pojawia się tekst. Na przykład, jeśli masz 5-poziomową jednostkę nauczaną maszyną i wybierasz tekst, który został oznaczony jako pierwszy i drugi poziom (oznaczony etykietą nazwy jednostki w przykładowym wypowiedź), jednostki dostępne w wyskakującym oknie są ograniczone do w kontekście elementów trzeciego poziomu. Aby oznaczyć tekst innymi elementami, wybierz pozycję Etykieta jako inną opcję **encji.**

> [!div class="mx-imgBorder"]
> ![Paleta jednostek dla jednostki nauczanych maszynowo](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Podskładne mogą być etykietowane tylko wtedy, gdy element nadrzędny jest również oznaczony etykietą.

## <a name="labeling-entity-roles"></a>Etykietowanie ról encji

Role encji są oznaczone przy użyciu palety encji.

1. Na stronie Szczegółów intencji wybierz **paletę Encja** z paska narzędzi kontekstu.
1. Po otwarciu palety Encja wybierz encję z listy encji.
1. Przejdź do **inspektora encji**i wybierz istniejącą rolę lub utwórz nową rolę.
1. W przykładowym tekście wypowiedź oznaczaj tekst rolą jednostki.

## <a name="unlabel-entities"></a>Encje bez etykiety

Aby odeznać encję, zaznacz nazwę encji pod tekstem i wybierz opcję **Nieoznakowanie**. Jeśli encja, którą próbujesz odznakować, oznaczyła podskładne, podskładne muszą być najpierw nieoznakowane.

## <a name="editing-labels-using-the-entity-palette"></a>Edytowanie etykiet przy użyciu palety encji

Jeśli popełnisz błąd podczas etykietowania, paleta jednostek jest łatwym narzędziem, które umożliwia szybkie edycje. Na przykład jeśli etykieta encji obejmuje dodatkowe słowo przez pomyłkę i ma już oznaczone podskładności, można użyć palety encji do szczotkowania wymaganego krótszego zakresu wyrazów.

Przykład:

1. Pizza Type subcomponent obejmuje "pizza ser z", który zawiera dodatkowe niepoprawne słowo -- "z"

    > [!div class="mx-imgBorder"]
    > ![Paleta jednostek dla jednostki nauczanych maszynowo](media/label-utterances/edit-label-with-palette-1.png)

2. Użyj palety encji, aby wybrać rodzaj pizzy i szczotkować "pizza sera". Wynik jest taki, że tylko pizza serowa jest teraz oznaczona jako Pizza Type.

    > [!div class="mx-imgBorder"]
    > ![Paleta jednostek dla jednostki nauczanych maszynowo](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Etykiety dla elementów tekstu dopasowania

Encje tekstu pasującego obejmują wstępnie utworzone jednostki, jednostki wyrażenia regularnego, encje listy i jednostki pattern.any. Są one automatycznie oznaczone przez usługę LUIS, więc nie muszą być ręcznie oznaczone przez użytkowników.

## <a name="entity-prediction-errors"></a>Błędy przewidywania jednostek

Błędy przewidywania jednostki wskazują, że przewidywana jednostka nie pasuje do jednostki oznaczonej etykietą. Jest to wizualizowane ze wskaźnikiem ostrożności obok wypowiedź.

> [!div class="mx-imgBorder"]
> ![Paleta jednostek dla jednostki nauczanych maszynowo](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Następne kroki

Użyj [pulpitu nawigacyjnego](luis-how-to-use-dashboard.md) i [przejrzyj wypowiedzi punktów końcowych,](luis-how-to-review-endpoint-utterances.md) aby poprawić jakość przewidywania aplikacji.
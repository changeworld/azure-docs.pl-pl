---
title: Przeglądanie wypowiedzi użytkowników — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Przejrzyj wypowiedzi przechwycone przez aktywne uczenie się, aby wybrać intencje i oznaczyć jednostki dla wypowiedzi świata odczytu; akceptować zmiany, szkolić i publikować.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219853"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Jak ulepszyć aplikację usługi LUIS, przeglądając wypowiedzi punktów końcowych

Proces przeglądania wypowiedzi punktu końcowego dla poprawnych prognoz jest nazywany [aktywne uczenie](luis-concept-review-endpoint-utterances.md)się. Aktywne uczenie przechwytuje kwerendy punktu końcowego i wybiera wypowiedzi punktu końcowego użytkownika, których nie ma pewności. Przeglądać te wypowiedzi, aby wybrać intencji i oznaczyć jednostki dla tych wypowiedzi świata odczytu. Zaakceptuj te zmiany w wypowiedzi przykład, a następnie trenować i publikować. Usługa LUIS następnie identyfikuje wypowiedzi dokładniej.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Włącz aktywne uczenie się

Aby włączyć aktywne uczenie, należy rejestrować zapytania użytkowników. Jest to realizowane przez wywołanie [kwerendy punktu końcowego](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) `log=true` z querystring parametru i wartości.

Użyj portalu usługi LUIS do skonstruowania poprawnej kwerendy punktu końcowego.

1. W portalu usługi LUIS w [wersji zapoznawczej](https://preview.luis.ai/)wybierz aplikację z listy aplikacji.
1. Przejdź do sekcji **Zarządzanie,** a następnie wybierz pozycję **Zasoby platformy Azure**.
1. W przypadku przypisanego zasobu prognozowania wybierz pozycję **Zmień parametry kwerendy**.

    > [!div class="mx-imgBorder"]
    > ![Użyj portalu usługi LUIS, aby zapisać dzienniki, co jest wymagane do aktywnego uczenia się.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Przełącz **zapisz dzienniki,** a następnie zapisz, wybierając pozycję **Gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Użyj portalu usługi LUIS, aby zapisać dzienniki, co jest wymagane do aktywnego uczenia się.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Ta akcja zmienia przykładowy `log=true` adres URL, dodając parametr querystring. Kopiowanie i używanie zmienionego przykładowego adresu URL kwerendy podczas wykonywania kwerend prognozowania do punktu końcowego środowiska wykonawczego.

## <a name="correct-intent-predictions-to-align-utterances"></a>Korygowanie prognoz intencji w celu wyrównania wypowiedzi

Każda wypowiedź ma sugerowane intencji wyświetlane w **aligned intencji** kolumny.

> [!div class="mx-imgBorder"]
> [![Przejrzyj wypowiedzi punktów końcowych, których usługa LUIS nie ma](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Jeśli zgadzasz się z tym zamiarem, zaznacz znacznik wyboru. Jeśli nie zgadzasz się z sugestią, wybierz poprawną intencję z listy rozwijanej wyrównany zamiar, a następnie wybierz znacznik wyboru po prawej stronie wyrównanej intencji. Po wybraniu w znaczniku wyboru wypowiedź jest przenoszona do intencji i usuwana z listy **Przejrzyj wypowiedzi punktu końcowego.**

> [!TIP]
> Ważne jest, aby przejść do strony szczegóły intencji, aby przejrzeć i poprawić prognoz jednostki ze wszystkich wypowiedzi przykład z **listy Przejrzyj wypowiedzi punktu końcowego.**

## <a name="delete-utterance"></a>Usuwanie wypowiedź

Każdy wypowiedź można usunąć z listy recenzji. Po usunięciu nie pojawi się ponownie na liście. Jest to prawdą, nawet jeśli użytkownik wprowadzi ten sam wypowiedź z punktu końcowego.

Jeśli nie masz pewności, czy należy usunąć wypowiedź, przenieść go do None `miscellaneous` intencji lub utworzyć nowy zamiar, takich jak i przenieść wypowiedź do tego zamiaru.

## <a name="disable-active-learning"></a>Wyłączanie aktywnego uczenia się

Aby wyłączyć aktywne uczenie, nie rejestruj zapytań użytkowników. Jest to realizowane przez ustawienie [kwerendy punktu końcowego](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) z `log=false` querystring parametr i wartość lub nie przy użyciu querystring wartość, ponieważ wartość domyślna jest false.

## <a name="next-steps"></a>Następne kroki

Aby przetestować, jak zwiększa wydajność po oznaczeniu sugerowane wypowiedzi, można uzyskać dostęp do konsoli testowej, wybierając **test** w górnym panelu. Aby uzyskać instrukcje dotyczące testowania aplikacji przy użyciu konsoli testowej, zobacz [Szkolenie i testowanie aplikacji](luis-interactive-test.md).

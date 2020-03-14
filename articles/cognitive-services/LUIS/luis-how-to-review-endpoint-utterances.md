---
title: Przeglądanie użytkownika wyrażenia długości — LUIS
titleSuffix: Azure Cognitive Services
description: Przejrzyj wyrażenia długości przechwycone przez aktywną naukę, aby wybrać pozycję zamierzenia i oznaczyć jednostki do odczytu-World wyrażenia długości; Akceptowanie zmian, uczenie i publikowanie.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219853"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Jak ulepszyć aplikację LUIS, przeglądając punkt końcowy wyrażenia długości

Proces recenzowania punktów końcowych wyrażenia długości pod kątem prawidłowych prognoz jest nazywany [aktywną nauką](luis-concept-review-endpoint-utterances.md). Usługa Active Learning przechwytuje zapytania punktów końcowych i wybiera punkt końcowy użytkownika wyrażenia długości, że nie ma pewności. Te wyrażenia długości są przeglądane w celu wybrania zamiar i oznaczenie jednostek dla tych wyrażenia długości do odczytu. Zaakceptuj te zmiany w przykładzie wyrażenia długości, a następnie Wyszkol i Opublikuj. LUIS następnie identyfikuje wyrażenia długości dokładniej.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Włącz aktywną naukę

Aby włączyć aktywną naukę, należy zarejestrować zapytania użytkownika. Jest to realizowane przez wywołanie [zapytania punktu końcowego](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) z `log=true` ciągu QueryString i wartością.

Użyj portalu LUIS, aby utworzyć poprawną kwerendę punktu końcowego.

1. W [portalu Luis w wersji zapoznawczej](https://preview.luis.ai/)wybierz aplikację z listy aplikacji.
1. Przejdź do sekcji **Zarządzanie** , a następnie wybierz pozycję **zasoby platformy Azure**.
1. Dla przypisanego zasobu przewidywania wybierz pozycję **Zmień parametry zapytania**.

    > [!div class="mx-imgBorder"]
    > ![użyć portalu LUIS do zapisania dzienników wymaganych przez aktywną naukę.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Przełącz pozycję **Zapisz dzienniki** i Zapisz, wybierając pozycję **gotowe**.

    > [!div class="mx-imgBorder"]
    > ![użyć portalu LUIS do zapisania dzienników wymaganych przez aktywną naukę.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Ta akcja powoduje zmianę przykładowego adresu URL przez dodanie parametru `log=true` QueryString. Skopiuj i użyj zmienionego przykładowego adresu URL zapytania podczas tworzenia zapytań prognozowania do punktu końcowego środowiska uruchomieniowego.

## <a name="correct-intent-predictions-to-align-utterances"></a>Popraw przewidywania zamierzeń, aby wyrównać wyrażenia długości

Każdy wypowiedź ma sugerowane zamierzone w kolumnie **cel** .

> [!div class="mx-imgBorder"]
> [![punktu końcowego wyrażenia długości, że LUIS nie ma pewności](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Jeśli zgadzasz się z tym zamiarem, zaznacz znacznik wyboru. Jeśli nie zgadzasz się z sugestiami z wyrównany opcji listy rozwijanej wybierz prawidłowe opcje, a następnie wybierz znacznik wyboru na prawo od intencji wyrównane. Po zaznaczeniu znacznika wyboru wypowiedź jest przenoszony do zamiaru i usunięty z listy **wyrażenia długości punktu końcowego** .

> [!TIP]
> Ważne jest, aby przejść do strony Szczegóły intencji, aby przejrzeć i skorygować przewidywania jednostek ze wszystkich przykładów wyrażenia długości z listy **wyrażenia długości punktu końcowego przeglądu** .

## <a name="delete-utterance"></a>Usuń wypowiedź

Każdy wypowiedź może być usunięty z listy przeglądu. Po usunięciu nie pojawi się na liście ponownie. Ta zasada obowiązuje, nawet wtedy, gdy użytkownik musi wprowadzić ten sam wypowiedź z punktu końcowego.

Jeśli nie masz pewności, czy należy usunąć wypowiedź, przenieś je do zamiaru brak lub Utwórz nowe zamierzenie, takie jak `miscellaneous` i Przenieś wypowiedź do tego celu.

## <a name="disable-active-learning"></a>Wyłącz aktywną naukę

Aby wyłączyć aktywną naukę, nie Rejestruj zapytań użytkownika. Jest to realizowane przez ustawienie [zapytania punktu końcowego](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) z `log=false` ciągu QueryString i wartości lub bez użycia wartości QueryString, ponieważ wartością domyślną jest false.

## <a name="next-steps"></a>Następne kroki

Aby sprawdzić, jak wydajność zwiększa się po oznaczeniu sugerowanych wyrażenia długości, możesz uzyskać dostęp do konsoli testowej, wybierając **test** w górnym panelu. Aby uzyskać instrukcje dotyczące testowania aplikacji za pomocą konsoli testowej, zobacz [uczenie i testowanie aplikacji](luis-interactive-test.md).

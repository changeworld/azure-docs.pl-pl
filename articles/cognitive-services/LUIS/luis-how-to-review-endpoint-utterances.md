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
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: ed0b9450217b06ff145641d9e268ccee28ee49b2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710588"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Jak ulepszyć aplikację LUIS, przeglądając punkt końcowy wyrażenia długości

Proces recenzowania punktów końcowych wyrażenia długości pod kątem prawidłowych prognoz jest nazywany [aktywną nauką](luis-concept-review-endpoint-utterances.md). Usługa Active Learning przechwytuje zapytania punktów końcowych i wybiera punkt końcowy użytkownika wyrażenia długości, że nie ma pewności. Te wyrażenia długości są przeglądane w celu wybrania zamiar i oznaczenie jednostek dla tych wyrażenia długości do odczytu. Zaakceptuj te zmiany w przykładzie wyrażenia długości, a następnie Wyszkol i Opublikuj. LUIS następnie identyfikuje wyrażenia długości dokładniej.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Włącz aktywną naukę

Aby włączyć aktywną naukę, należy zarejestrować zapytania użytkownika. Jest to realizowane przez wywołanie [zapytania punktu końcowego](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) z `log=true` ciągu QueryString i wartością.

## <a name="correct-intent-predictions-to-align-utterances"></a>Popraw przewidywania zamierzeń, aby wyrównać wyrażenia długości

Każdy wypowiedź ma sugerowane przeznaczenie, wyświetlana w **wyrównane intencji** kolumny.

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

Aby sprawdzić, jak wydajność poprawia po etykiety sugerowane wypowiedzi, możesz uzyskać dostęp w konsoli testów wybierając **Test** w górnym panelu. Aby uzyskać instrukcje na temat testowania aplikacji przy użyciu konsoli testów, zobacz [szkolenie i testowanie aplikacji](luis-interactive-test.md).

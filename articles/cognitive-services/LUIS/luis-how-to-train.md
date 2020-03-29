---
title: Aplikacja Pociąg — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Szkolenie to proces nauczania wersji aplikacji language understanding (LUIS), aby poprawić jego zrozumienie języka naturalnego. Trenuj aplikację usługi LUIS po aktualizacji modelu, takich jak dodawanie, edytowanie, etykietowanie lub usuwanie jednostek, intencji lub wypowiedzi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219864"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Trenuj aktywną wersję aplikacji usługi LUIS 

Szkolenie to proces nauczania aplikacji language understanding (LUIS), aby poprawić jej zrozumienie języka naturalnego. Trenuj aplikację usługi LUIS po aktualizacji modelu, takich jak dodawanie, edytowanie, etykietowanie lub usuwanie jednostek, intencji lub wypowiedzi. 

Szkolenie i [testowanie](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po przeszkoleniu aplikacji usługi LUIS, należy przetestować go z wypowiedzi próbki, aby sprawdzić, czy intencje i jednostki są rozpoznawane poprawnie. Jeśli tak nie jest, należy ponownie wprowadzać aktualizacje aplikacji usługi LUIS, szkolić i testować. 

Szkolenie jest stosowane do aktywnej wersji w portalu usługi LUIS. 

## <a name="how-to-train-interactively"></a>Jak trenować interaktywnie

Aby rozpocząć proces iteracyjne w [portalu usługi LUIS,](https://www.luis.ai)należy najpierw trenować aplikację usługi LUIS co najmniej raz. Upewnij się, że każdy zamiar ma co najmniej jedną wypowiedź przed szkoleniem.

1. Uzyskaj dostęp do aplikacji, wybierając jej nazwę na stronie **Moje aplikacje.** 

1. W aplikacji wybierz pozycję **Trenuj** w górnym panelu. 

1. Po zakończeniu szkolenia w górnej części przeglądarki pojawi się powiadomienie.

## <a name="training-date-and-time"></a>Data i godzina szkolenia

Data i godzina treningu to GMT + 2. 

## <a name="train-with-all-data"></a>Trenuj ze wszystkimi danymi

Szkolenie wykorzystuje niewielki procent negatywnego pobierania próbek. Jeśli chcesz użyć wszystkich danych zamiast małego negatywnego próbkowania, użyj [interfejsu API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Ustawienia wersji Użycie interfejsu API UseAllTrainingData

Użyj [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) ustawień `UseAllTrainingData` wersji z ustawionym na true, aby wyłączyć tę funkcję. 

## <a name="unnecessary-training"></a>Niepotrzebne szkolenie

Nie musisz trenować po każdej zmianie. Szkolenie należy wykonać po zastosowaniu grupy zmian do modelu, a następnym krokiem, który chcesz wykonać, jest przetestowanie lub opublikowanie. Jeśli nie musisz testować ani publikować, szkolenie nie jest konieczne. 

## <a name="training-with-the-rest-apis"></a>Szkolenie z interfejsami API REST

Szkolenie w portalu usługi LUIS jest pojedynczym krokiem naciśnięcia **train** przycisku. Szkolenie z interfejsami API REST jest procesem dwuetapowym. Pierwszym z nich jest [żądanie szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) z HTTP POST. Następnie zażądaj [stanu szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) za pomocą protokołu HTTP Get. 

Aby wiedzieć, kiedy szkolenie jest zakończone, musisz sondować stan, dopóki wszystkie modele nie zostaną pomyślnie przeszkolone. 

## <a name="next-steps"></a>Następne kroki

* [Testowanie interakcyjne](luis-interactive-test.md)
* [Testowanie wsadowe](luis-how-to-batch-test.md)

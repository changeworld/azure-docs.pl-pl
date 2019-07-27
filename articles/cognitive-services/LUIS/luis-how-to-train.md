---
title: Uczenie aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Szkolenie to proces uczenia usługi Language Understanding (LUIS) wersji aplikacji, aby zwiększyć jego interpretacja języka naturalnego. Szkolenie aplikacją usługi LUIS po aktualizacji do modelu, takich jak dodawanie, edytowanie, etykietowania lub usunięcie jednostki, intencji lub wypowiedzi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/07/2019
ms.author: diberry
ms.openlocfilehash: 70c29769da2ec092b2b27b83f2f83200d4e727af
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560368"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Uczenie aktywnej wersji aplikacji LUIS 

Szkolenie to proces uczenia aplikacji Language Understanding (LUIS), aby zwiększyć jego interpretacja języka naturalnego. Szkolenie aplikacją usługi LUIS po aktualizacji do modelu, takich jak dodawanie, edytowanie, etykietowania lub usunięcie jednostki, intencji lub wypowiedzi. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Szkolenia i [testowania](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po uczenie jest aplikacją usługi LUIS, testowanie za pomocą wypowiedzi próbki, aby sprawdzić, czy intencje i podmioty są rozpoznawane prawidłowo. Jeśli nie jesteś, aktualizowanie aplikacją usługi LUIS, szkolenie i test ponownie. 

Szkolenie jest stosowany do aktywnej wersji w portalu usługi LUIS. 

## <a name="how-to-train-interactively"></a>Sposób trenowania interaktywnie

Aby rozpocząć proces iteracyjny w [portal usługi LUIS](https://www.luis.ai), najpierw musisz uczyć aplikacją usługi LUIS w co najmniej raz. Upewnij się, że każdy celem ma co najmniej jeden wypowiedź przed szkolenia.

1. Dostęp do aplikacji, wybierając jego nazwę na **Moje aplikacje** strony. 

2. W swojej aplikacji, wybierz **Train** w górnym panelu. 

3. Po zakończeniu szkolenia na pasku powiadomień zielony pojawia się u góry strony w przeglądarce.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>W przypadku co najmniej jeden intencje w swojej aplikacji, które nie zawierają na przykład wypowiedzi nie uczenie aplikacji. Dodawanie wypowiedzi dla wszystkich profilów konwersji. Aby uzyskać więcej informacji, zobacz [Dodawanie wypowiedzi przykład](luis-how-to-add-example-utterances.md).

## <a name="training-date-and-time"></a>Data i godzina szkolenia

Data i godzina szkolenia to GMT + 2. 

## <a name="train-with-all-data"></a>Szkolenie przy użyciu wszystkich danych

Szkolenie używa niewielką część ujemna próbkowania. Na używanie wszystkich danych zamiast krótki przykład ujemna, należy użyć [wersji interfejsu API z ustawień](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) z `UseAllTrainingData` ustawiony na wartość PRAWDA, aby wyłączyć tę funkcję. 

## <a name="unnecessary-training"></a>Niepotrzebne szkolenia

Nie musisz uczyć się po każdej zmianie jednego. Szkolenie powinno się odbywać po grupę zmian są stosowane do modelu, i następnym krokiem, jaki chcesz przeprowadzić test lub opublikować. Jeśli nie potrzebujesz do testów lub opublikować, szkolenie nie jest konieczne. 

## <a name="training-with-the-rest-apis"></a>Szkolenie przy użyciu interfejsów API REST

Szkolenie w portalu usługi LUIS jest w jednym kroku naciśnięcie **Train** przycisku. Szkolenie przy użyciu interfejsów API REST jest procesem dwuetapowym. Pierwsza to [żądania szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) za pomocą żądania HTTP POST. Następnie zażądać [stan szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) za pomocą protokołu HTTP Get. 

Aby dowiedzieć się, po zakończeniu szkolenia, należy wykonać sondowanie stanu, dopóki wszystkie modele pomyślnie są uczone. 

## <a name="next-steps"></a>Kolejne kroki

* [Etykieta sugerowane wypowiedzi z użyciem usługi LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Używanie funkcji w celu zwiększenia wydajności aplikacją usługi LUIS](luis-how-to-add-features.md) 

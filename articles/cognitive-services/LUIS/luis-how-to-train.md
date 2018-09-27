---
title: Szkolenie swoją wersję usługi LUIS
titleSuffix: Azure Cognitive Services
description: Szkolenie to proces uczenia usługi Language Understanding (LUIS) wersji aplikacji, aby zwiększyć jego interpretacja języka naturalnego. Szkolenie aplikacją usługi LUIS po aktualizacji do modelu, takich jak dodawanie, edytowanie, etykietowania lub usunięcie jednostki, intencji lub wypowiedzi.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: f27716cc416b162a5b2df5542d709058f3b3e903
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182043"
---
# <a name="train-your-luis-app-version"></a>Szkolenie używana wersja aplikacji usługi LUIS

Szkolenie to proces uczenia aplikacji Language Understanding (LUIS), aby zwiększyć jego interpretacja języka naturalnego. Szkolenie aplikacją usługi LUIS po aktualizacji do modelu, takich jak dodawanie, edytowanie, etykietowania lub usunięcie jednostki, intencji lub wypowiedzi. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Szkolenia i [testowania](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po uczenie jest aplikacją usługi LUIS, testowanie za pomocą wypowiedzi próbki, aby sprawdzić, czy intencje i podmioty są rozpoznawane prawidłowo. Jeśli nie jesteś, aktualizowanie aplikacją usługi LUIS, szkolenie i test ponownie. 

## <a name="how-to-train"></a>Sposób trenowania
Aby rozpocząć proces iteracyjny, należy najpierw szkolenie aplikacją usługi LUIS w co najmniej raz. Upewnij się, że każdy celem ma co najmniej jeden wypowiedź przed szkolenia.

1. Dostęp do aplikacji, wybierając jego nazwę na **Moje aplikacje** strony. 

2. W swojej aplikacji, wybierz **Train** w górnym panelu. 

3. Po zakończeniu szkolenia na pasku powiadomień zielony pojawia się u góry strony w przeglądarce.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>W przypadku co najmniej jeden intencje w swojej aplikacji, które nie zawierają na przykład wypowiedzi nie uczenie aplikacji. Dodawanie wypowiedzi dla wszystkich profilów konwersji. Aby uzyskać więcej informacji, zobacz [Dodawanie wypowiedzi przykład](luis-how-to-add-example-utterances.md).

## <a name="train-with-all-data"></a>Szkolenie przy użyciu wszystkich danych
Szkolenie używa niewielką część ujemna próbkowania. Na używanie wszystkich danych zamiast krótki przykład ujemna, należy użyć [wersji interfejsu API z ustawień](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) z `UseAllTrainingData` ustawiona na wartość true, aby wyłączyć tę funkcję. 

## <a name="next-steps"></a>Kolejne kroki

* [Etykieta sugerowane wypowiedzi z użyciem usługi LUIS](luis-how-to-review-endoint-utt.md) 
* [Używanie funkcji w celu zwiększenia wydajności aplikacją usługi LUIS](luis-how-to-add-features.md) 
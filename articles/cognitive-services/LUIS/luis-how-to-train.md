---
title: Szkolenie z aplikacją usługi LUIS
titleSuffix: Azure Cognitive Services
description: Szkolenie to proces uczenia aplikacji Language Understanding (LUIS), aby zwiększyć jego interpretacja języka naturalnego. Szkolenie aplikacją usługi LUIS po aktualizacji do modelu, takich jak dodawanie, edytowanie, etykietowania lub usunięcie jednostki, intencji lub wypowiedzi.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 6ed76e35ce07f2848c67ef007ad7d3f062f465f7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036766"
---
# <a name="train-your-luis-app"></a>Szkolenie z aplikacją usługi LUIS

Szkolenie to proces uczenia aplikacji Language Understanding (LUIS), aby zwiększyć jego interpretacja języka naturalnego. Szkolenie aplikacją usługi LUIS po aktualizacji do modelu, takich jak dodawanie, edytowanie, etykietowania lub usunięcie jednostki, intencji lub wypowiedzi. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Szkolenia i [testowania](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po uczenie jest aplikacją usługi LUIS, testowanie za pomocą wypowiedzi próbki, aby sprawdzić, czy intencje i podmioty są rozpoznawane prawidłowo. Jeśli nie jesteś, aktualizowanie aplikacją usługi LUIS, szkolenie i test ponownie. 

## <a name="train-your-app"></a>Uczenie aplikacji
Aby rozpocząć proces iteracyjny, należy najpierw szkolenie aplikacją usługi LUIS w co najmniej raz. Upewnij się, że każdy celem ma co najmniej jeden wypowiedź przed szkolenia.

1. Dostęp do aplikacji, wybierając jego nazwę na **Moje aplikacje** strony. 

2. W swojej aplikacji, wybierz **Train** w górnym panelu. 

3. Po zakończeniu szkolenia na pasku powiadomień zielony pojawia się u góry strony w przeglądarce.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>W przypadku co najmniej jeden intencje w swojej aplikacji, które nie zawierają na przykład wypowiedzi nie uczenie aplikacji. Dodawanie wypowiedzi dla wszystkich profilów konwersji. Aby uzyskać więcej informacji, zobacz [Dodawanie wypowiedzi przykład](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>Kolejne kroki

* [Etykieta sugerowane wypowiedzi z użyciem usługi LUIS](luis-how-to-review-endoint-utt.md) 
* [Używanie funkcji w celu zwiększenia wydajności aplikacją usługi LUIS](luis-how-to-add-features.md) 
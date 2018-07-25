---
title: Szkolenie z aplikacją usługi LUIS — Azure | Dokumentacja firmy Microsoft
description: Language Understanding (LUIS) umożliwia uczenie modelu.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: e947df20141b0b9870f318f410488aea23bafcf5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223188"
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

    ![Przycisk Train (Ucz)](./media/luis-how-to-train/train-button.png)

3. Po zakończeniu szkolenia na pasku powiadomień zielony pojawia się u góry strony w przeglądarce.

    ![Strona szkolenie i testowanie aplikacji](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>W przypadku co najmniej jeden intencje w swojej aplikacji, które nie zawierają na przykład wypowiedzi nie uczenie aplikacji. Dodawanie wypowiedzi dla wszystkich profilów konwersji. Aby uzyskać więcej informacji, zobacz [Dodawanie wypowiedzi przykład](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>Kolejne kroki

* [Etykieta sugerowane wypowiedzi z użyciem usługi LUIS](luis-how-to-review-endoint-utt.md) 
* [Używanie funkcji w celu zwiększenia wydajności aplikacją usługi LUIS](luis-how-to-add-features.md) 
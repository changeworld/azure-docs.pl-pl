---
title: Szkolenie aplikacji LUIS - Azure | Dokumentacja firmy Microsoft
description: Użyj języka opis (LUIS) do uczenia modelu.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 4593954e9e0a60beaa5ee86df848f908b23c6b20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349572"
---
# <a name="train-your-luis-app"></a>Szkolenie LUIS aplikacji

Szkolenie jest proces uczenia aplikację opis języka (LUIS), aby zwiększyć jego opis języka naturalnego. Szkolenie aplikacji LUIS po aktualizacji do modelu, takie jak dodawanie, edytowanie, etykietowania lub usuwanie jednostek, intencje lub zniesławiających. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Szkolenie i [testowania](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po uczenia LUIS aplikacji, przetestować go z przykładowych zniesławiających, aby sprawdzić, czy są prawidłowo rozpoznawane intencje i jednostek. Nie są one aby aktualizacji aplikacji LUIS pociągu i test ponownie. 

## <a name="train-your-app"></a>Szkolenie aplikacji
Aby rozpocząć proces iteracji, należy najpierw uczenia aplikacji LUIS co najmniej raz. Upewnij się, że każdy zamiar ma co najmniej jeden utterance przed szkolenia.

1. Dostęp do aplikacji przez wybranie jego nazwę na **Moje aplikacje** strony. 

2. W aplikacji, wybierz **pociągu** w górnym panelu. 

    ![Przycisk pociągu](./media/luis-how-to-train/train-button.png)

3. Po zakończeniu szkolenia na pasku powiadomień zielony wyświetlany u góry przeglądarki.

    ![Strony pociągu i przetestuj aplikację](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Jeśli masz co najmniej jeden profile aplikacji, które nie zawierają na przykład zniesławiających, nie można później aplikacji. Dodaj zniesławiających dla wszystkich lokalizacji docelowych. Aby uzyskać więcej informacji, zobacz [dodać zniesławiających przykład](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>Kolejne kroki

* [Etykieta sugerowane zniesławiających z LUIS](Label-Suggested-Utterances.md) 
* [Użyj funkcji, aby zwiększyć wydajność aplikacji LUIS](luis-how-to-add-features.md) 
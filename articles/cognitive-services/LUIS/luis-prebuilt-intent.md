---
title: Prebudowane intencje dla Language Understanding
titleSuffix: Azure Cognitive Services
description: Usługa LUIS zawiera zestaw wbudowanych intencji do szybkiego dodawania scenariuszy użytkowników typowe i konwersacyjny.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 4b14d9f39ef5bb0806cfe0720dbaa2fe95b32421
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932614"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>Dodawanie intencji wbudowane dla wspólnych intencji 

Usługa LUIS zawiera zestaw wbudowanych intencji ze wstępnie utworzonych domen do szybkiego dodawania typowych intencje i wypowiedzi. Jest to szybki i łatwy sposób dodać możliwości do aplikacji klienckiej konwersacji, bez konieczności projektowania modeli dla tych możliwości. 

## <a name="add-a-prebuilt-intent"></a>Dodawanie wbudowanych intencji

1. Na **Moje aplikacje** wybierz aplikację. Spowoduje to otwarcie aplikacji **kompilacji** części aplikacji. 

1. Na **intencji** wybierz opcję **Dodawanie intencji wstępnie** na pasku narzędzi powyżej listy intencji. 

1. Wybierz **Utilities.Cancel** intencji w podręcznym oknie dialogowym. 

    ![Dodawanie wbudowanych intencji](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Wybierz **gotowe** przycisku.

## <a name="train-and-test"></a>Szkolenie i testowanie

1. Po dodaniu zamiar uczenie aplikacji, wybierając **szkolenie** w górnym rogu, kliknij prawym przyciskiem myszy pasek narzędzi. 

1. Testowanie nowych intencji, wybierając **testu** na prawym pasku narzędzi. 

1. W polu tekstowym wprowadź wypowiedzi anuluje:

    |Wypowiedź testu|Współczynnik przewidywania|
    |--|:--|
    |Czy chcesz anulować Moje lotu.|0,67|
    |Anulowanie zakupu.|0.52|
    |Anuluj spotkania.|0.56|

    ![Testowanie wbudowanych intencji](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Wstępnie utworzonych jednostek](./luis-prebuilt-entities.md)

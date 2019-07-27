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
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: b3cac3f173cadc8fc24df50f51771d248ab85b2e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563524"
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

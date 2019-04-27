---
title: Jak dodać Pre-Trained jednostki do modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak dodać Pre-trained jednostki do modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: dade442cb04f79ef75e65d6eb29128b105e72b40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707753"
---
# <a name="how-to-add-pre-trained-entities"></a>Jak dodać Pre-trained jednostek
W tym samouczku przedstawiono sposób dodawania Pre-Trained jednostki do modelu uczeń konwersacji.

## <a name="video"></a>Połączenia wideo

[![Samouczek wstępnie przeszkolonych jednostki (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, że ogólne samouczek Bot działa

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Wstępnie przeszkolonych jednostek rozpoznaje typowe rodzaje jednostek, takich jak liczb, dat, kwot pieniężnych i innych.  Działają one "out-of--box," nie wymagają żadnych szkoleń i nie można zmienić ich zachowania w odróżnieniu od jednostek niestandardowych.  Domyślnie Pre-Trained jednostki są wielowartościowy grupowania każdy zidentyfikowany wystąpienia jednostki.

## <a name="steps"></a>Kroki

Rozpocznij na stronie głównej w Interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu

1. Wybierz **nowy Model**.
2. Wprowadź **PretrainedEntities** dla **nazwa**.
3. Wybierz pozycję **Utwórz**.

### <a name="entity-creation"></a>Tworzenie jednostki

1. Wybierz **jednostek** w lewym panelu, a następnie **Nowa jednostka**.
2. Wybierz **wstępnej Trained/datetimeV2** dla **typu jednostki**.
3. Sprawdź **wielowartościowe** umożliwiające jednostki są gromadzone co najmniej jedną wartość. Należy pamiętać, że Pre-Trained jednostki nie może być można negować.
4. Wybierz pozycję **Utwórz**.

![](../media/T08_entity_create.png)

1. Wybierz **akcje** w lewym panelu, a następnie **nowa akcja**.
2. Wprowadź **przypada $builtin-datetimev2** dla **odpowiedzi Botów...** .
3. Wybierz pozycję **Utwórz**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Tworzenie drugiej akcji

1. Wybierz **akcje** w lewym panelu, a następnie **nowa akcja**.
2. Wprowadź **jaka jest data?** dla **odpowiedzi Botów...** . Wstępnie przeszkolonych jednostki nie może być **wymaganych jednostek** rozpoznawanym przez domyślne dla wszystkich wypowiedzi.
3. Wprowadź **builtin datetimev2** dla **dyskwalifikacji jednostek**.
4. Wybierz pozycję **Utwórz**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Uczenie modelu

1. Wybierz **okien dialogowych Train** w lewym panelu, a następnie **okno dialogowe Nowy Train**.
2. Wprowadź **hello** dla wypowiedź użytkownika w panelu po lewej stronie rozmowy.
3. Wybierz **wynik akcji**.
4. Wybierz **jaka jest data?** z listy akcji
5. Wprowadź **już dziś** dla wypowiedź użytkownika w panelu po lewej stronie rozmowy.
    - **Już dziś** wypowiedź jest automatycznie rozpoznawany przez wstępnie szkolone modele w usługi LUIS.
    - Kursor wartości jednostki Pre-Trained przedstawia dodatkowe dane udostępniane przez usługi LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Mechanizmy rozpoznawania elementów jednostki](./09-entity-resolvers.md)

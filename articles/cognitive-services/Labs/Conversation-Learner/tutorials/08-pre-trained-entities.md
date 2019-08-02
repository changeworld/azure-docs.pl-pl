---
title: Jak dodać wstępnie przeszkolonych jednostek do modelu Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak dodać wstępnie przeszkolonych jednostek do modelu Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: fceca459b80725e1a9c8b7dbc6a4387ea98dbb7b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704117"
---
# <a name="how-to-add-pre-trained-entities"></a>Jak dodać wstępnie przeszkolonych jednostek
W tym samouczku przedstawiono sposób dodawania wstępnie przeszkolonych jednostek do modelu Conversation Learner.

## <a name="video"></a>Połączenia wideo

[![Samouczek dotyczący wstępnie przeszkolonych jednostek — wersja zapoznawcza](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Wstępnie przeszkolone jednostki rozpoznają typowe typy jednostek, takie jak liczby, daty, kwoty pieniężne i inne.  Działają one jako "wbudowane" — nie wymagają żadnego szkolenia ani nie można zmienić ich zachowania w przeciwieństwie do jednostek niestandardowych.  Domyślnie wstępnie przeszkolonymi jednostkami są wartości wielowartościowe, które gromadzą każde zidentyfikowane wystąpienie jednostki.

## <a name="steps"></a>Kroki

Uruchom stronę główną w interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu

1. Wybierz pozycję **Nowy model**.
2. Wprowadź **PretrainedEntities** dla **nazwy**.
3. Wybierz pozycję **Utwórz**.

### <a name="entity-creation"></a>Tworzenie jednostki

1. Wybierz pozycję **jednostki** w lewym panelu, a następnie pozycję **Nowa jednostka**.
2. Wybierz opcję **pre-szkolony/datetimeV2** dla **typu jednostki**.
3. Sprawdź **wiele** wartości, aby umożliwić jednostkom kumulowanie co najmniej jednego elementu. Należy zauważyć, że wstępnie przeszkolone jednostki nie mogą być negacją.
4. Wybierz pozycję **Utwórz**.

![](../media/T08_entity_create.png)

1. W lewym panelu wybierz pozycję **Akcje** , a następnie pozycję **Nowa akcja**.
2. Wprowadź **datę $BUILTIN-datetimev2** dla **odpowiedzi bot...**
3. Wybierz pozycję **Utwórz**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Utwórz drugą akcję

1. W lewym panelu wybierz pozycję **Akcje** , a następnie pozycję **Nowa akcja**.
2. Wprowadź **datę?** dla **odpowiedzi bot...** Wstępnie przeszkolonych jednostek nie mogą być **wymagane jednostki** , ponieważ są one rozpoznawane domyślnie dla wszystkich wyrażenia długości.
3. Wprowadź **wbudowaną datetimev2** dla niekwalifikujących się **jednostek**.
4. Wybierz pozycję **Utwórz**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Uczenie modelu

1. Wybierz pozycję **okna dialogowe szkolenia** w lewym panelu, a następnie **okno dialogowe nowe szkolenie**.
2. Wprowadź **Hello** dla wypowiedź użytkownika w lewym panelu rozmowy.
3. Wybierz **Akcje oceny**.
4. Wybierz **datę?** z listy akcje
5. Wprowadź **dziś** dla wypowiedź użytkownika w lewym panelu rozmowy.
    - **Dzisiaj** wypowiedź są automatycznie rozpoznawane przez wstępnie nauczone modele w Luis.
    - Umieszczenie wskaźnika myszy nad wartościami wstępnie przeszkolonych jednostek pokazuje dodatkowe dane dostarczone przez LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rozwiązania jednostek](./09-entity-resolvers.md)

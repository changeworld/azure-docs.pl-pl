---
title: Jak używać jednostek wielowartościowych z modelem Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać jednostek wielowartościowych z modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704082"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Jak używać jednostek wielowartościowych z modelem Conversation Learner
W tym samouczku przedstawiono Właściwość wielowartościowa jednostek.

## <a name="video"></a>Połączenia wideo

[![Samouczek dotyczący jednostek wielowartościowych — wersja zapoznawcza](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Jednostki wielowartościowe gromadzą wartości na liście zamiast przechowywania pojedynczej wartości.  Te jednostki są przydatne, gdy użytkownicy mogą określić więcej niż jedną wartość. Na przykład toppings na Pizza.

Jednostki oznaczone jako wielowartościowe będą miały każde rozpoznane wystąpienie jednostki dołączone do listy w pamięci bot. Po dołączeniu kolejnego rozpoznawania do wartości jednostki zamiast zastępowania.

## <a name="steps"></a>Kroki

Uruchom stronę główną w interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu

1. Wybierz pozycję **Nowy model**.
2. Wprowadź **MultiValueEntities** dla **nazwy**.
3. Wybierz pozycję **Utwórz**.

### <a name="entity-creation"></a>Tworzenie jednostki

1. Wybierz pozycję **jednostki** w lewym panelu, a następnie pozycję **Nowa jednostka**.
2. Wybierz pozycję niestandardowa przeszkolony dla **typu jednostki**.
3. Wprowadź **toppings** dla **nazwy jednostki**.
4. Sprawdź **wiele** wartości, aby umożliwić jednostkom kumulowanie co najmniej jednego elementu.
5. Sprawdź **negację**.
6. Wybierz pozycję **Utwórz**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Utwórz pierwszą akcję

1. W lewym panelu wybierz pozycję **Akcje** , a następnie pozycję **Nowa akcja**.
2. Wprowadź **tutaj toppings: $toppings** dla **odpowiedzi bot..** . Znak dolara wiodącego wskazuje odwołanie do jednostki.
3. Wybierz pozycję **Utwórz**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Utwórz drugą akcję

1. W lewym panelu wybierz pozycję **Akcje** , a następnie pozycję **Nowa akcja**.
2. Podaj **, jakie toppings chcesz?** dla **odpowiedzi bot...**
3. Wprowadź **toppings** wcelu odkwalifikowania się.
4. Wybierz pozycję **Utwórz**.

Teraz masz dwie akcje.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Uczenie modelu

1. Wybierz pozycję **okna dialogowe szkolenia** w lewym panelu, a następnie **okno dialogowe nowe szkolenie**.
2. Wprowadź wartość " **Witaj** " dla wypowiedź użytkownika w lewym panelu rozmowy.
3. Wybierz **Akcje oceny**.
4. Wybierz, **co toppings chcesz?** z listy akcje. Percentyl ma wartość 100%, ponieważ jedyną prawidłową akcją opartą na ograniczeniach.
5. Wprowadź **sery i grzyby** dla wypowiedź użytkownika w lewym panelu rozmowy.
6. Wyróżnij **ser** , a następnie wybierz pozycję **+ toppings**.
7. Zaznacz pozycję grzyby, a następnie wybierz opcję **+ toppings**.
8. Wybierz **Akcje oceny**.
9. Wybierz **toppings: $toppings** z listy akcji.
10. Wprowadź wartość **Dodaj paprykę** dla następnej wypowiedź użytkownika w lewym panelu rozmowy.
11. Wyróżnij **paprykę** , a następnie wybierz pozycję **+ toppings**.
12. Wybierz **Akcje oceny**.
13. Wybierz **toppings: $toppings** z listy akcji.
14. Wprowadź wartość " **Usuń ser** " dla trzeciego wypowiedź użytkownika w lewym panelu rozmowy.
15. Wyróżnij **ser** , a następnie wybierz pozycję **-toppings**.
16. Wybierz **Akcje oceny**.
17. Wybierz **toppings: $toppings** z listy akcji.

![](../media/T07_training.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wstępnie przeszkolonych jednostek](./08-pre-trained-entities.md)

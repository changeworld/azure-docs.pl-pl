---
title: Jak używać jednostek negacji z modelem Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać jednostek negacji z modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4edbfc396d4ab32b991b084a7b738f04b5205418
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704106"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Jak używać jednostek negacji z modelem Conversation Learner

W tym samouczku przedstawiono Właściwość "Negacja" jednostek.

## <a name="video"></a>Połączenia wideo

[![Samouczek dotyczący jednostek z negacją](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Właściwość "Negacja" jednostki umożliwia etykietowanie zarówno normalnych, jak i ujemnych wystąpień jednostki, uczenie się w oparciu o modele dodatnie i ujemne, a następnie czyści wartość istniejącej jednostki. Jednostki z ustawioną właściwością negacji są nazywane jednostkami negacji w obstawce.

## <a name="steps"></a>Kroki

Uruchom stronę główną w interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu

1. Wybierz pozycję **Nowy model**.
2. Wprowadź **NegatableEntity** dla **nazwy**.
3. Wybierz pozycję **Utwórz**.

### <a name="entity-creation"></a>Tworzenie jednostki

1. Wybierz pozycję **jednostki** w lewym panelu, a następnie pozycję **Nowa jednostka**.
2. Wybierz pozycję niestandardowa przeszkolony dla **typu jednostki**.
3. Wprowadź **nazwę** dla **nazwy jednostki**.
4. Zaznacz opcję Negacja, aby umożliwić użytkownikom podanie wartości jednostki, lub wymów coś *nie* jest wartością jednostki, usuwając pasującą wartość jednostki.
5. Wybierz pozycję **Utwórz**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Utwórz pierwszą akcję

1. W lewym panelu wybierz pozycję **Akcje** , a następnie pozycję **Nowa akcja**.
2. Wprowadź **nieznane nazwy.** na **odpowiedź bot...**
3. Wprowadź **nazwę** **uprawniającą**do niekwalifikowania.
4. Wybierz pozycję **Utwórz**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Utwórz drugą akcję

1. W lewym panelu wybierz pozycję **Akcje** , a następnie pozycję **Nowa akcja**.
2. Podaj **nazwę użytkownika. Jest $name.** na **odpowiedź bot...**
3. Wybierz pozycję **Utwórz**.

> [!NOTE]
> Jednostka **nazwy** została automatycznie dodana jako **wymagane jednostki** przez odwołanie w odpowiedzi wypowiedź.

Teraz masz dwie akcje.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Uczenie modelu

1. Wybierz pozycję **okna dialogowe szkolenia** w lewym panelu, a następnie **okno dialogowe nowe szkolenie**.
2. Wprowadź **Hello** dla wypowiedź użytkownika w lewym panelu rozmowy.
3. Wybierz **Akcje oceny**.
4. Wybierz opcję **nie znam nazwy.** z listy akcje. Percentyl ma wartość 100%, ponieważ jedyną prawidłową akcją opartą na ograniczeniach.
5. Wprowadź **My Name to Piotr** dla wypowiedź użytkownika w lewym panelu rozmowy.
6. Wyróżnij **Piotr** , a następnie wybierz pozycję **+ Nazwa**. Jednostki negacjowe mają dwa wystąpienia: (+) plus dodaje lub zastępuje wartość; (-) minus usuwa wartość.
7. Wybierz **Akcje oceny**. Obiekt **name** jest teraz zdefiniowany jako **Piotr** w pamięci modelu **, więc znam swoją nazwę. $Name** akcja jest dostępna.
8. Wybierz **opcję Znam swoją nazwę. Jest $name.** z listy akcje.
9. Wprowadź **moją nazwę nie jest Piotrem.** dla wypowiedź użytkownika w lewym panelu rozmowy.
10. Wyróżnij **Piotr** , a następnie wybierz pozycję **-name** , aby wyczyścić wartość z jednostki **nazwy** .
11. Wybierz **Akcje oceny**.
12. Wybierz opcję **nie znam nazwy.** z listy akcje.
13. Wprowadź **moją nazwę Susan.** w przypadku trzeciego wypowiedź użytkownika w lewym panelu rozmowy.
14. Wyróżnij **Susan** then **+ name** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Jednostki wielowartościowe](./07-multi-value-entities.md)

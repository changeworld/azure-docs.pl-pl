---
title: Jak używać wielowartościowych jednostek z modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak wielowartościowych jednostek za pomocą modelu uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 1f62def5e498f3f744beaed0cda207e1a75bfdf2
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387960"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Jak używać wielowartościowych jednostek z modelu uczeń konwersacji
Ten samouczek pokazuje właściwości wielu wartości jednostki.

## <a name="video"></a>Połączenia wideo

[![Samouczek wielowartościowych jednostki (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, że ogólne samouczek Bot działa

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Jednostki wielowartościowych wzrosnąć wartości listy, zamiast magazynować pojedynczej wartości.  Te jednostki są przydatne, gdy użytkownicy mogą określić więcej niż jedną wartość. Toppings na głosi, na przykład.

Jednostki oznaczone jako wielowartościowy odniesie każdego rozpoznane wystąpienia jednostki dołączana do listy w pamięci Botów. Rozpoznawanie kolejnych dołącza do jednostki wartości, zamiast zastępowania.

## <a name="steps"></a>Kroki

Rozpocznij na stronie głównej w Interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu

1. Wybierz **nowy Model**.
2. Wprowadź **MultiValueEntities** dla **nazwa**.
3. Wybierz pozycję **Utwórz**.

### <a name="entity-creation"></a>Tworzenie jednostki

1. Wybierz **jednostek** w lewym panelu, a następnie **Nowa jednostka**.
2. Wybierz **niestandardowych skonfigurowanych pod kątem** dla **typu jednostki**.
3. Wprowadź **toppings** dla **nazwa jednostki**.
4. Sprawdź **wielowartościowe** umożliwiające jednostki są gromadzone co najmniej jedną wartość.
5. Sprawdź **można negować**.
6. Wybierz pozycję **Utwórz**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Tworzenie pierwszej akcji

1. Wybierz **akcje** w lewym panelu, a następnie **nowa akcja**.
2. Wprowadź **Oto Twoja toppings: $toppings** dla **odpowiedzi Botów...** . Wiodący znak dolara wskazuje odwołanie do jednostki.
3. Wybierz pozycję **Utwórz**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Tworzenie drugiej akcji

1. Wybierz **akcje** w lewym panelu, a następnie **nowa akcja**.
2. Wprowadź **toppings, które chcesz użyć?** dla **odpowiedzi Botów...** .
3. Wprowadź **toppings** dla **dyskwalifikacji uprawnia**.
4. Wybierz pozycję **Utwórz**.

Masz teraz dwie akcje.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Uczenie modelu

1. Wybierz **okien dialogowych Train** w lewym panelu, a następnie **okno dialogowe Nowy Train**.
2. Wprowadź **cześć** dla wypowiedź użytkownika w panelu po lewej stronie rozmowy.
3. Wybierz **wynik akcji**.
4. Wybierz **toppings, które chcesz użyć?** z listy akcji. Percentyla wynosi 100%, co jedyne prawidłowe działanie na podstawie ograniczeń.
5. Wprowadź **ser i grzyby** dla wypowiedź użytkownika w panelu po lewej stronie rozmowy.
6. Wyróżnij **ser** polecenie **+ toppings**.
7. Wyróżnij **grzyby** polecenie **+ toppings**.
8. Wybierz **wynik akcji**.
9. Wybierz **Oto Twoja toppings: $toppings** z listy akcji.
10. Wprowadź **Dodaj chili** dla użytkownika wypowiedź dalej w panelu po lewej stronie rozmowy.
11. Wyróżnij **chili** polecenie **+ toppings**.
12. Wybierz **wynik akcji**.
13. Wybierz **Oto Twoja toppings: $toppings** z listy akcji.
14. Wprowadź **Usuń ser** dla wypowiedź trzeciego użytkownika w panelu po lewej stronie rozmowy.
15. Wyróżnij **ser** polecenie **-toppings**.
16. Wybierz **wynik akcji**.
17. Wybierz **Oto Twoja toppings: $toppings** z listy akcji.

![](../media/T07_training.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wstępnie przeszkolonych jednostek](./08-pre-trained-entities.md)

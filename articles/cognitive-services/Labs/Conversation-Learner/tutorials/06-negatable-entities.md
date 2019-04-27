---
title: Jak można negować jednostek za pomocą modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak można negować jednostek za pomocą modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 78dc759632c4fc3116a59ea1e5bc0b93200bca45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708025"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Jak można negować jednostek za pomocą modelu uczeń konwersacji

Ten samouczek przedstawia "Negatable" właściwość jednostki.

## <a name="video"></a>Połączenia wideo

[![Samouczek można negować jednostki (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, że ogólne samouczek Bot działa

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Właściwość "Negatable" jednostki umożliwia etykiety obu normalny (pozytywna) i ujemne wystąpień jednostki, uczyć modele dodatnie i ujemne w oparciu i wyczyszczenie tej wartości istniejącej jednostki. Jednostki z ich ustawioną właściwość "Negatable" nazywane są jednostkami można negować w bardziej oszczędny konwersacji.

## <a name="steps"></a>Kroki

Rozpocznij na stronie głównej w Interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu

1. Wybierz **nowy Model**.
2. Wprowadź **NegatableEntity** dla **nazwa**.
3. Wybierz pozycję **Utwórz**.

### <a name="entity-creation"></a>Tworzenie jednostki

1. Wybierz **jednostek** w lewym panelu, a następnie **Nowa jednostka**.
2. Wybierz **niestandardowych skonfigurowanych pod kątem** dla **typu jednostki**.
3. Wprowadź **nazwa** dla **nazwa jednostki**.
4. Sprawdź **Negatable** aby użytkownicy mogli podać wartość jednostki lub powiedzieć coś, co jest *nie* jednostki wartości, a tym samym usuwanie pasującej wartości jednostki.
5. Wybierz pozycję **Utwórz**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Tworzenie pierwszej akcji

1. Wybierz **akcje** w lewym panelu, a następnie **nowa akcja**.
2. Wprowadź **nie wiem, swoją nazwę.** Aby uzyskać **odpowiedzi Botów...** .
3. Wprowadź **nazwa** dla **dyskwalifikacji uprawnia**.
4. Wybierz pozycję **Utwórz**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Tworzenie drugiej akcji

1. Wybierz **akcje** w lewym panelu, a następnie **nowa akcja**.
2. Wprowadź **sprawdzić swoją nazwę. It is $name.** Aby uzyskać **odpowiedzi Botów...** .
3. Wybierz pozycję **Utwórz**.

> [!NOTE]
> **Nazwa** jednostka została automatycznie dodana jako **wymaganych jednostek** poprzez odwołanie w wypowiedź odpowiedzi.

Masz teraz dwie akcje.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Uczenie modelu

1. Wybierz **okien dialogowych Train** w lewym panelu, a następnie **okno dialogowe Nowy Train**.
2. Wprowadź **hello** dla wypowiedź użytkownika w panelu po lewej stronie rozmowy.
3. Wybierz **wynik akcji**.
4. Wybierz **nie wiem, swoją nazwę.** z listy akcji. Percentyla wynosi 100%, co jedyne prawidłowe działanie na podstawie ograniczeń.
5. Wprowadź **Nazywam się Frank** dla wypowiedź użytkownika w panelu po lewej stronie rozmowy.
6. Wyróżnij **Frank** polecenie **+ nazwa**. Jednostki można negować mają dwa wystąpienia: (+) oraz dodaje lub zastępuje wartość; (-) znak minus Usuwa wartość.
7. Wybierz **wynik akcji**. **Nazwa** jednostki jest teraz zdefiniowany jako **Frank** w modelu pamięci, więc **sprawdzić swoją nazwę. Jest $name** akcja jest dostępna.
8. Wybierz **sprawdzić swoją nazwę. It is $name.** z listy akcji.
9. Wprowadź **Moja nazwa nie jest Frank.** Aby uzyskać wypowiedź użytkownika w panelu po lewej stronie rozmowy.
10. Wyróżnij **Frank** następnie wybierz pozycję **— nazwa** wyczyszczenie tej wartości z **nazwa** jednostki.
11. Wybierz **wynik akcji**.
12. Wybierz **nie wiem, swoją nazwę.** z listy akcji.
13. Wprowadź **Nazywam się Susan.** Aby uzyskać wypowiedź trzeciego użytkownika w panelu po lewej stronie rozmowy.
14. Wyróżnij **Susan** następnie **+ nazwa** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wartość wielu jednostek](./07-multi-value-entities.md)

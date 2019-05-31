---
title: Jak używać właściwości "Oczekiwaniami Entity" Akcje uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać właściwości "Oczekiwaniami Entity" modelu uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 48a8abd401ff7191da4709c726042b566d140b78
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387824"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Jak użyć właściwości "Oczekiwaniami Entity" w akcji

Ten samouczek przedstawia właściwość "Oczekiwaniami Entity" w akcji.

## <a name="video"></a>Połączenia wideo

[![Samouczek oczekiwanej jednostki (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Użyj właściwości "Oczekiwaniami Entity" w akcji, aby zapisać odpowiedzi użytkownika do tej akcji w jednostce.

Podczas dodawania jednostki z właściwością "Oczekiwaniami Entity" akcję, system wykonują następujące czynności:

1. Rozpocznij od próbując dopasować jednostki przy użyciu usługi machine learning Model oparty na jednostki wyodrębniania
2. Przypisz wypowiedź całego użytkownika do $entity oparte na Algorytm heurystyczny, jeśli nie zostaną znalezione nie jednostki
3. Wywołaj `EntityDetectionCallback`i przejdź do wybór akcji.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk "Nowy Model".
2. W polu "Nazwa" wpisz "ExpectedEntities" i naciśnij klawisz enter.
3. Kliknij przycisk "Utwórz".

### <a name="entity-creation"></a>Tworzenie jednostki

1. Na lewym panelu kliknij przycisk "Jednostki", a następnie przycisku "Nowa jednostka".
2. Wybierz pozycję "Uczony niestandardowe" dla "Typu jednostek".
3. Wpisz "name", "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

> [!NOTE]
> "Niestandardowa Uczonego" typ jednostki oznacza, że może być uczony tej jednostki w przeciwieństwie do innych typów jednostek.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Tworzenie pierwszej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "Jak ma nazwę"?
3. W polu "Oczekiwano jednostki" wpisz "name".
4. Kliknij przycisk "Utwórz".

> [!NOTE]
> Obiekty wykryte i wyodrębnione z odpowiedzi użytkownika zostanie zapisany do jednostki "name", jeśli zostanie wybrana ta akcja. Brak jednostek w przypadku wykrycia całej odpowiedzi zostaną zapisane do tej jednostki.

### <a name="create-the-second-action"></a>Tworzenie drugiej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi...", wpisz "Hi $name"!
3. Kliknij przycisk "Utwórz".

> [!NOTE]
> Jednostka "name" automatycznie został dodany jako "Wymagana jednostki" przez odwołanie w odpowiedzi.

Masz teraz dwie akcje.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Uczenie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hi."
    - Symuluje to użytkownika po stronie konwersacji.
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "Jak ma nazwę"?
    - "Cześć $name!" Nie można wybrać odpowiedzi, jako odpowiedź wymaga "name" jednostki teraz zdefiniowane w modelu pamięci.
5. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Piotr".
    - "Piotr" jest podświetlona jako jednostka, w oparciu o heurystykę, możemy skonfigurować wcześniej zapisać odpowiedź jako jednostka.
6. Kliknij przycisk "Wynik akcje".
    - "name" jednostki teraz jest zdefiniowany jako "Piotr" w modelu pamięci, więc można wybierać jako akcję akcji "Hello $name".
7. Wybierz odpowiedź "Hi $name"!
8. Kliknij przycisk "Zapisz".

Dodawanie pociągów dalszych danych wejściowych alternatywnych modelu.

1. W polu "Dodaj alternatywne dane wejściowe..." wpisz "Jestem Jose."
    - Model nie może rozpoznać nazwę jako jednostki tak powoduje zaznaczenie całego bloku tekstu jako wartość jednostki
2. Kliknij przycisk frazę "Jestem Jose", a następnie kliknij ikonę Kosza na śmieci.
3. Kliknij pozycję "Jose", a następnie kliknij "name", z listy jednostek.
4. Kliknij wynik akcji.
5. Wybierz odpowiedź, "Piotr Witaj!"
6. Kliknij przycisk "Zapisz".

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Jednostki można negować](./06-negatable-entities.md)

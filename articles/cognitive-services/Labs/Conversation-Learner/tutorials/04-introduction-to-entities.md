---
title: Sposób użycia jednostek z modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak i używanie jednostek z modelu uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 3d9e2498a23ad49eb014cb0f81c819f3f63eef5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387793"
---
# <a name="introduction-to-entities"></a>Wprowadzenie do jednostek

W tym samouczku wprowadza jednostki, dyskwalifikacji jednostek, wymaganych jednostek i ich użycie w ramach uczeń konwersacji.

## <a name="video"></a>Połączenia wideo

[![Wprowadzenie do samouczka jednostki (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Wymagania

Ten samouczek wymaga, że ogólne samouczek Bot działa

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Jednostki przechwycić informacje, że Bot potrzebuje do wykonywania swoich zadań, za pomocą wyodrębniania z wypowiedzi użytkowników lub przypisania przez kod niestandardowy. Jednostki się również ograniczyć dostępność działania przez jawnie klasyfikowane jako "Required" lub "Dyskwalifikacji".

- Wymagane jednostki musi znajdować się w pamięci modelu w kolejności dla akcji, które mają być dostępne
- Dyskwalifikacji jednostki musi *nie* znajdować się w pamięci modelu w kolejności dla akcji, które mają być dostępne

Ten samouczek koncentruje się na jednostkach niestandardowych. Wstępnie przeszkolonych wielu wartości, można negować jednostek i programowe jednostek zostały wprowadzone w innych samouczkach.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk "Nowy Model".
2. W polu "Nazwa" wpisz "IntroToEntities" i naciśnij klawisz enter.
3. Kliknij przycisk "Utwórz".

### <a name="entity-creation"></a>Tworzenie jednostki

1. Na lewym panelu kliknij przycisk "Jednostki", a następnie przycisku "Nowa jednostka".
2. Wybierz pozycję "Uczony niestandardowe" dla "Typu jednostek".
3. Wpisz "city", "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

> [!NOTE]
> "Niestandardowa Uczonego" typ jednostki oznacza, że może być uczony tej jednostki w przeciwieństwie do innych typów jednostek.

### <a name="create-the-actions"></a>Tworzenie akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "Nie wiem, jakim mieście mają."
3. W polu "Jednostki dyskwalifikacji" wpisz "Miasto".
4. Kliknij przycisk "Utwórz".

> [!NOTE]
> Dodawanie jednostki "city" do "Dyskwalifikacji jednostki" będzie zdyskwalifikowany tej akcji, Bot pod uwagę, gdy "city" jednostki jest zdefiniowany w pamięci Botów.

Teraz Utwórz drugiej akcji.

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "pogody w $city jest prawdopodobnie sunny".
3. Kliknij przycisk "Utwórz".

> [!NOTE]
> Jednostka "city" został dodany automatycznie na liście jednostek wymaganych przez odwołanie w odpowiedzi.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Uczenie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hello".
    - Symuluje to użytkownika po stronie konwersacji.
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "Nie wiem, jakim mieście mają."
5. Jako użytkownik odpowiada za pomocą "Seattle".
6. Kliknij przycisk "Wynik akcje".
7. Wybierz odpowiedź "Pogody w $city jest prawdopodobnie sunny".
8. Kliknij przycisk "Zapisz".

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Oczekiwanej jednostki](./05-expected-entity.md)

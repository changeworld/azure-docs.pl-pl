---
title: Jak używać wywołania zwrotnego wykrywania jednostek z modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak wywołania zwrotnego wykrywania jednostki za pomocą modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 576dc6bd44360f73c4133907233e59e5f51837b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707578"
---
# <a name="how-to-use-entity-detection-callback"></a>Jak używać wywołania zwrotnego wykrywania jednostki

Ten samouczek pokazuje wywołanie zwrotne wykrywania jednostki oraz przedstawia typowy wzorzec rozpoznawania jednostek.

## <a name="video"></a>Połączenia wideo

[![Jednostki wykrywania wywołania zwrotnego samouczka (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby `tutorialEntityDetectionCallback` bot jest uruchomiona.

    npm run tutorial-entity-detection

## <a name="details"></a>Szczegóły
Wywołania zwrotne wykrywania jednostki umożliwiają modyfikowanie zachowania rozpoznawania jednostek i manipulowania jednostki za pomocą kodu. Ta funkcja zostaną przedstawione przez Instruktaż jednostki typowy wzorzec projektowania wywołania zwrotnego wykrywania. Na przykład rozpoznawanie "big Data firmy apple" do "new york".

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk "Nowy Model".
2. W polu "Nazwa" wpisz "EntityDetectionCallback" i naciśnij klawisz enter.
3. Kliknij przycisk "Utwórz".

W tym przykładzie potrzebne są trzy jednostek, więc Utwórz trzy.

### <a name="create-the-custom-trained-entity"></a>Tworzenie niestandardowej jednostki Uczonego

1. Na lewym panelu kliknij przycisk "Jednostki", a następnie przycisku "Nowa jednostka".
2. Wybierz pozycję "Uczony niestandardowe" dla "Typu jednostek".
3. Typ "Miasto" "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

### <a name="create-the-first-programmatic-entity"></a>Tworzenie pierwszej jednostki programowe

1. Kliknij przycisk "Nowa jednostka".
2. Wybierz pozycję "Programowy" dla "Typu jednostek".
3. Typ "CityUnknown", "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

### <a name="create-the-first-programmatic-entity"></a>Tworzenie pierwszej jednostki programowe

1. Kliknij przycisk "Nowa jednostka".
2. Wybierz pozycję "Programowy" dla "Typu jednostek".
3. Typ "CityResolved", "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

Teraz Utwórz trzy czynności.

### <a name="action-creation"></a>Tworzenie akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "Miasto, której chcesz użyć?"
3. W polu "Oczekiwano jednostki w odpowiedzi użytkownik..." wpisz "Miasto".
4. W polu "Jednostki dyskwalifikacji" wpisz "Miasto".
5. Kliknij przycisk "Utwórz".
6. Kliknij przycisk "Nowa akcja".
7. W "Botów odpowiedzi..." pola, wpisz "Miasto, której chcesz użyć?"
8. W polu "Oczekiwano jednostki w odpowiedzi użytkownik..." wpisz "Nie wiem, nazwa miejscowości."
9. Kliknij przycisk "Utwórz".
10. Kliknij przycisk "Nowa akcja".
11. W "Botów odpowiedzi..." pola, wpisz "$CityResolved to bardzo dobre rozwiązanie".
12. Kliknij przycisk "Utwórz".

Oto kod wywołania zwrotnego:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Uczenie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hi"
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "Miasto, której chcesz użyć?"
5. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "big apple"
6. Kliknij przycisk "Wynik akcje".
    - Kliknięcie przycisku wyzwala wywołanie zwrotne wykrywania jednostki
    - Wywołanie zwrotne kod ustawia wartość jednostki CityResolved poprawnie "new york"
7. Wybierz odpowiedź "new york to bardzo dobre rozwiązanie".

Ten wzorzec jest typowy dla wielu scenariuszy botów. Wypowiedzi użytkowników i jednostek wyodrębnione są dostarczane do logiki biznesowej, a tej logiki przekształca wypowiedź w formie kanonicznej, który następnie jest zapisywany do programowego jednostek dla kolejnych włącza okna dialogowego.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołania zwrotne sesji](./13-session-callbacks.md)

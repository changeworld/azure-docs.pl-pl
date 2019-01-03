---
title: Jak można negować jednostek za pomocą modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak można negować jednostek za pomocą modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 34806803c50308a288007bf3bad129075ca8bc6d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796950"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Jak można negować jednostek za pomocą modelu uczeń konwersacji

Ten samouczek przedstawia "Negatable" właściwość jednostki.

## <a name="video"></a>Połączenia wideo

[![Samouczek można negować jednostki (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Właściwość "Negatable" jednostki umożliwia etykiety obu normalny (pozytywna) i ujemne wystąpień jednostki, uczyć modele dodatnie i ujemne w oparciu i wyczyszczenie tej wartości istniejącej jednostki. Jednostki z ich ustawioną właściwość "Negatable" nazywane są jednostkami można negować w bardziej oszczędny konwersacji.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk "Nowy Model".
2. W polu "Nazwa" wpisz "NegatableEntity" i naciśnij klawisz enter.
3. Kliknij przycisk "Utwórz".

### <a name="entity-creation"></a>Tworzenie jednostki

1. Na lewym panelu kliknij przycisk "Jednostki", a następnie przycisku "Nowa jednostka".
2. Wybierz opcję "Niestandardowy" dla "Typu jednostek".
3. Wpisz "name", "Nazwa jednostki".
4. Zaznacz to pole wyboru "Negatable".
    - Sprawdzanie ta właściwość umożliwia użytkownikowi wprowadzenia wartości jednostki lub Załóżmy, że coś jest nie *nie* wartości jednostki. W tym ostatnim przypadku wynik jest usunięcie pasującej wartości jednostki.
5. Kliknij przycisk "Utwórz".

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>Tworzenie pierwszej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "Nie wiem swoją nazwę."
3. W polu "Dyskwalifikacji uprawnia" wpisz "name".
4. Kliknij przycisk "Utwórz".

### <a name="create-the-second-action"></a>Tworzenie drugiej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "wiem, swoją nazwę. To $name".
3. Kliknij przycisk "Utwórz".

> [!NOTE]
> Jednostka "name" automatycznie został dodany jako "Wymagana jednostki" przez odwołanie w odpowiedzi.

Masz teraz dwie akcje.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>Uczenie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hello".
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "Nie wiem swoją nazwę."
    - Percentyla wynosi 100%, zgodnie z jedynymi prawidłowymi akcji na podstawie ograniczeń.
5. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Moja nazwa jest Piotr"
6. Wybierz pozycję "Piotr" i wybierz etykietę "+ Nazwa"
    - Istnieją dwa wystąpienia pod kątem "name" jednostki: "+ Nazwa" i "-name".  (+) A także dodaje lub zastępuje wartość. (-) Znak minus Usuwa wartość.
7. Kliknij przycisk "Wynik akcje".
    - Jednostka "name" teraz jest zdefiniowany jako "Piotr" w modelu pamięci, dlatego "I know swoją nazwę. To $name"Akcja jest dostępna.
8. Wybierz odpowiedź "wiem, swoją nazwę. To $name".
9. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Moja nazwa nie jest Frank."
10. Wybierz "Piotr", a następnie wybierz etykietę "-name"
    - Wybieramy "-name" Aby wyczyścić bieżącą wartość jednostki.
11. Kliknij przycisk "Wynik akcje".
12. Wybierz odpowiedź "Nie wiem swoją nazwę."
13. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Moja nazwa jest Susan".
14. Wybierz pozycję "Susan" i wybierz etykietę "+ Nazwa"

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wartość wielu jednostek](./07-multi-value-entities.md)

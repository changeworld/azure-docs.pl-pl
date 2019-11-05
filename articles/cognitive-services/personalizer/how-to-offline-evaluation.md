---
title: Jak przeprowadzić ocenę w trybie offline — Personalizowanie
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak analizować pętlę szkoleniową za pomocą oceny w trybie offline
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 9d7336fb933ee5e9781d15214fa3e59446aa5b6a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490714"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analizuj pętlę szkoleniową przy użyciu oceny w trybie offline

Dowiedz się, jak przeprowadzić ocenę w trybie offline i zrozumieć wyniki.

Oceny w trybie offline umożliwiają mierzenie, jak skuteczny program Personalizuj jest porównywany z domyślnym zachowaniem aplikacji, Dowiedz się, jakie funkcje są najbardziej związane z personalizacją i automatycznie odnajduj nowe wartości uczenia maszynowego.

Przeczytaj o [ocenach w trybie offline](concepts-offline-evaluation.md) , aby dowiedzieć się więcej.


## <a name="prerequisites"></a>Wymagania wstępne

* Skonfigurowana pętla personalizacji
* Pętla personalizacji musi mieć reprezentatywną ilość danych — jako przybliżoną zalecamy co najmniej 50 000 zdarzeń w swoich dziennikach w celu uzyskania istotnych wyników oceny. Opcjonalnie możesz również wcześniej wyeksportować pliki _zasad uczenia_ , które można porównywać i testować w tej samej ocenie.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Kroki umożliwiające rozpoczęcie nowej oceny w trybie offline

1. W [Azure Portal](https://azure.microsoft.com/free/)Znajdź zasób personalizacji.
1. W Azure Portal przejdź do sekcji **oceny** i wybierz pozycję **Utwórz ocenę**.
    ![na Azure Portal, przejdź do sekcji * * oceny * * i wybierz pozycję * * Utwórz ocenę * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Skonfiguruj następujące wartości:

    * Nazwa ewaluacyjna
    * Data rozpoczęcia i zakończenia — są to daty w przeszłości, które określają zakres danych do użycia podczas obliczania. Te dane muszą być obecne w dziennikach, jak określono w wartości [przechowywania danych](how-to-settings.md) .
    * Funkcja odnajdywania optymalizacji ma ustawioną **wartość tak**

    ![Wybieranie ustawień oceny w trybie offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Rozpocznij obliczanie, wybierając **przycisk OK**. 

## <a name="results"></a>Wyniki

Obliczenia mogą zająć dużo czasu, w zależności od ilości danych do przetworzenia, liczby zasad uczenia do porównania oraz od tego, czy Optymalizacja zażądała.

Po zakończeniu możesz wybrać ocenę z listy ocen. 

Porównania zasad uczenia obejmują:

* **Zasady online**: bieżące zasady uczenia używane w programie personalizacji
* **Linia bazowa**: wartość domyślna aplikacji (zgodnie z pierwszą akcją wysłaną w wywołaniach rangi),
* **Zasady losowe**: zachowanie rangi urojonej, które zawsze zwraca losowo wybór akcji z dostarczonych.
* **Zasady niestandardowe**: dodatkowe zasady uczenia zostały przekazane podczas uruchamiania oceny.
* **Zoptymalizowane zasady**: Jeśli Ocena została rozpoczęta z opcją wykrywania zoptymalizowanych zasad, zostanie ona również porównana i będzie można ją pobrać lub wprowadzić zasady uczenia online, zastępując bieżące.

![Wykres wyników ustawień oceny w trybie offline](./media/offline-evaluation/evaluation-results.png)

Skuteczność [funkcji](concepts-features.md) dla akcji i kontekstu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, [jak działają oceny w trybie offline](concepts-offline-evaluation.md).

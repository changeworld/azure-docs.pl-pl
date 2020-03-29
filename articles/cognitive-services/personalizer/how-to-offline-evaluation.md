---
title: Jak przeprowadzić ocenę offline - Personalizer
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak używać oceny w trybie offline do pomiaru skuteczności aplikacji i analizowania pętli uczenia się.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622781"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analizowanie pętli uczenia się za pomocą oceny w trybie offline

Dowiedz się, jak ukończyć ocenę offline i zrozumieć wyniki.

Oceny w trybie offline umożliwiają mierzenie skuteczności personalizatora w porównaniu z domyślnym zachowaniem aplikacji, dowiedz się, jakie funkcje najbardziej przyczyniają się do personalizacji i automatycznie odnajdują nowe wartości uczenia maszynowego.

Przeczytaj o [ocenach offline,](concepts-offline-evaluation.md) aby dowiedzieć się więcej.

## <a name="prerequisites"></a>Wymagania wstępne

* Skonfigurowana pętla Personalizer
* Pętla Personalizer musi mieć reprezentatywną ilość danych - jako ballpark zalecamy co najmniej 50 000 zdarzeń w dziennikach, aby uzyskać znaczące wyniki oceny. Opcjonalnie można również wcześniej wyeksportować pliki _zasad uczenia się,_ które można porównać i przetestować w tej samej ocenie.

## <a name="run-an-offline-evaluation"></a>Uruchamianie oceny w trybie offline

1. W [witrynie Azure portal](https://azure.microsoft.com/free/)znajdź zasób Personalizer.
1. W witrynie Azure portal przejdź do sekcji **Oceny** i wybierz pozycję **Utwórz ocenę**.
    ![W witrynie Azure portal przejdź do sekcji **Oceny** i wybierz **Utwórz ocenę**.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Skonfiguruj następujące wartości:

    * Nazwa oceny.
    * Data rozpoczęcia i zakończenia — są to daty określające zakres danych do użycia w ocenie. Te dane muszą być obecne w dziennikach, jak określono w [wartości przechowywania danych.](how-to-settings.md)
    * Odnajdowanie optymalizacji ustawione na **tak**.

    > [!div class="mx-imgBorder"]
    > ![Wybieranie ustawień oceny w trybie offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Rozpocznij ocenę, **wybierając**ok .

## <a name="review-the-evaluation-results"></a>Przegląd wyników oceny

Oceny mogą zająć dużo czasu, w zależności od ilości danych do przetworzenia, liczby zasad uczenia się do porównania i czy zażądano optymalizacji.

Po zakończeniu można wybrać ocenę z listy ocen, a następnie wybrać **Porównaj wynik aplikacji z innymi potencjalnymi ustawieniami uczenia się.** Wybierz tę funkcję, aby zobaczyć, jak działa bieżąca zasada uczenia się w porównaniu z nowymi zasadami.

1. Przejrzyj skuteczność [zasad uczenia się](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy).

    > [!div class="mx-imgBorder"]
    > [![Przejrzyj wyniki oceny](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Wybierz **zastosuj,** aby zastosować zasady, które poprawia model najlepiej dla danych.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tym, jak działają oceny offline](concepts-offline-evaluation.md).

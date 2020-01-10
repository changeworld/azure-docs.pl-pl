---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775214"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** Wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![konfiguracja komunikacji równorzędnej — Exchange](../media/setup-exchange-conf-tab.png)

    * W obszarze **Typ komunikacji równorzędnej**wybierz pozycję *Exchange*.
    * Wybierz **jednostkę SKU** jako *bezpłatnie jako podstawową*.
    * Wybierz lokalizację **, w której** chcesz skonwertować komunikację równorzędną z zasobem platformy Azure. Jeśli masz połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **Metro** , które nie zostały przekonwertowane na zasób platformy Azure, takie połączenia będą wyświetlane w sekcji **połączenia komunikacji równorzędnej** , jak pokazano poniżej. Teraz możesz przekonwertować te połączenia komunikacji równorzędnej na zasób platformy Azure.

        > [!div class="mx-imgBorder"]
        > ![konfiguracja komunikacji równorzędnej — połączenia z starszą wersją programu Exchange](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Nie można modyfikować ustawień starszych połączeń komunikacji równorzędnej. Jeśli chcesz dodać dodatkowe połączenia komunikacji równorzędnej do firmy Microsoft w wybranej lokalizacji **Metro** , możesz to zrobić, klikając przycisk **Utwórz nowy** . Aby uzyskać więcej informacji [, zobacz Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](../howto-exchange-portal.md) .
        >

1. Kliknij pozycję **Przegląd + Utwórz**. Zwróć uwagę na to, że w portalu zostanie uruchomione podstawowe sprawdzanie poprawności wprowadzonych informacji. Ten element jest wyświetlany na Wstążce u góry, jak *trwa sprawdzanie poprawności końcowej...*

    > [!div class="mx-imgBorder"]
    > ![kartę walidacji komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po *przejściu na weryfikację*Sprawdź informacje i prześlij żądanie, klikając pozycję **Utwórz**. Jeśli musisz zmodyfikować żądanie, kliknij przycisk **Wstecz** i powtórz powyższe kroki.

    > [!div class="mx-imgBorder"]
    > ![przesyłania komunikacji równorzędnej](../media/setup-exchange-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com). Pomyślne wdrożenie zostanie wyświetlone poniżej.

    > [!div class="mx-imgBorder"]
    > ![sukcesu komunikacji równorzędnej](../media/setup-direct-success.png)

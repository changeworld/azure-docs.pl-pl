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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775214"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** w obszarze **Konfiguracja** karta Wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Konfiguracja komunikacji równorzędnej — wymiana](../media/setup-exchange-conf-tab.png)

    * W przypadku **typu komunikacji równorzędnej**wybierz pozycję *Exchange*.
    * Wybierz **jednostkę SKU** jako *bezpłatną opcję Basic*.
    * Wybierz lokalizację **metro,** w której chcesz przekonwertować komunikację równorzędnej na zasób platformy Azure. Jeśli masz połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **metro,** które nie są konwertowane do zasobu platformy Azure, takie połączenia zostaną wymienione w sekcji **Połączenia równorzędne,** jak pokazano poniżej. Teraz można przekonwertować te połączenia komunikacji równorzędnej na zasób platformy Azure.

        > [!div class="mx-imgBorder"]
        > ![Konfiguracja komunikacji równorzędnej — Exchange — starsze połączenia](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Nie można modyfikować ustawień starszych połączeń komunikacji równorzędnej. Jeśli chcesz dodać dodatkowe połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **Metro,** możesz to zrobić, klikając przycisk **Utwórz nowe.** Aby uzyskać więcej informacji, zobacz [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange za pomocą portalu.](../howto-exchange-portal.md)
        >

1. Kliknij **przejrzyj + utwórz**. Należy zauważyć, że portal uruchamia podstawowe sprawdzanie poprawności wprowadzonych informacji. Jest to wyświetlane na wstążce u góry, jako *Bieżące sprawdzanie poprawności końcowej...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Sprawdzanie poprawności komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po przełączeniu do *weryfikacji przekazanych,* sprawdź informacje i prześlij żądanie, klikając przycisk **Utwórz**. Jeśli chcesz zmodyfikować prośbę, kliknij **przycisk Poprzedni** i powtórz powyższe czynności.

    > [!div class="mx-imgBorder"]
    > ![Przekazywanie komunikacji równorzędnej](../media/setup-exchange-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na jego ukończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peering@microsoft.com). Pomyślne wdrożenie pojawi się poniżej.

    > [!div class="mx-imgBorder"]
    > ![Sukces komunikacji równorzędnej](../media/setup-direct-success.png)

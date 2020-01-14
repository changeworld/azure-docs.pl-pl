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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774499"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** Wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![konfiguracja komunikacji równorzędnej — Exchange](../media/setup-exchange-conf-tab.png)

    * W obszarze **Typ komunikacji równorzędnej**wybierz pozycję *Exchange*.
    * Wybierz **jednostkę SKU** jako *bezpłatnie jako podstawową*.
    * Wybierz lokalizację **Metro** , dla której chcesz skonfigurować komunikację równorzędną.

        > [!NOTE]
        > Jeśli masz już połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **Metro** i używasz portalu po raz pierwszy w celu skonfigurowania komunikacji równorzędnej w tej lokalizacji, istniejące połączenia komunikacji równorzędnej będą wyświetlane w sekcji **połączenia komunikacji równorzędnej** , jak pokazano poniżej. Firma Microsoft automatycznie przekonwertuje te połączenia komunikacji równorzędnej na zasoby platformy Azure, aby można było zarządzać nimi razem z nowymi połączeniami w jednym miejscu. Aby uzyskać więcej informacji [, zobacz Konwertowanie starszej komunikacji równorzędnej programu Exchange do zasobu platformy Azure przy użyciu portalu](../howto-legacy-exchange-portal.md) .
        >

1. W obszarze **połączenia komunikacji równorzędnej**kliknij przycisk **Utwórz nowy** , aby dodać wiersz dla każdego nowego połączenia, które chcesz skonfigurować.

    * Aby skonfigurować/zmodyfikować ustawienia połączenia, kliknij przycisk Edytuj dla wiersza.

        > [!div class="mx-imgBorder"]
        > ![konfiguracja komunikacji równorzędnej — Edycja w programie Exchange](../media/setup-exchange-conf-tab-edit.png)

    * Aby usunąć wiersz, kliknij przycisk **...** przycisk > **Usuń**.

        > [!div class="mx-imgBorder"]
        > ![konfiguracja komunikacji równorzędnej — Edycja w programie Exchange](../media/setup-exchange-conf-tab-delete.png)

    * Musisz podać wszystkie ustawienia dla połączenia, jak pokazano poniżej.

         > [!div class="mx-imgBorder"]
         > ![konfiguracja komunikacji równorzędnej — połączenie programu Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Wybierz funkcję **komunikacji równorzędnej** , w której ma zostać skonfigurowane połączenie.
        1. W polach **adres IPv4** i **adres IPv6**wprowadź odpowiednio adres IPv4 i IPv6, które mają być skonfigurowane w routerach firmy Microsoft przy użyciu polecenia Neighbor.
        1. Wprowadź liczbę prefiksów IPv4 i IPv6, które będą anonsowane w polach **maksymalne anonsowane adresy IPv4** i **maksymalnie anonsowane adresy IPv6** .
        1. Kliknij przycisk **OK** , aby zapisać ustawienia połączenia.

1. Powtórz poprzedni krok, aby dodać więcej połączeń w dowolnym miejscu, w którym firma Microsoft znajduje się w sieci w ramach wybranej wcześniej **linii metra** .

1. Po dodaniu wszystkich wymaganych połączeń kliknij pozycję **Recenzja + Utwórz**.

    > [!div class="mx-imgBorder"]
    > Ostatnia karta ![konfiguracja komunikacji równorzędnej](../media/setup-exchange-conf-tab-final.png)

1. Zwróć uwagę na to, że w portalu zostanie uruchomione podstawowe sprawdzanie poprawności wprowadzonych informacji. Ten element jest wyświetlany na Wstążce u góry, jak *trwa sprawdzanie poprawności końcowej...*

    > [!div class="mx-imgBorder"]
    > ![kartę walidacji komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po *przejściu na weryfikację*Sprawdź informacje i prześlij żądanie, klikając pozycję **Utwórz**. Jeśli musisz zmodyfikować żądanie, kliknij przycisk **Wstecz** i powtórz powyższe kroki.

    > [!div class="mx-imgBorder"]
    > ![przesyłania komunikacji równorzędnej](../media/setup-exchange-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com). Pomyślne wdrożenie zostanie wyświetlone poniżej.

    > [!div class="mx-imgBorder"]
    > ![sukcesu komunikacji równorzędnej](../media/setup-direct-success.png)

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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774499"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** w obszarze **Konfiguracja** karta Wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Konfiguracja komunikacji równorzędnej — wymiana](../media/setup-exchange-conf-tab.png)

    * W przypadku **typu komunikacji równorzędnej**wybierz pozycję *Exchange*.
    * Wybierz **jednostkę SKU** jako *bezpłatną opcję Basic*.
    * Wybierz lokalizację **metra,** w której chcesz skonfigurować komunikację równorzędnych.

        > [!NOTE]
        > Jeśli masz już połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **metro** i po raz pierwszy używasz portalu do konfigurowania komunikacji równorzędnej w tej lokalizacji, istniejące połączenia komunikacji równorzędnej zostaną wyświetlone w sekcji **Połączenia komunikacji równorzędnej,** jak pokazano poniżej. Firma Microsoft automatycznie przekonwertuje te połączenia komunikacji równorzędnej na zasoby platformy Azure, dzięki czemu można nimi zarządzać wraz z nowymi połączeniami w jednym miejscu. Zobacz [Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu portalu,](../howto-legacy-exchange-portal.md) aby uzyskać więcej informacji.
        >

1. W obszarze **Połączenia komunikacji równorzędnej**kliknij pozycję **Utwórz nowy,** aby dodać linię dla każdego nowego połączenia, które chcesz skonfigurować.

    * Aby skonfigurować/zmodyfikować ustawienia połączenia, kliknij przycisk edytuj wiersz.

        > [!div class="mx-imgBorder"]
        > ![Konfiguracja komunikacji równorzędnej — edycja programu Exchange](../media/setup-exchange-conf-tab-edit.png)

    * Aby usunąć wiersz, kliknij przycisk **...** > **Usuń**.

        > [!div class="mx-imgBorder"]
        > ![Konfiguracja komunikacji równorzędnej — edycja programu Exchange](../media/setup-exchange-conf-tab-delete.png)

    * Musisz podać wszystkie ustawienia połączenia, jak pokazano poniżej.

         > [!div class="mx-imgBorder"]
         > ![Konfiguracja komunikacji równorzędnej — połączenie programu Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Wybierz **obiekt komunikacji równorzędnej,** w której należy skonfigurować połączenie.
        1. W polach **Adres IPv4** i **adres IPv6**wprowadź odpowiednio adres IPv4 i IPv6, który byłby skonfigurowany w routerach firmy Microsoft przy użyciu polecenia sąsiada.
        1. Wprowadź liczbę prefiksów IPv4 i IPv6, które będą anonsowane w polach **Maksimum anonsowanych adresów IPv4** i **maksymalna anonsowane adresy IPv6** odpowiednio.
        1. Kliknij **przycisk OK,** aby zapisać ustawienia połączenia.

1. Powtórz powyższy krok, aby dodać więcej połączeń w dowolnym obiekcie, w którym firma Microsoft jest współlokowana z siecią, w ramach wcześniej wybranego **metra.**

1. Po dodaniu wszystkich wymaganych połączeń kliknij **przejrzyj + utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Karta konfiguracji komunikacji równorzędnej Wersja ostateczna](../media/setup-exchange-conf-tab-final.png)

1. Należy zauważyć, że portal uruchamia podstawowe sprawdzanie poprawności wprowadzonych informacji. Jest to wyświetlane na wstążce u góry, jako *Bieżące sprawdzanie poprawności końcowej...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Sprawdzanie poprawności komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po przełączeniu do *weryfikacji przekazanych,* sprawdź informacje i prześlij żądanie, klikając przycisk **Utwórz**. Jeśli chcesz zmodyfikować prośbę, kliknij **przycisk Poprzedni** i powtórz powyższe czynności.

    > [!div class="mx-imgBorder"]
    > ![Przekazywanie komunikacji równorzędnej](../media/setup-exchange-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na jego ukończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peering@microsoft.com). Pomyślne wdrożenie pojawi się poniżej.

    > [!div class="mx-imgBorder"]
    > ![Sukces komunikacji równorzędnej](../media/setup-direct-success.png)

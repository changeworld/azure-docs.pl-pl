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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775279"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** w obszarze **Konfiguracja** karta Wypełnij pola, jak pokazano poniżej.

    * W przypadku **typu komunikacji równorzędnej**wybierz opcję *Bezpośrednie*.
    * W przypadku **sieci Microsoft**wybierz *AS8075*. Nie należy tworzyć komunikacji równorzędnej z ASN 8069. Jest on zarezerwowany dla specjalnych aplikacji i używany tylko przez [microsoft peering](mailto:peering@microsoft.com).
    * Wybierz **jednostkę SKU** jako *bezpłatną opcję Basic*. Nie wybieraj *Premium Free,* ponieważ jest ona zarezerwowana dla specjalnych zastosowań.
    * Wybierz lokalizację **metra,** w której chcesz skonfigurować komunikację równorzędnych.

        > [!NOTE]
        > Jeśli masz już połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **metro** i po raz pierwszy używasz portalu do konfigurowania komunikacji równorzędnej w tej lokalizacji, istniejące połączenia komunikacji równorzędnej zostaną wyświetlone w sekcji **Połączenia komunikacji równorzędnej,** jak pokazano poniżej. Firma Microsoft automatycznie przekonwertuje te połączenia komunikacji równorzędnej na zasoby platformy Azure, dzięki czemu można nimi zarządzać wraz z nowymi połączeniami w jednym miejscu. Aby uzyskać więcej [informacji, zobacz Konwertowanie starszego bezpośredniego równorzędowania na zasób platformy Azure przy użyciu portalu.](../howto-legacy-direct-portal.md)
        >

1. W obszarze **Połączenia komunikacji równorzędnej**kliknij pozycję **Utwórz nowy,** aby dodać linię dla każdego nowego połączenia, które chcesz skonfigurować.

    * Aby skonfigurować/zmodyfikować ustawienia połączenia, kliknij przycisk edytuj wiersz.

        > [!div class="mx-imgBorder"]
        > ![Konfiguracja komunikacji równorzędnej — edycja bezpośrednia](../media/setup-direct-conf-tab-edit.png)
    
    * Aby usunąć wiersz, kliknij przycisk **...** > **Usuń**.

        > [!div class="mx-imgBorder"]
        > ![Konfiguracja komunikacji równorzędnej — edycja bezpośrednia](../media/setup-direct-conf-tab-delete.png)

    * Musisz podać wszystkie ustawienia połączenia, jak pokazano poniżej.

         > [!div class="mx-imgBorder"]
         > ![Konfiguracja komunikacji równorzędnej — bezpośrednie połączenie](../media/setup-direct-conf-tab-connection.png)

        1. Wybierz **obiekt komunikacji równorzędnej,** w której należy skonfigurować połączenie.
        1. **Dostawca adresów sesji** służy do określenia, kto udostępnia podsieć potrzebną do skonfigurowania sesji BGP między siecią a firmą Microsoft. Jeśli możesz podać podsieć, wybierz pozycję *Peer*. W przeciwnym razie wybierz **Microsoft** i [Microsoft komunikacji równorzędnej skontaktuje](mailto:peering@microsoft.com) się z Tobą. Należy zauważyć, że wybór tak, zajęłoby więcej czasu dla firmy Microsoft do przetwarzania żądania komunikacji równorzędnej. W niektórych przypadkach firma Microsoft może nie być w stanie podać podsieci, co spowoduje odmowę żądania.
        1. Jeśli *jako*peer zostanie wybrany dostawca **adresu sesji,** wprowadź adres IPv4 i IPv6 wraz z maską prefiksu w polach **Prefiks sesji IPv4** i **Prefiks IPv6 sesji.**
        1. Wprowadź liczbę prefiksów IPv4 i IPv6, które będą anonsowane w polach **Maksimum anonsowanych adresów IPv4** i **maksymalna anonsowane adresy IPv6** odpowiednio.
        1. Dostosuj suwak **Całkowita przepustowość,** aby odzwierciedlić przepustowość połączenia.
        1. Kliknij **przycisk OK,** aby zapisać ustawienia połączenia.

1. Powtórz powyższy krok, aby dodać więcej połączeń w dowolnym obiekcie, w którym firma Microsoft jest współlokowana z siecią, w ramach wcześniej wybranego **metra.**

1. Po dodaniu wszystkich wymaganych połączeń kliknij **przejrzyj + utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Karta konfiguracji komunikacji równorzędnej Wersja ostateczna](../media/setup-direct-conf-tab-final.png)

1. Należy zauważyć, że portal uruchamia podstawowe sprawdzanie poprawności wprowadzonych informacji. Jest to wyświetlane na wstążce u góry, jako *Bieżące sprawdzanie poprawności końcowej...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Sprawdzanie poprawności komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po przełączeniu do *weryfikacji przekazanych,* sprawdź informacje i prześlij żądanie, klikając przycisk **Utwórz**. Jeśli chcesz zmodyfikować prośbę, kliknij **przycisk Poprzedni** i powtórz powyższe czynności.

    > [!div class="mx-imgBorder"]
    > ![Przekazywanie komunikacji równorzędnej](../media/setup-direct-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na jego ukończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peering@microsoft.com). Pomyślne wdrożenie pojawi się poniżej.

    > [!div class="mx-imgBorder"]
    > ![Sukces komunikacji równorzędnej](../media/setup-direct-success.png)

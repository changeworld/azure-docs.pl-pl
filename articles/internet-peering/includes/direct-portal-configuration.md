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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775279"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** Wypełnij pola, jak pokazano poniżej.

    * W obszarze **Typ komunikacji równorzędnej**wybierz pozycję *bezpośrednie*.
    * W przypadku **sieci firmy Microsoft**wybierz pozycję *AS8075*. Nie twórz komunikacji równorzędnej z numerem ASN 8069. Jest ona zarezerwowana dla specjalnych aplikacji i jest używana tylko przez [komunikację równorzędną firmy Microsoft](mailto:peering@microsoft.com).
    * Wybierz **jednostkę SKU** jako *bezpłatnie jako podstawową*. Nie wybieraj opcji *bezpłatna wersja Premium* , ponieważ jest zarezerwowana dla aplikacji specjalnych.
    * Wybierz lokalizację **Metro** , dla której chcesz skonfigurować komunikację równorzędną.

        > [!NOTE]
        > Jeśli masz już połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **Metro** i używasz portalu po raz pierwszy w celu skonfigurowania komunikacji równorzędnej w tej lokalizacji, istniejące połączenia komunikacji równorzędnej będą wyświetlane w sekcji **połączenia komunikacji równorzędnej** , jak pokazano poniżej. Firma Microsoft automatycznie przekonwertuje te połączenia komunikacji równorzędnej na zasoby platformy Azure, aby można było zarządzać nimi razem z nowymi połączeniami w jednym miejscu. Aby uzyskać więcej informacji [, zobacz Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](../howto-legacy-direct-portal.md) .
        >

1. W obszarze **połączenia komunikacji równorzędnej**kliknij przycisk **Utwórz nowy** , aby dodać wiersz dla każdego nowego połączenia, które chcesz skonfigurować.

    * Aby skonfigurować/zmodyfikować ustawienia połączenia, kliknij przycisk Edytuj dla wiersza.

        > [!div class="mx-imgBorder"]
        > ![konfiguracja komunikacji równorzędnej — bezpośrednie edytowanie](../media/setup-direct-conf-tab-edit.png)
    
    * Aby usunąć wiersz, kliknij przycisk **...** przycisk > **Usuń**.

        > [!div class="mx-imgBorder"]
        > ![konfiguracja komunikacji równorzędnej — bezpośrednie edytowanie](../media/setup-direct-conf-tab-delete.png)

    * Musisz podać wszystkie ustawienia dla połączenia, jak pokazano poniżej.

         > [!div class="mx-imgBorder"]
         > ![konfiguracja komunikacji równorzędnej — połączenie bezpośrednie](../media/setup-direct-conf-tab-connection.png)

        1. Wybierz funkcję **komunikacji równorzędnej** , w której ma zostać skonfigurowane połączenie.
        1. **Dostawca adresów sesji** służy do określania, kto zapewnia podsieć potrzebną do skonfigurowania sesji protokołu BGP między siecią i firmą Microsoft. Jeśli masz możliwość zapewnienia podsieci, wybierz pozycję *równorzędne*. W przeciwnym razie **firma Microsoft** i [Komunikacja równorzędna firmy Microsoft](mailto:peering@microsoft.com) będą kontaktować się z Tobą. Należy pamiętać, że wybranie tej opcji zajmie więcej czasu firmie Microsoft w celu przetworzenia żądania komunikacji równorzędnej. W niektórych przypadkach firma Microsoft może nie być w stanie zapewnić podsieci, co spowoduje odmowę żądania.
        1. Jeśli wybrano opcję **dostawca adresów sesji** jako *element równorzędny*, wprowadź adres IPv4 i IPv6 wraz z maską prefiksu odpowiednio w polach **prefiks IPv4 sesji** i prefiks **IPv6** sesji.
        1. Wprowadź liczbę prefiksów IPv4 i IPv6, które będą anonsowane w polach **maksymalne anonsowane adresy IPv4** i **maksymalnie anonsowane adresy IPv6** .
        1. Dostosuj suwak **łączną przepustowość** , aby odzwierciedlał przepustowość połączenia.
        1. Kliknij przycisk **OK** , aby zapisać ustawienia połączenia.

1. Powtórz poprzedni krok, aby dodać więcej połączeń w dowolnym miejscu, w którym firma Microsoft znajduje się w sieci w ramach wybranej wcześniej **linii metra** .

1. Po dodaniu wszystkich wymaganych połączeń kliknij pozycję **Recenzja + Utwórz**.

    > [!div class="mx-imgBorder"]
    > Ostatnia karta ![konfiguracja komunikacji równorzędnej](../media/setup-direct-conf-tab-final.png)

1. Zwróć uwagę na to, że w portalu zostanie uruchomione podstawowe sprawdzanie poprawności wprowadzonych informacji. Ten element jest wyświetlany na Wstążce u góry, jak *trwa sprawdzanie poprawności końcowej...*

    > [!div class="mx-imgBorder"]
    > ![kartę walidacji komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po *przejściu na weryfikację*Sprawdź informacje i prześlij żądanie, klikając pozycję **Utwórz**. Jeśli musisz zmodyfikować żądanie, kliknij przycisk **Wstecz** i powtórz powyższe kroki.

    > [!div class="mx-imgBorder"]
    > ![przesyłania komunikacji równorzędnej](../media/setup-direct-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com). Pomyślne wdrożenie zostanie wyświetlone poniżej.

    > [!div class="mx-imgBorder"]
    > ![sukcesu komunikacji równorzędnej](../media/setup-direct-success.png)

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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774642"
---
1. Kliknij pozycję **Utwórz zasób,**  > **zobaczyć wszystkie**.

    > [!div class="mx-imgBorder"]
    > ![wyszukiwanie komunikacji równorzędnej](../media/setup-seeall.png)

1. Wyszukaj *komunikację równorzędną* w polu wyszukiwania i naciśnij klawisz *Enter* na klawiaturze. Na stronie wyników kliknij pozycję zasób **komunikacji równorzędnej** .

    > [!div class="mx-imgBorder"]
    > ![uruchamiania komunikacji równorzędnej](../media/setup-launch.png)

1. Po uruchomieniu **komunikacji równorzędnej** Przejrzyj stronę, aby poznać szczegóły. Gdy wszystko będzie gotowe, kliknij przycisk **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![utworzyć komunikację równorzędną](../media/setup-create.png)

1. Na stronie **Tworzenie komunikacji równorzędnej** w obszarze **podstawowe** karty Wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![podstawy komunikacji równorzędnej](../media/setup-basics-tab.png)

    * Wybierz swoją **subskrypcję**platformy Azure.
    * W obszarze **Grupa zasobów**można wybrać istniejącą grupę zasobów z listy rozwijanej lub utworzyć nową grupę, klikając pozycję **Utwórz nową**. W tym przykładzie utworzymy nową grupę zasobów.
    * **Nazwa** odpowiada nazwie zasobu i może być dowolna.
    * **Region** jest wybierany w przypadku wybrania istniejącej grupy zasobów w powyższym kroku. Jeśli wybrano opcję utworzenia nowej grupy zasobów, należy również wybrać region platformy Azure, w którym ma się znajdować zasób. Wschodnie stany USA

        > [!NOTE]
        > Region, w którym znajduje się grupa zasobów, jest niezależny od lokalizacji, w której chcesz utworzyć komunikację równorzędną z firmą Microsoft. Najlepszym rozwiązaniem jest zorganizowanie zasobów komunikacji równorzędnej w grupach zasobów znajdujących się w najbliższych regionach świadczenia usługi Azure. Przykład: w przypadku komunikacji równorzędnej w Ashburn można utworzyć grupę zasobów w *regionie Wschodnie stany USA* lub *Wschodnie stany USA 2*

    * Wybierz swój numer ASN w polu **peer ASN** .

        > [!IMPORTANT]
        > * Przed przesłaniem żądania komunikacji równorzędnej można wybrać tylko numer ASN z ValidationState jako "zatwierdzony". Jeśli żądanie PeerAsn zostało przesłane, poczekaj przez 12 godzin, a więc w przypadku skojarzenia ASN na "zatwierdzone". Jeśli wybrany numer ASN oczekuje na weryfikację, zostanie wyświetlony komunikat o błędzie. 
        > * Jeśli nie widzisz numeru ASN, należy wybrać, a następnie sprawdzić, czy wybrano poprawną subskrypcję. Jeśli tak jest, sprawdź, czy utworzono już PeerAsn przy użyciu opcji [Skojarz równorzędną pozycję ASN z subskrypcją platformy Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![wypełnione podstawy komunikacji równorzędnej](../media/setup-direct-basics-filled-tab.png)

    * Kliknij przycisk **Dalej: > konfiguracji** , aby kontynuować.

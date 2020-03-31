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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774642"
---
1. Kliknij **pozycję Utwórz zasób** > **Zobacz wszystkie**.

    > [!div class="mx-imgBorder"]
    > ![Komunikacja równorzędna wyszukiwania](../media/setup-seeall.png)

1. Wyszukaj *peering* w polu wyszukiwania i naciśnij *klawisz Enter* na klawiaturze. Na podstawie wyników kliknij zasób **komunikacji równorzędnej.**

    > [!div class="mx-imgBorder"]
    > ![Uruchamianie komunikacji równorzędnej](../media/setup-launch.png)

1. Po uruchomieniu **komunikacji równorzędnej** przejrzyj stronę, aby zrozumieć szczegóły. Gdy będzie gotowy, kliknij przycisk **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie komunikacji równorzędnej](../media/setup-create.png)

1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Podstawy** wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Podstawy komunikacji równorzędnej](../media/setup-basics-tab.png)

    * Wybierz **subskrypcję**platformy Azure .
    * W przypadku **grupy zasobów**można wybrać istniejącą grupę zasobów z listy rozwijanej lub utworzyć nową grupę, klikając pozycję **Utwórz nowy**. W tym przykładzie utworzymy nową grupę zasobów.
    * **Nazwa** odpowiada nazwie zasobu i może być wszystkim, co wybierzesz.
    * **Region** jest wybierany automatycznie, jeśli w powyższym kroku wybrano istniejącą grupę zasobów. Jeśli zdecydujesz się utworzyć nową grupę zasobów, należy również wybrać region platformy Azure, w którym ma się rezydować zasób. Wschodnie stany USA

        > [!NOTE]
        > Region, w którym znajduje się grupa zasobów, jest niezależny od lokalizacji, w której chcesz utworzyć komunikację równorzędnej z firmą Microsoft. Najlepszym rozwiązaniem jest jednak organizowanie zasobów komunikacji równorzędnej w grupach zasobów znajdujących się w najbliższych regionach platformy Azure. Np.: dla komunikacji równorzędnej w Ashburn, można utworzyć grupę zasobów we *wschodnich stanach USA* lub *wschodnich us2*

    * Wybierz asn w polu **Peer ASN.**

        > [!IMPORTANT]
        > * Przed przesłaniem żądania komunikacji równorzędnej można wybrać asn z certyfikatem sprawdzania poprawności jako "Zatwierdzone". Jeśli właśnie przesłałeś żądanie PeerAsn, poczekaj około 12 godzin, aż skojarzenie ASN zostanie "zatwierdzone". Jeśli wybrana asn jest oczekująca na sprawdzenie, zostanie wyświetlony komunikat o błędzie. 
        > * Jeśli nie widzisz asn trzeba wybrać, a następnie sprawdź, czy wybrano właściwą subskrypcję. Jeśli tak, sprawdź, czy utworzono już peerasn przy użyciu [skojarzenia elementów równorzędnych ASN do subskrypcji platformy Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Wypełnione podstawowe informacje o komunikacji równorzędnej](../media/setup-direct-basics-filled-tab.png)

    * Kliknij **dalej: Konfiguracja >,** aby kontynuować.

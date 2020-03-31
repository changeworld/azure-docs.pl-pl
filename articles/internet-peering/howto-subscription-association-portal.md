---
title: Kojarzenie równorzędnego numeru ASN z subskrypcją platformy Azure przy użyciu portalu
titleSuffix: Azure
description: Kojarzenie równorzędnego numeru ASN z subskrypcją platformy Azure przy użyciu portalu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912133"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Kojarzenie równorzędnego numeru ASN z subskrypcją platformy Azure przy użyciu portalu

Przed przesłaniem żądania komunikacji równorzędnej należy najpierw skojarzyć asn z subskrypcją platformy Azure, wykonując poniższe kroki.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą programu [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Tworzenie peerasn do skojarzenia asn z subskrypcją platformy Azure

### <a name="sign-in-to-the-portal"></a>Zaloguj się do portalu
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Zarejestruj się dla dostawcy zasobów komunikacji równorzędnej
Zarejestruj się, aby uzyskać dostawcę zasobów komunikacji równorzędnej w ramach subskrypcji, wykonując poniższe czynności. Jeśli nie wykonasz tego, zasoby platformy Azure wymagane do skonfigurowania komunikacji równorzędnej nie są dostępne.

1. Kliknij **subskrypcje** w lewym górnym rogu portalu. Jeśli go nie widzisz, kliknij **więcej usług** i wyszukaj go.

    > [!div class="mx-imgBorder"]
    > ![Otwarte subskrypcje](./media/rp-subscriptions-open.png)

1. Kliknij subskrypcję, której chcesz użyć do komunikacji równorzędnej.

    > [!div class="mx-imgBorder"]
    > ![Uruchamianie subskrypcji](./media/rp-subscriptions-launch.png)

1. Po otwarciu subskrypcji po lewej stronie kliknij **dostawców zasobów**. Następnie w prawym okienku wyszukaj *komunikację równorzędnej* w oknie wyszukiwania lub użyj paska przewijania, aby znaleźć **program Microsoft.Peering** i przyjrzyj się **statusowi**. Jeśli stan jest ***zarejestrowany,*** pomiń poniższe kroki i przejdź do sekcji **Utwórz PeerAsn**. Jeśli stan nie jest ***zarejestrowany,*** wybierz pozycję **Microsoft.Peering** i kliknij pozycję **Zarejestruj**.

    > [!div class="mx-imgBorder"]
    > ![Rozpoczęcie rejestracji](./media/rp-register-start.png)

1. Należy zauważyć, że stan zmienia się ***na Rejestracja***.

    > [!div class="mx-imgBorder"]
    > ![Trwa rejestracja](./media/rp-register-progress.png)

1. Poczekaj na min lub tak, aby zakończyć rejestrację. Następnie kliknij **odśwież i** sprawdź, czy stan jest ***zarejestrowany***.

    > [!div class="mx-imgBorder"]
    > ![Rejestracja zakończona](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Tworzenie peerasn
Można utworzyć nowy zasób PeerAsn do kojarzenia numeru systemu autonomicznego (ASN) z subskrypcją platformy Azure. Można skojarzyć wiele sieci ASN do subskrypcji, tworząc **PeerAsn** dla każdego ASN, który należy skojarzyć.

1. Kliknij **pozycję Utwórz zasób** > **Zobacz wszystkie**.

    > [!div class="mx-imgBorder"]
    > ![Szukaj PeerAsn](./media/peerasn-seeall.png)

1. Wyszukaj *PeerAsn* w polu wyszukiwania i naciśnij *klawisz Enter* na klawiaturze. Na podstawie wyników kliknij zasób **PeerAsn.**

    > [!div class="mx-imgBorder"]
    > ![Uruchamianie programu PeerAsn](./media/peerasn-launch.png)

1. Po uruchomieniu **PeerAsn** kliknij przycisk **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie peerasn](./media/peerasn-create.png)

1. Na stronie **Kojarzenie równorzędnej sieci ASN** w obszarze **Podstawy** kartę wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Karta Podstawowe programy PeerAsn](./media/peerasn-basics-tab.png)

    * **Nazwa** odpowiada nazwie zasobu i może być wszystkim, co wybierzesz.  
    * Wybierz **subskrypcję,** z którą musisz skojarzyć ASN.
    * **Nazwa równorzędna** odpowiada nazwie firmy i musi być jak najbardziej zbliżona do profilu PeeringDB. Należy zauważyć, że wartość obsługuje tylko znaki a-z, A-Z i spacja
    * Wprowadź asn w polu **Peer ASN.**
    * Kliknij **pozycję Utwórz nowy** i wprowadź **adres e-mail** i **numer telefonu** dla sieciowego centrum operacyjnego (NOC)
1. Następnie kliknij **przejrzyj + utwórz** i obserwuj, że portal uruchamia podstawową weryfikację wprowadzonych informacji. Jest to wyświetlane na wstążce u góry, jako *Bieżące sprawdzanie poprawności końcowej...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Recenzja peerAsn](./media/peerasn-review-tab-validation.png)

1. Gdy wiadomość na wstążce zmieni się w *Pozycję Weryfikacja przekazana,* zweryfikuj informacje i prześlij żądanie, klikając przycisk **Utwórz**. Jeśli sprawdzanie poprawności nie przejdzie, kliknij **przycisk Poprzedni** i powtórz powyższe kroki, aby zmodyfikować żądanie i upewnić się, że wprowadzone wartości nie mają błędów.

    > [!div class="mx-imgBorder"]
    > ![Karta Recenzja peerAsn](./media/peerasn-review-tab.png)

1. Po przesłaniu żądania poczekaj na jego zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peering@microsoft.com). Pomyślne wdrożenie pojawi się poniżej.

    > [!div class="mx-imgBorder"]
    > ![Sukces PeerAsn](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Wyświetl stan peerAsn
Po pomyślnym wdrożeniu zasobu PeerAsn należy poczekać, aż firma Microsoft zatwierdzi żądanie skojarzenia. Zatwierdzenie może potrwać do 12 godzin. Po zatwierdzeniu otrzymasz powiadomienie na adres e-mail wpisany w powyższej sekcji.

> [!IMPORTANT]
> Poczekaj na validationstate, aby włączyć "Zatwierdzone" przed przesłaniem żądania komunikacji równorzędnej. Zatwierdzenie to może potrwać do 12 godzin.

## <a name="modify-peerasn"></a>Modyfikowanie elementu PeerAsn
Modyfikowanie PeerAsn nie jest obecnie obsługiwane. Jeśli chcesz zmodyfikować, skontaktuj się z [programem Microsoft peering](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Usuń PeerAsn
Usuwanie PeerAsn nie jest obecnie obsługiwane. Jeśli chcesz usunąć PeerAsn, skontaktuj się z [microsoft komunikacji równorzędnej](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md)
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)
* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange za pomocą portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, odwiedź [najczęściej zadawane pytania dotyczące komunikacji internet](faqs.md)
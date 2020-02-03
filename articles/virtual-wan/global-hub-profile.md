---
title: Pobierz globalne lub oparte na centrach profilów sieci VPN Azure Virtual WAN | Microsoft Docs
description: Dowiedz się więcej o wirtualnej skalowalnej gałęzi sieci WAN, dostępnych regionach i partnerach.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965231"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Pobieranie profilu globalnego lub opartego na centrum dla klientów sieci VPN użytkowników

Wirtualna sieć WAN platformy Azure oferuje dwa typy łączności dla użytkowników zdalnych: globalna i oparta na centrum. Skorzystaj z poniższych sekcji, aby dowiedzieć się więcej o tym, jak pobrać profil.

## <a name="global-profile"></a>Profil globalny

Profil wskazuje moduł równoważenia obciążenia obejmujący wszystkie aktywne centra sieci VPN użytkowników. Użytkownik jest kierowany do centrum znajdującego się najbliżej lokalizacji geograficznej użytkownika. Ten typ łączności jest przydatny, gdy użytkownicy często podróżują z różnymi lokalizacjami. Aby pobrać profil **globalny** :

1. Przejdź do wirtualnej sieci WAN.
2. Kliknij pozycję **Konfiguracja sieci VPN użytkownika**.
3. Zaznacz konfigurację, dla której chcesz pobrać profil.
4. Kliknij pozycję **Pobierz profil sieci VPN wirtualnego użytkownika sieci WAN**.

   ![Profil globalny](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Profil oparty na centrum

Profil wskazuje na jedno centrum. Użytkownik może łączyć się tylko z konkretnym centrum przy użyciu tego profilu. Aby pobrać profil **oparty na centrum** :

1. Przejdź do wirtualnej sieci WAN.
2. Kliknij pozycję **centrum** na stronie Przegląd.

    ![Profil centrum 1](./media/global-hub-profile/hub1.png)
3. Kliknij pozycję **Sieć VPN użytkownika (wskaż pozycję lokacja)** .
4. Kliknij pozycję **Pobierz profil sieci VPN użytkownika wirtualnego centrum**.

   ![Profil centrum 2](./media/global-hub-profile/hub2.png)
5. Sprawdź **EAPTLS**.
6. Kliknij pozycję **Generuj i Pobierz profil**.

   ![Profil centrum 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).

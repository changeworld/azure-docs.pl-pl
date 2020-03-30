---
title: Pobieranie globalnych lub centralnych profili sieci VPN platformy Azure Virtual WAN | Dokumenty firmy Microsoft
description: Dowiedz się więcej o wirtualnej sieci WAN zautomatyzowanej skalowalnej łączności między gałęziami, dostępnych regionach i partnerach.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965231"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Pobieranie globalnego lub opartego na centrum profilu dla klientów sieci VPN użytkowników

Wirtualna sieć WAN platformy Azure oferuje dwa typy łączności dla użytkowników zdalnych: globalne i oparte na centrum. Skorzystaj z poniższych sekcji, aby dowiedzieć się więcej o profilu i pobrać go.

## <a name="global-profile"></a>Profil globalny

Profil wskazuje moduł równoważenia obciążenia, który zawiera wszystkie aktywne centra sieci VPN użytkowników. Użytkownik jest kierowany do centrum, który znajduje się najbliżej lokalizacji geograficznej użytkownika. Ten typ łączności jest przydatny, gdy użytkownicy często podróżują do różnych lokalizacji. Aby pobrać profil **globalny:**

1. Przejdź do wirtualnej sieci WAN.
2. Kliknij **pozycję Konfiguracja sieci VPN użytkownika**.
3. Wyróżnij konfigurację, dla której chcesz pobrać profil.
4. Kliknij **pozycję Pobierz wirtualny profil sieci VPN użytkownika sieci WAN**.

   ![Profil globalny](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Profil oparty na koncentratorze

Profil wskazuje na pojedynczy koncentrator. Użytkownik może łączyć się tylko z określonym koncentratorem przy użyciu tego profilu. Aby pobrać profil oparty na **koncentratorze:**

1. Przejdź do wirtualnej sieci WAN.
2. Kliknij **pozycję Centrum** na stronie Przegląd.

    ![Profil piasty 1](./media/global-hub-profile/hub1.png)
3. Kliknij **pozycję User VPN (Point to site)**.
4. Kliknij **pozycję Pobierz profil sieci VPN użytkownika wirtualnego centrum**.

   ![Profil piasty 2](./media/global-hub-profile/hub2.png)
5. Sprawdź, **czy w pliku EAPTLS**jest
6. Kliknij **pozycję Generuj i pobierz profil**.

   ![Profil piasty 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).

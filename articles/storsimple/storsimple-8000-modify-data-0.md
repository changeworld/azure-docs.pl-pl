---
title: Modyfikowanie danych 0 ustawienia na urządzeniu z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować interfejs sieciowy 0 danych na urządzeniu StorSimple za pomocą programu Windows PowerShell dla urządzenia StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 90df43e22f17fd32fe642514df098b72700e77af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927586"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Zmodyfikuj ustawienia interfejsu sieciowego 0 danych na urządzeniu z serii StorSimple 8000

## <a name="overview"></a>Omówienie

Microsoft Azure StorSimple urządzenie ma sześć interfejsów sieciowych z dane 0 do dane 5. DANE 0 interfejsu zawsze jest skonfigurowana za pośrednictwem interfejsu programu Windows PowerShell lub konsoli szeregowej i jest automatycznie włączoną obsługę chmury. Należy pamiętać, że nie można skonfigurować interfejs sieciowy 0 danych za pośrednictwem portalu Azure.

DANE 0 interfejs został skonfigurowany za pomocą Kreatora instalacji podczas początkowej wdrażania urządzenia StorSimple. Gdy urządzenie jest w trybie operacyjne, konieczne może być ponownie skonfigurować dane 0 ustawienia. Ten samouczek zawiera dwie metody, aby zmodyfikować dane 0 sieci ustawienia, zarówno za pomocą programu Windows PowerShell dla StorSimple.

Po przeczytaniu tego samouczka, będą mieć możliwość:

* Modyfikowanie danych 0 ustawienie za pomocą Kreatora konfiguracji sieci
* Modyfikowanie ustawień sieciowych 0 danych za pośrednictwem `Set-HcsNetInterface` polecenia cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modyfikowanie ustawień sieciowych 0 danych za pomocą Kreatora konfiguracji
Można ponownie skonfigurować ustawienia sieciowe 0 danych, łącząc się interfejsu programu Windows PowerShell urządzenia StorSimple i uruchomić sesję kreatora instalacji. Wykonaj poniższe kroki, aby zmodyfikować dane 0 ustawienia:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Aby zmodyfikować ustawienia sieciowe 0 danych za pomocą Kreatora konfiguracji
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło jest `Password1`.
2. W wierszu polecenia wpisz:
   
    `Invoke-HcsSetupWizard`
3. Kreator instalacji jest wyświetlany w celu skonfigurowania dane 0 interfejsu urządzenia. Podaj wartości nowy adres IP, bramę i maski podsieci.

> [!NOTE]
> Kontrolery stałe adresy IP należy tak skonfigurować za pomocą **ustawień sieciowych** bloku urządzenia StorSimple w portalu Azure. Aby uzyskać więcej informacji, przejdź do [zmodyfikować interfejsów sieciowych](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modyfikowanie ustawień sieciowych 0 danych za pomocą polecenia cmdlet Set-HcsNetInterface
Innym sposobem ponownie skonfigurować dane 0 interfejs sieciowy jest za pośrednictwem `Set-HcsNetInterface` polecenia cmdlet. Polecenia cmdlet jest uruchamiane z interfejsu programu Windows PowerShell urządzenia StorSimple. Korzystając z tej procedury, kontroler, stałe adresy IP można również skonfigurować w tym miejscu. Wykonaj poniższe kroki, aby zmodyfikować dane 0 ustawienia: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Aby zmodyfikować ustawienia sieciowe 0 danych za pomocą polecenia cmdlet Set-HcsNetInterface
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Po wyświetleniu monitu podaj hasło administratora urządzenia. Domyślne hasło jest `Password1`.
2. W wierszu polecenia wpisz:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    W nawiasach wpisz następujące wartości dla danych 0:
   
   * Adres IPv4
   * Brama IPv4
   * Maska podsieci IPv4
   * Stały adres IPv4 dla kontrolera 0
   * Stały adres IPv4 dla kontrolera 1
     
     Aby uzyskać więcej informacji dotyczących użycia tego polecenia cmdlet, przejdź do [programu Windows PowerShell dla StorSimple dokumentacji poleceń cmdlet](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Następne kroki
* Aby skonfigurować interfejsy sieciowe inne niż dane 0, można użyć [skonfigurować ustawienia sieciowe w portalu Azure](storsimple-8000-modify-device-config.md). 
* Jeśli wystąpią problemy podczas konfigurowania interfejsów sieciowych, zapoznaj się [Rozwiązywanie problemów dotyczących wdrożenia](storsimple-troubleshoot-deployment.md).


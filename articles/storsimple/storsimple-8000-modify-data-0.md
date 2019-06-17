---
title: Modyfikowanie danych 0 ustawień urządzenia StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ponownie skonfigurować interfejs sieciowy DATA 0 na urządzeniu StorSimple przy użyciu programu Windows PowerShell dla usługi StorSimple.
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
ms.openlocfilehash: 3cf136c5ddec8f4998d15c597914e1f806453945
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631587"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Modyfikowanie ustawień DATA 0 sieci interfejsu na urządzeniu StorSimple 8000 series

## <a name="overview"></a>Omówienie

Urządzenie StorSimple systemu Azure firmy Microsoft ma sześć interfejsów sieciowych, dane 0 do dane 5. DATA 0 jest zawsze konfigurowane za pośrednictwem interfejsu programu Windows PowerShell lub konsoli szeregowej interfejsu i jest automatycznie włączoną obsługę chmury. Należy pamiętać, że nie można skonfigurować interfejs sieciowy DATA 0 w witrynie Azure portal.

DATA 0 interfejs został skonfigurowany za pomocą Kreatora instalacji podczas początkowego wdrożenia urządzenia StorSimple. Gdy urządzenie jest w trybie operacyjnej, konieczne może być ponownie skonfigurować interfejs DATA 0 ustawienia. Ten samouczek zawiera dwie metody, aby zmodyfikować interfejs DATA 0 sieci ustawienia, zarówno za pomocą programu Windows PowerShell dla usługi StorSimple.

Po przeczytaniu tego samouczka, będzie mieć możliwość:

* Modyfikowanie danych 0 ustawienie za pomocą Kreatora konfiguracji sieci
* Modyfikowanie ustawień sieci 0 danych za pośrednictwem `Set-HcsNetInterface` polecenia cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modyfikowanie ustawień sieci 0 danych za pomocą Kreatora instalacji
Można ponownie skonfigurować ustawienia sieciowe 0 danych przez połączenie interfejsu programu Windows PowerShell urządzenia StorSimple i uruchamianie sesji kreatora instalacji. Wykonaj poniższe kroki, aby zmodyfikować interfejs DATA 0 ustawienia:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Aby zmodyfikować ustawienia sieciowe 0 danych za pomocą Kreatora instalacji
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło jest `Password1`.
2. W wierszu polecenia wpisz polecenie:
   
    `Invoke-HcsSetupWizard`
3. Zostanie uruchomiony Kreator instalacji pojawi się w celu skonfigurowania DATA 0 interfejsu urządzenia. Podaj nowe wartości dla adresu IP, bramy i masek podsieci.

> [!NOTE]
> Kontrolery stałych adresów IP będzie wymagać ponownego za pośrednictwem **ustawienia sieciowe** bloku urządzenia StorSimple w witrynie Azure portal. Aby uzyskać więcej informacji, przejdź do [zmodyfikować interfejsów sieciowych](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modyfikowanie ustawień sieci 0 danych za pomocą polecenia cmdlet Set-HcsNetInterface
Alternatywny sposób, aby ponownie skonfigurować interfejs DATA 0 interfejs sieciowy jest za pośrednictwem `Set-HcsNetInterface` polecenia cmdlet. Polecenie cmdlet jest wykonywane z interfejsu programu Windows PowerShell urządzenia StorSimple. Korzystając z tej procedury, stałe adresy IP kontrolera można również skonfigurować w tym miejscu. Wykonaj poniższe kroki, aby zmodyfikować dane 0 ustawienia: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Aby zmodyfikować ustawienia sieciowe 0 danych za pośrednictwem polecenia cmdlet Set-HcsNetInterface
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Po wyświetleniu monitu podaj hasło administratora urządzenia. Domyślne hasło jest `Password1`.
2. W wierszu polecenia wpisz polecenie:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    W nawiasach wpisz następujące wartości dla interfejsu dane 0:
   
   * Adres IPv4
   * IPv4 gateway
   * IPv4 subnet mask
   * Stały adres IPv4 dla kontrolera 0
   * Stały adres IPv4 dla kontrolera 1
     
     Aby uzyskać więcej informacji dotyczących używania tego polecenia cmdlet, przejdź do [programu Windows PowerShell dla StorSimple Dokumentacja poleceń cmdlet](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować interfejsy sieciowe inne niż interfejs DATA 0, można użyć [skonfigurować ustawienia sieciowe w witrynie Azure portal](storsimple-8000-modify-device-config.md). 
* Jeśli napotkasz jakiekolwiek problemy podczas konfigurowania interfejsów sieciowych, zapoznaj się [Rozwiązywanie problemów z wdrożeniem](storsimple-troubleshoot-deployment.md).


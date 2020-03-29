---
title: Modyfikowanie ustawień DATA 0 na urządzeniu storsimple serii 8000 | Dokumenty firmy Microsoft
description: Dowiedz się, jak ponownie skonfigurować interfejs sieciowy DATA 0 na urządzeniu StorSimple za pomocą programu Windows PowerShell for StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631587"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Modyfikowanie ustawień interfejsu sieciowego DATA 0 na urządzeniu z serii StorSimple 8000

## <a name="overview"></a>Omówienie

Urządzenie Microsoft Azure StorSimple ma sześć interfejsów sieciowych, od DANYCH od DANYCH od 0 do danych 5. Interfejs DATA 0 jest zawsze konfigurowany za pośrednictwem interfejsu programu Windows PowerShell lub konsoli szeregowej i jest automatycznie włączony w chmurze. Należy zauważyć, że nie można skonfigurować interfejsu sieciowego DATA 0 za pośrednictwem witryny Azure portal.

Interfejs DATA 0 jest najpierw konfigurowany za pomocą kreatora instalacji podczas początkowego wdrażania urządzenia StorSimple. Gdy urządzenie jest w trybie operacyjnym, może być konieczne ponowne skonfigurowanie ustawień DANYCH 0. Ten samouczek zawiera dwie metody modyfikowania ustawień sieciowych danych 0, zarówno za pośrednictwem programu Windows PowerShell dla StorSimple.

Po przeczytaniu tego samouczka, będzie można:

* Modyfikowanie ustawienia sieci DATA 0 za pomocą kreatora instalacji
* Modyfikowanie ustawień sieciowych `Set-HcsNetInterface` DATA 0 za pomocą polecenia cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modyfikowanie ustawień sieciowych DATA 0 za pomocą Kreatora konfiguracji
Ustawienia sieciowe DATA 0 można ponownie skonfigurować, łącząc się z interfejsem programu Windows PowerShell urządzenia StorSimple i uruchamiając sesję kreatora konfiguracji. Wykonaj następujące czynności, aby zmodyfikować ustawienia DANYCH 0:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Aby zmodyfikować ustawienia sieciowe DATA 0 za pomocą Kreatora instalacji
1. W menu konsoli szeregowej wybierz opcję 1, **zaloguj się z pełnym dostępem**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślnym hasłem jest `Password1`.
2. W wierszu polecenia wpisz polecenie:
   
    `Invoke-HcsSetupWizard`
3. Kreator konfiguracji wydaje się pomagać w konfigurowaniu interfejsu DATA 0 urządzenia. Podaj nowe wartości dla adresu IP, bramy i maski sieciowej.

> [!NOTE]
> Stałe kontrolery ip kontrolerów będą musiały zostać ponownie skonfigurowane za pomocą bloku **ustawienia sieci** urządzenia StorSimple w witrynie Azure portal. Aby uzyskać więcej informacji, przejdź do [tematu Modyfikowanie interfejsów sieciowych](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modyfikowanie ustawień sieciowych DATA 0 za pomocą polecenia cmdlet Set-HcsNetInterface
Alternatywnym sposobem ponownego skonfigurowania interfejsu sieciowego DATA 0 `Set-HcsNetInterface` jest użycie polecenia cmdlet. Polecenie cmdlet jest wykonywane z interfejsu programu Windows PowerShell urządzenia StorSimple. Korzystając z tej procedury, kontroler stałych ip można również skonfigurować tutaj. Wykonaj następujące kroki, aby zmodyfikować ustawienia DATA 0: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Aby zmodyfikować ustawienia sieciowe DATA 0 za pomocą polecenia cmdlet Set-HcsNetInterface
1. W menu konsoli szeregowej wybierz opcję 1, **zaloguj się z pełnym dostępem**. Po wyświetleniu monitu podaj hasło administratora urządzenia. Domyślnym hasłem jest `Password1`.
2. W wierszu polecenia wpisz polecenie:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    W nawiasach kątowych wpisz następujące wartości dla DANYCH 0:
   
   * Adres IPv4
   * Brama IPv4
   * Maska podsieci IPv4
   * Stały adres IPv4 dla kontrolera 0
   * Stały adres IPv4 dla kontrolera 1
     
     Aby uzyskać więcej informacji na temat używania tego polecenia cmdlet, przejdź do [programu Windows PowerShell dla storsimple odwołanie do polecenia cmdlet](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Następne kroki
* Aby skonfigurować interfejsy sieciowe inne niż DANE 0, można użyć [funkcji Konfiguruj ustawienia sieciowe w witrynie Azure portal](storsimple-8000-modify-device-config.md). 
* Jeśli podczas konfigurowania interfejsów sieciowych występują jakiekolwiek problemy, zapoznaj się [z polem rozwiązywania problemów z wdrażaniem](storsimple-troubleshoot-deployment.md).


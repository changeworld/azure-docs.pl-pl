---
title: Aktualizowanie urządzenia StorSimple | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak korzystać z funkcji aktualizacji StorSimple, aby zainstalować regularne i konserwacji aktualizacje trybu i poprawki.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: twooley
ms.openlocfilehash: c9451afaefdd220b5f87d4650c7844f06926b03a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933432"
---
# <a name="update-your-storsimple-8000-series-device"></a>Aktualizowanie urządzenia StorSimple serii 8000
> [!NOTE]
> Klasyczny portal dla urządzeń StorSimple jest przestarzały. Menedżerowie urządzeń StorSimple dokonają automatycznego przeniesienia do nowej witryny Azure Portal zgodnie z ustalonym harmonogramem wycofywania przestarzałych produktów. Powiadomienie o przeniesieniu otrzymasz pocztą e-mail i za pośrednictwem portalu. Ten dokument zostanie wkrótce usunięty. W razie jakichkolwiek pytań dotyczących przeniesienia, zobacz [FAQ: Move to Azure portal (Często zadawane pytania — przeniesienie do witryny Azure Portal)](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Omówienie
Funkcje aktualizacji StorSimple umożliwiają łatwe aktualizowanie urządzenia StorSimple. W zależności od typu aktualizacji można zastosować aktualizacje do urządzenia za pośrednictwem klasycznego portalu platformy Azure lub za pośrednictwem interfejsu programu Windows PowerShell. W tym samouczku opisano typy aktualizacji i sposób instalowania każdego z nich.

Można zastosować dwa typy aktualizacji urządzeń: 

* Regularne (lub normalne) aktualizacje
* Aktualizacje trybu konserwacji

Regularne aktualizacje można instalować za pośrednictwem klasycznego portalu azure lub programu Windows PowerShell; jednak należy użyć programu Windows PowerShell, aby zainstalować aktualizacje trybu konserwacji. 

Każdy typ aktualizacji jest opisany oddzielnie, poniżej.

### <a name="regular-updates"></a>Regularne aktualizacje
Regularne aktualizacje są aktualizacje bez zakłóceń, które mogą być instalowane, gdy urządzenie jest w trybie normalnym. Te aktualizacje są stosowane za pośrednictwem witryny microsoft update do każdego kontrolera urządzenia. 

> [!IMPORTANT]
> Podczas procesu aktualizacji może wystąpić przetwórnie awaryjne kontrolera. Nie wpłynie to jednak na dostępność lub działanie systemu.
> 
> 

* Aby uzyskać szczegółowe informacje na temat instalowania regularnych aktualizacji za pośrednictwem klasycznego portalu platformy Azure, zobacz [Instalowanie regularnych aktualizacji za pośrednictwem klasycznego portalu platformy Azure.](#install-regular-updates-via-the-azure-classic-portal)
* Można również zainstalować regularne aktualizacje za pośrednictwem programu Windows PowerShell dla StorSimple. Aby uzyskać szczegółowe informacje, zobacz [Instalowanie regularnych aktualizacji za pośrednictwem programu Windows PowerShell for StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Aktualizacje trybu konserwacji
Aktualizacje trybu konserwacji są przełomowe aktualizacje, takie jak uaktualnienia oprogramowania układowego dysku. Te aktualizacje wymagają, aby urządzenie było przesuń w tryb konserwacji. Aby uzyskać szczegółowe informacje, zobacz [Krok 2: Wejdź w tryb konserwacji](#step2). Nie można użyć klasycznego portalu platformy Azure do zainstalowania aktualizacji trybu konserwacji. Zamiast tego należy użyć programu Windows PowerShell dla StorSimple. 

Aby uzyskać szczegółowe informacje na temat instalowania aktualizacji trybu konserwacji, zobacz [Instalowanie aktualizacji trybu konserwacji za pośrednictwem programu Windows PowerShell for StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Aktualizacje trybu konserwacji muszą być stosowane oddzielnie do każdego kontrolera. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalowanie regularnych aktualizacji za pośrednictwem klasycznego portalu platformy Azure
Za pomocą klasycznego portalu platformy Azure można zastosować aktualizacje do urządzenia StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalowanie regularnych aktualizacji za pośrednictwem programu Windows PowerShell for StorSimple
Alternatywnie można użyć programu Windows PowerShell for StorSimple do stosowania regularnych aktualizacji (tryb normalny).

> [!IMPORTANT]
> Mimo że można instalować regularne aktualizacje przy użyciu programu Windows PowerShell for StorSimple, zdecydowanie zaleca się instalowanie regularnych aktualizacji za pośrednictwem klasycznego portalu platformy Azure. Począwszy od aktualizacji 1, przed zainstalowaniem aktualizacji z portalu zostaną przeprowadzone wstępne kontrole. Te kontrole wstępne wywłaszczą błędy i zapewnią płynniejsze działanie. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalowanie aktualizacji trybu konserwacji za pośrednictwem programu Windows PowerShell dla StorSimple
Program Windows PowerShell for StorSimple służy do stosowania aktualizacji trybu konserwacji na urządzeniu StorSimple. Wszystkie żądania we/wy są wstrzymane w tym trybie. Usługi, takie jak nieulotna pamięć dostępu losowego (NVRAM) lub usługa klastrowania, również są zatrzymywane. Oba kontrolery są ponownie uruchamiane po wejściu lub wyjściu z tego trybu. Po wyjściu z tego trybu wszystkie usługi zostaną wznowione i powinny być w dobrej kondycji. (Może to potrwać kilka minut).)

Jeśli musisz zastosować aktualizacje trybu konserwacji, otrzymasz alert za pośrednictwem klasycznego portalu platformy Azure, że masz aktualizacje, które muszą być zainstalowane. Ten alert będzie zawierać instrukcje dotyczące instalowania aktualizacji za pomocą programu Windows PowerShell for StorSimple. Po zaktualizowaniu urządzenia użyj tej samej procedury, aby zmienić urządzenie na tryb normalny. Aby uzyskać instrukcje krok po kroku, zobacz [Krok 4: Wyjście z trybu konserwacji](#step4).

> [!IMPORTANT]
> * Przed wejściem w tryb konserwacji sprawdź, czy oba kontrolery urządzeń są w dobrej kondycji, sprawdzając **stan sprzętu** na stronie **Konserwacja** w klasycznym portalu platformy Azure. Jeśli kontroler nie jest w dobrej kondycji, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać następujące kroki. Aby uzyskać więcej informacji, przejdź do pomocy technicznej firmy Microsoft. 
> * Gdy jesteś w trybie konserwacji, należy zastosować aktualizację najpierw na jednym kontrolerze, a następnie na drugim kontrolerze.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Krok 1: Połącz się z konsolą szeregową<a name="step1">
Najpierw należy użyć aplikacji, takiej jak PuTTY, aby uzyskać dostęp do konsoli szeregowej. W poniższej procedurze wyjaśniono, jak używać putty do łączenia się z konsolą szeregową.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Krok 2: Wejdź w tryb konserwacji<a name="step2">
Po nawiązaniu połączenia z konsolą określ, czy są aktualizacje do zainstalowania, i wprowadź tryb konserwacji, aby je zainstalować.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Krok 3: Zainstaluj aktualizacje<a name="step3">
Następnie zainstaluj aktualizacje.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Krok 4: Tryb konserwacji wyjścia<a name="step4">
Na koniec wyjdź z trybu konserwacji.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalowanie poprawek za pośrednictwem programu Windows PowerShell dla StorSimple
W przeciwieństwie do aktualizacji dla usługi Microsoft Azure StorSimple poprawki są instalowane z folderu udostępnionego. Podobnie jak w przypadku aktualizacji, istnieją dwa typy poprawek: 

* Regularne poprawki 
* Poprawki trybu konserwacji  

Poniższe procedury wyjaśniają, jak używać programu Windows PowerShell for StorSimple do instalowania poprawek trybu regularnego i konserwacyjnego.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Co się stanie z aktualizacjami w przypadku przywrócenia ustawień fabrycznych urządzenia?
Jeśli urządzenie zostanie zresetowane do ustawień fabrycznych, wszystkie aktualizacje zostaną utracone. Po zarejestrowaniu i skonfigurowaniu urządzenia resetowania fabrycznego należy ręcznie zainstalować aktualizacje za pośrednictwem klasycznego portalu azure i/lub programu Windows PowerShell for StorSimple. Aby uzyskać więcej informacji na temat resetowania ustawień fabrycznych, zobacz [Resetowanie ustawień domyślnych urządzenia do ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą programu Windows PowerShell for StorSimple](storsimple-windows-powershell-administration.md).
* Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą usługi StorSimple Manager.](storsimple-manager-service-administration.md)


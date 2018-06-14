---
title: Aktualizowanie urządzenia StorSimple | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak korzystać z funkcji aktualizacji StorSimple do zainstalowania zwykłych oraz obsługi trybu aktualizacje i poprawki.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: v-sharos
ms.openlocfilehash: 412978d2c343394f295e336690ec72153dda4b79
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2018
ms.locfileid: "28103612"
---
# <a name="update-your-storsimple-8000-series-device"></a>Zaktualizuj urządzenia z serii StorSimple 8000
> [!NOTE]
> Klasyczny portal dla urządzeń StorSimple jest przestarzały. Menedżerowie urządzeń StorSimple dokonają automatycznego przeniesienia do nowej witryny Azure Portal zgodnie z ustalonym harmonogramem wycofywania przestarzałych produktów. Powiadomienie o przeniesieniu otrzymasz pocztą e-mail i za pośrednictwem portalu. Ten dokument zostanie wkrótce usunięty. W razie jakichkolwiek pytań dotyczących przeniesienia, zobacz [FAQ: Move to Azure portal (Często zadawane pytania — przeniesienie do witryny Azure Portal)](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Przegląd
Funkcje aktualizacji StorSimple umożliwiają łatwe aktualizowanie urządzenia StorSimple. W zależności od typu aktualizacji należy zastosować aktualizacje na urządzeniu za pośrednictwem klasycznego portalu Azure lub za pośrednictwem interfejsu programu Windows PowerShell. W tym samouczku opisano typy aktualizacji i sposobu instalacji każdego z nich.

Można zastosować dwa typy aktualizacji urządzenia: 

* Regularne (lub tryb normalny) aktualizacji
* Aktualizacje trybu konserwacji

Możesz zainstalować regularne aktualizacje za pośrednictwem klasycznego portalu Azure lub programu Windows PowerShell; jednak należy użyć środowiska Windows PowerShell do instalowania aktualizacji trybu konserwacji. 

Każdy typ aktualizacji jest opisane oddzielnie, poniżej.

### <a name="regular-updates"></a>Regularne aktualizacje
Regularne aktualizacje są Brak aktualizacji, które mogą być instalowane, gdy urządzenie jest w trybie normalnym. Te aktualizacje są stosowane za pośrednictwem witryny Microsoft Update w każdym kontrolerze urządzenia. 

> [!IMPORTANT]
> Tryb failover kontroler może wystąpić podczas procesu aktualizacji. Jednak nie wpłynie to dostępność systemu lub operacji.
> 
> 

* Aby uzyskać więcej informacji na temat instalowania regularne aktualizacje za pośrednictwem klasycznego portalu Azure, zobacz [zainstalować regularne aktualizacje za pośrednictwem klasycznego portalu Azure](#install-regular-updates-via-the-azure-classic-portal).
* Można także zainstalować regularne aktualizacje za pomocą środowiska Windows PowerShell dla urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [zainstalować regularne aktualizacje za pomocą środowiska Windows PowerShell dla StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Aktualizacje trybu konserwacji
Aktualizacje trybu konserwacji są destrukcyjne aktualizacje, takie jak aktualizacje oprogramowania dysku. Te aktualizacje urządzenie musi znajdować się w trybie konserwacji. Aby uzyskać więcej informacji, zobacz [krok 2: tryb konserwacji wprowadź](#step2). Klasyczny portal Azure nie można używać do instalowania aktualizacji tryb konserwacji. Zamiast tego należy użyć programu Windows PowerShell dla urządzenia StorSimple. 

Aby uzyskać więcej informacji na temat sposobu instalowania aktualizacji tryb konserwacji, zobacz [zainstalować obsługi trybu aktualizacje za pomocą programu Windows PowerShell dla StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Aktualizacje trybu konserwacji musi zostać zastosowana osobno na każdym kontrolerze. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Zainstaluj regularne aktualizacje za pośrednictwem klasycznego portalu Azure
Klasyczny portal Azure służy do stosowania aktualizacji na urządzeniu StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Zainstaluj regularne aktualizacje za pomocą środowiska Windows PowerShell dla urządzenia StorSimple
Alternatywnie można użyć programu Windows PowerShell dla StorSimple do stosowania aktualizacji regularne (tryb normalny).

> [!IMPORTANT]
> Mimo że można zainstalować regularne aktualizacje za pomocą środowiska Windows PowerShell dla urządzenia StorSimple, zdecydowanie zaleca się zainstalowanie regularne aktualizacje za pośrednictwem klasycznego portalu Azure. Począwszy od aktualizacji 1, wstępne sprawdzanie będzie przeprowadzane przed zainstalowaniem aktualizacji z portalu. Kontrole wstępne będzie zastępują błędy i upewnij się, płynniejszy efekt. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Zainstaluj aktualizacje tryb konserwacji za pomocą środowiska Windows PowerShell dla urządzenia StorSimple
Program Windows PowerShell dla StorSimple umożliwia stosowanie aktualizacji trybu konserwacji do urządzenia StorSimple. Wszystkie żądania We/Wy są wstrzymane w tym trybie. Usługi, takie jak trwałej pamięci (NVRAM) lub usługę klastrowania również zostały zatrzymane. Zarówno kontrolery są ponownie uruchamiane po wprowadzeniu lub opuścić ten tryb. Po zakończeniu pracy w tym trybie, wszystkie usługi zostanie wznowiona i powinna być w dobrej kondycji. (Może to potrwać kilka minut).

Jeśli trzeba zastosować aktualizacje tryb konserwacji, otrzymasz alert przy użyciu klasycznego portalu Azure, czy są aktualizacje, które muszą zostać zainstalowane. Ten alert zawiera instrukcje dotyczące instalowanie aktualizacji za pomocą programu Windows PowerShell dla StorSimple. Po zaktualizowaniu urządzenia należy użyć tej samej procedury, aby zmienić urządzenia do trybu regularne. Aby uzyskać instrukcje, zobacz [krok 4: tryb konserwacji zakończenia](#step4).

> [!IMPORTANT]
> * Przed wprowadzeniem tryb konserwacji, sprawdź, czy zarówno kontrolery urządzeń są dobrej kondycji, sprawdzając **stan sprzętu** na **konserwacji** strony w klasycznym portalu Azure. Jeśli kontroler nie jest w dobrej kondycji, skontaktuj się z Microsoft Support następnych kroków. Aby uzyskać więcej informacji przejdź do skontaktuj się z pomocą techniczną firmy Microsoft. 
> * Podczas pracy w trybie konserwacji, należy najpierw zastosować aktualizację na jeden kontroler, a następnie na innym kontrolerze.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Krok 1: Łączenie z konsolą szeregową<a name="step1">
Po pierwsze korzystanie z aplikacji, takiego jak PuTTY dostęp do konsoli szeregowej. W poniższej procedurze wyjaśniono sposób nawiązywania połączenia z konsolą szeregową przy użyciu programu PuTTY.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Krok 2: Wprowadź trybu konserwacji<a name="step2">
Po połączeniu konsoli, należy ustalić, czy są aktualizacje do zainstalowania, a następnie przejść do trybu konserwacji na ich instalację.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Krok 3: Instalowanie aktualizacji<a name="step3">
Następnie zainstaluj aktualizacje.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Krok 4: Tryb obsługi zakończenia<a name="step4">
Na koniec wyjść z trybu konserwacji.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalowanie poprawek za pomocą środowiska Windows PowerShell dla urządzenia StorSimple
W przeciwieństwie do aktualizacji dla programu Microsoft Azure StorSimple są zainstalowane poprawki z folderu udostępnionego. Podobnie jak w przypadku aktualizacji, istnieją dwa typy poprawki: 

* Regularne poprawki 
* Poprawki trybu konserwacji  

Poniższe procedury dotyczą sposobu używania programu Windows PowerShell dla StorSimple zainstalować regularne i poprawki dla trybu konserwacji.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Co się dzieje z aktualizacji w przypadku resetowania urządzenia do ustawień fabrycznych?
Jeśli urządzenie jest zresetowane do ustawień fabrycznych, wszystkie aktualizacje zostaną utracone. Po rejestracji i konfiguracji Resetowanie do ustawień fabrycznych urządzenia, należy ręcznie zainstalować aktualizacje za pośrednictwem klasycznego portalu Azure i/lub środowiska Windows PowerShell dla urządzenia StorSimple. Aby uzyskać więcej informacji na temat resetowania do ustawień fabrycznych, zobacz [zresetowania urządzenia do domyślnych ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [przy użyciu programu Windows PowerShell dla StorSimple do administrowania urządzeniem StorSimple](storsimple-windows-powershell-administration.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple do administrowania urządzeniem StorSimple](storsimple-manager-service-administration.md).


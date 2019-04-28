---
title: Aktualizacja urządzenia StorSimple | Dokumentacja firmy Microsoft
description: Opis sposobu używania funkcji aktualizacji usługi StorSimple do zainstalowania aktualizacji trybu zwykłych i konserwacji i poprawki.
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
ms.openlocfilehash: d973a16c121a1e8ebee10826d135bcbb33ef748c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409991"
---
# <a name="update-your-storsimple-8000-series-device"></a>Aktualizacja urządzenia StorSimple 8000 Series
> [!NOTE]
> Klasyczny portal dla urządzeń StorSimple jest przestarzały. Menedżerowie urządzeń StorSimple dokonają automatycznego przeniesienia do nowej witryny Azure Portal zgodnie z ustalonym harmonogramem wycofywania przestarzałych produktów. Powiadomienie o przeniesieniu otrzymasz pocztą e-mail i za pośrednictwem portalu. Ten dokument zostanie wkrótce usunięty. W razie jakichkolwiek pytań dotyczących przeniesienia, zobacz [— często zadawane pytania: Przenieś do witryny Azure portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Omówienie
Funkcje aktualizacje StorSimple umożliwiają łatwe aktualne urządzenia StorSimple. W zależności od typu aktualizacji można zastosować aktualizacji do urządzenia za pośrednictwem klasycznego portalu Azure lub za pośrednictwem interfejsu programu Windows PowerShell. W tym samouczku opisano typy aktualizacji i sposobu instalacji każdej z nich.

Można zastosować dwa typy aktualizacji urządzenia: 

* Regularne (lub tryb normalny) aktualizacji
* Aktualizacje trybu konserwacji

Możesz zainstalować regularne aktualizacje za pomocą klasycznej witryny Azure portal lub programu Windows PowerShell; jednak należy użyć programu Windows PowerShell, aby zainstalować aktualizacje trybu konserwacji. 

Każdy typ aktualizacji jest opisane osobno, poniżej.

### <a name="regular-updates"></a>Regularne aktualizacje
Regularne aktualizacje są niezakłócającego aktualizacje, które mogą być instalowane, gdy urządzenie jest w trybie normalnym. Te aktualizacje są stosowane za pośrednictwem witryny Microsoft Update w odniesieniu do każdego kontrolera urządzenia. 

> [!IMPORTANT]
> Praca awaryjna kontrolera mogą wystąpić podczas procesu aktualizacji. Jednak to nie wpłynie na dostępność systemu lub operacji.
> 
> 

* Aby uzyskać więcej informacji na temat instalowania regularne aktualizacje za pośrednictwem klasycznego portalu Azure, zobacz [zainstalować regularne aktualizacje za pośrednictwem klasycznego portalu Azure](#install-regular-updates-via-the-azure-classic-portal).
* Można także zainstalować regularne aktualizacje za pośrednictwem programu Windows PowerShell dla usługi StorSimple. Aby uzyskać więcej informacji, zobacz [zainstalować regularne aktualizacje za pośrednictwem programu Windows PowerShell dla usługi StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Aktualizacje trybu konserwacji
Aktualizacje trybu konserwacji są aktualizacje wymagają zatrzymania pracy, takich jak aktualizacje oprogramowania układowego dysku. Te aktualizacje wymagają urządzenia do umieszczenia w trybie konserwacji. Aby uzyskać więcej informacji, zobacz [krok 2: Przejście do trybu konserwacji](#step2). Nie można używać klasycznego portalu Azure, aby zainstalować aktualizacje trybu konserwacji. Zamiast tego należy użyć programu Windows PowerShell dla usługi StorSimple. 

Aby uzyskać więcej informacji na temat instalowania aktualizacji w trybie konserwacji, zobacz [aktualizacje trybu konserwacji zainstalować za pomocą programu Windows PowerShell dla usługi StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Aktualizacje trybu konserwacji musi stosowane osobno do każdego kontrolera. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Zainstaluj regularne aktualizacje za pośrednictwem klasycznego portalu Azure
Klasyczny portal Azure umożliwia stosowanie aktualizacji na urządzeniu StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Zainstaluj regularne aktualizacje za pośrednictwem programu Windows PowerShell dla usługi StorSimple
Alternatywnie można użyć środowiska Windows PowerShell dla usługi StorSimple do stosowania aktualizacji zwykłych (tryb normalny).

> [!IMPORTANT]
> Mimo że można zainstalować regularne aktualizacje przy użyciu programu Windows PowerShell dla usługi StorSimple, zdecydowanie zaleca się zainstalowanie regularne aktualizacje za pośrednictwem klasycznego portalu Azure. Począwszy od aktualizacji 1 wstępne kontrole zostanie przeprowadzone przed zainstalowaniem aktualizacji z witryny portal. Kontrole wstępne będzie wywłaszczenia błędy i upewnij się, płynne. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Zainstaluj aktualizacje trybu konserwacji za pomocą programu Windows PowerShell dla usługi StorSimple
Programu Windows PowerShell dla usługi StorSimple umożliwia stosowanie aktualizacji trybu konserwacji na urządzeniu StorSimple. Wszystkie żądania We/Wy są wstrzymane w tym trybie. Usługi, takie jak trwałej pamięci (NVRAM) lub usługę klastrowania również zostaną zatrzymane. Oba kontrolery są ponownie uruchamiane po wprowadzeniu lub opuścić ten tryb. Po zakończeniu tego trybu, wszystkich usług zostanie wznowiona i powinna być w dobrej kondycji. (Może to potrwać kilka minut).

Jeśli trzeba zastosować aktualizacje trybu konserwacji, otrzymasz alert przy użyciu klasycznego portalu Azure, czy masz aktualizacje, które muszą być zainstalowane. Ten alert uwzględni instrukcje dotyczące korzystania z programu Windows PowerShell dla usługi StorSimple do zainstalowania aktualizacji. Po zaktualizowaniu urządzenia należy użyć tej samej procedury, aby zmienić urządzenie do trybu normalnego. Aby uzyskać instrukcje krok po kroku, zobacz [krok 4: Wyjdź z trybu konserwacji](#step4).

> [!IMPORTANT]
> * Przed wprowadzeniem trybu konserwacji, sprawdź, czy oba kontrolery urządzeń są w dobrej kondycji, sprawdzając **stan sprzętu** na **konserwacji** strony w klasycznej witrynie Azure portal. Jeśli kontrolera nie jest w dobrej kondycji, skontaktuj się z Microsoft Support następnych kroków. Aby uzyskać więcej informacji przejdź do skontaktuj się z pomocą techniczną firmy Microsoft. 
> * Podczas pracy w trybie konserwacji, należy najpierw zastosować aktualizację na jeden kontroler, a następnie na drugim kontrolerze.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Krok 1: Łączenie z konsolą szeregową <a name="step1">
Dostęp do konsoli szeregowej, najpierw za pomocą aplikacji, takiego jak PuTTY. Poniższa procedura wyjaśnia, jak nawiązać połączenie z konsolą szeregową przy użyciu programu PuTTY.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Krok 2: Przejście do trybu konserwacji <a name="step2">
Po nawiązaniu połączenia z konsolą, należy ustalić, czy są aktualizacje do zainstalowania, a następnie przejście do trybu konserwacji, aby je zainstalować.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Krok 3: Zainstaluj aktualizacje <a name="step3">
Następnie zainstaluj aktualizacje.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Krok 4: Wyjdź z trybu konserwacji <a name="step4">
Na koniec wyjść z trybu konserwacji.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalowania poprawek za pomocą programu Windows PowerShell dla usługi StorSimple
W odróżnieniu od aktualizacji usługi Microsoft Azure StorSimple poprawki są instalowane z folderem udostępnionym. Podobnie jak w przypadku aktualizacji, istnieją dwa rodzaje poprawki: 

* Regularne poprawki 
* Poprawki przeznaczone do trybu konserwacji  

Poniższe procedury wyjaśniają jak używać programu Windows PowerShell dla usługi StorSimple do zainstalowania zwykłych i poprawki przeznaczone do trybu konserwacji.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Co stanie się aktualizacji, jeśli przeprowadzisz resetowania do ustawień fabrycznych urządzenia?
W przypadku zresetowania urządzenia do ustawień fabrycznych, wszystkie aktualizacje zostaną utracone. Po rejestracji i konfiguracji urządzenia do ustawień fabrycznych może być konieczne ręczne instalowanie aktualizacji za pośrednictwem klasycznego portalu Azure i/lub programu Windows PowerShell dla usługi StorSimple. Aby uzyskać więcej informacji na temat resetowania do ustawień fabrycznych, zobacz [zresetowania urządzenia do domyślnych ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [przy użyciu programu Windows PowerShell dla usługi StorSimple do administrowania urządzeniem StorSimple](storsimple-windows-powershell-administration.md).
* Dowiedz się więcej o [do administrowania urządzeniem StorSimple przy użyciu usługi StorSimple Manager](storsimple-manager-service-administration.md).


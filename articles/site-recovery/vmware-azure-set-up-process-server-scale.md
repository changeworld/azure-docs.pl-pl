---
title: Skonfiguruj serwer przetwarzania na platformie Azure dla maszyny Wirtualnej VMware i powrotu po awarii serwera fizycznego z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak skonfigurować serwer przetwarzania na platformie Azure do powrotu po awarii maszyn wirtualnych platformy Azure do programu VMware.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 7bbe690e749680edde08facadf6d5910d7896f7e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Skonfiguruj serwer przetwarzania na platformie Azure do powrotu po awarii

Po przejścia w tryb failover maszyn wirtualnych VMware lub serwerów fizycznych do platformy Azure przy użyciu [usługi Site Recovery](site-recovery-overview.md), możesz w trybie je do lokacji lokalnej, gdy jest uruchomione ponownie. Aby wykonaj powrót po awarii, należy skonfigurować tymczasowy serwer przetwarzania na platformie Azure, do obsługi replikacji z platformy Azure do środowiska lokalnego. Po zakończeniu powrotu po awarii można usunąć tej maszyny Wirtualnej.

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się więcej o [przełączonej](vmware-azure-reprotect.md) i [powrotu po awarii](vmware-azure-failback.md) procesu.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>Wdrażanie serwera przetwarzania na platformie Azure

1. W magazynie > **infrastruktura usługi Site Recovery**> **Zarządzaj** > **serwery konfiguracji**, wybierz serwer konfiguracji.
2. Na stronie serwera, kliknij przycisk **+ serwera przetwarzania**
3. W **Dodaj serwer przetwarzania** strony i wybierz, aby wdrożyć serwer przetwarzania na platformie Azure.
4. Określ ustawienia platformy Azure, w tym subskrypcja używana na potrzeby trybu failover, grupy zasobów, region platformy Azure używana w trybie failover i sieci wirtualnej, w którym znajdują się maszyny wirtualne platformy Azure. Jeśli używasz wielu sieci platformy Azure, należy serwer proces w każdej z nich.
5. W **nazwy serwera**, **nazwy użytkownika**, i **hasło**, określ nazwę serwera przetwarzania i poświadczenia, które zostaną przypisane uprawnienia administratora na serwerze.
6. Określ konto magazynu do zastosowania w przypadku dysków maszyny Wirtualnej serwera, podsieci, w której znajdą się serwera przetwarzania maszyny Wirtualnej i adres IP serwera, który zostanie przypisany podczas uruchamiania maszyny Wirtualnej.
7. Kliknij przycisk **OK** przycisk, aby rozpocząć wdrażanie serwera przetwarzania maszyny Wirtualnej.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Rejestrowanie serwera przetwarzania (działające na platformie Azure) do konfiguracji serwera (uruchamiane lokalnie)

Po serwera przetwarzania maszyna wirtualna jest uruchomiona, konieczne jest zarejestrowanie go za pomocą lokalnego serwera konfiguracji, w następujący sposób:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]



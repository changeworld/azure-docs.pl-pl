---
title: Konfigurowanie serwera przetwarzania na platformie Azure dla maszyny Wirtualnej VMware i serwera fizycznego powrotu po awarii przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania serwera przetwarzania na platformie Azure, powrót po awarii maszyn wirtualnych platformy Azure do programu VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 037f0ff64b114ce9341702564147825099695aa0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62110034"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Konfigurowanie serwera przetwarzania powrotu po awarii na platformie Azure

Po przełączeniu w tryb failover maszyny wirtualne VMware lub serwery fizyczne do platformy Azure za pomocą [Site Recovery](site-recovery-overview.md), można przełączyć je do lokacji lokalnej, gdy jest uruchomione ponownie. Aby można było wykonać powrotu po awarii, należy skonfigurować serwer tymczasowy przetwarzania na platformie Azure, w celu obsługi replikacji z platformy Azure do środowiska lokalnego. Po zakończeniu powrotu po awarii, można usunąć tej maszyny Wirtualnej.

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się więcej o [ponownego włączania ochrony](vmware-azure-reprotect.md) i [powrotu po awarii](vmware-azure-failback.md) procesu.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Wdrażanie serwera przetwarzania na platformie Azure

1. W magazynie > **infrastruktura usługi Site Recovery**> **Zarządzaj** > **serwery konfiguracji**, wybierz serwer konfiguracji.
2. Na stronie serwera, kliknij przycisk **+ serwer przetwarzania**
3. W **Dodaj serwer przetwarzania** strony, a następnie wybierz, aby wdrożyć serwer przetwarzania na platformie Azure.
4. Określ ustawienia platformy Azure, w tym subskrypcja używana na potrzeby trybu failover, grupę zasobów i region platformy Azure używane w trybie failover i sieci wirtualnej, w którym znajdują się maszyny wirtualne platformy Azure. Jeśli używasz wielu sieci platformy Azure, potrzebujesz serwera przetwarzania w każdej z nich.

   ![Dodawanie elementu galerii serwera przetwarzania](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. W **nazwy serwera**, **nazwa_użytkownika**, i **hasło**, określ nazwę serwera przetwarzania i poświadczenia, które zostaną przypisane uprawnienia administratora na serwerze.
5. Określ konto magazynu ma być używany dla serwera dysków maszyny Wirtualnej, podsieci, w którym zostaną umieszczone na serwerze przetwarzania maszyny Wirtualnej i adres IP serwera, która zostanie przypisana podczas uruchamiania maszyny Wirtualnej.
6. Kliknij przycisk **OK** przycisk, aby rozpocząć wdrażanie maszyny Wirtualnej serwera przetwarzania.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Rejestrowanie serwera przetwarzania (działających na platformie Azure) do serwera konfiguracji (działającego lokalnie)

Po skonfigurowaniu i uruchomieniu maszyny Wirtualnej serwera przetwarzania należy zarejestrować go na lokalnym serwerze konfiguracji w następujący sposób:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]



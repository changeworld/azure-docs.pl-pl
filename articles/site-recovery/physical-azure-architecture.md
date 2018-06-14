---
title: Serwer fizyczny do architektury replikacji Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie składników i architektury używane podczas replikowania lokalnych serwerów fizycznych do platformy Azure z usługą Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: raynew
ms.openlocfilehash: a8af2ee4a32925603d24aee2403ab504a0ca05a8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874896"
---
# <a name="physical-server-to-azure-replication-architecture"></a>Serwer fizyczny do architektury Azure replikacji

W tym artykule opisano architektura i procesy stosowane podczas replikacji, pracy awaryjnej i odzyskiwania serwerów fizycznych systemu Windows i Linux między lokacją lokalną i platformą Azure, przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi.


## <a name="architectural-components"></a>Składniki architektury

Poniższej tabeli i grafiki zapewnia widok ogólny składników używanych w przypadku replikacji serwera fizycznego na platformie Azure.  

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konto magazynu Azure i sieć platformy Azure. | Replikowane dane z lokalnych maszyn wirtualnych są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone przy użyciu zreplikowanych danych podczas uruchamiania błędu za pośrednictwem z lokalnej na platformie Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Serwer konfiguracji** | Pojedynczy komputer fizyczny lokalnymi lub wdrożeniu maszyny Wirtualnej VMware do wszystkich lokalną składnikami usługi Site Recovery. Maszyna wirtualna działa serwer konfiguracji, serwer przetwarzania i główny serwer docelowy. | Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych.
 **Serwer przetwarzania**:  | Instalowany domyślnie z serwerem konfiguracji. | Działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.<br/><br/> Serwer przetwarzania instaluje usługi mobilności na serwerach, które mają być replikowane.<br/><br/> Wraz z rozwojem wdrożenia, możesz dodać dodatkowe, oddzielne procesu serwerów do obsługi większych ilości ruchu replikacji.
 **Główny serwer docelowy** | Instalowany domyślnie z serwerem konfiguracji. | Służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.<br/><br/> W przypadku dużych wdrożeń można dodać dodatkowe, oddzielne główny serwer docelowy do powrotu po awarii.
**Zreplikowane serwerów** | Usługa mobilności jest zainstalowany na każdym serwerze, które są replikowane. | Zaleca się Zezwalaj automatycznej instalacji z serwera przetwarzania. Możesz też ręcznie zainstalować usługę lub użyć metody wdrażania automatycznego, takie jak System Center Configuration Manager.

**Fizycznych do platformy Azure architektury**

![Składniki](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proces replikacji

1. Konfigurowanie wdrożenia, łącznie z lokalnymi i składniki platformy Azure. W magazynie usług odzyskiwania Określ replikacji źródłowych i docelowych, skonfiguruj serwer konfiguracji Utwórz zasady replikacji i włączyć replikację.
2. Replikacja maszyny, zgodnie z zasadami replikacji i początkowej kopii danych serwera są replikowane do magazynu Azure.
3. Po zakończeniu replikacji początkowej, rozpoczyna replikację zmian różnicowych na platformie Azure. Śledzone zmiany dla maszyny są przechowywane w pliku hrl.
    - Maszyny komunikacji z serwerem konfiguracji na porcie 443 protokołu HTTPS dla ruchu przychodzącego dla zarządzania replikacji.
    - Maszyny wysyłanie danych replikacji na serwer przetwarzania na porcie HTTPS 9443 dla ruchu przychodzącego (może być modyfikowany).
    - Serwer konfiguracji synchronizuje replikację z platformą Azure za pośrednictwem portu HTTPS 443 dla danych wychodzących.
    - Serwer przetwarzania odbiera dane z maszyn źródłowych, optymalizuje je i szyfruje, a następnie wysyła do usługi Azure Storage za pośrednictwem portu 443 dla danych wychodzących.
    - Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Funkcja spójności wielu maszyn wirtualnych jest używana, jeśli wiele maszyn zostanie połączonych w grupę replikacji, która współużytkuje punkty odzyskiwania spójne na poziomie awarii i aplikacji. Jest to przydatne, jeśli maszyny obsługują to samo obciążenie i muszą być spójne.
4. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Alternatywnie można użyć [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) usługi Azure ExpressRoute. Replikowanie ruchu za pośrednictwem sieci VPN typu lokacja-lokacja z lokacji lokalnej platformy Azure nie jest obsługiwane.


**Fizyczny do procesu replikacji Azure**

![Proces replikacji](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji i uruchomieniu wyszczególniania odzyskiwania po awarii (test trybu failover) do sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami, można uruchomić trybu failover i powrotu po awarii na potrzeby. Należy pamiętać, że:

- Planowane przejście w tryb failover nie jest obsługiwane.
- Wróć do programu VMware lokalnej maszyny Wirtualnej musi zakończyć się niepowodzeniem. Oznacza to, że należy lokalnej infrastruktury VMware, nawet w przypadku replikacji lokalnych serwerów fizycznych do platformy Azure.
- W tryb failover jednego komputera lub utworzyć plany odzyskiwania do trybu failover wiele maszyn jednocześnie.
- Po uruchomieniu trybu failover, maszynach wirtualnych platformy Azure są tworzone na podstawie zreplikowane dane w magazynie Azure.
- Po wyzwalają początkowej trybu failover, Zatwierdź go uruchomić uzyskiwania dostępu do obciążenia z maszyny Wirtualnej platformy Azure.
- Po ponownym udostępnieniu głównej lokacji lokalnej można do niej powrócić po awarii.
- Należy skonfigurować infrastrukturę powrotu po awarii, w tym:
    - **Tymczasowy serwer przetwarzania na platformie Azure**: aby awarii z platformy Azure, należy skonfigurować maszyny Wirtualnej platformy Azure do działania jako serwer przetwarzania, do obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
    - **Połączenia sieci VPN**: aby wykonaj powrót po awarii, należy połączenie sieci VPN (lub usługa Azure ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
    - **Oddzielne główny serwer docelowy**: Domyślnie główny serwer docelowy, który został zainstalowany na serwerze konfiguracji w programie VMware lokalnej maszyny Wirtualnej, obsługuje powrót po awarii. Jednak niepowodzenie wstecz dużych ilości ruchu należy należy skonfigurować oddzielny lokalny główny serwer docelowy do tego celu.
    - **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. To został utworzony automatycznie po utworzeniu zasad replikacji z lokalnej na platformie Azure.
    - **Infrastruktura VMware**: należy infrastruktury VMware do powrotu po awarii. Nie można powracać po awarii do serwera fizycznego.
- Po składniki zostały spełnione, powrotu po awarii w trzech etapach:
    - Etap 1: Włącz ponownie ochronę maszyn wirtualnych platformy Azure, dzięki czemu mają być replikowane z platformy Azure do maszyn wirtualnych VMware lokalnymi.
    - Etap 2: Uruchomić trybu failover do lokacji lokalnej.
    - Etap 3: Po obciążeń powiodły się z powrotem, należy ponownie włączyć replikację.

**VMware powrotu po awarii z platformy Azure**

![Powrót po awarii](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z [w tym samouczku](physical-azure-disaster-recovery.md) serwera fizycznych do platformy Azure replikacji.

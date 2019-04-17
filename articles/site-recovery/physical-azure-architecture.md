---
title: Architektura dla odzyskiwania po awarii serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas odzyskiwania po awarii serwerów fizycznych lokalnych na platformę Azure za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 99aec3be893693e523dffefbb3c422222ac19a2e
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616870"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Serwer fizyczny do architektury odzyskiwania po awarii platformy Azure

W tym artykule opisano architekturę i procesów związanych z replikacji, pracy awaryjnej i odzyskiwania serwerów fizycznych Windows i Linux między lokacją lokalną i platformą Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md) usługi.


## <a name="architectural-components"></a>Składniki architektury

Poniższej tabeli i grafika przedstawia ogólny widok składniki używane na potrzeby replikacji serwerów fizycznych na platformę Azure.  

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure i siecią platformy Azure. | Replikowane dane ze środowiska lokalnego maszyny fizyczne są przechowywane na platformie Azure usługa managed disks. Maszyny wirtualne platformy Azure są tworzone z replikowanych danych, po przejściu ze środowiska lokalnego do platformy Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Serwer konfiguracji** | Pojedynczy on-premises komputer fizyczny lub maszyny Wirtualnej VMware jest wdrażana do uruchomienia wszystkie lokalne składniki usługi Site Recovery. Maszyna wirtualna działa serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy. | Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych.
 **Serwer przetwarzania**:  | Instalowany domyślnie wraz z serwerem konfiguracji. | Działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.<br/><br/> Serwer przetwarzania instaluje także usługę mobilności na serwerach, które mają być replikowane.<br/><br/> Wraz z rozwojem wdrożenia, możesz dodać dodatkowe, oddzielny proces serwerów w celu obsługi większych ilości ruchu związanego z replikacją.
 **Główny serwer docelowy** | Instalowany domyślnie wraz z serwerem konfiguracji. | Służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.<br/><br/> W przypadku dużych wdrożeń możesz dodać dodatkowe, oddzielny główny serwer docelowy do powrotu po awarii.
**Zreplikowane serwerów** | Usługa mobilności jest zainstalowana na każdym serwerze, które są replikowane. | Zaleca się, że zezwolisz automatycznej instalacji z serwera przetwarzania. Możesz też ręcznie zainstalować usługę lub użyć metody wdrażania automatycznego, takie jak System Center Configuration Manager.

**Środowisko fizyczne do architektury platformy Azure**

![Składniki](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proces replikacji

1. Należy skonfigurować wdrożenie, w tym lokalnych i składników platformy Azure. W magazynie usługi Recovery Services, należy określić źródło i cel replikacji, skonfigurować serwer konfiguracji, utworzyć zasady replikacji i włączanie replikacji.
2. Replikowanie maszyn zgodnie z zasadami replikacji i tworzy początkową kopię danych serwera są replikowane do usługi Azure storage.
3. Po zakończeniu replikacji początkowej rozpoczyna się replikacja zmian różnicowych do platformy Azure. Śledzone zmiany dla maszyny są przechowywane w pliku hrl.
    - Maszyny komunikują się z serwerem konfiguracji na porcie HTTPS 443 dla ruchu przychodzącego na potrzeby zarządzania replikacją.
    - Maszyny wysyłają dane replikacji do serwera przetwarzania na porcie HTTPS 9443 dla ruchu przychodzącego (można modyfikować).
    - Serwer konfiguracji synchronizuje replikację z platformą Azure za pośrednictwem portu HTTPS 443 dla danych wychodzących.
    - Serwer przetwarzania odbiera dane z maszyn źródłowych, optymalizuje je i szyfruje, a następnie wysyła do usługi Azure Storage za pośrednictwem portu 443 dla danych wychodzących.
    - Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Funkcja spójności wielu maszyn wirtualnych jest używana, jeśli wiele maszyn zostanie połączonych w grupę replikacji, która współużytkuje punkty odzyskiwania spójne na poziomie awarii i aplikacji. Jest to przydatne, jeśli maszyny obsługują to samo obciążenie i muszą być spójne.
4. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Alternatywnie można użyć [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#publicpeering) usługi Azure ExpressRoute. Replikowanie ruchu za pośrednictwem sieci VPN typu lokacja-lokacja z lokacji lokalnej platformy Azure nie jest obsługiwane.


**Środowisko fizyczne do procesu replikacji platformy Azure**

![Proces replikacji](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji i po uruchomieniu próbnego odzyskiwania po awarii (Testowanie trybu failover), aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, możesz uruchomić tryb failover i powrotu po awarii na potrzeby. Należy pamiętać, że:

- Planowane przejście w tryb failover nie jest obsługiwane.
- Należy powrotu po awarii do lokalnej maszyny Wirtualnej VMware. Oznacza to, że potrzebujesz lokalnej infrastruktury VMware, nawet w przypadku replikowania lokalnych serwerów fizycznych na platformę Azure.
- W trybie Failover pojedynczą maszynę lub tworzyć plany odzyskiwania, do trybu failover wiele maszyn jednocześnie.
- Po uruchomieniu trybu failover, maszyny wirtualne platformy Azure są tworzone na podstawie replikowanych danych w usłudze Azure storage.
- Po wyzwoleniem początkowej trybu failover, należy zatwierdzić je, aby można było rozpocząć uzyskiwanie dostępu do obciążenia maszyny wirtualnej platformy Azure.
- Po ponownym udostępnieniu głównej lokacji lokalnej można do niej powrócić po awarii.
- Należy skonfigurować infrastrukturę powrotu po awarii, w tym:
    - **Tymczasowy serwer przetwarzania na platformie Azure**: Aby nie powiedzie się, powrót po awarii z platformy Azure, należy skonfigurować Maszynę wirtualną platformy Azure do działania jako serwer przetwarzania do obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
    - **Połączenie sieci VPN**: Do powrotu po awarii, potrzebujesz połączenia sieci VPN (lub usługi Azure ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
    - **Osobny główny serwer docelowy**: Domyślnie główny serwer docelowy, który został zainstalowany na serwerze konfiguracji w programie VMware lokalnych maszyn wirtualnych, obsługuje powrót po awarii. Jednakże jeśli musisz zakończyć się niepowodzeniem ponownie dużych ilości ruchu należy skonfigurować oddzielny lokalny główny serwer docelowy w tym celu.
    - **Zasady powrotu po awarii**: Replikacja z lokacji lokalnej, potrzebne są zasady powrotu po awarii. Ten został utworzony automatycznie podczas tworzenia zasad replikacji ze środowiska lokalnego do platformy Azure.
    - **Infrastruktura VMware**: Infrastruktura VMware potrzeby powrotu po awarii. Nie można powracać po awarii do serwera fizycznego.
- Po składniki znajdują się w miejscu, powrót po awarii odbywa się w trzech etapach:
    - Etap 1: Ponowne włączanie ochrony maszyn wirtualnych platformy Azure, dzięki czemu mają być replikowane na platformie Azure do lokalnych maszyn wirtualnych VMware.
    - Etap 2: Uruchamianie trybu failover do lokacji lokalnej.
    - Etap 3: Po obciążeń przywrócono po awarii, możesz ponownie włączyć replikację.

**Podczas powrotu po awarii programu VMware na platformie Azure**

![Powrót po awarii](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z [w tym samouczku](physical-azure-disaster-recovery.md) Aby włączyć serwer fizyczny z platformą Azure replikacji.

---
title: Architektura odzyskiwania po awarii serwera fizycznego w Azure Site Recovery
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas odzyskiwania po awarii lokalnych serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 72f21babd4d12e69cd346d8693e5ed4fe9117134
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513953"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architektura odzyskiwania po awarii serwera fizycznego na platformę Azure

W tym artykule opisano architekturę i procesy używane podczas replikacji, przełączania awaryjnego i odzyskiwania fizycznych serwerów z systemami Windows i Linux między lokacją lokalną i platformą Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Składniki architektury

Poniższa tabela i ilustracja przedstawiają ogólny widok składników służących do replikacji serwera fizycznego do platformy Azure.  

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure i sieć platformy Azure. | Zreplikowane dane z lokalnych maszyn fizycznych są przechowywane na dyskach zarządzanych przez platformę Azure. Maszyny wirtualne platformy Azure są tworzone przy użyciu replikowanych danych po uruchomieniu trybu failover z lokalizacji lokalnej na platformę Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Serwer konfiguracji** | Pojedyncza lokalna maszyna fizyczna lub maszyna wirtualna VMware jest wdrażana w celu uruchomienia wszystkich lokalnych składników Site Recovery. Na maszynie wirtualnej jest uruchomiony serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy. | Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych.
 **Serwer przetwarzania**:  | Instalowany domyślnie razem z serwerem konfiguracji. | Działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.<br/><br/> Serwer przetwarzania instaluje także usługę mobilności na serwerach, które mają być replikowane.<br/><br/> Wraz z rozwojem wdrożenia można dodać dodatkowe, oddzielne serwery przetwarzania do obsługi większych woluminów ruchu związanego z replikacją.
 **Główny serwer docelowy** | Instalowany domyślnie razem z serwerem konfiguracji. | Służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.<br/><br/> W przypadku dużych wdrożeń można dodać dodatkowy, oddzielny główny serwer docelowy na potrzeby powrotu po awarii.
**Zreplikowane serwery** | Usługa mobilności jest instalowana na każdym replikowanym serwerze. | Zalecamy zezwolenie na automatyczną instalację z serwera przetwarzania. Alternatywnie możesz zainstalować usługę ręcznie lub użyć metody automatycznego wdrażania, takiej jak Configuration Manager.

**Architektura fizyczna-Azure**

![Składniki](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proces replikacji

1. Należy skonfigurować wdrożenie, w tym składniki lokalne i usługi platformy Azure. W magazynie Recovery Services należy określić źródło i cel replikacji, skonfigurować serwer konfiguracji, utworzyć zasady replikacji i włączyć replikację.
2. Maszyny są replikowane zgodnie z zasadami replikacji, a początkowa kopia danych serwera jest replikowana do usługi Azure Storage.
3. Po zakończeniu replikacji początkowej zostanie rozpoczęta replikacja zmian różnicowych na platformie Azure. Śledzone zmiany dla maszyny są przechowywane w pliku hrl.
    - Maszyny komunikują się z serwerem konfiguracji na porcie HTTPS 443 ruchu przychodzącego na potrzeby zarządzania replikacją.
    - Maszyny wysyłają dane replikacji do serwera przetwarzania na porcie HTTPS 9443 ruchu przychodzącego (można je modyfikować).
    - Serwer konfiguracji synchronizuje replikację z platformą Azure za pośrednictwem portu HTTPS 443 dla danych wychodzących.
    - Serwer przetwarzania odbiera dane z maszyn źródłowych, optymalizuje je i szyfruje, a następnie wysyła do usługi Azure Storage za pośrednictwem portu 443 dla danych wychodzących.
    - Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Funkcja spójności wielu maszyn wirtualnych jest używana, jeśli wiele maszyn zostanie połączonych w grupę replikacji, która współużytkuje punkty odzyskiwania spójne na poziomie awarii i aplikacji. Jest to przydatne, jeśli maszyny obsługują to samo obciążenie i muszą być spójne.
4. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Alternatywnie można użyć [publicznej komunikacji równorzędnej](../expressroute/about-public-peering.md) usługi Azure ExpressRoute. Replikowanie ruchu za pośrednictwem sieci VPN typu lokacja-lokacja z lokacji lokalnej platformy Azure nie jest obsługiwane.


**Proces replikacji fizycznej do platformy Azure**

![Proces replikacji](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji i uruchomieniu funkcji drążenia odzyskiwania po awarii (test pracy w trybie failover) w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami, możesz uruchomić tryb failover i powrót po awarii, jak to konieczne. Należy pamiętać, że:

- Planowane przejście w tryb failover nie jest obsługiwane.
- Musisz powrócić po awarii do lokalnej maszyny wirtualnej VMware. Oznacza to, że potrzebna jest lokalna infrastruktura VMware, nawet w przypadku replikowania lokalnych serwerów fizycznych na platformę Azure.
- Pojedyncze maszyny są przełączane w tryb failover lub można utworzyć plany odzyskiwania, aby przełączyć wiele maszyn jednocześnie do trybu failover.
- Po uruchomieniu trybu failover maszyny wirtualne platformy Azure są tworzone na podstawie replikowanych danych w usłudze Azure Storage.
- Po wyzwoleniu początkowej pracy w trybie failover należy zatwierdzić, aby rozpocząć uzyskiwanie dostępu do obciążenia z maszyny wirtualnej platformy Azure.
- Po ponownym udostępnieniu głównej lokacji lokalnej można do niej powrócić po awarii.
- Należy skonfigurować infrastrukturę powrotu po awarii, w tym:
    - **Tymczasowy serwer przetwarzania na platformie Azure**: aby powrócić po awarii z platformy Azure, należy skonfigurować maszynę wirtualną platformy Azure do działania jako serwer przetwarzania w celu obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
    - **Połączenie sieci VPN**: w celu powrotu po awarii wymagane jest połączenie sieci VPN (lub Azure ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
    - **Oddzielny główny serwer docelowy**: domyślnie główny serwer docelowy, który został zainstalowany z serwerem konfiguracji, na lokalnej maszynie wirtualnej VMware obsługuje powrót po awarii. Jeśli jednak zachodzi potrzeba niepowodzenia odtwarzania dużych ilości danych, należy skonfigurować w tym celu oddzielny lokalny główny serwer docelowy.
    - **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. Ta wartość została utworzona automatycznie podczas tworzenia zasad replikacji z lokalnego na platformę Azure.
    - **Infrastruktura VMware**: potrzebna jest infrastruktura VMware na potrzeby powrotu po awarii. Nie można powracać po awarii do serwera fizycznego.
- Po zakończeniu powrotu po awarii w trzech etapach wystąpi powrót
    - Etap 1. Ponowne włączanie ochrony maszyn wirtualnych platformy Azure w celu replikowania ich z powrotem z platformy Azure do lokalnych maszyn wirtualnych programu VMware.
    - Etap 2: uruchamianie trybu failover w lokacji lokalnej.
    - Etap 3. po niepomyślnym zakończeniu obciążeń należy ponownie włączyć replikację.

**Powrót po awarii programu VMware z platformy Azure**

![Powrót po awarii](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [tym samouczkiem](physical-azure-disaster-recovery.md) , aby włączyć replikację serwera fizycznego na platformie Azure.

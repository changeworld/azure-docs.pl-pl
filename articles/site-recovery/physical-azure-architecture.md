---
title: Architektura odzyskiwania po awarii serwera fizycznego w usłudze Azure Site Recovery
description: Ten artykuł zawiera omówienie składników i architektury używane podczas odzyskiwania po awarii lokalnych serwerów fizycznych na platformie Azure za pomocą usługi Azure Site Recovery.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 089d981284986a2b6eb0ee7f1dbd401fc7ce4fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162841"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architektura odzyskiwania po awarii z serwera fizycznego do platformy Azure

W tym artykule opisano architekturę i procesy używane podczas replikowania, pracy awaryjnej i odzyskiwania fizycznych serwerów systemu Windows i Linux między lokacją lokalną a platformą Azure przy użyciu usługi [Azure Site Recovery.](site-recovery-overview.md)

## <a name="architectural-components"></a>Składniki architektury

Poniższa tabela i grafika zawiera widok wysokiego poziomu składników używanych do replikacji serwera fizycznego na platformie Azure.

| **Składnik** | **Wymaganie** | **Szczegóły** |
| --- | --- | --- |
| **Azure** | Subskrypcja platformy Azure i sieć platformy Azure. | Replikowane dane z lokalnych komputerów fizycznych są przechowywane na dyskach zarządzanych platformy Azure. Maszyny wirtualne platformy Azure są tworzone przy obliczu replikowanych danych po uruchomieniu pracy awaryjnej z lokalnego na platformę Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone. |
| **Serwer przetwarzania** | Zainstalowany domyślnie wraz z serwerem konfiguracji. | Działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.<br/><br/> Serwer przetwarzania instaluje również usługę mobilności na serwerach, które chcesz replikować.<br/><br/> W miarę rozwoju wdrożenia można dodać dodatkowe, oddzielne serwery procesów do obsługi większych woluminów ruchu replikacji. |
| **Główny serwer docelowy** | Zainstalowany domyślnie wraz z serwerem konfiguracji. | Obsługuje dane replikacji podczas powrotu po awarii z platformy Azure.<br/><br/> W przypadku dużych wdrożeń można dodać dodatkowy, oddzielny serwer docelowy dla powrotu po awarii. |
| **Serwery replikowane** | Usługa mobilności jest zainstalowana na każdym serwerze, który replikujesz. | Zalecamy zezwolenie na automatyczną instalację z serwera przetwarzania. Można też zainstalować usługę ręcznie lub użyć metody wdrażania automatycznego, takiej jak program Menedżer konfiguracji. |

**Architektura maszyna fizyczna–Azure**

![Składniki](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proces replikacji

1. Skonfigurować wdrożenie, w tym lokalnych i składników platformy Azure. W przechowalni usług odzyskiwania można określić źródło replikacji i obiekt docelowy, skonfigurować serwer konfiguracji, utworzyć zasady replikacji i włączyć replikację.
1. Maszyny replikują się przy użyciu zasad replikacji, a początkowa kopia danych serwera jest replikowana do magazynu platformy Azure.
1. Po zakończeniu replikacji początkowej rozpoczyna się replikacja zmian różnicowych na platformie Azure. Śledzone zmiany dla komputera są przechowywane w pliku z rozszerzeniem _.hrl._
   - Maszyny komunikują się z serwerem konfiguracji na przychodzącym porcie HTTPS 443 w celu zarządzania replikacją.
   - Maszyny wysyłają dane replikacji do serwera przetwarzania na porcie HTTPS 9443 przychodzącego (można zmodyfikować).
   - Serwer konfiguracji organizuje zarządzanie replikacją za pomocą platformy Azure za pośrednictwem portu HTTPS 443 wychodzącego.
   - Serwer przetwarzania odbiera dane z komputerów źródłowych, optymalizuje je i szyfruje oraz wysyła do magazynu platformy Azure za pośrednictwem portu HTTPS 443 wychodzącego.
   - Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Funkcja spójności wielu maszyn wirtualnych jest używana, jeśli wiele maszyn zostanie połączonych w grupę replikacji, która współużytkuje punkty odzyskiwania spójne na poziomie awarii i aplikacji. Te grupy są przydatne, jeśli na komputerach jest uruchomione to samo obciążenie i muszą być spójne.
1. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Alternatywnie można użyć [publicznej komunikacji równorzędnej](../expressroute/about-public-peering.md) usługi Azure ExpressRoute.

   > [!NOTE]
   > Replikacja nie jest obsługiwana przez sieć VPN typu lokacja-lokacja z lokacji lokalnej lub [prywatnej komunikacji równorzędnej](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)usługi Azure ExpressRoute.

**Proces replikacji fizycznej do platformy Azure**

![Proces replikacji](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji można uruchomić ćwiczenie odzyskiwania po awarii (test pracy awaryjnej), aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami. Następnie można w razie potrzeby przebieć awaryjnie i wrócić po awarii. Należy wziąć pod uwagę następujące elementy:

- Planowane przejście w tryb failover nie jest obsługiwane.
- Konieczne jest ponowne powrót do lokalnej maszyny Wirtualnej VMware. Potrzebna jest lokalna infrastruktura VMware, nawet podczas replikowania lokalnych serwerów fizycznych na platformie Azure.
- Przełączenie w tryb fail over jednego komputera lub tworzenie planów odzyskiwania w trybie fail over wiele komputerów razem.
- Po uruchomieniu pracy awaryjnej maszyny wirtualne platformy Azure są tworzone na podstawie replikowanych danych w magazynie platformy Azure.
- Po wyzwoleniu początkowej pracy awaryjnej, należy zatwierdzić go, aby rozpocząć dostęp do obciążenia z maszyny Wirtualnej platformy Azure.
- Po ponownym udostępnieniu głównej lokacji lokalnej można do niej powrócić po awarii.
- Skonfiguruj infrastrukturę powrotu po awarii, która obejmuje:
  - **Tymczasowy serwer procesów na platformie Azure:** Aby odzyskać po awarii z platformy Azure, skonfigurować maszynę wirtualną platformy Azure do działania jako serwer procesu, do obsługi replikacji z platformy Azure. Tę maszynę wirtualną można usunąć po zakończeniu po awarii.
  - **Połączenie sieci VPN:** Aby przywrócić po awarii, potrzebujesz połączenia sieci VPN (lub usługi Azure ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
  - **Oddzielny główny serwer docelowy:** Domyślnie powrót po awarii jest obsługiwany przez główny serwer docelowy zainstalowany z serwerem konfiguracji na lokalnej maszynie wirtualnej VMware. Jeśli konieczne jest przywrócenie dużych ilości ruchu po awarii, należy skonfigurować oddzielny lokalny serwer docelowy.
  - **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. Zasady zostały automatycznie utworzone podczas tworzenia zasad replikacji z lokalnego na platformę Azure.
  - **Infrastruktura VMware:** Aby wrócić po awarii, potrzebujesz infrastruktury VMware. Nie można powracać po awarii do serwera fizycznego.
- Po składniki są w miejscu, powrót awaryjny występuje w trzech etapach:
  - **Etap 1:** Ponowne przetwarzanie maszyn wirtualnych platformy Azure, tak aby były replikowane z platformy Azure z powrotem do lokalnych maszyn wirtualnych VMware.
  - **Etap 2:** Uruchom przechołów awaryjnych do lokacji lokalnej.
  - **Etap 3:** Po zakończeniu pracy obciążeń replikacja wielokrotnego konfigurowania.

**Powrót po awarii VMware z platformy Azure**

![Powrót po awarii](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować odzyskiwanie po awarii dla serwerów fizycznych na platformie Azure, zobacz [przewodnik jak to zrobić](physical-azure-disaster-recovery.md).

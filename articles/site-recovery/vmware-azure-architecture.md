---
title: Architektura odzyskiwania po awarii maszyn wirtualnych VMware w usłudze Azure Site Recovery
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych VMware na platformie Azure za pomocą usługi Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 77b4dd4c0efbe6d03e64865f18c2c87614aaecb5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632525"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architektura odzyskiwania po awarii vMware na platformie Azure

W tym artykule opisano architekturę i procesy używane podczas wdrażania replikacji odzyskiwania po awarii, pracy awaryjnej i odzyskiwania maszyn wirtualnych VMware (VMware) między lokalną lokacją VMware a platformą Azure przy użyciu usługi [Azure Site Recovery.](site-recovery-overview.md)


## <a name="architectural-components"></a>Składniki architektury

Poniższa tabela i grafika zapewniają widok wysokiego poziomu składników używanych do odzyskiwania po awarii VMware na platformie Azure.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konto usługi Azure Storage dla pamięci podręcznej, dysku zarządzanego i sieci platformy Azure. | Replikowane dane z lokalnych maszyn wirtualnych są przechowywane w magazynie platformy Azure. Maszyny wirtualne platformy Azure są tworzone przy obliczu replikowanych danych po uruchomieniu pracy awaryjnej z lokalnego na platformę Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Maszyna serwera konfiguracji** | Pojedyncza maszyna lokalna. Zaleca się uruchomienie go jako maszyny Wirtualnej VMware, które można wdrożyć z pobranego szablonu OVF.<br/><br/> Na komputerze są uruchamiane wszystkie lokalne składniki odzyskiwania lokacji, które obejmują serwer konfiguracji, serwer przetwarzania i główny serwer docelowy. | **Serwer konfiguracji:** Koordynuje komunikację między lokalną a platformą Azure i zarządza replikacją danych.<br/><br/> **Serwer przetwarzania:** Zainstalowany domyślnie na serwerze konfiguracji. Odbiera dane replikacji; optymalizuje go za pomocą buforowania, kompresji i szyfrowania; i wysyła go do usługi Azure Storage. Serwer przetwarzania instaluje także usługę Azure Site Recovery Mobility Service na maszynach wirtualnych, które będą replikowane, i przeprowadza automatycznie odnajdywanie lokalnych maszyn wirtualnych. W miarę rozwoju wdrożenia można dodać dodatkowe, oddzielne serwery procesów do obsługi większych woluminów ruchu replikacji.<br/><br/> **Główny serwer docelowy:** Zainstalowany domyślnie na serwerze konfiguracji. Obsługuje dane replikacji podczas powrotu po awarii z platformy Azure. W przypadku dużych wdrożeń można dodać dodatkowy, oddzielny serwer docelowy dla powrotu po awarii.
**Serwery VMware** | Maszyny wirtualne VMware są hostowane na lokalnych serwerach vSphere ESXi. Zalecamy serwer vCenter do zarządzania hostami. | Podczas wdrażania usługi Site Recovery serwery VMware są dodawanye do magazynu usług odzyskiwania.
**Zreplikowane maszyny** | Usługa mobilności jest zainstalowana na każdej replikowanej maszynie wirtualnej VMware. | Zaleca się zezwolenie na automatyczną instalację z serwera przetwarzania. Alternatywnie można zainstalować usługę ręcznie lub użyć metody wdrażania automatycznego, takiej jak program Menedżer konfiguracji.

**Architektura VMware–Azure**

![Składniki](./media/vmware-azure-architecture/arch-enhanced.png)


## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny Wirtualnej rozpoczyna się replikacja początkowa do magazynu platformy Azure przy użyciu określonych zasad replikacji. Pamiętaj o następujących kwestiach:
    - W przypadku maszyn wirtualnych VMware replikacja jest na poziomie bloku, prawie ciągła, przy użyciu agenta usługi mobilności uruchomionego na maszynie wirtualnej.
    - Stosowane są wszystkie ustawienia zasad replikacji:
        - **próg RPO**. To ustawienie nie ma wpływu na replikację. Pomaga w monitorowaniu. Zdarzenie jest wywoływane i opcjonalnie wysłany e-mail, jeśli bieżący cel ochrony danych przekracza limit progu, który określisz.
        - **Retencja punktów odzyskiwania**. To ustawienie określa, jak daleko wstecz w czasie, który ma się udać, gdy wystąpi zakłócenie. Maksymalne przechowywanie w magazynie w wersji premium wynosi 24 godziny. W przypadku standardowego przechowywania jest to 72 godziny. 
        - **Migawki spójne z aplikacjami**. Migawka spójna z aplikacjami może być pobierana co 1 do 12 godzin, w zależności od potrzeb aplikacji. Migawki są standardowe migawki obiektów blob platformy Azure. Agent mobilności uruchomiony na maszynie wirtualnej żąda migawki usługi VSS zgodnie z tym ustawieniem i zakładki, że punkt w czasie jako spójny punkt aplikacji w strumieniu replikacji.

2. Ruch jest replikowane do publicznych punktów końcowych magazynu platformy Azure przez Internet. Alternatywnie można użyć usługi Azure ExpressRoute z [komunikacją równorzędową firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Replikowanie ruchu za pośrednictwem wirtualnej sieci prywatnej lokacji (VPN) z lokacji lokalnej na platformę Azure nie jest obsługiwane.
3. Operacja replikacji początkowej zapewnia, że całe dane na komputerze w czasie włączania replikacji są wysyłane do platformy Azure. Po zakończeniu replikacji początkowej rozpoczyna się replikacja zmian różnicowych na platformie Azure. Śledzone zmiany dla komputera są wysyłane do serwera przetwarzania.
4. Komunikacja odbywa się w następujący sposób:

    - Maszyny wirtualne komunikują się z lokalnym serwerem konfiguracji na porcie HTTPS 443 przychodzącym w celu zarządzania replikacją.
    - Serwer konfiguracji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 wychodzącego.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 przychodzącym. Ten port można modyfikować.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do magazynu platformy Azure za pomocą portu 443 wychodzącego.
5. Dzienniki danych replikacji najpierw lądują na koncie magazynu pamięci podręcznej na platformie Azure. Te dzienniki są przetwarzane, a dane są przechowywane w dysku zarządzanym platformy Azure (nazywanym dyskiem źródłowym asr). Punkty odzyskiwania są tworzone na tym dysku.

**Proces replikacji VMware do platformy Azure**

![Proces replikacji](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Proces ponownej synchronizowania

1. Czasami podczas replikacji początkowej lub podczas przesyłania zmian różnicowych mogą wystąpić problemy z łącznością sieciową między komputerem źródłowym do przetwarzania serwera lub między serwerem przetwarzania na platformę Azure. Każda z nich może prowadzić do awarii transferu danych do platformy Azure na chwilę.
2. Aby uniknąć problemów z integralnością danych i zminimalizować koszty transferu danych, usługa Site Recovery oznacza maszynę do ponownej synchronizacji.
3. Maszyna może być również oznaczona do ponownej synchronizowania w sytuacjach takich jak obserwowanie, aby zachować spójność między maszyną źródłową a danymi przechowywanymi na platformie Azure
    - Jeśli maszyna ulega wymusić wyłączenie
    - Jeśli komputer ulega zmianom konfiguracyjnym, takim jak zmiana rozmiaru dysku (modyfikowanie rozmiaru dysku z 2 TB do 4 TB)
4. Ponowna synchronizacja wysyła tylko dane delta do platformy Azure. Transfer danych między środowiskiem lokalnym a platformą Azure przez zminimalizowanie przez przetwarzanie sum kontrolnych danych między komputerem źródłowym a danymi przechowywanymi na platformie Azure.
5. Domyślnie ponowna synchronizacja jest zaplanowana do automatycznego uruchamiania poza godzinami pracy biura. Jeśli nie chcesz czekać na domyślną ponowną synchronizację poza godzinami, możesz ponownie zsynchronizować maszynę wirtualną ręcznie. Aby to zrobić, przejdź do witryny Azure portal, wybierz maszynę wirtualną > **Resynchronize**.
6. Jeśli domyślna ponowna synchronizacja kończy się niepowodzeniem poza godzinami pracy i wymagana jest ręczna interwencja, na określonym komputerze w witrynie Azure portal jest generowany błąd. Można rozwiązać ten błąd i ręcznie wyzwolić ponowną synchronizację.
7. Po zakończeniu ponownej synchronizacji zostanie wznowiona replikacja zmian różnicowych.

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji i uruchomieniu ćwiczenia odzyskiwania po awarii (test pracy awaryjnej), aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, można uruchomić pracę awaryjną i powrót po awarii, zgodnie z potrzebami.

1. Uruchom niepowodzenie dla jednego komputera lub utworzyć plany odzyskiwania w trybie fail over wielu maszyn wirtualnych w tym samym czasie. Zalety planu odzyskiwania, a nie pracy awaryjnej pojedynczej maszyny obejmują:
    - Możesz modelować zależności aplikacji, dołączając wszystkie maszyny wirtualne w aplikacji w jednym planie odzyskiwania.
    - Można dodawać skrypty, runbooki platformy Azure i wstrzymywać akcje ręczne.
2. Po wyzwoleniu początkowej pracy awaryjnej, należy zatwierdzić go, aby rozpocząć dostęp do obciążenia z maszyny Wirtualnej platformy Azure.
3. Gdy podstawowa lokacja lokalna jest ponownie dostępna, można przygotować się do powrotu po awarii. Aby przywrócić po awarii, należy skonfigurować infrastrukturę powrotu po awarii, w tym:

    * **Tymczasowy serwer procesów na platformie Azure:** Aby odzyskać po awarii z platformy Azure, skonfigurować maszynę wirtualną platformy Azure, aby działać jako serwer procesów do obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
    * **Połączenie sieci VPN:** Aby przywrócić po awarii, potrzebujesz połączenia sieci VPN (lub usługi ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
    * **Oddzielny główny serwer docelowy:** Domyślnie główny serwer docelowy zainstalowany z serwerem konfiguracji na lokalnej maszynie wirtualnej VMware obsługuje powrót zwrotny po awarii. Jeśli konieczne jest przywrócenie dużych ilości ruchu, należy skonfigurować w tym celu oddzielny lokalny główny serwer docelowy.
    * **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. Ta zasada jest tworzona automatycznie podczas tworzenia zasad replikacji z lokalnego na platformę Azure.
4. Po składniki są w miejscu, powrót po awarii występuje w trzech akcjach:

    - Etap 1: Ponowne przetwarzanie maszyn wirtualnych platformy Azure, tak aby były replikowane z platformy Azure z powrotem do lokalnych maszyn wirtualnych VMware.
    -  Etap 2: Uruchom pracy awaryjnej do lokacji lokalnej.
    - Etap 3: Po zakończeniu pracy obciążeń replikacja jest ponownie konfigurowana dla lokalnych maszyn wirtualnych.
    
 
**Powrót po awarii VMware z platformy Azure**

![Powrót po awarii](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Następne kroki

Wykonaj [ten samouczek,](vmware-azure-tutorial.md) aby włączyć replikację VMware na platformie Azure.

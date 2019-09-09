---
title: Architektura odzyskiwania po awarii oprogramowania VMware na platformę Azure w Azure Site Recovery
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych VMware na platformie Azure przy użyciu Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 7c21b8d7a4a2723ddf10c4ac88f8b1ce4a5d6b47
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814591"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architektura odzyskiwania po awarii oprogramowania VMware na platformę Azure

W tym artykule opisano architekturę i procesy używane podczas wdrażania replikacji odzyskiwania po awarii, trybu failover i odzyskiwania maszyn wirtualnych VMware między lokalną lokacją programu VMware i platformą Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Składniki architektury

Poniższa tabela i grafika zawierają ogólny widok składników służących do odzyskiwania po awarii programu VMware do platformy Azure.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konto usługi Azure Storage na potrzeby pamięci podręcznej, dysku zarządzanego i sieci platformy Azure. | Zreplikowane dane z lokalnych maszyn wirtualnych są przechowywane w usłudze Azure Storage. Maszyny wirtualne platformy Azure są tworzone przy użyciu replikowanych danych podczas pracy w trybie failover z poziomu lokalnego na platformę Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Komputer serwera konfiguracji** | Pojedyncza maszyna lokalna. Firma Microsoft zaleca, aby uruchomić ją jako maszynę wirtualną VMware, którą można wdrożyć przy użyciu pobranego szablonu OVF.<br/><br/> Na maszynie działa wszystkie lokalne składniki Site Recovery, takie jak serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy. | **Serwer konfiguracji**: Koordynuje komunikację między środowiskiem lokalnym i platformą Azure oraz zarządza replikacją danych.<br/><br/> **Serwer przetwarzania**: Domyślnie instalowany na serwerze konfiguracji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła je do usługi Azure Storage. Serwer przetwarzania instaluje także usługę Azure Site Recovery Mobility Service na maszynach wirtualnych, które będą replikowane, i przeprowadza automatycznie odnajdywanie lokalnych maszyn wirtualnych. Wraz z rozwojem wdrożenia można dodać dodatkowe, oddzielne serwery przetwarzania do obsługi większych woluminów ruchu związanego z replikacją.<br/><br/> **Główny serwer docelowy**: Domyślnie instalowany na serwerze konfiguracji. Obsługuje dane replikacji podczas powrotu po awarii z platformy Azure. W przypadku dużych wdrożeń można dodać dodatkowy, oddzielny główny serwer docelowy na potrzeby powrotu po awarii.
**Serwery VMware** | Maszyny wirtualne VMware są hostowane na lokalnych serwerach vSphere ESXi. Zalecamy serwer vCenter do zarządzania hostami. | Podczas wdrażania Site Recovery należy dodać serwery VMware do magazynu Recovery Services.
**Zreplikowane maszyny** | Usługa mobilności jest instalowana na wszystkich zreplikowanych maszynach wirtualnych VMware. | Zalecamy, aby zezwalać na automatyczną instalację z serwera przetwarzania. Alternatywnie możesz zainstalować usługę ręcznie lub użyć zautomatyzowanej metody wdrażania, takiej jak System Center Configuration Manager.

**Architektura VMware–Azure**

![Składniki](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny wirtualnej rozpocznie się replikacja początkowa do usługi Azure Storage przy użyciu określonych zasad replikacji. Należy pamiętać o następujących kwestiach:
    - W przypadku maszyn wirtualnych VMware replikacja jest na poziomie bloku, blisko ciągłego, przy użyciu agenta usługi mobilności uruchomionego na maszynie wirtualnej.
    - Zostaną zastosowane wszystkie ustawienia zasad replikacji:
        - **Próg punktu odzyskiwania**. To ustawienie nie ma wpływu na replikację. Ułatwia to monitorowanie. Zostanie zgłoszone zdarzenie i opcjonalnie zostanie wysłana wiadomość e-mail, jeśli bieżący cel punktu odzyskiwania przekroczy określony limit progu.
        - **Przechowywanie punktu odzyskiwania**. To ustawienie określa, jak daleko wstecz w czasie ma nastąpić przejście, gdy wystąpi zakłócenie. Maksymalne przechowywanie w magazynie w warstwie Premium wynosi 24 godziny. W przypadku magazynu w warstwie Standardowa jest 72 godzin. 
        - **Migawki spójne z aplikacjami**. Migawkę spójną na poziomie aplikacji można wykonać co 1 do 12 godzin, w zależności od potrzeb aplikacji. Migawki to standardowe migawki obiektów blob platformy Azure. Agent mobilności działający na maszynie wirtualnej żąda migawki VSS zgodnie z tym ustawieniem i zakładki wskazujące punkt w czasie jako punkt spójny aplikacji w strumieniu replikacji.

2. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Alternatywnie możesz użyć usługi Azure ExpressRoute z usługą [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Replikowanie ruchu za pośrednictwem wirtualnej sieci prywatnej (VPN) typu lokacja-lokacja z lokacji lokalnej do platformy Azure nie jest obsługiwane.
3. Po zakończeniu replikacji początkowej zostanie rozpoczęta replikacja zmian różnicowych na platformie Azure. Śledzone zmiany dla maszyny są wysyłane do serwera przetwarzania.
4. Komunikacja odbywa się w następujący sposób:

    - Maszyny wirtualne komunikują się z lokalnym serwerem konfiguracji na porcie HTTPS 443 ruchu przychodzącego na potrzeby zarządzania replikacją.
    - Serwer konfiguracji organizuje replikację za pomocą usługi Azure over port HTTPS 443.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 w ruchu przychodzącym. Ten port może być modyfikowany.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage przez port 443 wychodzące.
5. Dane replikacji są najpierw przechowywane na koncie magazynu pamięci podręcznej na platformie Azure. Te dzienniki są przetwarzane, a dane są przechowywane na dysku zarządzanym platformy Azure (wywoływana jako dysk inicjujący ASR). Na tym dysku są tworzone punkty odzyskiwania.




**Proces replikacji z programu VMware do platformy Azure**

![Proces replikacji](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji i uruchomieniu funkcji drążenia odzyskiwania po awarii (test pracy w trybie failover), aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, możesz uruchomić tryb failover i powrót po awarii, jak to konieczne.

1. Uruchamianie nie powiedzie się dla jednej maszyny lub można utworzyć plany odzyskiwania w celu jednoczesnego przełączenia wielu maszyn wirtualnych w tryb failover. Zaletą planu odzyskiwania zamiast pojedynczej pracy awaryjnej komputera jest:
    - Zależności aplikacji można modelować, dołączając wszystkie maszyny wirtualne w aplikacji w ramach jednego planu odzyskiwania.
    - Możesz dodawać skrypty, elementy Runbook platformy Azure i wstrzymywać je w ramach akcji ręcznych.
2. Po wyzwoleniu początkowej pracy w trybie failover należy zatwierdzić, aby rozpocząć uzyskiwanie dostępu do obciążenia z maszyny wirtualnej platformy Azure.
3. Po ponownym udostępnieniu podstawowej lokacji lokalnej można przygotować się do powrotu po awarii. W celu powrotu po awarii należy skonfigurować infrastrukturę powrotu po awarii, w tym:

    * **Tymczasowy serwer przetwarzania na platformie Azure**: Aby powrócić po awarii z platformy Azure, należy skonfigurować maszynę wirtualną platformy Azure do działania jako serwer przetwarzania w celu obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
    * **Połączenie sieci VPN**: Aby powrócić po awarii, musisz mieć połączenie sieci VPN (lub ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
    * **Oddzielny główny serwer docelowy**: Domyślnie główny serwer docelowy, który został zainstalowany z serwerem konfiguracji na lokalnej maszynie wirtualnej VMware, obsługuje powrót po awarii. Jeśli zachodzi potrzeba odtworzenia dużych ilości ruchu, należy skonfigurować w tym celu oddzielny lokalny główny serwer docelowy.
    * **Zasady powrotu po awarii**: Aby przeprowadzić replikację z powrotem do lokacji lokalnej, potrzebne są zasady powrotu po awarii. Te zasady są tworzone automatycznie podczas tworzenia zasad replikacji z lokalnego na platformę Azure.
4. Gdy składniki są stosowane, powrót po awarii odbywa się w trzech akcjach:

    - Etap 1: Ponownie Włącz ochronę maszyn wirtualnych platformy Azure, aby replikować je z platformy Azure z powrotem do lokalnych maszyn wirtualnych programu VMware.
    -  Etap 2: Uruchom tryb failover w lokacji lokalnej.
    - Etap 3: Gdy obciążenia nie powiodło się, należy ponownie włączyć replikację lokalnych maszyn wirtualnych.
    
 
**Powrót po awarii programu VMware z platformy Azure**

![Powrót po awarii](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [tym samouczkiem](vmware-azure-tutorial.md) , aby włączyć replikację programu VMware do platformy Azure.

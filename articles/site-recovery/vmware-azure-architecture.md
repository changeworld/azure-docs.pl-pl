---
title: Architektura replikacji platformy Azure w usłudze Azure Site Recovery VMware – | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas replikowania lokalnych maszyn wirtualnych z programu VMware na platformę Azure za pomocą usługi Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 48adf61dc0f1796b820e1e14ca509d4618c6256b
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920571"
---
# <a name="vmware-to-azure-replication-architecture"></a>Architektura Azure replikacji VMware –

W tym artykule opisano architekturę i procesów związanych z replikacja, tryb failover i odzyskiwanie maszyn wirtualnych VMware (VM) między lokalną lokacją programu VMware i platformą Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Składniki architektury

Poniższej tabeli i grafika przedstawia ogólny widok składniki używane na potrzeby replikacji oprogramowania VMware na platformę Azure.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konta usługi Azure Storage i sieci platformy Azure. | Replikowane dane z lokalnych maszyn wirtualnych są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone z replikowanych danych, po uruchomieniu trybu failover ze środowiska lokalnego do platformy Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Konfiguracja komputera serwera** | Pojedynczy na komputerze lokalnym. Firma Microsoft zaleca, uruchom go jako maszyny Wirtualnej VMware, które mogą być wdrażane z pobranego szablonu OVF.<br/><br/> Komputer uruchamia wszystkie składniki usługi Site Recovery w środowisku lokalnym, które obejmują serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy. | **Serwer konfiguracji**: służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.<br/><br/> **Serwer przetwarzania**: instalowany domyślnie na serwerze konfiguracji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła je do usługi Azure Storage. Serwer przetwarzania instaluje także usługę mobilności usługi Azure Site Recovery na maszynach wirtualnych, którą chcesz replikować, i przeprowadza automatyczne odnajdywanie maszyn lokalnych. Wraz z rozwojem wdrożenia, możesz dodać dodatkowe, oddzielny proces serwerów w celu obsługi większych ilości ruchu związanego z replikacją.<br/><br/> **Główny serwer docelowy**: instalowany domyślnie na serwerze konfiguracji. Obsługuje on replikację danych podczas powrotu po awarii z platformy Azure. W przypadku dużych wdrożeń możesz dodać dodatkowe, oddzielny główny serwer docelowy do powrotu po awarii.
**Serwery VMware** | Maszyny wirtualne VMware są hostowane na serwerach lokalnych vSphere ESXi. Firma Microsoft zaleca serwera vCenter do zarządzania hostami. | Podczas wdrażania usługi Site Recovery serwery VMware są dodawane do magazynu usługi Recovery Services.
**Zreplikowane maszyny** | Usługa mobilności jest zainstalowana na każdej maszynie Wirtualnej VMware są replikowane. | Zaleca się, że zezwalają na automatycznej instalacji z serwera przetwarzania. Można też ręcznie zainstalować usługę, lub użyć metody wdrażania automatycznego, takie jak System Center Configuration Manager.

**Architektura VMware–Azure**

![Składniki](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Kroki konfiguracji

Czynności tworzenia oprogramowania VMware do migracji lub odzyskiwania po awarii platformy Azure są następujące:

1. **Konfigurowanie składników platformy Azure**. Potrzebujesz konta platformy Azure przy użyciu odpowiednich uprawnień, konto magazynu platformy Azure, siecią wirtualną platformy Azure i magazyn usługi Recovery Services. [Dowiedz się więcej](tutorial-prepare-azure.md).
2. **Konfigurowanie lokalnych**. Obejmują one skonfigurowanie konta na serwerze VMware tak, aby Usługa Site Recovery może automatycznie odnajdować maszyny wirtualne do replikacji, konfigurując konto, który może służyć do instalowania składnika usługi Mobility na maszynach wirtualnych, którą chcesz replikować i weryfikowanie aby serwery VMware i maszyny wirtualne spełniają wymagania wstępne. Możesz również opcjonalnie można przygotować połączyć się z tych maszyn wirtualnych platformy Azure po włączeniu trybu failover. Usługa Site Recovery replikuje dane maszyny Wirtualnej do konta usługi Azure storage i tworzy maszyny wirtualne platformy Azure przy użyciu danych, po uruchomieniu trybu failover na platformie Azure. [Dowiedz się więcej](vmware-azure-tutorial-prepare-on-premises.md).
3. **Konfigurowanie replikacji**. Możesz wybrać miejsce ma zostać zreplikowana na. Możesz skonfigurować środowiska źródłowego replikacji poprzez skonfigurowanie pojedynczego lokalnego wdrożenia oprogramowania VMware maszyny Wirtualnej (serwer konfiguracji), w którym są uruchamiane, wszystkie lokalne składniki usługi Site Recovery, które należy. Po zakończeniu instalacji należy zarejestrować komputera serwera konfiguracji w magazynie usługi Recovery Services. Następnie wybierz ustawienia docelowej. [Dowiedz się więcej](vmware-azure-tutorial.md).
4. **Tworzenie zasad replikacji**. Należy utworzyć zasady replikacji, określający, jak replikacja powinno mieć miejsce. 
    - **Próg celu punktu odzyskiwania**: Monitorowanie to ustawienie stanów, jeśli replikacja nie występuje w określonym czasie, alert (i opcjonalnie wiadomość e-mail) jest wystawiony. Na przykład jeśli próg celu punktu odzyskiwania jest ustawiona na 30 minut, a problem uniemożliwia replikację z występuje w ciągu 30 minut, zdarzenie jest generowane. To ustawienie nie ma wpływu na replikację. Replikacja jest ciągły i punkty odzyskiwania są tworzone co kilka minut
    - **Przechowywanie**: punkt odzyskiwania przechowywania Określa, ile punktów odzyskiwania powinny być umieszczone na platformie Azure. Można określić wartość z zakresu od 0 do 24 godzin dla usługi premium storage lub w górę do 72 godzin do magazynu w warstwie standardowa. Możesz przełączać awaryjnie do najnowszego punktu odzyskiwania lub punkt przechowywanych Jeśli ustawisz wartość większą od zera. Po okno przechowywania punkty odzyskiwania zostaną usunięte.
    - **Migawki spójne na poziomie awarii**: Domyślnie usługa Site Recovery tworzy migawki spójne na poziomie awarii i tworzy punkty odzyskiwania z nich co kilka minut. Punkt odzyskiwania spójny na wszystkich składników powiązane ze sobą dane w przypadku zapisu rzędu spójny awarii, znajdowały się w chwili tworzenia punktu odzyskiwania. Aby dowiedzieć się więcej, Wyobraź sobie stan danych na dysku twardym komputera po awarii zasilania lub podobne zdarzenie. Punktu odzyskiwania spójnego na poziomie awarii jest zwykle wystarczające, jeśli aplikacja jest przeznaczona do sprawności po awarii bez żadnych niespójności w danych.
    - **Migawki spójne z aplikacji**: Jeśli ta wartość nie jest zero, usługi mobilności uruchomioną na maszynie Wirtualnej spróbuje go wygenerować migawki spójne z systemu plików i punktów odzyskiwania. Pierwszy migawki po zakończeniu replikacji początkowej. Następnie migawki są pobierane z częstotliwością, które określisz. Punkt odzyskiwania jest spójne z aplikacjami oprócz kolejności zapisu wykonać wszystkie operacje spójne, uruchomionej aplikacji i opróżnienia buforów, ich na dysku (przełączanie w stan spoczynku aplikacji). Punkty odzyskiwania spójne na poziomie aplikacji są zalecane w przypadku aplikacji bazy danych, takich jak SQL, Oracle i Exchange. Jeśli migawki spójne na poziomie awarii jest wystarczająca, tę wartość można ustawić na wartość 0.  
    - **Spójność wielu maszyn wirtualnych**: można opcjonalnie utworzyć grupę replikacji. Następnie po włączeniu replikacji, możesz zbierać maszyn wirtualnych w tej grupie. Maszyn wirtualnych w replikacji grupy replikacji ze sobą, a udostępnione punkty odzyskiwania spójne na poziomie awarii i spójny na poziomie aplikacji, gdy przełączone w tryb failover. Tego opcji należy używać ostrożnie, ponieważ może mieć wpływ na wydajność obciążenia jako migawki potrzebne do zebrania na wielu komputerach. Jeśli maszyny wirtualne działają te same obciążenia i muszą być zgodne, a maszyny wirtualne mają podobne churns. Możesz dodać maksymalnie 8 maszyn wirtualnych do grupy. 
5. **Włączanie replikacji maszyny Wirtualnej**. Na koniec należy włączyć replikację dla lokalnych maszyn wirtualnych VMware. Jeśli konto, aby zainstalować usługę mobilności i określić, czy usługa Site Recovery należy wykonać instalację wypychaną usługi Mobility zostanie zainstalowana na każdej maszynie Wirtualnej, dla którego należy włączyć replikację. [Dowiedz się więcej](vmware-azure-tutorial.md#enable-replication). Jeśli utworzono grupy replikacji w celu zachowania spójności wielu maszyn wirtualnych, maszyny wirtualne zostaną dodane do tej grupy.
6. **Testowanie trybu failover**. Po skonfigurowaniu jest wszystko można wykonać test trybu failover do sprawdzenia, czy maszyny wirtualne Failover na platformę Azure zgodnie z oczekiwaniami. [Dowiedz się więcej](tutorial-dr-drill-azure.md).
7. **Tryb failover**. Tylko w przypadku migrowania maszyn wirtualnych na platformie Azure — możesz uruchomić pełną trybu failover, aby to zrobić. Jeśli podczas konfigurowania odzyskiwania po awarii, możesz uruchomić pełną przejścia w tryb failover na potrzeby. Podczas odzyskiwania pełnego systemu po włączeniu trybu failover na platformie Azure, mogą nie do lokacji lokalnej jako lub jest ona dostępna. [Dowiedz się więcej](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny Wirtualnej, rozpocznie replikowanie zgodnie z zasadami replikacji. 
2. Ruch są replikowane do usługi Azure storage publicznych punktów końcowych za pośrednictwem Internetu. Alternatywnie można użyć usługi Azure ExpressRoute za pomocą [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replikowanie ruchu za pośrednictwem lokacja lokacja wirtualnej sieci prywatnej (VPN) z lokacji lokalnej do platformy Azure nie jest obsługiwane.
3. Początkowa kopia danych maszyny Wirtualnej są replikowane do usługi Azure storage.
4. Po zakończeniu replikacji początkowej rozpoczyna się replikacja zmian różnicowych do platformy Azure. Śledzone zmiany dla maszyny są przechowywane w pliku hrl.
5. Komunikacja odbywa się w następujący sposób:

    - Maszyny wirtualne komunikować się z lokalnym serwerem konfiguracji na porcie HTTPS 443 dla ruchu przychodzącego na potrzeby zarządzania replikacją.
    - Serwer konfiguracji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 dla ruchu przychodzącego. Ten port może być modyfikowany.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je szyfruje i wysyła je do usługi Azure storage za pośrednictwem portu 443 wychodzących.


**Z programu VMware na proces replikacji platformy Azure**

![Proces replikacji](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji i uruchamianie próbnego odzyskiwania po awarii (Testowanie trybu failover), aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, możesz uruchomić tryb failover i powrotu po awarii na potrzeby.

1. Uruchamiania kończą się niepowodzeniem dla pojedynczego komputera lub utworzyć odzyskiwania plany, aby przełączyć wiele maszyn wirtualnych w tym samym czasie. Zaletą planu odzyskiwania zamiast jednej maszyny w tryb failover obejmują:
    - Model z zależności aplikacji, umieszczając wszystkie maszyny wirtualne w aplikacji w pojedynczym planie odzyskiwania.
    - Można dodać skrypty, elementy runbook usługi Azure i wstrzymania dla działania ręczne.
2. Po wyzwoleniem początkowej trybu failover, należy zatwierdzić je, aby można było rozpocząć uzyskiwanie dostępu do obciążenia maszyny wirtualnej platformy Azure.
3. Gdy w lokacji głównej w środowisku lokalnym będzie znowu dostępna, można przygotować się do powrotu po awarii. Aby powrót po awarii, musisz skonfigurować infrastrukturę powrotu po awarii, w tym:

    * **Tymczasowy serwer przetwarzania na platformie Azure**: Aby zakończyć się niepowodzeniem, powrót po awarii z platformy Azure, należy skonfigurować Maszynę wirtualną platformy Azure do działania jako serwer przetwarzania do obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
    * **Połączenie sieci VPN**: do powrotu po awarii, potrzebujesz połączenia sieci VPN (lub usługi ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
    * **Osobny główny serwer docelowy**: Domyślnie główny serwer docelowy, który został zainstalowany na serwerze konfiguracji w lokalnych zasobów programu VMware maszyny Wirtualnej obsługuje powrót po awarii. Jeśli musisz zakończyć się niepowodzeniem ponownie dużych ilości ruchu należy skonfigurować oddzielny lokalny główny serwer docelowy do tego celu.
    * **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. Zasada została automatycznie utworzona podczas tworzenia zasad replikacji ze środowiska lokalnego do platformy Azure.
4. Po składniki znajdują się w miejscu, powrót po awarii odbywa się w trzy czynności:

    - Etap 1: Ponowne włączanie ochrony maszyn wirtualnych platformy Azure, dzięki czemu mają być replikowane na platformie Azure do lokalnych maszyn wirtualnych VMware.
    -  Etap 2: Uruchamianie trybu failover do lokacji lokalnej.
    - Etap 3: Po obciążeń przywrócono po awarii, możesz ponownie włączyć replikację dla maszyn wirtualnych w środowisku lokalnym.
    
 
**Podczas powrotu po awarii programu VMware na platformie Azure**

![Powrót po awarii](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z [w tym samouczku](vmware-azure-tutorial.md) umożliwiające VMware do platformy Azure replikacji.

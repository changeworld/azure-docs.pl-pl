---
title: VMware do platformy Azure replikacji architektury w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie składników i architektury używane podczas replikowania lokalnych maszyn wirtualnych VMware do platformy Azure z usługą Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 61c283c178936c98a9a18509c1b46035e48f8f24
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285274"
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware do platformy Azure replikacji architektury

W tym artykule opisano architektura i procesy stosowane podczas replikacji, pracy awaryjnej i odzyskiwania maszyn wirtualnych VMware (VM) między lokacji oprogramowania VMware lokalną i platformą Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Składniki architektury

Następujące tabeli i grafika Podaj ogólny widok składniki używane do replikacji maszyn wirtualnych VMware do platformy Azure.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konta magazynu Azure i sieć platformy Azure. | Replikowane dane z lokalnych maszyn wirtualnych są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone przy użyciu zreplikowanych danych podczas uruchamiania trybu failover z lokalnej na platformie Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Komputer serwera konfiguracji** | Pojedynczy lokalnego komputera. Firma Microsoft zaleca, uruchom go jako maszyna wirtualna oprogramowania VMware, które mogą być wdrażane z pobranego szablonu OVF.<br/><br/> Komputer uruchamia wszystkie lokalne usługi Site Recovery składniki, które obejmują konfiguracji serwera, serwer przetwarzania i główny serwer docelowy. | **Serwer konfiguracji**: współrzędne komunikacji między lokalną i platformą Azure i zarządza replikacji danych.<br/><br/> **Serwer przetwarzania**: instalowana domyślnie na serwerze konfiguracji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła go do magazynu Azure. Serwer przetwarzania instaluje usługi mobilności Azure Site Recovery na maszynach wirtualnych, którą chcesz replikować, i przeprowadza automatyczne odnajdywanie komputerów lokalnych. Wraz z rozwojem wdrożenia, możesz dodać dodatkowe, oddzielne procesu serwerów do obsługi większych ilości ruchu replikacji.<br/><br/> **Główny serwer docelowy**: instalowana domyślnie na serwerze konfiguracji. Obsługuje on replikację danych podczas powrotu po awarii z platformy Azure. W przypadku dużych wdrożeń można dodać dodatkowe, oddzielne główny serwer docelowy do powrotu po awarii.
**Serwery VMware** | Maszyny wirtualne VMware znajdują się na serwerach ESXi vSphere lokalnymi. Firma Microsoft zaleca serwer vCenter do zarządzania hostami. | Podczas wdrażania usługi Site Recovery należy dodać serwery VMware do magazynu usług odzyskiwania.
**Zreplikowane maszyny** | Usługa mobilności jest zainstalowana na każdej maszynie Wirtualnej VMware, które są replikowane. | Firma Microsoft zaleca, Zezwalaj na automatyczną instalację z serwera przetwarzania. Można też ręcznie zainstalować usługę lub użyj metody wdrażania automatycznego, takie jak System Center Configuration Manager.

**Architektura VMware–Azure**

![Składniki](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Kroki konfiguracji

Czynności tworzenia VMware do odzyskiwania danych platformy Azure lub migracji są następujące:

1. **Konfigurowanie składników Azure**. Potrzebujesz konta platformy Azure z odpowiednich uprawnień, konto magazynu platformy Azure, sieć wirtualną platformy Azure i magazynu usług odzyskiwania. [Dowiedz się więcej](tutorial-prepare-azure.md).
2. **Konfigurowanie lokalnego**. Obejmują one skonfigurowanie konta na serwerze VMware, aby Usługa Site Recovery może automatycznie odnajdować maszyny wirtualne do replikacji, konfigurowanie konta, które mogą służyć do instalowania składnika usługi Mobility na maszynach wirtualnych, którą chcesz replikować i weryfikowania tego serwerów VMware i maszyny wirtualne są zgodne z wymagań wstępnych. Opcjonalnie można przygotować nawiązać połączenia z tych maszyn wirtualnych Azure po pracy awaryjnej. Usługa Site Recovery replikuje dane dotyczące maszyny Wirtualnej do konta magazynu platformy Azure i tworzy maszynach wirtualnych platformy Azure przy użyciu danych po uruchomieniu trybu failover na platformie Azure. [Dowiedz się więcej](vmware-azure-tutorial-prepare-on-premises.md).
3. **Konfigurowanie replikacji**. Wybierz gdzie chcesz replikować do. Należy skonfigurować środowisko replikacji źródło przez skonfigurowanie VMware pojedynczym lokalnym maszyny Wirtualnej (serwer konfiguracji), który uruchamia wszystkie lokalną potrzebne składniki usługi Site Recovery. Po zakończeniu instalacji należy zarejestrować w magazynie usług odzyskiwania z maszyną serwera konfiguracji. Następnie możesz wybrać ustawienia obiektu docelowego. [Dowiedz się więcej](vmware-azure-tutorial.md).
4. **Utwórz zasady replikacji**. Możesz utworzyć zasady replikacji, który określa, jak powinno się zdarzyć replikacji. 
    - **Próg RPO**: Monitorowanie to ustawienie stanów Jeśli replikacja nie występuje w określonym czasie, alert (i opcjonalnie wiadomość e-mail) wystawienia. Na przykład jeśli próg RPO jest ustawiany na 30 minut, a problem uniemożliwia replikację zapobiec przez 30 minut, zdarzenie zostanie wygenerowane. To ustawienie nie ma wpływu na replikację. Replikacja jest ciągły i punkty odzyskiwania są tworzone co kilka minut
    - **Przechowywania**: punkt odzyskiwania przechowywania Określa, jak długo punkty odzyskiwania powinny być przechowywane na platformie Azure. Określ wartość z zakresu od 0 do 24 godzin magazyn w warstwie premium lub wyższej do 72 godzin dla magazynu w warstwie standardowa. Możesz w trybie Failover do najnowszego punktu odzyskiwania lub do punktu przechowywanych Jeśli ustawisz wartość większą niż zero. Po okna przechowywania punkty odzyskiwania zostaną usunięte.
    - **Migawki spójne z awarii**: domyślnie przyjmuje migawki spójne z awarii usługi Site Recovery i tworzy punkty odzyskiwania z nimi co kilka minut. Punkt odzyskiwania jest spójne, jeśli wszystkie składniki powiązanych danych są spójne zapisu kolejności awarii, jakie były natychmiast utworzenia punktu odzyskiwania. Aby lepiej zrozumieć, załóżmy stan danych na dysku twardym komputera po awarii zasilania lub podobne zdarzenie. Punkt odzyskiwania spójna w razie awarii jest zwykle wystarczające, jeśli aplikacja została zbudowana Aby dokonać odzyskiwania po awarii bez niespójności danych.
    - **Migawki spójne z aplikacjami**: Jeśli ta wartość nie jest zero, usługa mobilności uruchomionych na Maszynie wirtualnej prób wygenerowania migawki spójne z systemu plików i punktów odzyskiwania. Pierwszy migawki po zakończeniu replikacji początkowej. Następnie migawki są wykonywane przez użytkownika częstotliwością. Punkt odzyskiwania jest spójne z aplikacjami oprócz kolejności zapisu spójne, uruchomionej aplikacji należy wykonać wszystkie operacje i opróżnienia buforów ich na dysku (przełączany w stan spoczynku aplikacji). Zaleca się punktów odzyskiwania zapewniających spójność aplikacji dla aplikacji baz danych, takich jak SQL, Oracle i Exchange. Jeśli migawka spójna w razie awarii jest wystarczająca, tę wartość można ustawić na wartość 0.  
    - **Spójność wielu maszyn wirtualnych**: można opcjonalnie utworzyć grupy replikacji. Następnie po włączeniu replikacji można zbierać maszyn wirtualnych w tej grupie. Maszyn wirtualnych w replikacji grupy replikacji ze sobą i udostępnione punkty odzyskiwania spójna w razie awarii i spójności aplikacji podczas przejścia w tryb failover. Tej opcji należy używać ostrożnie, ponieważ może wpłynąć na wydajność obciążenia jako migawki potrzebne do zebrania na wielu komputerach. Jeśli na maszynach wirtualnych Uruchom te same obciążenia i muszą być zgodne, a maszyny wirtualne mają podobne maselnic. Można dodać maksymalnie 8 maszyn wirtualnych do grupy. 
5. **Włącz replikację maszyny Wirtualnej**. Na koniec replikację można włączyć dla lokalnych maszyn wirtualnych VMware. Jeśli utworzone konto używane do zainstalowania usługi mobilności i określić, czy usługa Site Recovery należy wykonać instalacji wypychanej usługi mobilności zostanie zainstalowana na każdej maszynie Wirtualnej, dla którego należy włączyć funkcję replikacji. [Dowiedz się więcej](vmware-azure-tutorial.md#enable-replication). Jeśli po utworzeniu grupy replikacji dla wielu maszyn wirtualnych, maszyny wirtualne można dodać do tej grupy.
6. **Testowanie trybu failover**. Po wszystko zostało skonfigurowane, można wykonać test trybu failover, aby sprawdzić, czy maszyny wirtualne awaryjnie do platformy Azure zgodnie z oczekiwaniami. [Dowiedz się więcej](tutorial-dr-drill-azure.md).
7. **Tryb failover**. Jest tylko migrowania maszyn wirtualnych na platformie Azure — możesz uruchomić pełne pracy awaryjnej w tym celu. Jeśli podczas konfigurowania odzyskiwania po awarii, możesz uruchomić pełne trybu failover na potrzeby. Podczas odzyskiwania pełnego systemu po w tryb failover na platformie Azure, mogą nie do lokacji lokalnej jako lub jest ona dostępna. [Dowiedz się więcej](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny Wirtualnej, rozpocznie replikowanie zgodnie z zasadami replikacji. 
2. Ruch są replikowane do magazynu Azure publiczne punkty końcowe w Internecie. Alternatywnie można użyć usługi Azure ExpressRoute z [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replikację ruchu za pośrednictwem witryny do witryny wirtualnej sieci prywatnej (VPN) z lokacji lokalnej do platformy Azure nie jest obsługiwana.
3. Początkowej kopii danych maszyny Wirtualnej są replikowane do magazynu Azure.
4. Po zakończeniu replikacji początkowej, rozpoczyna replikację zmian różnicowych na platformie Azure. Śledzone zmiany dla maszyny są przechowywane w pliku hrl.
5. Komunikacja odbywa się w następujący sposób:

    - Maszyny wirtualne komunikować się z lokalnego serwera konfiguracji na porcie 443 protokołu HTTPS dla ruchu przychodzącego dla zarządzania replikacji.
    - Serwer konfiguracji organizuje replikację za pomocą usługi Azure za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego.
    - Maszyny wirtualne wysyłanie danych replikacji do serwera przetwarzania (uruchomiony na komputerze z serwerem konfiguracji) na porcie HTTPS 9443 ruchu przychodzącego. Port ten może być modyfikowany.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje i je szyfruje i wysyła go do magazynu Azure za pośrednictwem portu 443 ruchu wychodzącego.


**VMware do procesu replikacji Azure**

![Proces replikacji](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji i uruchom wyszczególniania odzyskiwania po awarii (test trybu failover) do sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami, można uruchomić trybu failover i powrotu po awarii na potrzeby.

1. Uruchom błędów dla pojedynczego komputera lub utworzyć plany do trybu failover wiele maszyn wirtualnych w tym samym czasie odzyskiwania. Zaletą planu odzyskiwania zamiast trybu failover z jednym komputerze obejmują:
    - Aby model zależności aplikacji, należy również wszystkich maszyn wirtualnych w aplikacji w pojedynczym planie odzyskiwania.
    - Można dodać skryptów, elementy runbook Azure i wstrzymać działań ręcznych.
2. Po wyzwalają początkowej trybu failover, Zatwierdź go uruchomić uzyskiwania dostępu do obciążenia z maszyny Wirtualnej platformy Azure.
3. Gdy lokacji głównej lokalnymi jest ponownie dostępny, można przygotować powrotu po awarii. Aby wykonaj powrót po awarii, należy skonfigurować infrastrukturę powrotu po awarii, w tym:

    * **Tymczasowy serwer przetwarzania na platformie Azure**: aby awarii z platformy Azure, należy skonfigurować maszyny Wirtualnej platformy Azure do działania jako serwera przetwarzania do obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
    * **Połączenia sieci VPN**: aby wykonaj powrót po awarii, należy połączenie sieci VPN (lub ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
    * **Oddzielne główny serwer docelowy**: Domyślnie główny serwer docelowy, który został zainstalowany na serwerze konfiguracji w programie VMware lokalnej maszyny Wirtualnej obsługuje powrót po awarii. Niepowodzenie wstecz dużych ilości ruchu należy skonfigurować oddzielny lokalny główny serwer docelowy do tego celu.
    * **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. Zasada została utworzona automatycznie, podczas tworzenia zasady replikacji z lokalnych do platformy Azure.
4. Po składniki zostały spełnione, powrotu po awarii w trzy czynności:

    - Etap 1: Włącz ponownie ochronę maszyn wirtualnych platformy Azure, dzięki czemu mają być replikowane z platformy Azure do maszyn wirtualnych VMware lokalnymi.
    -  Etap 2: Uruchomić trybu failover do lokacji lokalnej.
    - Etap 3: Po obciążeń powiodły się z powrotem, należy ponownie włączyć replikację dla maszyn wirtualnych lokalnie.
    
 
**VMware powrotu po awarii z platformy Azure**

![Powrót po awarii](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z [w tym samouczku](vmware-azure-tutorial.md) umożliwiające VMware do platformy Azure replikacji.

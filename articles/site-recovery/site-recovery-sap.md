---
title: Konfigurowanie odzyskiwania po awarii oprogramowania SAP NetWeaver z użyciem Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla wdrożeń aplikacji SAP NetWeaver przy użyciu Azure Site Recovery.
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: carmonm
ms.openlocfilehash: 3ae9a92a27da1b736bf9db6dff88660f7d40143b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934444"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowego wdrożenia aplikacji SAP NetWeaver

Najnowsze wdrożenia SAP o dużych rozmiarach i średnim rozmiarze używają pewnego rodzaju rozwiązania do odzyskiwania po awarii. Znaczenie niezawodnych i weryfikowalneych rozwiązań odzyskiwania po awarii zostało zwiększone, ponieważ bardziej podstawowe procesy biznesowe są przenoszone do aplikacji takich jak SAP. Azure Site Recovery został przetestowany i zintegrowany z aplikacjami SAP. Site Recovery przekracza możliwości większości lokalnych rozwiązań odzyskiwania po awarii oraz niższy całkowity koszt posiadania (TCO) niż konkurencyjne rozwiązania.

Za pomocą Site Recovery można:
* **Włącz ochronę oprogramowania SAP NetWeaver i innych niż NetWeaver aplikacji produkcyjnych, które są uruchamiane lokalnie** przez replikowanie składników do platformy Azure.
* **Włącz ochronę oprogramowania SAP NetWeaver i innych niż NetWeaver aplikacji produkcyjnych uruchamianych na platformie Azure** przez replikowanie składników do innego centrum danych platformy Azure.
* **Uprość migrację do chmury** , korzystając z Site Recovery do migrowania wdrożenia SAP do platformy Azure.
* **Upraszczanie uaktualnień projektów SAP, testowanie i Tworzenie prototypów** przez utworzenie klonu produkcyjnego na żądanie na potrzeby testowania aplikacji SAP.

W tym artykule opisano sposób ochrony wdrożeń aplikacji SAP NetWeaver przy użyciu [Azure Site Recovery](site-recovery-overview.md). W tym artykule opisano najlepsze rozwiązania dotyczące ochrony wielowarstwowego wdrożenia oprogramowania SAP NetWeaver na platformie Azure przez replikację do innego centrum danych platformy Azure przy użyciu Site Recovery. Opisano w nim obsługiwane scenariusze i konfiguracje oraz sposób wykonywania testu pracy w trybie failover (przechodzenia do odzyskiwania po awarii) i rzeczywistej pracy awaryjnej.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że wiesz, jak wykonać następujące zadania:

* [Replikowanie maszyny wirtualnej na platformę Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Projektowanie sieci odzyskiwania](site-recovery-azure-to-azure-networking-guidance.md)
* [Wykonaj test pracy w trybie failover na platformie Azure](azure-to-azure-walkthrough-test-failover.md)
* [Przełączenie w tryb failover na platformie Azure](site-recovery-failover.md)
* [Replikowanie kontrolera domeny](site-recovery-active-directory.md)
* [Replikowanie programu SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Za pomocą Site Recovery można zaimplementować rozwiązanie odzyskiwania po awarii w następujących scenariuszach:
* Systemy SAP działające w jednym centrum danych platformy Azure, które są replikowane do innego centrum danych platformy Azure (odzyskiwanie po awarii z platformy Azure do platformy Azure). Aby uzyskać więcej informacji, zobacz [Architektura replikacji z platformy Azure do platformy Azure](https://aka.ms/asr-a2a-architecture).
* Systemy SAP działające na serwerach VMware (lub fizycznych) w środowisku lokalnym, które są replikowane do lokacji odzyskiwania po awarii w centrum danych platformy Azure (odzyskiwanie po awarii oprogramowania VMware na platformę Azure). W tym scenariuszu wymagane są pewne dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [Architektura replikacji VMware-to-Azure](https://aka.ms/asr-v2a-architecture).
* Systemy SAP uruchomione w lokalnej funkcji Hyper-V, które są replikowane do lokacji odzyskiwania po awarii w centrum danych platformy Azure (funkcja Hyper-V do odzyskiwania po awarii na platformie Azure). W tym scenariuszu wymagane są pewne dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [Architektura replikacji funkcji Hyper-V do platformy Azure](https://aka.ms/asr-h2a-architecture).

W tym artykule opisano scenariusz odzyskiwania po awarii z **platformy Azure do platformy Azure** w celu zademonstrowania możliwości odzyskiwania po awarii SAP Site Recovery. Ponieważ replikacja Site Recovery nie jest specyficzna dla aplikacji, proces, który został opisany, ma zastosowanie również do innych scenariuszy.

### <a name="required-foundation-services"></a>Wymagane usługi Foundation
W scenariuszu omawianym w tym artykule wdrożono następujące usługi programu Foundation:
* Azure ExpressRoute lub Azure VPN Gateway
* Co najmniej jeden Active Directory kontroler domeny i serwer DNS działający na platformie Azure

Zalecamy ustanowienie tej infrastruktury przed wdrożeniem Site Recovery.

## <a name="reference-sap-application-deployment"></a>Odwołanie do wdrożenia aplikacji SAP

W tej architekturze referencyjnej przedstawiono Uruchamianie programu SAP NetWeaver w środowisku systemu Windows na platformie Azure o wysokiej dostępności.  Ta architektura jest wdrażana z określonymi rozmiarami maszyn wirtualnych, które można zmienić w celu uwzględnienia potrzeb organizacji.

![Diagram typowego wzorca wdrażania SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Zagadnienia dotyczące odzyskiwania po awarii

Do odzyskiwania po awarii (DR) musi być możliwe do trybu failover do regionu pomocniczego. Każda warstwa używa innej strategii w celu zapewnienia ochrony odzyskiwania po awarii (DR, disaster recovery).

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Maszyny wirtualne z uruchomioną pulą dyspozytorów sieci Web SAP 
Składnik Web Dispatcher służy jako moduł równoważenia obciążenia dla ruchu SAP na serwery aplikacji SAP. Aby zapewnić wysoką dostępność składnika dyspozytora sieci Web, Azure Load Balancer jest używany do implementowania konfiguracji równoległego dyspozytora sieci Web w konfiguracji okrężnej dla dystrybucji ruchu HTTP (S) wśród dostępnych elementów docelowych w puli modułu równoważenia obciążenia. Zostanie ona zreplikowana przy użyciu Azure Site Recovery (ASR), a skrypty automatyzacji zostaną użyte do skonfigurowania modułu równoważenia obciążenia w regionie odzyskiwania po awarii. 

#### <a name="vms-running-application-servers-pool"></a>Maszyny wirtualne z uruchomioną pulą serwerów aplikacji
Aby zarządzać grupami logowania dla serwerów aplikacji ABAP, transakcji SMLG jest używany. Używa ona funkcji w ramach serwera wiadomości centralnej usług równoważenia obciążenia do dystrybucji obciążeń między puli serwerów aplikacji SAP SAPGUIs i RFC ruchu. Ta replikacja zostanie zreplikowana przy użyciu Azure Site Recovery 

#### <a name="vms-running-sap-central-services-cluster"></a>Maszyny wirtualne z systemem SAP Central Services — klaster
Ta architektura referencyjna uruchamia centralnej usług na maszynach wirtualnych w warstwie aplikacji. Central Services jest potencjalnym pojedynczym punktem awarii (SPOF), po wdrożeniu na jednej maszynie Wirtualnej — typowe wdrożenie w przypadku wysokiej dostępności nie jest wymagane.<br>

Aby zaimplementować rozwiązanie wysokiej dostępności, można użyć udostępnionego klastra dysków lub klastra udziałów plików. Aby skonfigurować maszyny wirtualne dla udostępnionego klastra dysków, należy użyć klastra trybu failover systemu Windows Server. Monitor chmury jest zalecany jako monitor kworum. 
 > [!NOTE]
 > Azure Site Recovery nie replikuje monitora chmury, dlatego zaleca się wdrożenie monitora chmury w regionie odzyskiwania po awarii.

Do obsługi środowiska klastra trybu failover, [oprogramowanie SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) wykonuje funkcję woluminu udostępnionego klastra, replikując niezależne dyski należące do węzłów klastra. Azure nie obsługuje natywnie udostępnionych dysków i dlatego wymaga udostępniane przez oprogramowanie SIOS rozwiązania. 

Innym sposobem obsługi klastrowania jest zaimplementowanie klastra udziałów plików. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ostatnio zmodyfikowane wzorca wdrażania centralnego usługi dostęp do katalogów globalnych /sapmnt za pośrednictwem ścieżki UNC. Jednak nadal zaleca się upewnienie się, że udział UNC/sapmnt ma wysoką dostępność. Można to zrobić na wystąpieniu usług centralnych przy użyciu klastra trybu failover systemu Windows Server z serwerem plików skalowalnym w poziomie (SOFS) i funkcją Bezpośrednie miejsca do magazynowania (S2D) w systemie Windows Server 2016. 
 > [!NOTE]
 > Obecnie Azure Site Recovery obsługuje tylko replikację punktu w poziomie awarii na maszynach wirtualnych przy użyciu funkcji bezpośrednie miejsca do magazynowania i pasywnego węzła oprogramowanie SIOS DataKeeper


## <a name="disaster-recovery-considerations"></a>Zagadnienia dotyczące odzyskiwania po awarii

Za pomocą Azure Site Recovery można organizować tryb failover całego wdrożenia SAP w regionach platformy Azure.
Poniżej przedstawiono procedurę konfigurowania odzyskiwania po awarii 

1. Replikowanie maszyn wirtualnych 
2. Projektowanie sieci odzyskiwania
3.  Replikowanie kontrolera domeny
4.  Replikuj warstwę bazową danych 
5.  Przeprowadzanie testu trybu failover 
6.  Przejdź w tryb failover 

Poniżej znajduje się zalecenie dotyczące odzyskiwania po awarii dla każdej warstwy używanej w tym przykładzie. 

 **Warstwy SAP** | **Zalecenie**
 --- | ---
**Pula dyspozytorów sieci Web SAP** |  Replikacja przy użyciu usługi Site Recovery 
**Pula serwerów aplikacji SAP** |  Replikacja przy użyciu usługi Site Recovery 
**Klaster usług SAP Central** |  Replikacja przy użyciu usługi Site Recovery 
**Maszyny wirtualne usługi Active Directory** |  Replikacja usługi Active Directory 
**Serwery baz danych SQL** |  Zawsze włączona replikacja SQL

## <a name="replicate-virtual-machines"></a>Replikowanie maszyn wirtualnych

Aby rozpocząć replikowanie wszystkich maszyn wirtualnych aplikacji SAP do centrum danych odzyskiwania po awarii platformy Azure, postępuj zgodnie ze wskazówkami zawartymi w artykule [replikowanie maszyny wirtualnej na platformę Azure](azure-to-azure-walkthrough-enable-replication.md).


* Aby uzyskać wskazówki dotyczące ochrony Active Directory i systemu DNS, zobacz [ochrona Active Directory i dokumentu DNS](site-recovery-active-directory.md) .

* Wskazówki dotyczące ochrony warstwy bazy danych działającej w programie SQL Server można znaleźć w temacie [ochrona SQL Server](site-recovery-sql.md) dokumentu.

## <a name="networking-configuration"></a>Konfiguracja sieci

Jeśli używasz statycznego adresu IP, możesz określić adres IP, który ma być pobierany przez maszynę wirtualną. Aby ustawić adres IP, przejdź do pozycji **obliczenia i ustawienia sieciowe** > **karcie interfejsu sieciowego**.

![Zrzut ekranu pokazujący sposób ustawiania prywatnego adresu IP w okienku karty sieciowej Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowanie różnych warstw w aplikacji wielowarstwowej podczas pracy w trybie failover. Sekwencjonowanie pomaga zachować spójność aplikacji. Po utworzeniu planu odzyskiwania dla wielowarstwowej aplikacji sieci Web wykonaj kroki opisane w temacie [Tworzenie planu odzyskiwania przy użyciu Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover

1.  Utwórz plan odzyskiwania, dodając serwer aplikacji, Dyspozytor sieci Web i maszyny wirtualne usług SAP Central Services.
2.  Kliknij przycisk "Dostosuj", aby zgrupować maszyny wirtualne. Domyślnie wszystkie maszyny wirtualne są częścią grupy 1.



### <a name="add-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania
Aby aplikacje działały prawidłowo, może być konieczne wykonanie pewnych operacji na maszynach wirtualnych platformy Azure po przejściu do trybu failover lub w trakcie testu pracy w trybie failover. Można zautomatyzować niektóre operacje wykonywane po przejściu w tryb failover. Można na przykład zaktualizować wpis DNS i zmienić powiązania i połączenia przez dodanie odpowiednich skryptów do planu odzyskiwania.


Najczęściej używane skrypty Azure Site Recovery można wdrożyć na koncie usługi Automation, klikając przycisk "wdróż na platformie Azure" poniżej. W przypadku korzystania z dowolnego opublikowanego skryptu upewnij się, że zostały podane wskazówki w skrypcie.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Dodaj skrypt przed akcją w grupie dostępności programu SQL do trybu failover. Użyj skryptu "ASR-SQL-FailoverAG" opublikowanego w przykładowych skryptach. Upewnij się, że postępuj zgodnie ze wskazówkami w skrypcie i wprowadź odpowiednio wymagane zmiany w skrypcie.
2. Dodaj skrypt akcji po stronie w celu dołączenia modułu równoważenia obciążenia do maszyn wirtualnych w trybie failover w warstwie sieci Web (Grupa 1). Użyj skryptu "ASR-AddSingleLoadBalancer" opublikowanego w przykładowych skryptach. Upewnij się, że postępuj zgodnie ze wskazówkami w skrypcie i wprowadź odpowiednio wymagane zmiany w skrypcie.

![Plan odzyskiwania SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1.  W Azure Portal wybierz swój magazyn Recovery Services.
2.  Wybierz plan odzyskiwania, który został utworzony dla aplikacji SAP.
3.  Wybierz pozycję **Test pracy w trybie failover**.
4.  Aby rozpocząć proces testowego przełączania do trybu failover, wybierz punkt odzyskiwania i sieć wirtualną platformy Azure.
5.  Gdy środowisko pomocnicze jest w pełni, wykonaj walidację.
6.  Po zakończeniu walidacji w celu oczyszczenia środowiska trybu failover wybierz pozycję **Oczyść test pracy w trybie failover**.

Aby uzyskać więcej informacji, zobacz [test pracy w trybie failover na platformie Azure w Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1.  W Azure Portal wybierz swój magazyn Recovery Services.
2.  Wybierz plan odzyskiwania, który został utworzony dla aplikacji SAP.
3.  Wybierz pozycję **Tryb failover**.
4.  Aby rozpocząć proces trybu failover, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [tryb failover w Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat tworzenia rozwiązania do odzyskiwania po awarii dla wdrożeń SAP NetWeaver przy użyciu Site Recovery, zobacz dokument [dotyczący pobierania danych z systemu SAP NetWeaver: kompilowanie rozwiązania do odzyskiwania po awarii z Azure Site Recovery](https://aka.ms/asr_sap). Oficjalny dokument omawia zalecenia dotyczące różnych architektur SAP, zawiera listę obsługiwanych aplikacji i typów maszyn wirtualnych dla oprogramowania SAP na platformie Azure oraz opisuje opcje planu testowania dla rozwiązania do odzyskiwania po awarii.
* Dowiedz się więcej o [replikacji innych obciążeń](site-recovery-workload.md) przy użyciu Site Recovery.

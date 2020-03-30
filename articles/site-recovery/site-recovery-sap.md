---
title: Konfigurowanie odzyskiwania po awarii SAP NetWeaver za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla sap NetWeaver za pomocą usługi Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190793"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Konfigurowanie odzyskiwania po awarii dla wdrożenia wielowarstwowej aplikacji SAP NetWeaver

Większość dużych i średnich wdrożeń SAP korzysta z jakiejś formy rozwiązania do odzyskiwania po awarii. Znaczenie niezawodnych i sprawdzalnych rozwiązań do odzyskiwania po awarii wzrosło w miarę przenoszenia większej liczby podstawowych procesów biznesowych do aplikacji takich jak SAP. Usługa Azure Site Recovery została przetestowana i zintegrowana z aplikacjami SAP. Odzyskiwanie lokacji przekracza możliwości większości lokalnych rozwiązań do odzyskiwania po awarii i przy niższym całkowitym koszcie posiadania niż konkurencyjne rozwiązania.

Dzięki uczynek Site Recovery możesz:
* Włącz ochronę aplikacji produkcyjnych SAP NetWeaver i innych niż NetWeaver, które są uruchamiane lokalnie przez replikowanie składników na platformę Azure.
* Włącz ochronę aplikacji produkcyjnych SAP NetWeaver i innych niż NetWeaver, które są uruchamiane na platformie Azure przez replikowanie składników do innego centrum danych platformy Azure.
* Skorzystaj z uproszczenia migracji do chmury, używając usługi Site Recovery do migrowania wdrożenia SAP do platformy Azure.
* Uprość uaktualnianie, testowanie i prototypowanie projektów SAP, tworząc klon produkcyjny na żądanie do testowania aplikacji SAP.

Wdrożenia aplikacji SAP NetWeaver można chronić za pomocą [usługi Azure Site Recovery.](site-recovery-overview.md) W tym artykule opisano najlepsze rozwiązania dotyczące ochrony wdrożenia usługi SAP NetWeaver na platformie Azure podczas replikowania do innego centrum danych platformy Azure przy użyciu usługi Site Recovery. W tym artykule opisano obsługiwane scenariusze i konfiguracje oraz sposób testowania pracy awaryjnej (ćwiczenia odzyskiwania po awarii) i rzeczywiste tryb failover.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że wiesz, jak wykonać następujące zadania:

* [Replikowanie maszyny wirtualnej na platformę Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Projektowanie sieci odzyskiwania](site-recovery-azure-to-azure-networking-guidance.md)
* [Testowanie pracy awaryjnej na platformie Azure](azure-to-azure-walkthrough-test-failover.md)
* [Wykonaj przejście awaryjne na platformie Azure](site-recovery-failover.md)
* [Replikowanie kontrolera domeny](site-recovery-active-directory.md)
* [Replikowanie wystąpienia programu SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Za pomocą usługi Site Recovery można zaimplementować rozwiązanie odzyskiwania po awarii w następujących scenariuszach:
* Masz systemy SAP uruchomione w jednym centrum danych platformy Azure i replikujesz je do innego centrum danych platformy Azure (odzyskiwanie po awarii platformy Azure na platformie Azure). 
   Aby uzyskać więcej informacji, zobacz [architektura replikacji platformy Azure do platformy Azure.](https://aka.ms/asr-a2a-architecture)
* Masz systemy SAP uruchomione na serwerach VMware (lub fizycznych) lokalnie. Replikujesz również systemy SAP do lokacji odzyskiwania po awarii w centrum danych platformy Azure (odzyskiwanie po awarii VMware-to-Azure). 
   Ten scenariusz wymaga niektórych dodatkowych składników. Aby uzyskać więcej informacji, zobacz [architekturę replikacji VMware-to-Azure](https://aka.ms/asr-v2a-architecture).
* Masz systemy SAP działające w środowisku lokalnym funkcji Hyper-V. Replikujesz również systemy SAP do lokacji odzyskiwania po awarii w centrum danych platformy Azure (odzyskiwanie po awarii funkcji Hyper-V-to-Azure).
   Ten scenariusz wymaga niektórych dodatkowych składników. Aby uzyskać więcej informacji, zobacz [Architektura replikacji funkcji Hyper-V-to-Azure](https://aka.ms/asr-h2a-architecture).

W tym artykule używamy scenariusza odzyskiwania po awarii **platformy Azure do platformy Azure.** Scenariusz pokazuje możliwości odzyskiwania po awarii SAP usługi site recovery. Ponieważ replikacja usługi Site Recovery nie jest specyficzna dla aplikacji, opisany proces ma również zastosowanie do innych scenariuszy.

### <a name="required-foundation-services"></a>Wymagane usługi fundacji
W scenariuszu omówimy w tym artykule wdrożone są następujące usługi fundacji:
* Usługa Azure ExpressRoute lub brama sieci VPN platformy Azure
* Co najmniej jeden kontroler domeny usługi Azure Active Directory i serwer DNS działający na platformie Azure

Zaleca się ustanowienie tej infrastruktury przed wdrożeniem usługi Site Recovery.

## <a name="reference-sap-application-deployment"></a>Odwołanie do wdrażania aplikacji SAP

Ta architektura referencyjna jest uruchomiona SAP NetWeaver w środowisku systemu Windows na platformie Azure z wysoką dostępnością. Ta architektura jest wdrażana z określonymi rozmiarami maszyn wirtualnych (VM), które można zmienić, aby dostosować się do potrzeb organizacji.

![Diagram typowego wzorca wdrażania SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Zagadnienia dotyczące odzyskiwania po awarii

W przypadku odzyskiwania po awarii musi być w stanie awaryjnie do regionu pomocniczego. Każda warstwa używa innej strategii, aby zapewnić ochronę odzyskiwania po awarii.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>Maszyny wirtualne z uruchomionymi pulami SAP Web Dispatcher

Składnik Web Dispatcher działa jako moduł równoważenia obciążenia dla ruchu SAP między serwerami aplikacji SAP. Aby osiągnąć wysoką dostępność dla składnika Web Dispatcher, moduł Równoważenia obciążenia platformy Azure implementuje równoległą konfigurację dyspozytora sieci Web. Web Dispatcher używa konfiguracji okrężkowej dla dystrybucji ruchu HTTP(S) wśród dostępnych dyspozytorów sieci Web w puli modułów równowagi.

#### <a name="vms-running-application-servers-pools"></a>Serwery maszyn wirtualnych z uruchomionymi serwerami aplikacji
Transakcja SMLG zarządza grupami logowania dla serwerów aplikacji ABAP. Używa funkcji równoważenia obciążenia w serwerze komunikatów usług centralnych do dystrybucji obciążenia między pulami serwerów aplikacji SAP dla ruchu SAPGUIs i RFC. Zarządzanie można replikować za pomocą funkcji Odzysk witryny.

#### <a name="vms-running-sap-central-services-clusters"></a>Maszyny wirtualne z klastrami SAP Central Services
Ta architektura referencyjna uruchamia usługi centralne na maszynach wirtualnych w warstwie aplikacji. Usługi centralne to potencjalny pojedynczy punkt awarii, gdy w jednej maszynie wirtualnej. Typowe wdrożenie i wysoka dostępność nie są wymagania.

Aby zaimplementować rozwiązanie o wysokiej dostępności, można użyć klastra dysku udostępnionego lub klastra udziału plików. Aby skonfigurować maszyny wirtualne dla udostępnionego klastra dysków, użyj klastra trybu failover systemu Windows Server. Zaleca się używanie monitora chmury jako monitora kworum.

 > [!NOTE]
 > Ponieważ usługa Site Recovery nie replikuje monitora chmury, zaleca się wdrożenie monitora chmury w regionie odzyskiwania po awarii.

Aby obsługiwać środowisko klastra trybu failover, [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) wykonuje funkcję współdzielonego woluminu klastra. W tej funkcji klaster SIOS DataKeeper replikuje niezależne dyski należące do węzłów klastra. Ponieważ platforma Azure nie obsługuje natywnie dysków udostępnionych, wymaga rozwiązań dostarczonych przez SIOS.

Klastrowanie można również obsługiwać, implementując klaster udziału plików. [Firma SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) niedawno zmodyfikowała wzorzec wdrażania usług centralnych, aby uzyskać dostęp do katalogów globalnych /sapmnt za pośrednictwem ścieżki UNC. Nadal zalecamy upewnienie się, że udział /sapmnt UNC jest wysoce dostępny. Możesz sprawdzić swoje wystąpienie usług centralnych. Użyj klastra trybu failover systemu Windows Server z programem Skaluj w poziomie serwera plików (SOFS) i funkcji Bezpośrednie miejsca do magazynowania (S2D) w systemie Windows Server 2016.

 > [!NOTE]
 > Usługa Site Recovery obsługuje obecnie tylko replikację punktową spójną z awariami maszyn wirtualnych, które używają bezpośrednich miejsc do magazynowania i pasywnego węzła SIOS Datakeeper.


## <a name="more-disaster-recovery-considerations"></a>Więcej zagadnień dotyczących odzyskiwania po awarii

Usługa Site Recovery służy do organizowania pracy awaryjnej pełnego wdrożenia SAP w regionach platformy Azure.
Poniżej przedstawiono kroki konfigurowania odzyskiwania po awarii:

1. Replikowanie maszyn wirtualnych
1. Projektowanie sieci odzyskiwania
1. Replikowanie kontrolera domeny
1. Replikowanie warstwy bazowej danych
1. Przeprowadzanie testu trybu failover
1. Przejdź w tryb failover

Poniżej przedstawiono zalecenie odzyskiwania po awarii każdej warstwy używane w tym przykładzie.

 **Warstwy SAP** | **Zalecenie**
 --- | ---
**Pula dyspozytora sieci Web SAP** |  Replikowanie przy użyciu odzyskiwania witryny 
**Pula serwerów aplikacji SAP** |  Replikowanie przy użyciu odzyskiwania witryny 
**Klaster sap central services** |  Replikowanie przy użyciu odzyskiwania witryny 
**Maszyny wirtualne usługi Active Directory** |  Korzystanie z replikacji usługi Active Directory 
**Serwery usługi SQL Database** |  Korzystanie z replikacji programu SQL Server Always On

## <a name="replicate-virtual-machines"></a>Replikowanie maszyn wirtualnych

Aby rozpocząć replikowanie wszystkich maszyn wirtualnych aplikacji SAP do centrum danych odzyskiwania po awarii platformy Azure, postępuj zgodnie ze wskazówkami zawartymi w [aplikacji Replikacja maszyny wirtualnej na platformie Azure.](azure-to-azure-walkthrough-enable-replication.md)

* Aby uzyskać wskazówki dotyczące ochrony usługi Active Directory i systemu DNS, dowiedz się, [jak chronić usługę Active Directory i system DNS](site-recovery-active-directory.md).

* Aby uzyskać wskazówki dotyczące ochrony warstwy bazy danych uruchomionej w programie SQL Server, dowiedz się, [jak chronić program SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Konfiguracja sieci

Jeśli używasz statycznego adresu IP, można określić adres IP, który ma podjąć maszyna wirtualna. Aby ustawić adres IP, przejdź do **pozycji Ustawienia obliczeniowe i ustawienia** > sieci**Karta interfejsu sieciowego**.

![Zrzut ekranu przedstawiający sposób ustawiania prywatnego adresu IP w okienku interfejsu sieci odzyskiwania lokacji](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania

Plan odzyskiwania obsługuje sekwencjonowanie różnych warstw w aplikacji wielowarstwowej podczas pracy awaryjnej. Sekwencjonowanie pomaga zachować spójność aplikacji. Podczas tworzenia planu odzyskiwania dla wielowarstwowej aplikacji sieci web wykonaj kroki opisane w [aplikacji Tworzenie planu odzyskiwania przy użyciu funkcji Odzysk witryny](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover

1. Utwórz plan odzyskiwania, dodając maszyny wirtualne z serwerem aplikacji, dyspozytorem sieci web i usługami SAP Central.
1. Wybierz **pozycję Dostosuj,** aby zgrupować maszyny wirtualne. Domyślnie wszystkie maszyny wirtualne są częścią grupy 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania
Aby aplikacje działały poprawnie, może być konieczne wykonać niektóre operacje na maszynach wirtualnych platformy Azure. Wykonaj te operacje po pracy awaryjnej lub podczas pracy awaryjnej testu. Można również zautomatyzować niektóre operacje po pracy awaryjnej. Na przykład zaktualizuj wpis DNS i zmień powiązania i połączenia, dodając odpowiednie skrypty do planu odzyskiwania.

Najczęściej używane skrypty usługi Site Recovery można wdrożyć na koncie usługi Azure Automation, wybierając **pozycję Wdrażanie na platformie Azure.** Korzystając z dowolnego opublikowanego skryptu, postępuj zgodnie ze wskazówkami w skrypcie.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Dodaj skrypt przeddziałakiem do grupy 1, aby przejrzyć grupę dostępności programu SQL Server w stan faila. Użyj skryptu ASR-SQL-FailoverAG opublikowanego w przykładowych skryptach. Postępuj zgodnie ze wskazówkami w skrypcie i należy odpowiednio wprowadzić wymagane zmiany w skrypcie.
1. Dodaj skrypt po akcji, aby dołączyć moduł równoważenia obciążenia do maszyn wirtualnych awaryjnych warstwy sieci Web (grupa 1). Użyj skryptu ASR-AddSingleLoadBalancer opublikowanego w przykładowych skryptach. Postępuj zgodnie ze wskazówkami w skrypcie i w razie potrzeby wprowadzać wymagane zmiany w skrypcie.

![Plan odzyskiwania SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. W witrynie Azure portal wybierz magazyn usług odzyskiwania.
1. Wybierz plan odzyskiwania utworzony dla aplikacji SAP.
1. Wybierz pozycję **Test pracy w trybie failover**.
1. Aby rozpocząć proces pracy awaryjnej testu, wybierz punkt odzyskiwania i sieć wirtualną platformy Azure.
1. Gdy środowisko pomocnicze jest w górę, wykonać sprawdzanie poprawności.
1. Po zakończeniu sprawdzania poprawności wyczyść środowisko pracy awaryjnej, wybierając opcję **Oczyszczanie testu w pełni czasu awaryjnego.**

Aby uzyskać więcej informacji, zobacz [Testowanie pracy awaryjnej na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1. W witrynie Azure portal wybierz magazyn usług odzyskiwania.
1. Wybierz plan odzyskiwania utworzony dla aplikacji SAP.
1. Wybierz pozycję **Tryb failover**.
1. Aby rozpocząć proces pracy awaryjnej, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [Odzyskiwanie awaryjne w odzyskiwaniu witryny](site-recovery-failover.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o tworzeniu rozwiązania do odzyskiwania po awarii dla wdrożeń SAP NetWeaver przy użyciu funkcji Odzysk lokacji. Zobacz oficjalny dokument do pobrania [SAP NetWeaver: Tworzenie rozwiązania do odzyskiwania po awarii za pomocą usługi Site Recovery](https://aka.ms/asr_sap). W opracowali białe księgi omówiono zalecenia dotyczące różnych architektur SAP. Możesz zobaczyć obsługiwane aplikacje i typy maszyn wirtualnych dla sap na platformie Azure. Istnieją również opcje planowania testowania rozwiązania odzyskiwania po awarii.
* Dowiedz się więcej o [replikowaniu innych obciążeń](site-recovery-workload.md) przy użyciu funkcji Odzysk witryny.

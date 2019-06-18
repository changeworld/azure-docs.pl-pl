---
title: Konfigurowanie odzyskiwania po awarii dla wdrożenia aplikacji SAP NetWeaver obejmujące wiele warstw przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla wdrożenia aplikacji SAP NetWeaver przy użyciu usługi Azure Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 0848738b71a605d8baf049847daa3ae2428a7abe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65793669"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Konfigurowanie odzyskiwania po awarii dla wdrożenia obejmujące wiele warstw aplikacji SAP NetWeaver

Większości wdrożeń SAP duży rozmiar i średniego użycia pewnego rodzaju rozwiązania odzyskiwania po awarii. Znaczenie rozwiązania odzyskiwania po awarii niezawodne i testowaniu został zwiększony, ponieważ więcej procesów biznesowych core są przenoszone do aplikacji, takich jak SAP. Usługa Azure Site Recovery została przetestowana i zintegrowana z aplikacjami SAP. Usługa Site Recovery przekracza możliwości większości rozwiązań odzyskiwania po awarii w środowisku lokalnym oraz na niższy całkowity koszt posiadania (TCO) niż konkurencyjne rozwiązania.

Usługa Site Recovery można wykonywać następujące czynności:
* **Włącz ochronę aplikacji produkcyjnych oprogramowania SAP NetWeaver i innych NetWeaver działających w środowisku lokalnym** replikując składniki do platformy Azure.
* **Włącz ochronę aplikacji produkcyjnych oprogramowania SAP NetWeaver i innych NetWeaver działających na platformie Azure** replikując składniki do innego centrum danych platformy Azure.
* **Skorzystaj z uproszczenia migracji do chmury** przy użyciu usługi Site Recovery do migrowania wdrożenia SAP do platformy Azure.
* **Uprość SAP uaktualnienia projektu, testowanie i tworzenie prototypów** , tworząc produkcyjnym sklonować wybieraniem numeru na potrzeby testowania aplikacji SAP.

W tym artykule opisano, jak zapewnić ochronę wdrożeń aplikacji SAP NetWeaver przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md). Artykuł opisuje najlepsze wskazówki dotyczące ochrony trójwarstwowej wdrożenie oprogramowania SAP NetWeaver na platformie Azure przez replikację do innego centrum danych platformy Azure przy użyciu usługi Site Recovery. Opisano w nim obsługiwane scenariusze i konfiguracjach oraz jak przeprowadzić test pracy awaryjnej (odzyskiwanie po awarii) i rzeczywisty tryb failover.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że wiesz, jak wykonać następujące zadania:

* [Replikacja maszyny wirtualnej do platformy Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Projektowanie sieć odzyskiwania](site-recovery-azure-to-azure-networking-guidance.md)
* [Wykonaj test trybu failover na platformie Azure](azure-to-azure-walkthrough-test-failover.md)
* [Czy tryb failover na platformie Azure](site-recovery-failover.md)
* [Replikowanie kontrolera domeny](site-recovery-active-directory.md)
* [Replikowanie programu SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Usługa Site Recovery można użyć do wdrożenia rozwiązanie odzyskiwania po awarii w następujących scenariuszach:
* Systemów SAP działające w jednym centrum danych platformy Azure, które są replikowane do innego centrum danych platformy Azure (odzyskiwanie po awarii Azure – Azure). Aby uzyskać więcej informacji, zobacz [architektura replikacji Azure – Azure](https://aka.ms/asr-a2a-architecture).
* SAP komputerów z systemami w oprogramowaniu firmy VMware (lub fizyczny) serwerów lokalnych tego replikacja do lokacji odzyskiwania po awarii w centrum danych platformy Azure (odzyskiwanie po awarii programu VMware do platformy Azure). Ten scenariusz wymaga pewnych dodatkowych składników. Aby uzyskać więcej informacji, zobacz [architektura replikacji VMware – Azure](https://aka.ms/asr-v2a-architecture).
* Systemów SAP uruchomiona na funkcji Hyper-V w środowisku lokalnym, które są replikowane do lokacji odzyskiwania po awarii w centrum danych platformy Azure (odzyskiwanie po awarii z funkcji Hyper-V — na platformie Azure). Ten scenariusz wymaga pewnych dodatkowych składników. Aby uzyskać więcej informacji, zobacz [architektury replikacji funkcji Hyper V – Azure](https://aka.ms/asr-h2a-architecture).

W tym artykule używamy **Azure – Azure** scenariusza odzyskiwania po awarii, aby zademonstrować funkcje odzyskiwania po awarii SAP Site Recovery. Ponieważ replikacji usługi Site Recovery nie jest specyficzna dla aplikacji, proces, który jest opisany powinien również zastosowanie do innych scenariuszy.

### <a name="required-foundation-services"></a>Wymagane foundation services
W tym scenariuszu, które omówimy w tym artykule są wdrażane następujących usług foundation:
* Usługa ExpressRoute systemu Azure lub na platformie Azure VPN Gateway
* Co najmniej jeden kontroler domeny usługi Active Directory i serwer DNS, działające na platformie Azure

Firma Microsoft zaleca ustanowienia tej infrastruktury, zanim wdrożenia usługi Site Recovery.

## <a name="reference-sap-application-deployment"></a>Wdrażanie aplikacji SAP odwołania

Ta architektura referencyjna pokazuje, z oprogramowaniem SAP NetWeaver w środowisku Windows na platformie Azure dzięki wysokiej dostępności.  Ta architektura jest wdrażana z rozmiarów określonej maszyny wirtualnej (VM), które mogą być zmieniane spełniają wymagania Twojej organizacji.

![Diagram przedstawiający typowy wzorzec wdrożenia SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Zagadnienia dotyczące odzyskiwania po awarii

Do odzyskiwania po awarii (DR) musi być możliwe do trybu failover do regionu pomocniczego. Każda warstwa używa innej strategii w celu zapewnienia ochrony odzyskiwania po awarii (DR, disaster recovery).

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Maszyny wirtualne z systemem SAP Web Dispatcher puli 
Składnik Web Dispatcher służy jako moduł równoważenia obciążenia dla ruchu SAP na serwery aplikacji SAP. Uzyskanie wysokiej dostępności dla składnika Web Dispatcher, usługi Azure Load Balancer umożliwia wdrożenie równoległe Instalator Web Dispatcher w konfiguracji działania okrężnego dla ruchu HTTP (S) rozkład dostępne dyspozytorów sieci Web w puli usługi równoważenia. To będą replikowane, za pomocą Recovery (ASR) do usługi Azure Site i skrypty automatyzacji będzie służyć do konfigurowania modułu równoważenia obciążenia w regionie odzyskiwania po awarii. 

#### <a name="vms-running-application-servers-pool"></a>Maszyny wirtualne z puli serwerów aplikacji
Aby zarządzać grupami logowania dla serwerów aplikacji ABAP, transakcji SMLG jest używany. Używa ona funkcji w ramach serwera wiadomości centralnej usług równoważenia obciążenia do dystrybucji obciążeń między puli serwerów aplikacji SAP SAPGUIs i RFC ruchu. To będą replikowane, za pomocą usługi Azure Site Recovery 

#### <a name="vms-running-sap-central-services-cluster"></a>Maszyny wirtualne działające w klastrze usług SAP Central Services
Ta architektura referencyjna uruchamia centralnej usług na maszynach wirtualnych w warstwie aplikacji. Central Services jest potencjalnym pojedynczym punktem awarii (SPOF), po wdrożeniu na jednej maszynie Wirtualnej — typowe wdrożenie w przypadku wysokiej dostępności nie jest wymagane.<br>

Aby zaimplementować rozwiązanie zapewniające wysoką dostępność, można użyć udostępnionych klastrów dysku lub klastrze udziału plików. Aby skonfigurować maszyn wirtualnych do udostępnionych klastrów dysku, należy użyć klastra trybu Failover systemu Windows Server. Zaleca się monitor w chmurze jako monitor kworum. 
 > [!NOTE]
 > Usługa Azure Site Recovery nie jest replikowany monitora w chmurze w związku z tym zaleca się wdrażanie monitora w chmurze w regionie odzyskiwania po awarii.

Do obsługi środowiska klastra trybu failover, [oprogramowanie SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) wykonuje funkcję woluminu udostępnionego klastra, replikując niezależne dyski należące do węzłów klastra. Azure nie obsługuje natywnie udostępnionych dysków i dlatego wymaga udostępniane przez oprogramowanie SIOS rozwiązania. 

Innym sposobem obsługi klastrowania jest do wdrożenia klastra udziału plików. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ostatnio zmodyfikowane wzorca wdrażania centralnego usługi dostęp do katalogów globalnych /sapmnt za pośrednictwem ścieżki UNC. Jednak nadal zaleca się upewnij się, że udział UNC /sapmnt o wysokiej dostępności. Można to zrobić w wystąpieniu usługi centralnego przy użyciu klastra trybu Failover systemu Windows Server skalowania poziomie serwera plików (SOFS) oraz funkcję bezpośrednimi miejscami do magazynowania (S2D) w systemie Windows Server 2016. 
 > [!NOTE]
 > Obecnie pomocy technicznej usługi Azure Site Recovery tylko awarii punktu spójnego na poziomie replikacji maszyn wirtualnych przy użyciu magazynu miejsca do magazynowania bezpośrednie i pasywnym węzła oprogramowanie SIOS Datakeeper


## <a name="disaster-recovery-considerations"></a>Zagadnienia dotyczące odzyskiwania po awarii

Za pomocą usługi Azure Site Recovery do organizowania pracy nad pełnego wdrożenia SAP regionami platformy Azure.
Poniżej przedstawiono kroki konfigurowania odzyskiwania po awarii 

1. Replikowanie maszyn wirtualnych 
2. Projektowanie sieć odzyskiwania
3.  Replikowanie kontrolera domeny
4.  Replikowanie danych warstwy podstawowa 
5.  Przeprowadzanie testu trybu failover 
6.  Przejdź w tryb failover 

Poniżej przedstawiono zalecenia dotyczące odzyskiwania po awarii dla każdej warstwy, w tym przykładzie. 

 **Warstwy SAP** | **Zalecenie**
 --- | ---
**SAP Web Dispatcher puli** |  Replikacja za pomocą Site recovery 
**Puli serwerów aplikacji SAP** |  Replikacja za pomocą Site recovery 
**SAP Central Services klastra** |  Replikacja za pomocą Site recovery 
**Maszyny wirtualne usługi Active directory** |  Replikacja usługi Active directory 
**Serwery baz danych SQL** |  Zawsze włączone replikacji programu SQL Server

## <a name="replicate-virtual-machines"></a>Replikowanie maszyn wirtualnych

Aby rozpocząć replikowanie wszystkich SAP aplikacji maszyn wirtualnych do centrum danych odzyskiwania po awarii platformy Azure, postępuj zgodnie ze wskazówkami w [replikacji maszyny wirtualnej na platformie Azure](azure-to-azure-walkthrough-enable-replication.md).


* Aby uzyskać wskazówki na temat ochrony usługi Active Directory i DNS, zobacz [chronić Active Directory i DNS](site-recovery-active-directory.md) dokumentu.

* Aby uzyskać wskazówki na temat ochrony warstwy bazy danych uruchomione w programie SQL server, zapoznaj się [ochrona programu SQL Server](site-recovery-active-directory.md) dokumentu.

## <a name="networking-configuration"></a>Konfiguracja sieci

Jeśli korzystasz ze statycznego adresu IP, można określić adresu IP, który ma maszynę wirtualną. Aby ustawić adres IP, przejdź do **ustawień obliczenia i sieć** > **karty interfejsu sieciowego**.

![Zrzut ekranu pokazujący sposób ustawiania prywatny adres IP w okienku karty interfejsu sieci odzyskiwania lokacji](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Trwa tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowania różnych warstw w aplikacji wielowarstwowej podczas przejścia w tryb failover. Sekwencjonowanie pomaga w utrzymaniu spójności aplikacji. Po utworzeniu planu odzyskiwania w przypadku aplikacji sieci web w wielowarstwowych pełną kroki opisane w [utworzyć plan odzyskiwania przy użyciu usługi Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grupy trybu failover

1.  Dodając serwer aplikacji sieci web dispatcher i SAP Central services maszyn wirtualnych, należy utworzyć plan odzyskiwania.
2.  Kliknij przycisk "Dostosuj", aby pogrupować maszyny wirtualne. Domyślnie wszystkie maszyny wirtualne są częścią "Grupa 1".



### <a name="add-scripts-to-the-recovery-plan"></a>Dodaj skrypty do planu odzyskiwania
Dla Twoich aplikacji działać poprawnie konieczne może być czy niektóre operacje na maszynach wirtualnych Azure po przełączeniu w tryb failover lub podczas testowania trybu failover. Można zautomatyzować niektóre operacje po przejściu do trybu failover. Na przykład można zaktualizować wpisu DNS i zmiany mień skojarzenia i połączenia przez dodanie odpowiedniej skrypty do planu odzyskiwania.


Najczęściej używane skryptów usługi Azure Site Recovery można wdrożyć na koncie usługi Automation, klikając przycisk "Wdróż na platformie Azure" poniżej. Korzystając z dowolnego skryptu opublikowane, upewnij się, że należy postępować zgodnie ze wskazówkami w skrypcie.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Dodać skrypt akcję wstępną "Grupa 1", aby przełączyć awaryjnie grupy dostępności programu SQL. Użyj skryptu "ASR-SQL-FailoverAG" opublikowany w przykładowych skryptach. Upewnij się, zgodnie z wytycznymi w skrypcie i wprowadź wymagane zmiany w skrypcie, odpowiednio.
2. Dodaj skrypt akcji post w celu dołączenia modułu równoważenia obciążenia w trybie za pośrednictwem maszyn wirtualnych warstwy internetowej (Grupa 1). Użyj skryptu "ASR-AddSingleLoadBalancer" opublikowany w przykładowych skryptach. Upewnij się, zgodnie z wytycznymi w skrypcie i wprowadź wymagane zmiany w skrypcie, odpowiednio.

![Plan odzyskiwania SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1.  W witrynie Azure portal wybierz swój magazyn usługi Recovery Services.
2.  Wybierz plan odzyskiwania, który został utworzony dla aplikacji SAP.
3.  Wybierz pozycję **Test pracy w trybie failover**.
4.  Aby rozpocząć proces testu trybu failover, wybierz punkt odzyskiwania i usługa Azure virtual network.
5.  Po skonfigurowaniu dodatkowej środowiska wykonywać operacji sprawdzania poprawności.
6.  Po zakończeniu walidacji można wyczyścić środowisko trybu failover wybierz **wyczyść test pracy awaryjnej**.

Aby uzyskać więcej informacji, zobacz [testowy tryb failover na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1.  W witrynie Azure portal wybierz swój magazyn usługi Recovery Services.
2.  Wybierz plan odzyskiwania, który został utworzony dla aplikacji SAP.
3.  Wybierz pozycję **Tryb failover**.
4.  Aby rozpocząć proces pracy awaryjnej, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [trybu Failover w usłudze Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat tworzenia rozwiązania do odzyskiwania po awarii, w przypadku wdrożeń oprogramowania SAP NetWeaver przy użyciu usługi Site Recovery, zobacz oficjalny dokument do pobrania [oprogramowanie SAP NetWeaver: Tworzenie rozwiązania odzyskiwania po awarii przy użyciu usługi Azure Site Recovery](https://aka.ms/asr_sap). Oficjalny dokument w tym artykule omówiono zalecenia dotyczące różnych architekturach SAP, wyświetla listę obsługiwanych aplikacji i typy maszyn wirtualnych dla rozwiązania SAP na platformie Azure i w tym artykule opisano opcje planu testowania rozwiązanie odzyskiwania po awarii.
* Dowiedz się więcej o [replikowanie innych obciążeń](site-recovery-workload.md) przy użyciu usługi Site Recovery.

---
title: Ochrona za pomocą usługi Azure Site Recovery wdrożenia aplikacji wielowarstwowych SAP NetWeaver | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak chronić SAP NetWeaver wdrożenia aplikacji za pomocą usługi Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: asgang
ms.openlocfilehash: 27dfdec4e833a2f30963157ba2f4d95232e21270
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267336"
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Wdrożenie aplikacji wielowarstwowych SAP NetWeaver jest chroniony przy użyciu usługi Site Recovery

Większości wdrożeń SAP dużego rozmiaru i średniej wielkości niektórych formularz rozwiązanie odzyskiwania po awarii. Znaczenie rozwiązania w zakresie odzyskiwania po awarii niezawodny i testować wzrosło jako więcej podstawowe procesy biznesowe zostaną przeniesione do aplikacji, takich jak SAP. Usługa Azure Site Recovery został przetestowany i zintegrowany z aplikacjami SAP. Usługa Site Recovery przekracza możliwości większości rozwiązań odzyskiwania po awarii lokalnie i na niższy całkowity koszt posiadania (TCO) niż konkurencyjnych rozwiązania.

Za pomocą usługi Site Recovery można:
* **Włącz ochronę SAP NetWeaver i z systemem innym niż NetWeaver aplikacji produkcyjnych, które lokalnie** przez replikację składników do platformy Azure.
* **Włącz ochronę SAP NetWeaver i z systemem innym niż NetWeaver aplikacji produkcyjnych, uruchamianych na platformie Azure** przez replikację składników do innego centrum danych Azure.
* **Uproszczenie migracji chmury** przy użyciu usługi Site Recovery do migrowania wdrożenia SAP do platformy Azure.
* **Uprościć SAP uaktualniania projektu, testowania i prototypowania** tworząc produkcji sklonować na żądanie do testowania aplikacji SAP.

W tym artykule opisano, jak chronić SAP NetWeaver wdrożenia aplikacji za pomocą [usługi Azure Site Recovery](site-recovery-overview.md). Artykuł obejmuje najlepsze rozwiązania w zakresie ochrony wdrożenia SAP NetWeaver trójwarstwowa na platformie Azure przez replikację do innego centrum danych Azure przy użyciu usługi Site Recovery. Opisuje obsługiwane scenariusze i konfiguracje i wykonać test pracy awaryjnej (testowanie odzyskiwania po awarii), a rzeczywista praca awaryjna.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że należy znać sposób wykonywania następujących zadań:

* [Replikacja maszyny wirtualnej do platformy Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Projektowanie sieci odzyskiwania](site-recovery-azure-to-azure-networking-guidance.md)
* [Wykonaj test trybu failover na platformie Azure](azure-to-azure-walkthrough-test-failover.md)
* [Czy tryb failover na platformie Azure](site-recovery-failover.md)
* [Replikowanie kontrolera domeny](site-recovery-active-directory.md)
* [Replikowanie programu SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Site Recovery można użyć do wdrożenia rozwiązania odzyskiwania po awarii w następujących scenariuszach:
* Systemy SAP uruchomiona w centrum danych Azure jednego, które są replikowane do innego centrum danych Azure (Azure do platformy Azure odzyskiwania po awarii). Aby uzyskać więcej informacji, zobacz [architektura replikacji Azure do platformy Azure](https://aka.ms/asr-a2a-architecture).
* Systemie SAP VMware (lub fizyczny) serwerów lokalnych tego Replikuj do lokacji odzyskiwania po awarii w centrum danych Azure (odzyskiwania po awarii VMware do platformy Azure). Ten scenariusz wymaga niektóre dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [architektura replikacji VMware do platformy Azure](https://aka.ms/asr-v2a-architecture).
* SAP z systemami na funkcji Hyper-V lokalnych które są replikowane do lokacji odzyskiwania po awarii w centrum danych Azure (odzyskiwania po awarii dla funkcji Hyper-V-do Azure). Ten scenariusz wymaga niektóre dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [architektura replikacji funkcji Hyper-V-do Azure](https://aka.ms/asr-h2a-architecture).

W tym artykule używamy **Azure do platformy Azure** sytuacji odzyskiwania po awarii, aby zademonstrować funkcje odzyskiwania po awarii SAP usługi Site Recovery. Ponieważ replikacja usługi Site Recovery nie jest specyficzne dla aplikacji, proces, który opisano powinien mają zastosowanie również w innych scenariuszach.

### <a name="required-foundation-services"></a>Foundation wymagane usługi
W tym scenariuszu, który omówiono w tym artykule są wdrażane z następującymi usługami foundation:
* Brama sieci VPN platformy Azure ExpressRoute lub Microsoft Azure
* Co najmniej jeden kontroler domeny usługi Active Directory i serwera DNS, działające na platformie Azure

Firma Microsoft zaleca ustanowienia tej infrastruktury, przed przystąpieniem do wdrażania usługi Site Recovery.

## <a name="reference-sap-application-deployment"></a>Wdrażanie aplikacji SAP odwołania

Informacje o architekturze wzorcowej pokazuje systemie SAP NetWeaver w środowisku Windows Azure o wysokiej dostępności.  Taka architektura jest wdrażana z rozmiary określonej maszyny wirtualnej (VM), które można zmieniać w celu uwzględnienia potrzeb organizacji.

![Diagram typowy wzorzec wdrożenia SAP](./media/site-recovery-sap/reference_sap.png)

## <a name="disaster-recovery-considerations"></a>Zagadnienia dotyczące odzyskiwania po awarii

Do odzyskiwania awaryjnego (DR) musi być możliwe do trybu failover w regionie pomocniczym. Każda warstwa używa innej strategii w celu zapewnienia ochrony odzyskiwania po awarii (DR, disaster recovery).

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Maszyny wirtualne z systemami puli dyspozytorów Web SAP 
Składnik Web dyspozytora jest używany jako modułu równoważenia obciążenia dla SAP ruchu między serwerami aplikacji SAP. Aby osiągnąć wysoką dostępność dla składnika dyspozytora sieci Web, usługi równoważenia obciążenia Azure służy do wdrożenie równoległe Instalator sieci Web dyspozytora w konfiguracji okrężnego dla protokołu HTTP (S) Dystrybucja ruchu między dostępne dyspozytorów sieci Web w puli usługi równoważenia. To będą replikowane za pomocą usługi Azure Site Recovery (ASR) i skryptów automatyzacji będzie służyć do konfiguracji usługi równoważenia obciążenia na region odzyskiwania po awarii. 

####<a name="vms-running-application-servers-pool"></a>Pula serwerów aplikacji uruchomionych maszyn wirtualnych
Do zarządzania grupami logowania dla serwerów aplikacji ABAP, SMLG transakcji jest używany. Używa funkcji w ramach serwera komunikat centralnej usług równoważenia obciążenia do dystrybucji obciążeń między puli serwerów aplikacji SAP SAPGUIs i RFC ruchu. To będą replikowane za pomocą usługi Azure Site Recovery 

####<a name="vms-running-sap-central-services-cluster"></a>Maszyny wirtualne z systemami klastra usługi centralnej SAP
Informacje o architekturze wzorcowej działa centralnej usług na maszynach wirtualnych w warstwie aplikacji. Centralnej usługi to potencjalne pojedynczy punkt awarii (SPOF), po wdrożeniu na jednej maszyny Wirtualnej — typowe wdrożenie w przypadku wysokiej dostępności nie jest wymagane.<br>

Aby wdrożyć rozwiązanie zapewniające wysoką dostępność, można użyć klastra udostępnionego dysku lub klaster udziału plików. Aby skonfigurować maszyn wirtualnych klastra udostępnionego dysku, należy użyć klastra trybu Failover systemu Windows Server. Monitor chmury jest zalecane jako monitor kworum. 
 > [!NOTE]
 > Usługa Azure Site Recovery nie jest replikowany monitora chmury w związku z tym zaleca się wdrożyć monitora chmury w regionie odzyskiwania po awarii.

Do obsługi środowiska klastra trybu failover, [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) pełni funkcję woluminu udostępnionego klastra poprzez replikację niezależnych dysków należących do węzłów klastra. Azure nie obsługuje udostępnionych dysków i dlatego wymaga rozwiązań dostarczonych przez SIOS. 

Innym sposobem obsługi klastra jest wdrożenie klastra udziału plików. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ostatnio zmodyfikowanego wzorca wdrażania usług centralnej dostęp do katalogów globalnych /sapmnt za pośrednictwem ścieżki UNC. Ta zmiana usuwa wymóg SIOS lub innych rozwiązań udostępnionego dysku, na maszynach wirtualnych centralnej usług. Zalecane jest nadal upewnij się, że udział UNC /sapmnt jest wysokiej dostępności. Można to zrobić w wystąpieniu usługi centralnego przy użyciu klastra trybu Failover systemu Windows Server z skali limit serwera plików (SOFS) oraz funkcję bezpośrednie miejsca do magazynowania (S2D) w systemie Windows Server 2016. 
 > [!NOTE]
 > Obecnie pomocy technicznej usługi Azure Site Recovery tylko awarii punktu spójnego replikacji maszyn wirtualnych przy użyciu bezpośrednie miejsca do magazynowania 


## <a name="disaster-recovery-considerations"></a>Zagadnienia dotyczące odzyskiwania po awarii

Można użyć usługi Azure Site Recovery do organizowania pracy nad pełnego wdrożenia SAP w regionach platformy Azure.
Poniżej przedstawiono procedurę konfigurowania odzyskiwania po awarii 

1. Replikowanie maszyn wirtualnych 
2. Projektowanie sieci odzyskiwania
3.  Replikowanie kontrolera domeny
4.  Replikowanie danych warstwy podstawowej 
5.  Wykonaj test trybu failover 
6.  Tryb failover 

Poniżej przedstawiono zalecenia dotyczące odzyskiwania po awarii dla każdej warstwy używana w tym przykładzie. 

 **Warstw SAP** | **Zalecenia**
 --- | ---
**SAP puli dyspozytorów sieci Web** |  Replikuj za pomocą usługi Site recovery 
**Pula serwerów aplikacji SAP** |  Replikuj za pomocą usługi Site recovery 
**Klaster usługi centralnej SAP** |  Replikuj za pomocą usługi Site recovery 
**Maszyny wirtualne w usłudze Active directory** |  Replikacja usługi Active directory 
**Serwery bazy danych SQL** |  Zawsze na replikacji SQL

##<a name="replicate-virtual-machines"></a>Replikowanie maszyn wirtualnych

Aby rozpocząć replikację wszystkich SAP aplikacji maszyn wirtualnych do odzyskiwania centrum danych Azure po awarii, postępuj zgodnie ze wskazówkami w [replikowanie maszyny wirtualnej na platformie Azure](azure-to-azure-walkthrough-enable-replication.md).


* Aby uzyskać wskazówki dotyczące ochrony usługi Active Directory i DNS, zapoznaj się [ochrony usługi Active Directory i DNS](site-recovery-active-directory.md) dokumentu.

* Aby uzyskać wskazówki dotyczące ochrony warstwy bazy danych uruchomiony na serwerze SQL server, zapoznaj się [ochrony programu SQL Server](site-recovery-active-directory.md) dokumentu.

## <a name="networking-configuration"></a>Konfiguracja sieci

Użycie statycznego adresu IP, można określić adres IP, który ma maszynę wirtualną do wykonania. Aby ustawić adres IP, przejdź do **obliczeniowe i ustawień sieciowych** > **karty interfejsu sieciowego**.

![Zrzut ekranu pokazujący sposób ustawiania prywatnego adresu IP w okienku karty interfejsu sieciowego odzyskiwania lokacji](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowania różnych warstw w wielowarstwowej aplikacji podczas pracy w trybie failover. Sekwencjonowanie pomaga zachować spójność aplikacji. Po utworzeniu planu odzyskiwania dla aplikacji sieci web w wielowarstwowych pełną kroki opisane w [Tworzenie planu odzyskiwania przy użyciu usługi Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do trybu failover grupy

1.  Tworzenie planu odzyskiwania przez dodanie serwera aplikacji, dyspozytora sieci web i usług SAP centralnej maszyn wirtualnych.
2.  Kliknij pozycję Dostosuj, aby zgrupować maszyn wirtualnych. Domyślnie wszystkie maszyny wirtualne są częścią "Grupa 1".



### <a name="add-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania
Dla aplikacji działała prawidłowo może być konieczne wykonaj niektóre operacje na maszynach wirtualnych Azure po przejściu w tryb failover podczas testowania trybu failover. Można zautomatyzować niektóre operacje post pracy w trybie failover. Na przykład można zaktualizować wpisu DNS i mień skojarzenia i połączenia, dodając odpowiednie skrypty do planu odzyskiwania.


Najczęściej używane skrypty usługi Azure Site Recovery można wdrożyć na koncie automatyzacji, klikając przycisk "Wdrażanie na platformie Azure" poniżej. Korzystając z dowolnego skryptu opublikowane, upewnij się, że możesz postępuj zgodnie ze wskazówkami w skrypcie.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Skrypt przed akcji "Grupa 1" do trybu failover grupy dostępności funkcji SQL. Użyj skryptu "ASR-SQL-FailoverAG" opublikowany w przykładowe skrypty. Upewnij się, postępuj zgodnie ze wskazówkami w skrypcie i odpowiednio wprowadzić wymagane zmiany w skrypcie.
2. Dodawanie skryptu akcji post do podłączenia modułu równoważenia obciążenia na nieudane przez maszyny wirtualne warstwy sieci Web (Grupa 1). Użyj skryptu "ASR AddSingleLoadBalancer" opublikowany w przykładowe skrypty. Upewnij się, postępuj zgodnie ze wskazówkami w skrypcie i odpowiednio wprowadzić wymagane zmiany w skrypcie.

![Plan odzyskiwania SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1.  W portalu Azure wybierz magazyn usług odzyskiwania.
2.  Wybierz plan odzyskiwania, który został utworzony dla aplikacje SAP.
3.  Wybierz **testowanie trybu Failover**.
4.  Aby rozpocząć proces test trybu failover, wybierz punkt odzyskiwania i sieć wirtualna platformy Azure.
5.  Po skonfigurowaniu dodatkowej środowiska wykonywać operacji sprawdzania poprawności.
6.  Po zakończeniu operacji sprawdzania poprawności, aby wyczyścić środowisko trybu failover wybierz **oczyszczania testowy tryb failover**.

Aby uzyskać więcej informacji, zobacz [testowy tryb failover na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1.  W portalu Azure wybierz magazyn usług odzyskiwania.
2.  Wybierz plan odzyskiwania, który został utworzony dla aplikacje SAP.
3.  Wybierz **pracy awaryjnej**.
4.  Aby rozpocząć proces trybu failover, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [pracy awaryjnej w usłudze Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej o tworzeniu rozwiązanie odzyskiwania po awarii dla wdrożenia SAP NetWeaver przy użyciu usługi Site Recovery, zobacz oficjalny dokument do pobrania [SAP NetWeaver: tworzenie rozwiązanie odzyskiwania po awarii z usługą Azure Site Recovery](http://aka.ms/asr-sap). Oficjalny dokument zawiera omówienie zalecenia dla różnych architektur SAP, zawiera listę obsługiwanych aplikacji i typów maszyny Wirtualnej dla programu SAP na platformie Azure, a zawiera opis opcji planu testowania rozwiązanie do odzyskiwania po awarii.
* Dowiedz się więcej o [replikowanie innych obciążeń](site-recovery-workload.md) przy użyciu usługi Site Recovery.

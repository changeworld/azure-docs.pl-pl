---
title: Ochrona serwera plików za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób chronić serwer plików przy użyciu usługi Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 0b6d5dccbce30c55e259e4bb3f8ae4194a02b646
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916887"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Ochrona serwera plików za pomocą usługi Azure Site Recovery 

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza i organizuje odzyskiwanie po awarii maszyn lokalnych i Azure virtual machines (VMs). Odzyskiwanie po awarii obejmuje replikacji, trybu failover i odzyskiwania w różnych obciążeniach.

W tym artykule opisano, jak chronić serwer plików przy użyciu usługi Site Recovery i sprawia, że inne zalecenia do potrzeb różnych środowiskach. 

- [Replikuj maszyny serwera plików w usłudze IaaS platformy Azure](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replikowanie serwera plików w środowisku lokalnym przy użyciu usługi Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Architektura serwera plików
W celu otwórz udostępnianie plików systemu rozproszonego jest zapewnienie środowiska, w której grupy użytkowników geograficznie rozproszonych mogą współpracować efektywnie pracować z plikami i można zagwarantować, że ich wymagań integralności są wymuszane. Typowe w środowisku lokalnym ekosystemu serwera plików obsługuje dużą liczbę równoczesnych użytkowników i dużą liczbę elementów zawartości używa Distributed pliku System replikacji (DFSR) do replikacji planowania i ograniczania przepustowości. 

DFSR stosowany jest algorytm kompresji, znane jako zdalnego różnicowej kompresji (RDC), który może służyć do wydajnego pliki aktualizacji w ograniczonej przepustowości sieci. Wykrywa wstawienia, usuwania i zmianę położenia danych w plikach. DFSR jest włączona na replikowanie tylko zmienionych bloków pliku, gdy pliki są aktualizowane. Dostępne są także server pliku w środowiskach, gdzie codzienne kopie zapasowe są wykonywane w chronometrażu poza szczytem, adresowanych do potrzeb po awarii. DFSR nie jest zaimplementowana.

Na poniższym diagramie przedstawiono środowisko serwera plików za pomocą DFSR zaimplementowana.
                
![Architektura DFSR](media/site-recovery-file-server/dfsr-architecture.JPG)

Na poprzednim rysunku wielu serwerów plików o nazwie członków aktywnie uczestniczyć w replikacji plików w grupie replikacji. Zawartość w folderze replikowanym są dostępne dla wszystkich klientów, którzy wysyłają żądania do jednego z elementów członkowskich, nawet wtedy, gdy członek przejdzie do trybu offline.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Zalecenia dotyczące odzyskiwania po awarii, na serwerach plików

* **Replikowanie serwera plików przy użyciu usługi Site Recovery**: serwerów plików mogą być replikowane na platformie Azure przy użyciu usługi Site Recovery. Gdy jeden lub więcej serwerów plików w środowisku lokalnym są niedostępne, odzyskiwanie maszyn wirtualnych może zostać wznowione na platformie Azure. Maszyny wirtualne mogą następnie przekazuje żądania od klientów i lokalnych, pod warunkiem że to połączenie sieci VPN typu lokacja lokacja usługi Active Directory jest skonfigurowany na platformie Azure. Za pomocą tej metody w przypadku środowisku skonfigurowane DFSR lub środowisku serwera prostego pliku z nie DFSR. 

* **Rozszerzanie DFSR na maszynie Wirtualnej IaaS platformy Azure**: W środowisku serwera plików za pomocą DFSR zaimplementowane DFSR lokalnych można rozszerzyć na platformie Azure. Maszynę wirtualną platformy Azure następnie jest skonfigurowany do przeprowadzania rolę serwera plików. 

    * Po zależności połączenie sieci VPN typu lokacja lokacja i usługi Active Directory są obsługiwane i DFSR obowiązuje, gdy jeden lub więcej serwerów plików w środowisku lokalnym są niedostępne, klienci mogą łączyć się z maszyną wirtualną Azure, która służy do żądania.

    * Można użyć tej metody, jeśli maszyny wirtualne mają konfiguracje, które nie są obsługiwane przez usługę Site Recovery. Przykładem jest udostępnionym dysku klastra, która jest czasem powszechnie używana w środowisku serwerów plików. DFSR działa również w środowiskach o niskiej przepustowości z współczynnik zmian danych średniej. Należy wziąć pod uwagę dodatkowych kosztów o Maszynie wirtualnej platformy Azure w i uruchomiony przez cały czas. 

* **Użyj usługi Azure File Sync, aby replikować plików**: Jeśli zamierzasz korzystać z chmury lub korzysta już z Maszyną wirtualną platformy Azure, możesz użyć usługi Azure File Sync. Usługa Azure File Sync zapewnia synchronizację z w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem będące standardami branżowymi [Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) protokołu SMB. Udziały plików platformy Azure mogą instalowane równolegle przez chmurowych lub lokalnych wdrożeń systemu Windows, Linux i macOS. 

Poniższy diagram ułatwia określenie, jakie strategii do użycia dla danego środowiska serwera plików.

![Drzewo decyzyjne](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Czynniki do rozważenia w decyzje dotyczące odzyskiwania po awarii na platformie Azure

|Środowisko  |Zalecenie  |Kwestie do rozważenia |
|---------|---------|---------|
|Środowisko serwera plików z użyciem lub bez DFSR|   [Usługa use Site Recovery do replikacji](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Usługa Site Recovery nie obsługuje udostępnianych klastrach dysków lub magazynu dołączone do sieci (NAS). Jeśli środowisko korzysta z tych konfiguracji, należy użyć dowolnego z innych metod, zgodnie z potrzebami. <br> Usługa Site Recovery nie obsługuje protokołu SMB 3.0. Replikowane maszyny Wirtualnej zawiera zmiany, tylko wtedy, gdy zmiany wprowadzone w plikach są aktualizowane w oryginalnej lokalizacji plików.
|Środowisko serwera plików za pomocą DFSR     |  [Rozszerzanie DFSR na maszynie wirtualnej IaaS platformy Azure](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      DFSR działa dobrze w środowiskach bardzo crunched przepustowości. Takie podejście wymaga maszynie Wirtualnej platformy Azure, który jest włączony i uruchomiony przez cały czas. Należy uwzględnić w planach koszt maszyny Wirtualnej.         |
|Maszyna wirtualna IaaS platformy Azure     |     [File Sync ](#use-azure-file-sync-service-to-replicate-your-files)   |     Jeśli używasz File Sync w scenariuszu odzyskiwania po awarii, należy wykonać działania ręczne, aby upewnić się, że udziały plików są dostępne na komputerze klienckim, w sposób przezroczysty podczas pracy awaryjnej. Synchronizacja plików wymaga portu 445 być otwarte z komputera klienckiego.     |


### <a name="site-recovery-support"></a>Obsługa odzyskiwania lokacji
Ponieważ replikacji usługi Site Recovery jest niezależny od aplikacji, te zalecenia pozwalają powinny przechowywać wartość true w następujących scenariuszach.
| Element źródłowy    |Lokacja dodatkowa    |Na platformie Azure
|---------|---------|---------|
|Azure| -|Yes|
|Funkcja Hyper-V|   Yes |Yes
|VMware |Yes|   Yes
|Serwer fizyczny|   Yes |Yes
 

> [!IMPORTANT]
> Przed rozpoczęciem pracy z dowolnego z następujących trzech metod, upewnij się, że te zależności są wykonywane.

**Połączenie lokacja lokacja**: bezpośrednie połączenie między lokacją lokalną i siecią platformy Azure, należy ustanowić do zezwalania na komunikację między serwerami. Użyj bezpiecznego połączenia sieci VPN typu lokacja lokacja z siecią wirtualną platformy Azure, która jest używana jako lokacji odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [nawiązywania połączenia VPN lokacja lokacja między lokacją lokalną i siecią wirtualną platformy Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Usługi Active Directory**: DFSR jest zależna od usługi Active Directory. Oznacza to, że lasu usługi Active Directory przy użyciu lokalnych kontrolerów domeny jest rozszerzony do lokacji odzyskiwania po awarii na platformie Azure. Nawet jeśli nie używasz DFSR, jeśli oprogramowanie wyświetla odpowiednich użytkowników, należy udzielić dostępu lub sprawdzane pod kątem dostępu, należy wykonać następujące kroki. Aby uzyskać więcej informacji, zobacz [rozszerzanie środowiska lokalnego usługi Active Directory na platformę Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Zalecenie odzyskiwania po awarii dla maszyn wirtualnych IaaS platformy Azure

Jeśli konfigurujesz i zarządzanie odzyskiwania po awarii serwerów plików hostowane na maszynach wirtualnych IaaS platformy Azure, można wybrać jedną z dwóch opcji, oparte na tego, czy chcesz przenieść do [usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [Użyj File Sync](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Usługa Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Umożliwia replikowanie plików hostowany na maszynie wirtualnej IaaS File Sync

Usługi Azure Files można użyć w celu całkowitego zastąpienia lub uzupełnienia tradycyjnych lokalnych serwerów plików lub urządzeń NAS. Udziały plików platformy Azure także mogą być replikowane za pomocą File Sync na serwerach Windows, lokalnie lub w chmurze, wydajność i rozproszonego buforowania danych, w których jest używany. W poniższych krokach opisano zalecenie dotyczące odzyskiwania po awarii dla maszyn wirtualnych platformy Azure, które wykonują te same funkcje co tradycyjnych serwerów plików:
* Ochrona maszyn przy użyciu usługi Site Recovery. Postępuj zgodnie z instrukcjami w [replikować Maszynę wirtualną platformy Azure do innego regionu platformy Azure](azure-to-azure-quickstart.md).
* File Sync umożliwia replikację plików z maszyny Wirtualnej, który działa jako serwer plików do chmury.
* Usługa Site Recovery [planu odzyskiwania](site-recovery-create-recovery-plans.md) funkcję, aby dodać skrypty umożliwiające [instalowanie udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) i uzyskiwanie dostępu do udziału na maszynie wirtualnej.

W poniższych krokach opisano skrótowo sposób używania File Sync:

1. [Utwórz konto magazynu na platformie Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Jeśli wybrano magazyn geograficznie nadmiarowy z dostępem do odczytu dla konta magazynu otrzymasz dostęp do odczytu do danych z regionu pomocniczego, w razie awarii. Aby uzyskać więcej informacji, zobacz [Strategie odzyskiwania po awarii udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Utwórz udział plików](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Rozpoczęcia synchronizacji plików](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) na serwerze plików platformy Azure.
4. Tworzenie grupy synchronizacji. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Grupa synchronizacji musi zawierać co najmniej jeden punkt końcowy w chmurze, który reprezentuje udział plików platformy Azure. Grupa synchronizacji również musi zawierać jeden punkt końcowy serwera, który reprezentuje ścieżkę na serwerze Windows.
5. Pliki są teraz synchronizowane z udziału plików platformy Azure i usługi na serwerze lokalnym.
6. Awarii w środowisku lokalnie wykonać przejścia w tryb failover przy użyciu [planu odzyskiwania](site-recovery-create-recovery-plans.md). Dodaj skrypt w celu [instalowanie udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) i uzyskiwanie dostępu do udziału na maszynie wirtualnej.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Replikowanie maszyny wirtualnej IaaS pliku server przy użyciu usługi Site Recovery

Jeśli klienci lokalni bez uzyskujących dostęp do maszyny wirtualnej IaaS pliku server, należy wykonać następujące czynności. W przeciwnym razie przejdź do kroku 3.

1. Nawiąż połączenie sieci VPN typu lokacja lokacja między lokacją lokalną i siecią platformy Azure.
2. Rozszerzanie usługi Active Directory w środowisku lokalnym.
3. [Konfigurowanie odzyskiwania po awarii](azure-to-azure-tutorial-enable-replication.md) maszyny serwera plików IaaS do regionu pomocniczego.


Aby uzyskać więcej informacji na temat odzyskiwania po awarii do regionu pomocniczego, zobacz [w tym artykule](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replikowanie serwera plików w środowisku lokalnym przy użyciu usługi Site Recovery

W poniższych krokach opisano replikacji dla maszyny Wirtualnej VMware. Aby replikować Maszynę wirtualną funkcji Hyper-V znajduje się [w tym samouczku](tutorial-hyper-v-to-azure.md).

1. [Przygotowywanie zasobów platformy Azure](tutorial-prepare-azure.md) do replikacji maszyn lokalnych.
2. Nawiąż połączenie sieci VPN typu lokacja lokacja między lokacją lokalną i siecią platformy Azure. 
3. Rozszerzanie usługi Active Directory w środowisku lokalnym.
4. [Przygotowywanie lokalnych serwerów VMware](tutorial-prepare-on-premises-vmware.md).
5. [Konfigurowanie odzyskiwania po awarii](tutorial-vmware-to-azure.md) na platformie Azure dla lokalnych maszyn wirtualnych.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Rozszerzanie DFSR na maszynie wirtualnej IaaS platformy Azure

1. Nawiąż połączenie sieci VPN typu lokacja lokacja między lokacją lokalną i siecią platformy Azure. 
2. Rozszerzanie usługi Active Directory w środowisku lokalnym.
3. [Tworzenie i aprowizowanie serwera plików maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) w sieci wirtualnej platformy Azure.
Upewnij się, że maszyna wirtualna zostanie dodany do tej samej sieci wirtualnej platformy Azure, mającej wielu — łączność z lokalnym środowiskiem. 
4. Zainstaluj i [skonfigurować DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) w systemie Windows Server.
5. [Implementowanie przestrzeni nazw systemu plików DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Za pomocą przestrzeni nazw systemu plików DFS, zaimplementowane pracy w trybie failover folderów udostępnionych ze środowiska produkcyjnego do lokacji odzyskiwania po awarii może odbywać się, aktualizując obiektów docelowych folderów w przestrzeni nazw systemu plików DFS. Po tych systemu plików DFS replikacji zmian przestrzeni nazw za pomocą usługi Active Directory, użytkownicy są połączeni do celów odpowiedni folder w sposób niewidoczny dla użytkownika.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Replikowanie plików lokalnych przy użyciu pliku synchronizacji
File Sync umożliwia replikację plików do chmury. W przypadku awarii i niedostępności swojego serwera plików w środowisku lokalnym można zainstalować lokalizacje żądany plik z chmury i kontynuować do obsługi żądań od komputerów klienckich.
Aby zintegrować File Sync z usługą Site Recovery:

* Chroń maszyny serwera plików przy użyciu usługi Site Recovery. Postępuj zgodnie z instrukcjami w [w tym samouczku](tutorial-vmware-to-azure.md).
* File Sync umożliwia replikację plików z komputera, który służy jako serwer plików do chmury.
* Funkcja odzyskiwania planu w usłudze Site Recovery w celu dodania skryptów, aby zainstalować udział plików platformy Azure na serwerze plików w trybie failed-over maszyny Wirtualnej na platformie Azure.

Wykonaj następujące kroki, aby korzystać z synchronizacji plików:

1. [Utwórz konto magazynu na platformie Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Jeśli wybrano magazyn geograficznie nadmiarowy z dostępem do odczytu (zalecane) dla konta magazynu, masz dostęp do odczytu do danych z regionu pomocniczego, w razie awarii. Aby uzyskać więcej informacji, zobacz [Strategie odzyskiwania po awarii udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Utwórz udział plików](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Wdrażanie synchronizacji plików](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) na serwerze plików w środowisku lokalnym.
4. Tworzenie grupy synchronizacji. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Grupa synchronizacji musi zawierać co najmniej jeden punkt końcowy w chmurze, który reprezentuje udział plików platformy Azure. Grupa synchronizacji również musi zawierać jeden punkt końcowy serwera, który reprezentuje ścieżkę na lokalnym serwerze Windows.
5. Pliki są teraz synchronizowane z udziału plików platformy Azure i usługi na serwerze lokalnym.
6. Awarii w środowisku lokalnie wykonać przejścia w tryb failover przy użyciu [planu odzyskiwania](site-recovery-create-recovery-plans.md). Dodaj skrypt, aby zainstalować udział plików platformy Azure i uzyskiwanie dostępu do udziału na maszynie wirtualnej.

> [!NOTE]
> Upewnij się, że jest otwarty port 445. Usługa Azure Files korzysta z protokołu SMB. Protokół SMB komunikuje się za pośrednictwem portu TCP 445. Sprawdź, czy zapory nie blokuje portu TCP 445 z komputera klienckiego.


## <a name="do-a-test-failover"></a>Wykonaj test trybu failover

1. Przejdź do witryny Azure portal, a następnie wybierz swój magazyn usług odzyskiwania.
2. Wybierz plan odzyskiwania utworzone w środowisku serwera plików.
3. Wybierz **testowanie trybu Failover**.
4. Wybierz punkt odzyskiwania i sieci wirtualnej platformy Azure, aby rozpocząć proces testu trybu failover.
5. Po skonfigurowaniu dodatkowej środowiska wykonywać swoje operacji sprawdzania poprawności.
6. Po zakończeniu operacji sprawdzania poprawności, wybierz **wyczyść test pracy awaryjnej** w planie odzyskiwania i testowania trybu failover jest czyszczona środowiska.

Aby uzyskać więcej informacji na temat sposobu wykonywania testu trybu failover, zobacz [testu pracy w trybie failover Usługa Site Recovery](site-recovery-test-failover-to-azure.md).

Aby uzyskać wskazówki na ten test trybu failover dla usługi Active Directory i DNS, zobacz [testowanie trybu failover zagadnienia dotyczące usługi Active Directory i DNS](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Do trybu failover

1. Przejdź do witryny Azure portal i wybierz swój magazyn usługi Recovery Services.
2. Wybierz plan odzyskiwania utworzone w środowisku serwera plików.
3. Wybierz **trybu Failover**.
4. Wybierz punkt odzyskiwania, aby rozpocząć proces trybu failover.

Aby uzyskać więcej informacji na temat sposobu pracy w trybie Failover, zobacz [trybu Failover w usłudze Site Recovery](site-recovery-failover.md).

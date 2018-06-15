---
title: Ochrona serwera plików za pomocą usługi Azure Site Recovery
description: W tym artykule opisano, jak chronić serwer plików przy użyciu usługi Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 830f9c76d9d1bf11692fa9f2f5c49cbecdb69f25
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
ms.locfileid: "30266193"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Ochrona serwera plików za pomocą usługi Azure Site Recovery 

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza i organizuje odzyskiwania po awarii maszyn lokalnych i Azure maszynach wirtualnych (VM). Odzyskiwanie po awarii obejmuje replikacji, trybu failover i odzyskiwania różnych obciążeń.

W tym artykule opisano, jak chronić serwer plików przy użyciu usługi Site Recovery i sprawia, że inne zalecenia do potrzeb różnych środowiskach. 

- [Replikowanie maszyn serwera plików Azure IaaS](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replikowanie lokalnych serwera plików przy użyciu usługi Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Architektura serwera plików
W celu otwórz rozproszonej udostępnianie plików systemu ma zapewnić środowisko, w którym mogą współpracować grupy użytkowników geograficznie rozproszonych wydajną pracę na plikach i można zagwarantować, że ich wymagania dotyczące integralności są wymuszane. Typowy lokalnymi ekosystemu serwera plików obsługuje dużej liczby równoczesnych użytkowników i dużą liczbę elementów zawartości używa rozproszonych replikacji systemu plików (DFSR) dla replikacji planowania i ograniczania przepustowości. 

DFSR stosowany jest algorytm kompresji, znane jako zdalnego różnicowej kompresji RDC służący do wydajnie pliki aktualizacji w ograniczonej przepustowości sieci. Wykrywa wstawienia, usuwania i zmianę położenia danych w plikach. DFSR jest włączona na replikowanie tylko zmienionych bloków pliku podczas aktualizacji plików. Dostępne są także środowisku serwerów plików, gdzie codzienne kopie zapasowe są wykonywane w chronometrażu poza szczytem, które automatycznie dostosowują się do potrzeb po awarii. DFSR nie jest zaimplementowana.

Na poniższym diagramie przedstawiono środowiska serwera plików z DFSR zaimplementowana.
                
![Architektura usługi DFSR](media/site-recovery-file-server/dfsr-architecture.JPG)

W poprzedni diagram wieloma serwerami plików o nazwie członków aktywnie uczestniczyć w replikacji plików między grupą replikacji. Zawartość w folderze replikowanym są dostępne dla wszystkich klientów, którzy wysyłają żądania do jednego z elementów członkowskich, nawet wtedy, gdy element członkowski przejdzie do trybu offline.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Zalecenia dotyczące odzyskiwania po awarii dla serwerów plików

* **Replikowanie serwera plików przy użyciu usługi Site Recovery**: serwerów plików mogą być replikowane na platformie Azure przy użyciu usługi Site Recovery. Gdy jeden lub więcej serwerów plików lokalnych są niedostępne, odzyskiwania maszyn wirtualnych można włączane na platformie Azure. Maszyn wirtualnych można następnie obsługiwać żądań od klientów, lokalnymi, pod warunkiem że jest połączenie sieci VPN typu lokacja lokacja i usługi Active Directory jest skonfigurowany na platformie Azure. Z nie DFSR służy tej metody w przypadku środowisku skonfigurowane DFSR lub środowisku serwera prostego pliku. 

* **Rozszerzenie do maszyny Wirtualnej platformy Azure IaaS DFSR**: W środowisku serwera plików z DFSR zaimplementowana można rozszerzyć DFSR lokalnego do platformy Azure. Następnie włączono maszyny Wirtualnej platformy Azure do pełnienia funkcji serwera plików. 

    * Po zależności połączenie sieci VPN typu lokacja lokacja i usługi Active Directory są obsługiwane i DFSR znajduje się w miejscu, gdy jeden lub więcej serwerów plików lokalnych są niedostępne, klienci mogą łączyć się maszyna wirtualna Azure, który służy do żądania.

    * Tej metody można użyć, jeśli maszyny wirtualne mają konfiguracje, które nie są obsługiwane przez usługę Site Recovery. Przykładem jest dysku udostępnionego klastra, który jest czasami powszechnie używany w środowisku serwerów plików. DFSR również działa dobrze w środowiskach niskiej przepustowości przy użyciu zmian średnia. Należy wziąć pod uwagę dodatkowych kosztów o maszynie Wirtualnej platformy Azure w i uruchomione przez cały czas. 

* **Użyj synchronizacji plików Azure w celu zreplikowania plików**: Jeśli planujesz użyć chmury lub już maszyny Wirtualnej platformy Azure, możesz użyć synchronizacji plików Azure. Synchronizacja programu Azure plików oferuje synchronizowanie udziałów plików pełni zarządzane w chmurze, które są dostępne za pośrednictwem standardu branżowego [bloku komunikatów serwera](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) protokołu (SMB). Udziały plików platformy Azure mogą zainstalować jednocześnie w chmurze lub lokalnie wdrożeń systemu Windows, Linux i macOS. 

Poniższy diagram ułatwia określenie, jakie strategii dla danego środowiska serwera plików.

![drzewa decyzyjnego](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Czynniki, które należy uwzględnić w decyzjach o odzyskiwania po awarii na platformie Azure

|Środowisko  |Zalecenie  |Kwestie do rozważenia |
|---------|---------|---------|
|Środowisko serwera plików lub bez DFSR|   [Użyj usługi Site Recovery dla replikacji](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Usługa Site Recovery nie obsługuje udostępnione klastry dysków lub magazynu dołączone do sieci (NAS). Jeśli środowisko używa tych ustawień, należy użyć jednej z innych metod, zależnie od potrzeb. <br> Usługa Site Recovery nie obsługuje protokołu SMB 3.0. Zreplikowanej maszyny Wirtualnej uwzględniająca modyfikacje tylko wtedy, gdy zmiany wprowadzone do plików są aktualizowane w oryginalnej lokalizacji plików.
|Środowisko serwera plików z DFSR     |  [Rozszerzenie DFSR maszyny wirtualnej platformy Azure IaaS](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      DFSR działa dobrze w środowiskach bardzo crunched przepustowości. Ta metoda wymaga maszyny Wirtualnej platformy Azure, który jest włączony i uruchomione przez cały czas. Należy uwzględnić koszty maszyny Wirtualnej w procesie planowania.         |
|Maszyna wirtualna platformy Azure IaaS     |     [Plik synchronizacji ](#use-azure-file-sync-service-to-replicate-your-files)   |     Jeśli używasz synchronizacji plików w przypadku odzyskiwania po awarii, należy wykonać działania ręczne, aby upewnić się, że udziały plików są dostępne do komputera klienckiego w przezroczysty sposób podczas pracy awaryjnej. Synchronizacja plików wymaga port 445 być otwarty z komputera klienta.     |


### <a name="site-recovery-support"></a>Obsługa odzyskiwania lokacji
Ponieważ replikacja usługi Site Recovery jest niezależny od aplikacji, te zalecenia powinny dotyczy następujących scenariuszy.
| Element źródłowy    |Do lokacji dodatkowej    |Na platformie Azure
|---------|---------|---------|
|Azure| -|Yes|
|Funkcja Hyper-V|   Yes |Yes
|VMware |Yes|   Yes
|Serwer fizyczny|   Yes |Yes
 

> [!IMPORTANT]
> Przed rozpoczęciem pracy z jednej z następujących trzech metod, upewnij się, że te zależności są podejmowane obsługę.

**Połączenie lokacja lokacja**: można nawiązać bezpośrednie połączenie między lokacją lokalną i sieć platformy Azure Zezwalaj na komunikację między serwerami. Używać bezpiecznego połączenia sieci VPN typu lokacja lokacja do sieci wirtualnej platformy Azure, który jest używany jako lokacja odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [ustanowić połączenie sieci VPN lokacja lokacja między lokacją lokalną i sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Usługi Active Directory**: DFSR zależy od usługi Active Directory. Oznacza to, że lasu usługi Active Directory za pomocą lokalnych kontrolerów domeny jest rozszerzony do lokacji odzyskiwania po awarii na platformie Azure. Nawet jeśli nie używasz DFSR, jeśli uprawnionych użytkowników należy udzielić dostępu lub zweryfikować dostępu, należy wykonać następujące kroki. Aby uzyskać więcej informacji, zobacz [Rozszerz lokalnej usługi Active Directory na platformie Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Zalecenie odzyskiwania po awarii dla maszyn wirtualnych IaaS platformy Azure

Jeśli konfigurujesz i zarządzanie odzyskiwaniem hostowanych na maszynach wirtualnych Azure IaaS serwerów plików, można wybrać jedną z dwóch opcji, oparte na czy chcesz przenieść [plików Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [Przy użyciu pliku synchronizacji](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Użyj usługi Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Synchronizację plików replikację plików obsługiwanych na maszynie wirtualnej modelu IaaS

Usługi Azure Files można użyć w celu całkowitego zastąpienia lub uzupełnienia tradycyjnych lokalnych serwerów plików lub urządzeń NAS. Udziały plików platformy Azure również mogą być replikowane z synchronizacji plików na serwerach z systemem Windows, lokalnie lub w chmurze, wydajności i danych, gdzie są używane systemy buforowania rozproszonego. W poniższych krokach opisano zalecenie dotyczące odzyskiwania po awarii dla maszyn wirtualnych platformy Azure, który wykonać te same funkcje co serwerów plików tradycyjny:
* Ochrona maszyn przy użyciu usługi Site Recovery. Postępuj zgodnie z instrukcjami [replikacji maszyny Wirtualnej platformy Azure do innego regionu Azure](azure-to-azure-quickstart.md).
* Plik synchronizacji umożliwia replikację plików z maszyny Wirtualnej, który działa jako serwer plików do chmury.
* Użyj usługi Site Recovery [planu odzyskiwania](site-recovery-create-recovery-plans.md) funkcję, aby dodać skryptów [instalowanie udziału plików na platformę Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) i uzyskania dostępu do udziału na maszynie wirtualnej.

W poniższych krokach opisano krótko jak używać synchronizacji plików:

1. [Utwórz konto magazynu na platformie Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Jeśli wybrano magazynu geograficznie nadmiarowego dostęp do odczytu dla kont magazynu możesz uzyskać dostęp do odczytu do danych, z regionu pomocniczego w przypadku awarii. Aby uzyskać więcej informacji, zobacz [strategii odzyskiwania danych udział plików na platformę Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Tworzenie udziału plików](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Rozpocznij synchronizację plików](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) na serwerze plików na platformę Azure.
4. Utwórz grupę synchronizacji. Punkty końcowe w ramach grupy synchronizacji są zsynchronizowane ze sobą. Grupy synchronizacji musi zawierać co najmniej jednej chmurze punktu końcowego, który reprezentuje udział plików na platformę Azure. Grupy synchronizacji musi zawierać również co serwer punktu końcowego, który reprezentuje ścieżkę, w systemie Windows server.
5. Pliki są teraz zsynchronizowane między udziału plików platformy Azure i na serwerze lokalnym.
6. W przypadku awarii w środowisku lokalnym, należy wykonać trybu failover przy użyciu [planu odzyskiwania](site-recovery-create-recovery-plans.md). Dodaj skrypt [instalowanie udziału plików na platformę Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) i uzyskania dostępu do udziału na maszynie wirtualnej.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Replikuj maszyny wirtualnej serwera plików IaaS przy użyciu usługi Site Recovery

Jeśli lokalnymi klientom dostęp do maszyny wirtualnej serwera plików IaaS, należy wykonać następujące kroki. W przeciwnym razie przejdź do kroku 3.

1. Nawiąż połączenie sieci VPN typu lokacja lokacja między lokacją lokalną i sieć platformy Azure.
2. Rozszerzenie w lokalnej usłudze Active Directory.
3. [Konfigurowanie odzyskiwania po awarii](azure-to-azure-tutorial-enable-replication.md) IaaS maszyny serwera plików w regionie pomocniczym.


Aby uzyskać więcej informacji dotyczących odzyskiwania po awarii w regionie pomocniczym, zobacz [w tym artykule](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replikowanie lokalnych serwera plików przy użyciu usługi Site Recovery

W poniższych krokach opisano replikacji dla maszyny Wirtualnej VMware. Kroki do replikowania maszyny Wirtualnej funkcji Hyper-V, zobacz [w tym samouczku](tutorial-hyper-v-to-azure.md).

1. [Przygotowanie zasobów Azure](tutorial-prepare-azure.md) dla replikację maszyn lokalnych.
2. Nawiąż połączenie sieci VPN typu lokacja lokacja między lokacją lokalną i sieć platformy Azure. 
3. Rozszerzenie w lokalnej usłudze Active Directory.
4. [Przygotowanie serwerów VMware lokalnych](tutorial-prepare-on-premises-vmware.md).
5. [Konfigurowanie odzyskiwania po awarii](tutorial-vmware-to-azure.md) Azure dla maszyn wirtualnych lokalnie.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Rozszerzenie DFSR maszyny wirtualnej platformy Azure IaaS

1. Nawiąż połączenie sieci VPN typu lokacja lokacja między lokacją lokalną i sieć platformy Azure. 
2. Rozszerzenie w lokalnej usłudze Active Directory.
3. [Tworzenie i Inicjowanie ich obsługi serwera plików maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) w sieci wirtualnej platformy Azure.
Upewnij się, że maszyna wirtualna jest dodana do tej samej sieci wirtualnej platformy Azure, z łącznością między ze środowiska lokalnego. 
4. Zainstaluj i [skonfigurować DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) w systemie Windows Server.
5. [Implementowanie przestrzeni nazw systemu plików DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Z przestrzeni nazw systemu plików DFS zaimplementowana trybu failover folderów udostępnionych z produkcji lokacjach odzyskiwania po awarii jest możliwe, aktualizując obiektów docelowych folderów przestrzeni nazw systemu plików DFS. Po tych systemu plików DFS replikacji zmian przestrzeni nazw przy użyciu usługi Active Directory, użytkownicy są podłączone do odpowiedniego folderu obiektów docelowych przezroczysty.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Replikowanie lokalnych plików przy użyciu synchronizacji plików
Plik synchronizacji umożliwia replikację plików do chmury. W przypadku awarii i niedostępność serwera plików lokalnie można zainstalować lokalizacje żądanego pliku z chmury i kontynuować do obsługi żądań od komputerów klienckich.
Aby zintegrować plik synchronizacji z usługą Site Recovery:

* Ochrona maszyny serwera plików przy użyciu usługi Site Recovery. Postępuj zgodnie z instrukcjami [w tym samouczku](tutorial-vmware-to-azure.md).
* Plik synchronizacji umożliwia replikację plików z komputera, który służy jako serwer plików do chmury.
* Użyj funkcji planu odzyskiwania w usłudze Site Recovery, aby dodać skryptów, aby zainstalować udział plików na platformę Azure na serwerze plików przełączona w tryb failover maszyny Wirtualnej na platformie Azure.

Wykonaj następujące kroki, aby użyć pliku synchronizacji:

1. [Utwórz konto magazynu na platformie Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Jeśli dostęp do odczytu magazynu geograficznie nadmiarowego (zalecane) w przypadku kont magazynu, masz dostęp do odczytu do danych z regionu pomocniczego w przypadku awarii. Aby uzyskać więcej informacji, zobacz [strategii odzyskiwania danych udział plików na platformę Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Tworzenie udziału plików](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Wdrażanie synchronizacji plików](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) w lokalnej serwera plików.
4. Utwórz grupę synchronizacji. Punkty końcowe w ramach grupy synchronizacji są zsynchronizowane ze sobą. Grupy synchronizacji musi zawierać co najmniej jednej chmurze punktu końcowego, który reprezentuje udział plików na platformę Azure. Grupy synchronizacji musi zawierać również co serwer punktu końcowego, który reprezentuje ścieżkę, w systemie Windows server lokalnymi.
5. Pliki są teraz zsynchronizowane między udziału plików platformy Azure i na serwerze lokalnym.
6. W przypadku awarii w środowisku lokalnym, należy wykonać trybu failover przy użyciu [planu odzyskiwania](site-recovery-create-recovery-plans.md). Dodawanie skryptu, aby zainstalować udział plików na platformę Azure i uzyskać dostępu do udziału na maszynie wirtualnej.

> [!NOTE]
> Upewnij się, że jest otwarty port 445. Usługa pliki Azure korzysta z protokołu SMB. SMB komunikuje się za pośrednictwem portu TCP 445. Sprawdź, czy Zapora nie blokuje port TCP 445 z komputera klienta.


## <a name="do-a-test-failover"></a>Wykonaj test trybu failover

1. Przejdź do portalu Azure, a następnie wybierz magazyn usług odzyskiwania.
2. Wybierz plan odzyskiwania utworzone w środowisku serwera plików.
3. Wybierz **testowanie trybu Failover**.
4. Wybierz punkt odzyskiwania i sieć wirtualna platformy Azure, aby rozpocząć proces test trybu failover.
5. Po skonfigurowaniu dodatkowej środowiska wykonania programu operacji sprawdzania poprawności.
6. Po zakończeniu operacji sprawdzania poprawności, wybierz **oczyszczania testowy tryb failover** w planie odzyskiwania i testowania trybu failover jest czyszczona środowiska.

Aby uzyskać więcej informacji na temat sposobu wykonywania test trybu failover, zobacz [testu pracy w trybie failover Usługa Site Recovery](site-recovery-test-failover-to-azure.md).

Aby uzyskać wskazówki na ten test trybu failover dla usługi Active Directory i DNS, zobacz [Test pracy awaryjnej zagadnienia dotyczące usługi Active Directory i DNS](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Tryb failover

1. Przejdź do portalu Azure, a następnie wybierz magazyn usług odzyskiwania.
2. Wybierz plan odzyskiwania utworzone w środowisku serwera plików.
3. Wybierz **pracy awaryjnej**.
4. Wybierz punkt odzyskiwania, aby rozpocząć proces trybu failover.

Aby uzyskać więcej informacji na temat sposobu uruchamiania trybu failover, zobacz [pracy awaryjnej w usłudze Site Recovery](site-recovery-failover.md).

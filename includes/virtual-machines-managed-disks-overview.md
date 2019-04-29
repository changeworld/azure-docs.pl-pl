---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9d96bd76a4d284e9b4390c564446e8b27c43d591
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613691"
---
## <a name="benefits-of-managed-disks"></a>Korzyści z dyskami zarządzanymi

Zajmijmy niektóre korzyści, jakie możesz uzyskać przy użyciu dysków zarządzanych.

### <a name="highly-durable-and-available"></a>Duża trwałość i wysoka dostępność

Dyski zarządzane zostały zaprojektowane dla dostępność przez 99,999% czasu. Dyski zarządzane to osiągnąć, udostępniając trzy repliki danych, co zapewnia wysoką trwałość. Jeśli w jednej lub nawet w dwóch replikach wystąpią błędy, pozostałe repliki pomogą w zapewnieniu trwałości danych i dużej tolerancji w przypadku awarii. Ta architektura pomogła platformie Azure w przeznaczonych dla przedsiębiorstw dzięki wiodącej w branży trwałość infrastruktura jako usługa (IaaS) dysków ZEROWĄ stawkę wycena błąd %.

### <a name="simple-and-scalable-vm-deployment"></a>Proste i skalowalne wdrażanie maszyn wirtualnych

Używanie dysków zarządzanych, możesz utworzyć maksymalnie 50 000 maszyn wirtualnych **dysków** typu w subskrypcję w danym regionie, co pozwala na tworzenie tysięcy **maszyn wirtualnych** w ramach jednej subskrypcji. Tę funkcję również zwiększa skalowalność [zestawy skalowania maszyn wirtualnych](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) , dzięki czemu możesz utworzyć maksymalnie 1000 maszyn wirtualnych w maszyny wirtualnej zestawu skalowania przy użyciu obrazu portalu Marketplace.

### <a name="integration-with-availability-sets"></a>Integracja z zestawami dostępności

Dyski zarządzane są zintegrowane z zestawami dostępności w celu zapewnienia, że dyski [maszyn wirtualnych w zestawie dostępności](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) są wystarczająco odizolowane od siebie, aby uniknąć pojedynczego punktu awarii. Dyski są automatycznie umieszczane w różnych jednostkach skalowania magazynu (sygnatury). Sygnatura zakończy się niepowodzeniem z powodu awarii sprzętu lub oprogramowania, tylko wystąpienia maszyn wirtualnych z dyskami na tych sygnatur zakończyć się niepowodzeniem. Na przykład załóżmy, że masz aplikację działającą w pięciu maszyn wirtualnych i maszyny wirtualne znajdują się w zestawie dostępności. Dyski dla tych maszyn wirtualnych nie będzie można przechowywać w tej samej sygnaturze, więc jeśli jednej sygnatury ulegnie awarii, pozostałe wystąpienia aplikacji nadal działać.

### <a name="integration-with-availability-zones"></a>Integracja z usługą strefy dostępności

Managed disks obsługuje [strefy dostępności](../articles/availability-zones/az-overview.md), czyli oferty o wysokiej dostępności, która chroni aplikacji przed awariami centrum danych. Strefy dostępności to unikatowe fizycznie lokalizacje w regionie platformy Azure. Każda strefa składa się z co najmniej jeden centrów danych, wyposażone w niezależne zasilanie, chłodzenie i usługi sieciowe. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Dzięki strefom dostępności platforma Azure oferuje najlepszą w branży umowę dotycząca poziomu usług (SLA) gwarantującą czas działania na poziomie 99,99%.

### <a name="azure-backup-support"></a>Obsługa usługi Azure Backup

Aby zapewnić ochronę przed regionalnej awarii [kopia zapasowa Azure](../articles/backup/backup-introduction-to-azure-backup.md) może służyć do tworzenia zadania tworzenia kopii zapasowej za pomocą zasad przechowywania kopii zapasowych i kopii zapasowych opartych na czasie. Dzięki temu można łatwo przywracana maszyny Wirtualnej w momencie wykonywania. Usługa Azure Backup obsługuje obecnie rozmiary dysków maksymalnie cztery dyski tebibyte (TiB). Aby uzyskać więcej informacji, zobacz [przy użyciu usługi Azure Backup dla maszyn wirtualnych z dyskami zarządzanymi](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

### <a name="granular-access-control"></a>Szczegółową kontrolę dostępu

Możesz użyć [kontroli dostępu opartej na rolach na platformie Azure (RBAC)](../articles/role-based-access-control/overview.md) przypisać określone uprawnienia dla dysku zarządzanego do co najmniej jednego użytkownika. Uwidacznianie dysków zarządzanych w różne operacje, w tym odczytu, zapisu (Tworzenie/aktualizowanie), usuwania i pobierania [sygnatury dostępu współdzielonego (SAS) identyfikator URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) dla dysku. Możesz udzielić dostępu do operacji tylko osoba potrzebuje do wykonywania ich zadań. Na przykład jeśli nie chcesz, aby osoby do kopiowania dysku zarządzanego do konta magazynu, możesz nie można udzielić dostępu do akcji eksportu dla dysku zarządzanego. Podobnie jeśli nie chcesz, aby osoba kopiowania dysku zarządzanego za pomocą identyfikatora URI sygnatury dostępu Współdzielonego, można nie przyznać to uprawnienie do dysków zarządzanych.

## <a name="disk-roles"></a>Role dysku

Istnieją trzy role głównego dysku na platformie Azure: dysk z danymi, dysk systemu operacyjnego i tymczasowy dysk. Te role są mapowane na dyskach, które są dołączone do maszyny wirtualnej.

![Role dysku w działaniu](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Dysk z danymi

Dysk z danymi jest dysk zarządzany, który jest dołączony do maszyny wirtualnej do przechowywania danych aplikacji lub innymi danymi, które należy zachować. Dyski danych są rejestrowane jako dyski SCSI i są oznaczone literą, który wybierzesz. Każdy dysk z danymi ma maksymalną pojemność wynoszącą 32 767 gibibajtach (GiB). Rozmiar maszyny wirtualnej Określa, jak wiele dysków z danymi można dołączać i typ magazynu służy do obsługi dysków.

### <a name="os-disk"></a>Dysk systemu operacyjnego

Do każdej maszyny wirtualnej ma jeden dysk dołączony systemu operacyjnego. Ten dysk systemu operacyjnego został wstępnie zainstalowany system operacyjny, który został wybrany podczas tworzenia maszyny Wirtualnej.

Ten dysk ma maksymalną pojemność wynoszącą 2048 GiB.

### <a name="temporary-disk"></a>Dysk tymczasowy

Każda maszyna wirtualna zawiera dysk tymczasowy brak dysku zarządzanego. Dysk tymczasowy udostępnia krótkoterminowy magazyn dla aplikacji i procesów i jest przeznaczone do przechowywania tylko dane, takie jak stronicowania lub plik wymiany. Dane na dysku tymczasowym mogą zostać utracone podczas [zdarzeń związanych z konserwacją](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) zdarzenia lub gdy użytkownik [ponowne wdrożenie maszyny Wirtualnej](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Na maszynach wirtualnych z systemem Linux platformy Azure dysk tymczasowy to dysk /dev/sdb domyślnie, i na maszynach wirtualnych Windows dysk tymczasowy to dysk E: domyślnie. Podczas pomyślnego standardowa ponowny rozruch maszyny wirtualnej zostanie utrzymany danych na dysku tymczasowym.

## <a name="managed-disk-snapshots"></a>Migawki dysków zarządzanych

Migawki dysków zarządzanych jest tylko do odczytu pełnej kopii dysku zarządzanego, która jest przechowywana jako dysk zarządzany standardowy domyślnie. Przy użyciu migawek kopii zapasowych dysków zarządzanych w dowolnym momencie w czasie. Migawki te istnieć niezależnie od dysku źródłowego i można tworzyć nowe dyski zarządzane. One są rozliczane na podstawie rozmiaru używane. Na przykład jeśli utworzysz migawkę dysku zarządzanego z zaprowizowaną pojemnością 64 GiB i rzeczywistym użyciem danych rozmiaru 10 GiB tej migawki jest zapłacisz tylko za użyte dane o rozmiarze od 10 GiB.  

Aby dowiedzieć się więcej na temat tworzenia migawek z dyskami zarządzanymi, zobacz następujące zasoby:

* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Obrazy

Dyski zarządzane obsługują także tworzenie zarządzany obraz niestandardowy. Można utworzyć obrazu z niestandardowego dysku VHD na koncie magazynu lub bezpośrednio z uogólnionego (przetworzonego) maszyny Wirtualnej. Ten proces przechwytuje pojedynczego obrazu. Ten obraz zawiera wszystkie dyski zarządzane skojarzony z maszyną Wirtualną, w tym zarówno systemu operacyjnego i dysków z danymi. To zarządzany obraz niestandardowy umożliwia tworzenia setek maszyn wirtualnych przy użyciu niestandardowego obrazu bez konieczności kopiowania lub zarządzać wszystkie konta magazynu.

Aby uzyskać informacje na temat tworzenia obrazów zobacz następujące artykuły:

* [Jak przechwycić obrazu zarządzanego uogólnionej maszyny Wirtualnej na platformie Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Uogólnianie i przechwytywanie maszyny wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Obrazów i migawki

Należy zrozumieć różnicę między migawki i obrazy. W przypadku dysków zarządzanych możesz korzystać z obrazu uogólnionej maszyny Wirtualnej, która została wycofana. Ten obraz zawiera wszystkie dyski dołączone do maszyny Wirtualnej. Można użyć tego obrazu, utworzyć maszynę Wirtualną i zawiera wszystkie dyski.

Migawka jest kopii dysku w punkcie w czasie migawki. Dotyczy tylko jeden dysk. W przypadku maszyny Wirtualnej, która ma jeden dysk (dysk systemu operacyjnego) można wykonać migawki lub obrazu ją i Utwórz Maszynę wirtualną z migawki lub obrazu.

Migawka nie ma świadomości każdego dysku, z wyjątkiem tego, jakie zawiera. Dzięki temu problematyczne do użycia w scenariuszach wymagających koordynacji wielu dysków, takich jak rozkładanie. Migawki musi być w stanie zapewnić koordynację ze sobą, i to nie jest obecnie obsługiwane.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o typach poszczególnych dysków oferowanych na platformie Azure i jakiego typu jest odpowiednia dla Twoich potrzeb w naszym artykule typy dysków.

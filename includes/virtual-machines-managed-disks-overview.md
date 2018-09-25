---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 617f60345fb2a349eddb0db697604ba383794591
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060628"
---
# <a name="azure-managed-disks-overview"></a>Omówienie usługi Azure Managed Disks

Usługa Azure Managed Disks upraszcza zarządzanie dyskami maszyn wirtualnych IaaS platformy Azure dzięki zarządzaniu [kont magazynu](../articles/storage/common/storage-introduction.md) skojarzone z dyskami maszyn wirtualnych. Należy określić typ ([standardowych dysków Twardych](../articles/virtual-machines/windows/standard-storage.md), [SSD w warstwie standardowa](../articles/virtual-machines/windows/disks-standard-ssd.md), lub [dysku Premium SSD](../articles/virtual-machines/windows/premium-storage.md)) i potrzebny rozmiar dysku i Azure tworzy i zarządza dysku.

## <a name="benefits-of-managed-disks"></a>Korzyści z dyskami zarządzanymi

Spójrzmy na niektóre z korzyści można osiągnąć przy użyciu dysków zarządzanych, począwszy od tego wideo Channel 9 [większą odporność maszyny Wirtualnej platformy Azure z usługą Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Proste i skalowalne wdrażanie maszyn wirtualnych

Managed Disks obsługuje magazyn dla Ciebie w tle. Wcześniej konieczne było utworzenie kont magazynu do przechowywania dysków (pliki VHD) dla maszyn wirtualnych platformy Azure. Podczas skalowania w górę, trzeba było upewnij się, że utworzono dodatkowych kont magazynu, więc nie może przekraczać limit operacji We/Wy magazynu ze wszystkimi dysków. Z usługą Managed Disks obsługi magazynu, jest już ograniczona przez limity konta magazynu (np. 20 000 operacji We/Wy / konta). Masz również nie można skopiować obrazów niestandardowych (plików VHD) do wielu kont magazynu. Można zarządzać nimi w centralnej lokalizacji — jedno konto magazynu na region platformy Azure — i używać ich do tworzenia setek maszyn wirtualnych w ramach subskrypcji.

Dyski zarządzane pozwala utworzyć maksymalnie 50 000 maszyn wirtualnych **dysków** typu w subskrypcję w danym regionie, który umożliwi twórz tysiące **maszyn wirtualnych** w ramach jednej subskrypcji. Tę funkcję również zwiększa skalowalność [Virtual Machine Scale Sets](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) , umożliwiając tworzenie do tysięcy maszyn wirtualnych w maszyny wirtualnej zestawu skalowania przy użyciu obrazu portalu Marketplace.

### <a name="better-reliability-for-availability-sets"></a>Większą niezawodność zestawów dostępności

Managed Disks zapewnia to lepszą niezawodność zestawów dostępności przez zagwarantowanie, że dyski [maszyn wirtualnych w zestawie dostępności](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) są wystarczająco odizolowane od siebie, aby uniknąć pojedynczych punktów awarii. Dyski są automatycznie umieszczane w różnych jednostkach skalowania magazynu (sygnatury). Sygnatura zakończy się niepowodzeniem z powodu awarii sprzętu lub oprogramowania, tylko wystąpienia maszyn wirtualnych z dyskami na tych sygnatur zakończyć się niepowodzeniem. Na przykład załóżmy, że masz aplikację działającą w pięciu maszyn wirtualnych i maszyny wirtualne znajdują się w zestawie dostępności. Dyski dla tych maszyn wirtualnych nie będzie można przechowywać w tej samej sygnaturze, więc jeśli jednej sygnatury ulegnie awarii, pozostałe wystąpienia aplikacji nadal działać.

### <a name="highly-durable-and-available"></a>Duża trwałość i wysoka dostępność

Dyski platformy Azure zaprojektowano tak, aby zapewniały 99,999% dostępności. Śpij spokojnie, wiedząc, że masz trzy repliki danych, która zapewnia wysoką trwałość. Jeśli w jednej lub nawet w dwóch replikach wystąpią błędy, pozostałe repliki pomogą w zapewnieniu trwałości danych i dużej tolerancji w przypadku awarii. Ta architektura pomogła platformie Azure w zapewnieniu niezawodności klasy korporacyjnej dla dysków IaaS przez długi czas z rocznym współczynnikiem awarii w wysokości 0%, co stawia ją w czołówce branży. 

### <a name="granular-access-control"></a>Szczegółową kontrolę dostępu

Możesz użyć [based kontroli dostępu (RBAC)](../articles/role-based-access-control/overview.md) przypisać określone uprawnienia dla dysku zarządzanego do co najmniej jednego użytkownika. Zarządzane dyski udostępnia różne operacje, w tym Odczyt, zapis (Tworzenie/aktualizowanie), usuwania i pobierania [sygnatury dostępu współdzielonego (SAS) identyfikator URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) dla dysku. Możesz udzielić dostępu do operacji tylko osoba potrzebuje do wykonywania ich zadań. Na przykład jeśli nie chcesz, aby osoby do kopiowania dysku zarządzanego do konta magazynu, możesz nie można udzielić dostępu do akcji eksportu dla dysku zarządzanego. Podobnie jeśli nie chcesz, aby osoba kopiowania dysku zarządzanego za pomocą identyfikatora URI sygnatury dostępu Współdzielonego, można nie przyznać to uprawnienie do dysków zarządzanych.

### <a name="azure-backup-service-support"></a>Pomoc techniczna usługi kopii zapasowej platformy Azure

Tworzenie zadania tworzenia kopii zapasowej z kopii zapasowych opartych na czasie, łatwe przywracanie maszyny Wirtualnej i zasad przechowywania kopii zapasowych za pomocą usługi Kopia zapasowa Azure z usługą Managed Disks. Dyski zarządzane obsługują tylko magazyn lokalnie nadmiarowy (LRS) jako opcji replikacji. Trzy kopie danych są przechowywane w jednym regionie. Do odzyskiwania po awarii regionalnej, należy wykonać kopię zapasową dysków maszyn wirtualnych w różnych regionach za pomocą [usługę Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) i konto magazynu GRS jako magazyn kopii zapasowych. Usługa Azure Backup obsługuje obecnie rozmiary dysków do 4 TB dyski. Musisz [stosu kopii zapasowej maszyny Wirtualnej uaktualnienia do wersji 2](../articles/backup/backup-upgrade-to-vm-backup-stack-v2.md) obsługę dysków do 4 TB. Aby uzyskać więcej informacji, zobacz [usługi przy użyciu usługi Azure Backup dla maszyn wirtualnych z usługą Managed Disks](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Korzystając z dysków zarządzanych, zastosuj następujące zagadnienia dotyczące rozliczeń:

* Typ magazynu

* Rozmiar dysku

* Liczba transakcji

* Wychodzące transfery danych

* Zarządzane migawki dysków (kopii dyskowej pełna)

Przyjrzyjmy się bliżej w tych opcji.

**Typ magazynu:** Managed Disks oferuje 3 warstwy wydajności: [standardowych dysków Twardych](../articles/virtual-machines/windows/standard-storage.md), [SSD w warstwie standardowa](../articles/virtual-machines/windows/disks-standard-ssd.md), i [Premium](../articles/virtual-machines/windows/premium-storage.md). Naliczanie opłat za dysku zarządzanego zależy od tego, jakiego typu magazynu wybranego dysku.

**Rozmiar dysku**: opłaty za dyski zarządzane zależy od rozmiaru aprowizowanego dysku. Usługi Azure maps zaprowizowany rozmiar (z zaokrągleniem) do najbliższej opcji dysków Managed Disks w określonych w poniższych tabelach. Każdy dysk zarządzany mapowany na jeden z obsługiwanych rozmiarów elastycznie i jest rozliczana w związku z tym. Na przykład jeśli Tworzenie dysku zarządzanego standardowe i określ aprowizowanego rozmiaru 200 GB, są rozliczane zgodnie z cennikiem typ dysku S15.

W tym miejscu rozmiary dysków są dostępne dla dysku zarządzanego w warstwie premium:

| **Premium HDD Managed <br>typ dysku** | **P4** | **P6** | **P10** | **P15** | **P20** | **P30** | **P40** | **P50** | **P60** | **P70** | **P80** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Rozmiar dysku        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1024 giB (1 TiB) | 2048 giB (2 TiB) | 4095 giB (4 TiB) | 8192 giB (8 TiB) | 16 384 giB (16 TiB) | 32 767 giB (TiB) |

W tym miejscu są dostępne dla dysku zarządzanego standardowy dysk SSD rozmiary dysków:

| **Standardowy dysk SSD zarządzane <br>typ dysku** | **E10** | **E15** | **E20** | **E30** | **E40** | **E50** | **E60** | **E70** | **E80** |
|------------------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Rozmiar dysku        | 128 GiB | 256 GiB | 512 GiB | 1024 giB (1 TiB) | 2048 giB (2 TiB) | 4095 giB (4 TiB) | 8192 giB (8 TiB) | 16 384 giB (16 TiB) | 32 767 giB (TiB) |

W tym miejscu są dostępne dla dysku zarządzanego standardowych dysków Twardych rozmiary dysków:

| **Standardowy dysk twardy zarządzane <br>typ dysku** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** | **S60** | **S70** | **S80** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Rozmiar dysku        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1024 giB (1 TiB) | 2048 giB (2 TiB) | 4095 giB (4 TiB) | 8192 giB (8 TiB) | 16 384 giB (16 TiB) | 32 767 giB (TiB) |

**Liczba transakcji**: opłaty są naliczane za liczbę transakcji, które można wykonać na standardowa dysku zarządzanego.

Dyski SSD w warstwie standardowa użyć rozmiaru jednostki we/wy wynoszący 256KB. Transferowanych danych jest mniejsza niż 256 KB, jest uznawane za 1 jednostkę operacji We/Wy. Większego rozmiaru operacji We/Wy są liczone jako wiele operacji We/Wy o rozmiarze 256 KB. Na przykład KB 1100 operacji We/Wy jest traktowana jako pięć jednostki we/wy.

Nie ma żadnych kosztów transakcji dla dysku zarządzanego w warstwie premium.

**Wychodzące transfery danych**: [wychodzące transfery danych](https://azure.microsoft.com/pricing/details/data-transfers/) (dane wychodzące z centrów danych platformy Azure) Naliczanie opłat za zużycie przepustowości.

Aby uzyskać szczegółowe informacje na temat cen dla dysków Managed Disks, zobacz [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Migawki dysków zarządzanych

Migawka zarządzana to tylko do odczytu pełnej kopii dysku zarządzanego, która jest przechowywana jako dysk zarządzany standardowy domyślnie. Przy użyciu migawek kopii zapasowych dysków zarządzanych w dowolnym momencie w czasie. Migawki te istnieć niezależnie od dysku źródłowego i mogą służyć do tworzenia nowych dysków zarządzanych. One są rozliczane na podstawie rozmiaru używane. Na przykład jeśli utworzysz migawkę dysku zarządzanego z zaprowizowaną pojemnością 64 GiB i rzeczywistym użyciem danych rozmiaru 10 GiB migawki zostaną pobrane opłaty tylko za użyte dane o rozmiarze od 10 GiB.  

[Migawek przyrostowych](../articles/virtual-machines/windows/incremental-snapshots.md) nie są obecnie obsługiwane dla dysków Managed Disks.

Aby dowiedzieć się więcej na temat tworzenia migawek z usługą Managed Disks, zobacz następujące zasoby:

* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

## <a name="images"></a>Obrazy

Dyski zarządzane obsługują także tworzenie zarządzany obraz niestandardowy. Można utworzyć obrazu z niestandardowego dysku VHD na koncie magazynu lub bezpośrednio z uogólnionej maszyny Wirtualnej (z jego sys). Ten proces przechwytuje w jednym obrazie wszystkie zarządzane dyski skojarzone z maszyną Wirtualną, w tym zarówno systemu operacyjnego i dysków z danymi. To zarządzany obraz niestandardowy umożliwia tworzenia setek maszyn wirtualnych przy użyciu niestandardowego obrazu bez konieczności kopiowania lub zarządzać wszystkie konta magazynu.

Aby uzyskać informacje na temat tworzenia obrazów zobacz następujące artykuły:

* [Jak przechwycić obrazu zarządzanego uogólnionej maszyny Wirtualnej na platformie Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Jak Uogólnij i Przechwyć maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>Obrazów i migawki

Często widoczny wyraz "image" jest używana z maszynami wirtualnymi, a teraz widać "migawki" także. Należy zrozumieć różnicę pomiędzy tymi terminami. Z usługą Managed Disks możesz korzystać z obrazu uogólnionej maszyny Wirtualnej, która została wycofana. Ten obraz będzie zawierać wszystkie dyski dołączone do maszyny Wirtualnej. Tego obrazu można użyć do utworzenia nowej maszyny Wirtualnej i będzie on zawierał wszystkie dyski.

Migawki to kopia dysku w punkcie w czasie, w których jest ona traktowana. Dotyczy to tylko jeden dysk. W przypadku maszyny Wirtualnej, która ma tylko jeden dysk (OS) można wykonać migawki lub obrazu ją i Utwórz Maszynę wirtualną z migawki lub obrazu.

Co zrobić, jeśli maszyna wirtualna ma pięć dysków i są one rozkładane? Można utworzyć migawkę każdy z tych dysków, ale nie Brak świadomości na maszynie wirtualnej stan dyski — migawki tylko wiedzieć o tym jeden dysk. W takim przypadku migawki musi być koordynowane ze sobą, a który nie jest obecnie obsługiwany.

## <a name="managed-disks-and-encryption"></a>Dyski zarządzane i szyfrowania

Istnieją dwa rodzaje szyfrowania w celu omówienia w odniesieniu do dysków zarządzanych. Pierwsza z nich jest szyfrowanie usługi Storage (SSE), które jest wykonywane przez usługi storage. Drugi z nich jest usługa Azure Disk Encryption, które można włączyć dla maszyn wirtualnych na dyskach systemu operacyjnego i danych.

### <a name="storage-service-encryption-sse"></a>Szyfrowanie usługi Storage (SSE)

[Szyfrowanie usługi Azure Storage](../articles/storage/common/storage-service-encryption.md) oferuje szyfrowania podczas spoczynku i chronić dane zgodnie z wymaganiami bezpieczeństwa i zgodności obowiązującymi w organizacji. Funkcja SSE jest domyślnie włączona dla wszystkich dysków zarządzanych, migawek i obrazów we wszystkich regionach, w której są dostępne dyski zarządzane. Od 10 czerwca 2017 r. wszystkie nowe zarządzane migawki/dysków/obrazów i nowe dane zapisane na istniejących dyskach zarządzanych są automatycznie szyfrowane w spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft, domyślnie. Odwiedź stronę [strona często zadawane pytania dotyczące dysków zarządzanych](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) Aby uzyskać więcej informacji.

### <a name="azure-disk-encryption-ade"></a>Usługa Azure Disk Encryption (ADE)

Usługa Azure Disk Encryption umożliwia szyfrowanie dysków systemu operacyjnego i danych używanych przez maszynę wirtualną IaaS. To szyfrowanie obejmuje dysków zarządzanych. Dla Windows dyski są szyfrowane przy użyciu technologii szyfrowania funkcją BitLocker będące standardami branżowymi. W przypadku systemu Linux dyski są szyfrowane przy użyciu technologii DM-Crypt. Proces szyfrowania jest zintegrowana z usługą Azure Key Vault umożliwia kontrolowanie i zarządzać kluczami szyfrowania dysków. Aby uzyskać więcej informacji, zobacz [Azure szyfrowania dysku dla Windows i maszyn wirtualnych IaaS z systemem Linux](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat dysków zarządzanych zapoznaj się z następującymi artykułami.

### <a name="get-started-with-managed-disks"></a>Rozpoczynanie pracy z usługą Managed Disks

* [Tworzenie maszyny wirtualnej przy użyciu usługi Resource Manager i programu PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Create a Linux VM using the Azure CLI](../articles/virtual-machines/linux/quick-create-cli.md) (Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure)

* [Dołączanie dysku danych zarządzanego do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Dodawanie dysku zarządzanego do maszyny wirtualnej z systemem Linux](../articles/virtual-machines/linux/add-disk.md)

* [Zarządzane dyski PowerShell przykładowe skrypty](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Użycie usługi Managed Disks w szablonach usługi Azure Resource Manager](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Porównanie opcji magazynu dysków Managed Disks

* [Dyski SSD w warstwie Premium](../articles/virtual-machines/windows/premium-storage.md)

* [Dyski SSD w warstwie standardowa i dysk twardy](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Wskazówki dotyczące obsługi

* [Migrowanie z usług AWS i innych platform do usługi Managed Disks na platformie Azure](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Konwertowanie maszyn wirtualnych platformy Azure do usługi managed disks na platformie Azure](../articles/virtual-machines/windows/migrate-to-managed-disks.md)

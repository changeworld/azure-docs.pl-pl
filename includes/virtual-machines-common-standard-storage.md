---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: yuemlu
ms.service: storage
ms.topic: include
ms.date: 01/08/2019
ms.author: yuemlu
ms.custom: include file
ms.openlocfilehash: ad57d373422e0fc310e51ac31f2a2e76999abf22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127161"
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Ekonomicznego magazynu w warstwie standardowa oraz zarządzane i niezarządzane dyski maszyny Wirtualnej platformy Azure

Usługa Azure Standard Storage zapewnia obsługę przez dyski niezawodne, niedrogie dla maszyn wirtualnych, w uruchamianiu obciążeń liczonych niewrażliwego na opóźnienia. Obsługuje również obiekty BLOB, tabel, kolejek i plików. Dzięki usłudze Standard Storage dane są przechowywane dyski twarde (HDD). Podczas pracy z maszynami wirtualnymi, używając dysków SSD i HDD w warstwie standardowa dla scenariuszy deweloperskich lub testowych oraz mniej krytycznych obciążeń i dysków SSD w warstwie premium dla aplikacji produkcyjnych o kluczowym znaczeniu. Magazynu w warstwie standardowa jest dostępna we wszystkich regionach platformy Azure. 

Ten artykuł koncentruje się na korzystanie z dysków SSD i HDD w warstwie standardowa. Aby uzyskać więcej informacji o używaniu magazynu obiektów blob, tabel, kolejek i plików, zobacz [wprowadzenie do usługi Storage](../articles/storage/common/storage-introduction.md).

## <a name="disk-types"></a>Typy dysków

Istnieją dwa sposoby tworzenia dysków w warstwie standardowa dla maszyn wirtualnych platformy Azure:

**Niezarządzane dyski**: Ten typ dysku jest oryginalnej metody, w których zarządzasz konta magazynu używany do przechowywania plików wirtualnego dysku twardego, które odpowiadają na dyskach maszyny Wirtualnej. Pliki VHD są przechowywane jako stronicowe obiekty BLOB na kontach magazynu. Usługa Unmanaged disks można dołączyć do dowolnego rozmiaru maszyny Wirtualnej platformy Azure, w tym o maszynach wirtualnych, które przede wszystkim używasz usługi Premium Storage, takich jak seria DSv2 i GS. Maszyny wirtualne platformy Azure obsługują dołączanie kilka dyski w warstwie standardowa, dzięki czemu do 256 TiB pamięci na maszynę Wirtualną. Jeśli używasz rozmiary dysków (wersja zapoznawcza), możesz mieć maksymalnie około 2 PiB magazynu na maszynę Wirtualną.

[**Azure Managed Disks**](../articles/virtual-machines/windows/managed-disks-overview.md): Ta funkcja umożliwia zarządzanie kont magazynu dla dysków maszyn wirtualnych dla Ciebie. Określ typ (w warstwie Premium SSD, SSD w warstwie standardowa lub standardowych dysków Twardych) i rozmiar dysku należy i platforma Azure utworzy i zarządza dysku. Nie trzeba martwić się o umieszczenie dysków na wielu kontach magazynu w celu zapewnienia przekroczysz limity skalowalności konta magazynu — Azure sobie z nimi poradzi dla Ciebie.

Mimo że oba typy dysków są dostępne, zaleca się przy użyciu dysków zarządzanych z zalet ich wiele funkcji.

Aby rozpocząć korzystanie z usługi Azure Standard Storage, odwiedź stronę [Rozpocznij za darmo](https://azure.microsoft.com/pricing/free-trial/). 

Aby uzyskać informacje dotyczące sposobu tworzenia maszyny Wirtualnej z usługą Managed Disks zobacz jeden z następujących artykułów.

* [Tworzenie maszyny wirtualnej przy użyciu usługi Resource Manager i programu PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Create a Linux VM using the Azure CLI](../articles/virtual-machines/linux/quick-create-cli.md) (Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure)

## <a name="standard-storage-features"></a>Funkcje magazynu w warstwie standardowa

Spójrzmy na niektóre funkcje magazynu w warstwie standardowa. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Storage](../articles/storage/common/storage-introduction.md).

**Magazynu w warstwie standardowa**: Azure Standard Storage obsługuje usługi Azure Disks, obiektów blob platformy Azure, Azure Files, tabele platformy Azure i kolejek platformy Azure. Aby korzystać z usług magazynu w warstwie standardowa, zacznij od [Tworzenie konta usługi Azure Storage](../articles/storage/common/storage-quickstart-create-account.md).

**Dyski SSD w warstwie standardowa:** Dyski SSD w warstwie standardowa zapewnia bardziej niezawodną wydajność niż dyski standardowe dyski TWARDE i są obecnie dostępne. Aby dowiedzieć się więcej o dostępność w poszczególnych regionach dysków SSD w warstwie standardowa, zobacz [dostępność regionów dla dysków SSD w warstwie standardowa](../articles/virtual-machines/windows/faq-for-disks.md#standard-ssds-azure-regions).

**Dyski w warstwie standardowa dysk twardy:** Dyski w warstwie standardowa dysk twardy można dołączyć do wszystkich maszyn wirtualnych platformy Azure, w tym maszyny wirtualne z serii rozmiar używane dzięki usłudze Premium Storage, takich jak seria DSv2 i GS. Dysk standardowy dysk twardy można powiązać tylko z jednej maszyny Wirtualnej. Jednakże można dołączyć co najmniej jeden z tych dysków do maszyny Wirtualnej, maksymalna liczba dysków maksymalna, zdefiniowanych dla tego rozmiaru maszyny Wirtualnej. W poniższej sekcji Standard Storage dotyczące skalowalności i cele wydajności opisujemy specyfikacje bardziej szczegółowo.

**Standardowa stronicowych obiektów blob**: Standardowa stronicowe obiekty BLOB są używane do przechowywania dysków trwałych maszyn wirtualnych i można także uzyskać dostęp bezpośrednio za pośrednictwem interfejsu REST, podobnie jak inne typy obiektów blob platformy Azure. [Stronicowe obiekty BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) to zbiór stron 512-bajtowego zoptymalizowane pod kątem losowe odczytu i zapisu. 

**Replikacja magazynu:** W większości regionów danych na koncie magazynu w warstwie standardowa może być replikowany lokalnie lub replikacją geograficzną w wielu centrach danych. Cztery dostępnych typach replikacji to magazyn lokalnie nadmiarowy (LRS), Magazyn strefowo nadmiarowy (ZRS), Magazyn geograficznie nadmiarowy (GRS) i dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS). Dysków zarządzanych w magazynie Standard Storage aktualnie obsługuje magazyn lokalnie nadmiarowy (LRS) tylko. Aby uzyskać więcej informacji, zobacz [replikacja magazynu](../articles/storage/common/storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności

W tej sekcji opisano cele dotyczące skalowalności i wydajności, które należy wziąć pod uwagę podczas korzystania z magazynu w warstwie standardowa.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Limity konta — nie ma zastosowania do usługi managed disks

| **Zasób** | **Limit domyślny** |
|--------------|-------------------|
| TB na konto magazynu  | 500 TB |
| Maksymalna liczba przychodzących<sup>1</sup> na konto magazynu (nam regiony) | 10 GB/s włączenie GRS/ZRS, 20 GB/s dla magazynu LRS |
| Maksymalna liczba wychodzących<sup>1</sup> na konto magazynu (nam regiony) | 20 GB/s włączenie RA-GRS/GRS/ZRS, 30 GB/s dla magazynu LRS |
| Maksymalna liczba przychodzących<sup>1</sup> na konto magazynu (Europejską i regionów Azji) | 5 GB/s włączenie GRS/ZRS, 10 GB/s dla magazynu LRS |
| Maksymalna liczba wychodzących<sup>1</sup> na konto magazynu (Europejską i regionów Azji) | 10 GB/s włączenie RA-GRS/GRS/ZRS, 15 GB/s dla magazynu LRS |
| Łączna liczba żądań stawka za transakcje (w przypadku obiektu o rozmiarze 1 KB) konto magazynu | Maksymalnie 20 000 operacji We/Wy, jednostek na sekundę lub komunikatów na sekundę |

<sup>1</sup> ruch przychodzący odnosi się do wszystkich danych (żądań) są wysyłane do konta magazynu. Ruch wychodzący odnosi się do wszystkich danych (żądań) wysyłanych z konta magazynu.

Aby uzyskać więcej informacji, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](../articles/storage/common/storage-scalability-targets.md).

Wymagania aplikacji przekroczy cele skalowalności konta magazynu w jednym, tworzenie aplikacji na używanie wielu kont magazynu i partycjonuj dane na tych kontach magazynu. Alternatywnie możesz usługi Azure Managed Disks, a platforma Azure zarządza partycjonowania i umieszczenia danych dla Ciebie.

### <a name="standard-disks-limits"></a>Standard Disks Limits

W przeciwieństwie do dysków w warstwie Premium nie są udostępnione operacji wejścia/wyjścia na sekundę (IOPS) i przepływność (przepustowość) dysków w warstwie standardowa. Wydajność dysków w warstwie standardowa zależy od rozmiaru maszyny Wirtualnej, do której jest dołączony dysk i rozmiar dysku.

Jeśli obciążenie wymaga obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach, należy rozważyć użycie magazynu Premium Storage. Aby dowiedzieć się więcej korzyści z magazynu Premium, odwiedź stronę [High-Performance Premium Storage i dyski maszyn wirtualnych platformy Azure](../articles/virtual-machines/windows/premium-storage.md).

## <a name="snapshots-and-copy-blob"></a>Migawki i obiektu blob kopiowania

Z usługą Magazyn plików wirtualnego dysku twardego jest stronicowych obiektów blob. Można wykonać migawki stronicowe obiekty BLOB i skopiuj je do innej lokalizacji, takiej jak innego konta magazynu.

### <a name="unmanaged-disks"></a>Dyski niezarządzane

Możesz utworzyć [migawek przyrostowych](../articles/virtual-machines/windows/incremental-snapshots.md) dla standardowych dysków niezarządzanych w taki sam sposób możesz za pomocą migawek magazynu w warstwie standardowa. Firma Microsoft zaleca tworzenie migawek, a następnie skopiuj te migawki na konto magazynu geograficznie nadmiarowego magazynu w warstwie standardowa, jeśli dysk źródłowy znajduje się w konto magazynu lokalnie nadmiarowego. Aby uzyskać więcej informacji, zobacz [Opcje nadmiarowości magazynu Azure](../articles/storage/common/storage-redundancy.md).

Jeśli dysk jest dołączony do maszyny Wirtualnej, niektóre operacje interfejsu API nie są dozwolone na dyskach. Na przykład nie można wykonać [obiektu Blob kopiowania](/rest/api/storageservices/Copy-Blob) operacji na tym obiekcie blob, tak długo, jak dysk jest podłączony do maszyny Wirtualnej. Zamiast tego należy najpierw utworzyć migawkę tego obiektu blob za pomocą [wykonanie migawki obiektu Blob](/rest/api/storageservices/Snapshot-Blob) metody interfejsu API REST, a następnie wykonaj [obiektu Blob kopiowania](/rest/api/storageservices/Copy-Blob) migawki, aby skopiować dysk dołączony. Alternatywnie można odłączyć dysk, a następnie wykonaj wszelkie niezbędne operacje.

Aby zachować geograficznie nadmiarowych kopii usługi migawek, można skopiować migawki z konta magazynu lokalnie nadmiarowego do konta magazynu geograficznie nadmiarowego magazynu w warstwie standardowa przy użyciu narzędzia AzCopy lub obiektu Blob kopiowania. Aby uzyskać więcej informacji, zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](../articles/storage/common/storage-use-azcopy.md) i [obiektu Blob kopiowania](/rest/api/storageservices/Copy-Blob).

Aby uzyskać szczegółowe informacje dotyczące wykonywania operacji REST względem stronicowe obiekty BLOB na kontach magazynu w warstwie standardowa, zobacz [interfejsu API REST usługi Azure Storage](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Dyski zarządzane

Migawkę dysku zarządzanego jest tylko do odczytu kopię dysku zarządzanego, która jest przechowywana jako dysk zarządzany standardowych. Migawek przyrostowych nie są obecnie obsługiwane dla dysków Managed Disks, ale będą obsługiwane w przyszłości.

Dysk zarządzany jest dołączony do maszyny Wirtualnej, niektóre operacje interfejsu API nie są dozwolone na dyskach. Na przykład nie można wygenerować sygnaturę dostępu współdzielonego (SAS), można wykonać operacji kopiowania, gdy dysk jest podłączony do maszyny Wirtualnej. Zamiast tego należy najpierw utworzyć migawkę dysku, a następnie wykonaj kopię migawki. Można także Odłącz dysk, a następnie wygenerować sygnaturę dostępu współdzielonego (SAS), można wykonać operacji kopiowania.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Korzystając z magazynu w warstwie standardowa, zastosuj następujące zagadnienia dotyczące rozliczeń:

* Rozmiar dysków/danych magazynu w warstwie standardowa niezarządzanych
* Dyski zarządzane w warstwie standardowa
* Migawki magazynu w warstwie standardowa
* Wychodzące transfery danych
* Transakcje

**Rozmiar danych i dysków niezarządzanych magazynu:** W przypadku dysków niezarządzanych i innych danych (obiekty BLOB, tabel, kolejek i plików) jest naliczana tylko ilości miejsca, którego używasz. Na przykład, jeśli masz maszynę Wirtualną którego stronicowych obiektów blob jest inicjowana jak 127 GB, ale maszyna wirtualna jest tak naprawdę tylko przy użyciu 10 GB miejsca, stosowana jest stawka za 10 GB miejsca. Magazynu w warstwie standardowa firma Microsoft obsługuje maksymalnie 8191 GB i Standard niezarządzanych dysków do 4095 GB. 

**Dyski zarządzane:** Rozliczenia dla dysków zarządzanych w warstwie standardowa zależy od rozmiaru aprowizowanego dysku. Usługi Azure maps zaprowizowany rozmiar (z zaokrągleniem) do najbliższej opcji dysków Managed Disks w określonych w poniższych tabelach. Każdy dysk zarządzany mapowany na jeden z obsługiwanych rozmiarów elastycznie i jest rozliczana w związku z tym. Na przykład jeśli Tworzenie dysku zarządzanego standardowe i określ aprowizowanego rozmiaru 200 GiB, są rozliczane zgodnie z cennikiem typ dysku S15.

Rozmiary oznaczone gwiazdką są obecnie dostępne w wersji zapoznawczej.

| **Standardowy dysk twardy zarządzane <br>typ dysku** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** | **S60*** | **S70*** | **S80*** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Rozmiar dysku        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1024 GiB (1 TiB) | 2048 GiB (2 TiB) | 4095 GiB (4 TiB) | 8192 GiB (8 TiB) | 16,385 GiB (16 TiB) | 32 767 giB (32 TiB) |


**Migawki**: Migawki dysków w warstwie standardowa są naliczane za dodatkową pojemność posługują się migawki. Aby uzyskać informacji na temat migawek, zobacz [Tworzenie migawki obiektu Blob](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Wychodzące transfery danych**: [wychodzące transfery danych](https://azure.microsoft.com/pricing/details/data-transfers/) (dane wychodzące z centrów danych platformy Azure) Naliczanie opłat za zużycie przepustowości.

**Transakcja**: Na platformie Azure obowiązuje 0.0036 $ za 100 000 transakcji dla magazynu w warstwie standardowa. Transakcje obejmują operacje odczytu i zapisu związane z magazynem.

Aby uzyskać szczegółowe informacje na temat cen magazynu w warstwie standardowa, maszyny wirtualne i dyski Managed Disks zobacz:

* [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Ceny maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Ceny usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Pomoc techniczna usługi kopii zapasowej platformy Azure

Maszyny wirtualne z dyskami niezarządzanymi utworzeniem kopii zapasowej za pomocą usługi Azure Backup. [Więcej szczegółów](../articles/backup/backup-azure-vms-first-look-arm.md).

Za pomocą usługi Azure Backup i dyski Managed Disks do utworzenia zadania tworzenia kopii zapasowej z kopii zapasowych opartych na czasie, łatwe przywracanie maszyny Wirtualnej i zasad przechowywania kopii zapasowych. Możesz dowiedzieć się więcej o tym w [usługi przy użyciu usługi Azure Backup dla maszyn wirtualnych z usługą Managed Disks](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Storage](../articles/storage/common/storage-introduction.md)

* [Tworzenie konta magazynu](../articles/storage/common/storage-quickstart-create-account.md)

* [Omówienie funkcji Dyski zarządzane](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Tworzenie maszyny wirtualnej przy użyciu usługi Resource Manager i programu PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Create a Linux VM using the Azure CLI](../articles/virtual-machines/linux/quick-create-cli.md) (Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure)

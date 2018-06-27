---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: yuemlu
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: yuemlu
ms.custom: include file
ms.openlocfilehash: 4e62342a32456787863da775ea98df178ab1d559
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806302"
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Ekonomiczne Standard Storage i dysków maszyny Wirtualnej Azure niezarządzane i zarządzane

Azure Standard Storage zapewnia obsługę niezawodnych, tanich dysków dla maszyn wirtualnych obciążeniami niezależnych od opóźnienia. Obsługuje ona również obiekty BLOB, tabel, kolejek i plików. Z magazynu w warstwie standardowa dane są przechowywane na dyskach twardych (HDD). Podczas pracy z maszyn wirtualnych, można użyć dyski SSD i HDD standardowe scenariusze tworzenia/testowania i mniej krytycznych obciążeń i dysków SSD w warstwie premium aplikacji produkcyjnych krytycznym. Standardowy magazyn jest dostępny we wszystkich regionach platformy Azure. 

Ten artykuł skupia się na używanie standardowych dysków SSD i HDD. Aby uzyskać więcej informacji o używaniu magazynu z obiektów blob, tabel, kolejek i plików, zobacz [wprowadzenie do magazynu](../articles/storage/common/storage-introduction.md).

## <a name="disk-types"></a>Typy dysków

Istnieją dwa sposoby tworzenia dyski standardowe dla maszyn wirtualnych platformy Azure:

**Niezarządzane dysków**: ten typ dysku jest oryginalnej metody, w których zarządzasz kont magazynu, używany do przechowywania plików VHD, które odpowiadają dysków maszyny Wirtualnej. Pliki VHD są przechowywane jako stronicowe obiekty BLOB na kontach magazynu. Niezarządzane dysków można dołączyć do dowolnego rozmiaru maszyny Wirtualnej platformy Azure, w tym maszyn wirtualnych, które używają głównie magazyn w warstwie Premium, takich jak seria DSv2 i GS. Maszyny wirtualne platformy Azure obsługuje podłączania kilka dyski standardowe, dzięki czemu maksymalnie 256 TB pamięci masowej dla maszyny Wirtualnej.

[**Azure dysków zarządzanych**](../articles/virtual-machines/windows/managed-disks-overview.md): Ta funkcja zarządza kont magazynu użył dysków maszyny Wirtualnej dla Ciebie. Określ typ (Premium SSD, standardowych dysków SSD lub standardowych dysków Twardych) oraz rozmiar dysku należy i Azure tworzy i zarządza dysku. Nie trzeba martwić umieszczenie dyski za wiele kont magazynu w celu zapewnienia pozostać w limity skalowalności konta magazynu — Azure obsługuje, który automatycznie.

Mimo że oba typy dysków są dostępne, firma Microsoft zaleca używanie dysków zarządzanych przeprowadzać ich wiele funkcji.

Aby zacząć korzystać z usługi Azure Standard Storage, odwiedź stronę [zacznij pracę bezpłatnie](https://azure.microsoft.com/pricing/free-trial/). 

Aby uzyskać informacje dotyczące tworzenia maszyny Wirtualnej z dyskami zarządzanych Zobacz jedną z następujących artykułów.

* [Tworzenie maszyny wirtualnej przy użyciu usługi Resource Manager i programu PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Funkcje magazynu w warstwie standardowa 

Spójrzmy na niektóre funkcje magazynu w warstwie standardowa. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Storage](../articles/storage/common/storage-introduction.md).

**Standard Storage**: usługi Azure Standard Storage obsługuje dysków Azure, obiektów blob Azure, plików Azure, Azure tabel i kolejek Azure. Aby korzystać z usług magazynu w warstwie standardowa, Rozpocznij od [Tworzenie konta usługi Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).

**Dyski SSD standardowe:** dyski SSD standardowe zapewnić bardziej niezawodny wydajność niż dyski standardowe dysk twardy i są obecnie dostępne w wersji zapoznawczej. Aby uzyskać więcej informacji o dostępności region dysków SSD standardowego, zobacz [dostępność w danym regionie dysków SSD standardowe (wersja zapoznawcza)](../articles/virtual-machines/windows/faq-for-disks.md#standard-ssds-azure-regions).

**Dyski standardowe HDD:** dysków standardowych dysków Twardych mogą być dołączane do wszystkich maszyn wirtualnych platformy Azure, łącznie z serii rozmiar maszyn wirtualnych używane z magazyn w warstwie Premium, takich jak seria DSv2 i GS. Dysk standardowych dysków Twardych może zostać dołączona tyko do jednej maszyny Wirtualnej. Jednak możesz dołączyć co najmniej jeden z tych dysków do maszyny Wirtualnej, do liczby maksymalna liczba dyskowych operacji zdefiniowane dla tego rozmiaru maszyny Wirtualnej. W poniższej sekcji na cele dotyczące wydajności i skalowalności magazynu standardowego opisano szczegółowo w specyfikacji.

**Standardowa stronicowych obiektów blob**: standardowy stronicowe obiekty BLOB są używane do przechowywania stałe dyski dla maszyn wirtualnych i mogą również uzyskiwać bezpośrednio za pomocą REST, podobnie jak inne typy obiektów blob Azure. [Stronicowe obiekty BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) to zbiór stron 512-bajtowych zoptymalizowane pod kątem losowego odczytu i zapisu. 

**Replikacja magazynu:** w większości regionów, dane na koncie magazynu w warstwie standardowa mogą być replikowane lokalnie replikacją geograficzną w wielu centrach danych. Cztery typy replikacji dostępne są magazyn lokalnie nadmiarowy (LRS), Magazyn Strefowo nadmiarowy (ZRS) i magazynu geograficznie nadmiarowego (GRS) oraz dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS). Dysków zarządzanych w Standard Storage obsługuje obecnie magazyn lokalnie nadmiarowy (LRS) tylko. Aby uzyskać więcej informacji, zobacz [replikacji magazynu](../articles/storage/common/storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności

W tej sekcji opisano elementy docelowe skalowalności i wydajności, które należy wziąć pod uwagę w przypadku korzystania z magazynu w warstwie standardowa.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Limity konta — nie ma zastosowania do zarządzanych dysków

| **Zasób** | **Limit domyślny** |
|--------------|-------------------|
| TB na konto magazynu  | 500 TB |
| Maksymalna liczba wejściowych<sup>1</sup> na konto magazynu (nam regiony) | 10 GB/s włączenie GRS/ZRS, 20 GB/s dla LRS |
| Maksymalna liczba wyjście<sup>1</sup> na konto magazynu (nam regiony) | 20 GB/s włączenie RA-GRS/GRS/ZRS 30 GB/s dla LRS |
| Maksymalna liczba wejściowych<sup>1</sup> na konto magazynu (Europejskiej i regiony wschodniej) | 5 GB/s włączenie GRS/ZRS 10 GB/s dla LRS |
| Maksymalna liczba wyjście<sup>1</sup> na konto magazynu (Europejskiej i regiony wschodniej) | 10 GB/s włączenie RA-GRS/GRS/ZRS, 15 GB/s dla LRS |
| Całkowita liczba żądań stawka za transakcje (w przypadku obiektu o rozmiarze 1 KB) konta magazynu | Do 20 000 IOPS, w jednostkach na sekundę lub wiadomości na sekundę |

<sup>1</sup> wejściowych odwołuje się do wszystkich danych (liczba żądań) są wysyłane do konta magazynu. Transfer danych wychodzących odwołuje się do wszystkich danych (odpowiedzi) odbierane z konta magazynu.

Aby uzyskać więcej informacji, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](../articles/storage/common/storage-scalability-targets.md).

Jeśli wymagania aplikacji przekraczają wartości docelowe skalowalności konta jednego magazynu, skompilować aplikację do używania wielu kont magazynu i partycji danych przez tych kont magazynu. Alternatywnie można dysków zarządzanych Azure i Azure zarządza partycjonowania i umieszczenia danych dla Ciebie.

### <a name="standard-disks-limits"></a>Dyski standardowe, limity

W przeciwieństwie do dysków w warstwie Premium operacji wejścia/wyjścia na sekundę (IOPS) i przepływności (przepustowość) dyski standardowe nie są udostępnione. Wydajność dyski standardowe zależy od rozmiaru maszyny Wirtualnej, do którego jest podłączony dysk, a nie do rozmiaru dysku. Można spodziewać się do osiągnięcia limitu wydajności wymienione w poniższej tabeli.

**Dyski standardowe limity (zarządzanych i niezarządzanych)**

| **Warstwy maszyny Wirtualnej**            | **Maszyna wirtualna w warstwie podstawowej** | **Maszyna wirtualna w warstwie standardowa** |
|------------------------|-------------------|----------------------|
| Rozmiar maksymalny dysku          | 4095 GB           | 4095 GB              |
| Maksymalna liczba 8 KB IOPS dla każdego dysku | Maksymalnie 300         | Do 500            |
| Maksymalna przepustowość dla każdego dysku | Do 60 MB/s     | Do 60 MB/s        |

Jeśli obciążenie wymaga obsługi wysokiej wydajności i małych opóźnieniach dysku, należy rozważyć przy użyciu magazyn w warstwie Premium. Aby dowiedzieć się więcej zalet magazyn w warstwie Premium, odwiedź stronę [magazyn w warstwie Premium wysokiej wydajności i dyski maszyny Wirtualnej Azure](../articles/virtual-machines/windows/premium-storage.md). 

## <a name="snapshots-and-copy-blob"></a>Migawki i kopii obiektu blob

Usługa Magazyn plików wirtualnego dysku twardego jest stronicowych obiektów blob. Twórz migawki stronicowe obiekty BLOB i skopiuj je do innej lokalizacji, takiej jak innego konta magazynu.

### <a name="unmanaged-disks"></a>Dyski niezarządzane

Można utworzyć [przyrostowe migawki](../articles/virtual-machines/windows/incremental-snapshots.md) dla niezarządzanego standardowych dysków w taki sam sposób używać migawek z magazynu w warstwie standardowa. Zalecamy Tworzenie migawki, a następnie skopiować te migawki na konto standardowy magazyn geograficznie nadmiarowy, jeśli dysk źródłowy jest konto magazyn lokalnie nadmiarowy. Aby uzyskać więcej informacji, zobacz [opcje nadmiarowość magazynu Azure](../articles/storage/common/storage-redundancy.md).

Jeśli dysk jest dołączony do maszyny Wirtualnej, niektóre operacje interfejsu API nie są dozwolone na dyskach. Na przykład nie można wykonać [kopiowania obiektu Blob](/rest/api/storageservices/Copy-Blob) operacji dla tego obiektu blob tak długo, jak dysk jest dołączony do maszyny Wirtualnej. Zamiast tego należy najpierw utworzyć migawkę tego obiektu blob przy użyciu [migawki obiektu Blob](/rest/api/storageservices/Snapshot-Blob) metody interfejsu API REST, a następnie wykonaj [kopiowania obiektu Blob](/rest/api/storageservices/Copy-Blob) migawki, aby skopiować dołączono dysk. Alternatywnie można odłączyć dysk, a następnie wykonaj wszystkie niezbędne operacje.

Aby zachować geograficznie nadmiarowego kopie z migawki, możesz skopiować migawki konto magazyn lokalnie nadmiarowy do konta geograficznie nadmiarowego magazynu w warstwie standardowa przy użyciu narzędzia AzCopy lub kopiowania obiektu Blob. Aby uzyskać więcej informacji, zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](../articles/storage/common/storage-use-azcopy.md) i [kopiowania obiektu Blob](/rest/api/storageservices/Copy-Blob).

Aby uzyskać szczegółowe informacje dotyczące przeprowadzania operacji REST względem stronicowe obiekty BLOB na kontach magazynu w warstwie standardowa, zobacz [interfejsu API REST usług magazynu Azure](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Dyski zarządzane

Migawek dla dysków zarządzanych jest tylko do odczytu kopię dysków zarządzanych, który jest przechowywany jako standardowych dysków zarządzanych. Przyrostowe migawki nie są obecnie obsługiwane w przypadku dysków zarządzanych, ale będzie możliwe w przyszłości.

Jeśli dysków zarządzanych jest dołączony do maszyny Wirtualnej, niektóre operacje interfejsu API nie są dozwolone na dyskach. Na przykład nie można wygenerować sygnaturę dostępu współdzielonego (SAS), można wykonać operacji kopiowania, gdy dysk jest dołączony do maszyny Wirtualnej. Zamiast tego należy najpierw utworzyć migawkę dysku, a następnie wykonaj kopię migawki. Alternatywnie można Odłącz dysk, a następnie wygenerować sygnaturę dostępu współdzielonego (SAS), aby wykonać operację kopiowania.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Podczas korzystania z magazynu w warstwie standardowa, zastosuj następujące zagadnienia dotyczące rozliczeń:

* Rozmiar dysków/danych niezarządzanych magazynu w warstwie standardowa 
* Dyski zarządzane w warstwie Standardowa
* Migawki magazynu w warstwie standardowa
* Wychodzące transfery danych
* Transakcje

**Rozmiar magazynu danych i dysku niezarządzanych:** dysków niezarządzanych i innych danych (obiekty BLOB, tabel, kolejek i plików), naliczane są opłaty tylko ilości miejsca można dokonać. Na przykład, jeśli masz maszyny Wirtualnej obsługiwanej którego stronicowych obiektów blob jako 127 GB, ale maszyna wirtualna jest naprawdę tylko przy użyciu 10 GB miejsca, są rozliczane 10 GB miejsca. Firma Microsoft do obsługi magazynu w warstwie standardowa 8191 GB i Standard niezarządzanych dyski do 4095 GB. 

**Dyski zarządzane:** dysków zarządzane są rozliczane według rozmiaru elastycznie. Jeśli używane są tylko 5 GB obsługiwanej dysku jako dysku 10 GB, są naliczane rozmiaru udostępniania 10 GB.

**Migawki**: migawki dyski standardowe są rozliczane dodatkowej pojemności używanych przez migawki. Aby uzyskać informacji dotyczących migawek, zobacz [tworzenia migawki obiektu Blob](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Transfer danych wychodzących**: [transfery danych wychodzących](https://azure.microsoft.com/pricing/details/data-transfers/) (danych wychodzących z centrów danych Azure) powodują Naliczanie opłat za zużycie przepustowości.

**Transakcja**: $0.0036 na 100 000 transakcji dla magazynu w warstwie standardowa użytkownicy platformy Azure. Transakcje obejmują operacje odczytu i zapisu związane z magazynem.

Aby uzyskać szczegółowe informacje o cenach dla magazynu w warstwie standardowa, maszyn wirtualnych i dysków zarządzanych Zobacz:

* [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Cennik maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Dyski zarządzane ceny](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Obsługa usługi Kopia zapasowa Azure 

Maszyny wirtualne z dyskami niezarządzane utworzeniem kopii zapasowej za pomocą usługi Kopia zapasowa Azure. [Więcej szczegółów](../articles/backup/backup-azure-vms-first-look-arm.md).

Za pomocą usługi Kopia zapasowa Azure i zarządzane dysków do tworzenia zadania tworzenia kopii zapasowej na podstawie czasu tworzenia kopii zapasowych, łatwe przywrócenie maszyny Wirtualnej i zasady przechowywania kopii zapasowych. Więcej na temat [usługi przy użyciu kopii zapasowej Azure dla maszyn wirtualnych z dyskami zarządzane](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Storage](../articles/storage/common/storage-introduction.md)

* [Tworzenie konta magazynu](../articles/storage/common/storage-create-storage-account.md)

* [Omówienie usługi Managed Disks](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Tworzenie maszyny wirtualnej przy użyciu usługi Resource Manager i programu PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06e6e491fa1e9a047527efb78149855b125771ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66111410"
---
# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>Tworzenie kopii zapasowej Azure niezarządzanych dysków maszyn wirtualnych przy użyciu migawek przyrostowych
## <a name="overview"></a>Omówienie
Usługa Azure Storage zapewnia możliwość robienia migawek obiektów blob. Migawki przechwytują stan obiektu blob w danym momencie. W tym artykule opisano scenariusz, w którym można zachować kopii zapasowych dysków maszyny wirtualnej przy użyciu migawek. Możesz użyć tej metody, możesz zrezygnować z używania usługi Azure Backup i Recovery Service, gdy chcesz utworzyć niestandardowe strategii tworzenia kopii zapasowych dysków maszyny wirtualnej.

Dyski maszyny wirtualnej platformy Azure są przechowywane jako stronicowe obiekty BLOB w usłudze Azure Storage. Ponieważ firma Microsoft zawierająca opis strategii tworzenia kopii zapasowej dla dysków maszyny wirtualnej, w tym artykule nazywamy migawki w kontekście stronicowych obiektów blob. Aby dowiedzieć się więcej na temat migawek, zobacz [Tworzenie migawki obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Co to jest migawkę?
Migawki obiektu blob jest w wersji tylko do odczytu obiektu blob, która jest przechwytywana w punkcie w czasie. Po utworzeniu migawki go można odczytać, kopiowana, lub usunięte, ale nie zostały zmodyfikowane. Migawki zapewniają sposób, aby utworzyć kopię zapasową obiektu blob, jak pojawia się na moment w czasie. Do chwili REST w wersji 2015-04-05 trzeba było możliwość kopiowania pełnej migawki. Za pomocą architektury REST w wersji 2015-07-08 i powyżej, można również skopiować migawek przyrostowych.

## <a name="full-snapshot-copy"></a>Pełna migawka kopii
Migawki można skopiować do innego konta magazynu jako obiekt blob można przechowywać kopie zapasowe podstawowego obiektu blob. Możesz również skopiować migawki za pośrednictwem jego podstawowy obiekt blob, co przypomina Przywracanie obiektu blob do starszej wersji. Jeśli migawka jest kopiowana z jednego konta magazynu do innego, zajmuje się tą samą przestrzenią jako obiekt blob strony podstawowej Dlatego kopiowanie migawek całego z jednego konta magazynu do innego działa wolno i zużywa dużo miejsce docelowe konto magazynu.

> [!NOTE]
> Jeśli kopiujesz podstawowego obiektu blob do innego miejsca docelowego migawki obiektu blob nie są kopiowane zapotrzebowaniu. Podobnie Jeśli zastąpisz podstawowego obiektu blob przy użyciu kopii migawki związane z podstawowego obiektu blob nie ulegają zmianom i pozostają niezmienione pod nazwą podstawowego obiektu blob.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Wykonywanie kopii zapasowych dysków przy użyciu migawek
Jako strategii tworzenia kopii zapasowej dla dysków maszyny wirtualnej może potrwać okresowe migawki obiektu blob dysku lub na stronie, a kopia je do innego magazynu konta przy użyciu narzędzi takich jak [obiektu Blob kopiowania](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) operacji lub [AzCopy](../articles/storage/common/storage-use-azcopy.md). Migawki można skopiować do stronicowych obiektów blob docelowy o innej nazwie. Wynikowy obiekt blob typu page docelowy jest zapisywalna stronicowe obiekty blob i nie migawki. W dalszej części tego artykułu opisano kroki, aby wykonać kopie zapasowe dysków maszyn wirtualnych przy użyciu migawek.

### <a name="restore-disks-using-snapshots"></a>Przywróć dyski przy użyciu migawek
Gdy nadejdzie czas, aby przywrócić stabilną wersję, wcześniej przechwycone w jednym z migawkami kopii zapasowych na dysku, można skopiować migawki obiektu blob strony podstawowej. Po migawki zostanie podwyższony do strony podstawowej blob pozostanie migawki, ale jego źródło zostały zastąpione kopię, którą można zarówno Odczyt i zapis. W dalszej części tego artykułu opisano kroki, aby przywrócić poprzednią wersję dysku z jej migawek.

### <a name="implementing-full-snapshot-copy"></a>Implementowanie pełna migawka kopii
Pełna migawka kopii można wdrożyć, wykonując następujące polecenie,

* Najpierw należy utworzyć migawkę do podstawowego obiektu blob przy użyciu [wykonanie migawki obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) operacji.
* Następnie skopiuj do docelowego konta magazynu za pomocą migawki [obiektu Blob kopiowania](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Powtórz ten proces, do obsługi kopii zapasowych podstawowy obiekt blob.

## <a name="incremental-snapshot-copy"></a>Kopiowanie migawek przyrostowych
Nowa funkcja w [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) interfejs API zapewnia znacznie lepszą sposobem wykonania kopii zapasowej migawki stronicowych obiektów blob lub dysków. Interfejs API zwraca listę zmian między podstawowego obiektu blob i migawki, co zmniejsza ilość miejsca do magazynowania używane na koncie kopii zapasowej. Interfejs API obsługuje stronicowych obiektów blob na usługę Premium Storage, a także magazynu w warstwie standardowa. Za pomocą tego interfejsu API, można utworzyć szybsze i wydajniejsze rozwiązania tworzenia kopii zapasowych maszyn wirtualnych platformy Azure. Ten interfejs API będą dostępne w wersji REST 2015-07-08 lub nowszy.

Przyrostowe Kopiowanie migawki umożliwia kopiowanie z jednego konta magazynu do innego różnicy między,

* Podstawowy obiekt blob i jego migawki lub
* Wszystkie dwóch migawki obiektu blob podstawowy

Pod warunkiem, że są spełnione następujące warunki,

* Obiekt blob został utworzony w sty-1-2016 lub nowszym.
* Obiekt blob nie został zastąpiony [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) lub [obiektu Blob kopiowania](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) między dwiema migawkami.

**Uwaga**: Ta funkcja jest dostępna dla wersji Premium i standardowa usługi Azure stronicowe obiekty BLOB.

W przypadku strategii tworzenia kopii zapasowej niestandardowych przy użyciu migawek Kopiowanie migawki z jednego konta magazynu do innego może działać powoli i może używać dużej ilości miejsca do magazynowania. Zamiast kopiować całą migawkę do konta magazynu kopii zapasowej, można napisać różnią się kolejne migawki kopii zapasowej stronicowy obiekt blob. W ten sposób znacznie zmniejszyć czas do skopiowania i miejsce do przechowywania kopii zapasowych.

### <a name="implementing-incremental-snapshot-copy"></a>Implementowanie kopiowanie migawek przyrostowych
Kopiowanie migawek przyrostowych można wdrożyć, wykonując następujące polecenie,

* Utwórz migawkę do podstawowego obiektu blob przy użyciu [wykonanie migawki obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Kopiowanie migawki do docelowego konta magazynu kopii zapasowych w tej samej lub dowolnym innym regionie świadczenia usługi Azure przy użyciu [obiektu Blob kopiowania](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Jest to kopii zapasowej stronicowych obiektów blob. Utworzenie migawki kopii zapasowej stronicowych obiektów blob i zapisz go w kopii zapasowej konta.
* Utwórz kolejną migawkę obiektu blob podstawowej za pomocą migawki obiektu Blob.
* Pobierz różnica między pierwszym i drugim migawki do podstawowego obiektu blob przy użyciu [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Użyj nowego parametru **prevsnapshot**, aby określić wartości daty/godziny migawki w celu uzyskania różnica między. Jeśli ten parametr jest obecny, odpowiedź REST zawiera tylko strony, które zostały zmienione między migawki docelowej i poprzedniej migawki, w tym wyczyść stron.
* Użyj [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) Aby zastosować te zmiany do tworzenia kopii zapasowej stronicowych obiektów blob.
* Na koniec warto zrobić migawkę kopii zapasowej stronicowych obiektów blob i zapisz go na koncie magazynu kopii zapasowych.

W następnej sekcji firma Microsoft będzie szczegółowo opisano sposób umożliwiający obsługę kopii zapasowych dysków przy użyciu przyrostowej kopii migawki

## <a name="scenario"></a>Scenariusz
W tej sekcji opisano scenariusz, który obejmuje niestandardowe strategia tworzenia kopii zapasowych dysków maszyny wirtualnej przy użyciu migawek.

Należy wziąć pod uwagę maszyny Wirtualnej platformy Azure serii DS, razem z dyskiem premium storage P30 dołączone. Wywołuje się, dysk P30 *mypremiumdisk* znajduje się konto magazynu premium storage o nazwie *mypremiumaccount*. Konto magazynu w warstwie standardowa o nazwie *mybackupstdaccount* służy do przechowywania kopii zapasowej *mypremiumdisk*. Chcemy zapewnić migawkę *mypremiumdisk* co 12 godzin.

Aby dowiedzieć się więcej na temat tworzenia konta magazynu, zobacz [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Aby uzyskać informacje dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure, zapoznaj się [kopii zapasowych maszyn wirtualnych platformy Azure dla planu](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Kroki, aby zachowywać kopie zapasowe na dysku przy użyciu migawek przyrostowych
W poniższych krokach opisano sposób wykonania migawki *mypremiumdisk* i zachowywać kopie zapasowe w *mybackupstdaccount*. Kopia zapasowa jest standardowa stronicowy obiekt blob o nazwie *mybackupstdpageblob*. Kopii zapasowej stronicowych obiektów blob jest zawsze odzwierciedla takiego samego stanu, która jako ostatnia migawka o *mypremiumdisk*.

1. Tworzenie kopii zapasowej stronicowych obiektów blob dla dysku magazynu premium, wykonując migawkę *mypremiumdisk* o nazwie *mypremiumdisk_ss1*.
2. Skopiuj tę migawkę służącą do mybackupstdaccount jako stronicowy obiekt blob o nazwie *mybackupstdpageblob*.
3. Utwórz migawkę *mybackupstdpageblob* o nazwie *mybackupstdpageblob_ss1*przy użyciu [wykonanie migawki obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) i zapisać ją w *mybackupstdaccount*.
4. Podczas okna tworzenia kopii zapasowej, należy utworzyć migawkę innej *mypremiumdisk*, załóżmy, że *mypremiumdisk_ss2*i zapisz go w *mypremiumaccount*.
5. Pobieranie przyrostowych zmian między dwiema migawkami *mypremiumdisk_ss2* i *mypremiumdisk_ss1*przy użyciu [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) na *mypremiumdisk_ ss2* z **prevsnapshot** parametr wartość sygnatury czasowej *mypremiumdisk_ss1*. Zapisać tych zmian przyrostowych kopii zapasowych stronicowych obiektów blob *mybackupstdpageblob* w *mybackupstdaccount*. W przypadku usuniętej zakresów w zmiany przyrostowe, muszą zostać wyczyszczone z kopii zapasowej stronicowych obiektów blob. Użyj [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) do zapisywania zmian przyrostowych kopii zapasowych stronicowych obiektów blob.
6. Utworzenie migawki kopii zapasowej stronicowych obiektów blob *mybackupstdpageblob*, co jest nazywane *mybackupstdpageblob_ss2*. Usunąć poprzednią migawkę *mypremiumdisk_ss1* z konta usługi premium storage.
7. Powtórz kroki od 4 do 6 każdego okna tworzenia kopii zapasowej. W ten sposób można zachować kopie zapasowe *mypremiumdisk* na koncie magazynu w warstwie standardowa.

![Tworzenie kopii zapasowej dysku przy użyciu migawek przyrostowych](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Kroki w celu przywrócenia dysku z migawki
W poniższych krokach opisano sposób przywracania dysku w warstwie premium *mypremiumdisk* do wcześniejszej migawki z konta magazynu kopii zapasowych *mybackupstdaccount*.

1. Identyfikowania punktu w czasie, który chcesz przywrócić dysk premium. Załóżmy, że jej migawki *mybackupstdpageblob_ss2*, który jest przechowywany na koncie magazynu kopii zapasowych *mybackupstdaccount*.
2. W mybackupstdaccount, podwyższanie poziomu migawki *mybackupstdpageblob_ss2* jako nowy obiekt blob kopii zapasowej strony podstawowej *mybackupstdpageblobrestored*.
3. Utworzenie migawki tej przywróconej kopii zapasowej stronicowych obiektów blob, o nazwie *mybackupstdpageblobrestored_ss1*.
4. Skopiuj przywróconej stronicowych obiektów blob *mybackupstdpageblobrestored* z *mybackupstdaccount* do *mypremiumaccount* jako nowego dysku w warstwie premium  *mypremiumdiskrestored*.
5. Utwórz migawkę *mypremiumdiskrestored*, co jest nazywane *mypremiumdiskrestored_ss1* składania przyszłych przyrostowych kopii zapasowych.
6. Wskaż serię DS, maszyny Wirtualnej przywróconego dysku *mypremiumdiskrestored* i odłączanie starego *mypremiumdisk* z maszyny Wirtualnej.
7. Rozpocznij proces tworzenia kopii zapasowej opisanych w poprzedniej sekcji dotyczącej przywróconego dysku *mypremiumdiskrestored*przy użyciu *mybackupstdpageblobrestored* jako kopii zapasowej stronicowych obiektów blob.

![Przywracanie dysku z migawki](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Następne kroki
Użyj następujących linków, aby dowiedzieć się więcej na temat tworzenia migawek obiektu blob i Planowanie infrastruktury kopii zapasowej maszyny Wirtualnej.

* [Trwa tworzenie migawki obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planowanie infrastruktury kopii zapasowej maszyny Wirtualnej](../articles/backup/backup-azure-vms-introduction.md)


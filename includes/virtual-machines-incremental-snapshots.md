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
ms.openlocfilehash: f30518c3bfc9876cbddaf8295ff9e8b667a70200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74014549"
---
## <a name="overview"></a>Omówienie
Usługa Azure Storage udostępnia możliwość robienia migawek obiektów blob. Migawki przechwytywania stanu obiektu blob w tym momencie. W tym artykule opisano scenariusz, w którym można obsługiwać kopie zapasowe dysków maszyn wirtualnych przy użyciu migawek. Tej metodologii można użyć, gdy zdecydujesz się nie używać usługi Azure Backup and Recovery Service i chcesz utworzyć niestandardową strategię tworzenia kopii zapasowych dla dysków maszyn wirtualnych.

Dyski maszyn wirtualnych platformy Azure są przechowywane jako obiekty blob stron w usłudze Azure Storage. Ponieważ opisujemy strategię tworzenia kopii zapasowych dla dysków maszyn wirtualnych w tym artykule, odnosimy się do migawek w kontekście obiektów blob strony. Aby dowiedzieć się więcej o migawkach, zobacz [Tworzenie migawki obiektu blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Co to jest migawka?
Migawka obiektu blob jest tylko do odczytu wersja obiektu blob, który jest przechwytywany w punkcie w czasie. Po utworzeniu migawki można ją odczytać, skopiować lub usunąć, ale nie zmodyfikować. Migawki umożliwiają tworzenie kopii zapasowej obiektu blob, gdy pojawia się w momencie w czasie. Do wersji REST 2015-04-05, można było skopiować pełne migawki. W wersji REST 2015-07-08 lub nowszej można również kopiować migawki przyrostowe.

## <a name="full-snapshot-copy"></a>Kopia pełnej migawki
Migawki mogą być kopiowane do innego konta magazynu jako obiekt blob, aby zachować kopie zapasowe podstawowego obiektu blob. Można również skopiować migawkę za pomocą podstawowego obiektu blob, który jest jak przywracanie obiektu blob do wcześniejszej wersji. Gdy migawka jest kopiowana z jednego konta magazynu do innego, zajmuje to samo miejsce co obiekt blob strony podstawowej. W związku z tym kopiowanie całych migawek z jednego konta magazynu do innego jest powolne i zużywa dużo miejsca na koncie magazynu docelowego.

> [!NOTE]
> Jeśli kopiujesz podstawowy obiekt blob do innego miejsca docelowego, migawki obiektu blob nie są kopiowane wraz z nim. Podobnie, jeśli zastąpisz podstawowy obiekt blob kopią, migawki skojarzone z podstawowym obiektem blob nie zostaną naruszone i pozostaną nienaruszone pod podstawową nazwą obiektu blob.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Tworzenie kopii zapasowych dysków przy użyciu migawek
Jako strategia tworzenia kopii zapasowych dla dysków maszyn wirtualnych można wykonać okresowe migawki dysku lub stronicowego obiektu blob i skopiować je na inne konto magazynu przy użyciu narzędzi, takich jak kopiowanie operacji [obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) lub [AzCopy](../articles/storage/common/storage-use-azcopy.md). Migawkę można skopiować do docelowego obiektu blob strony o innej nazwie. Wynikowy obiekt blob strony docelowej jest zapisywalny obiekt blob strony, a nie migawka. W dalszej części tego artykułu opisano kroki do wykonywania kopii zapasowych dysków maszyn wirtualnych przy użyciu migawek.

### <a name="restore-disks-using-snapshots"></a>Przywracanie dysków przy użyciu migawek
Gdy nadszedł czas, aby przywrócić dysk do stabilnej wersji, która została wcześniej przechwycona w jednej z migawek kopii zapasowej, można skopiować migawkę za pomocą obiektu blob strony podstawowej. Po migawka jest promowany do obiektu blob strony podstawowej, migawka pozostaje, ale jego źródło jest zastępowane z kopią, która może być zarówno odczytywane, jak i zapisywane. W dalszej części tego artykułu opisano kroki, aby przywrócić poprzednią wersję dysku z jego migawki.

### <a name="implementing-full-snapshot-copy"></a>Implementowanie pełnej kopii migawki
Pełną kopię migawki można zaimplementować, wykonując następujące czynności,

* Najpierw zrób migawkę podstawowego obiektu blob przy użyciu operacji [obiektu blob migawki.](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob)
* Następnie skopiuj migawkę do docelowego konta magazynu przy użyciu [funkcji Kopiuj obiekt blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Powtórz ten proces, aby zachować kopie zapasowe podstawowego obiektu blob.

## <a name="incremental-snapshot-copy"></a>Kopia migawki przyrostowej
Nowa funkcja w interfejsie API [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) zapewnia znacznie lepszy sposób tworzenie kopii zapasowych migawek obiektów blob lub dysków strony. Interfejs API zwraca listę zmian między podstawowym obiektem blob a migawkami, co zmniejsza ilość miejsca używanego na koncie kopii zapasowej. Interfejs API obsługuje obiekty blob stron w magazynie w wersji Premium oraz w magazynie standardowym. Za pomocą tego interfejsu API można tworzyć szybsze i bardziej wydajne rozwiązania do tworzenia kopii zapasowych dla maszyn wirtualnych platformy Azure. Ten interfejs API będzie dostępny w wersji REST 2015-07-08 i nowszej.

Przyrostowa kopia migawki umożliwia kopiowanie z jednego konta magazynu do drugiego różnicy między,

* Podstawowy obiekt blob i jego migawka OR
* Wszystkie dwie migawki podstawowego obiektu blob

Pod warunkiem spełnienia następujących warunków,

* Obiekt blob został utworzony w styczniu 1-2016 lub później.
* Obiekt blob nie został zastąpiony za pomocą [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) lub [Kopiuj obiekt blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) między dwoma migawkami.

**Uwaga:** Ta funkcja jest dostępna dla obiektów blob premium i standardowej platformy Azure.

Jeśli masz niestandardową strategię tworzenia kopii zapasowych przy użyciu migawek, kopiowanie migawek z jednego konta magazynu do innego może być powolne i może zużywać dużo miejsca. Zamiast kopiować całą migawkę do konta magazynu kopii zapasowej, można zapisać różnicę między kolejnymi migawkami do obiektu blob strony kopii zapasowej. W ten sposób znacznie skrócić czas kopiowania i miejsca do przechowywania kopii zapasowych.

### <a name="implementing-incremental-snapshot-copy"></a>Implementowanie kopii migawki przyrostowej
Kopię migawki przyrostowej można zaimplementować, wykonując następujące czynności,

* Zrób migawkę podstawowego obiektu blob przy użyciu [obiektu Blob migawki](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Skopiuj migawkę do docelowego konta magazynu kopii zapasowej w tym samym lub innym regionie platformy Azure przy użyciu [funkcji Kopiuj obiekt blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Jest to obiekt blob strony kopii zapasowej. Wykonaj migawkę obiektu blob strony kopii zapasowej i zapisz ją na koncie kopii zapasowej.
* Zrób kolejną migawkę podstawowego obiektu blob przy użyciu obiektu blob migawki.
* Pobierz różnicę między pierwszą i drugą migawką podstawowego obiektu blob za pomocą [programu GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Użyj nowego parametru **prevsnapshot**, aby określić datetime wartość migawki, które mają uzyskać różnicę z. Gdy ten parametr jest obecny, odpowiedź REST zawiera tylko strony, które zostały zmienione między migawką docelową i poprzedniej migawki, w tym strony clear.
* Użyj [PutPage,](https://docs.microsoft.com/rest/api/storageservices/Put-Page) aby zastosować te zmiany do obiektu blob strony kopii zapasowej.
* Na koniec wykonaj migawkę obiektu blob strony kopii zapasowej i zapisz ją na koncie magazynu kopii zapasowej.

W następnej sekcji opiszemy bardziej szczegółowo, w jaki sposób można obsługiwać kopie zapasowe dysków przy użyciu przyrostowej kopii migawki

## <a name="scenario"></a>Scenariusz
W tej sekcji opisano scenariusz, który obejmuje niestandardową strategię tworzenia kopii zapasowych dla dysków maszyn wirtualnych przy użyciu migawek.

Należy wziąć pod uwagę maszyny Wirtualnej platformy Azure serii DS z dołączonym dyskiem P30 magazynu w wersji premium. Dysk P30 o nazwie *mypremiumdisk* jest przechowywany na koncie pamięci masowej premium o nazwie *mypremiumaccount*. Standardowe konto magazynu o nazwie *mybackupstdaccount* służy do przechowywania kopii zapasowej *mypremiumdisk*. Chcielibyśmy, aby migawka *mypremiumdisk* co 12 godzin.

Aby dowiedzieć się więcej o tworzeniu konta magazynu, zobacz [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Aby dowiedzieć się więcej na temat tworzenia kopii zapasowych maszyn wirtualnych platformy Azure, zobacz [Planowanie kopii zapasowych maszyn wirtualnych platformy Azure.](../articles/backup/backup-azure-vms-introduction.md)

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Kroki obsługi kopii zapasowych dysku przy użyciu migawek przyrostowych
W poniższych krokach opisano, jak robić migawki *mypremiumdisk* i utrzymywać kopie zapasowe w *mybackupstdaccount*. Kopia zapasowa jest standardową stroną blob o nazwie *mybackupstdpageblob*. Obiekt blob strony kopii zapasowej zawsze odzwierciedla ten sam stan, co ostatnia migawka *programu mypremiumdisk*.

1. Utwórz obiekt blob strony kopii zapasowej dla dysku magazynu premium, robiąc migawkę *mypremiumdisk* o nazwie *mypremiumdisk_ss1*.
2. Skopiuj tę migawkę do mybackupstdaccount jako obiekt blob strony o nazwie *mybackupstdpageblob*.
3. Zrób migawkę *mybackupstdpageblob* o nazwie *mybackupstdpageblob_ss1*, za pomocą [Migawki Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) i przechowywać go w *mybackupstdaccount*.
4. Podczas okna kopii zapasowej, utworzyć kolejną migawkę *mypremiumdisk*, *powiedzmy mypremiumdisk_ss2*, i przechowywać go w *mypremiumaccount*.
5. Pobierz przyrostowe zmiany między dwoma migawkami, *mypremiumdisk_ss2* i *mypremiumdisk_ss1*, używając [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) na *mypremiumdisk_ss2* z parametrem **prevsnapshot** ustawionym na sygnaturę czasową *mypremiumdisk_ss1*. Wpisz te przyrostowe zmiany w obiekcie blob strony kopii zapasowej *mybackupstdpageblob* w *mybackupstdaccount*. Jeśli są usunięte zakresy w przyrostowe zmiany, muszą być wyczyszczone z obiektu blob strony kopii zapasowej. Użyj [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) do zapisywania przyrostowych zmian w obiekcie blob strony kopii zapasowej.
6. Zrób migawkę strony kopii zapasowej obiektu blob *mybackupstdpageblob*, o nazwie *mybackupstdpageblob_ss2*. Usuń poprzednią migawkę *mypremiumdisk_ss1* z konta magazynu w wersji Premium.
7. Powtórz kroki 4-6 każde okno kopii zapasowej. W ten sposób można zachować kopie zapasowe *mypremiumdisk* na standardowym koncie magazynu.

![Tworzenie kopii zapasowej dysku przy użyciu migawek przyrostowych](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Kroki przywracania dysku z migawek
W poniższych krokach opisano sposób przywracania dysku premium, *mypremiumdisk* do wcześniejszej migawki z konta magazynu kopii zapasowej *mybackupstdaccount*.

1. Zidentyfikuj punkt w czasie, do którego chcesz przywrócić dysk premium. Załóżmy, że jest to migawka *mybackupstdpageblob_ss2*, która jest przechowywana na koncie przechowywania kopii zapasowej *mybackupstdaccount*.
2. W mybackupstdaccount, promuj migawkę *mybackupstdpageblob_ss2* jako nowy podstawowy obiekt blob strony zapasowej *mybackupstdpageblobrestored*.
3. Zrób migawkę tego przywróconego obiektu blob strony kopii zapasowej, zwanego *mybackupstdpageblobrestored_ss1*.
4. Skopiuj przywrócone strony blob *mybackupstdpageblobrestored* z *mybackupstdaccount* do *mypremiumaccount* jako nowy dysk premium *mypremiumdiskrestored*.
5. Zrób migawkę *mypremiumdiskrestored*, o nazwie *mypremiumdiskrestored_ss1* do tworzenia przyszłych przyrostowych kopii zapasowych.
6. Skieruj maszynę wirtualną z serii DS na przywrócony dysk *mypremiumdiskretored* i odłącz stary *dysk mypremiumdisk* od maszyny Wirtualnej.
7. Rozpocznij proces tworzenia kopii zapasowej opisany w poprzedniej sekcji dla przywróconego dysku *mypremiumdiskrestored*, używając *mybackupstdpageblobrestored* jako elementu blob strony kopii zapasowej.

![Przywracanie dysku z migawek](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Następne kroki
Skorzystaj z poniższych łączy, aby dowiedzieć się więcej na temat tworzenia migawek obiektu blob i planowania infrastruktury kopii zapasowej maszyny Wirtualnej.

* [Tworzenie migawki obiektu blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planowanie infrastruktury kopii zapasowych maszyn wirtualnych](../articles/backup/backup-azure-vms-introduction.md)


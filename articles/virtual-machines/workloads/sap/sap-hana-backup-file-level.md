---
title: SAP HANA Azure Backup na poziomie pliku | Microsoft Docs
description: Istnieją dwie istotne możliwości tworzenia kopii zapasowych SAP HANA na maszynach wirtualnych platformy Azure, w tym artykule omówiono SAP HANA Azure Backup na poziomie pliku
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 86a0633a433623c2b43bb26721e5fcee08d4301f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640813"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup na poziomie pliku

## <a name="introduction"></a>Wprowadzenie

Jest to część szeregu powiązanych artykułów w SAP HANA kopii zapasowej. [Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines](./sap-hana-backup-guide.md) zawiera omówienie i informacje na temat rozpoczynania pracy, a [SAP HANA tworzenia kopii zapasowej na podstawie migawek magazynu](./sap-hana-backup-storage-snapshots.md) obejmują opcję tworzenia kopii zapasowej opartej na migawce magazynu.

Różne typy maszyn wirtualnych na platformie Azure umożliwiają dołączenie innej liczby wirtualnych dysków twardych. Dokładne szczegóły są udokumentowane w [rozmiarach maszyn wirtualnych z systemem Linux na platformie Azure](../../linux/sizes.md). W przypadku testów, do których odwołuje się ta dokumentacja, używana jest maszyna wirtualna platformy Azure GS5, która umożliwia 64 dołączonych dysków danych. W przypadku większych systemów SAP HANA można już wykonać znaczną liczbę dysków dla plików danych i dziennika, prawdopodobnie w połączeniu z rozłożeniem oprogramowania w celu zapewnienia optymalnej przepływności we/wy dysku. Aby uzyskać więcej informacji na temat sugerowanych konfiguracji dysków dla wdrożeń SAP HANA na maszynach wirtualnych platformy Azure, zapoznaj się z artykułem [SAP HANA w Przewodniku obsługi platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Zalecenia zawarte w programie obejmują również zalecenia dotyczące miejsca na dysku dla lokalnych kopii zapasowych.

W tej chwili usługa Azure Backup nie ma integracji z SAP HANAą kopii zapasowej. Standardową metodą zarządzania wykonywaniem kopii zapasowych/przywracania na poziomie plików jest tworzenie kopii zapasowej opartej na plikach za pośrednictwem SAP HANA Studio lub SAP HANA instrukcji SQL. Więcej informacji można znaleźć w temacie [SAP HANA SQL i informacje o widokach systemu](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) .

![Ten rysunek przedstawia okno dialogowe elementu menu kopia zapasowa w programie SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Ten rysunek przedstawia okno dialogowe elementu menu kopia zapasowa w programie SAP HANA Studio. Po wybraniu &quot;typu plik&quot; , jeden musi określać ścieżkę w systemie plików, gdzie SAP HANA zapisuje pliki kopii zapasowej. Przywracanie działa w ten sam sposób.

Chociaż to rozwiązanie jest proste i proste w przód, istnieją pewne zagadnienia. Jak wspomniano wcześniej, maszyna wirtualna platformy Azure ma ograniczenie liczby dysków z danymi, które można dołączać. Przechowywanie plików SAP HANA kopii zapasowych w systemach plików maszyny wirtualnej może nie być możliwe, w zależności od rozmiaru bazy danych i wymagań dotyczących przepływności dysku, co może wiązać się z rozłożeniem oprogramowania na wielu dyskach danych. W dalszej części tego artykułu są dostępne różne opcje przeniesienia tych plików kopii zapasowej oraz zarządzanie ograniczeniami rozmiaru plików i wydajnością podczas obsługi terabajtów danych.

Kolejną opcją, która oferuje większą swobodę dotyczącą całkowitej pojemności, jest usługa Azure Blob Storage. Chociaż pojedynczy obiekt BLOB jest również ograniczony do 1 TB, całkowita pojemność pojedynczego kontenera obiektów BLOB jest obecnie 500 TB. Dodatkowo umożliwia klientom wybór &quot;tego, czy chłodny&quot; magazyn obiektów blob, który ma korzystny koszt. Zobacz [BLOB Storage platformy Azure: Gorąca i chłodna warstwa](../../../storage/blobs/storage-blob-storage-tiers.md) magazynowania, aby uzyskać szczegółowe informacje na temat magazynu chłodnych obiektów BLOB.

Aby zachować dodatkowe bezpieczeństwo, użyj konta magazynu zreplikowanego geograficznie do przechowywania kopii zapasowych SAP HANA. Aby uzyskać szczegółowe informacje na temat replikacji konta magazynu, zobacz [Replikacja usługi Azure Storage](../../../storage/common/storage-redundancy.md) .

Jeden z nich może umieścić dedykowane wirtualne dyski twarde dla SAP HANA kopii zapasowych na dedykowanym koncie magazynu kopii zapasowej, które jest replikowane geograficznie. W przeciwnym razie można skopiować wirtualne dyski twarde, które przechowują kopie zapasowe SAP HANA na koncie magazynu z replikacją geograficzną, lub na konto magazynu znajdujące się w innym regionie.

## <a name="azure-backup-agent"></a>Agent usługi Azure Backup

Usługa Azure Backup oferuje opcję nie tylko tworzenia kopii zapasowych kompletnych maszyn wirtualnych, ale także plików i katalogów za pośrednictwem agenta kopii zapasowych, który należy zainstalować w systemie operacyjnym gościa. Jednak ten Agent jest obsługiwany tylko w systemie Windows (zobacz [Tworzenie kopii zapasowej systemu Windows Server lub klienta na platformie Azure przy użyciu modelu wdrażania Menedżer zasobów](../../../backup/backup-configure-vault.md)).

Obejście polega na pierwszym skopiowaniu plików SAP HANA kopii zapasowej na maszynę wirtualną z systemem Windows na platformie Azure (na przykład za pośrednictwem usługi SAMBA Share), a następnie za pomocą agenta usługi Azure Backup. Chociaż jest to technicznie możliwe, spowodowałoby to dodanie złożoności i spowolnienie procesu tworzenia kopii zapasowej lub przywracania z powodu kopiowania między maszyną wirtualną systemu Linux i systemem Windows. Nie zaleca się przestrzegania tego podejścia.

## <a name="azure-blobxfer-utility-details"></a>Szczegóły narzędzia Azure blobxfer

Przechowywanie katalogów i plików w usłudze Azure Storage może być możliwe za pomocą interfejsu wiersza polecenia lub programu PowerShell lub opracowywania narzędzia przy użyciu jednego z [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/). Do kopiowania danych do usługi Azure Storage jest również dostępne gotowe do użycia narzędzie AzCopy. (zobacz [transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../../../storage/common/storage-use-azcopy.md)).

W związku z tym blobxfer był używany do kopiowania plików kopii zapasowej SAP HANA. Jest to środowisko Open Source używane przez wielu klientów w środowiskach produkcyjnych i dostępne w serwisie [GitHub](https://github.com/Azure/blobxfer). To narzędzie umożliwia jednemu skopiowaniu danych bezpośrednio do usługi Azure Blob Storage lub udziału plików platformy Azure. Oferuje również szereg przydatnych funkcji, takich jak skrót MD5 lub automatyczna równoległość podczas kopiowania katalogu z wieloma plikami.

## <a name="sap-hana-backup-performance"></a>Wydajność kopii zapasowych SAP HANA

![Ten zrzut ekranu jest SAP HANA konsoli zapasowej w programie SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Ten zrzut ekranu jest SAP HANA konsoli tworzenia kopii zapasowych w programie SAP HANA Studio. Wykonanie kopii zapasowej 230 GB na pojedynczym dysku magazynu platformy Azure w warstwie Standardowa, które zostało dołączone do maszyny wirtualnej HANA przy użyciu systemu plików XFS, zajęło około 42 minut.

![Ten zrzut ekranu jest YaST SAP HANA na testowej maszynie wirtualnej](media/sap-hana-backup-file-level/image024.png)

Ten zrzut ekranu jest YaST SAP HANA na testowej maszynie wirtualnej. Jeden z nich może zobaczyć pojedynczy dysk 1 TB dla SAP HANA kopii zapasowej, jak wspomniano wcześniej. Wykonanie kopii zapasowej 230 GB trwało około 42 minut. Ponadto dyski 5 200-GB zostały dołączone i utworzono oprogramowanie RAID MD0 w systemie, w którym znajduje się na tym pięciu dyskach danych platformy Azure.

![Powtarzanie tej samej kopii zapasowej na platformie RAID oprogramowania z rozłożonymi na pięć dołączonych dysków z danymi usługi Azure Standard Storage](media/sap-hana-backup-file-level/image025.png)

Powtarzanie tej samej kopii zapasowej na woluminie RAID oprogramowania z rozłożonymi na pięć podłączonych dysków z danymi magazynu w warstwie Standardowa platformy Azure spowodowało czas wykonywania kopii zapasowej od 42 minut do 10 minut. Dyski zostały dołączone bez buforowania do maszyny wirtualnej. Jest to oczywiste, jak ważna przepustowość zapisu na dysku jest dla czasu wykonywania kopii zapasowej. Następnie można przełączyć się na platformę Azure Premium Storage, aby jeszcze bardziej przyspieszyć proces optymalnej wydajności. Ogólnie rzecz biorąc, usługa Azure Premium Storage powinna być używana w systemach produkcyjnych.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopiowanie plików SAP HANA kopii zapasowej do usługi Azure Blob Storage

Innym rozwiązaniem jest szybkie przechowywanie SAP HANA plików kopii zapasowej w usłudze Azure Blob Storage. Jeden kontener obiektów BLOB ma limit 500 TB, wystarczający dla niektórych mniejszych SAP HANA systemów, przy użyciu typów maszyn wirtualnych M32ts, M32ls, M64ls i GS5 na platformie Azure, aby zachować wystarczającą SAP HANA kopii zapasowych. Klienci mają możliwość wyboru między &quot;gorącą &quot;&quot; i&quot; zimną magazynem [obiektów BLOB (zobacz BLOB Storage platformy Azure: Gorąca i chłodna warstwa](../../../storage/blobs/storage-blob-storage-tiers.md)magazynu).

Za pomocą narzędzia blobxfer można łatwo kopiować pliki kopii zapasowej SAP HANA bezpośrednio do usługi Azure Blob Storage.

![W tym miejscu można zobaczyć pliki pełnej kopii zapasowej pliku SAP HANA](media/sap-hana-backup-file-level/image026.png)

W tym miejscu można zobaczyć pliki pełnej kopii zapasowej pliku SAP HANA. Istnieją cztery pliki, a największy z nich ma około 230 GB.

![Kopiowanie 230 GB do kontenera obiektów BLOB usługi Azure Standard Storage trwało około 3000 sekund](media/sap-hana-backup-file-level/image027.png)

Użycie skrótu MD5 w teście wstępnym trwało około 3000 sekund na skopiowanie 230 GB do kontenera obiektów BLOB konta magazynu w warstwie Standardowa Azure.

![Na tym zrzucie ekranu można zobaczyć, jak wygląda na Azure Portal](media/sap-hana-backup-file-level/image028.png)

Na tym zrzucie ekranu można zobaczyć, jak wygląda na Azure Portal. Kontener obiektów BLOB o &quot;nazwie SAP-HANA —&quot; kopie zapasowe został utworzony i zawiera cztery obiekty blob, które reprezentują SAP HANA pliki kopii zapasowej. Jeden z nich ma rozmiar około 230 GB.

Konsola tworzenia kopii zapasowych HANA Studio pozwala na ograniczenie maksymalnego rozmiaru plików kopii zapasowych HANA. W przykładowym środowisku Ulepszona wydajność, dzięki czemu możliwe jest posiadanie wielu mniejszych plików kopii zapasowej zamiast jednego dużego pliku 230 GB.

![Ustawienie limitu rozmiaru pliku kopii zapasowej po stronie&#39;Hana nie wydłuża czasu wykonywania kopii zapasowej](media/sap-hana-backup-file-level/image029.png)

Ustawienie limitu rozmiaru pliku kopii zapasowej po stronie&#39;Hana nie wydłuża czasu wykonywania kopii zapasowej, ponieważ pliki są zapisywane sekwencyjnie, jak pokazano na poniższej ilustracji. Limit rozmiaru pliku został ustawiony na 60 GB, więc kopia zapasowa stworzyła cztery duże pliki danych zamiast jednego pliku 230-GB. Korzystanie z wielu plików kopii zapasowej to konieczność tworzenia kopii zapasowych baz danych HANA, które wykorzystują pamięć większych maszyn wirtualnych platformy Azure, takich jak M64s, M64ms, M128s i M128ms.

![Aby przetestować równoległość narzędzia blobxfer, maksymalny rozmiar plików dla kopii zapasowych HANA został następnie ustawiony na 15 GB](media/sap-hana-backup-file-level/image030.png)

Aby przetestować równoległość narzędzia blobxfer, maksymalny rozmiar plików dla kopii zapasowych HANA został następnie ustawiony na 15 GB, co spowodowało 19 plików kopii zapasowej. Ta konfiguracja umożliwia blobxfer do kopiowania 230 GB do magazynu obiektów blob platformy Azure z 3000 sekund do 875 sekund.

Wynika to z limitu 60 MB/s podczas pisania obiektu blob platformy Azure. Równoległość za pośrednictwem wielu obiektów BLOB rozwiązuje wąskie gardło, ale istnieje Minusem: zwiększenie wydajności narzędzia blobxfer w celu skopiowania wszystkich plików kopii zapasowych HANA do magazynu obiektów blob platformy Azure powoduje obciążenie zarówno maszyny wirtualnej HANA, jak i sieci. Ma to wpływ na działanie systemu HANA.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Kopia obiektów BLOB dedykowanych dysków danych platformy Azure w oprogramowaniu RAID tworzenia kopii zapasowych

W przeciwieństwie do kopii zapasowej ręcznego dysku danych maszyny wirtualnej, w tym przypadku nie tworzy kopii zapasowej wszystkich dysków danych na maszynie wirtualnej w celu zapisania całej instalacji SAP, w tym danych platformy HANA, plików dziennika HANA i plików konfiguracji. Zamiast tego pomysłem jest posiadanie dedykowanego oprogramowania RAID z rozłożonymi na wiele wirtualnych dysków twardych danych platformy Azure do przechowywania pełnej kopii zapasowej plików SAP HANA. Jeden kopiuje tylko te dyski, na których jest SAP HANA kopia zapasowa. Mogą one być łatwo przechowywane w dedykowanym koncie magazynu kopii zapasowych Hana lub dołączone do dedykowanej &quot;maszyny wirtualnej&quot; do zarządzania kopiami zapasowymi w celu dalszej obróbki.

![Wszystkie objęte dyski VHD zostały skopiowane przy użyciu polecenia * * Start-azurestorageblobcopy * * środowiska PowerShell](media/sap-hana-backup-file-level/image031.png)

Po zakończeniu wykonywania kopii zapasowej na lokalnym oprogramowaniu RAID wszystkie środowiska zostały skopiowane za pomocą polecenia programu PowerShell **Start-azurestorageblobcopy** (patrz polecenie [Start-azurestorageblobcopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Ponieważ ma to wpływ tylko na dedykowany system plików do przechowywania plików kopii zapasowej, nie ma żadnych problemów dotyczących spójności SAP HANA danych lub plików dziennika na dysku. Zaletą tego polecenia jest to, że działa ono, gdy maszyna wirtualna pozostaje w trybie online. Aby upewnić się, że żaden proces nie zapisuje do zestawu pasków kopii zapasowych, należy go odinstalować przed skopiowaniem obiektu BLOB, a następnie zainstalować ponownie. Lub jeden może użyć odpowiedniego sposobu na &quot;zablokowanie&quot; systemu plików. Na przykład za pośrednictwem\_XFS Zablokuj system plików XFS.

![Ten zrzut ekranu przedstawia listę obiektów BLOB w kontenerze VHD na Azure Portal](media/sap-hana-backup-file-level/image032.png)

Ten zrzut ekranu przedstawia listę obiektów BLOB w &quot;kontenerze VHD&quot; na Azure Portal. Zrzut ekranu przedstawia pięć wirtualnych dysków twardych, które zostały dołączone do maszyny wirtualnej serwera SAP HANA, aby obsłużyć oprogramowanie RAID do przechowywania SAP HANA plików kopii zapasowej. Przedstawiono w nim również pięć kopii, które zostały wykonane za pomocą polecenia BLOB Copy.

![W celach testowych kopie SAP HANA dysków RAID z oprogramowaniem kopii zapasowej zostały dołączone do maszyny wirtualnej serwera aplikacji](media/sap-hana-backup-file-level/image033.png)

Do celów testowych kopia SAP HANA dysków RAID z oprogramowaniem do tworzenia kopii zapasowych została dołączona do maszyny wirtualnej serwera aplikacji.

![Maszyna wirtualna serwera aplikacji została wyłączona w celu dołączenia kopii dysku](media/sap-hana-backup-file-level/image034.png)

Maszyna wirtualna serwera aplikacji została wyłączona w celu dołączenia kopii dysków. Po uruchomieniu maszyny wirtualnej dyski i wolumin RAID zostały wykryte prawidłowo (są one instalowane za pośrednictwem identyfikatora UUID). Brak tylko punktu instalacji, który został utworzony za pośrednictwem partycji YaST. Następnie SAP HANA kopie zapasowe plików są widoczne na poziomie systemu operacyjnego.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopiowanie plików kopii zapasowej SAP HANA do udziału NFS

Aby zmniejszyć potencjalny wpływ na system SAP HANA z perspektywy wydajności lub miejsca na dysku, jedna z nich może rozważyć przechowywanie SAP HANA plików kopii zapasowej w udziale NFS. Technicznie działa, ale oznacza użycie drugiej maszyny wirtualnej platformy Azure jako hosta udziału NFS. Nie powinna to być mała wielkość maszyny wirtualnej ze względu na przepustowość sieci maszyny wirtualnej. Warto wypróbować tę &quot;maszynę wirtualną&quot; kopii zapasowej i tylko ją utworzyć w celu wykonywania kopii zapasowej SAP HANA. Zapis w udziale NFS powoduje obciążenie sieci i ma wpływ na system SAP HANA, ale tylko zarządzanie plikami kopii zapasowej w późniejszym czasie na &quot;maszynie wirtualnej&quot; kopii zapasowej nie wpłynie na system SAP HANA.

![Udział w systemie plików NFS z innej maszyny wirtualnej platformy Azure został zainstalowany na maszynie wirtualnej serwera SAP HANA](media/sap-hana-backup-file-level/image035.png)

Aby zweryfikować przypadek użycia systemu plików NFS, udział NFS z innej maszyny wirtualnej platformy Azure został zainstalowany na maszynie wirtualnej serwera SAP HANA. Nie zastosowano specjalnych strojenia systemu plików NFS.

![Wykonanie kopii zapasowej trwało 1 godzinę i 46 minut](media/sap-hana-backup-file-level/image036.png)

Udział NFS był szybkim zestawem, na przykład na serwerze SAP HANA. Niemniej jednak trwało 1 godzinę i 46 minut, aby utworzyć kopię zapasową bezpośrednio w udziale NFS, a nie 10 minut, podczas zapisywania do lokalnego zestawu paskowego.

![Alternatywa nie była znacznie szybsza od 1 godziny i 43 minut](media/sap-hana-backup-file-level/image037.png)

Alternatywne o wykonaniu kopii zapasowej do lokalnego rozłożony i kopiowanie do udziału NFS na poziomie systemu operacyjnego (prosty **cp-avr** polecenia) nie została znacznie szybciej. Zajęło 1 i godz.

Dlatego działa, ale wydajność nie była dobra dla testu kopii zapasowych 230 GB. Będzie wyglądać nawet gorsze dla wieloterabajtów.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopiowanie SAP HANA plików kopii zapasowej do Azure Files

Możliwe jest zainstalowanie udziału Azure Files w ramach maszyny wirtualnej z systemem Linux na platformie Azure. W tym artykule [opisano sposób korzystania z usługi Azure File Storage z systemem Linux](../../../storage/files/storage-how-to-use-files-linux.md) . Należy pamiętać, że istnieje limit przydziału 5 TB dla jednego udziału plików platformy Azure, a limit rozmiaru pliku wynoszący 1 TB na plik. Aby uzyskać informacje o limitach magazynu [, zobacz cele dotyczące skalowalności i wydajności usługi Azure Storage](../../../storage/common/storage-scalability-targets.md) .

Testy, które wykazały, że SAP HANA Backup&#39;nie działają obecnie bezpośrednio z tym rodzajem instalacji CIFS. Jest również określony w programie [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529) , że CIFS nie jest zalecane.

![Ten rysunek przedstawia błąd w oknie dialogowym tworzenia kopii zapasowej w programie SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Ten rysunek przedstawia błąd w oknie dialogowym tworzenia kopii zapasowej w programie SAP HANA Studio podczas próby utworzenia kopii zapasowej bezpośrednio do udziału plików platformy Azure zainstalowanego przy użyciu protokołu CIFS. W związku z tym należy najpierw wykonać SAP HANA kopię zapasową w systemie plików maszyny wirtualnej, a następnie skopiować pliki kopii zapasowej z tego miejsca do usługi plików platformy Azure.

![Na tym rysunku przedstawiono około 929 sekund na skopiowanie 19 SAP HANA plików kopii zapasowej](media/sap-hana-backup-file-level/image039.png)

Na tym rysunku przedstawiono około 929 sekund na kopiowanie 19 SAP HANA plików kopii zapasowej o całkowitym rozmiarze około 230 GB do udziału plików platformy Azure.

![Źródłowa struktura katalogów na maszynie wirtualnej SAP HANA została skopiowana do udziału plików platformy Azure](media/sap-hana-backup-file-level/image040.png)

Na tym zrzucie ekranu można zobaczyć, że źródłowa struktura katalogów na maszynie wirtualnej SAP HANA została skopiowana do udziału plików platformy Azure: jeden katalog (\_Hana\_Backup\_FSL 15gb) i 19 poszczególnych plików kopii zapasowej.

Przechowywanie SAP HANA plików kopii zapasowej w usłudze Azure Files może być ciekawą opcją w przyszłości, gdy SAP HANA kopie zapasowe plików są obsługiwane bezpośrednio. Lub kiedy będzie możliwe zainstalowanie usługi Azure Files za pośrednictwem systemu plików NFS, a maksymalny limit przydziału jest znacznie większy niż 5 TB.

## <a name="next-steps"></a>Następne kroki
* [Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines](sap-hana-backup-guide.md) zawiera omówienie i informacje na temat rozpoczynania pracy.
* [SAP HANA kopia zapasowa oparta na migawkach magazynu](sap-hana-backup-storage-snapshots.md) zawiera opis opcji tworzenia kopii zapasowej opartej na migawce magazynu.
* Aby dowiedzieć się, jak stworzyć wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).

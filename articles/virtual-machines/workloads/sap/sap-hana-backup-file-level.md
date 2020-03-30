---
title: SAP HANA Azure Backup na poziomie pliku | Dokumenty firmy Microsoft
description: Istnieją dwie główne możliwości tworzenia kopii zapasowych dla sap HANA na maszynach wirtualnych platformy Azure, w tym artykule omówiono SAP HANA Azure Backup na poziomie pliku
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271386"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Sap HANA Azure Backup na poziomie pliku

## <a name="introduction"></a>Wprowadzenie

Ten artykuł jest powiązany artykuł [do tworzenia kopii zapasowych przewodnik dla SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide), który zawiera omówienie i informacje na temat rozpoczynania pracy i więcej szczegółów na temat usługi Azure Backup i migawek magazynu. 

Różne typy maszyn wirtualnych na platformie Azure zezwalają na inną liczbę dołączonych wirtualnych maszyn wirtualnych. Dokładne szczegóły są udokumentowane w [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes). W przypadku testów, o których mowa w tej dokumentacji, użyliśmy maszyny Wirtualnej platformy Azure GS5, która zezwala na 64 dołączone dyski danych. W przypadku większych systemów SAP HANA znaczna liczba dysków może być już podjęta dla plików danych i dzienników, prawdopodobnie w połączeniu z rozbieraniem oprogramowania w celu uzyskania optymalnej przepustowości we/wy dysku. Aby uzyskać więcej informacji na temat sugerowanych konfiguracji dysków dla wdrożeń SAP HANA na maszynach wirtualnych platformy Azure, przeczytaj artykuł [Konfiguracje magazynu maszyn wirtualnych platformy SAP HANA Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) Zalecenia są zawarte zalecenia dotyczące miejsca na dysku dla lokalnych kopii zapasowych, jak również.

Standardowy sposób zarządzania tworzeniem kopii zapasowych/przywracania na poziomie pliku jest z kopią zapasową opartą na plikach za pośrednictwem SAP HANA Studio lub za pośrednictwem instrukcji SQL SAP HANA. Aby uzyskać więcej informacji, przeczytaj artykuł [SAP HANA SQL i system views reference](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf).

![Na rysunku przedstawiono okno dialogowe elementu menu kopii zapasowej w programie SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Na rysunku przedstawiono okno dialogowe elementu menu kopii zapasowej w programie SAP HANA Studio. Wybierając plik &quot;typu, trzeba określić ścieżkę w systemie plików,&quot; w którym SAP HANA zapisuje pliki kopii zapasowej. Przywracanie działa w ten sam sposób.

Chociaż ten wybór brzmi prosto i prosto do przodu, istnieją pewne względy. Maszyna wirtualna platformy Azure ma ograniczenie liczby dysków danych, które można dołączyć. Może nie być pojemność do przechowywania plików kopii zapasowych SAP HANA w systemach plików maszyny Wirtualnej, w zależności od rozmiaru bazy danych i wymagania dotyczące przepływności dysku, które mogą obejmować rozkładanie oprogramowania na wielu dyskach danych. W dalszej części tego artykułu przedstawiono różne opcje przenoszenia tych plików kopii zapasowych oraz zarządzania ograniczeniami rozmiaru pliku i wydajnością podczas obsługi terabajtów danych.

Inną opcją, która oferuje większą swobodę w zakresie całkowitej pojemności, jest magazyn obiektów blob platformy Azure. Podczas gdy pojedynczy obiekt blob jest również ograniczony do 1 TB, całkowita pojemność pojedynczego kontenera obiektów blob wynosi obecnie 500 TB. Ponadto daje klientom możliwość wyboru tak &quot;zwanego chłodnego&quot; magazynu obiektów blob, co ma korzyść kosztową. Zobacz [usługi Azure Blob storage: hot, cool i archiwum warstwy dostępu, aby](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal) uzyskać szczegółowe informacje na temat chłodnego magazynu obiektów blob.

Aby uzyskać dodatkowe bezpieczeństwo, użyj konta magazynu replikowanego geograficznie do przechowywania kopii zapasowych SAP HANA. Aby uzyskać szczegółowe informacje na temat nadmiarowości i replikacji magazynu, zobacz [nadmiarowość usługi Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Można umieścić dedykowane dyski VHD dla kopii zapasowych SAP HANA w dedykowanym koncie magazynu kopii zapasowych, które jest replikowane geograficznie. Inaczej można skopiować veds, które przechowują kopie zapasowe SAP HANA do konta magazynu replikowanego geograficznie lub do konta magazynu, który znajduje się w innym regionie.

## <a name="azure-blobxfer-utility-details"></a>Szczegóły narzędzia usługi Azure blobxfer

Aby przechowywać katalogi i pliki w magazynie platformy Azure, można użyć interfejsu wiersza polecenia lub programu PowerShell lub opracować narzędzie przy użyciu jednego z [zestawów SDK platformy Azure.](https://azure.microsoft.com/downloads/) Istnieje również gotowe do użycia narzędzie AzCopy do kopiowania danych do magazynu platformy Azure. (patrz [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy).](../../../storage/common/storage-use-azcopy.md)

W związku z tym blobxfer był używany do kopiowania plików kopii zapasowych SAP HANA. Jest open source, używany przez wielu klientów w środowiskach produkcyjnych i dostępny na [GitHub.](https://github.com/Azure/blobxfer) To narzędzie umożliwia kopiowanie danych bezpośrednio do magazynu obiektów blob platformy Azure lub udziału plików platformy Azure. Oferuje również szereg przydatnych funkcji, takich jak skrót md5 lub automatyczny równoległość podczas kopiowania katalogu z wieloma plikami.

## <a name="sap-hana-backup-performance"></a>Wydajność tworzenia kopii zapasowych oprogramowania SAP HANA
W tym rozdziale omówione są zagadnienia dotyczące wydajności. Liczby osiągnięte nie może reprezentować najnowszy stan, ponieważ istnieje stały rozwój, aby osiągnąć lepszą przepływność do magazynu platformy Azure. W rezultacie należy wykonać indywidualne testy dla konfiguracji i regionu platformy Azure.

![Ten zrzut ekranu jest z konsoli kopii zapasowej SAP HANA w SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Ten zrzut ekranu przedstawia konsolę kopii zapasowej SAP HANA sap HANA Studio. Wykonanie kopii zapasowej 230 GB na jednym dysku magazynu partycji Azure Standard HDD podłączonego do maszyny Wirtualnej HANA przy użyciu systemu plików XFS na jednym dysku zajęło około 42 minut.

![Ten zrzut ekranu jest yast na SAP HANA test VM](media/sap-hana-backup-file-level/one-backup-disk.png)

Ten zrzut ekranu jest yast na maszynie wirtualnej testu SAP HANA. Możesz zobaczyć pojedynczy dysk o pojemności 1 TB dla kopii zapasowej SAP HANA. Zajęło około 42 minut, aby wykonać kopię zapasową 230 GB. Ponadto dołączono pięć dysków o 200 GB i stworzono program RAID md0, z rozkładaniem na tych pięciu dyskach z danymi platformy Azure.

![Powtarzanie tej samej kopii zapasowej na macierzy RAID oprogramowania z rozkładem na pięciu dołączonych dyskach danych magazynu standardowego platformy Azure](media/sap-hana-backup-file-level/five-backup-disks.png)

Powtarzanie tej samej kopii zapasowej na macierzy RAID oprogramowania z rozkładaniem na pięciu dołączonych dyskach danych magazynu standardowego platformy Azure spowodowało, że czas tworzenia kopii zapasowej z 42 minut do 10 minut. Dyski zostały dołączone bez buforowania do maszyny Wirtualnej. To ćwiczenie pokazuje znaczenie przepływności zapisu dysku dla osiągnięcia dobrego czasu tworzenia kopii zapasowej. Możesz przełączyć się na usługę Azure Standard SSD storage lub Azure Premium Storage, aby jeszcze bardziej przyspieszyć proces w celu uzyskania optymalnej wydajności. Ogólnie rzecz biorąc standardowy magazyn dysków twardych platformy Azure nie jest zalecane i był używany tylko do celów demonstracyjnych. Zaleca się użycie minimalnej usługi Azure Standard SSD storage lub usługi Azure Premium Storage dla systemów produkcyjnych.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopiowanie plików kopii zapasowych SAP HANA do magazynu obiektów blob platformy Azure
Numery wydajności, numery czasu trwania kopii zapasowej i numery czasu trwania kopiowania wymienione może nie reprezentować najnowszy stan technologii platformy Azure. Firma Microsoft stale ulepsza magazyn platformy Azure, aby zapewnić większą przepływność i mniejsze opóźnienia. W związku z tym numery są tylko do celów demonstracyjnych. Musisz przetestować na indywidualne potrzeby w wybranym regionie platformy Azure, aby móc ocenić za pomocą metody jest najlepszy dla Ciebie.

Inną opcją szybkiego przechowywania plików kopii zapasowych SAP HANA jest magazyn obiektów blob platformy Azure. Jeden kontener obiektu blob ma limit około 500 TB, wystarczający dla systemów SAP HANA, przy użyciu typów platformy Azure M32ts, M32ls, M64ls i GS5 VM, aby zachować wystarczającą ilość kopii zapasowych SAP HANA. Klienci mają do &quot;&quot; wyboru &quot;&quot; magazyn gorąca i zimna przestrzeń blob (zobacz [azure blob storage: hot, cool i archive access tiers).](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)

Za pomocą narzędzia blobxfer można łatwo skopiować pliki kopii zapasowej SAP HANA bezpośrednio do magazynu obiektów blob platformy Azure.

![Tutaj można zobaczyć pliki pełnej kopii zapasowej pliku SAP HANA](media/sap-hana-backup-file-level/list-of-backups.png)

Możesz zobaczyć pliki pełnej kopii zapasowej pliku SAP HANA. Z czterech plików, największy ma około 230 GB rozmiaru.

![Skopiowanie 230 GB do kontenera obiektów blob konta magazynu standardowego platformy Azure zajęło około 3000 sekund](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Nie przy użyciu skrótu md5 w teście początkowym zajęło około 3000 sekund, aby skopiować 230 GB do kontenera obiektów blob konta magazynu standardowego platformy Azure.

Konsola kopii zapasowej HANA Studio pozwala ograniczyć maksymalny rozmiar plików kopii zapasowych HANA. W przykładowym środowisku poprawiła wydajność, mając wiele mniejszych plików kopii zapasowych zamiast jednego dużego pliku o rozmiarze 230 GB.

Ustawienie limitu rozmiaru pliku kopii zapasowej po stronie HANA nie poprawia&#39;czasu tworzenia kopii zapasowej, ponieważ pliki są zapisywane sekwencyjnie. Limit rozmiaru pliku został ustawiony na 60 GB, więc kopia zapasowa utworzyła cztery duże pliki danych zamiast pojedynczego pliku o rozmiarze 230 GB. Korzystanie z wielu plików kopii zapasowych może stać się koniecznością tworzenia kopii zapasowych baz danych HANA, jeśli obiekty docelowe kopii zapasowej mają ograniczenia dotyczące rozmiarów plików obiektów blob.

![Aby przetestować równoległość narzędzia blobxfer, maksymalny rozmiar pliku dla kopii zapasowych HANA został następnie ustawiony na 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Aby przetestować równoległość narzędzia blobxfer, maksymalny rozmiar pliku dla kopii zapasowych HANA został następnie ustawiony na 15 GB, co spowodowało 19 plików kopii zapasowych. Ta konfiguracja wydłakowała czas kopiowania magazynu obiektów blob 230 GB na platformę Azure z 3000 sekund do 875 sekund.

Podczas eksplorowania kopiowania kopii zapasowych wykonywanych na dyskach lokalnych do innych lokalizacji, takich jak magazyn obiektów blob platformy Azure, należy pamiętać, że przepustowość używana przez proces kopiowania równoległego jest księgowanie względem przydziału sieci lub magazynu poszczególnych typów maszyn wirtualnych. W rezultacie należy zrównoważyć czas trwania kopii z przepustowością sieci i magazynu, którego wymaga normalne obciążenie uruchomione na maszynie wirtualnej. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopiowanie plików kopii zapasowych SAP HANA do udziału systemu plików NFS

Platforma Microsoft Azure oferuje natywne udziały NFS za pośrednictwem [usługi Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Można utworzyć różne woluminy o pojemności kilkunastu tb w celu przechowywania kopii zapasowych i zarządzania nimi. Możesz również migać te woluminy w oparciu o technologię NetApp. Usługa Azure NetApp Files (ANF) jest oferowana w trzech różnych poziomach usług, które zapewniają różną przepływność magazynu. Aby uzyskać więcej informacji, przeczytaj artykuł [Poziomy usług dla plików NetApp platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Wolumin NFS można utworzyć i zainstalować z usługi ANF zgodnie z opisem w artykule [Szybki start: Konfigurowanie plików NetApp platformy Azure i tworzenie woluminu NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal).

Oprócz korzystania z natywnych woluminów NFS platformy Azure za pośrednictwem usługi ANF istnieją różne możliwości tworzenia własnych wdrożeń, które zapewniają udziały NFS na platformie Azure. Wszystkie mają tę wadę, że trzeba wdrożyć i zarządzać tymi rozwiązaniami samodzielnie. Niektóre z tych możliwości są udokumentowane w następujących artykułach:

- [Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [System GlusterFS na maszynach wirtualnych platformy Azure z systemem Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

Udziały NFS utworzone za pomocą opisanych powyżej środków mogą być używane do bezpośredniego wykonywania kopii zapasowych HANA przeciwko lub do kopiowania kopii zapasowych, które zostały wykonane na dyskach lokalnych do tych udziałów NFS.

> [!NOTE]
> SAP HANA obsługuje system NFS v3 i NFS v4.x. Każdy inny format, taki jak SMB z systemem plików CIFS, nie jest obsługiwany do zapisywania kopii zapasowych HANA. Zobacz też [uwaga dotycząca obsługi sap #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopiowanie plików kopii zapasowych SAP HANA do usługi Azure Files

Istnieje możliwość zainstalowania udziału usługi Azure Files wewnątrz maszyny Wirtualnej systemu Azure linux. Artykuł [Jak używać usługi Azure File Storage z systemem Linux](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux) zawiera szczegółowe informacje na temat wykonywania konfiguracji. Aby uzyskać ograniczenia dotyczące plików usługi Azure Files lub azure w wersji premium, przeczytaj artykuł [Usługi Azure Files skalowalności i docelowych wydajności](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets).

> [!NOTE]
> SMB z systemem plików CIFS nie jest obsługiwany przez SAP HANA do zapisywania kopii zapasowych HANA przeciwko. Zobacz też [uwaga dotycząca obsługi sap #1820529](https://launchpad.support.sap.com/#/notes/1820529). W rezultacie można używać tego rozwiązania tylko jako ostatecznego miejsca docelowego kopii zapasowej bazy danych HANA, która została przeprowadzona bezpośrednio na lokalnych dyskach podłączonych
> 

W teście przeprowadzonym na platformie Azure Files, a nie w usłudze Azure Premium Files, skopiowanie 19 plików kopii zapasowej o ogólnej objętości 230 GB zajęło około 929 sekund. Oczekujemy, że czas przy użyciu usługi Azure Premium Files jest o wiele lepszy. Należy jednak pamiętać, że należy zrównoważyć interesy szybkiej kopii z wymaganiami, które obciążenie ma na przepustowość sieci. Ponieważ każdy typ maszyny Wirtualnej platformy Azure wymusza przydział przepustowości sieci, musisz pozostać w zakresie tego przydziału z obciążeniem oraz kopią plików kopii zapasowej.

![Liczba ta pokazuje, że skopiowanie 19 plików kopii zapasowych SAP HANA zajęło około 929 sekund](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Przechowywanie plików kopii zapasowych SAP HANA w plikach platformy Azure może być interesującą opcją. Szczególnie w przypadku zwiększonego opóźnienia i przepływności plików premium platformy Azure.

## <a name="next-steps"></a>Następne kroki
* [Przewodnik po kopiach zapasowych dla systemu SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-backup-guide.md) zawiera omówienie i informacje na temat rozpoczynania pracy.
* Aby dowiedzieć się, jak ustalić wysoką dostępność i zaplanować odzyskiwanie po awarii sap HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure.](hana-overview-high-availability-disaster-recovery.md)

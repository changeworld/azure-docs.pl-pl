---
title: SAP HANA usługi Azure Backup na poziomie plików | Dokumentacja firmy Microsoft
description: Istnieją dwie główne kopii zapasowej możliwości platformy SAP Hana na maszynach wirtualnych platformy Azure, w tym artykule opisano SAP HANA kopia zapasowa Azure na poziomie plików
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
ms.openlocfilehash: 02ee65020f72fb9c3262db82e035e628f780e2cf
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709996"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA usługi Azure Backup na poziomie plików

## <a name="introduction"></a>Wprowadzenie

Jest to część trzyczęściowej serii pokrewnych artykułów dotyczących kopii zapasowych oprogramowania SAP HANA. [Przewodnik tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych Azure](./sap-hana-backup-guide.md) zawiera przegląd i informacje na temat rozpoczynania pracy i [kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu](./sap-hana-backup-storage-snapshots.md) obejmuje opcję tworzenia kopii zapasowej na podstawie migawki magazynu.

Różne typy maszyn wirtualnych na platformie Azure umożliwiają różną liczbę wirtualnych dysków twardych dołączonych. Szczegółowymi informacjami na temat są udokumentowane w artykule [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](../../linux/sizes.md). W przypadku testów określonych w niniejszej dokumentacji użyliśmy maszyny Wirtualnej platformy Azure GS5, co pozwala 64 dołączonych dysków z danymi. Dla większych systemów SAP HANA już być zajęta plików danych i dziennika, prawdopodobnie w połączeniu z rozkładanie oprogramowania dotyczące przepływności operacji We/Wy dysku optymalne znaczącą liczbę dysków. Aby uzyskać więcej informacji na temat konfiguracji dysku sugerowane dla wdrożeń SAP HANA na maszynach wirtualnych Azure, przeczytaj artykuł [platformy SAP HANA w podręczniku obsługi platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Zalecenia przedstawione są w tym zalecenia miejsca na dysku dla również lokalne kopie zapasowe.

W tej chwili dostępna przy użyciu usługi Azure Backup jest Brak integracji kopii zapasowych oprogramowania SAP HANA. Standardowy sposób zarządzania i przywracania kopii zapasowych na poziomie plików jest oparte na plikach kopii zapasowej za pośrednictwem programu SAP HANA Studio lub za pomocą instrukcji SQL platformy SAP HANA. Zobacz [SQL platformy SAP HANA i odwołanie do widoków systemu](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) Aby uzyskać więcej informacji.

![Poniższy rysunek pokazuje okno dialogowe elementu menu kopii zapasowych w systemie SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Poniższy rysunek pokazuje okno dialogowe elementu menu kopii zapasowych w systemie SAP HANA Studio. Podczas wybierania typu &quot;pliku&quot; jedna ma należy określić ścieżkę w systemie plików gdzie platformy SAP HANA zapisuje pliki kopii zapasowej. Przywracanie działa tak samo.

Gdy ten wybór wydaje się prosta i proste, istnieją pewne zagadnienia. Jak wspomniano wcześniej, Maszynie wirtualnej platformy Azure ma ograniczenie liczby dysków z danymi, które mogą być dołączone. Nie może być przygotowanie wydajności niezbędnej do przechowywania plików kopii zapasowych oprogramowania SAP HANA w systemach plików maszyny wirtualnej, w zależności od rozmiaru bazy danych i dysku przepływności wymagań, które może obejmować oprogramowania rozkładania na wielu dyskach danych. Różne opcje dotyczące przenoszenia tych plików kopii zapasowej i zarządzanie ograniczenia rozmiaru plików i wydajności podczas obsługi terabajty danych, znajdują się w dalszej części tego artykułu.

Innym rozwiązaniem, która zapewnia większą swobodę w sprawie łączna pojemność, jest usługa Azure blob storage. Pojedynczy obiekt blob jest także ograniczona do 1 TB, łączna pojemność jednego kontenera obiektów blob jest obecnie 500 TB. Ponadto go daje klientom możliwość wybierz tak zwane &quot;chłodna&quot; usługi blob storage, która ma obniżenie kosztów. Zobacz [usługi Azure Blob Storage: Gorącego i chłodnego warstw magazynowania](../../../storage/blobs/storage-blob-storage-tiers.md) szczegółowe informacje na temat chłodnego magazynu obiektów blob.

Dla dodatkowego bezpieczeństwa należy użyć konta magazynu z replikacją geograficzną do przechowywania kopii zapasowych oprogramowania SAP HANA. Zobacz [replikacja usługi Azure Storage](../../../storage/common/storage-redundancy.md) szczegółowe informacje na temat replikacji konta magazynu.

Jeden mógł umieścić dedykowanych dysków VHD dla kopii zapasowych oprogramowania SAP HANA na koncie dedykowanych dla magazynu kopii zapasowych, który jest replikowana geograficznie. W przeciwnym razie jeden można skopiować wirtualne dyski twarde, które przechowywać kopie zapasowe oprogramowania SAP HANA na konto magazynu z replikacją geograficzną lub na koncie magazynu, który znajduje się w innym regionie.

## <a name="azure-backup-agent"></a>Agent usługi Azure backup

Usługa Azure backup oferuje opcję, aby nie tylko kopie zapasowe pełne maszyn wirtualnych, ale również plików i katalogów przy użyciu agenta kopii zapasowej, które zostały zainstalowane w systemie operacyjnym gościa. Jednak ten agent jest obsługiwany tylko w Windows (zobacz [tworzenie kopii zapasowej systemu Windows Server lub klienta na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager](../../../backup/backup-configure-vault.md)).

Obejście tego problemu jest najpierw skopiuj pliki kopii zapasowych oprogramowania SAP HANA do maszyny Wirtualnej z systemem Windows na platformie Azure (na przykład za pośrednictwem udziału SAMBA), a następnie użyć agenta usługi Azure backup, w tym miejscu. Chociaż jest technicznie możliwe, go będzie zwiększenia złożoności i spowolnić kopii zapasowej lub przywracania procesu dość nieco ze względu na kopię między systemu Linux i Windows maszyny Wirtualnej. Nie zaleca się zastosowanie tego podejścia.

## <a name="azure-blobxfer-utility-details"></a>Szczegóły narzędzia blobxfer platformy Azure

Do przechowywania katalogów i plików w usłudze Azure storage, jeden można użyć programu PowerShell lub interfejsu wiersza polecenia lub Opracuj narzędzie przy użyciu jednej z [zestawami SDK Azure](https://azure.microsoft.com/downloads/). Również to narzędzie gotowych do użycia, AzCopy do kopiowania danych do usługi Azure storage, ale jest on Windows tylko (zobacz [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../../../storage/common/storage-use-azcopy.md)).

W związku z tym blobxfer użyto kopiowanie plików kopii zapasowych oprogramowania SAP HANA. Jest typu open source, używane przez wielu klientów w środowiskach produkcyjnych i dostępne w [GitHub](https://github.com/Azure/blobxfer). To narzędzie umożliwia jeden skopiować dane bezpośrednio do usługi Azure blob storage lub udziału plików platformy Azure. Oferuje szeroką gamę przydatnych funkcji, takich jak skrót md5 lub automatycznego równoległości podczas kopiowania katalogu z wieloma plikami.

## <a name="sap-hana-backup-performance"></a>Wydajność tworzenia kopii zapasowych platformy SAP HANA

![Ten zrzut ekranu jest konsola kopii zapasowej platformy SAP HANA w systemie SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Ten zrzut ekranu jest konsola kopii zapasowej platformy SAP HANA w systemie SAP HANA Studio. Kopii 230 GB na dysku jednego standardowego magazynu platformy Azure dołączonych do maszyny Wirtualnej platformy HANA przy użyciu systemu plików XFS zajęło to około 42 minuty.

![Ten zrzut ekranu jest YaST na testowej maszynie Wirtualnej platformy SAP HANA](media/sap-hana-backup-file-level/image024.png)

Ten zrzut ekranu jest YaST na testowej maszynie Wirtualnej platformy SAP HANA. Jak wspomniano wcześniej, jeden zobacz jeden dysk 1 TB pojemności do tworzenia kopii zapasowych oprogramowania SAP HANA. Do tworzenia kopii zapasowej GB 230 zajęło to około 42 minuty. Ponadto zostały dołączone pięciu dysków 200 GB i md0 macierzy RAID oprogramowania tworzona, gdy rozkładanie na podstawie tych pięciu dysków danych na platformie Azure.

![Powtarzanie kopii zapasowej tego samego oprogramowania RAID z Rozkładanie w pięciu dołączonych dysków z danymi usługi Azure standard storage](media/sap-hana-backup-file-level/image025.png)

Powtarzanie kopii zapasowej tego samego oprogramowania RAID z Rozkładanie w pięciu dołączonych dysków z danymi usługi Azure standard storage wprowadzony czas wykonywania kopii zapasowych z 42 minuty w dół do 10 minut. Dyski zostały dołączone, bez buforowania do maszyny Wirtualnej. Dlatego jest oczywisty jak ważne przepływność zapisu na dysku jest czas tworzenia kopii zapasowej. Jeden można następnie przełącz się do usługi Azure Premium Storage do dalszego przyspieszyć ten proces, aby uzyskać optymalną wydajność. Ogólnie rzecz biorąc należy używać usługi Azure Premium Storage dla systemów produkcyjnych.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Skopiuj pliki kopii zapasowej platformy SAP HANA w usłudze Azure blob storage

Inną opcją szybkiego przechowywania plików kopii zapasowych oprogramowania SAP HANA jest usługi Azure blob storage. Jeden kontener obiektów blob pojedynczego obowiązuje limit 500 TB, wystarczająca dla niektórych mniejszych systemów SAP HANA, użycie typów M32ts, M32ls, M64ls i maszyna wirtualna GS5 platformy Azure, aby zapewnić wystarczające kopie zapasowe oprogramowania SAP HANA. Klienci mogą sami między &quot;gorąca&quot; i &quot;zimnych&quot; magazynu obiektów blob (zobacz [usługi Azure Blob Storage: Gorącego i chłodnego warstw magazynowania](../../../storage/blobs/storage-blob-storage-tiers.md)).

Za pomocą narzędzia blobxfer jest łatwe skopiować pliki kopii zapasowych oprogramowania SAP HANA bezpośrednio w usłudze Azure blob storage.

![Tutaj widać plików pełnej kopii zapasowej pliku oprogramowania SAP HANA](media/sap-hana-backup-file-level/image026.png)

Tutaj widać plików pełnej kopii zapasowej pliku oprogramowania SAP HANA. Istnieją cztery pliki a przez największe około 230 GB.

![Zajęło to około 3000 sekund, aby skopiować 230 GB do kontenera obiektów blob konta magazynu platformy Azure w warstwie standardowa](media/sap-hana-backup-file-level/image027.png)

Nie używa skrótu md5 w testu wstępnego, jaki zajęło około 3000 sekund, aby skopiować 230 GB do kontenera obiektów blob konta magazynu platformy Azure w warstwie standardowa.

![W tym zrzucie ekranu widać jak to wygląda w witrynie Azure portal](media/sap-hana-backup-file-level/image028.png)

W tym zrzucie ekranu widać jak to wygląda w witrynie Azure portal. Kontener obiektów blob o nazwie &quot;sap hana — kopie zapasowe&quot; został utworzony i zawiera cztery obiektów blob, które reprezentują pliki kopii zapasowych oprogramowania SAP HANA. Jeden z nich ma rozmiar około 230 GB.

Konsola kopii zapasowej HANA Studio umożliwia jeden ograniczyć maksymalny rozmiar pliku kopii zapasowej plików HANA. W środowisku przykładowe zwiększona wydajność dzięki czemu można mieć kilka mniejszych plików kopii zapasowej, zamiast jednego dużego pliku 230 GB.

![Ustawienie limit rozmiaru pliku kopii zapasowej po stronie platformy HANA&#39;t poprawić czas wykonywania kopii zapasowych](media/sap-hana-backup-file-level/image029.png)

Ustawienie limit rozmiaru pliku kopii zapasowej po stronie platformy HANA&#39;t poprawić czas tworzenia kopii zapasowych, ponieważ pliki są zapisywane sekwencyjnie, jak pokazano na ilustracji. Limit rozmiaru pliku zostało ustawione na 60 GB, dlatego kopia zapasowa tworzona cztery duże pliki danych zamiast 230 GB pojedynczy plik. Użycie wielu plików kopii zapasowej jest konieczność wykonywania kopii zapasowych baz danych HANA, korzystających z pamięci w większych maszyn wirtualnych platformy Azure, takich jak M64s, M64ms, M128s i M128ms.

![Aby przetestować równoległości narzędzie blobxfer, maksymalny rozmiar pliku dla kopii zapasowych HANA następnie została ustawiona do 15 GB](media/sap-hana-backup-file-level/image030.png)

Aby przetestować równoległości narzędzie blobxfer, maksymalny rozmiar pliku dla kopii zapasowych HANA następnie została ustawiona do 15 GB, co spowodowało 19 pliki kopii zapasowej. Ta konfiguracja rozstrzygane czas blobxfer do skopiowania 230 GB w usłudze Azure blob storage 3000 sekund do 875 sekund.

Ten wynik jest z powodu limitu 60 MB na sekundę dla zapisu obiektu blob platformy Azure. Równoległość za pomocą wielu obiektów blob rozwiązuje wąskie gardło, ale ma to pewną wadę: zwiększanie wydajności narzędzie blobxfer, aby skopiować wszystkie pliki kopii zapasowych platformy HANA do usługi Azure blob storage umieszcza obciążenia na maszynie Wirtualnej platformy HANA i sieci. Staje się negatywny wpływ na działanie systemu HANA.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Kopiowanie obiektu blob dysków danych na platformie Azure dedykowany w macierzy RAID oprogramowania kopii zapasowej

W przeciwieństwie do ręcznego danych dysku kopii zapasowych maszyn wirtualnych w tym podejściu, jeden nie Utwórz kopię zapasową wszystkich dysków danych na maszynie Wirtualnej do zapisania całej instalacji SAP, w tym dane oprogramowania HANA HANA rejestruje pliki i pliki konfiguracji. Zamiast tego chodzi o to, aby są wyposażone w dedykowane macierzy RAID oprogramowania za pomocą rozkładanie danych platformy Azure wiele wirtualnych dysków twardych do przechowywania pełnej kopii zapasowej pliku oprogramowania SAP HANA. Kopiuje jeden tylko te dyski, których kopia zapasowa oprogramowania SAP HANA. One może łatwo być przechowywane w dedykowanego konta magazynu kopii zapasowych HANA lub dołączone do dedykowanej &quot;kopii zapasowej maszyny Wirtualnej zarządzania&quot; do dalszego przetwarzania.

![Wszystkie dyski VHD, które są zaangażowane zostały skopiowane, za pomocą ** start-azurestorageblobcopy ** polecenia programu PowerShell](media/sap-hana-backup-file-level/image031.png)

Po kopii zapasowej w celu lokalnego macierzy RAID oprogramowania została ukończona, wszystkie dyski VHD, które są zaangażowane zostały skopiowane, za pomocą **start azurestorageblobcopy** polecenia programu PowerShell (zobacz [Start AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Jak dotyczy tylko systemu plików dedykowanych do przechowywania plików kopii zapasowej, nie ma żadnych wątpliwości dotyczących spójności platformy SAP HANA danych lub dziennika plików na dysku. Zaletą tego polecenia jest, że działa, gdy maszyna wirtualna pozostaje w trybie online. Aby upewnić się, że żaden proces nie zapisuje do zestawu kopii zapasowych usługi stripe, należy go odinstalować przed kopii obiektu blob, a następnie zainstalować go ponownie później. Lub jeden wystarczą stosownego sposobu &quot;Zablokuj&quot; systemu plików. Na przykład za pośrednictwem xfs\_Zablokuj XFS systemu plików.

![Ten zrzut ekranu przedstawia listę obiektów blob w kontenerze wirtualnych dysków twardych w witrynie Azure portal](media/sap-hana-backup-file-level/image032.png)

Ten zrzut ekranu przedstawia listę obiektów blob w &quot;wirtualne dyski twarde&quot; kontenera w witrynie Azure portal. Zrzut ekranu przedstawia pięć wirtualnych dysków twardych, które zostały dołączone do serwera SAP HANA maszyny Wirtualnej, która będzie służyć jako programowej macierzy RAID, aby zachować pliki kopii zapasowych oprogramowania SAP HANA. Zawiera także pięć kopii, które zostały wykonane za pomocą polecenia kopiowania obiektu blob.

![Do celów testowych kopie dysków RAID kopii zapasowych oprogramowania SAP HANA zostały dołączone do serwera aplikacji maszyny Wirtualnej](media/sap-hana-backup-file-level/image033.png)

Do celów testowych kopie dysków RAID kopii zapasowych oprogramowania SAP HANA zostały dołączone do serwera aplikacji maszyny Wirtualnej.

![Kopiuje serwera aplikacji, które maszyna wirtualna została zamknięta można dołączyć dysku](media/sap-hana-backup-file-level/image034.png)

Serwer aplikacji maszyny Wirtualnej został zamknięty, aby podłączyć dysk kopiuje. Po uruchomieniu maszyny Wirtualnej, dysków i RAID zostały prawidłowo odnalezione (zainstalowany za pomocą identyfikatora UUID). Tylko brakowało punktu instalacji, która została utworzona za pomocą partycjonera YaST. Później kopiuje plik kopii zapasowej platformy SAP HANA stały się widoczne na poziomie systemu operacyjnego.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Skopiuj pliki kopii zapasowej platformy SAP HANA do udziału NFS

Aby zmniejszyć potencjalnego wpływu na wydajność lub dysku w systemie SAP HANA, jeden rozważyć przechowywanie plików kopii zapasowych oprogramowania SAP HANA w udziale NFS. Z technicznego punktu widzenia działa, ale oznacza to, co host udziału NFS przy użyciu drugiej maszyny Wirtualnej platformy Azure. Nie może być mały rozmiar maszyny Wirtualnej ze względu na przepustowość sieci maszyny Wirtualnej. Sensowne będzie następnie zamknąć ten &quot;kopii zapasowej maszyny Wirtualnej&quot; i tylko przełączyć go do wykonywania kopii zapasowych oprogramowania SAP HANA. Pisanie na systemu plików NFS udostępniania umieszcza obciążenia w sieci i ma wpływ na system SAP HANA, ale jedynie zarządzania pliki kopii zapasowej później na &quot;kopii zapasowej maszyny Wirtualnej&quot; czy wpłynęło na wszystkich systemu SAP HANA.

![Udziału NFS z innej maszyny Wirtualnej platformy Azure został zainstalowany serwer SAP HANA maszyny Wirtualnej](media/sap-hana-backup-file-level/image035.png)

Aby sprawdzić, czy przypadek użycia systemu plików NFS, udziału NFS z innej maszyny Wirtualnej platformy Azure został zainstalowany serwer SAP HANA maszyny Wirtualnej. Nie było żadnych specjalnych systemu plików NFS dostrajania stosowane.

![Zajmowało 1 godzinę i 46 minut wykonaj kopię zapasową bezpośrednio](media/sap-hana-backup-file-level/image036.png)

Udziału NFS była szybka rozłożony, takim jak rejestr na serwerze SAP HANA. Niemniej jednak zajęło to 1 godzina, a 46 minut bezpośrednio na kopii udziału NFS zamiast 10 minut, po ustawieniu zapis do lokalnej usługi stripe.

![Alternatywnie nie został znacznie szybsza, bo w 1 i godz.](media/sap-hana-backup-file-level/image037.png)

Alternatywne o wykonaniu kopii zapasowej do lokalnego rozłożony i kopiowanie do udziału NFS na poziomie systemu operacyjnego (prosty **cp-avr** polecenia) nie została znacznie szybciej. Zajęło 1 i godz.

Tak to działa, ale wydajność nie była dobra dla kopii zapasowych testowania 230 GB. Wyglądałaby jeszcze gorzej dla wielu terabajtów.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Skopiuj pliki kopii zapasowej platformy SAP HANA do usługi Azure Files

Istnieje możliwość zainstalować udział plików platformy Azure w maszynie Wirtualnej systemu Linux platformy Azure. Artykuł [jak używać usługi Azure File storage z systemem Linux](../../../storage/files/storage-how-to-use-files-linux.md) zawiera szczegółowe informacje o tym, jak to zrobić. Należy pamiętać, że jest obecnie limit przydziału o rozmiarze 5 TB jeden udział plików platformy Azure, a limit rozmiaru pliku o rozmiarze 1 TB na plik. Zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](../../../storage/common/storage-scalability-targets.md) informacji o limitach magazynu.

Badania wykazały, jednak bez tworzenia kopii zapasowych oprogramowania SAP HANA&#39;t obecnie pracować bezpośrednio za pomocą tego rodzaju CIFS instalacji. Również jest podany w [1820529 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1820529) , CIFS nie jest zalecane.

![Poniższy rysunek pokazuje błąd, w oknie dialogowym tworzenia kopii zapasowych w systemie SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Poniższy rysunek pokazuje błąd w oknie dialogowym tworzenia kopii zapasowych w systemie SAP HANA Studio, podczas próby utworzenia kopii zapasowej bezpośrednio do udziału plików platformy Azure zainstalowany CIFS. Dlatego musi zapasowej standardowe platformy SAP HANA w systemie plików maszyny Wirtualnej najpierw, a następnie skopiuj pliki kopii zapasowej w tym miejscu, usługą Azure file service.

![Poniższy rysunek pokazuje trwało około 929 sekund, aby skopiować 19 pliki kopii zapasowych oprogramowania SAP HANA](media/sap-hana-backup-file-level/image039.png)

Poniższy rysunek pokazuje trwało około 929 sekund, aby skopiować 19 pliki kopii zapasowych oprogramowania SAP HANA o całkowitym rozmiarze około 230 GB do udziału plików platformy Azure.

![Źródło struktury katalogów na maszynie Wirtualnej SAP HANA zostały skopiowane do udziału plików platformy Azure](media/sap-hana-backup-file-level/image040.png)

W tym zrzucie ekranu widać czy strukturę katalogu źródłowego na maszynie Wirtualnej SAP HANA, zostały skopiowane do udziału plików platformy Azure: jeden katalog (hana\_kopii zapasowej\_fsl\_15 gb) i 19 pojedynczych plików kopii zapasowej.

Przechowywanie plików kopii zapasowych oprogramowania SAP HANA na usługi pliki Azure może być interesujące opcję w przyszłości, gdy kopie zapasowe plików SAP HANA obsługują je bezpośrednio. Lub kiedy możliwe staje się instalacji usługi Azure files za pośrednictwem systemu plików NFS i maksymalny limit przydziału jest znacznie wyższa niż 5 TB.

## <a name="next-steps"></a>Następne kroki
* [Przewodnik tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md) zawiera omówienie oraz informacje na temat rozpoczynania pracy.
* [Kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu](sap-hana-backup-storage-snapshots.md) opisy opcji kopii zapasowych opartych na migawkach magazynu.
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia), zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).

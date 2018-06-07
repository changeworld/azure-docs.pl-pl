---
title: SAP HANA Azure Backup na poziomie plików | Dokumentacja firmy Microsoft
description: Istnieją dwa główne kopii zapasowej możliwości SAP HANA na maszynach wirtualnych Azure, w tym artykule omówiono SAP HANA kopia zapasowa Azure na poziomie plików
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 1c0222bffe6ccf2ca35e5ca5874f91a490ab352d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656996"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup na poziomie plików

## <a name="introduction"></a>Wprowadzenie

To jest częścią serii trzyczęściowej pokrewne artykuły kopii zapasowej SAP HANA. [Podręcznik tworzenia kopii zapasowej programu SAP HANA na maszynach wirtualnych Azure](./sap-hana-backup-guide.md) zawiera omówienie i informacje wprowadzające, i [SAP HANA kopii zapasowych oparte na magazynu migawek](./sap-hana-backup-storage-snapshots.md) obejmuje opcję tworzenia kopii zapasowej na podstawie migawki magazynu.

Spojrzenie na rozmiary maszyn wirtualnych Azure, co Zobacz, czy GS5 umożliwia 64 dysków dołączonych danych. W dużych systemach SAP HANA już być wykonana plików danych i dziennika, prawdopodobnie w połączeniu z oprogramowaniem RAID dla dysku optymalną wydajność We/Wy duża liczba dysków. Następnie pytanie jest miejsce przechowywania plików kopii zapasowej SAP HANA, które może zapełnić dysków dołączonych danych wraz z upływem czasu? Zobacz [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](../../linux/sizes.md) dla tabel rozmiar maszyny Wirtualnej platformy Azure.

W tej chwili dostępne w usłudze Kopia zapasowa Azure jest integracja kopii zapasowej nie SAP HANA. Jest standardowy sposób zarządzać wykonywania kopii zapasowej/przywracania na poziomie plików z kopii zapasowej za pomocą programu SAP HANA Studio lub za pomocą instrukcji SAP HANA SQL opartych na plikach. Zobacz [SAP HANA SQL i odwołanie widoków systemu](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) Aby uzyskać więcej informacji.

![Poniższy rysunek przedstawia okno dialogowe elementu menu kopii zapasowej w SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Na poniższym rysunku pokazano okna dialogowego elementu menu kopii zapasowej w SAP HANA Studio. W przypadku wybrania typu &quot;pliku&quot; jeden musi określić ścieżkę w systemie plików gdzie SAP HANA zapisuje pliki kopii zapasowej. Przywracanie działa tak samo.

Chociaż ten wybór wydaje się proste i bezpośrednio do przodu, istnieją pewne zagadnienia. Jak wspomniano wcześniej, maszyny Wirtualnej platformy Azure obowiązuje ograniczenie liczby dysków z danymi, które można dołączyć. Może nie być pojemności do przechowywania plików kopii zapasowej SAP HANA w systemach plików maszyny wirtualnej, zależnie od rozmiaru bazy danych i dysku przepływności wymagania, które mogą dotyczyć oprogramowania RAID przy użyciu stosowanie w danych w wielu dysków. Różne opcje przenoszenia tych plików kopii zapasowej i zarządzanie ograniczenia rozmiaru plików i wydajności podczas obsługi terabajtów danych, znajdują się w dalszej części tego artykułu.

Inną opcją, który zapewnia większą swobodę dotyczące całkowitej pojemności, jest magazynu obiektów blob platformy Azure. Gdy pojedynczego obiektu blob jest również ograniczony do 1 TB, całkowita pojemność kontenera pojedynczego obiektu blob jest obecnie 500 TB. Ponadto zapewnia klientom wybrać opcję tak zwane &quot;chłodnych&quot; obiektu blob magazynu, który ma kosztów korzyści. Zobacz [usługi Azure Blob Storage: Hot i cool warstw magazynowania](../../../storage/blobs/storage-blob-storage-tiers.md) szczegółowe informacje na temat magazynu chłodnego obiektów blob.

Dla dodatkowego bezpieczeństwa należy użyć konta magazynu z replikacją geograficzną do przechowywania kopii zapasowych SAP HANA. Zobacz [replikacja usługi Azure Storage](../../../storage/common/storage-redundancy.md) szczegółowe informacje o replikacji konta magazynu.

Co można umieścić dedykowanych dysków VHD dla kopii zapasowych SAP HANA w konto dedykowanych dla magazynu kopii zapasowej, które jest replikacją geograficzną. W przeciwnym razie jedną można skopiować pliki VHD, które przechowują kopie zapasowe SAP HANA na konto magazynu z replikacją geograficzną, lub na konto magazynu, który znajduje się w innym regionie.

## <a name="azure-backup-agent"></a>Agent usługi Kopia zapasowa Azure

Kopia zapasowa Azure oferuje możliwość nie tylko tworzenie kopii zapasowej pełnej maszyn wirtualnych, ale również plików i katalogów za pomocą agenta kopii zapasowej, który ma być zainstalowany na system operacyjny gościa. Jednak począwszy od grudnia 2016 r. Ten agent jest obsługiwana tylko w systemie Windows (zobacz [kopii zapasowych systemu Windows Server lub klienta przy użyciu modelu wdrażania Menedżera zasobów Azure](../../../backup/backup-configure-vault.md)).

Obejście tego problemu jest najpierw skopiuj pliki kopii zapasowej SAP HANA do maszyny Wirtualnej systemu Windows Azure (na przykład za pośrednictwem udziału SAMBA), a następnie użyć agenta kopii zapasowej Azure stamtąd. Chociaż jest technicznie możliwe, go spowoduje zwiększenia złożoności oraz spowolnić kopii zapasowej lub przywracanie procesu dość nieco z powodu kopiowania między systemu Linux i maszyny Wirtualnej systemu Windows. Nie zaleca się zastosowanie tego podejścia.

## <a name="azure-blobxfer-utility-details"></a>Szczegóły narzędzia Azure blobxfer

Do przechowywania katalogów i plików w magazynie Azure, co można użyć interfejsu wiersza polecenia lub programu PowerShell, lub Opracuj narzędzie przy użyciu jednej z [zestawów SDK usługi Azure](https://azure.microsoft.com/downloads/). Również jest gotowe do użycia narzędzie AzCopy, kopiowania danych do magazynu Azure, ale jest tylko systemu Windows (zobacz [transferu danych za pomocą narzędzia wiersza polecenia AzCopy](../../../storage/common/storage-use-azcopy.md)).

W związku z tym blobxfer została użyta do kopiowania plików kopii zapasowej SAP HANA. Jest typu open source, używane przez wielu klientów w środowisku produkcyjnym i dostępne w [GitHub](https://github.com/Azure/blobxfer). To narzędzie umożliwia jedna, aby skopiować dane bezpośrednio do magazynu obiektów blob platformy Azure lub udział plików na platformę Azure. Oferuje szeroką gamę przydatne funkcje, takie jak wyznaczania wartości skrótu md5 lub automatyczne równoległości podczas kopiowania katalogu z wieloma plikami.

## <a name="sap-hana-backup-performance"></a>SAP HANA wydajności tworzenia kopii zapasowych

![Jest to zrzut ekranu konsoli kopii zapasowej SAP HANA w SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Ten zrzut ekranu jest SAP HANA konsoli kopii zapasowej w SAP HANA Studio. Zajęło około 42 minut do utworzenia kopii zapasowej 230 GB na dysku jednego magazynu standardowego platformy Azure dołączonych do maszyny Wirtualnej HANA przy użyciu systemu plików XFS.

![Ten zrzut ekranu jest YaST na SAP HANA testowej maszyny Wirtualnej](media/sap-hana-backup-file-level/image024.png)

Ten zrzut ekranu jest YaST na testowej maszynie Wirtualnej SAP HANA. Jak wspomniano wcześniej, jeden Zobacz 1 TB jednego dysku do utworzenia kopii zapasowej SAP HANA. Zajęło około 42 minut do tworzenia kopii zapasowej 230 GB. Ponadto zostały dołączone pięć dysków 200 GB i md0 RAID oprogramowania utworzona zastosowanie rozkładania na podstawie tych pięciu dysków danych Azure.

![Powtarzanie kopii zapasowej tego samego oprogramowania RAID z rozkładanie przez pięć dołączone dyski danych Azure magazynu w warstwie standardowa](media/sap-hana-backup-file-level/image025.png)

Powtarzające się kopii zapasowej tego samego oprogramowania RAID z rozkładanie przez pięć dołączonych dysków z danymi usługi Azure standard storage przejdzie 42 minut do 10 minut od czasu tworzenia kopii zapasowej. Dyski zostały dołączone bez buforowania do maszyny Wirtualnej. Dlatego jest oczywiste przepływność zapisu dysku jak ważna jest dla wykonywania kopii zapasowej. Następnie można jedną przełączyć do magazynu Azure premium do dalszego przyspieszyć ten proces, aby uzyskać optymalną wydajność. Ogólnie rzecz biorąc magazynu Azure premium powinien być używany dla systemów produkcyjnych.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Skopiuj pliki kopii zapasowej SAP HANA do magazynu obiektów blob platformy Azure

Począwszy od grudnia 2016 r. najlepszym rozwiązaniem szybko przechowywania plików kopii zapasowej SAP HANA jest magazynu obiektów blob platformy Azure. Jeden kontener pojedynczego obiektu blob jest objęta limitem 500 TB, wystarczająca dla większości systemów SAP HANA, uruchomione na maszynie wirtualnej GS5 na platformie Azure, aby zachować wystarczające SAP HANA kopii zapasowych. Klienci muszą wybrać między &quot;gorących&quot; i &quot;zimnych&quot; magazynu obiektów blob (zobacz [usługi Azure Blob Storage: Hot i cool warstw magazynowania](../../../storage/blobs/storage-blob-storage-tiers.md)).

Za pomocą narzędzia blobxfer jest łatwa do skopiuj pliki kopii zapasowej SAP HANA bezpośrednio do magazynu obiektów blob platformy Azure.

![W tym miejscu jedną Zobacz pliki pełnej kopii zapasowej pliku SAP HANA](media/sap-hana-backup-file-level/image026.png)

W tym miejscu jedną Zobacz pliki pełnej kopii zapasowej pliku SAP HANA. Istnieją cztery pliki i jeden największych ma około 230 GB.

![Zajęło około 3000 sekund, aby skopiować 230 GB do kontenera obiektów blob konta magazynu Azure w warstwie standardowa](media/sap-hana-backup-file-level/image027.png)

Nie używa skrótu md5 w teście początkowej, zajęło około 3000 sekund, aby skopiować 230 GB do kontenera obiektów blob konta standardowe magazynu Azure.

![W tym zrzucie ekranu pokazano co Zobacz, jak wygląda w portalu Azure](media/sap-hana-backup-file-level/image028.png)

Na tym zrzucie ekranu pokazano co Zobacz, jak wygląda w portalu Azure. Kontener obiektu blob o nazwie &quot;sap hana kopie zapasowe&quot; został utworzony i obejmuje cztery obiektów blob, które zawierają pliki kopii zapasowej SAP HANA. Jeden z nich ma rozmiar około 230 GB.

Konsola kopii zapasowej HANA Studio pozwala jedna, aby ograniczyć maksymalny rozmiar plików HANA kopii zapasowej. W środowisku próbki wyższą wydajność, co umożliwia kilka mniejszych plików kopii zapasowej, zamiast jednego dużego pliku 230 GB.

![Ustawienie limitu rozmiaru pliku kopii zapasowej po stronie HANA&#39;t zwiększyć czas tworzenia kopii zapasowej](media/sap-hana-backup-file-level/image029.png)

Ustawienie limitu rozmiaru pliku kopii zapasowej po stronie HANA&#39;t zwiększyć czas tworzenia kopii zapasowej, ponieważ pliki są zapisywane w odpowiedniej kolejności, jak pokazano na poniższym rysunku. Limit rozmiaru pliku ustawiono 60 GB, więc kopii zapasowej utworzyć cztery pliki dużej ilości danych, zamiast pliku jednym 230 GB.

![Aby przetestować równoległości narzędzia blobxfer, rozmiar maksymalny pliku kopii zapasowych HANA następnie ustawiono 15 GB](media/sap-hana-backup-file-level/image030.png)

Aby przetestować równoległości narzędzia blobxfer, rozmiar maksymalny pliku kopii zapasowych HANA następnie ustawiono 15 GB, co spowodowało 19 pliki kopii zapasowej. Ta konfiguracja wprowadzane czas blobxfer do skopiowania do magazynu obiektów blob platformy Azure 3000 sekund do 875 sekund 230 GB.

Ten wynik jest spowodowane limit 60 MB/s do zapisywania obiektów blob platformy Azure. Równoległość za pomocą wielu obiektów blob rozwiązuje wąskie gardło, ale wadą interfejsu: zwiększanie wydajności narzędzie blobxfer, aby skopiować wszystkie pliki kopii zapasowych HANA do magazynu obiektów blob platformy Azure umieszcza obciążenia na HANA maszyny Wirtualnej i sieci. Staje się negatywny wpływ na działanie systemu HANA.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Kopiowania obiektu blob danych Azure dedykowanych dysków w oprogramowania kopii zapasowej RAID

W przeciwieństwie do ręcznego wirtualna danych kopii zapasowej w tej metody, co nie kopię zapasową wszystkich dysków danych maszyny wirtualnej do zapisania całej instalacji SAP, włącznie z danymi HANA HANA rejestruje plików i plików konfiguracji. Koncepcja jest są wyposażone w dedykowane oprogramowaniem RAID o zastosowanie rozkładania na wielu danych Azure wirtualne dyski twarde do przechowywania pełnej kopii zapasowej pliku SAP HANA. Kopiuje jeden tylko te dyski, których kopia zapasowa SAP HANA. Ich można łatwo być przechowywane w dedykowane konto magazynu kopii zapasowych HANA, lub dołączony do dedykowana &quot;kopii zapasowej maszyny Wirtualnej zarządzania&quot; dla dalszego przetwarzania.

![Zaangażowany wszystkie wirtualne dyski twarde zostały skopiowane przy użyciu ** start-azurestorageblobcopy ** polecenia programu PowerShell](media/sap-hana-backup-file-level/image031.png)

Po kopii zapasowej do oprogramowania lokalnego RAID, wszystkie wirtualne dyski twarde zaangażowany zostały skopiowane przy użyciu **start azurestorageblobcopy** polecenia programu PowerShell (zobacz [Start AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Ponieważ wpływa tylko na system plików dedykowanych zachować pliki kopii zapasowej, nie ma żadnych problemów dotyczących SAP HANA spójności pliku danych lub dziennika na dysku. Zaletą tego polecenia jest to działa, gdy maszyna wirtualna pozostaje w trybie online. Upewnij się, że żaden proces zapisuje do zestawu kopii zapasowych rozłożonego, należy go odinstalować przed kopiowania obiektów blob i zainstalować go ponownie później. Lub jeden można użyć w odpowiedni sposób &quot;Zablokuj&quot; systemu plików. Na przykład za pomocą xfs\_Zablokuj XFS systemu plików.

![Ten zrzut ekranu przedstawia listę obiektów blob w kontenerze wirtualne dyski twarde w portalu Azure](media/sap-hana-backup-file-level/image032.png)

Ten zrzut ekranu przedstawia listę obiektów blob w &quot;wirtualne dyski twarde&quot; kontenera w portalu Azure. Zrzut ekranu przedstawia pięciu dysków VHD, które zostały dołączone do serwera SAP HANA maszyna wirtualna może służyć jako oprogramowaniem RAID, aby zachować pliki kopii zapasowej SAP HANA. Przedstawia on także pięć kopii, które zostały pobrane za pomocą polecenia kopiowania obiektu blob.

![Do celów testowych, kopie dysków RAID SAP HANA oprogramowania kopii zapasowej zostały dołączone do serwera aplikacji maszyny Wirtualnej](media/sap-hana-backup-file-level/image033.png)

Do celów testowych, kopie dysków RAID SAP HANA oprogramowania kopii zapasowej zostały dołączone serwerowi aplikacji maszyny Wirtualnej.

![Kopiuje serwera aplikacji, które wyłączenia maszyny Wirtualnej można dołączyć dysku](media/sap-hana-backup-file-level/image034.png)

Serwer aplikacji maszyny Wirtualnej został wyłączony, aby podłączyć dysk kopiuje. Po uruchomieniu maszyny Wirtualnej, dyski i RAID odkryto poprawnie (zainstalowany za pomocą identyfikatora UUID). Tylko Brak punktu instalacji, który został utworzony za pomocą partycjonera YaST. Następnie kopii pliku kopii zapasowej SAP HANA stały się widoczne na poziomie systemu operacyjnego.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Skopiuj pliki kopii zapasowej SAP HANA do udziału NFS

Aby zmniejszyć potencjalnego wpływu na wydajność lub dysku w systemie SAP HANA, co może warto przechowywać SAP HANA pliki kopii zapasowej w udziale NFS. Technicznie działa, ale oznacza to, za pomocą drugiego maszyny Wirtualnej platformy Azure jako host udziału NFS. Nie należy mały rozmiar maszyny Wirtualnej, ze względu na przepustowość sieci maszyny Wirtualnej. Powinna mieć następnie zamknie to &quot;kopii zapasowej maszyny Wirtualnej&quot; i przełączyć go tylko do wykonywania kopii zapasowej SAP HANA. Zapisywanie w systemie plików NFS udziału umieszcza obciążenia w sieci i ma wpływ na system SAP HANA, ale tylko na zarządzanie później pliki kopii zapasowej &quot;kopii zapasowej maszyny Wirtualnej&quot; czy wpłynęło na wszystkich systemu SAP HANA.

![Udziału NFS z innej maszyny Wirtualnej platformy Azure został zainstalowany z serwerem SAP HANA maszyny Wirtualnej](media/sap-hana-backup-file-level/image035.png)

Aby sprawdzić, czy w przypadku użycia systemu plików NFS, udziału NFS z innej maszyny Wirtualnej platformy Azure został zainstalowany serwera SAP HANA maszyny Wirtualnej. Nie znaleziono żadnych specjalnych systemu plików NFS dostrajania stosowany.

![Zajęło 1 godzinę i 46 minut bezpośrednio do tworzenia kopii zapasowej](media/sap-hana-backup-file-level/image036.png)

Udział systemu plików NFS została szybkiego rozłożony, jak na serwerze SAP HANA. Niemniej jednak zajęło 1 godzinę i 46 minut do utworzenia kopii zapasowej bezpośrednio na zamiast 10 minut, gdy wartość Zapisywanie lokalnej usługi stripe udziału NFS.

![Alternatywa nie znacznie szybsza, bo na 1 i godz.](media/sap-hana-backup-file-level/image037.png)

Alternatywne o wykonaniu kopii zapasowej do lokalnego rozłożony i kopiowanie do udziału NFS na poziomie systemu operacyjnego (prosty **cp-avr** polecenia) nie została znacznie szybciej. Zajęło 1 i godz.

Dlatego działa, ale wydajność nie został dobrej kopii zapasowej testu 230 GB. Będzie ona wyglądać większe dla wielu terabajtów.

## <a name="copy-sap-hana-backup-files-to-azure-file-service"></a>Skopiuj pliki kopii zapasowej SAP HANA usługi plików na platformę Azure

Istnieje możliwość zainstalować udział plików na platformę Azure wewnątrz maszyny Wirtualnej systemu Linux na platformie Azure. Artykuł [jak używać magazynu plików Azure z systemem Linux](../../../storage/files/storage-how-to-use-files-linux.md) zawiera szczegółowe informacje o tym, jak to zrobić. Należy pamiętać, że jest obecnie limit przydziału 5 TB jeden udział plików na platformę Azure, a limit rozmiaru pliku o rozmiarze 1 TB na plik. Zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](../../../storage/common/storage-scalability-targets.md) dla informacji na temat limitów magazynu.

Badania wykazały, jednak bez tworzenia kopii zapasowej SAP HANA&#39;t obecnie pracować bezpośrednio z tego rodzaju CIFS instalacji. Również jest podany w [1820529 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1820529) który CIFS nie jest zalecane.

![Poniższy rysunek pokazuje błąd, w oknie dialogowym kopii zapasowej w SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Na poniższym rysunku pokazuje błąd w oknie dialogowym kopii zapasowej w systemie SAP HANA Studio podczas próby utworzyć kopię zapasową bezpośrednio do udziału plików na platformę Azure zainstalowane CIFS. Dlatego musi najpierw wykonaj standardowe SAP HANA kopii zapasowej w systemie plików maszyny Wirtualnej, a następnie skopiuj pliki kopii zapasowej z niego do usługi plików na platformę Azure.

![Na poniższym rysunku pokazano zajęło około 929 sekund, aby skopiować 19 SAP HANA pliki kopii zapasowej](media/sap-hana-backup-file-level/image039.png)

Na poniższym rysunku pokazano, że zajęło około 929 sekund kopiowania 19 SAP HANA pliki kopii zapasowej o całkowitym rozmiarze około 230 GB do udziału plików na platformę Azure.

![Struktura katalogów źródła na Maszynie wirtualnej SAP HANA został skopiowany do udziału plików na platformę Azure](media/sap-hana-backup-file-level/image040.png)

W tego zrzutu ekranu, co można zobaczyć, że struktura katalogów źródła na Maszynie wirtualnej SAP HANA został skopiowany do udziału plików na platformę Azure: jeden katalog (hana\_kopii zapasowej\_fsl\_15 gb) i 19 poszczególnych plików kopii zapasowej.

Przechowywanie plików kopii zapasowej SAP HANA na pliki Azure może być interesujące opcję w przyszłości w przypadku, gdy kopie zapasowe plików SAP HANA obsługuje go bezpośrednio. Lub jeśli staje się możliwe do usługi pliki Azure instalacji za pomocą systemu plików NFS i maksymalny limit przydziału jest znacznie wyższa niż 5 TB.

## <a name="next-steps"></a>Kolejne kroki
* [Podręcznik tworzenia kopii zapasowej programu SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md) zapewnia przegląd oraz informacje o wprowadzenie.
* [Kopia zapasowa SAP HANA oparte na magazynu migawek](sap-hana-backup-storage-snapshots.md) zawiera opis opcji tworzenia kopii zapasowej na podstawie migawki magazynu.
* Aby dowiedzieć się jak ustanowić wysokiej dostępności i planu odzyskiwania po awarii programu SAP HANA na platformie Azure (wystąpienia duże), zobacz [SAP HANA (duże wystąpień) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).

---
title: SAP HANA usługi Azure Backup na podstawie migawek magazynu | Microsoft Docs
description: Istnieją dwie istotne możliwości tworzenia kopii zapasowych SAP HANA na maszynach wirtualnych platformy Azure. w tym artykule omówiono SAP HANA tworzenia kopii zapasowych na podstawie migawek magazynu
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 8bcfdefa2ea9de12ca6029839a41c91111a5c61c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078599"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Kopie zapasowe oprogramowania SAP HANA na podstawie migawek magazynu

## <a name="introduction"></a>Wprowadzenie

Jest to część szeregu powiązanych artykułów w SAP HANA kopii zapasowej. [Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines](sap-hana-backup-guide.md) zawiera omówienie i informacje na temat rozpoczynania pracy, a [SAP HANA Azure Backup na poziomie pliku](sap-hana-backup-file-level.md) obejmuje opcję tworzenia kopii zapasowej opartej na plikach.

W przypadku korzystania z funkcji tworzenia kopii zapasowej maszyny wirtualnej dla pojedynczego wystąpienia systemu demonstracyjnego na jednym wystąpieniu, należy rozważyć wykonanie kopii zapasowej maszyny wirtualnej zamiast zarządzania kopiami zapasowymi HANA na poziomie systemu operacyjnego. Alternatywą jest przejęcie migawek obiektów blob platformy Azure w celu utworzenia kopii poszczególnych dysków wirtualnych, które są dołączone do maszyny wirtualnej i przechowywania plików danych platformy HANA. Jednak punkt krytyczny to spójność aplikacji podczas tworzenia kopii zapasowej maszyny wirtualnej lub migawki dysku, gdy system jest uruchomiony. Zapoznaj się z tematem _SAP HANA spójności danych podczas tworzenia migawek magazynu_ w powiązanym [podręczniku wykonywania kopii zapasowych artykułu SAP HANA na platformie Azure Virtual Machines](sap-hana-backup-guide.md). SAP HANA zawiera funkcję, która obsługuje te rodzaje migawek magazynu.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>SAP HANA migawek jako centralnej części kopii zapasowych spójnych z aplikacjami

W SAP HANA jest dostępna funkcja, która obsługuje tworzenie migawek magazynu. Istnieje ograniczenie dotyczące systemów z jednym kontenerem. Scenariusze pojedynczo SAP HANA serwerze MCS z więcej niż jedną dzierżawą nie obsługują tego rodzaju SAP HANA migawki bazy danych (zobacz [Tworzenie migawki magazynu (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Działa w następujący sposób:

- Przygotowywanie do utworzenia migawki magazynu przez inicjowanie migawki SAP HANA
- Uruchamianie migawki magazynu (na przykład migawek obiektów blob platformy Azure)
- Potwierdź migawkę SAP HANA

![Ten zrzut ekranu pokazuje, że SAP HANA migawki danych można utworzyć za pomocą instrukcji SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Ten zrzut ekranu pokazuje, że SAP HANA migawki danych można utworzyć za pomocą instrukcji SQL.

![Następnie migawka pojawia się również w wykazie kopii zapasowych w programie SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

Migawka zostanie również wyświetlona w wykazie kopii zapasowych w programie SAP HANA Studio.

![Na dysku migawka zostanie wyświetlona w katalogu danych SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

Na dysku migawka zostanie wyświetlona w katalogu danych SAP HANA.

Jeden z nich musi mieć pewność, że spójność systemu plików jest również gwarantowana przed uruchomieniem migawki magazynu, podczas gdy SAP HANA jest w trybie przygotowywania migawek. Zapoznaj się z tematem _SAP HANA spójności danych podczas tworzenia migawek magazynu_ w powiązanym [podręczniku wykonywania kopii zapasowych artykułu SAP HANA na platformie Azure Virtual Machines](sap-hana-backup-guide.md).

Po zakończeniu migawki magazynu krytyczne jest potwierdzenie SAP HANA migawki. Istnieje odpowiednia instrukcja SQL do uruchomienia: MIGAWKA zamykania danych kopii ZAPASowej (zobacz [instrukcje dotyczące zamykania migawki danych kopii zapasowej i odzyskiwania)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm).

> [!IMPORTANT]
> Potwierdź migawkę platformy HANA. Ze względu na&quot; KopiowaniewtrakciezapisuSAPHANAmogąwymagaćdodatkowegomiejscanadyskuwtrybieprzygotowywaniamigawekiniemożnauruchomićnowychkopiizapasowych,dopókiniezostaniepotwierdzoneSAPHANAmigawka.&quot;

## <a name="hana-vm-backup-via-azure-backup-service"></a>Tworzenie kopii zapasowej maszyny wirtualnej HANA za pośrednictwem usługi Azure Backup

Agent kopii zapasowej usługi Azure Backup nie jest dostępny dla maszyn wirtualnych z systemem Linux. Ponadto system Linux nie ma podobnej funkcjonalności, ponieważ w systemie Windows jest dostępna usługa VSS.  Aby można było korzystać z usługi Azure Backup na poziomie pliku/katalogu, jeden z nich skopiuje SAP HANA kopie zapasowe plików na maszynę wirtualną z systemem Windows, a następnie użyj agenta kopii zapasowej. 

W przeciwnym razie za pośrednictwem usługi Azure Backup jest możliwe tylko pełne kopie zapasowe maszyn wirtualnych z systemem Linux. Zobacz [Omówienie funkcji dostępnych w Azure Backup](../../../backup/backup-introduction-to-azure-backup.md) , aby dowiedzieć się więcej.

Usługa Azure Backup oferuje opcję tworzenia kopii zapasowej i przywracania maszyny wirtualnej. Więcej informacji na temat tej usługi i jej działania można znaleźć w artykule [Planowanie infrastruktury kopii zapasowych maszyn wirtualnych na platformie Azure](../../../backup/backup-azure-vms-introduction.md).

Istnieją dwa ważne zagadnienia, zgodnie z tym artykułem:

_&quot;W przypadku maszyn wirtualnych z systemem Linux możliwy jest tylko kopia zapasowa spójna z plikami, ponieważ system Linux nie ma równoważnej platformy do usługi VSS.&quot;_

_&quot;Aplikacje muszą implementować własny &quot;&quot; mechanizm naprawy dla przywróconych danych.&quot;_

W związku z tym przed rozpoczęciem tworzenia kopii zapasowej należy upewnić się, że SAP HANA jest w stanie spójnym na dysku. Zobacz _migawki SAP HANA_ opisane wcześniej w dokumencie. Ale występuje potencjalny problem, gdy SAP HANA pozostaje w tym trybie przygotowywania migawek. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki magazynu (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) .

Ten artykuł ma następujące stany:

_&quot;Zdecydowanie zaleca się potwierdzenie lub odrzucenie migawki magazynu tak szybko, jak to możliwe po utworzeniu. Gdy migawka magazynu jest przygotowywana lub tworzona, dane dotyczące migawki są zamrożone. Mimo że dane dotyczące migawki pozostają zablokowane, w bazie danych można nadal wprowadzać zmiany. Takie zmiany nie spowodują zmiany zablokowanej migawki. Zamiast tego zmiany są zapisywane do pozycji w obszarze danych, które są niezależne od migawki magazynu. Zmiany są również zapisywane w dzienniku. Jednak im dłużej trwa zamrożone dane powiązane z migawką, tym większa ilość danych może się zwiększyć.&quot;_

Azure Backup dba o spójność systemu plików za pośrednictwem rozszerzeń maszyny wirtualnej platformy Azure. Te rozszerzenia są niedostępne autonomicznie i działają tylko w połączeniu z usługą Azure Backup. Jednak nadal jest wymagane zapewnienie skryptów do tworzenia i usuwania migawki SAP HANA w celu zagwarantowania spójności aplikacji.

Azure Backup ma cztery główne etapy:

- Skrypt przygotowania wykonywania — skrypt musi utworzyć migawkę SAP HANA
- Utwórz migawkę
- Wykonaj skrypt po utworzeniu migawki — skrypt musi usunąć SAP HANA utworzony przez skrypt przygotowania
- Transferowanie danych do magazynu

Aby uzyskać szczegółowe informacje o tym, gdzie należy skopiować te skrypty i szczegółowe informacje o tym, jak działa Azure Backup dokładnie, zapoznaj się z następującymi artykułami:

- [Planowanie infrastruktury kopii zapasowych maszyny wirtualnej na platformie Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Kopia zapasowa spójna na poziomie aplikacji maszyn wirtualnych platformy Azure z systemem Linux](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



W tym momencie firma Microsoft nie opublikowała skryptów Prepare i skryptów po migawce dla SAP HANA. Ponieważ klient lub Integrator systemów będzie musiał utworzyć te skrypty i skonfigurować procedurę na podstawie dokumentacji podanej powyżej.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Przywracanie z kopii zapasowej spójnej na poziomie aplikacji względem maszyny wirtualnej
Proces przywracania kopii zapasowej spójnej na poziomie aplikacji wykonanej przez usługę Azure Backup został udokumentowany w artykule [odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> W artykule [odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) jest listą wyjątków i kroków wymienionych podczas korzystania z zestawów pasków dysków. Dyski rozłożone to zwykle zwykła konfiguracja maszyn wirtualnych dla SAP HANA. W związku z tym ważne jest, aby przeczytać artykuł i przetestować proces przywracania w taki sposób, jak opisano w artykule. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Klucz licencji HANA i przywracanie maszyny wirtualnej za pośrednictwem usługi Azure Backup

Usługa Azure Backup została zaprojektowana w celu utworzenia nowej maszyny wirtualnej podczas przywracania. Nie ma już planu, aby wykonać &quot;przywracanie w miejscu&quot; istniejącej maszyny wirtualnej platformy Azure.

![Ten rysunek przedstawia opcję przywracania usługi platformy Azure w Azure Portal](media/sap-hana-backup-storage-snapshots/image019.png)

Ten rysunek przedstawia opcję przywracania usługi platformy Azure w Azure Portal. Jeden z nich może wybrać między utworzeniem maszyny wirtualnej podczas przywracania lub przywracaniem dysków. Po przywróceniu dysków nadal trzeba utworzyć nową maszynę wirtualną na niej. Za każdym razem, gdy nowa maszyna wirtualna zostanie utworzona na platformie Azure, zmiany identyfikatora maszyny wirtualnej są unikatowe (zobacz [dostęp i używanie unikatowego identyfikatora maszyny wirtualnej platformy Azure](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Ten rysunek przedstawia unikatowy identyfikator maszyny wirtualnej platformy Azure przed i po przywróceniu za pośrednictwem usługi Azure Backup](media/sap-hana-backup-storage-snapshots/image020.png)

Ten rysunek przedstawia unikatowy identyfikator maszyny wirtualnej platformy Azure przed i po przywróceniu za pośrednictwem usługi Azure Backup. Klucz sprzętowy SAP używany na potrzeby licencjonowania SAP używa tego unikatowego identyfikatora maszyny wirtualnej. W związku z tym należy zainstalować nową licencję SAP po przywróceniu maszyny wirtualnej.

Nowa funkcja Azure Backup była wyświetlana w trybie podglądu podczas tworzenia tego przewodnika tworzenia kopii zapasowych. Umożliwia przywracanie na poziomie pliku na podstawie migawki maszyny wirtualnej, która została wykonana dla kopii zapasowej maszyny wirtualnej. Pozwala to uniknąć konieczności wdrażania nowej maszyny wirtualnej, w związku z czym unikatowy identyfikator maszyny wirtualnej pozostaje taki sam i nie jest wymagany nowy klucz licencji SAP HANA. Więcej dokumentacji dotyczącej tej funkcji będzie można znaleźć po jej pełnym przetestowaniu.

W Azure Backup ostatecznie będzie można tworzyć kopie zapasowe pojedynczych dysków wirtualnych platformy Azure oraz plików i katalogów z poziomu maszyny wirtualnej. Główną zaletą Azure Backup jest zarządzanie wszystkimi kopiami zapasowymi, co pozwala zaoszczędzić klienta. Jeśli przywracanie będzie konieczne, Azure Backup wybierze poprawną kopię zapasową do użycia.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>SAP HANA kopii zapasowej maszyny wirtualnej za pomocą migawki dysku ręcznego

Zamiast korzystać z usługi Azure Backup, jedna z nich może skonfigurować pojedyncze rozwiązanie do tworzenia kopii zapasowych przez tworzenie migawek obiektów blob platformy Azure ręcznie za pośrednictwem programu PowerShell. Opis kroków można znaleźć w temacie [Używanie migawek obiektów BLOB w programie PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) .

Zapewnia większą elastyczność, ale nie rozwiązuje problemów opisanych wcześniej w tym dokumencie:

- Nadal musisz upewnić się, że SAP HANA jest w spójnym stanie przez utworzenie migawki SAP HANA
- Nie można nadpisać dysku systemu operacyjnego, nawet jeśli cofnięto przydział maszyny wirtualnej z powodu błędu informującego o istnieniu dzierżawy. Działa ono tylko po usunięciu maszyny wirtualnej, co może prowadzić do nowego unikatowego identyfikatora maszyny wirtualnej i konieczności instalacji nowej licencji SAP.

![Możliwe jest przywrócenie tylko dysków danych maszyny wirtualnej platformy Azure](media/sap-hana-backup-storage-snapshots/image021.png)

Istnieje możliwość przywrócenia tylko dysków danych maszyny wirtualnej platformy Azure, co pozwala uniknąć problemu z uzyskaniem nowego unikatowego identyfikatora maszyny wirtualnej, w związku z czym unieważnia licencję SAP:

- Na potrzeby testu dwa dyski danych platformy Azure zostały dołączone do maszyny wirtualnej, a oprogramowanie RAID zostało zdefiniowane na ich podstawie 
- Potwierdzono, że SAP HANA był w spójnym stanie przez funkcję migawek SAP HANA
- Zablokuj system plików (zobacz _SAP HANA spójności danych podczas tworzenia migawek magazynu_ w powiązanym [podręczniku wykonywania kopii zapasowych artykułu dla SAP HANA na platformie Azure Virtual Machines](sap-hana-backup-guide.md))
- Migawki obiektów BLOB zostały wykonane z obu dysków danych
- Odblokuj system plików
- Potwierdzenie SAP HANA migawki
- Aby przywrócić dyski danych, maszyna wirtualna została zamknięta i oba dyski zostały odłączone
- Po odłączeniu dysków zostały zastąpione poprzednimi migawkami obiektów BLOB
- Przywrócone dyski wirtualne zostały ponownie dołączone do maszyny wirtualnej
- Po uruchomieniu maszyny wirtualnej wszystkie elementy na oprogramowaniu RAID działały prawidłowo i zostały ustawione z powrotem na czas migawki obiektu BLOB
- Znaleziono z powrotem w migawce platformy Hana

Jeśli było możliwe wyłączenie SAP HANA przed migawkami obiektów blob, procedura byłaby mniej złożona. W takim przypadku jeden może pominąć migawkę HANA i, jeśli nic innego nie ma w systemie, również pominąć blokowanie systemu plików. Dodaliśmy złożoność do obrazu, gdy jest to konieczne do wykonania migawek, gdy wszystko jest w trybie online. Zapoznaj się z tematem _SAP HANA spójności danych podczas tworzenia migawek magazynu_ w powiązanym [podręczniku wykonywania kopii zapasowych artykułu SAP HANA na platformie Azure Virtual Machines](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Następne kroki
* [Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines](sap-hana-backup-guide.md) zawiera omówienie i informacje na temat rozpoczynania pracy.
* [SAP HANA kopia zapasowa oparta na poziomie pliku](sap-hana-backup-file-level.md) obejmuje opcję tworzenia kopii zapasowej opartej na plikach.
* Aby dowiedzieć się, jak stworzyć wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).

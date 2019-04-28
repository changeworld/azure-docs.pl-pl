---
title: SAP HANA w usłudze Azure backup na podstawie migawek magazynu | Dokumentacja firmy Microsoft
description: Istnieją dwie główne kopii zapasowej możliwości platformy SAP Hana na maszynach wirtualnych platformy Azure, w tym artykule opisano kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu
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
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 74f47344afff630a8633b340ea4ce21db28db7ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60936605"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Kopie zapasowe oprogramowania SAP HANA na podstawie migawek magazynu

## <a name="introduction"></a>Wprowadzenie

Jest to część trzyczęściowej serii pokrewnych artykułów dotyczących kopii zapasowych oprogramowania SAP HANA. [Przewodnik tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md) zawiera przegląd i informacje na temat rozpoczynania pracy i [SAP HANA kopia zapasowa Azure na poziomie plików](sap-hana-backup-file-level.md) obejmuje opcję tworzenia kopii zapasowych opartych na plikach.

Korzystając z funkcji tworzenia kopii zapasowej maszyny Wirtualnej systemu jednego wystąpienia w jednym pokaz, jeden należy rozważyć, wykonanie kopii zapasowych maszyn wirtualnych, zamiast na zarządzaniu HANA tworzenia kopii zapasowych na poziomie systemu operacyjnego. Alternatywą jest migawek obiektów blob platformy Azure do tworzenia kopii poszczególnych dysków wirtualnych, które są dołączone do maszyny wirtualnej, i przechowywać pliki danych HANA. Ale punkt krytyczny spójności aplikacji podczas tworzenia migawki kopii zapasowej lub dysku maszyny Wirtualnej, gdy system jest włączony i uruchomiony. Zobacz _wyjaśnienie pojęcia spójności danych SAP HANA podczas robienia migawek magazynu_ w powiązanym artykule [przewodniku kopia zapasowa dla oprogramowania SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md). Oprogramowanie SAP HANA oferuje funkcję, która obsługuje te rodzaje migawek magazynu.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>Migawki platformy SAP HANA jako centralna część kopie zapasowe spójne z aplikacjami

SAP HANA, obsługujący wykonanie migawki magazynu jest funkcjonalność. Ma ograniczeń do jednego kontenera systemów. Scenariusze odtwarzać SAP HANA MCS z więcej niż jednej dzierżawy nie obsługują tego rodzaju migawki bazy danych SAP HANA (zobacz [Utwórz migawkę pamięci masowej (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Działa w następujący sposób:

- Przygotowanie do migawek magazynu, inicjując migawki platformy SAP HANA
- Uruchamianie magazynu migawek (obiektów blob platformy Azure. Tworzenie migawki, na przykład)
- Upewnij się, migawki platformy SAP HANA

![Ten zrzut ekranu pokazuje, że migawki danych SAP HANA można tworzyć za pomocą instrukcji języka SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Ten zrzut ekranu pokazuje, że migawki danych SAP HANA można tworzyć za pomocą instrukcji języka SQL.

![Migawka następnie pojawia się również w wykaz kopii zapasowych w systemie SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

Migawka również pojawi się w katalogu kopii zapasowej w programie SAP HANA Studio.

![Na dysku migawki pojawia się w katalogu danych SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

Na dysku migawki pojawia się w katalogu danych SAP HANA.

Jedna ma upewnić się, że spójności systemu plików jest gwarantowana przed uruchomieniem migawek magazynu oprogramowania SAP HANA jest w trybie przygotowania migawki. Zobacz _wyjaśnienie pojęcia spójności danych SAP HANA podczas robienia migawek magazynu_ w powiązanym artykule [przewodniku kopia zapasowa dla oprogramowania SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md).

Po zakończeniu migawki magazynu koniecznie upewnij się, migawki platformy SAP HANA. Brak instrukcję SQL do uruchomienia: Zamknij migawek chronionych danych kopii zapasowej (zobacz [danych zamknij MIGAWKI instrukcję BACKUP (kopia zapasowa i odzyskiwanie)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Upewnij się, migawki platformy HANA. Ze względu na &quot;kopii przy zapisie,&quot; platformy SAP HANA mogą wymagać dodatkowego miejsca na dysku w migawki — przygotowywanie trybu, i nie jest możliwe jej uruchomienie nowych kopii zapasowych, dopóki migawki platformy SAP HANA został potwierdzony.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Kopia zapasowa maszyny Wirtualnej platformy HANA, za pośrednictwem usługi Azure Backup

Agenta kopii zapasowej przez usługę Azure Backup nie jest dostępna dla maszyn wirtualnych systemu Linux. Ponadto systemu Linux nie ma podobne funkcje Windows ma ją za pomocą VSS.  Aby korzystać z usługi Azure backup na poziomie pliku lub katalogu, jeden czy skopiuj pliki kopii zapasowych oprogramowania SAP HANA do maszyny Wirtualnej z systemem Windows, a następnie użyć agenta kopii zapasowej. 

W przeciwnym razie tylko pełna kopia maszyny Wirtualnej systemu Linux jest możliwe za pośrednictwem usługi Azure Backup. Zobacz [omówienie funkcji usługi Azure Backup](../../../backup/backup-introduction-to-azure-backup.md) Aby dowiedzieć się więcej.

Usługa Azure Backup oferuje opcję tworzenia kopii zapasowej i przywracanie maszyny Wirtualnej. Więcej informacji na temat tej usługi, i jak to działa, można znaleźć w artykule [Planowanie infrastruktury kopii zapasowej maszyny Wirtualnej na platformie Azure](../../../backup/backup-azure-vms-introduction.md).

Istnieją dwie ważne uwagi, zgodnie z tym artykułem:

_&quot;W przypadku maszyn wirtualnych systemu Linux jest to możliwe, są tylko spójna na poziomie plików kopii zapasowych, ponieważ systemu Linux nie ma równoważnej platformy w programie VSS.&quot;_

_&quot;Aplikacje muszą implementować własne &quot;konfigurowania&quot; mechanizm przywróconych danych.&quot;_

W związku z tym jedna ma upewnij się, że oprogramowanie SAP HANA jest spójna na dysku, gdy rozpoczyna się tworzenie kopii zapasowej. Zobacz _migawek platformy SAP HANA_ opisane we wcześniejszej części dokumentu. Potencjalny problem występuje, gdy platformy SAP HANA pozostaje w tym trybie przygotowania migawki Zobacz [Utwórz migawkę pamięci masowej (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) Aby uzyskać więcej informacji.

Ten artykuł stanowi:

_&quot;Zdecydowanie zalecane jest aby potwierdzić, lub Porzuć migawki magazynu możliwie najszybciej po jego utworzeniu. Podczas migawki magazyn przygotowany lub utworzony, jest zamrożony migawkę odpowiednich danych. Gdy migawkę odpowiednich danych jest zablokowany, może nadal można wprowadzać zmian w bazie danych. Takie zmiany nie spowoduje zamrożone danych migawkę odpowiednich mają być zmienione. Zamiast tego zmiany są zapisywane do pozycji w obszarze danych, które są niezależne od migawki magazynu. Zmiany są również zapisywane w dzienniku. Jednak im dłuższy migawki odpowiednie dane są przechowywane zamrożone, większej ilości danych można powiększać.&quot;_

Usługa Azure Backup zajmuje się spójności systemu plików za pomocą rozszerzenia maszyny Wirtualnej platformy Azure. Rozszerzenia te nie są autonomiczne dostępne i działają tylko w połączeniu z usługą Azure Backup. Niemniej jednak nadal jest wymagane skrypty do tworzenia i usuwania migawki platformy SAP HANA w celu zagwarantowania spójności aplikacji.

Usługa Azure Backup ma cztery fazy główne:

- Wykonaj przygotowania skryptu — skrypt musi można utworzyć migawki platformy SAP HANA
- Utwórz migawkę
- Uruchom skrypt po utworzeniu migawki — skrypt musi usunąć pozycję SAP HANA utworzone przez skrypt przygotowania
- Przekaż dane do magazynu

Szczegółowe informacje o tym, gdzie można skopiować te skrypty i szczegółowe informacje o sposobie działania usługi Azure Backup dokładnie sprawdź następujące artykuły:

- [Planowanie infrastruktury kopii zapasowych maszyny wirtualnej na platformie Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Spójna na poziomie aplikacji Kopia zapasowa maszyn wirtualnych systemu Linux platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



W tym momencie nie opublikował Microsoft przygotowanie i skryptów po utworzeniu migawki dla oprogramowania SAP HANA. Jako integrator systemu lub klienta należy utworzyć te skrypty i skonfigurować procedury oparte na temat dokumentacji wyżej.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Przywracanie z kopii zapasowej spójnej na aplikacji dla maszyny Wirtualnej z systemem
Proces przywracania kopii zapasowej spójnej aplikacji podjęte przez usługę Azure backup jest opisane w artykule [odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> W artykule [odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) znajduje się lista wyjątków i kroki, korzystając z zestawów stripe dysków na liście. Rozłożone dyski są prawdopodobnie regularne konfiguracji maszyny Wirtualnej w przypadku oprogramowania SAP HANA. Dlatego jest niezbędne, aby przeczytać artykuł i przetestować proces przywracania w takich przypadkach, zgodnie z opisem w artykule. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Klucz licencji platformy HANA i maszyny Wirtualnej przywrócić za pomocą usługi Azure Backup

Usługa Azure Backup jest przeznaczone do tworzenia nowej maszyny Wirtualnej podczas przywracania. Nie jest planowane teraz celu &quot;w miejscu&quot; przywracania istniejącej maszyny Wirtualnej platformy Azure.

![Poniższy rysunek pokazuje opcja przywracania usługi platformy Azure w witrynie Azure portal](media/sap-hana-backup-storage-snapshots/image019.png)

Poniższy rysunek pokazuje opcja przywracania usługi platformy Azure w witrynie Azure portal. Można między utworzeniem maszyny Wirtualnej podczas przywracania lub przywracanie dysków. Po przywróceniu dyski, jest nadal konieczne jest utworzenie nowej maszyny Wirtualnej na jego podstawie. Zawsze, gdy nowa maszyna wirtualna zostanie utworzona na platformie Azure to unikatowy identyfikator maszyny Wirtualnej zmieni się (zobacz [dostęp i za pomocą usługi Azure VM Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Poniższy rysunek pokazuje Unikatowy identyfikator maszyny Wirtualnej platformy Azure, przed i po przywróceniu za pośrednictwem usługi Azure Backup](media/sap-hana-backup-storage-snapshots/image020.png)

Poniższy rysunek pokazuje Unikatowy identyfikator maszyny Wirtualnej platformy Azure, przed i po przywróceniu za pośrednictwem usługi Azure Backup. Klucza sprzętowego SAP, który jest używany dla rozwiązania SAP licencjonowania, używa tego unikatowego identyfikatora maszyny Wirtualnej. W konsekwencji nową licencję na oprogramowanie SAP zostały zainstalowane po przywracanie maszyny Wirtualnej.

Nowa funkcja usługi Azure Backup został przedstawiony w wersji zapoznawczej podczas tworzenia tego przewodnika tworzenia kopii zapasowej. Umożliwia przywrócenie poziomu pliku oparte na migawki maszyny Wirtualnej, która została wykonana dla maszyny Wirtualnej kopii zapasowej. Umożliwia to uniknięcie konieczności wdrażania nowej maszyny Wirtualnej i w związku z tym pozostaje taki sam Unikatowy identyfikator maszyny Wirtualnej i nie nowy klucz licencji platformy SAP HANA jest wymagana. Więcej dokumentacji na temat tej funkcji będzie dostępna po został w pełni przetestowany.

Usługa Azure Backup, po pewnym czasie umożliwi tworzenie kopii zapasowych poszczególnych dysków wirtualnych platformy Azure, a także pliki i katalogi z wewnątrz maszyny Wirtualnej. Ważną zaletą usługi Azure Backup jest jego zarządzania wszystkie kopie zapasowe, zapisywanie klienta trzeba to zrobić. Jeśli przywrócenie staje się to konieczne, kopia zapasowa Azure wybierze poprawne kopii zapasowej do użycia.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Kopia zapasowa maszyn wirtualnych programu SAP HANA, za pomocą migawki dysku ręczne

Zamiast korzystać z usługi Azure Backup, jeden można skonfigurować poszczególne rozwiązania tworzenia kopii zapasowych przez tworzenie migawek obiektów blob Azure wirtualne dyski twarde ręcznie za pomocą programu PowerShell. Zobacz [Using migawek obiektów blob za pomocą programu PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) opis kroków.

Zapewnia większą elastyczność, ale nie rozwiązuje problemy opisane we wcześniejszej części tego dokumentu:

- Nadal upewnij się, że oprogramowanie SAP HANA w spójnym stanie, tworząc migawki platformy SAP HANA
- Nie można zastąpić dysk systemu operacyjnego, nawet wtedy, gdy jest cofnięty ze względu na komunikat o błędzie informujący, że istnieje dzierżawa. Działa tylko po usunięciu maszyny Wirtualnej, co może spowodować nowy unikatowy identyfikator maszyny Wirtualnej i musisz zainstalować nową licencję na oprogramowanie SAP.

![Istnieje możliwość przywrócić tylko dysków danych maszyny Wirtualnej platformy Azure](media/sap-hana-backup-storage-snapshots/image021.png)

Go przywrócić tylko dysków danych maszyny wirtualnej portalu Azure, unikając problemów oznaczające wprowadzenie nowego unikatowego Identyfikatora maszyny Wirtualnej i unieważniana licencji SAP:

- Do testowania dwa dyski danych na platformie Azure zostały dołączone do maszyny Wirtualnej i macierzy RAID oprogramowania została zdefiniowana oparte na nich 
- Czy platformy SAP HANA była w spójnym stanie zostało potwierdzone przez funkcję migawki platformy SAP HANA
- Zablokuj systemu plików (zobacz _wyjaśnienie pojęcia spójności danych SAP HANA podczas robienia migawek magazynu_ w powiązanym artykule [przewodniku kopia zapasowa dla oprogramowania SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md))
- Migawki obiektów blob zostały pobrane z obydwa dyski danych
- Odblokuj systemu plików
- Potwierdzenie migawki platformy SAP HANA
- Aby przywrócić dyski z danymi, maszyna wirtualna została zamknięta, a obydwa dyski odłączone
- Po odłączanie dysków, zostały zastąpione przy użyciu migawek obiektów blob w poprzedniej wersji portalu
- Następnie przywróconych dysków wirtualnych zostały ponownie dołączone do maszyny Wirtualnej
- Po uruchomieniu maszyny Wirtualnej, wszystko, od oprogramowania RAID działały prawidłowo i ustawiono ponownie do czasu migawki obiektu blob
- HANA była ustawiana dla migawki HANA

Jeśli było to możliwe należy wyłączyć platformy SAP HANA przed migawek obiektów blob, procedura będzie mniej skomplikowany. W takim przypadku jeden można pominąć migawki HANA i, jeśli nic się dzieje w systemie, również pominąć blokowanie systemu plików. Zwiększania stopnia złożoności platformy trafia do obrazu, gdy jest to konieczne w celu migawki, gdy wszystko jest w trybie online. Zobacz _wyjaśnienie pojęcia spójności danych SAP HANA podczas robienia migawek magazynu_ w powiązanym artykule [przewodniku kopia zapasowa dla oprogramowania SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Kolejne kroki
* [Przewodnik tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md) zawiera omówienie oraz informacje na temat rozpoczynania pracy.
* [Kopia zapasowa oprogramowania SAP HANA na podstawie pliku poziomu](sap-hana-backup-file-level.md) obejmuje opcję tworzenia kopii zapasowych opartych na plikach.
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia), zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).

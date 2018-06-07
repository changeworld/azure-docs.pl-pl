---
title: Kopia zapasowa SAP HANA Azure oparte na magazynu migawek | Dokumentacja firmy Microsoft
description: Istnieją dwa główne kopii zapasowej możliwości SAP HANA na maszynach wirtualnych Azure, w tym artykule omówiono SAP HANA kopię zapasową pamięci masowej migawki
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
ms.openlocfilehash: 819888800b9663f9b920fbaf11b30ad28287a0b5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658727"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Kopie zapasowe oprogramowania SAP HANA na podstawie migawek magazynu

## <a name="introduction"></a>Wprowadzenie

To jest częścią serii trzyczęściowej pokrewne artykuły kopii zapasowej SAP HANA. [Podręcznik tworzenia kopii zapasowej programu SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md) zawiera omówienie i informacje wprowadzające, i [SAP HANA kopia zapasowa Azure na poziomie plików](sap-hana-backup-file-level.md) obejmuje opartych na plikach opcję tworzenia kopii zapasowej.

W przypadku używania funkcji Kopia zapasowa maszyny Wirtualnej dla systemu jednego wystąpienia w jednym demonstracyjnej, co należy rozważyć, wykonanie kopii zapasowej maszyny Wirtualnej, zamiast zarządzania HANA tworzenia kopii zapasowych na poziomie systemu operacyjnego. Alternatywą jest migawek obiektów blob platformy Azure można utworzyć kopie poszczególnych dysków wirtualnych, które są dołączone do maszyny wirtualnej, i przechowywać pliki danych HANA. Ale punktu krytycznego spójności aplikacji podczas tworzenia migawki kopii zapasowej lub dysku maszyny Wirtualnej, gdy system jest uruchomiony i uruchomiony. Zobacz _spójność danych SAP HANA podejmując magazynu migawek_ w powiązanego artykułu [Podręcznik tworzenia kopii zapasowej programu SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md). SAP HANA jest funkcją, która obsługuje następujące rodzaje pamięci masowej migawki.

## <a name="sap-hana-snapshots"></a>SAP HANA migawki

Brak funkcji w SAP HANA, który obsługuje tworzenie migawki magazynu. Jednak począwszy od grudnia 2016 r. istnieje ograniczenie do pojedynczego kontenera systemów. Konfiguracje wielodostępnym kontenera nie obsługują tego rodzaju migawki bazy danych (zobacz [tworzenia magazynu migawek (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Działa on w następujący sposób:

- Przygotowanie do migawki magazynu inicjując SAP HANA migawki
- Uruchom magazynu migawek (Azure blob migawki, na przykład)
- Potwierdź SAP HANA migawki

![Ten zrzut ekranu pokazuje, że migawki danych SAP HANA można tworzyć za pomocą instrukcji SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Ten zrzut ekranu pokazuje, że migawki danych SAP HANA można tworzyć za pomocą instrukcji SQL.

![Migawki również pojawia się w katalogu kopii zapasowej w SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

Migawki następnie pojawia się również w katalogu kopii zapasowej w SAP HANA Studio.

![Na dysku migawki zostaną wyświetlone w katalogu danych SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

Na dysku migawki zostaną wyświetlone w katalogu danych SAP HANA.

Jeden ma upewnij się, że spójności systemu plików jest gwarantowana przed uruchomieniem migawki magazynu, gdy SAP HANA jest w trybie przygotowania migawki. Zobacz _spójność danych SAP HANA podejmując magazynu migawek_ w powiązanego artykułu [Podręcznik tworzenia kopii zapasowej programu SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md).

Po zakończeniu migawki magazynu jest krytyczne, aby potwierdzić migawki SAP HANA. Brak odpowiedniej instrukcji SQL do uruchomienia: Zamknij MIGAWKI danych kopii zapasowej (zobacz [danych zamknij MIGAWKI instrukcji BACKUP (kopii zapasowych i odzyskiwania)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Potwierdź migawki HANA. Ze względu na &quot;kopii przy zapisie,&quot; SAP HANA mogą wymagać dodatkowego miejsca na dysku w migawki — przygotowanie trybu i nie jest możliwe jej uruchomienie nowych kopii zapasowych, dopóki migawki SAP HANA został potwierdzony.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Maszyna wirtualna HANA tworzenia kopii zapasowej za pomocą usługi Kopia zapasowa Azure

Począwszy od grudnia 2016 r. agenta kopii zapasowej usługi Kopia zapasowa Azure nie jest dostępna dla maszyn wirtualnych systemu Linux. Aby korzystanie z kopii zapasowej platformy Azure na poziomie pliku/katalogu, co będzie skopiuj pliki kopii zapasowej SAP HANA do maszyny Wirtualnej systemu Windows, a następnie użyć agenta kopii zapasowej. W przeciwnym razie tylko pełna kopia maszyny Wirtualnej systemu Linux jest możliwe za pomocą usługi Kopia zapasowa Azure. Zobacz [omówienie funkcji Kopia zapasowa Azure](../../../backup/backup-introduction-to-azure-backup.md) Aby dowiedzieć się więcej.

Usługa Kopia zapasowa Azure udostępnia opcję kopii zapasowej i przywracania maszyny Wirtualnej. Więcej informacji na temat tej usługi oraz sposób jej działania można znaleźć w artykule [Zaplanuj infrastrukturę kopii zapasowej maszyny Wirtualnej na platformie Azure](../../../backup/backup-azure-vms-introduction.md).

Istnieją dwa istotne kwestie, zgodnie z tym artykułem:

_&quot;Dla maszyn wirtualnych systemu Linux tylko plik spójne kopie zapasowe są możliwe, ponieważ systemu Linux nie ma równoważnej platformy do usługi VSS.&quot;_

_&quot;Aplikacje muszą implementować własnych &quot;konfigurowania&quot; mechanizmu, na przywróconych danych.&quot;_

W związku z tym co ma upewnij się, że SAP HANA jest spójna na dysku podczas uruchamiania kopii zapasowej. Zobacz _migawki SAP HANA_ opisem we wcześniejszej części dokumentu. Potencjalny problem występuje, gdy SAP HANA pozostaje w tym trybie przygotowania migawki Zobacz [tworzenia magazynu migawek (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) Aby uzyskać więcej informacji.

Ten artykuł stanowi:

_&quot;Zdecydowanie zalecane jest aby potwierdzić, lub Porzuć migawki magazynu jak najszybciej po jego utworzeniu. Podczas tworzenia migawki magazynu przetworzone lub utworzone, migawkę odpowiednich danych jest zablokowana. Podczas migawkę odpowiednich danych jest zablokowany, może nadal być zmian w bazie danych. Takie zmiany nie spowodują zablokowane odpowiednich migawka danych zostanie zmieniony. Zamiast tego zmiany są zapisywane do pozycji w obszarze danych, które są niezależne od magazynu migawek. Zmiany są także zapisywane w dzienniku. Jednak im dłuższy migawkę odpowiednich danych jest przechowywana zablokowane, tym bardziej może zwiększyć ilość danych.&quot;_

Kopia zapasowa Azure zapewnia obsługę spójności systemu plików za pośrednictwem rozszerzenia maszyny Wirtualnej platformy Azure. Rozszerzenia te nie są dostępne autonomicznej i działa tylko w połączeniu z usługą kopia zapasowa Azure. Niemniej jednak nadal jest wymagane do zarządzania migawki SAP HANA w celu zagwarantowania spójności aplikacji.

Kopia zapasowa Azure ma dwa główne fazy:

- Utwórz migawkę
- Przesyłanie danych do magazynu

Dlatego można potwierdzić migawki SAP HANA po zakończeniu fazy usługi Kopia zapasowa Azure wykonywania migawki. Może upłynąć kilka minut, aby zobaczyć w portalu Azure.

![Na poniższym rysunku pokazano częścią listy zadania tworzenia kopii zapasowych usługi Kopia zapasowa Azure](media/sap-hana-backup-storage-snapshots/image014.png)

Na poniższym rysunku pokazano częścią listy zadania tworzenia kopii zapasowych usługi Kopia zapasowa Azure, który został użyty do kopii zapasowych HANA test maszyny Wirtualnej.

![Aby wyświetlić szczegóły zadania, kliknij zadanie tworzenia kopii zapasowej w portalu Azure](media/sap-hana-backup-storage-snapshots/image015.png)

Aby wyświetlić szczegóły zadania, kliknij zadanie tworzenia kopii zapasowej w portalu Azure. W tym miejscu jedną Zobacz dwóch faz. Może upłynąć kilka minut, aż będzie wyświetlana na etapie migawki jako ukończone. W większości przypadków rozkłada się na etapie transferu danych.

## <a name="hana-vm-backup-automation-via-azure-backup-service"></a>Maszyna wirtualna HANA automatyzacji tworzenia kopii zapasowej za pomocą usługi Kopia zapasowa Azure

Co można potwierdzić ręcznie migawki SAP HANA po zakończeniu fazy migawki kopii zapasowej Azure, zgodnie z wcześniejszym opisem, ale warto wziąć pod uwagę automatyzacji, ponieważ administrator nie może monitorować listy zadanie tworzenia kopii zapasowej w portalu Azure.

W tym miejscu jest wyjaśnienie, jak można można zrealizować przy użyciu poleceń cmdlet programu Azure PowerShell.

![Usługa Kopia zapasowa Azure została utworzona nazwa hana-magazynie kopii zapasowej —](media/sap-hana-backup-storage-snapshots/image016.png)

Usługa Kopia zapasowa Azure została utworzona o nazwie &quot;magazynu w przypadku kopii zapasowych hana.&quot; Polecenie PS **Get AzureRmRecoveryServicesVault-nazwa magazynu w przypadku kopii zapasowych hana** pobiera odpowiedni obiekt. Ten obiekt jest następnie używany do Ustaw kontekst kopii zapasowych, jak pokazano na rysunku dalej.

![Jeden sprawdzić, czy zadanie tworzenia kopii zapasowej w toku](media/sap-hana-backup-storage-snapshots/image017.png)

Po ustawieniu poprawny kontekst, jeden Sprawdź, czy zadanie tworzenia kopii zapasowej w toku, a następnie wyszukaj jego szczegóły zadania. Na liście podzadanie przedstawiono, jeśli fazy migawki zadanie tworzenia kopii zapasowej systemu Azure jest już ukończone:

```
$ars = Get-AzureRmRecoveryServicesVault -Name hana-backup-vault
Set-AzureRmRecoveryServicesVaultContext -Vault $ars
$jid = Get-AzureRmRecoveryServicesBackupJob -Status InProgress | select -ExpandProperty jobid
Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
```

![Sondowanie wartość w pętlę aż stanie się na ukończone](media/sap-hana-backup-storage-snapshots/image018.png)

Po szczegóły zadania są przechowywane w zmiennej, jest po prostu PS składnię, aby uzyskać dostęp do pierwszej pozycji tablicy i pobrać wartość stanu. Do wykonania skryptu automatyzacji, sondowanie wartość w pętli do momentu przechodzi on w &quot;ukończone.&quot;

```
$st = Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
$st[0] | select -ExpandProperty status
```

## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Klucz licencji HANA i wirtualna przywrócić za pomocą usługi Kopia zapasowa Azure

Usługa Kopia zapasowa Azure jest przeznaczone do tworzenia nowej maszyny Wirtualnej podczas przywracania. Nie jest od razu planowane czy &quot;w miejscu&quot; przywracania istniejącej maszyny Wirtualnej platformy Azure.

![Na poniższym rysunku pokazano opcja przywracania usługi Azure w portalu Azure](media/sap-hana-backup-storage-snapshots/image019.png)

Tej ilustracji przedstawiono opcja przywracania usługi Azure w portalu Azure. Można między tworzenia maszyny Wirtualnej podczas przywracania lub przywracanie dyski. Po przywróceniu dysków, jest nadal niezbędnych do utworzenia nowej maszyny Wirtualnej znajdujący się na nim. Zawsze, gdy nowej maszyny Wirtualnej jest tworzony na platformie Azure Unikatowy identyfikator maszyny Wirtualnej zmienia (zobacz [dostęp i przy użyciu usługi Azure VM Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Na poniższym rysunku pokazano Unikatowy identyfikator maszyny Wirtualnej platformy Azure, przed i po przywróceniu za pomocą usługi Kopia zapasowa Azure](media/sap-hana-backup-storage-snapshots/image020.png)

Na poniższym rysunku pokazano Unikatowy identyfikator maszyny Wirtualnej platformy Azure, przed i po przywróceniu za pomocą usługi Kopia zapasowa Azure. Klucz sprzętu SAP, który jest używany do licencjonowania SAP, używa tego Unikatowy identyfikator maszyny Wirtualnej. W konsekwencji nowej licencji SAP musi być zainstalowany po przywracanie maszyny Wirtualnej.

Nowa funkcja Kopia zapasowa Azure został przedstawiony w wersji zapoznawczej podczas tworzenia kopii zapasowej przewodnika. Umożliwia przywrócenie poziomu pliku oparte na migawki maszyny Wirtualnej, która została wykonana dla maszyny Wirtualnej kopii zapasowej. Dzięki temu można uniknąć konieczności wdrażania nowej maszyny Wirtualnej i w związku z tym pozostaje taki sam Unikatowy identyfikator maszyny Wirtualnej, a nie nowy klucz licencji SAP HANA jest wymagana. Więcej dokumentacji na ta funkcja będzie dostępna po został w pełni przetestowany.

Kopia zapasowa Azure ostatecznie umożliwi kopii zapasowej poszczególnych dysków wirtualnych platformy Azure, oraz pliki i katalogi z wewnątrz maszyny Wirtualnej. Główną zaletą usługi Kopia zapasowa Azure jest jego zarządzania wszystkie kopie zapasowe, zapisywanie klienta z konieczności to robić. Jeśli niezbędne przywracania kopii zapasowej Azure wybierze poprawne kopii zapasowej do użycia.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Kopia zapasowa maszyny Wirtualnej programu SAP HANA za pomocą migawki ręczne dysku

Zamiast przy użyciu usługi Kopia zapasowa Azure, co może skonfigurować poszczególnych tworzenia kopii zapasowych przez tworzenie migawek obiektów blob Azure wirtualne dyski twarde ręcznie za pomocą programu PowerShell. Zobacz [migawki obiektu blob korzystanie z programu PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) opis kroków.

Zapewnia większą elastyczność, ale nie rozwiązuje problemy wyjaśniono wcześniej w tym dokumencie:

- Jeden nadal należy się upewnić, że SAP HANA jest spójne
- Nie można zastąpić dysk systemu operacyjnego, nawet jeśli cofnięciu przydziału maszyny Wirtualnej z powodu komunikat o błędzie informujący, że istnieje dzierżawy. Działa ona tylko po usunięciu maszyny Wirtualnej, co może prowadzić do nowy unikatowy identyfikator maszyny Wirtualnej i konieczności instalowania nowej licencji SAP.

![Można przywrócić tylko dysków danych maszyny Wirtualnej platformy Azure](media/sap-hana-backup-storage-snapshots/image021.png)

Jest możliwe przywrócenie tylko dysków danych maszyny Wirtualnej platformy Azure, unikając problem pobierania nowy unikatowy identyfikator maszyny Wirtualnej i, w związku z tym unieważnienie licencji SAP:

- Dla testu dwa dyski danych Azure zostały dołączone do maszyn wirtualnych i oprogramowaniem RAID został zdefiniowany oparte na nich 
- Funkcja migawki SAP HANA potwierdzono, że SAP HANA była w spójnym stanie
- Zablokuj systemu plików (zobacz _spójność danych SAP HANA podejmując magazynu migawek_ w powiązanego artykułu [Podręcznik tworzenia kopii zapasowej programu SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md))
- Migawki obiektu blob zostały pobrane z obu dysków z danymi
- Odblokuj systemu plików
- Potwierdzenie migawki SAP HANA
- Aby przywrócić dysków z danymi, wyłączenia maszyny Wirtualnej i obydwa dyski odłączone
- Po odłączanie dysków, zostały zastąpione z migawkami wcześniejsze obiektów blob
- Następnie przywrócone dyski wirtualne zostały ponownie dołączyć do maszyny Wirtualnej
- Po uruchomieniu maszyny Wirtualnej, wszystko na oprogramowanie RAID działał prawidłowo i został ponownie ustawiony na czas migawki obiektu blob
- HANA była ustawiana dla migawki HANA

Jeśli było możliwe, zamknij SAP HANA przed migawki obiektu blob, procedura może być mniej złożona. W takim przypadku jeden można pominąć migawki HANA i, jeśli nic dzieje się w systemie, również pominąć zablokować systemu plików. Dodano złożoności wejścia obrazu, gdy jest to niezbędne wykonać migawki, podczas wszystko jest w trybie online. Zobacz _spójność danych SAP HANA podejmując magazynu migawek_ w powiązanego artykułu [Podręcznik tworzenia kopii zapasowej programu SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Kolejne kroki
* [Podręcznik tworzenia kopii zapasowej programu SAP HANA na maszynach wirtualnych Azure](sap-hana-backup-guide.md) zapewnia przegląd oraz informacje o wprowadzenie.
* [Kopia zapasowa SAP HANA oparte na poziomie plików](sap-hana-backup-file-level.md) obejmuje opartych na plikach opcję tworzenia kopii zapasowej.
* Aby dowiedzieć się jak ustanowić wysokiej dostępności i planu odzyskiwania po awarii programu SAP HANA na platformie Azure (wystąpienia duże), zobacz [SAP HANA (duże wystąpień) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).

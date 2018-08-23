---
title: 'Usługa Azure Backup: Odzyskiwanie plików i folderów z kopii zapasowej maszyny Wirtualnej platformy Azure'
description: Odzyskiwanie plików z punktu odzyskiwania maszyny wirtualnej platformy Azure
services: backup
author: pvrk
manager: shivamg
keywords: odzyskiwanie na poziomie elementu; odzyskiwanie plików z kopii zapasowej maszyny Wirtualnej platformy Azure; Przywracanie plików z maszyny Wirtualnej platformy Azure
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2018
ms.author: pullabhk
ms.openlocfilehash: 1f3b81c31dc566e5e3011167eee00145f6791cb1
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2018
ms.locfileid: "42616913"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure

Usługa Azure Backup udostępnia możliwość przywracania [Azure virtual machines (VMs) i dyski](./backup-azure-arm-restore-vms.md) z kopii zapasowych maszyn wirtualnych platformy Azure, znany także jako punktów odzyskiwania. W tym artykule wyjaśniono, jak odzyskiwanie plików i folderów z kopii zapasowej maszyny Wirtualnej platformy Azure. Przywracanie plików i folderów jest dostępna tylko dla maszyn wirtualnych platformy Azure wdrożone za pomocą modelu usługi Resource Manager i chronione do magazynu usługi Recovery services.

> [!Note]
> Ta funkcja jest dostępna dla maszyn wirtualnych platformy Azure wdrożone za pomocą modelu usługi Resource Manager i chronione w magazynie usługi Recovery Services.
> Odzyskiwanie plików z kopii zapasowej zaszyfrowanych maszyn wirtualnych nie jest obsługiwana.
>

## <a name="mount-the-volume-and-copy-files"></a>Instalacji woluminu i kopiować pliki

Aby przywrócić pliki lub foldery z punktu odzyskiwania, przejdź do maszyny wirtualnej, a następnie wybierz punkt odzyskiwania.

1. Zaloguj się do [witryny Azure portal](http://portal.Azure.com) i w okienku po lewej stronie kliknij **maszyn wirtualnych**. Z listy maszyn wirtualnych wybierz maszynę wirtualną, aby otworzyć pulpit nawigacyjny tej maszyny wirtualnej.

2. W menu maszyny wirtualnej, kliknij przycisk **kopii zapasowej** aby otworzyć pulpit nawigacyjny kopii zapasowej.

    ![Otwórz element kopii zapasowej magazynu usług Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. W menu pulpitu nawigacyjnego kopii zapasowej, kliknij przycisk **odzyskiwanie plików**.

    ![Przycisk odzyskiwania plików](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    **Odzyskiwanie plików** zostanie otwarte menu.

    ![Menu odzyskiwania plików](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Z **wybierz punkt odzyskiwania** menu rozwijanego wybierz punkt odzyskiwania, który zawiera pliki mają. Domyślnie najnowszy punkt odzyskiwania jest zaznaczona.

5. Aby pobrać oprogramowanie używany do kopiowania plików z punktu odzyskiwania, kliknij przycisk **Pobierz plik wykonywalny** (w przypadku maszyn wirtualnych platformy Azure Windows) lub **Pobierz skrypt** (dla systemu Linux maszyny Wirtualnej platformy Azure).

    ![Wygenerowane hasło](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure pobierze plik wykonywalny lub skrypt na komputerze lokalnym.

    ![Pobierz wiadomość dla pliku wykonywalnego lub skryptu](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Zalecane jest, aby uruchomić plik wykonywalny lub skrypt jako administrator, Zapisz pliki do pobrania na komputerze.

6. Plik wykonywalny lub skrypt jest chroniona hasłem i wymaga hasła. W **odzyskiwanie plików** menu, kliknij przycisk kopiowania, aby załadować hasło do pamięci.

    ![Wygenerowane hasło](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Z lokalizacji pobierania, (zwykle w folderze plików do pobrania) kliknij prawym przyciskiem myszy plik wykonywalny lub skrypt, a następnie uruchom go przy użyciu poświadczeń administratora. Po wyświetleniu monitu wpisz hasło lub wkleić hasło z pamięci, a następnie naciśnij klawisz Enter. Po wprowadzeniu prawidłowego hasła skrypt łączy się z punktu odzyskiwania.

    ![Menu odzyskiwania plików](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Jeśli uruchamiasz skrypt na komputerze z ograniczonym dostępem, upewnij się, ma dostęp do:

    - download.microsoft.com
    - Adresy URL usługi odzyskiwania (geograficznie nazwa odnosi się do regionu, w której znajduje się w magazynie usług odzyskiwania)
        - <https://pod01-rec2.geo-name.backup.windowsazure.com> (W przypadku publicznych regionach geograficznych systemu Azure)
        - <https://pod01-rec2.geo-name.backup.windowsazure.cn> (W przypadku chińska wersja platformy Azure)
        - <https://pod01-rec2.geo-name.backup.windowsazure.us> (Dla platformy Azure dla instytucji rządowych USA)
        - <https://pod01-rec2.geo-name.backup.windowsazure.de> (W przypadku platformy Azure (Niemcy))
    - wychodząca przez port 3260

    Dla systemu Linux skrypt wymaga "Otwórz za pomocą usługi iscsi" i "lshw" składników, aby połączyć się z punktem odzyskiwania. Jeśli nie istnieją składniki na komputerze, na którym skrypt jest uruchamiany, skrypt monituje o podanie uprawnień zainstalować składniki. Zgody Aby zainstalować wymagane składniki.

    Dostęp do witrynie download.microsoft.com jest wymagany do pobierania składniki używane do tworzenia bezpiecznego kanału między komputerem, w której skrypt jest uruchamiany i danych w punkcie odzyskiwania.

    Skrypt można uruchomić na dowolnym komputerze, który ma system operacyjny tego samego (lub zgodny) jako kopii zapasowej maszyny Wirtualnej. Zobacz [tabeli zgodny system operacyjny](backup-azure-restore-files-from-vm.md#system-requirements) zgodnych systemów operacyjnych. Jeśli chronionej maszyny wirtualnej platformy Azure używa do magazynowania systemu Windows (w przypadku maszyn wirtualnych platformy Azure Windows) lub tablic LVM/RAID (dla maszyn wirtualnych systemu Linux), nie możesz uruchomić plik wykonywalny lub skrypt na tej samej maszyny wirtualnej. Zamiast tego należy uruchomić plik wykonywalny lub skrypt na dowolnym komputerze, przy użyciu zgodny system operacyjny.

### <a name="identifying-volumes"></a>Identyfikowanie woluminów

#### <a name="for-windows"></a>W przypadku systemu Windows

Podczas uruchamiania pliku wykonywalnego systemu operacyjnego na komputerze instalująca nowe woluminy i przypisuje litery dysku. Aby przeglądać te dyski, można użyć Eksploratora Windows lub Eksploratora plików. Litery dysków przypisane do woluminów może nie być tych samych liter co oryginalna maszyna wirtualna, jednak nazwa woluminu są zachowywane. Na przykład, jeśli wolumin na oryginalna maszyna wirtualna została "dysk z danymi (E:`\`)", że wolumin może zostać dołączony na komputerze lokalnym jako "dysk z danymi (wszystkie litery:`\`). Przeglądaj wszystkie woluminy wymienionych w danych wyjściowych skryptu, dopóki nie znajdziesz plików/folderu.  

   ![Menu odzyskiwania plików](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>W przypadku systemu Linux

W systemie Linux woluminy punktu odzyskiwania są instalowane w folderze, w którym skrypt jest uruchamiany. W związku z tym są wyświetlane dołączonych dysków, woluminów i odpowiadające im ścieżki instalacji. Zainstaluj te ścieżki są widoczne dla użytkowników mających dostęp na poziomie głównym. Przeglądaj woluminy wymienionych w danych wyjściowych skryptu.

  ![Menu odzyskiwania plików w systemie Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  
## <a name="closing-the-connection"></a>Zamykanie połączenia

Po Trwa identyfikowanie plików i skopiować je do lokalizacji magazynu lokalnego, usuń lub odinstaluj dodatkowe dyski. Aby odinstalować dyski, na **odzyskiwanie plików** menu w witrynie Azure portal kliknij **odinstaluj dyski**.

![Odinstaluj dyski](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Gdy dyski zostały odinstalowane, komunikat o błędzie. Może upłynąć kilka minut, zanim połączenie w celu odświeżenia, aby usunąć dyski.

W systemie Linux, po połączenie punkt odzyskiwania jest oddzielone, system operacyjny nie powoduje usunięcia odpowiednich ścieżkach instalacji automatycznie. Ścieżki instalacji istnieją jako woluminy "porzucone" i są widoczne, ale sygnalizować błąd, gdy użytkownik dostęp do zapisu plików. Można je ręcznie usunąć. Skrypt uruchamiania, identyfikuje tych woluminów, istniejący element z poprzednich punktów odzyskiwania i czyści podczas wyrażania zgody.

## <a name="special-configurations"></a>Specjalne konfiguracje

### <a name="dynamic-disks"></a>Dyski dynamiczne

Jeśli chronione maszyny Wirtualnej platformy Azure ma woluminy zawierające jeden lub oba z następujących cech, nie można uruchomić pliku wykonywalnego skrypt na tej samej maszyny Wirtualnej.

    - Woluminy, które obejmują wiele dysków (łączone i woluminów rozłożonych)
    - Odporne na uszkodzenia (woluminy dublowane i RAID-5) w przypadku dysków dynamicznych

Zamiast tego należy uruchomić pliku wykonywalnego skryptów na dowolnym innym komputerze przy użyciu zgodny system operacyjny.

### <a name="windows-storage-spaces"></a>Miejsca do magazynowania systemu Windows

Miejsca do magazynowania systemu Windows jest technologii Windows, która umożliwia również wirtualizację magazynowania. Za pomocą funkcji miejsca do magazynowania systemu Windows można grupować standardowych dysków w pule magazynu. Następnie przy użyciu dostępnego miejsca w tych pulach magazynu do tworzenia dysków wirtualnych, nazywanych miejscami do magazynowania.

Jeśli chronione maszyny Wirtualnej platformy Azure korzysta z funkcji miejsca do magazynowania systemu Windows, nie można uruchomić pliku wykonywalnego skrypt na tej samej maszyny Wirtualnej. Zamiast tego należy uruchomić pliku wykonywalnego skryptów na dowolnym komputerze, przy użyciu zgodny system operacyjny.

### <a name="lvmraid-arrays"></a>Tablice LVM/RAID

W systemie Linux Menedżer woluminów logicznych (LVM) i/lub macierzy RAID oprogramowania są używane do zarządzania woluminów logicznych na wielu dyskach. Jeśli chronione maszyny Wirtualnej systemu Linux używa LVM i/lub macierzy RAID, nie można uruchomić skrypt na tej samej maszyny Wirtualnej. Zamiast tego Uruchom skrypt na dowolnym komputerze, przy użyciu zgodny system operacyjny i który obsługuje system plików chronionej maszyny Wirtualnej.

Następujące dane wyjściowe skryptu wyświetla dyski LVM i/lub macierzy RAID i woluminów przy użyciu typu partycji.

   ![Dane wyjściowe LVM Linux menu](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Aby przywrócić te partycje w trybie online, Uruchom polecenia w poniższych sekcjach.

#### <a name="for-lvm-partitions"></a>W przypadku partycji LVM

Aby wyświetlić listę nazw grupy woluminów w woluminie fizycznym.

```bash
#!/bin/bash
$ pvs <volume name as shown above in the script output>
```

Aby wyświetlić listę wszystkich woluminów logicznych, nazwy i ich ścieżek w grupie woluminu.

```bash
#!/bin/bash
$ lvdisplay <volume-group-name from the pvs command’s results>
```

Aby zainstalować woluminów logicznych do ścieżki wybranego.

```bash
#!/bin/bash
$ mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Dla macierzy RAID

Następujące polecenie wyświetla szczegółowe informacje o wszystkich dysków raid.

```bash
#!/bin/bash
$ mdadm –detail –scan
```

 Odpowiedni dysk RAID jest wyświetlany jako `/dev/mdm/<RAID array name in the protected VM>`

Jeśli na dysku RAID woluminy fizyczne, należy użyć polecenia instalacji.

```bash
#!/bin/bash
$ mount [RAID Disk Path] [/mountpath]
```

Jeśli dysk RAID inny LVM skonfigurowane w nim, następnie użyciu powyższej procedury LVM partycji, ale użyj nazwy woluminu, zamiast nazwy dysku RAID

## <a name="system-requirements"></a>Wymagania systemowe

### <a name="for-windows-os"></a>Dla systemu operacyjnego Windows

W poniższej tabeli przedstawiono zgodność między systemami operacyjnymi serwera i komputera. W przypadku odzyskiwania plików, nie można przywrócić plików do wersji poprzedniej lub przyszłe systemu operacyjnego. Na przykład nie można przywrócić pliku z maszyny Wirtualnej z systemem Windows Server 2016 do komputera systemu Windows 8 lub Windows Server 2012. Ten sam system operacyjny serwera lub system operacyjny zgodny klienta, możesz przywrócić pliki z maszyny Wirtualnej.

|System operacyjny serwera | Zgodny system operacyjny klienta  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>System operacyjny Linux

W systemie Linux systemu operacyjnego komputera służące do przywrócenia plików musi obsługiwać system plików chronionej maszyny wirtualnej. Po wybraniu komputera, aby uruchomić skrypt, upewnij się, komputer ma zgodny system operacyjny i używana jest jedną z wersji wskazanych w poniższej tabeli:

|System operacyjny Linux | Wersje  |
| --------------- | ---- |
| Ubuntu | 12.04 i nowsze wersje |
| CentOS | 6.5 i nowsze wersje  |
| RHEL | 6.7 i nowsze wersje |
| Debian | 7 i nowsze wersje |
| Oracle Linux | 6.4 i nowsze wersje |
| SLES | 12 i nowsze wersje |
| openSUSE | 42.2 i nowsze wersje |

Skrypt wymaga również składników języka Python i bash wykonanie i bezpieczne łączenie się z punktu odzyskiwania.

|Składnik | Wersja  |
| --------------- | ---- |
| Bash | 4 i nowsze wersje |
| python | 2.6.6 i nowsze wersje  |
| Protokół TLS | 1.2 powinna być obsługiwana.  |

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy podczas odzyskiwania pliki z maszyn wirtualnych, sprawdź poniższej tabeli, aby uzyskać dodatkowe informacje.

| Komunikat o błędzie / scenariusz | Prawdopodobna przyczyna | Zalecana akcja |
| ------------------------ | -------------- | ------------------ |
| Dane wyjściowe pliku exe: *wyjątek nawiązywania połączenia z docelowym* |Skrypt nie jest w stanie uzyskać dostępu do punktu odzyskiwania    | Sprawdź, czy komputer spełnia poprzednie wymagania dostępu. |  
| Dane wyjściowe pliku exe: *element docelowy został już zalogowany za pośrednictwem sesji iSCSI.* | Skrypt została już wykonana na tym samym komputerze, a dyski zostały dołączone | Zostały już dołączone woluminy punktu odzyskiwania. Mogą one nie można zainstalować przy użyciu tych samych liter dysków w pierwotnej maszyny wirtualnej. Przeglądaj dostępne woluminy w Eksploratorze plików w pliku |
| Dane wyjściowe pliku exe: *ten skrypt jest nieprawidłowy, ponieważ dyski mają został odinstalowany za pośrednictwem portalu/przekracza 12-godzinnego limitu. Pobierz nowy skrypt z witryny portal.* |    Dyski mają został odinstalowany z portalu lub przekroczono limit 12 godz. | Tego konkretnego pliku exe teraz jest nieprawidłowy i nie można go uruchomić. Jeśli chcesz uzyskać dostęp do plików w tym odzyskiwania punktu w czasie, odwiedź portal dla nowego pliku exe|
| Na komputerze, na którym jest uruchamiane plik exe: nowe woluminy nie są odinstalowane, po kliknięciu przycisku odinstalowywanie | Inicjator iSCSI na maszynie nie odpowiada/odświeżania połączenia z docelowym i nie obsługi pamięci podręcznej. |  Po kliknięciu przycisku **odinstalować**, poczekaj kilka minut. Nowe woluminy nie są odinstalowane, przejrzyj wszystkie woluminy. Przeglądanie wszystkich woluminów wymusza inicjatora można odświeżyć połączenia, a ten wolumin został odinstalowany z komunikatem o błędzie, że dysk nie jest dostępna.|
| Dane wyjściowe pliku exe: skrypt jest uruchamiany pomyślnie, ale "Nowe woluminy dołączony" nie jest wyświetlany w danych wyjściowych skryptu |    Jest to błąd przejściowy    | Woluminy będą zostały już dołączone. Otwórz Eksploratora, aby przeglądać. Jeśli do uruchamiania skryptów za każdym razem, gdy używane są tym samym komputerze, należy rozważyć ponowne uruchomienie komputera i lista powinna być wyświetlana w uruchamia kolejne exe. |
| Określonych w systemie Linux: nie można wyświetlić żądanego woluminy | Maszyny, na którym skrypt jest uruchamiany system operacyjny nie może rozpoznać bazowego systemu plików chronionych maszyn wirtualnych | Sprawdź, czy punkt odzyskiwania jest awarii zgodne lub spójna na poziomie plików. Jeśli plik spójne, uruchom skrypt na innej maszyny którego OS rozpoznaje chronionej maszyny Wirtualnej w systemie plików |
| Windows określonego: nie można wyświetlić żądanego woluminy | Dyski zostały dołączone, ale nie skonfigurowano woluminów | Na ekranie zarządzania dyskami zidentyfikować dodatkowe dyski powiązane z punktem odzyskiwania. Jeśli dowolny z tych dysków znajdują się w trybie offline stan spróbuj, dzięki czemu ich w trybie online, klikając prawym przyciskiem myszy na dysku, a następnie kliknij przycisk "Online"|

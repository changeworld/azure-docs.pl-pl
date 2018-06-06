---
title: 'Kopia zapasowa Azure: Odzyskiwanie plików i folderów z kopii zapasowej maszyny Wirtualnej Azure'
description: Odzyskiwanie plików z punktu odzyskiwania maszyny wirtualnej platformy Azure
services: backup
author: pvrk
manager: shivamg
keywords: odzyskiwanie na poziomie elementu; odzyskiwanie plików z kopii zapasowej maszyny Wirtualnej platformy Azure. Przywróć pliki z maszyny Wirtualnej Azure
ms.service: backup
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: pullabhk
ms.openlocfilehash: e9d1c79d6485764f4b671ad529e0812b49df34b1
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737022"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure

Kopia zapasowa Azure udostępnia możliwość przywracania [maszyn wirtualnych platformy Azure (maszyny wirtualne) oraz dyski](./backup-azure-arm-restore-vms.md) z kopii zapasowych maszyny Wirtualnej platformy Azure, znanej także jako punkty odzyskiwania. W tym artykule opisano sposób odzyskiwanie plików i folderów z kopii zapasowej maszyny Wirtualnej platformy Azure. Przywracanie plików i folderów jest dostępna tylko dla maszyn wirtualnych platformy Azure wdrażane za pomocą modelu usługi Resource Manager i chronione w magazynie usług odzyskiwania.

> [!Note]
> Ta funkcja jest dostępna dla maszyn wirtualnych platformy Azure wdrażane za pomocą modelu usługi Resource Manager i chronione w magazynie usług odzyskiwania.
> Odzyskiwanie plików z zaszyfrowanej kopii zapasowej maszyny Wirtualnej nie jest obsługiwane.
>

## <a name="mount-the-volume-and-copy-files"></a>Zainstaluj woluminu i kopiować pliki

Aby przywrócić pliki lub foldery z punktu odzyskiwania, przejdź do maszyny wirtualnej, a następnie wybierz pozycję żądanego punktu odzyskiwania. 

1. Zaloguj się do [portalu Azure](http://portal.Azure.com) i w menu po lewej stronie kliknij **maszyn wirtualnych**. Wybierz maszynę wirtualną, aby otworzyć pulpit nawigacyjny tej maszyny wirtualnej z listy maszyn wirtualnych. 

2. W menu maszyny wirtualnej, kliknij przycisk **kopii zapasowej** aby otworzyć pulpit nawigacyjny kopii zapasowej.

    ![Otwórz element kopii zapasowej magazynu usług odzyskiwania](./media/backup-azure-restore-files-from-vm/open-vault-from-vm.png)

3. W menu nawigacyjnym kopii zapasowej, kliknij **odzyskiwanie plików** aby otworzyć menu.

    ![Menu Plik odzyskiwania](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Z **wybierz punkt odzyskiwania** menu rozwijanego wybierz punkt odzyskiwania, który zawiera pliki. Domyślnie już jest wybrany najnowszy punkt odzyskiwania.

5. Aby pobrać oprogramowanie używany do kopiowania plików z punktu odzyskiwania, kliknij przycisk **Pobierz plik wykonywalny** (dla systemu Windows Azure maszyny Wirtualnej) lub **Pobierz skrypt** (dla systemu Linux maszyny Wirtualnej platformy Azure). 

    ![Wygenerowane hasło](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure pobiera plik wykonywalny lub skrypt na komputerze lokalnym.

    ![Pobierz komunikatu dla tego pliku wykonywalnego lub skryptu](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Zalecane jest, aby uruchomić plik wykonywalny lub skrypt jako administrator, zapisać pobierania na tym komputerze.

6. Plik wykonywalny lub skrypt jest chroniona hasłem i wymaga hasła. W **odzyskiwanie plików** menu, kliknij przycisk Kopiuj, aby załadować hasło do pamięci.

    ![Wygenerowane hasło](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Z lokalizacji pobierania (zwykle w folderze pobrane) kliknij prawym przyciskiem myszy plik wykonywalny lub skrypt i uruchom go przy użyciu poświadczeń administratora. Po wyświetleniu monitu wpisz hasło lub wkleić hasło z pamięci, a następnie naciśnij klawisz Enter. Po wpisaniu jest prawidłowe hasło, skrypt nawiązuje połączenie z punktem odzyskiwania.

    ![Menu Plik odzyskiwania](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Jeśli skrypt zostanie uruchomiony na komputerze z ograniczonym dostępem, upewnij się, Brak dostępu do:

    - download.microsoft.com
    - [Punkty końcowe systemu Azure, używany do tworzenia kopii zapasowych maszyny Wirtualnej Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity)
    - wychodzące port 3260

    Dla systemu Linux skrypt wymaga składników "open-iscsi" i "lshw" połączyć się z punktem odzyskiwania. Jeśli składniki nie istnieją na komputerze, na którym skrypt jest uruchamiany, skrypt z pytaniem o zgodę na instalację składników. Podaj zgody Aby zainstalować wymagane składniki.
    
    Dostęp do witrynie download.microsoft.com jest wymagany do pobrania składniki używane do tworzenia bezpiecznego kanału między komputerem, w którym skrypt jest uruchamiany i danych w punkcie odzyskiwania.         

    Skrypt można uruchomić na dowolnym komputerze, systemu operacyjnego tego samego (lub zgodne) jako kopii zapasowej maszyny Wirtualnej. Zobacz [tabeli zgodny system operacyjny](backup-azure-restore-files-from-vm.md#system-requirements) dla zgodnych systemów operacyjnych. Jeśli chronionej maszyny wirtualnej Azure korzysta z miejsca do magazynowania systemu Windows (w przypadku maszyn wirtualnych systemu Windows Azure) lub tablic LVM/RAID (dla maszyn wirtualnych systemu Linux), nie można uruchomić plik wykonywalny lub skrypt na tej samej maszyny wirtualnej. Zamiast tego należy uruchomić plik wykonywalny lub skrypt na innym komputerze z zgodny system operacyjny.
 

### <a name="identifying-volumes"></a>Identyfikowanie woluminów

#### <a name="for-windows"></a>Dla systemu Windows

Podczas uruchamiania pliku wykonywalnego systemu operacyjnego instaluje nowe woluminy i przypisuje litery dysku. Eksplorator Windows lub w Eksploratorze plików można użyć do przeglądania tych dysków. Przypisane do woluminów litery dysków może nie być tych samych liter, co oryginalna maszyna wirtualna, jednak jest zachowywana nazwa woluminu. Na przykład, jeśli został woluminu na maszynie wirtualnej oryginalnego "dysk danych (E:`\`)", że wolumin może zostać dołączony na komputerze lokalnym jako "dysk danych (dowolnej litery:`\`). Przeglądaj wszystkie woluminy wymienionych w danych wyjściowych skryptu do momentu znalezienia plików/folderów.  
       
   ![Menu Plik odzyskiwania](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>For Linux

W systemie Linux woluminy punktu odzyskiwania są instalowane w folderze, w którym skrypt jest uruchamiany. W związku z tym są wyświetlane dołączonych dysków, woluminów i odpowiadające im ścieżki instalacji. Zainstaluj tych ścieżek są widoczne dla użytkowników mających dostęp na poziomie głównym. Przeglądaj woluminów wymienionych w danych wyjściowych skryptu.

  ![Menu odzyskiwania plików systemu Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Zamykanie połączenia

Po zidentyfikowaniu pliki i skopiować je do lokalizacji magazynu lokalnego, usunąć lub odinstalować dodatkowych dysków. Aby odinstalować dyski, na **odzyskiwanie plików** menu w portalu Azure kliknij **odinstalować dyski**.

![Odinstaluj dysków](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Gdy dyski zostały odinstalowane, pojawi się komunikat informujący o pomyślnym zakończeniu go. Może upłynąć kilka minut, aż połączenie w celu odświeżenia, aby usunąć dyski.

W systemie Linux, po jest Przerwano połączenie z punktem odzyskiwania, system operacyjny nie powoduje usunięcia odpowiednich ścieżkach instalacji automatycznie. Ścieżki instalacji istnieje jako woluminy "operacji" i są widoczne, ale zgłosić błąd, gdy użytkownik dostępu do zapisu plików. Można je ręcznie usunąć. Skrypt uruchamiania, identyfikuje wszelkie istniejące z poprzednich punktów odzyskiwania woluminy i czyści po zgody.

## <a name="special-configurations"></a>Konfiguracje specjalne

### <a name="dynamic-disks"></a>Dyski dynamiczne

Jeśli chronione maszyny Wirtualnej Azure ma woluminy jedną lub obie następujące właściwości, nie można uruchomić pliku wykonywalnego skryptu na tej samej maszyny Wirtualnej. 

  - Woluminy, które obejmują wiele dysków (łączone i woluminów rozłożonych)
  - Odpornej na uszkodzenia (woluminy dublowane i RAID-5) na dyskach dynamicznych 

Zamiast tego należy uruchomić pliku wykonywalnego skryptów na dowolnym innym komputerze z zgodny system operacyjny.

### <a name="windows-storage-spaces"></a>Miejsca do magazynowania systemu Windows

Miejsca do magazynowania systemu Windows to technologia systemu Windows, która pozwala na wirtualizację magazynu. Funkcja miejsca do magazynowania systemu Windows można grupować standardowych dysków w pule magazynu. Dostępne miejsce w tych pul magazynów użyć do tworzenia dysków wirtualnych, nazywanych miejscami do magazynowania.

Jeśli chronione maszyny Wirtualnej Azure korzysta z funkcji miejsca do magazynowania systemu Windows, nie można uruchomić pliku wykonywalnego skryptu na tej samej maszyny Wirtualnej. Zamiast tego należy uruchomić pliku wykonywalnego skryptu na innym komputerze z zgodny system operacyjny.

### <a name="lvmraid-arrays"></a>Tablice LVM/RAID

W systemie Linux Menedżer woluminów logicznych (LVM) i/lub oprogramowania macierze RAID są używane do zarządzania przez wiele dysków logicznych woluminów. Jeśli chronione maszyny Wirtualnej systemu Linux używa LVM i/lub macierzy RAID, nie można uruchomić skrypt na tej samej maszyny Wirtualnej. Zamiast tego uruchomić skrypt na innym komputerze z zgodny system operacyjny i który obsługuje system plików chronionej maszyny wirtualnej.

Następujące dane wyjściowe skryptu wyświetla dyski LVM i/lub macierzy RAID i woluminów o typie partycji.

   ![Dane wyjściowe LVM Linux menu](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
Aby przenieść te partycje w trybie online, Uruchom polecenia w poniższych sekcjach. 

**W przypadku partycji LVM**

Aby wyświetlić listę nazw woluminów grupy w woluminie fizycznym.
```
$ pvs <volume name as shown above in the script output> 
```
Aby wyświetlić listę wszystkich woluminów logicznych, nazwy i ich ścieżek w grupie woluminu.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```

Aby zainstalować logicznej woluminów na ścieżkę wybranych przez użytkownika.

```
$ mount <LV path> </mountpath>
```



**Dla macierzy RAID**

Polecenie wyświetla szczegółowe informacje o wszystkich dysków raid.

```
$ mdadm –detail –scan
```
 Odpowiedni dysk RAID jest wyświetlany jako `/dev/mdm/<RAID array name in the protected VM>`

Jeśli na dysku RAID woluminy fizyczne za pomocą polecenia instalacji.
```
$ mount [RAID Disk Path] [/mountpath]
```

Jeśli dysk RAID inny LVM skonfigurowane w nim, przy użyciu powyższej procedury dla partycji LVM ale użyj nazwy woluminu zamiast nazwy dysku RAID

## <a name="system-requirements"></a>Wymagania systemowe

### <a name="for-windows"></a>Dla systemu Windows

W poniższej tabeli przedstawiono zgodność między systemami operacyjnymi serwera i komputera. Podczas odzyskiwania plików, nie można przywrócić pliki z wersją systemu operacyjnego poprzedniego lub przyszłe. Na przykład nie można przywrócić pliku z maszyny Wirtualnej systemu Windows Server 2016 system Windows Server 2012 lub na komputerze z systemem Windows 8. Ten sam system operacyjny serwera lub klienta zgodny system operacyjny, można przywrócić pliki z maszyny Wirtualnej.   

|System operacyjny serwera | Zgodny system operacyjny klienta  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux"></a>For Linux

W systemie Linux system operacyjny komputera służące do przywrócenia plików musi obsługiwać system plików chronionej maszyny wirtualnej. Po wybraniu komputera, aby uruchomić skrypt, upewnij się, komputer ma zgodny system operacyjny i korzysta z jednego z wersji określonej w poniższej tabeli:

|System operacyjny Linux | Wersje  |
| --------------- | ---- |
| Ubuntu | 12.04 i powyżej. |
| CentOS | 6.5 i powyżej.  |
| RHEL | 6.7 i powyżej. |
| Debian | 7 i nowsze |
| Oracle Linux | 6.4 i powyżej. |
| SLES | 12 lub nowszym |
| openSUSE | 42.2 i powyżej. |

Skrypt wymaga również Python i bash składników do wykonywania i bezpieczne łączenie z punktu odzyskiwania.

|Składnik | Wersja  |
| --------------- | ---- |
| Bash | 4 i nowsze |
| python | 2.6.6 i powyżej.  |
| Protokół TLS | 1.2 powinny być obsługiwane.  |

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy podczas przywracania plików z maszyn wirtualnych Sprawdź poniższej tabeli, aby uzyskać dodatkowe informacje.

| Komunikat o błędzie / scenariusza | Prawdopodobna przyczyna | Zalecana akcja |
| ------------------------ | -------------- | ------------------ |
| Dane wyjściowe exe: *wyjątek łączenie się z obiektem docelowym* |Skrypt nie będzie mógł uzyskać dostępu do punktu odzyskiwania | Sprawdź, czy komputer spełnia wymienionych powyżej wymagań dostępu. |  
|   Dane wyjściowe exe: *element docelowy ma już zalogowany za pośrednictwem sesji ISCSI.* | Skrypt zostało już wykonane na tym samym komputerze, a dyski zostały dołączone | Już zostały dołączone woluminy punktu odzyskiwania. Mogą one nie można zainstalować z tych samych liter dysków oryginalnego maszyny wirtualnej. Przeglądaj wszystkie dostępne woluminy w Eksploratorze plików dla pliku |
| Dane wyjściowe exe: *tego skryptu jest nieprawidłowa, ponieważ dyski mają został odinstalowany przez 12 hr portal/przekroczył limit. Pobierz nowy skrypt z portalu.* | Dyski mają został odinstalowany z portalu lub przekroczono limit 12 hr |    Tego konkretnego pliku exe teraz jest nieprawidłowy i nie można uruchomić. Jeśli chcesz uzyskać dostęp do plików tego odzyskiwania w momencie, odwiedź portal dla nowego pliku exe|
| Na komputerze, na którym uruchomiono pliku exe: nowe woluminy nie są odinstalowany po kliknięciu przycisku dezinstalacji |    Inicjator ISCSI na tym komputerze nie jest odpowiada/odświeżyć połączenie z docelowym i obsługi pamięci podręcznej |    Poczekaj kilka minut po naciśnięciu przycisku dezinstalacji. Jeśli nowe woluminy nadal są nie odinstalowana, przejdź do wszystkich woluminów. To zmusza inicjatora, aby odświeżyć połączenie i ten wolumin został odinstalowany z komunikatem o błędzie, że dysk nie jest dostępna|
| Dane wyjściowe exe: skrypt jest uruchamiany pomyślnie, ale "Nowe woluminy dołączony" nie jest wyświetlany w danych wyjściowych skryptu | Jest to przejściowy błąd   | Woluminy czy zostały już dołączone. Otwórz Eksploratora do przeglądania. Jeśli używasz tej samej maszyny do uruchamiania skryptów zawsze, rozważ ponowne uruchomienie komputera i listy powinien być wyświetlany w kolejnych exe uruchamia. |
| Właściwe dla systemu Linux: nie można wyświetlić żądanego woluminów | System operacyjny na komputerze, na którym skrypt jest uruchamiany nie może rozpoznać źródłowy system plików chronionej maszyny wirtualnej | Sprawdź, czy punkt odzyskiwania awaryjnego zgodne lub zgodne z plikami. Jeśli plik spójne, uruchom skrypt na innym komputerze których system operacyjny rozpoznaje plików chronionych maszyn wirtualnych |
| Właściwe dla systemu Windows: nie można wyświetlić żądanego woluminów | Dyski zostały dołączone, ale nie skonfigurowano woluminów | Na ekranie zarządzania dysku określenie dodatkowych dysków, powiązane z punktem odzyskiwania. Jeśli którykolwiek z tych dysków znajduje się w trybie offline stan spróbuj je w trybie online klikając prawym przyciskiem myszy na dysku i kliknij przycisk "Online"|

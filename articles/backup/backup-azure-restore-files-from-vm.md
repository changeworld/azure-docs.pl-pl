---
title: Odzyskiwanie plików i folderów z kopii zapasowej maszyny Wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak odzyskać pliki i foldery z punktu odzyskiwania maszyny wirtualnej platformy Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273308"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure

Usługa Azure Backup umożliwia przywracanie [maszyn wirtualnych platformy Azure (maszyn wirtualnych) i dysków](./backup-azure-arm-restore-vms.md) z kopii zapasowych maszyn wirtualnych platformy Azure, nazywanych również punktami odzyskiwania. W tym artykule wyjaśniono, jak odzyskać pliki i foldery z kopii zapasowej maszyny Wirtualnej platformy Azure. Przywracanie plików i folderów jest dostępne tylko dla maszyn wirtualnych platformy Azure wdrożonych przy użyciu modelu Menedżera zasobów i chronionych w magazynie usług odzyskiwania.

> [!NOTE]
> Ta funkcja jest dostępna dla maszyn wirtualnych platformy Azure wdrożonych przy użyciu modelu Menedżera zasobów i chronionych w magazynie usług odzyskiwania.
> Odzyskiwanie plików z zaszyfrowanej kopii zapasowej maszyny Wirtualnej nie jest obsługiwane.
>

## <a name="mount-the-volume-and-copy-files"></a>Montowanie woluminów i kopiowania plików

Aby przywrócić pliki lub foldery z punktu odzyskiwania, przejdź do maszyny wirtualnej i wybierz żądany punkt odzyskiwania.

1. Zaloguj się do [witryny Azure portal](https://portal.Azure.com) i w lewym okienku kliknij pozycję **Maszyny wirtualne**. Z listy maszyn wirtualnych wybierz maszynę wirtualną, aby otworzyć pulpit nawigacyjny tej maszyny wirtualnej.

2. W menu maszyny wirtualnej kliknij polecenie **Kopia zapasowa,** aby otworzyć pulpit nawigacyjny kopii zapasowej.

    ![Otwórz element kopii zapasowej magazynu usług odzyskiwania](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. W menu pulpitu nawigacyjnego kopia zapasowa kliknij polecenie **Odzyskiwanie plików**.

    ![Przycisk Odzyskiwanie plików](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Zostanie otwarte menu **Odzyskiwanie plików.**

    ![Menu odzyskiwania plików](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Z menu rozwijanego **Wybierz punkt odzyskiwania** wybierz punkt odzyskiwania zawierający żądane pliki. Domyślnie ostatni punkt odzyskiwania jest już zaznaczony.

5. Aby pobrać oprogramowanie używane do kopiowania plików ze punktu odzyskiwania, kliknij przycisk **Pobierz plik wykonywalny** (dla maszyn wirtualnych z systemem Windows Azure) lub **Download Script** (dla maszyn wirtualnych platformy Linux Azure generowany jest skrypt języka Python).

    ![Wygenerowane hasło](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Platforma Azure pobiera plik wykonywalny lub skrypt na komputer lokalny.

    ![pobierz wiadomość dla pliku wykonywalnego lub skryptu](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Aby uruchomić plik wykonywalny lub skrypt jako administrator, zaleca się zapisanie pobranego pliku na komputerze.

6. Plik wykonywalny lub skrypt jest chroniony hasłem i wymaga hasła. W menu **Odzyskiwanie plików** kliknij przycisk kopiowania, aby załadować hasło do pamięci.

    ![Wygenerowane hasło](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Z lokalizacji pobierania (zwykle folderu Pobrane) kliknij prawym przyciskiem myszy plik wykonywalny lub skrypt i uruchom go z poświadczeniami administratora. Po wyświetleniu monitu wpisz hasło lub wklej hasło z pamięci, a następnie naciśnij **klawisz Enter**. Po wprowadzeniu prawidłowego hasła skrypt łączy się z punktem odzyskiwania.

    ![Menu odzyskiwania plików](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. W przypadku maszyn z systemem Linux generowany jest skrypt języka Python. Trzeba pobrać skrypt i skopiować go na odpowiedni / kompatybilny serwer Linux. Może być konieczne zmodyfikowanie uprawnień ```chmod +x <python file name>```do wykonania go za pomocą pliku . Następnie uruchom plik ```./<python file name>```pythona za pomocą pliku .

Zapoznaj się z sekcją [Wymagania programu Access,](#access-requirements) aby upewnić się, że skrypt został pomyślnie uruchomiony.

### <a name="identifying-volumes"></a>Identyfikowanie woluminów

#### <a name="for-windows"></a>W przypadku systemu Windows

Po uruchomieniu pliku wykonywalnego system operacyjny montuje nowe woluminy i przypisuje litery dysków. Do przeglądania tych dysków można użyć Eksploratora Windows lub Eksploratora plików. Litery dysków przypisane do woluminów mogą nie być tymi samymi literami co oryginalna maszyna wirtualna. Jednak nazwa woluminu jest zachowywana. Na przykład, jeśli wolumin na oryginalnej maszynie wirtualnej to "Dysk danych (E:`\`)", wolumin ten może`\`być dołączony na komputerze lokalnym jako "Dysk danych ("Każda litera": ). Przeglądaj wszystkie woluminy wymienione w danych wyjściowych skryptu, aż znajdziesz pliki lub folder.  

   ![Menu odzyskiwania plików](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>W przypadku systemu Linux

W systemie Linux woluminy punktu odzyskiwania są instalowane w folderze, w którym skrypt jest uruchamiany. Dołączone dyski, woluminy i odpowiednie ścieżki instalacji są odpowiednio wyświetlane. Te ścieżki instalacji są widoczne dla użytkowników mających dostęp na poziomie katalogu głównego. Przejrzyj woluminy wymienione w danych wyjściowych skryptu.

  ![Menu odzyskiwania plików linuksa](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Zamykanie połączenia

Po zidentyfikowaniu plików i skopiowaniu ich do lokalnej lokalizacji magazynu usuń (lub odinstaluj) dodatkowe dyski. Aby odinstalować dyski, w menu **Odzyskiwanie plików** w witrynie Azure portal kliknij polecenie **Odinstaluj dyski**.

![Odmontowywki dysków](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Po odinstalowyniu dysków zostanie wyświetlony komunikat. Odświeżenie połączenia może potrwać kilka minut, aby można było usunąć dyski.

W systemie Linux po zerwaniu połączenia z punktem odzyskiwania system operacyjny nie usuwa automatycznie odpowiednich ścieżek instalacji. Ścieżki instalacji istnieją jako woluminy "sieroce" i są widoczne, ale zgłaszaj błąd podczas uzyskiwania dostępu/zapisywania plików. Można je usunąć ręcznie. Skrypt, po uruchomieniu, identyfikuje wszelkie takie woluminy istniejące z poprzednich punktów odzyskiwania i czyści je za zgodą.

## <a name="special-configurations"></a>Konfiguracje specjalne

### <a name="dynamic-disks"></a>Dyski dynamiczne

Jeśli chroniona maszyna wirtualna platformy Azure ma woluminy z jedną lub obie z następujących cech, nie można uruchomić skryptu wykonywalnego na tej samej maszynie wirtualnej.

- Woluminy obejmujące wiele dysków (woluminy łączone i rozłożone)
- Woluminy odporne na uszkodzenia (woluminy dublowane i RAID-5) na dyskach dynamicznych

Zamiast tego uruchom skrypt wykonywalny na dowolnym innym komputerze ze zgodnym systemem operacyjnym.

### <a name="windows-storage-spaces"></a>Miejsca do magazynowania w systemie Windows

Miejsca do magazynowania systemu Windows to technologia systemu Windows umożliwiająca wirtualizację magazynu. W systemie Windows Storage Spaces można grupować dyski standardowe w pulach magazynowych. Następnie można użyć dostępnego miejsca w tych pulach magazynu do tworzenia dysków wirtualnych, nazywanych miejscami do magazynowania.

Jeśli chroniona maszyna wirtualna platformy Azure używa miejsca do magazynowania systemu Windows, nie można uruchomić skryptu wykonywalnego na tej samej maszynie wirtualnej. Zamiast tego uruchom skrypt wykonywalny na dowolnym innym komputerze ze zgodnym systemem operacyjnym.

### <a name="lvmraid-arrays"></a>Macierze LVM/RAID

W systemie Linux menedżer woluminów logicznych (LVM) i/lub oprogramowanie macierze RAID są używane do zarządzania woluminami logicznymi na wielu dyskach. Jeśli chroniona maszyna wirtualna z systemem Linux używa macierzy LVM i/lub RAID, nie można uruchomić skryptu na tej samej maszynie wirtualnej. Zamiast tego uruchom skrypt na dowolnym innym komputerze z kompatybilnym systemem operacyjnym i który obsługuje system plików chronionej maszyny Wirtualnej.

Następujące dane wyjściowe skryptu są wyświetlane na dyskach LVM i/lub MAcierzy RAID oraz woluminach o typie partycji.

   ![Linux LVM Wyjście menu](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Aby połączyć te partycje w tryb online, uruchom polecenia w poniższych sekcjach.

#### <a name="for-lvm-partitions"></a>Dla partycji LVM

Aby wyświetlić nazwy grup woluminów pod woluminem fizycznym:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Aby wyświetlić listę wszystkich woluminów logicznych, nazw i ich ścieżek w grupie woluminów:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

Aby zainstalować woluminy logiczne na wybranej ścieżce:

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Dla macierzy RAID

Następujące polecenie wyświetla szczegółowe informacje o wszystkich dyskach raid:

```bash
#!/bin/bash
mdadm –detail –scan
```

 Odpowiedni dysk RAID jest wyświetlany jako`/dev/mdm/<RAID array name in the protected VM>`

Użyj polecenia mount, jeśli dysk RAID ma woluminy fizyczne:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Jeśli na dysku RAID skonfigurowano inny lvm, użyj poprzedniej procedury dla partycji LVM, ale użyj nazwy woluminu zamiast nazwy dysku RAID.

## <a name="system-requirements"></a>Wymagania systemowe

### <a name="for-windows-os"></a>Dla systemu operacyjnego Windows

W poniższej tabeli przedstawiono zgodność między serwerem a komputerem systemów operacyjnych. Podczas odzyskiwania plików nie można przywrócić plików do poprzedniej lub przyszłej wersji systemu operacyjnego. Na przykład nie można przywrócić pliku z maszyny wirtualnej systemu Windows Server 2016 do systemu Windows Server 2012 lub komputera z systemem Windows 8. Pliki z maszyny Wirtualnej można przywrócić do tego samego systemu operacyjnego serwera lub do zgodnego systemu operacyjnego klienta.

|System operacyjny serwera | Kompatybilny system operacyjny klienta  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Dla systemu operacyjnego Linux

W systemie Linux system operacyjny komputera używanego do przywracania plików musi obsługiwać system plików chronionej maszyny wirtualnej. Wybierając komputer do uruchomienia skryptu, upewnij się, że komputer ma zgodny system operacyjny i używa jednej z wersji określonych w poniższej tabeli:

|System operacyjny Linux | Wersje  |
| --------------- | ---- |
| Ubuntu | 12.04 i powyżej |
| CentOS | 6.5 i powyżej  |
| RHEL | 6.7 i powyżej |
| Debian | 7 i powyżej |
| Oracle Linux | 6.4 i powyżej |
| SLES | 12 i więcej |
| openSUSE | 42.2 i powyżej |

> [!NOTE]
> Znaleźliśmy pewne problemy z uruchomieniem skryptu odzyskiwania plików na komputerach z systemem operacyjnym SLES 12 SP4 i badamy z zespołem SLES.
> Obecnie uruchomienie skryptu odzyskiwania plików działa na komputerach z wersjami SLES 12 SP2 i SP3 OS.
>

Skrypt wymaga również składników Python i bash do wykonania i bezpiecznego połączenia z punktem odzyskiwania.

|Składnik | Wersja  |
| --------------- | ---- |
| bash | 4 i powyżej |
| python | 2.6.6 i powyżej  |
| TLS | 1.2 należy wspierać  |

## <a name="access-requirements"></a>Wymagania dotyczące uzyskiwania dostępu

Jeśli skrypt zostanie uruchomiony na komputerze z ograniczonym dostępem, upewnij się, że istnieje dostęp do:

- `download.microsoft.com`
- Adresy URL usługi odzyskiwania (nazwa geograficzna odnosi się do regionu, w którym znajduje się magazyn usługi odzyskiwania)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com`(Dla publicznej platformy Azure geos)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`(Dla platformy Azure Chiny 21Vianet)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us`(Dla platformy Azure dla instytucji rządowych USA)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de`(Dla platformy Azure Niemcy)
- Porty wychodzące 53 (DNS), 443, 3260

> [!NOTE]
>
> - Nazwa pobranego pliku skryptu będzie miała **nazwę geograficzną** do wypełnienia w adresie URL. Dla exampple: Pobrana nazwa \'skryptu zaczyna\'\_\'się\'od\'nazwy\'geoname VMname _ GUID , jak *ContosoVM_wcus_12345678*
> - Adres URL <https://pod01-rec2.wcus.backup.windowsazure.com>będzie "
>

W przypadku systemu Linux skrypt wymaga składników "open-iscsi" i 'lshw' do podłączenia do punktu odzyskiwania. Jeśli składniki nie istnieją na komputerze, na którym skrypt jest uruchamiany, skrypt prosi o pozwolenie na zainstalowanie składników. Wyrazić zgodę na zainstalowanie niezbędnych składników.

Dostęp do `download.microsoft.com` jest wymagany do pobierania składników używanych do tworzenia bezpiecznego kanału między komputerem, na którym jest uruchamiany skrypt, a danymi w punkcie odzyskiwania.

Skrypt można uruchomić na dowolnym komputerze, który ma ten sam (lub zgodny) system operacyjny, co kopia zapasowa maszyny Wirtualnej. Zobacz [tabelę Kompatybilny system operacyjny, aby](backup-azure-restore-files-from-vm.md#system-requirements) uzyskać zgodność z systemami operacyjnymi. Jeśli chroniona maszyna wirtualna platformy Azure używa miejsc do magazynowania systemu Windows (dla maszyn wirtualnych z systemem Windows Azure) lub macierzy LVM/RAID (dla maszyn wirtualnych z systemem Linux), nie można uruchomić pliku wykonywalnego lub skryptu na tej samej maszynie wirtualnej. Zamiast tego uruchom plik wykonywalny lub skrypt na dowolnym innym komputerze ze zgodnym systemem operacyjnym.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Odzyskiwanie plików z kopii zapasowych maszyn wirtualnych z dużymi dyskami

W tej sekcji wyjaśniono, jak wykonać odzyskiwanie plików z kopii zapasowych maszyn wirtualnych platformy Azure z więcej niż 16 dysków i każdy rozmiar dysku jest większa niż 32 TB.

Ponieważ proces odzyskiwania plików dołącza wszystkie dyski z kopii zapasowej, gdy używana jest duża liczba dysków (>16) lub dużych dysków (> po 32 TB każdy), zalecane są następujące punkty akcji:

- Zachowaj oddzielny serwer przywracania (maszyny wirtualne Usługi Azure VM D2v3) do odzyskiwania plików. Można go używać tylko do odzyskiwania plików, a następnie zamknąć go, gdy nie jest to wymagane. Przywracanie na oryginalnym komputerze nie jest zalecane, ponieważ będzie miało znaczący wpływ na samą maszynę wirtualną.
- Następnie uruchom skrypt raz, aby sprawdzić, czy operacja odzyskiwania plików zakończy się pomyślnie.
- Jeśli proces odzyskiwania plików zawiesza się (dyski nigdy nie są zainstalowane lub są zainstalowane, ale woluminy nie są wyświetlane), wykonaj następujące czynności.
  - Jeśli serwer przywracania jest maszyną wirtualną systemu Windows:
    - Upewnij się, że system operacyjny jest WS 2012 lub wyższy.
    - Upewnij się, że klucze rejestru są ustawione zgodnie z poniższymi sugestiami na serwerze przywracania i upewnij się, że ponownie uruchomić serwer. Liczba obok identyfikatora GUID może wynosić od 0001-0005. W poniższym przykładzie jest to 0004. Przejdź przez ścieżkę klucza rejestru, aż do sekcji parametrów.

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Jeśli serwer przywracania jest maszyną wirtualną z systemem Linux:
  - W pliku /etc/iscsi/iscsid.conf zmień ustawienie z:
    - node.conn[0].timeo.noop_out_timeout = 5 do node.conn[0].timeo.noop_out_timeout = 30
- Po dokonaniu powyższej zmiany uruchom skrypt ponownie. Dzięki tym zmianom jest wysoce prawdopodobne, że odzyskiwanie plików zakończy się pomyślnie.
- Za każdym razem, gdy użytkownik pobiera skrypt, usługa Azure Backup inicjuje proces przygotowywania punktu odzyskiwania do pobrania. W przypadku dużych dysków ten proces zajmie dużo czasu. Jeśli występują kolejne wybuchy żądań, przygotowanie docelowe przejdzie do spirali pobierania. W związku z tym zaleca się pobrać skrypt z portalu/powershell/cli, czekać na 20-30 minut (heurystyczny), a następnie uruchomić go. W tym czasie obiekt docelowy ma być gotowy do połączenia ze skryptu.
- Po odzyskaniu pliku upewnij się, że wracasz do portalu i klikasz **odinstaluj dyski** dla punktów odzyskiwania, w których nie można było zainstalować woluminów. Zasadniczo ten krok oczyści wszystkie istniejące procesy/sesje i zwiększy szansę na wyleczenie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy podczas odzyskiwania plików z maszyn wirtualnych, sprawdź w poniższej tabeli, aby uzyskać dodatkowe informacje.

| Komunikat o błędzie / Scenariusz | Prawdopodobna przyczyna | Zalecana akcja |
| ------------------------ | -------------- | ------------------ |
| Wyjście EXE: *Wyjątek przechwycony podczas łączenia się z obiektem docelowym* | Skrypt nie może uzyskać dostępu do punktu odzyskiwania    | Sprawdź, czy urządzenie spełnia [poprzednie wymagania dostępu.](#access-requirements) |  
| Wyjście EXE: *obiekt docelowy został już zalogowany za pośrednictwem sesji iSCSI.* | Skrypt został już wykonany na tym samym komputerze, a dyski zostały dołączone | Woluminy punktu odzyskiwania zostały już dołączone. NIE mogą być montowane z tymi samymi literami dysków oryginalnej maszyny Wirtualnej. Przejrzyj wszystkie dostępne woluminy w Eksploratorze plików dla swojego pliku. |
| Dane wyjściowe exe: *Ten skrypt jest nieprawidłowy, ponieważ dyski zostały odinstalowane za pośrednictwem portalu/przekroczyły limit 12 godzin. Pobierz nowy skrypt z portalu.* |    Dyski zostały zdemontowane z portalu lub przekroczono limit 12 godzin | Ten konkretny exe jest teraz nieprawidłowy i nie można go uruchomić. Jeśli chcesz uzyskać dostęp do plików tego punktu odzyskiwania w czasie, odwiedź portal dla nowego exe.|
| Na komputerze, na którym działa exe: nowe woluminy nie są odinstalowane po kliknięciu przycisku odinstalowywalnika | Inicjator iSCSI na komputerze nie odpowiada/odświeża swoje połączenie z obiektem docelowym i utrzymuje pamięć podręczną. |  Po kliknięciu **przycisku Odłącz ,** odczekaj kilka minut. Jeśli nowe woluminy nie są odinstalowane, przejrzyj wszystkie woluminy. Przeglądanie wszystkich woluminów wymusza na inicjatorze odświeżenie połączenia, a wolumin jest odinstalowany z komunikatem o błędzie, że dysk nie jest dostępny.|
| Wyjście exe: Skrypt jest uruchamiany pomyślnie, ale "Nowe woluminy dołączone" nie jest wyświetlany na wyjściu skryptu |    Jest to błąd przejściowy    | Woluminy zostaną już dołączone. Otwórz Eksploratora, aby przeglądać. Jeśli używasz tego samego komputera do uruchamiania skryptów za każdym razem, należy rozważyć ponowne uruchomienie komputera i lista powinna być wyświetlana w kolejnych uruchomieniach exe. |
| Specyficzne dla Linuksa: Nie można wyświetlić żądanych woluminów | System operacyjny komputera, na którym jest uruchamiany skrypt, może nie rozpoznać podstawowego systemu plików chronionej maszyny Wirtualnej | Sprawdź, czy punkt odzyskiwania jest zgodny z awariami lub spójny z plikami. Jeśli plik jest spójny, uruchom skrypt na innym komputerze, którego system operacyjny rozpoznaje chroniony system plików maszyny Wirtualnej. |
| Specyficzne dla systemu Windows: nie można wyświetlić żądanych woluminów | Dyski mogły być dołączone, ale woluminy nie zostały skonfigurowane | Na ekranie zarządzania dyskami zidentyfikuj dodatkowe dyski związane z punktem odzyskiwania. Jeśli którykolwiek z tych dysków jest w trybie offline, spróbuj przewieźć je do trybu online, klikając prawym przyciskiem myszy na dysku i kliknij polecenie **Online**.|

## <a name="security"></a>Zabezpieczenia

W tej sekcji omówiono różne środki zabezpieczeń podjęte w celu wdrożenia odzyskiwania plików z kopii zapasowych maszyn wirtualnych platformy Azure.

### <a name="feature-flow"></a>Przepływ operacji

Ta funkcja została zbudowana w celu uzyskania dostępu do danych maszyny Wirtualnej bez konieczności przywracania całych dysków maszyn wirtualnych lub maszyn wirtualnych i przy minimalnej liczbie kroków. Dostęp do danych maszyny Wirtualnej jest dostarczany przez skrypt (który montuje wolumin odzyskiwania po uruchomieniu, jak pokazano poniżej) i stanowi podstawę wszystkich implementacji zabezpieczeń:

  ![Przepływ funkcji zabezpieczeń](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementacje zabezpieczeń

#### <a name="select-recovery-point-who-can-generate-script"></a>Wybierz punkt odzyskiwania (kto może generować skrypt)

Skrypt zapewnia dostęp do danych maszyny Wirtualnej, dlatego ważne jest, aby regulować, kto może je wygenerować w pierwszej kolejności. Musisz zalogować się do witryny Azure portal i być [RBAC autoryzowany](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) do generowania skryptu.

Odzyskiwanie plików wymaga tego samego poziomu autoryzacji, jaki jest wymagany dla przywracania maszyn wirtualnych i przywracania dysków. Innymi słowy tylko autoryzowani użytkownicy mogą wyświetlać dane maszyny Wirtualnej może generować skrypt.

Wygenerowany skrypt jest podpisany za pomocą oficjalnego certyfikatu firmy Microsoft dla usługi Azure Backup. Wszelkie manipulowanie skryptem oznacza, że podpis jest uszkodzony, a każda próba uruchomienia skryptu jest podświetlona jako potencjalne ryzyko przez system operacyjny.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Zamontuj wolumin odzyskiwania (kto może uruchamiać skrypt)

Tylko administrator może uruchomić skrypt i powinien działać w trybie podwyższonego poziomu. Skrypt uruchamia tylko wstępnie wygenerowany zestaw kroków i nie akceptuje danych wejściowych z dowolnego źródła zewnętrznego.

Aby uruchomić skrypt, wymagane jest hasło, które jest wyświetlane tylko autoryzowanego użytkownika w czasie generowania skryptu w witrynie Azure portal lub PowerShell/CLI. Ma to na celu zapewnienie, że autoryzowany użytkownik, który pobiera skrypt jest również odpowiedzialny za uruchomienie skryptu.

#### <a name="browse-files-and-folders"></a>Przeglądanie plików i folderów

Aby przeglądać pliki i foldery, skrypt używa inicjatora iSCSI na komputerze i łączy się z punktem odzyskiwania skonfigurowanym jako obiekt docelowy iSCSI. Tutaj można sobie wyobrazić scenariusze, w których próbuje się naśladować/naciągać albo/wszystkie składniki.

Używamy mechanizmu uwierzytelniania wzajemnego protokołu CHAP, aby każdy składnik uwierzytelnił drugi. Oznacza to, że jest to niezwykle trudne dla fałszywego inicjatora, aby połączyć się z celem iSCSI i fałszywy cel, aby być podłączony do komputera, na którym skrypt jest uruchamiany.

Przepływ danych między usługą odzyskiwania a komputerem jest chroniony przez tworzenie bezpiecznego tunelu TLS za pomocą protokołu TCP[(TLS 1.2 powinien być obsługiwany](#system-requirements) na komputerze, na którym skrypt jest uruchamiany).

Wszystkie listy kontroli dostępu do plików (ACL) znajdujące się w nadrzędnej/zapasowej maszynie wirtualnej są również zachowywane w zainstalowanym systemie plików.

Skrypt daje dostęp tylko do odczytu do punktu odzyskiwania i jest ważny tylko przez 12 godzin. Jeśli chcesz usunąć dostęp wcześniej, a następnie zaloguj się do usługi Azure Portal/PowerShell/CLI i wykonać **odinstalować dyski** dla tego określonego punktu odzyskiwania. Skrypt zostanie natychmiast unieważniony.

## <a name="next-steps"></a>Następne kroki

- W przypadku jakichkolwiek problemów podczas przywracania plików, zapoznaj się z sekcją [Rozwiązywanie problemów](#troubleshooting)
- Dowiedz się, jak [przywracać pliki za pośrednictwem programu Powershell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- Dowiedz się, jak [przywracać pliki za pośrednictwem interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- Po przywróceniu maszyny wirtualnej dowiedz się, jak [zarządzać kopiami zapasowymi](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)

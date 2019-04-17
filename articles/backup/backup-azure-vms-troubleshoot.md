---
title: Rozwiązywanie problemów z błędami kopii zapasowych w usłudze Azure virtual machines
description: Rozwiązywanie problemów z kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure
services: backup
author: srinathv
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: srinathv
ms.openlocfilehash: 6f10d8bc7f813245a66296988e4bb3792d898e08
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59618196"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Rozwiązywanie problemów z kopiami zapasowymi maszyn wirtualnych platformy Azure
Można rozwiązać, usuwać błędy napotkane podczas używania usługi Azure Backup, podając informacje przedstawione w poniższej tabeli:

## <a name="backup"></a>Backup

### <a name="copyingvhdsfrombackupvaulttakinglongtime--copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime-kopiowania danych kopii zapasowej z magazynu przekroczyło limit czasu

Kod błędu: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Komunikat o błędzie: Kopiowanie danych kopii zapasowej z magazynu przekroczyło limit czasu

Może się to zdarzyć z powodu błędów magazynu przejściowego lub brak wystarczającej ilości miejsca konta operacje We/Wy dla usługi kopii zapasowej na przesyłanie danych do magazynu przed upływem limitu czasu. Konfigurowanie kopii zapasowych maszyn wirtualnych przy użyciu tych [najlepsze praktyki](backup-azure-vms-introduction.md#best-practices) i spróbuj ponownie wykonać operację tworzenia kopii zapasowej.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState — maszyna wirtualna nie jest w stanie, który umożliwia tworzenie kopii zapasowych.

Kod błędu: UserErrorVmNotInDesirableState <br/>
Komunikat o błędzie: Maszyna wirtualna nie jest w stanie, który umożliwia tworzenie kopii zapasowych.<br/>

Wykonywanie kopii zapasowej nie powiodło się, ponieważ maszyna wirtualna jest w stanie niepowodzenia. Do pomyślnego utworzenia kopii zapasowej maszyny Wirtualnej stan powinna być uruchomiona, zatrzymana lub zatrzymana (cofnięty przydział).

* Jeśli maszyna wirtualna jest w stanie przejściowym między **systemem** i **Zamknij**, poczekaj, aż zmianę stanu. Następnie wyzwolić zadanie tworzenia kopii zapasowej.
*  Jeśli maszyna wirtualna jest maszyną Wirtualną systemu Linux i używa modułu jądra systemu Linux Security-Enhanced, ścieżka agenta systemu Linux platformy Azure wykluczania **/var/lib/waagent** z zasad zabezpieczeń i upewnij się, że zainstalowano rozszerzenie kopii zapasowej.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed — nie można zablokować przynajmniej jednego punktu instalacji maszyny wirtualnej, aby utworzyć migawkę spójną systemu plików

Kod błędu: UserErrorFsFreezeFailed <br/>
Komunikat o błędzie: Nie można zablokować przynajmniej jednego punktu instalacji maszyny wirtualnej, aby utworzyć migawkę spójną systemu plików.

* Sprawdź stan systemu plików wszystkich zainstalowanych urządzeń przy użyciu **tune2fs** polecenia, na przykład **tune2fs -l/dev/sdb1 \\** .\| grep **stan systemu plików**.
* Odinstaluj urządzenia, dla których stan systemu plików nie został wyczyszczony, za pomocą **umount** polecenia.
* Uruchom sprawdzanie spójności systemu plików na tych urządzeniach za pomocą **fsck** polecenia.
* Ponownie zainstaluj urządzeń i ponów próbę wykonania operacji tworzenia kopii zapasowej.</ol>

### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC — operacja instalacji rozszerzenia nie powiodła się ze względu na błąd modelu COM +

Kod błędu: ExtensionSnapshotFailedCOM <br/>
Komunikat o błędzie: Operacja migawki nie powiodła się z powodu błędu modelu COM +

Kod błędu: ExtensionInstallationFailedCOM  <br/>
Komunikat o błędzie: Operacja instalacji rozszerzenia nie powiodła się z powodu błędu modelu COM +

Kod błędu: Komunikat o błędzie ExtensionInstallationFailedMDTC: Nie można zainstalować rozszerzenia z powodu błędu "COM + nie może komunikować się z Microsoft Distributed Transaction Coordinator

Operacja tworzenia kopii zapasowej nie powiodło się z powodu problemu z usługą Windows **modelu COM + System** aplikacji.  Aby rozwiązać ten problem, wykonaj poniższe czynności:

* Ponowne uruchamianie/usługa Windows **COM + System Application** (z wiersza polecenia z podwyższonym **-net start COMSysApp**).
* Upewnij się, **Distributed Transaction Coordinator** services działa jako **Usługa sieciowa** konta. Jeśli nie, zmień ją na uruchamianie jako **Usługa sieciowa** konta, a następnie ponownie uruchom **aplikacja systemowa modelu COM +**.
* Jeśli nie można ponownie uruchomić usługę, zainstaluj ponownie **Distributed Transaction Coordinator** usługi, wykonując następujące czynności:
    * Zatrzymaj usługę MSDTC
    * Otwórz wiersz polecenia (cmd)
    * Uruchom polecenie "msdtc — Odinstaluj"
    * Uruchom polecenie "msdtc — instalowanie"
    * Uruchom usługę MSDTC
* Uruchom usługę Windows **aplikacja systemowa modelu COM +**. Po **aplikacja systemowa modelu COM +** jest uruchamiana, wyzwalanie zadania tworzenia kopii zapasowej w witrynie Azure portal.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState — operacja migawki nie powiodło się, ponieważ składniki zapisywania usługi VSS są w złym stanie

Kod błędu: ExtensionFailedVssWriterInBadState <br/>
Komunikat o błędzie: Operacja migawki nie powiodło się, ponieważ składniki zapisywania usługi VSS są w złym stanie.

Uruchom ponownie składniki zapisywania usługi VSS, które znajdują się w nieprawidłowym stanie. W wierszu polecenia z podwyższonym poziomem uprawnień uruchom ```vssadmin list writers```. Dane wyjściowe zawierają wszystkie składniki zapisywania usługi VSS i ich stan. Dla każdego składnika zapisywania usługi VSS, stan, który nie jest **[1] stabilny**, aby ponownie uruchomić składnik zapisywania usługi VSS, uruchom następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień:

  * ```net stop serviceName```
  * ```net start serviceName```

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure — nie można przeanalizować konfiguracji dla rozszerzenia kopii zapasowej

Kod błędu: ExtensionConfigParsingFailure<br/>
Komunikat o błędzie: Nie można przeanalizować konfiguracji dla rozszerzenia kopii zapasowej.

Ten błąd występuje z powodu zmiany uprawnień na **MachineKeys** katalogu: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Uruchom następujące polecenie i sprawdź, że uprawnienia **MachineKeys** katalogu są domyślne:**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Uprawnienia domyślne są następujące:
* Każdy z nich: (R, W)
* BUILTIN\Administratorzy: (F)

Jeśli widzisz uprawnień w **MachineKeys** katalogu są inne niż domyślne, wykonaj następujące kroki, popraw uprawnienia, usunąć certyfikat i wyzwalanie tworzenia kopii zapasowej:

1. Usuń uprawnienia na **MachineKeys** katalogu. Za pomocą właściwości zabezpieczeń Explorer oraz zaawansowane ustawienia zabezpieczeń w katalogu, resetować uprawnienia do wartości domyślnych. Usuń wszystkie obiekty użytkowników z wyjątkiem ustawień domyślnych z katalogu i upewnij się, że **wszyscy** uprawnienie dostępu specjalnego ma w następujący sposób:

    * Wyświetlanie zawartości folderu/Odczyt danych
    * Odczyt atrybutów
    * Odczyt atrybutów rozszerzonych
    * Tworzenie plików/Zapis danych
    * Tworzenie folderów/Dołączanie danych
    * Zapis atrybutów
    * Zapis atrybutów rozszerzonych
    * Uprawnienia do odczytu
2. Usunąć wszystkie certyfikaty, gdzie **wystawiony dla** jest klasycznego modelu wdrażania lub **Generator certyfikatów systemu Windows Azure CRP**:
    * [Otwórz przystawkę Certyfikaty na komputerze lokalnym konsoli](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * W obszarze **osobistych** > **certyfikaty**, usunąć wszystkie certyfikaty, gdzie **wystawiony dla** jest klasycznego modelu wdrażania lub **CRP zostaje Zainstalowana Windows Azure Generator certyfikatu**.
3. Wyzwalanie zadania tworzenia kopii zapasowej maszyny Wirtualnej.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState — stan rozszerzenia nie obsługuje operacji kopii zapasowej

Kod błędu: ExtensionStuckInDeletionState <br/>
Komunikat o błędzie: Stan rozszerzenia nie obsługuje operacji kopii zapasowej

Operacja tworzenia kopii zapasowej nie powiodło się z powodu niespójnym stanie zapasowy numer wewnętrzny. Aby rozwiązać ten problem, wykonaj poniższe czynności:

* Upewnij się, że agent gościa jest zainstalowany i odpowiada
* W witrynie Azure Portal przejdź do pozycji **Maszyna wirtualna** > **Wszystkie ustawienia** > **Rozszerzenia**
* Wybierz rozszerzenie kopii zapasowej VmSnapshot lub VmSnapshotLinux, a następnie kliknij pozycję **Odinstaluj**
* Po usunięciu rozszerzenia kopii zapasowej ponów próbę wykonania operacji tworzenia kopii zapasowej
* Kolejna operacja tworzenia kopii zapasowej spowoduje zainstalowanie nowego rozszerzenia w odpowiednim stanie

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>Przekroczono ExtensionFailedSnapshotLimitReachedError — migawki nie można wykonać operacji jako limit migawek dla niektórych dołączonych dysków

Kod błędu: ExtensionFailedSnapshotLimitReachedError  <br/>
Komunikat o błędzie: Operacja migawki nie powiodło się, jak limit migawek został przekroczony dla niektórych dołączonych dysków

Operacja migawki nie powiodło się, jak limit migawek został przekroczony dla niektórych dołączonych dysków. Wykonaj poniższe Rozwiązywanie problemów z kroków, a następnie spróbuj ponownie wykonać operację.

* Usuń obiekt blob migawki dysków, które nie są wymagane. Należy zachować ostrożność nie należy usuwać obiektów blob dysku, powinny zostać usunięte tylko migawki obiektów blob.
* Jeśli usuwania nietrwałego jest włączona na dysku maszyny Wirtualnej kont magazynu, należy skonfigurować przechowywania usuwania nietrwałego w taki sposób, że istniejące migawki są mniejsze niż maksymalna dozwolona w dowolnym momencie.
* Po włączeniu usługi Azure Site Recovery w kopii zapasowej maszyny Wirtualnej. następnie wykonaj poniższe:

    * Upewnij się, wartość **isanysnapshotfailed** jest ustawiony jako wartość false w /etc/azure/vmbackup.conf
    * Planowanie usługi Azure Site Recovery w innym czasie, w taki sposób, że nie powoduje konfliktu operacji tworzenia kopii zapasowej.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive — operacja migawki nie powiodło się ze względu na nieodpowiednie zasoby maszyny Wirtualnej.

Kod błędu: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Komunikat o błędzie: Operacja migawki nie powiodła się ze względu na nieodpowiednie zasoby maszyny Wirtualnej.

Operacja tworzenia kopii zapasowej na maszynie Wirtualnej nie powiodła z powodu opóźnienia wywołań sieci podczas wykonywania operacji migawki. Aby rozwiązać ten problem, wykonaj krok 1. Jeśli problem będzie się powtarzał, spróbuj wykonać kroki 2 i 3.

**Krok 1**: Tworzenie migawki za pośrednictwem hosta

W wierszu polecenia z podwyższonym poziomem uprawnień (administrator) uruchom poniższe polecenie:

```
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Dzięki temu migawki będą wykonywane za pośrednictwem hosta, a nie konta gościa. Ponów próbę wykonania operacji tworzenia kopii zapasowej.

**Krok 2**: Spróbuj zmienić harmonogram tworzenia kopii zapasowych do czasu, gdy maszyna wirtualna ma pod mniejszym obciążeniem (mniej itp procesora CPU/operacji We/Wy.)

**Krok 3**: Spróbuj [zwiększenie rozmiaru maszyny Wirtualnej](https://azure.microsoft.com/blog/resize-virtual-machines/) i spróbuj ponownie wykonać operację

### <a name="common-vm-backup-errors"></a>Typowe błędy tworzenia kopii zapasowej maszyny Wirtualnej

| Szczegóły błędu | Obejście |
| ------ | --- |
| Kod błędu: 320001<br/> Komunikat o błędzie: Nie można wykonać operacji, ponieważ maszyna wirtualna już nie istnieje. <br/> <br/> Kod błędu: 400094 <br/> Komunikat o błędzie: Maszyna wirtualna nie istnieje <br/> <br/>  Nie można znaleźć maszyny wirtualnej platformy Azure.  |Ten błąd występuje, gdy podstawowa maszyna wirtualna zostanie usunięta, ale zasady kopii zapasowych nadal wygląda dla maszyny Wirtualnej utworzyć kopię zapasową. Aby naprawić ten błąd, wykonaj następujące czynności: <ol><li> Ponownie utwórz maszynę wirtualną przy użyciu tej samej nazwie i tej samej nazwy grupy zasobów, **nazwa usługi w chmurze**,<br>**lub**</li><li> Zatrzymaj ochronę maszyny wirtualnej z lub bez usuwania danych kopii zapasowej. Aby uzyskać więcej informacji, zobacz [zatrzymanie ochrony maszyn wirtualnych](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| Maszyna wirtualna jest w stanie niepowodzenia inicjowania obsługi administracyjnej: <br>Ponowne uruchomienie maszyny Wirtualnej i upewnij się, że maszyna wirtualna jest uruchomiona lub zamknięta. | Ten błąd występuje, gdy jeden z błędami rozszerzeń umieszcza maszynę Wirtualną w stanie niepowodzenia inicjowania obsługi administracyjnej. Przejdź do listy rozszerzeń, należy sprawdzić, jeśli plik ma rozszerzenia nie powiodło się, usuń go i spróbuj ponownie uruchomić maszynę wirtualną. Wszystkie rozszerzenia są w stanie uruchomienia, sprawdź, czy Usługa agenta maszyny Wirtualnej jest uruchomiona. Jeśli nie, uruchom ponownie usługę agenta maszyny Wirtualnej. |
|Kod błędu: UserErrorBCMPremiumStorageQuotaError<br/> Komunikat o błędzie: Nie można skopiować migawki maszyny wirtualnej, z powodu braku wolnego miejsca na koncie magazynu | Dla maszyn wirtualnych usługi premium na stos kopii zapasowej maszyny Wirtualnej w wersji 1 Firma Microsoft kopiowania migawki do konta magazynu. Ten krok pozwala się upewnić, że liczba operacji We/Wy dostępne dla aplikacji przy użyciu dysków w warstwie premium nie są ograniczone ruch zarządzania kopiami zapasowymi, który działa w migawce. <br><br>Zaleca się przydzielanie tylko 50 procent 17.5 TB miejsca do magazynowania łączna liczba kont. Następnie usługa Azure Backup można skopiować migawki do magazynu konta i transfer danych z tej lokalizacji skopiowany do magazynu na koncie magazynu. |
| Nie można zainstalować rozszerzenia usługi Microsoft Recovery Services, ponieważ maszyna wirtualna nie jest uruchomiona. <br>Agent maszyny Wirtualnej jest wymaganiem wstępnym dla rozszerzenia usługi Azure Recovery Services. Zainstaluj agenta maszyny wirtualnej platformy Azure, a następnie ponownie uruchom operację rejestracji. |<ol> <li>Sprawdź, czy prawidłowo zainstalowano agenta maszyny Wirtualnej. <li>Upewnij się, że Flaga w konfiguracji maszyny Wirtualnej jest ustawiona poprawnie.</ol> Dowiedz się więcej na temat instalowania agenta maszyny Wirtualnej i sposób sprawdzania poprawności instalacji agenta maszyny Wirtualnej. |
| Operacja migawki nie powiodło się z powodu błędu operacji usługi kopiowania woluminów w tle (VSS) **ten dysk jest zablokowany przez szyfrowanie dysków funkcją BitLocker. Musisz odblokować ten dysk w Panelu sterowania.** |Wyłącz funkcję BitLocker dla wszystkich dysków na maszynie Wirtualnej i sprawdź, czy usługi VSS problem został rozwiązany. |
| Maszyna wirtualna znajduje się w stanie, który umożliwia tworzenie kopii zapasowych. |<ul><li>Jeśli maszyna wirtualna jest w stanie przejściowym między **systemem** i **Zamknij**, poczekaj, aż zmianę stanu. Następnie wyzwolić zadanie tworzenia kopii zapasowej. <li> Jeśli maszyna wirtualna jest maszyną Wirtualną systemu Linux i używa modułu jądra systemu Linux Security-Enhanced, ścieżka agenta systemu Linux platformy Azure wykluczania **/var/lib/waagent** z zasad zabezpieczeń i upewnij się, że zainstalowano rozszerzenie kopii zapasowej.  |
| Agent maszyny Wirtualnej nie jest obecny w maszynie wirtualnej: <br>Zainstaluj wszelkie składniki wymagane wstępnie i agenta maszyny Wirtualnej. Następnie uruchom ponownie wykonać operację. |Przeczytaj więcej na temat [instalacji agenta maszyny Wirtualnej i sposób sprawdzania poprawności instalacji agenta maszyny Wirtualnej](#vm-agent). |
| Kopia zapasowa nie można zablokować co najmniej jednego punktu instalacji maszyny wirtualnej, aby wykonać migawkę spójną z systemu plików. | Wykonaj następujące czynności: <ul><li>Sprawdź stan systemu plików wszystkich zainstalowanych urządzeń przy użyciu **"tune2fs"** polecenia. Na przykład **tune2fs -l/dev/sdb1 \\** .\| grep **stan systemu plików**. <li>Odinstaluj urządzenia, dla których stan systemu plików nie jest czysty przy użyciu **"umount"** polecenia. <li> Uruchom sprawdzanie spójności systemu plików na tych urządzeniach za pomocą **"fsck"** polecenia. <li> Zainstaluj ponownie urządzenia, a następnie spróbuj kopii zapasowej.</ol> |
| Operacja migawki nie powiodło się z powodu błędu tworzenia kanału bezpiecznej komunikacji sieciowej. | <ol><li> Otwórz Edytor rejestru, uruchamiając **regedit.exe** w trybie podniesionych uprawnień. <li> Zidentyfikuj wszystkie wersje programu .NET Framework jest obecny w systemie. Są one obecne w hierarchii klucza rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Dla każdej platformy .NET Framework w kluczu rejestru należy dodać następujący klucz: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| Operacja migawki nie powiodło się z powodu błędu, aby zainstalować pakiet redystrybucyjny Visual C++ dla Visual Studio 2012. | Przejdź do C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion i zainstaluj vcredist2012_x64.<br/>Upewnij się, że wartość klucza rejestru, który umożliwia instalację usługi jest ustawiony do poprawnej wartości. Oznacza to, że ustawia **Start** wartość w **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** do **3** i nie **4**. <br><br>Jeśli nadal masz problemy z instalacją, uruchom ponownie usługę instalacji uruchamiając **MSIEXEC/unregister** następuje **MSIEXEC /REGISTER** z wiersza polecenia z podwyższonym poziomem uprawnień.  |


## <a name="jobs"></a>Stanowiska

| Szczegóły błędu | Obejście |
| --- | --- |
| Unieważnieniu nie jest obsługiwana dla tego typu zadania: <br>Poczekaj na zakończenie zadania. |Brak |
| Zadanie nie jest w stanie cancelable: <br>Poczekaj na zakończenie zadania. <br>**lub**<br> Wybrane zadanie nie jest w stanie cancelable: <br>Poczekaj na zakończenie zadania. |Istnieje prawdopodobieństwo, że zadanie jest prawie gotowy. Poczekaj na zakończenie zadania.|
| Kopia zapasowa nie można anulować zadania, ponieważ nie jest w toku: <br>Anulowanie jest obsługiwana tylko w przypadku zadania w toku. Spróbuj anulować zadanie w toku. |Ten błąd występuje ze względu na stan przejściowy. Poczekaj chwilę i ponów próbę wykonania operacji anulowania. |
| Nie można anulować zadania kopii zapasowej: <br>Poczekaj na zakończenie zadania. |Brak |

## <a name="restore"></a>Przywracanie

| Szczegóły błędu | Obejście |
| --- | --- |
| Przywracanie nie powiodło się z powodu błędu wewnętrznego chmury. |<ol><li>Usługą w chmurze, do którego próbujesz przywrócić skonfigurowano ustawienia DNS. Możesz sprawdzić: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>Jeśli **adres** jest skonfigurowany, a następnie ustawienia DNS są skonfigurowane.<br> <li>Skonfigurowano usługę chmurową, do którego próbujesz przywrócić **zastrzeżonego adresu IP**, i istniejących maszyn wirtualnych w usłudze w chmurze znajdują się w stanie zatrzymania. Możesz sprawdzić, czy usługa w chmurze zarezerwował adresu IP za pomocą następujących poleceń cmdlet programu PowerShell: **$deployment = Get-AzureDeployment - ServiceName "servicename"-miejsca $ "Produkcja" w programie dep. Nazwa zastrzeżonego adresu IP**. <br><li>Próbujesz przywrócić maszynę wirtualną za pomocą następujących specjalnymi konfiguracjami sieci w tej samej usłudze w chmurze: <ul><li>Maszyny wirtualne w ramach konfiguracji usługi równoważenia obciążenia, wewnętrznych i zewnętrznych.<li>Maszyny wirtualne za pomocą wielu zastrzeżonych adresów IP. <li>Maszyny wirtualne z wieloma kartami sieciowymi. </ul><li>Wybierz nową usługę w chmurze w interfejsie użytkownika lub zobacz [przywrócić zagadnienia](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) dla maszyn wirtualnych ze specjalnymi konfiguracjami sieci.</ol> |
| Wybrana nazwa DNS jest już zajęta: <br>Określ inną nazwę DNS i spróbuj ponownie. |Tę nazwę DNS, który odwołuje się do nazwa usługi w chmurze, kończąc się zwykle **. cloudapp.net**. Ta nazwa musi być unikatowa. Jeśli ten błąd, należy wybrać inną nazwę maszyny Wirtualnej podczas przywracania. <br><br> Ten błąd jest wyświetlany tylko dla użytkowników w witrynie Azure Portal. Operacja przywracania za pomocą programu PowerShell zakończy się pomyślnie, ponieważ przywraca tylko dyski i nie tworzy maszynę Wirtualną. Ten błąd będzie się sterowaną maszyny Wirtualnej jest jawnie utworzone przez użytkownika, po operacji przywracania dysku. |
| Określona konfiguracja sieci wirtualnej nie jest prawidłowy: <br>Określ inną konfigurację sieci wirtualnej i spróbuj ponownie. |Brak |
| Określona usługa w chmurze używa zastrzeżonego adresu IP, który nie odpowiada konfiguracji przywracanej maszyny wirtualnej: <br>Określ inną usługę w chmurze, która nie używa zastrzeżonego adresu IP. Lub wybierz inny punkt odzyskiwania, aby przywrócić z. |Brak |
| Usługi w chmurze osiągnął limit liczby wejściowych punktów końcowych: <br>Spróbuj ponownie wykonać operację, określając inną usługę w chmurze lub za pomocą istniejącego punktu końcowego. |Brak |
| Usługi Recovery Services vault i docelowe konto magazynu znajdują się w dwóch różnych regionach: <br>Upewnij się, że konto magazynu określone w operacji przywracania jest w tym samym regionie platformy Azure co magazyn usługi Recovery Services. |Brak |
| Konto magazynu określone dla operacji przywracania nie jest obsługiwane: <br>Tylko podstawowa lub standardowa z ustawieniami lokalnie nadmiarowej lub geograficznie nadmiarowej replikacji są obsługiwane konta magazynu. Wybieranie konta magazynu obsługiwane. |Brak |
| Typ konta magazynu określony dla operacji przywracania nie jest w trybie online: <br>Upewnij się, że konto magazynu określone w operacji przywracania jest w trybie online. |Ten błąd może wystąpić z powodu błędu przejściowego w usłudze Azure Storage lub z powodu awarii. Wybierz inne konto magazynu. |
| Osiągnięto limit przydziału grupy zasobów: <br>Usuń niektóre grupy zasobów w witrynie Azure portal lub skontaktuj się z działem pomocy technicznej systemu Azure w celu zwiększenia limitów. |Brak |
| Wybrana podsieć nie istnieje: <br>Wybierz podsieć, która istnieje. |Brak |
| Usługa Kopia zapasowa nie ma autoryzacji do dostępu do zasobów w ramach subskrypcji. |Aby rozwiązać ten problem, najpierw Przywróć dyski wykonując kroki opisane w [przywracanie kopii zapasowych dysków](backup-azure-arm-restore-vms.md#restore-disks). Następnie użyj programu PowerShell kroki [tworzenie maszyny Wirtualnej z przywróconych dysków](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Instrukcja BACKUP lub restore wymaga czasu
Jeśli kopia zapasowa ma więcej niż 12 godzin lub przywracania przyjmuje więcej niż 6 godzin, zapoznaj się z [najlepsze praktyki](backup-azure-vms-introduction.md#best-practices) i [zagadnienia dotyczące wydajności](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agent maszyny Wirtualnej
### <a name="set-up-the-vm-agent"></a>Konfigurowanie agenta maszyny Wirtualnej
Zazwyczaj Agent maszyny Wirtualnej jest już obecny w maszynach wirtualnych, które są tworzone z poziomu galerii Azure. Ale maszyny wirtualne, które są migrowane z lokalnych centrów danych, nie będziesz mieć zainstalowanego agenta maszyny Wirtualnej. Dla tych maszyn wirtualnych agenta maszyny Wirtualnej musi być jawnie zainstalowany.

#### <a name="windows-vms"></a>Maszyny wirtualne z systemem Windows

* Pobierz i zainstaluj [plik MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Niezbędne są uprawnienia administratora, aby zakończyć instalację.
* Dla maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania [zaktualizować właściwości maszyny Wirtualnej](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) do wskazania, że agent jest zainstalowany. Ten krok nie jest wymagane dla maszyn wirtualnych usługi Azure Resource Manager.

#### <a name="linux-vms"></a>Maszyny wirtualne z systemem Linux

* Zainstaluj najnowszą wersję agenta z repozytorium dystrybucji. Aby uzyskać szczegółowe informacje dotyczące nazwy pakietu, zobacz [repozytorium agenta systemu Linux](https://github.com/Azure/WALinuxAgent).
* Dla maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania [Użyj ten blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) Aby zaktualizować właściwości maszyny Wirtualnej i sprawdź, czy agent jest zainstalowany. Ten krok nie jest wymagane dla maszyn wirtualnych usługi Resource Manager.

### <a name="update-the-vm-agent"></a>Zaktualizuj agenta maszyny Wirtualnej
#### <a name="windows-vms"></a>Maszyny wirtualne z systemem Windows

* Aby zaktualizować agenta maszyny Wirtualnej, należy ponownie zainstalować [plików binarnych agenta maszyny Wirtualnej](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Przed uaktualnieniem agent, upewnij się, że żadne operacje tworzenia kopii zapasowej wystąpić podczas aktualizowania agenta maszyny Wirtualnej.

#### <a name="linux-vms"></a>Maszyny wirtualne z systemem Linux

* Aby zaktualizować agenta maszyny Wirtualnej systemu Linux, postępuj zgodnie z instrukcjami w artykule [aktualizowania agenta maszyny Wirtualnej systemu Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Aby zaktualizować agenta należy zawsze używać repozytorium dystrybucji.

    Nie można pobrać kod agenta z witryny GitHub. Najnowszą wersję agenta nie jest dostępna dla Twojej dystrybucji, należy skontaktować się z pomocy technicznej dystrybucji, aby uzyskać instrukcje uzyskać najnowszą wersję agenta. Możesz również sprawdzić najnowsze [agenta systemu Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) informacje w repozytorium GitHub.

### <a name="validate-vm-agent-installation"></a>Sprawdzanie poprawności instalacji agenta maszyny Wirtualnej

Sprawdź wersję agenta maszyny Wirtualnej, na maszynach wirtualnych Windows:

1. Zaloguj się do maszyny wirtualnej platformy Azure i przejdź do folderu **C:\WindowsAzure\Packages**. Powinien znajdować się **WaAppAgent.exe** pliku.
2. Kliknij prawym przyciskiem myszy plik i przejdź do **właściwości**. Następnie wybierz pozycję **szczegóły** kartę. **Wersji produktu** pola powinny znajdować się wartość 2.6.1198.718 lub wyższa.

## <a name="troubleshoot-vm-snapshot-issues"></a>Rozwiązywanie problemów dotyczących migawki maszyny Wirtualnej
Kopia zapasowa maszyny Wirtualnej, zależy od tego, wystawiania poleceń migawkę bazowego magazynu. Nie ma dostępu do magazynu lub opóźnienia w zadaniu migawki Uruchom może spowodować zadania tworzenia kopii zapasowej nie powiedzie się. Następujące warunki mogą spowodować niepowodzenie zadania migawki:

- **Dostęp sieciowy do magazynu jest zablokowany za pomocą sieciowej grupy zabezpieczeń**. Dowiedz się więcej na temat sposobu [ustanowić dostępu do sieci](backup-azure-arm-vms-prepare.md#establish-network-connectivity) do magazynu przy użyciu albo dodanie do listy dozwolonych adresów IP lub za pośrednictwem serwera proxy.
- **Maszyny wirtualne z skonfigurowano kopii zapasowej programu SQL Server może spowodować opóźnienie zadań migawek**. Domyślnie kopia zapasowa maszyny Wirtualnej tworzy VSS pełnej kopii zapasowej na maszynach wirtualnych Windows. Maszyny wirtualne z programem SQL Server, za pomocą programu SQL Server skonfigurowano kopię zapasową, mogą występować opóźnienia migawki. W przypadku opóźnienia migawki powodować niepowodzenia tworzenia kopii zapasowej, należy ustawić następujący klucz rejestru:

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **Stan maszyny Wirtualnej jest zgłaszany niepoprawnie, ponieważ maszyna wirtualna jest zamknięta w protokole RDP**. Jeśli używasz pulpitu zdalnego można zamknąć maszyny wirtualnej, sprawdź, czy stan maszyny Wirtualnej w portalu jest poprawna. Jeśli stan nie jest poprawny, należy użyć **zamknięcia** opcję zamykania maszyny Wirtualnej w pulpicie nawigacyjnym maszyn wirtualnych w portalu.
- **Jeśli więcej niż czterech maszyn wirtualnych z udziału jednej usługi w chmurze rozkłada się na maszyny wirtualne wielu zasad tworzenia kopii zapasowych**. Przesunąć godziny tworzenia kopii zapasowej, więc nie, uruchom więcej niż cztery kopii zapasowych maszyn wirtualnych, w tym samym czasie. Próbuje oddzielić godziny rozpoczęcia w zasadach przez co najmniej godzinę.
- **Maszyna wirtualna działa na wysoki procesora CPU lub pamięci**. Jeśli maszyna wirtualna jest wykonywany na dużą ilość pamięci lub użycia procesora CPU, więcej niż 90 procent zadanie migawki jest umieszczane w kolejce i opóźnienia. Po pewnym czasie upłynie limit czasu. Jeśli występuje ten problem, spróbuj kopii zapasowej na żądanie.

## <a name="networking"></a>Networking
Podobnie jak wszystkie rozszerzenia rozszerzenia kopii zapasowej muszą mieć dostęp do publicznego Internetu do pracy. Nie masz dostępu do publicznego Internetu można objawiać na różne sposoby:

* Instalacja rozszerzenia może zakończyć się niepowodzeniem.
* Operacje tworzenia kopii zapasowej, takie jak migawki dysku może zakończyć się niepowodzeniem.
* Wyświetlanie stanu operacji tworzenia kopii zapasowej może zakończyć się niepowodzeniem.

Trzeba rozwiązać publiczne adresy internetowe została omówiona w [ten blog pomocy technicznej systemu Azure](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Sprawdź konfiguracje DNS dla sieci Wirtualnej i upewnij się, że może zostać rozpoznana identyfikatorów URI usługi Azure.

Po rozpoznawanie nazw odbywa się poprawnie, dostęp do adresów IP platformy Azure wymaga także podać. Aby odblokować dostęp do infrastruktury platformy Azure, wykonaj jedną z następujących czynności:

- Umieszczenie zakresów adresów IP centrów danych platformy Azure na liście dozwolonych:
   1. Pobierz listę [centrum danych platformy Azure adresy IP](https://www.microsoft.com/download/details.aspx?id=41653) do listy dozwolonych.
   1. Odblokuj adresów IP przy użyciu [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) polecenia cmdlet. Uruchom to polecenie cmdlet na maszynie wirtualnej platformy Azure, w oknie programu PowerShell z podwyższonym poziomem uprawnień. Uruchom jako Administrator.
   1. Dodaj reguły do sieciowej grupy zabezpieczeń, jeśli nie masz w miejscu, aby umożliwić dostęp do adresów IP.
- Tworzenie ścieżki dla ruchu HTTP do przepływu:
   1. W przypadku niektórych ograniczeń sieci w miejscu, należy wdrożyć serwer proxy HTTP do kierowania ruchu sieciowego. Przykładem jest sieciowa grupa zabezpieczeń. Zobacz kroki wdrażania serwera proxy HTTP w [ustanowienia połączenia z siecią](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Dodaj reguły do sieciowej grupy zabezpieczeń, jeśli nie masz w miejscu, aby zezwolić na dostęp do Internetu z serwera proxy HTTP.

> [!NOTE]
> DHCP musi być włączona w systemie gościa dla maszyn wirtualnych IaaS do pracy. Jeśli potrzebujesz statycznego prywatnego adresu IP, należy go skonfigurować za pomocą witryny Azure portal lub programu PowerShell. Upewnij się, że jest włączona opcja DHCP wewnątrz maszyny Wirtualnej.
> Uzyskaj więcej informacji na temat sposobu konfigurowania statycznego adresu IP za pomocą programu PowerShell:
> - [Jak dodać statycznego adresu IP wewnętrznej do istniejącej maszyny Wirtualnej](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Zmienić metodę alokacji na prywatny adres IP przypisany do interfejsu sieciowego](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>

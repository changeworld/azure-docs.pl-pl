---
title: Rozwiązywanie problemów z błędami kopii zapasowych w usłudze Azure virtual machines
description: Rozwiązywanie problemów z kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure
services: backup
author: srinathv
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: srinathv
ms.openlocfilehash: b8d1152856935c239a59eb9133aaf48d26a5a8b6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259953"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Rozwiązywanie problemów z kopiami zapasowymi maszyn wirtualnych platformy Azure
Można rozwiązać, usuwać błędy napotkane podczas używania usługi Azure Backup, podając informacje przedstawione w poniższej tabeli:

| Szczegóły błędu | Obejście |
| ------ | --- |
| Kopia zapasowa nie można wykonać operacji, ponieważ maszyna wirtualna (VM) już nie istnieje: <br>Zatrzymaj ochronę maszyny wirtualnej bez usuwania danych kopii zapasowej. Aby uzyskać więcej informacji, zobacz [zatrzymanie ochrony maszyn wirtualnych](https://go.microsoft.com/fwlink/?LinkId=808124). |Ten błąd występuje, gdy podstawowa maszyna wirtualna zostanie usunięta, ale zasady kopii zapasowych nadal wygląda dla maszyny Wirtualnej utworzyć kopię zapasową. Aby naprawić ten błąd, wykonaj następujące czynności: <ol><li> Ponownie utwórz maszynę wirtualną przy użyciu tej samej nazwie i tej samej nazwy grupy zasobów, **nazwa usługi w chmurze**,<br>**lub**</li><li> Zatrzymaj ochronę maszyny wirtualnej z lub bez usuwania danych kopii zapasowej. Aby uzyskać więcej informacji, zobacz [zatrzymanie ochrony maszyn wirtualnych](https://go.microsoft.com/fwlink/?LinkId=808124).</li></ol> |
| Agent maszyny wirtualnej platformy Azure (Agent maszyny Wirtualnej) nie może komunikować się z usługą Azure Backup: <br>Upewnij się, maszyna wirtualna ma łączność sieciową, a agent maszyny Wirtualnej jest najnowsze i uruchomione. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure Backup, błąd: Problemy z agentem lub rozszerzenie](https://go.microsoft.com/fwlink/?LinkId=800034). |Ten błąd występuje, jeśli występuje problem z agentem maszyny Wirtualnej lub dostępu do sieci do infrastruktury platformy Azure jest zablokowana w jakiś sposób. Dowiedz się więcej o [profilowanie maszyn wirtualnych migawki problemów](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup). <br><br>Jeśli Agent maszyny Wirtualnej nie powoduje problemy, uruchom ponownie maszynę Wirtualną. Niepoprawny stan maszyny Wirtualnej może powodować problemy, a następnie ponowne uruchomienie maszyny Wirtualnej resetuje stan. |
| Maszyna wirtualna jest w stanie niepowodzenia inicjowania obsługi administracyjnej: <br>Ponowne uruchomienie maszyny Wirtualnej i upewnij się, że maszyna wirtualna jest uruchomiona lub zamknięta. | Ten błąd występuje, gdy jeden z błędami rozszerzeń umieszcza maszynę Wirtualną w stanie niepowodzenia inicjowania obsługi administracyjnej. Przejdź do listy rozszerzeń, należy sprawdzić, jeśli plik ma rozszerzenia nie powiodło się, usuń go i spróbuj ponownie uruchomić maszynę wirtualną. Wszystkie rozszerzenia są w stanie uruchomienia, sprawdź, czy Usługa agenta maszyny Wirtualnej jest uruchomiona. Jeśli nie, uruchom ponownie usługę agenta maszyny Wirtualnej. |
| Kopia zapasowa nie można skopiować migawki maszyny wirtualnej z powodu za mało wolnego miejsca na koncie magazynu: <br>Upewnij się, że konto magazynu ma miejsce równe danych na dyskach magazynu premium storage dołączonych do maszyny wirtualnej. | Dla maszyn wirtualnych usługi premium na stos kopii zapasowej maszyny Wirtualnej w wersji 1 Firma Microsoft kopiowania migawki do konta magazynu. Ten krok pozwala się upewnić, że liczba operacji We/Wy dostępne dla aplikacji przy użyciu dysków w warstwie premium nie są ograniczone ruch zarządzania kopiami zapasowymi, który działa w migawce. <br><br>Zaleca się przydzielanie tylko 50 procent 17.5 TB miejsca do magazynowania łączna liczba kont. Następnie usługa Azure Backup można skopiować migawki do magazynu konta i transfer danych z tej lokalizacji skopiowany do magazynu na koncie magazynu. |
| Kopia zapasowa nie można wykonać operacji, ponieważ Agent maszyny Wirtualnej nie jest interaktywnych. |Ten błąd występuje, jeśli występuje problem z agentem maszyny Wirtualnej lub dostępu do sieci do infrastruktury platformy Azure jest zablokowana w jakiś sposób. W przypadku maszyn wirtualnych Windows Sprawdź stan usługi agenta maszyny Wirtualnej w usługach i czy agent jest wyświetlane na programy w Panelu sterowania. <br><br>Spróbuj usunięcie programu z poziomu Panelu sterowania i ponowne zainstalowanie agenta, zgodnie z opisem w [agenta maszyny Wirtualnej](#vm-agent). Po ponownej instalacji agenta należy wyzwalanie tworzenia kopii zapasowej ad-hoc w celu zweryfikowania. |
| Operacja rozszerzenia usługi recovery services nie powiodło się: <br>Upewnij się najnowszy Agent maszyny Wirtualnej znajduje się na maszynie wirtualnej i jest uruchomiona usługa agenta maszyny Wirtualnej. Ponów próbę wykonania operacji tworzenia kopii zapasowej. Operacja tworzenia kopii zapasowej zakończy się niepowodzeniem, należy skontaktować się z Microsoft Support. |Ten błąd występuje, gdy Agent maszyny Wirtualnej jest nieaktualna. Zapoznaj się z kopiami zapasowymi maszyn wirtualnych Rozwiązywanie problemów z usługi Azure, aby zaktualizować agenta maszyny Wirtualnej. |
| Maszyna wirtualna nie istnieje: <br>Upewnij się, że maszyna wirtualna istnieje, lub wybierz inną maszynę wirtualną. |Ten błąd występuje, gdy podstawowa maszyna wirtualna zostanie usunięta, ale zasady kopii zapasowych nadal wygląda dla maszyny Wirtualnej utworzyć kopię zapasową. Aby naprawić ten błąd, wykonaj następujące czynności: <ol><li> Ponownie utwórz maszynę wirtualną przy użyciu tej samej nazwie i tej samej nazwy grupy zasobów, **nazwa usługi w chmurze**,<br>**lub**<br></li><li>Zatrzymaj ochronę maszyny wirtualnej bez usuwania danych kopii zapasowej. Aby uzyskać więcej informacji, zobacz [zatrzymanie ochrony maszyn wirtualnych](https://go.microsoft.com/fwlink/?LinkId=808124).</li></ol> |
| Polecenie nie powiodło się: <br>Inna operacja jest obecnie w toku dla tego elementu. Poczekaj na zakończenie poprzedniej operacji. Następnie spróbuj ponownie wykonać operację. |Istniejące zadanie tworzenia kopii zapasowej jest uruchomiona, a nowe zadanie nie można uruchomić, dopóki nie zakończy się bieżącego zadania. |
| Kopiowanie wirtualnych dysków twardych z magazynu usługi Recovery Services, upłynął limit czasu: <br>Spróbuj ponownie wykonać operację za kilka minut. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. | Ten błąd występuje, gdy jest to błąd przejściowy magazyn lub jeśli usługa Kopia zapasowa nie odbierze wystarczające konta magazynu, operacje We/Wy na przesyłanie danych do magazynu, przed upływem limitu czasu. Upewnij się, że należy wykonać [najważniejsze wskazówki podczas konfigurowania maszyn wirtualnych z systemem](backup-azure-vms-introduction.md#best-practices). Przenieś maszynę Wirtualną do innego konta magazynu, który nie został załadowany i ponów próbę wykonania zadania tworzenia kopii zapasowej.|
| Tworzenie kopii zapasowej nie powiodło się z powodu błędu wewnętrznego: <br>Spróbuj ponownie wykonać operację za kilka minut. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |Ten błąd dwóch powodów: <ul><li> Podczas uzyskiwania dostępu do magazynu maszyny Wirtualnej jest to problem przejściowy. Sprawdź [stan usługi Azure site](https://azure.microsoft.com/status/) aby zobaczyć, czy obliczeń, magazynu lub problemy z siecią, w tym regionie. Po usunięciu problemu ponów próbę wykonania zadania tworzenia kopii zapasowej. <li> Oryginalna maszyna wirtualna została usunięta i nie może być przyjęty punkt odzyskiwania. Aby przechowywać dane kopii zapasowej usuniętej maszyny Wirtualnej, ale Usuń błędy tworzenia kopii zapasowej, Wyłącz ochronę maszyny Wirtualnej, a następnie wybierz opcję, aby zachować dane. Ta akcja Zatrzymuje zadanie kopii zapasowej i cyklicznego komunikaty o błędach. |
| Tworzenie kopii zapasowej nie można zainstalować rozszerzenia usługi Azure Recovery Services na wybranym elemencie: <br>Agent maszyny Wirtualnej jest wymaganiem wstępnym dla rozszerzenia usługi Azure Recovery Services. Zainstaluj agenta maszyny wirtualnej platformy Azure, a następnie ponownie uruchom operację rejestracji. |<ol> <li>Sprawdź, czy prawidłowo zainstalowano agenta maszyny Wirtualnej. <li>Upewnij się, że Flaga w konfiguracji maszyny Wirtualnej jest ustawiona poprawnie.</ol> Dowiedz się więcej na temat instalowania agenta maszyny Wirtualnej i sposób sprawdzania poprawności instalacji agenta maszyny Wirtualnej. |
| Nie można zainstalować rozszerzenia z powodu błędu **modelu COM + nie może komunikować się z Microsoft Distributed Transaction Coordinator**. |Ten błąd zazwyczaj oznacza, że Usługa COM + nie jest uruchomiona. Aby uzyskać pomoc dotyczącą tego problemu, skontaktuj się z Microsoft Support. |
| Operacja migawki nie powiodło się z powodu błędu operacji usługi kopiowania woluminów w tle (VSS) **ten dysk jest zablokowany przez szyfrowanie dysków funkcją BitLocker. Musisz odblokować ten dysk w Panelu sterowania.** |Wyłącz funkcję BitLocker dla wszystkich dysków na maszynie Wirtualnej i sprawdź, czy usługi VSS problem został rozwiązany. |
| Maszyna wirtualna znajduje się w stanie, który umożliwia tworzenie kopii zapasowych. |<ul><li>Jeśli maszyna wirtualna jest w stanie przejściowym między **systemem** i **Zamknij**, poczekaj, aż zmianę stanu. Następnie wyzwolić zadanie tworzenia kopii zapasowej. <li> Jeśli maszyna wirtualna jest maszyną Wirtualną systemu Linux i używa modułu jądra systemu Linux Security-Enhanced, ścieżka agenta systemu Linux platformy Azure wykluczania **/var/lib/waagent** z zasad zabezpieczeń i upewnij się, że zainstalowano rozszerzenie usługi Azure Backup.  |
| Nie można znaleźć maszyny wirtualnej platformy Azure. |Ten błąd występuje, gdy podstawowa maszyna wirtualna zostanie usunięta, ale zasady kopii zapasowych nadal szuka usuniętej maszyny Wirtualnej. Naprawić ten błąd w następujący sposób: <ol><li>Ponownie utwórz maszynę wirtualną przy użyciu tej samej nazwie i tej samej nazwy grupy zasobów, **nazwa usługi w chmurze**, <br>**lub** <li> Wyłącz ochronę tej maszyny Wirtualnej, więc nie można utworzyć zadania tworzenia kopii zapasowej. </ol> |
| Agent maszyny Wirtualnej nie jest obecny w maszynie wirtualnej: <br>Zainstaluj wszelkie składniki wymagane wstępnie i agenta maszyny Wirtualnej. Następnie uruchom ponownie wykonać operację. |Przeczytaj więcej na temat [instalacji agenta maszyny Wirtualnej i sposób sprawdzania poprawności instalacji agenta maszyny Wirtualnej](#vm-agent). |
| Operacja migawki nie powiodła się, ponieważ składniki zapisywania usługi VSS są w złym stanie. |Uruchom ponownie składniki zapisywania usługi VSS, które znajdują się w nieprawidłowym stanie. W wierszu polecenia z podwyższonym poziomem uprawnień uruchom ```vssadmin list writers```. Dane wyjściowe zawierają wszystkie składniki zapisywania usługi VSS i ich stan. Dla każdego składnika zapisywania usługi VSS, stan, który nie jest **[1] stabilny**, aby ponownie uruchomić składnik zapisywania usługi VSS, uruchom następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień: <ol><li>```net stop serviceName``` <li> ```net start serviceName```</ol>|
| Operacja migawki nie powiodło się z powodu błędu podczas analizowania konfiguracji. |Ten błąd występuje z powodu zmiany uprawnień na **MachineKeys** katalogu: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**. <br> Uruchom następujące polecenie i sprawdź, że uprawnienia **MachineKeys** katalogu są domyślne:<br>**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**. <br><br>Uprawnienia domyślne są następujące: <ul><li>Każdy z nich: (R, W) <li>BUILTIN\Administratorzy: (F)</ul> Jeśli widzisz uprawnień w **MachineKeys** katalogu są inne niż domyślne, wykonaj następujące kroki, popraw uprawnienia, usunąć certyfikat i wyzwalanie tworzenia kopii zapasowej: <ol><li>Usuń uprawnienia na **MachineKeys** katalogu. Za pomocą właściwości zabezpieczeń Explorer oraz zaawansowane ustawienia zabezpieczeń w katalogu, resetować uprawnienia do wartości domyślnych. Usuń wszystkie obiekty użytkowników z wyjątkiem ustawień domyślnych z katalogu i upewnij się, że **wszyscy** uprawnienie dostępu specjalnego ma w następujący sposób: <ul><li>Wyświetlanie zawartości folderu/Odczyt danych <li>Odczyt atrybutów <li>Odczyt atrybutów rozszerzonych <li>Tworzenie plików/Zapis danych <li>Tworzenie folderów/Dołączanie danych<li>Zapis atrybutów<li>Zapis atrybutów rozszerzonych<li>Uprawnienia do odczytu </ul><li>Usunąć wszystkie certyfikaty, gdzie **wystawiony dla** jest klasycznego modelu wdrażania lub **Generator certyfikatów systemu Windows Azure CRP**:<ol><li>[Otwórz przystawkę Certyfikaty na komputerze lokalnym konsoli](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).<li>W obszarze **osobistych** > **certyfikaty**, usunąć wszystkie certyfikaty, gdzie **wystawiony dla** jest klasycznego modelu wdrażania lub **CRP zostaje Zainstalowana Windows Azure Generator certyfikatu**.</ol> <li>Wyzwalanie zadania tworzenia kopii zapasowej maszyny Wirtualnej. </ol>|
| Usługa Azure Backup nie ma wystarczających uprawnień do usługi Azure Key Vault dla kopii zapasowej zaszyfrowanych maszyn wirtualnych. |Dostarcza usługi Kopia zapasowa tych uprawnień w programie PowerShell wykonując kroki opisane w [tworzenie maszyny Wirtualnej z przywróconych dysków](backup-azure-vms-automation.md). |
|Instalacja rozszerzenia migawki nie powiodła się z powodu błędu **modelu COM + nie może komunikować się z Microsoft Distributed Transaction Coordinator**. | W wierszu polecenia z podwyższonym poziomem uprawnień uruchom usługę Windows **aplikacja systemowa modelu COM +**. Na przykład **net start COMSysApp**. Jeśli usługi nie powiedzie się, następnie wykonaj następujące czynności:<ol><li> Upewnij się, że konto logowania usługi **Distributed Transaction Coordinator** jest **Usługa sieciowa**. Jeśli nie, Zmień konto logowania do **Usługa sieciowa** i uruchom ponownie usługę. Następnie spróbuj uruchomić **aplikacja systemowa modelu COM +**.<li>Jeśli **COM + System Application** nie start, wykonaj następujące kroki, aby odinstalować i zainstalować usługę **Distributed Transaction Coordinator**: <ol><li>Zatrzymaj usługi MSDTC. <li>Otwórz wiersz polecenia **cmd**. <li>Uruchom polecenie ```msdtc -uninstall```. <li>Uruchom polecenie ```msdtc -install```. <li>Uruchom usługi MSDTC. </ol> <li>Uruchom usługę Windows **aplikacja systemowa modelu COM +**. Po **aplikacja systemowa modelu COM +** jest uruchamiana, wyzwalanie zadania tworzenia kopii zapasowej w witrynie Azure portal.</ol> |
|  Operacja migawki nie powiodło się z powodu błędu modelu COM +. | Firma Microsoft zaleca, uruchom ponownie usługę Windows **COM + System Application** w wierszu polecenia z podwyższonym poziomem uprawnień **net start COMSysApp**. Jeśli problem będzie się powtarzać, uruchom ponownie maszynę Wirtualną. Jeśli ponowne uruchomienie maszyny Wirtualnej nie pomoże, spróbuj [usuwając rozszerzenie VMSnapshot](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout) i ręcznie wyzwolić tworzenie kopii zapasowej. |
| Kopia zapasowa nie można zablokować co najmniej jednego punktu instalacji maszyny wirtualnej, aby wykonać migawkę spójną z systemu plików. | Wykonaj następujące czynności: <ul><li>Sprawdź stan systemu plików wszystkich zainstalowanych urządzeń przy użyciu **"tune2fs"** polecenia. Na przykład **tune2fs -l/dev/sdb1 \\** .\| grep **stan systemu plików**. <li>Odinstaluj urządzenia, dla których stan systemu plików nie jest czysty przy użyciu **"umount"** polecenia. <li> Uruchom sprawdzanie spójności systemu plików na tych urządzeniach za pomocą **"fsck"** polecenia. <li> Zainstaluj ponownie urządzenia, a następnie spróbuj kopii zapasowej.</ol> |
| Operacja migawki nie powiodło się z powodu błędu tworzenia kanału bezpiecznej komunikacji sieciowej. | <ol><li> Otwórz Edytor rejestru, uruchamiając **regedit.exe** w trybie podniesionych uprawnień. <li> Zidentyfikuj wszystkie wersje programu .NET Framework jest obecny w systemie. Są one obecne w hierarchii klucza rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Dla każdej platformy .NET Framework w kluczu rejestru należy dodać następujący klucz: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| Operacja migawki nie powiodło się z powodu błędu, aby zainstalować pakiet redystrybucyjny Visual C++ dla Visual Studio 2012. | Przejdź do C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion i zainstaluj vcredist2012_x64. Upewnij się, że wartość klucza rejestru, który umożliwia ta instalacja usługi jest ustawiony do poprawnej wartości. Oznacza to, że wartość klucza rejestru **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** ustawiono **3** i nie **4**. <br><br>Jeśli nadal masz problemy z instalacją, uruchom ponownie usługę instalacji uruchamiając **MSIEXEC/unregister** następuje **MSIEXEC /REGISTER** z wiersza polecenia z podwyższonym poziomem uprawnień.  |


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

## <a name="troubleshoot-backup-of-encrypted-vms"></a>Rozwiązywanie problemów z kopii zapasowej zaszyfrowanych maszyn wirtualnych

### <a name="azure-backup-doesnt-have-permissions-for-key-vault-access"></a>Usługa Azure Backup nie ma uprawnień do dostępu do magazynu kluczy
- **Kod błędu:**: UserErrorKeyVaultPermissionsNotConfigured
- **Komunikat o błędzie**: Usługa Azure Backup nie ma wystarczających uprawnień do usługi Key Vault dla kopii zapasowej zaszyfrowanych maszyn wirtualnych.
- **Rozwiązanie**: Przypisanie uprawnień usługi Azure Backup dla usługi Key Vault w [portal](backup-azure-vms-encryption.md#provide-permissions), lub za pomocą [programu PowerShell](backup-azure-vms-automation.md#enable-protection)

### <a name="the-vm-cant-be-restored-because-the-associated-key-vault-doesnt-exist"></a>Nie można przywrócić maszyny Wirtualnej, ponieważ skojarzone usługi Key Vault nie istnieje.
- **Rozwiązanie**: Upewnij się, masz [utworzyć usługę Key Vault](../key-vault/quick-create-portal.md#create-a-vault).
- **Rozwiązanie**: Postępuj zgodnie z [w instrukcjach](backup-azure-restore-key-secret.md) można przywrócić klucza i wpisu tajnego, nawet jeśli nie istnieją one w usłudze Key Vault.

### <a name="the-vm-cant-be-restored-because-the-associated-key-doesnt-exist"></a>Nie można przywrócić maszyny Wirtualnej, ponieważ skojarzony klucz nie istnieje.
- **Kod błędu:**: UserErrorKeyVaultKeyDoesNotExist
- **Komunikat o błędzie**: Nie możesz przywrócić tej zaszyfrowanej maszyny Wirtualnej, ponieważ klucz skojarzony z tą maszyną Wirtualną nie istnieje.
- **Rozwiązanie**: Postępuj zgodnie z [w instrukcjach](backup-azure-restore-key-secret.md) można przywrócić klucza i wpisu tajnego, nawet jeśli nie istnieją one w usłudze Key Vault.

### <a name="the-vm-cant-be-restored-because-azure-backup-doesnt-have-authorization"></a>Nie można przywrócić maszyny Wirtualnej, ponieważ usługa Azure Backup nie ma autoryzacji
- **Kod błędu:**: ProviderAuthorizationFailed/UserErrorProviderAuthorizationFailed
- **Komunikat o błędzie**: Usługa Kopia zapasowa nie ma autoryzacji umożliwiającej dostęp do zasobów w Twojej subskrypcji.
- **Rozwiązanie**: Przywróć dyski, zgodnie z zaleceniami. [Dowiedz się więcej](backup-azure-vms-encryption.md#restore-an-encrypted-vm). 


## <a name="networking"></a>Networking
Podobnie jak wszystkie rozszerzenia rozszerzenie usługi Azure Backup wymaga dostępu do publicznej sieci internet do pracy. Nie masz dostępu do publicznego Internetu można objawiać na różne sposoby:

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

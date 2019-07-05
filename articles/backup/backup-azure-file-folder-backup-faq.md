---
title: Często zadawane pytania podczas wykonywania kopii zapasowej plików i folderów za pomocą usługi Azure Backup
description: Dotyczy często zadawane pytania dotyczące tworzenia kopii zapasowych plików i folderów za pomocą usługi Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: dacurwin
ms.openlocfilehash: d4d1044a30d4ebc551cf1305993aba2a201c4c94
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514450"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Często zadawane pytania dotyczące tworzenia kopii zapasowych plików i folderów

Ten artykuł zawiera odpowiedzi na często zadawane pytania abound kopie zapasowe plików i folderów przy użyciu agenta usługi Microsoft Azure Recovery Services (MARS) w [kopia zapasowa Azure](backup-overview.md) usługi.

## <a name="general"></a>Ogólne

## <a name="configure-backups"></a>Konfigurowanie kopii zapasowych

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Gdzie można pobrać najnowszą wersję agenta usług MARS?
Najnowszą wersję agenta usług MARS używane podczas tworzenia kopii zapasowych maszyn z systemem Windows Server, programu System Center DPM i usługa Microsoft Azure Backup server jest dostępna dla [Pobierz](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Jak długo są prawidłowe poświadczenia magazynu?
Poświadczenia magazynu wygasają po upływie 48 godzin. Jeśli plik poświadczeń wygaśnie, należy ponownie pobrać plik z witryny Azure portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Z stacje, które można wykonywać kopie zapasowe plików i folderów?

Nie można utworzyć kopii zapasowej następujące typy dysków i woluminów:

* Nośniki wymienne: Wszystkie źródła elementów kopii zapasowych należy zgłaszać jako stały.
* Woluminy tylko do odczytu: Wolumin musi być zapisywalny dla usługi kopiowania woluminów w tle (VSS) do funkcji.
* Woluminy offline: Wolumin musi być w trybie online dla usługi VSS funkcji.
* Udziały sieciowe: Wolumin musi być lokalny dla serwera do wykonania kopii zapasowej przy użyciu kopii zapasowej online.
* Woluminy chronione przez funkcję BitLocker: Wolumin musi być odblokowany przed próbą kopii zapasowej.
* Identyfikacja systemu plików: Jedyny obsługiwany system plików to system NTFS.

### <a name="what-file-and-folder-types-are-supported"></a>Jakie typy plików i folderów są obsługiwane?

[Dowiedz się więcej](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) o typach plików i folderów obsługiwana dla kopii zapasowych.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Aby utworzyć kopię zapasową plików i folderów na Maszynie wirtualnej platformy Azure można używać agenta usług MARS?  
Tak. Usługa Azure Backup udostępnia kopii zapasowych na poziomie maszyny Wirtualnej dla maszyn wirtualnych platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej dla agenta maszyny Wirtualnej platformy Azure. Jeśli chcesz utworzyć kopię zapasową plików i folderów w systemie operacyjnym Windows gościa na maszynie Wirtualnej, można zainstalować agenta usług MARS, aby to zrobić.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Aby utworzyć kopię zapasową plików i folderów w magazynie tymczasowym maszyny wirtualnej platformy Azure można używać agenta usług MARS?
Tak. Zainstaluj agenta usług MARS i tworzenia kopii zapasowych plików i folderów w systemie operacyjnym Windows gościa do magazynu tymczasowego.

- Zadania tworzenia kopii zapasowej się nie powieść, gdy dane magazynu tymczasowego zostaną wyczyszczone.
- Jeśli dane magazynu tymczasowego zostanie usunięty, można przywracać tylko do trwałego magazynu.

### <a name="how-do-i-register-a-server-to-another-region"></a>Jak zarejestrować serwer w innym regionie?

Dane kopii zapasowej są wysyłane do centrum danych, magazynu, w której serwer jest zarejestrowany. Najprostszym sposobem zmiany centrum danych jest odinstalowanie i ponowne zainstalowanie agenta, a następnie zarejestrować komputera do nowego magazynu w regionie, których potrzebujesz.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Agenta usług MARS pomocy technicznej systemu Windows Server 2012 deduplikacji?
Tak. Agenta usług MARS konwertuje deduplikowane dane do zwykłej podczas przygotowywania operacji tworzenia kopii zapasowej. Go, a następnie optymalizuje dane kopii zapasowej, dane są szyfrowane, a następnie wysyła zaszyfrowane dane w magazynie.

## <a name="manage-backups"></a>Zarządzanie kopiami zapasowymi

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Co się stanie, jeśli zostanie zmieniona nazwa maszynę Windows skonfigurowane dla kopii zapasowej?

Po zmianie nazwy maszyny Windows wszystkie aktualnie skonfigurowane kopie zapasowe są zatrzymywane.

- Należy zarejestrować nową nazwę komputera w magazynie usługi Backup.
- Po zarejestrowaniu nowej nazwy w magazynie pierwszą operacją jest *pełne* kopii zapasowej.
- Jeśli potrzebujesz odzyskać dane z kopii zapasowej do magazynu przy użyciu starej nazwy serwera, użyj opcji, aby przywrócić do innej lokalizacji w Kreatorze odzyskiwania danych. [Dowiedz się więcej](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Co to jest maksymalna długość ścieżki pliku do utworzenia kopii zapasowej?
Agenta usług MARS opiera się na systemie plików NTFS i używa Specyfikacja długości ścieżki pliku, które są ograniczone przez [interfejsu Windows API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Jeśli pliki, które mają być chronione są dłuższe niż dozwolona wartość, należy utworzyć kopię zapasową folderu nadrzędnego lub napędu dyskowego.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Jakie znaki są dozwolone w ścieżkach plików?

Agenta usług MARS opiera się na systemie plików NTFS i umożliwia [obsługiwane znaki](/windows/desktop/FileIO/naming-a-file#naming-conventions) w ścieżkach nazw plików.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Zostanie wyświetlone ostrzeżenie "Kopii zapasowych Azure nie został skonfigurowany dla tego serwera".
To ostrzeżenie może się pojawić, nawet jeśli skonfigurowano zasady kopii zapasowych, gdy ustawienia harmonogramu tworzenia kopii zapasowych przechowywane na serwerze lokalnym nie są takie same jak ustawienia przechowywane w magazynie kopii zapasowych.
- Gdy serwer lub ustawienia zostały odzyskane do znanego, dobrego stanu, harmonogramy tworzenia kopii zapasowych może stać się niezsynchronizowane.
- Jeśli to ostrzeżenie zostanie wyświetlone [skonfigurować](backup-azure-manage-windows-server.md) zasad tworzenia kopii zapasowej ponownie, a następnie uruchom na żądanie kopii zapasowej, aby ponownie zsynchronizować lokalny serwer z platformą Azure.


## <a name="manage-the-backup-cache-folder"></a>Zarządzaj folderem pamięć podręczna kopii zapasowej

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Jaki jest minimalny wymagany rozmiar folderu pamięci podręcznej?
Rozmiar folderu pamięci podręcznej określa ilość danych, które można umieścić w kopii zapasowej.
- Woluminy folderu pamięci podręcznej powinien mieć wolnego miejsca, która jest równa co najmniej 5 – 10% całkowitego rozmiaru danych kopii zapasowej.
- Jeśli wolumin ma mniej niż 5% wolnego miejsca, Zwiększ rozmiar woluminu lub Przenieś folder pamięci podręcznej do woluminu z wystarczającą ilością miejsca.
- Jeśli utworzenie kopii zapasowej stanu systemu Windows, będziesz potrzebować dodatkowe 30 – 35 GB wolnego miejsca w woluminie zawierającym folder pamięci podręcznej
### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Jak zmienić lokalizację pamięci podręcznej agenta usług MARS?


1. Uruchom następujące polecenie w wierszu polecenia z podwyższonym zatrzymać Aparat kopii zapasowej:

    ```PS C:\> Net stop obengine```

2. Nie przenoś plików. Zamiast tego należy skopiować folder z obszarem pamięci podręcznej na inny dysk, który ma wystarczająco dużo miejsca.
3. Należy zaktualizować następujące wpisy rejestru ze ścieżką do nowego folderu pamięci podręcznej.<br/>

    | Ścieżka rejestru | Klucz rejestru | Wartość |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nowa lokalizacja folderu pamięci podręcznej* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nowa lokalizacja folderu pamięci podręcznej* |

4. Uruchom ponownie Aparat kopii zapasowej w wierszu polecenia z podwyższonym poziomem uprawnień:

    ```PS C:\> Net start obengine```

5. Po kopii zapasowej zakończy się pomyślnie przy użyciu nowej lokalizacji, można usunąć pierwotny folder pamięci podręcznej.


### <a name="where-should-the-cache-folder-be-located"></a>Gdzie należy zlokalizować folderu pamięci podręcznej?

Nie zaleca się używać następujących lokalizacji dla folderu pamięci podręcznej:

* Sieciowy udział/wymiennego nośnika: Folder pamięci podręcznej musi być lokalny dla serwera, który wymaga wykonywania kopii zapasowych przy użyciu kopii zapasowej online. Lokalizacje sieciowe lub nośników wymiennych, takich jak dyski USB nie są obsługiwane.
* Woluminy offline: Folder pamięci podręcznej musi być w trybie online dla oczekiwanej kopii zapasowej przy użyciu agenta usługi Azure Backup

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Czy istnieją atrybuty folderu pamięci podręcznej, które nie są obsługiwane?
Następujące atrybuty folderu pamięci podręcznej ani ich kombinacje nie są obsługiwane:

* Zaszyfrowane
* Deduplikowane
* Skompresowane
* Rozrzedzone
* Punkt ponownej analizy

Folder pamięci podręcznej i dysk VHD metadanych nie mają wymaganych atrybutów dla agenta usługi Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Czy istnieje sposób, aby dostosować wielkość przepustowości używanej do tworzenia kopii zapasowych?

Tak, możesz użyć **Zmień właściwości** opcji w agenta usług MARS dostosowanie przepustowości i chronometrażu. [Dowiedz się więcej](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Przywracanie

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co się stanie, czy w przypadku anulowania zadania przywracania ciągły?

Jeśli bieżące przywracania zostanie anulowane, nie będzie możliwy proces przywracania. Wszystkie pliki, które przywrócono przed anulowaniem Zachowuj skonfigurowane miejsce docelowe (lokalizacji oryginalnej lub alternatywnej) bez żadnych funkcji wycofywania.


## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się,](tutorial-backup-windows-server-to-azure.md) sposób wykonywania kopii zapasowej maszyny z systemem Windows.

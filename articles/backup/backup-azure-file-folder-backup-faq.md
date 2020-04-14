---
title: Tworzenie kopii zapasowych plików i folderów — często zadawane pytania
description: Rozwiązuje typowe pytania dotyczące tworzenia kopii zapasowych plików i folderów za pomocą usługi Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 6e9f265672ff15e40444a46a3e440e73a0051a5b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254754"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Typowe pytania dotyczące tworzenia kopii zapasowych plików i folderów

Ten artykuł zawiera odpowiedzi na często zadawane pytania związane z tworzeniem kopii zapasowych plików i folderów za pomocą agenta usług odzyskiwania platformy Microsoft Azure (MARS) w usłudze [Azure Backup.](backup-overview.md)

## <a name="configure-backups"></a>Konfigurowanie kopii zapasowych

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Gdzie mogę pobrać najnowszą wersję agenta MARS?

Najnowszy agent MARS używany podczas wykonywania kopii zapasowej komputerów z systemem Windows Server, programem System Center DPM i serwerem microsoft azure backup jest dostępny do [pobrania.](https://aka.ms/azurebackup_agent)

### <a name="how-long-are-vault-credentials-valid"></a>Jak długo są prawidłowe poświadczenia magazynu?

Poświadczenia do skarbca wygasają po 10 dniach. Jeśli plik poświadczeń wygaśnie, pobierz plik ponownie z witryny Azure portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Z jakich dysków mogę na swoim miejscu instalować kopie zapasowe plików i folderów?

Nie można wyw.

* Nośnik wymienny: Wszystkie źródła elementów kopii zapasowej muszą być raportowane jako stałe.
* Woluminy tylko do odczytu: wolumin musi być zapisywalny, aby usługa kopiowania woluminów w tle (VSS) działała.
* Woluminy w trybie offline: wolumin musi być w trybie online, aby usługa VSS działała.
* Udziały sieciowe: Wolumin musi być lokalny dla serwera, aby można było wykonać kopię zapasową przy użyciu kopii zapasowej online.
* Woluminy chronione przez funkcję BitLocker: wolumin musi zostać odblokowany, zanim będzie można wykonać kopię zapasową.
* Identyfikacja systemu plików: jedyny obsługiwany system plików to system NTFS.

### <a name="what-file-and-folder-types-are-supported"></a>Jakie typy plików i folderów są obsługiwane?

[Dowiedz się więcej](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) o typach plików i folderów obsługiwanych do tworzenia kopii zapasowych.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Czy mogę używać agenta MARS do utworzenia kopii zapasowej plików i folderów na maszynie Wirtualnej platformy Azure?  

Tak. Usługa Azure Backup zapewnia kopię zapasową na poziomie maszyny wirtualnej dla maszyn wirtualnych platformy Azure przy użyciu rozszerzenia maszyny wirtualnej dla agenta maszyny wirtualnej platformy Azure. Jeśli chcesz wykonać kopie zapasowe plików i folderów w systemie operacyjnym Windows gościa na maszynie Wirtualnej, możesz zainstalować w tym celu agenta MARS.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Czy mogę używać agenta MARS do utworzenia kopii zapasowej plików i folderów w magazynie tymczasowym dla maszyny Wirtualnej platformy Azure?

Tak. Zainstaluj agenta MARS i śmignie kopie zapasowe plików i folderów w systemie operacyjnym Windows gościa do tymczasowego magazynu.

* Zadania tworzenia kopii zapasowej kończą się niepowodzeniem, gdy dane magazynu tymczasowego zostaną wymazane.
* Jeśli dane magazynu tymczasowego zostaną usunięte, można przywrócić tylko do magazynu nieulotnego.

### <a name="how-do-i-register-a-server-to-another-region"></a>Jak zarejestrować serwer w innym regionie?

Dane kopii zapasowej są wysyłane do centrum danych magazynu, w którym jest zarejestrowany serwer. Najprostszym sposobem zmiany centrum danych jest odinstalowanie i ponowne zainstalowanie agenta, a następnie zarejestrowanie komputera w nowym magazynie w potrzebnym regionie.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Czy agent MARS obsługuje deduplikację systemu Windows Server 2012?

Tak. Agent MARS konwertuje deduplikowane dane na normalne dane podczas przygotowywania operacji tworzenia kopii zapasowej. Następnie optymalizuje dane do tworzenia kopii zapasowych, szyfruje dane, a następnie wysyła zaszyfrowane dane do magazynu.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>Czy potrzebuję uprawnień administratora do zainstalowania i skonfigurowania agenta MARS?

Tak, instalacja agenta MARS i konfiguracja kopii zapasowych przy użyciu konsoli MARS wymagają, aby użytkownik był administratorem lokalnym na chronionym serwerze.

## <a name="manage-backups"></a>Zarządzanie kopiami zapasowymi

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Co się stanie, jeśli zmienię nazwę komputera z systemem Windows skonfigurowanego do tworzenia kopii zapasowych?

Po zmianie nazwy komputera z systemem Windows wszystkie aktualnie skonfigurowane kopie zapasowe są zatrzymywane.

* Należy zarejestrować nową nazwę komputera w magazynie kopii zapasowych.
* Podczas rejestrowania nowej nazwy w przechowalni pierwszą operacją jest *pełna* kopia zapasowa.
* Jeśli chcesz odzyskać kopię zapasową danych w magazynie ze starą nazwą serwera, użyj tej opcji, aby przywrócić do alternatywnej lokalizacji w Kreatorze odzyskiwania danych. [Dowiedz się więcej](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Jaka jest maksymalna długość ścieżki pliku dla kopii zapasowej?

Agent MARS opiera się na systemie plików NTFS i używa specyfikacji długości ścieżki pliku ograniczonej przez [interfejs API systemu Windows](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Jeśli pliki, które chcesz chronić, są dłuższe niż dozwolona wartość, należy przywrócić zapasowy folderu nadrzędnego lub dysku.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Jakie znaki są dozwolone w ścieżkach plików?

Agent MARS opiera się na ntfs i umożliwia [obsługiwane znaki](/windows/desktop/FileIO/naming-a-file#naming-conventions) w nazwach plików /ścieżkach.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Pojawi się ostrzeżenie "Kopie zapasowe platformy Azure nie zostały skonfigurowane dla tego serwera"

To ostrzeżenie może pojawić się nawet wtedy, gdy skonfigurowano zasady tworzenia kopii zapasowych, gdy ustawienia harmonogramu kopii zapasowej przechowywane na serwerze lokalnym nie są takie same jak ustawienia przechowywane w magazynie kopii zapasowych.

* Gdy serwer lub ustawienia zostały odzyskane do znanego dobrego stanu, harmonogramy tworzenia kopii zapasowych mogą zostać niezsynchronizowane.
* Jeśli zostanie wyświetlone to ostrzeżenie, [skonfiguruj](backup-azure-manage-windows-server.md) zasady tworzenia kopii zapasowych ponownie, a następnie uruchom kopię zapasową na żądanie, aby ponownie zsynchronizować serwer lokalny za pomocą platformy Azure.

## <a name="manage-the-backup-cache-folder"></a>Zarządzanie folderem pamięci podręcznej kopii zapasowej

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Jaki jest minimalny wymagany rozmiar folderu pamięci podręcznej?

Rozmiar folderu pamięci podręcznej określa ilość danych, które można umieścić w kopii zapasowej.

* Woluminy folderów pamięci podręcznej powinny mieć wolne miejsce, które jest równe co najmniej 5-10% całkowitego rozmiaru danych kopii zapasowej.
* Jeśli wolumin ma mniej niż 5% wolnego miejsca, zwiększ rozmiar woluminu lub przenieś folder pamięci podręcznej na wolumin z wystarczającą ilością miejsca, wykonując [następujące kroki](#how-do-i-change-the-cache-location-for-the-mars-agent).
* W przypadku tworzenia kopii zapasowej stanu systemu Windows w woluminie zawierającym folder pamięci podręcznej potrzebne jest dodatkowe 30–35 GB wolnego miejsca.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Jak sprawdzić, czy folder scratch jest prawidłowy i dostępny?

1. Domyślnie folder zdrapki znajduje się w`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Upewnij się, że ścieżka lokalizacji folderu magazynu jest zgodna z wartościami wpisów klucza rejestru pokazanymi poniżej:

    | Ścieżka rejestru | Klucz rejestru | Wartość |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nowa lokalizacja folderu pamięci podręcznej* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nowa lokalizacja folderu pamięci podręcznej* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Jak zmienić lokalizację pamięci podręcznej agenta MARS?

1. Uruchom to polecenie w wierszu polecenia z podwyższonym poziomem uprawnień, aby zatrzymać aparat kopii zapasowej:

    ```Net stop obengine```
2. Jeśli skonfigurowano kopię zapasową stanu systemu, otwórz zarządzanie dyskami i odinstaluj dyski z nazwami w formacie `"CBSSBVol_<ID>"`.
3. Domyślnie folder zarysowania znajduje się w`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Skopiuj cały `\Scratch` folder na inny dysk, na który ma wystarczającą ilość miejsca. Upewnij się, że zawartość jest kopiowana, a nie przenoszona.
5. Zaktualizuj następujące wpisy rejestru ścieżką nowo przeniesionego folderu scratch.

    | Ścieżka rejestru | Klucz rejestru | Wartość |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nowa lokalizacja folderu zdrapki* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nowa lokalizacja folderu zdrapki* |

6. Uruchom ponownie aparat kopii zapasowej w wierszu polecenia z podwyższonym poziomem uprawnień:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Uruchom kopię zapasową na żądanie. Po pomyślnym zakończeniu tworzenia kopii zapasowej przy użyciu nowej lokalizacji można usunąć oryginalny folder pamięci podręcznej.

### <a name="where-should-the-cache-folder-be-located"></a>Gdzie powinien znajdować się folder pamięci podręcznej?

Następujące lokalizacje folderu pamięci podręcznej nie są zalecane:

* Udział sieciowy/nośnik wymienny: Folder pamięci podręcznej musi być lokalny dla serwera, który wymaga tworzenia kopii zapasowych przy użyciu kopii zapasowej online. Lokalizacje sieciowe lub nośniki wymienne, takie jak dyski USB, nie są obsługiwane.
* Woluminy w trybie offline: folder pamięci podręcznej musi być w trybie online dla oczekiwanej kopii zapasowej przy użyciu agenta azure kopii zapasowej

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Czy są jakieś atrybuty folderu pamięci podręcznej, które nie są obsługiwane?

Następujące atrybuty folderu pamięci podręcznej ani ich kombinacje nie są obsługiwane:

* Zaszyfrowane
* Deduplikowane
* Skompresowane
* Rozrzedzone
* Punkt ponownej analizy

Folder pamięci podręcznej i metadane VHD nie mają niezbędnych atrybutów dla agenta usługi Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Czy istnieje sposób, aby dostosować przepustowość używaną do tworzenia kopii zapasowych?

Tak, można użyć opcji **Zmień właściwości** w agencie MARS, aby dostosować przepustowość i czas. [Dowiedz się więcej](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Przywracanie

### <a name="manage"></a>Zarządzanie

**Czy mogę odzyskać, jeśli zapomniałem hasła?**
Agent usługi Azure Backup wymaga hasła (podanego podczas rejestracji) w celu odszyfrowania kopii zapasowej danych podczas przywracania. Zapoznaj się ze scenariuszami poniżej, aby zapoznać się z opcjami obsługi utraconego hasła:

| Oryginalna maszyna <br> *(maszyna źródłona, na której pobierano kopie zapasowe)* | Hasło | Dostępne opcje |
| --- | --- | --- |
| Dostępne |Utracone |Jeśli oryginalny komputer (na którym zostały wykonane kopie zapasowe) jest dostępny i nadal zarejestrowany w tym samym magazynie usług odzyskiwania, można ponownie wygenerować hasło, wykonując następujące [kroki.](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase)  |
| Utracone |Utracone |Nie można odzyskać danych lub danych nie jest dostępna |

Należy rozważyć następujące kwestie:

* Jeśli odinstalujesz i ponownie zarejestrujesz agenta na tym samym oryginalnym komputerze
  * *To samo hasło*, wtedy będzie można przywrócić dane kopii zapasowej.
  * *Różne hasło*, wtedy nie będzie w stanie przywrócić kopii zapasowej danych.
* Jeśli agent zostanie zainstalowany na *innym komputerze* z
  * *To samo hasło* (używane w oryginalnym komputerze), wtedy będzie można przywrócić dane kopii zapasowej.
  * *Różne hasło*, nie będzie można przywrócić kopii zapasowej danych.
* Jeśli oryginalny komputer jest uszkodzony (uniemożliwiając ponowne wygenerowanie hasła za pośrednictwem konsoli MARS), ale można przywrócić lub uzyskać dostęp do oryginalnego folderu zarysowania używanego przez agenta MARS, możesz być w stanie przywrócić (jeśli nie pamiętasz hasła). Aby uzyskać więcej pomocy, skontaktuj się z działem obsługi klienta.

**Jak odzyskać zdrowie, jeśli zgubiłem oryginalny komputer (w którym wykonano kopie zapasowe)?**

Jeśli masz to samo hasło (które podano podczas rejestracji) oryginalnego komputera, możesz przywrócić dane kopii zapasowej na komputerze alternatywnym. Przejrzyj poniższe scenariusze, aby zapoznać się z opcjami przywracania.

| Oryginalna maszyna | Hasło | Dostępne opcje |
| --- | --- | --- |
| Utracone |Dostępne |Agent MARS można zainstalować i zarejestrować na innym komputerze za pomocą tego samego hasła, który został podany podczas rejestracji oryginalnego komputera. Wybierz **opcję** > odzyskiwania**Inna lokalizacja,** aby wykonać przywracanie. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine).
| Utracone |Utracone |Nie można odzyskać danych lub danych nie jest dostępna |

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co się stanie, jeśli anuluję trwające zadanie przywracania?

Jeśli trwające zadanie przywracania zostanie anulowane, proces przywracania zostanie zatrzymany. Wszystkie pliki przywrócone przed anulowaniem pozostają w skonfigurowanym miejscu docelowym (oryginalna lub alternatywna lokalizacja), bez wycofywania.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>Czy agent MARS wykonuje kopię zapasową i przywraca listy ACL ustawione na plikach, folderach i woluminach?

* Agent MARS utworzy kopie zapasowe list ACL ustawionych na plikach, folderach i woluminach
* W przypadku opcji odzyskiwania przywracania woluminu agent MARS udostępnia opcję pominięcia przywracania uprawnień listy ACL do odzyskiwanych plików lub folderów
* W przypadku opcji odzyskiwania poszczególnych plików i folderów agent MARS przywróci je z uprawnieniami ACL (nie ma opcji pominięcia przywracania listy ACL).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się,](tutorial-backup-windows-server-to-azure.md) jak zrobić kopii zapasowej komputera z systemem Windows.

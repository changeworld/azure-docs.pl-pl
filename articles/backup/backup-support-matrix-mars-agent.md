---
title: Macierz obsługi dla kopii zapasowych maszyn z zainstalowanym agentem usługi Microsoft Azure Recovery Services (MARS) za pomocą usługi Azure Backup
description: Ten artykuł zawiera podsumowanie obsługi usługi Azure Backup, podczas wykonywania kopii zapasowych maszyn z agentem usługi Microsoft Azure Recovery Services (MARS).
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 61afefb955914c75606c4fff36ebcc05a4ad0057
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010909"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Macierz obsługi kopii zapasowej za pomocą agenta usługi Microsoft Azure Recovery Services (MARS)

Możesz użyć [usługi Azure Backup](backup-overview.md) do tworzenia kopii zapasowych maszyn lokalnych i aplikacji i maszyn wirtualnych platformy Azure. Ten artykuł zawiera podsumowanie ustawień obsługi i ograniczenia dotyczące tworzenia kopii zapasowych maszyn z agentem usługi Microsoft Azure Recovery Services (MARS).

## <a name="about-the-mars-agent"></a>Temat agenta usług MARS

Agenta usług MARS jest używany przez usługę Azure Backup do kopie zapasowe danych maszyn lokalnych i maszyn wirtualnych platformy Azure do tworzenia kopii zapasowej usług odzyskiwania magazynu na platformie Azure. Agenta usług MARS może służyć w następujący sposób:
- Uruchom agenta na maszynach Windows w środowisku lokalnym, tak aby ich kopię zapasową można wykonać bezpośrednio do tworzenia kopii zapasowej magazynu usługi Recovery Services na platformie Azure.
- Uruchom agenta na maszynach wirtualnych Azure Windows tak, aby go utworzyć kopię zapasową bezpośrednio do magazynu.
- Uruchom agenta na Microsoft Azure Backup Server (MABS) lub programu System Center Data Protection - server Manager (DPM). W tym scenariuszu maszyn i obciążeń tworzenie kopii zapasowej serwera usługi Mab/DPM, a następnie serwera usługi Mab/DPM jest obsługiwane tworzenie kopii zapasowej magazynu na platformie Azure przy użyciu agenta usług MARS. 

Co można utworzyć kopię, zależy od tego, gdzie agent jest zainstalowany.

- [Dowiedz się więcej](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) o architekturze kopii zapasowej za pomocą agenta usług MARS.
- Dowiedz się więcej na temat serwera usługi Mab/DPM [kopii zapasowej architektury](backup-architecture.md#architecture-back-up-to-dpmmabs)i [wymagania](backup-support-matrix-mabs-dpm.md).


## <a name="supported-installation"></a>Obsługiwane instalacji

**Instalowanie** | **Szczegóły**
--- | ---
**Pobierz najnowszą wersję agenta usług MARS** | Możesz pobrać najnowszą wersję agenta z magazynu, lub [pobrać go bezpośrednio](https://aka.ms/azurebackup_agent).
**Zainstaluj bezpośrednio na maszynie** | Można zainstalować agenta usług MARS bezpośrednio na lokalnym serwerze Windows lub Windows maszyny Wirtualnej platformy Azure z dowolnym z [obsługiwane systemy operacyjne](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).
**Zainstaluj na serwerze kopii zapasowej** | Po skonfigurowaniu programu DPM lub serwera usługi Mab do tworzenia kopii zapasowych na platformie Azure, Pobierz i zainstaluj agenta usług MARS na serwerze. Agenta można zainstalować przy użyciu [obsługiwane systemy operacyjne](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) w macierzy obsługi Utwórz kopię zapasową serwera.

> [!NOTE]
> Włączone dla kopii zapasowych maszyn wirtualnych platformy Azure domyślne mają instalacja rozszerzenia usługi Azure Backup. To rozszerzenie tworzy kopię zapasową całej maszyny Wirtualnej. Można zainstalować i uruchomić agenta usług MARS na maszynie Wirtualnej platformy Azure, obok rozszerzenia, jeśli chcesz utworzyć kopię zapasową określonych folderów i plików, a nie całej maszyny Wirtualnej.
> Po uruchomieniu agenta usług MARS na Maszynie wirtualnej platformy Azure, tworzy kopię zapasową plików/folderów na terenie magazynu tymczasowego na maszynie Wirtualnej. Tworzenie kopii zapasowych zakończy się niepowodzeniem, jeśli pliki/foldery są usuwane z tymczasowego magazynu lub magazyn tymczasowy zostanie usunięty.


## <a name="cache-folder-support"></a>Obsługa folderu pamięci podręcznej

Podczas wykonywania kopii zapasowej za pomocą agenta usług MARS agent tworzy migawkę danych i zapisuje go w folderze lokalnej pamięci podręcznej przed wysłaniem go do platformy Azure. Folder pamięci podręcznej (podstaw) ma pewne wymagania.

**Pamięć podręczna** | **Szczegóły**
--- | ---
**Rozmiar pamięci podręcznej** |  Ilość wolnego miejsca na rozmiar folderu pamięci podręcznej powinien być co najmniej 5 – 10% całkowitego rozmiaru danych kopii zapasowej. 
**Lokalizacja pamięci podręcznej** | Folder pamięci podręcznej musi określać elementy lokalne do maszyny, w których powstaje kopia zapasowa, a musi być w trybie online.<br/><br/> Folder pamięci podręcznej nie powinien być zlokalizowany w udziale sieciowym, nośnik wymienny lub wolumin w trybie offline. 
**Folder pamięci podręcznej** | Folder pamięci podręcznej powinien być zaszyfrowany, w deduplikowanym woluminie lub w folderze, który jest skompresowany/rozrzedzone/punkt ponownej analizy
**Zmodyfikuj lokalizację pamięci podręcznej** | Możesz zmienić lokalizację pamięci podręcznej przez zatrzymanie Aparat kopii zapasowej (net stop bengine) i kopiowania folderu pamięci podręcznej do nowego dysku (Upewnij się, ma wystarczającą ilość miejsca). Następnie zaktualizuj dwóch wpisów rejestru w obszarze HKLM\SOFTWARE\Microsoft\Windows usługi Azure Backup (Config/ScratchLocation i CloudBackupProvider/Config/ScratchLocation) do nowej lokalizacji i ponownie uruchomić aparat.

## <a name="networking-and-access-support"></a>Sieci i dostęp do pomocy technicznej

### <a name="url-access"></a>Dostęp do adresu URL

Agenta usług MARS musi mieć dostęp do tych adresów URL:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="throttling-support"></a>Ograniczanie pomocy technicznej

**Funkcja** | **Szczegóły**
--- | ---
Kontrola przepustowości | Obsługiwane<br/><br/> Użyj **Zmień właściwości** agenta MARS, aby ustawić przepustowość.
Ograniczanie przepustowości sieci | Nie jest dostępna dla kopii zapasowej maszyn z systemem Windows Server 2008 R2, Windows Server 2008 z dodatkiem SP2 lub Windows 7.

## <a name="support-for-direct-backups"></a>Obsługa bezpośrednich kopii zapasowych

W poniższej tabeli przedstawiono, które systemy operacyjne, działających na maszynach lokalnych i maszyn wirtualnych platformy Azure można kopii zapasowej bezpośrednio na platformie Azure za pomocą agenta usług MARS.

- Wszystkie systemy operacyjne są 64-bitowych.
- Wszystkie systemy operacyjne powinna być uruchomiona, najnowsze pakiety usług i aktualizacje.
- Aby uzyskać szczegółowe informacje dotyczące programu DPM i serwera usługi Mab, które może być kopie zapasowe serwerów za pomocą agenta usług MARS, przejrzyj [tej tabeli](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

**System operacyjny** | **Pliki i foldery** | **Stan systemu**
--- | --- | ---
System Windows 10 (Enterprise, Pro, strona główna) | Yes | Nie
Windows 8.1 (Enterprise, Pro)| Yes |Nie
System Windows 8 (Enterprise, Pro) | Yes | Nie
Windows 7 (Ultimate i Enterprise, Pro, Home Premium/Basic, początkowy) | Yes | Nie
System Windows Server 2016 (wersje Standard i Datacenter, podstawowe elementy) | Yes | Yes
Windows Server 2012 R2 (wersje Standard, Datacenter, Foundation, Essentials) | Yes | Yes
Windows Server 2012 (wersje Standard i Datacenter, Foundation) | Yes | Yes
Windows Server 2008 R2 (wersje Standard, Enterprise, Datacenter, Foundation) | Yes | Yes
Windows Server 2008 z dodatkiem SP2 (wersje Standard i Datacenter, Foundation) | Yes | Nie
Windows Storage Server 2016/2012 R2/2012 (wersje Standard i grupy roboczej | Yes | Nie


## <a name="backup-limits"></a>Limity kopii zapasowej

Usługa Azure Backup stosuje limit na rozmiar źródła danych plików/folderów, które można utworzyć kopię zapasową. Rozmiar elementów wybranych do utworzenia kopii zapasowej z jednego woluminu nie może przekraczać wielkości podsumowane w tabeli.

**System operacyjny** | **Limit rozmiaru**
--- | ---
Windows Server 2012 lub nowszy |  54 400 GB
Windows Server 2008 R2 SP1 |    1700 GB
Windows Server 2008 SP2 | 1700 GB
Windows 8 lub nowszy  | 54 400 GB
Windows 7   | 1700 GB


## <a name="supported-file-types-for-backup"></a>Obsługiwane typy plików do utworzenia kopii zapasowej

**Typ** | **Obsługiwane** 
--- | --- 
Zaszyfrowane   | Yes 
Skompresowane | Yes
Rozrzedzone | Yes 
Skompresowane i rozrzedzone | Yes
Twarde linki  | Nieobsługiwane<br/><br/> Pominięte
Punkt ponownej analizy   | Nieobsługiwane<br/><br/> Pominięte
Zaszyfrowane i rozrzedzone |  Nieobsługiwane<br/><br/> Pominięte
Skompresowany strumień   | Nieobsługiwane<br/><br/> Pominięte
Rozrzedzony strumień   | Nieobsługiwane<br/><br/> Pominięte
Jeden dysk (rozrzedzone strumienie są zsynchronizowane pliki)    | Nieobsługiwane 

## <a name="supported-drivesvolumes-for-backup"></a>Obsługiwane dysków/woluminów kopii zapasowej

**Dysk i wolumin** | **Obsługiwane** | **Szczegóły**
--- | --- | ---
Woluminy tylko do odczytu   | Nieobsługiwane | Wolumin musi być zapisywalny dla usługi VSS do pracy.
Woluminy offline | Nieobsługiwane |   Wolumin musi być w trybie online usługi VSS do pracy.
Udział sieciowy   | Nieobsługiwane |   Wolumin musi być lokalny na serwerze do utworzenia kopii zapasowej.
Woluminy chronione przez funkcję BitLocker | Nieobsługiwane |   Wolumin musi zostać odblokowany przed dla kopii zapasowej do pracy.
Identyfikacja systemu plików  | Nieobsługiwane |   Tylko system plików NTFS.
Nośnik wymienny | Nieobsługiwane |   Wszystkie źródła elementów kopii zapasowych musi mieć stan stały.
Dyski deduplikowanych | Obsługiwane.<br/><br/> Usługa Azure Backup konwertuje deduplikowane dane do zwykłych danych. Jego optymalizuje dane, szyfruje je, przechowuje i wysyła je do magazynu.

## <a name="support-for-initial-offline-backup"></a>Obsługa początkowej kopii zapasowej w trybie offline

Usługa Azure Backup obsługuje "rozmieszczania w trybie offline" do transferu danych początkowej kopii zapasowej na platformie Azure przy użyciu dysków. Jest to przydatne, jeśli tworzenie początkowej kopii zapasowej może należeć do zakresu terabajtów (TB). Kopia zapasowa offline jest obsługiwana:

- Bezpośrednie tworzenie kopii zapasowych plików i folderów na maszynach lokalnych działających agenta usług MARS.
- Tworzenie kopii zapasowych obciążeń i pliki z serwera DPM lub serwera usługi MAB.
- Kopie zapasowe offline, nie może służyć do pliki stanu systemu.


## <a name="support-for-restore"></a>Obsługa przywracania

- Nowy [natychmiastowe Przywracanie](backup-instant-restore-capability.md) wersji usługi Azure Backup umożliwia przywracanie danych, przed jego został skopiowany do magazynu.<br/><br/> Aby korzystać z tej funkcji maszyny, w których powstaje kopia zapasowa musi być uruchomiony .NET Framework 4.5.2 lub nowszej.
- Nie można przywrócić kopii zapasowych na maszynie docelowej, ze starszą wersją systemu operacyjnego. Na przykład kopii zapasowej z komputera, Windows 7 można przywrócić w systemie Windows 8 lub nowszym. Jednak kopii zapasowej z komputera z systemem Windows 8 nie można przywrócić komputer Windows 7.


## <a name="next-steps"></a>Kolejne kroki
- [Dowiedz się więcej](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) o architekturze kopii zapasowej za pomocą agenta usług MARS.
- [Dowiedz się,](backup-support-matrix-mabs-dpm.md) co jest obsługiwane podczas uruchamiania agenta usług MARS na Microsoft Azure Backup Server (MABS) lub programu System Center DPM.



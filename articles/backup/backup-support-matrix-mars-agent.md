---
title: Macierz obsługi dla kopii zapasowych maszyn z zainstalowanym agentem usługi Microsoft Azure Recovery Services (MARS) za pomocą usługi Azure Backup
description: Ten artykuł zawiera podsumowanie obsługi usługi Azure Backup, kopie zapasowe maszyn, które są uruchomione agenta usługi Microsoft Azure Recovery Services (MARS).
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9799914cdabf1f64fccfd6bfd891f9498b860e39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922999"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Macierz obsługi kopii zapasowej za pomocą agenta usługi Microsoft Azure Recovery Services (MARS)

Możesz użyć [usługi Azure Backup](backup-overview.md) do tworzenia kopii zapasowych maszyn lokalnych i aplikacji oraz do tworzenia kopii zapasowych maszyn wirtualnych (VM). Ten artykuł zawiera podsumowanie ustawień obsługi i ograniczenia, gdy używasz agenta usługi Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych maszyn.

## <a name="the-mars-agent"></a>Agenta usług MARS

Usługa Azure Backup używa agenta usług MARS, wykonywanie kopii zapasowych danych maszyn lokalnych i maszyn wirtualnych platformy Azure do tworzenia kopii zapasowej magazynu usługi Recovery Services na platformie Azure. Agenta usług MARS wykonywać następujące czynności:
- Tak, aby ich kopię zapasową można wykonać bezpośrednio do tworzenia kopii zapasowej magazynu usługi Recovery Services na platformie Azure, należy uruchomić na maszynach Windows w środowisku lokalnym.
- Tak, aby ich kopię zapasową można wykonać bezpośrednio do magazynu, należy uruchomić na maszynach wirtualnych Windows.
- Uruchom na serwerze System Center Data Protection Manager (DPM) lub Microsoft Azure Backup Server (MABS). W tym scenariuszu maszyn i obciążeń Utwórz kopię zapasową serwera usługi Mab lub serwerze programu DPM. Agenta usług MARS, a następnie tworzy kopię zapasową tego serwera do magazynu na platformie Azure.

Opcje kopii zapasowej zależą od tego, gdzie agent jest zainstalowany. Aby uzyskać więcej informacji, zobacz [architektury usługi Azure Backup przy użyciu agenta usług MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Aby uzyskać informacje o architekturze usługi tworzenia kopii zapasowej programu DPM i serwera usługi Mab, zobacz [wykonaj kopię zapasową programu DPM lub MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Zobacz też [wymagania](backup-support-matrix-mabs-dpm.md) architektury kopii zapasowej.

**Instalacja** | **Szczegóły**
--- | ---
Pobierz najnowszą wersję agenta usług MARS | Możesz pobrać najnowszą wersję agenta z magazynu, lub [pobrać go bezpośrednio](https://aka.ms/azurebackup_agent).
Zainstaluj bezpośrednio na maszynie | Można zainstalować agenta usług MARS bezpośrednio na serwerze Windows w środowisku lokalnym lub na maszynie Wirtualnej Windows, który jest uruchomiony jeden z [obsługiwane systemy operacyjne](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Zainstaluj na serwerze kopii zapasowej | Po skonfigurowaniu programu DPM lub serwera usługi Mab do tworzenia kopii zapasowych na platformie Azure, Pobierz i zainstaluj agenta usług MARS na serwerze. Można zainstalować agenta na [obsługiwane systemy operacyjne](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) w macierzy obsługi Utwórz kopię zapasową serwera.

> [!NOTE]
> Domyślnie maszyny wirtualne platformy Azure, które są włączone dla kopii zapasowej mają instalacja rozszerzenia usługi Azure Backup. To rozszerzenie tworzy kopię zapasową całej maszyny Wirtualnej. Można zainstalować i uruchomić agenta usług MARS na maszynie Wirtualnej platformy Azure, obok rozszerzenia, jeśli chcesz utworzyć kopię zapasową określonych folderów i plików, a nie całej maszyny Wirtualnej.
> Po uruchomieniu agenta usług MARS na Maszynie wirtualnej platformy Azure tworzy kopie zapasowe plików lub folderów, które znajdują się w magazynu tymczasowego na maszynie Wirtualnej. Kończy się niepowodzeniem, jeśli pliki lub foldery są usuwane z tymczasowego magazynu lub magazyn tymczasowy zostanie usunięty.


## <a name="cache-folder-support"></a>Obsługa folderu pamięci podręcznej

Podczas tworzenia kopii zapasowych za pomocą agenta usług MARS, agent tworzy migawkę danych i zapisuje go w folderze lokalnej pamięci podręcznej przed wysłaniem danych na platformie Azure. Folder pamięci podręcznej (podstaw) ma kilka wymagań:

**Pamięć podręczna** | **Szczegóły**
--- | ---
Rozmiar |  Wolne miejsce w folderze pamięci podręcznej powinien być co najmniej 5 – 10 procent całkowitego rozmiaru danych kopii zapasowej.
Lokalizacja | Folder pamięci podręcznej muszą być przechowywane lokalnie na komputerze, na którym jest tworzona kopia zapasowa, a musi być w trybie online. Folder pamięci podręcznej nie powinien być w udziale sieciowym, nośnik wymienny lub wolumin w trybie offline.
Folder | Folder pamięci podręcznej powinien być szyfrowany w deduplikowanym woluminie lub w folderze, który jest skompresowany, jest rozrzedzony lub zawierającego punkt ponownej analizy.
Zmiany lokalizacji | Możesz zmienić lokalizację pamięci podręcznej, zatrzymując Aparat kopii zapasowej (`net stop bengine`) oraz kopiowania folderu pamięci podręcznej do nowego dysku. (Upewnij się, że nowy dysk ma wystarczająco dużo miejsca). Następnie zaktualizuj dwóch wpisów rejestru w obszarze **kopia zapasowa Azure HKLM\SOFTWARE\Microsoft\Windows** (**Config/ScratchLocation** i **CloudBackupProvider-Config-ScratchLocation**) do nowej lokalizacji i uruchom ponownie aparat.

## <a name="networking-and-access-support"></a>Sieci i dostęp do pomocy technicznej

### <a name="url-access"></a>Dostęp do adresu URL

Agenta usług MARS musi mieć dostęp do tych adresów URL:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Ograniczanie pomocy technicznej

**Funkcja** | **Szczegóły**
--- | ---
Kontrola przepustowości | Obsługiwane. W agenta usług MARS **Zmień właściwości** dostosowanie przepustowości.
Ograniczanie przepustowości sieci | Niedostępne dla maszyn kopii zapasowej, z systemem Windows Server 2008 R2, Windows Server 2008 z dodatkiem SP2 lub Windows 7.

## <a name="support-for-direct-backups"></a>Obsługa bezpośrednich kopii zapasowych

Aby utworzyć kopię zapasową bezpośrednio na platformie Azure w niektórych systemach operacyjnych, które są uruchamiane na komputerach lokalnych i maszyn wirtualnych platformy Azure, można użyć agenta usług MARS. Systemy operacyjne muszą być 64-bitowym i powinna być uruchomiona najnowsze pakiety usług i aktualizacje. Poniższa tabela zawiera podsumowanie tych systemów operacyjnych:

**System operacyjny** | **Pliki i foldery** | **Stan systemu**
--- | --- | ---
System Windows 10 (Enterprise, Pro, strona główna) | Yes | Nie
Windows 8.1 (Enterprise, Pro)| Tak |Nie
System Windows 8 (Enterprise, Pro) | Tak | Nie
Windows 7 (Ultimate i Enterprise, Pro, Home Premium/Basic, początkowy) | Tak | Nie
System Windows Server 2016 (wersje Standard i Datacenter, podstawowe elementy) | Tak | Tak
Windows Server 2012 R2 (wersje Standard, Datacenter, Foundation, Essentials) | Tak | Yes
Windows Server 2012 (wersje Standard i Datacenter, Foundation) | Tak | Tak
Windows Server 2008 R2 (wersje Standard, Enterprise, Datacenter, Foundation) | Tak | Yes
Windows Server 2008 z dodatkiem SP2 (wersje Standard i Datacenter, Foundation) | Yes | Nie
Windows Storage Server 2016/2012 R2/2012 (wersje Standard i grupy roboczej) | Tak | Nie

Aby uzyskać więcej informacji, zobacz [systemy operacyjne obsługiwane serwera usługi Mab i program DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Limity kopii zapasowej

Usługa Azure Backup ogranicza rozmiar pliku lub folderu źródła danych, które można utworzyć kopię zapasową. Elementy, które wykonują kopie zapasowe z jednego woluminu nie może przekraczać wielkości podsumowane w poniższej tabeli:

**System operacyjny** | **Limit rozmiaru**
--- | ---
Windows Server 2012 lub nowszy |  54 400 GB
Windows Server 2008 R2 SP1 |    1700 GB
Windows Server 2008 SP2 | 1700 GB
Windows 8 lub nowszy  | 54 400 GB
Windows 7   | 1700 GB


## <a name="supported-file-types-for-backup"></a>Obsługiwane typy plików do utworzenia kopii zapasowej

**Typ** | **Pomoc techniczna**
--- | ---
Zaszyfrowane   | Obsługiwane.
Skompresowane | Obsługiwane.
Rozrzedzone | Obsługiwane.
Skompresowane i rozrzedzone | Obsługiwane.
Twarde linki  | Nieobsługiwane. Pominięto.
Punkt ponownej analizy   | Nieobsługiwane. Pominięto.
Zaszyfrowane i rozrzedzone |  Nieobsługiwane. Pominięto.
Skompresowany strumień   | Nieobsługiwane. Pominięto.
Rozrzedzony strumień   | Nieobsługiwane. Pominięto.
OneDrive (zsynchronizowane pliki są strumienie rozrzedzone)  | Nieobsługiwane.

## <a name="supported-drives-or-volumes-for-backup"></a>Obsługiwane dyski lub woluminy do utworzenia kopii zapasowej

**Dysk i wolumin** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Woluminy tylko do odczytu   | Nieobsługiwane | W tle usługi kopiowania woluminów (VSS) działa tylko wtedy, gdy wolumin jest zapisywalna.
Woluminy offline | Nieobsługiwane |   Usługa VSS działa tylko wtedy, gdy wolumin jest w trybie online.
Udział sieciowy   | Nieobsługiwane |   Wolumin musi być lokalny na serwerze.
Woluminy chronione przez funkcję BitLocker | Nieobsługiwane |   Wolumin musi być odblokowany przed rozpoczęciem tworzenia kopii zapasowej.
Identyfikacja systemu plików  | Nieobsługiwane |   Obsługiwane jest tylko systemu plików NTFS.
Nośnik wymienny | Nieobsługiwane |   Wszystkie źródła elementów kopii zapasowej muszą mieć *stałej* stanu.
Dyski deduplikowanych | Obsługiwane | Usługa Azure Backup konwertuje deduplikowane dane do zwykłych danych. Jego optymalizuje, są szyfrowane, przechowuje i wysyła dane do magazynu.

## <a name="support-for-initial-offline-backup"></a>Obsługa początkowej kopii zapasowej w trybie offline

Usługa Azure Backup obsługuje *rozmieszczanie w trybie offline* transferu danych początkowej kopii zapasowej na platformie Azure przy użyciu dysków. Ta funkcja jest przydatna, jeśli tworzenie początkowej kopii zapasowej może znajdować się w zakresie rozmiaru terabajtów (TB). Kopia zapasowa offline jest obsługiwana:

- Bezpośrednie tworzenie kopii zapasowych plików i folderów na komputerach lokalnych, w których uruchomiono agenta usług MARS.
- Tworzenie kopii zapasowych obciążeń i pliki z serwera DPM lub serwera usługi MAB.

Kopie zapasowe offline, nie może służyć do pliki stanu systemu.

## <a name="support-for-data-restoration"></a>Obsługa przywracaniem danych

Za pomocą [natychmiastowe Przywracanie](backup-instant-restore-capability.md) funkcji usługi Azure Backup można przywrócić dane, przed skopiowaniem ich do magazynu. Maszyny wykonujesz kopie zapasowe musi działać .NET Framework 4.5.2 lub nowszej.

Nie można przywrócić kopii zapasowych na komputerze docelowym z systemem wcześniejszej wersji systemu operacyjnego. Na przykład kopii zapasowej z komputera z systemem Windows 7 można przywrócić w systemie Windows 8 lub nowszym. Ale nie można przywrócić kopii zapasowej z komputera z systemem Windows 8 na komputerze z uruchomionym systemem Windows 7.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [kopii zapasowej architektury, która używa agenta usług MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Dowiedz się, co jest obsługiwane w przypadku możesz [Uruchom agenta MARS na serwerze programu DPM lub MABS](backup-support-matrix-mabs-dpm.md).

---
title: Macierz wsparcia dla agenta MARS
description: W tym artykule podsumowano obsługę usługi Azure Backup podczas tworzenia kopii zapasowej maszyn z uruchomionym agentem usług Microsoft Azure Recovery Services (MARS).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 6085bc647c06b5907282460a2d8706b8549e1bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247867"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Macierz obsługi kopii zapasowej za pomocą agenta usług odzyskiwania platformy Microsoft Azure (MARS)

Za pomocą [usługi Azure Backup](backup-overview.md) można wykonać kopię zapasową na komputerach i aplikacjach lokalnych oraz wykonać kopię zapasową maszyn wirtualnych platformy Azure.You can use the Azure Backup service to backup on-premises machines and apps and to backup up Azure virtual machines (VMs). W tym artykule podsumowano ustawienia i ograniczenia pomocy technicznej podczas korzystania z agenta usług Microsoft Azure Recovery Services (MARS) do utworzenia kopii zapasowej maszyn.

## <a name="the-mars-agent"></a>Agent MARS

Usługa Azure Backup używa agenta MARS do tworzenia kopii zapasowych danych z komputerów lokalnych i maszyn wirtualnych platformy Azure do magazynu usług odzyskiwania kopii zapasowej na platformie Azure. Agent MARS może:

- Uruchom na lokalnych komputerach z systemem Windows, dzięki czemu można wykonać kopię zapasową bezpośrednio do magazynu usług odzyskiwania kopii zapasowej na platformie Azure.
- Uruchom na maszynach wirtualnych systemu Windows, aby mogły one mieć możliwość utworzenia kopii zapasowej bezpośrednio w przechowalni.
- Uruchom na serwerze Microsoft Azure Backup Server (MABS) lub na serwerze Menedżera ochrony danych (System Center Data Protection Manager) (DPM). W tym scenariuszu maszyny i obciążenia kopii zapasowej do mabs lub na serwerze programu DPM. Agent MARS następnie kopii zapasowej tego serwera do magazynu na platformie Azure.

> [!NOTE]
>Usługa Azure Backup nie obsługuje automatycznej regulacji zegara dla czasu letniego (DST). Zmodyfikuj zasady, aby upewnić się, że czas letni jest brany pod uwagę, aby zapobiec rozbieżności między rzeczywistym czasem i zaplanowanym czasem tworzenia kopii zapasowej.

Opcje tworzenia kopii zapasowej zależą od miejsca zainstalowania agenta. Aby uzyskać więcej informacji, zobacz [architektura usługi Azure Backup przy użyciu agenta MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Aby uzyskać informacje na temat architektury kopii zapasowych mabs i programu DPM, zobacz [Tworzenie kopii zapasowych do programu DPM lub MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Zobacz także [wymagania dotyczące](backup-support-matrix-mabs-dpm.md) architektury kopii zapasowej.

**Instalacji** | **Szczegóły**
--- | ---
Pobierz najnowszego agenta MARS | Najnowszą wersję agenta można pobrać ze skarbca lub [pobrać bezpośrednio.](https://aka.ms/azurebackup_agent)
Instalacja bezpośrednio na komputerze | Agenta MARS można zainstalować bezpośrednio na lokalnym serwerze Windows lub na maszynie Wirtualnej systemu Windows z dowolnym [obsługiwanym systemem operacyjnym](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Instalowanie na serwerze kopii zapasowej | Po skonfigurowaniu programu DPM lub MABS do utworzenia kopii zapasowej na platformie Azure można pobrać i zainstalować agenta MARS na serwerze. Agenta można zainstalować w [obsługiwanych systemach operacyjnych](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) w macierzy obsługi serwera kopii zapasowych.

> [!NOTE]
> Domyślnie maszyny wirtualne platformy Azure, które są włączone do tworzenia kopii zapasowych mają instalację rozszerzenia usługi Azure Backup. To rozszerzenie jest kopii zapasowej całej maszyny Wirtualnej. Agent MARS można zainstalować i uruchomić na maszynie Wirtualnej platformy Azure wraz z rozszerzeniem, jeśli chcesz wykonać kopie zapasowe określonych folderów i plików, a nie pełnej maszyny Wirtualnej.
> Po uruchomieniu agenta MARS na maszynie Wirtualnej platformy Azure, kopie zapasowe plików lub folderów, które znajdują się w magazynie tymczasowym na maszynie Wirtualnej. Kopie zapasowe kończą się niepowodzeniem, jeśli pliki lub foldery zostaną usunięte z magazynu tymczasowego lub jeśli magazyn tymczasowy zostanie usunięty.

## <a name="cache-folder-support"></a>Obsługa folderów pamięci podręcznej

Podczas korzystania z agenta MARS do utworzenia kopii zapasowej danych agent wykonuje migawkę danych i przechowuje je w folderze lokalnej pamięci podręcznej, zanim wyśle dane na platformę Azure. Folder pamięci podręcznej (podstaw) ma kilka wymagań:

**Pamięci podręcznej** | **Szczegóły**
--- | ---
Rozmiar |  Wolne miejsce w folderze pamięci podręcznej powinno wynosić co najmniej 5 do 10 procent całkowitego rozmiaru danych kopii zapasowej.
Lokalizacja | Folder pamięci podręcznej musi być przechowywany lokalnie na komputerze, na który jest archiwizowana kopia zapasowa, i musi być w trybie online. Folder pamięci podręcznej nie powinien znajdować się w udziale sieciowym, na nośniku wymiennym ani na woluminie offline.
Folder | Folder pamięci podręcznej nie powinien być szyfrowany na woluminie deduplikowanym lub w folderze, który jest skompresowany, który jest rozrzedzony lub ma punkt ponownejparieny.
Zmiany lokalizacji | Lokalizację pamięci podręcznej można zmienić,`net stop bengine`zatrzymując aparat kopii zapasowej ( ) i kopiując folder pamięci podręcznej na nowy dysk. (Upewnij się, że nowy dysk ma wystarczająco dużo miejsca). Następnie zaktualizuj dwa wpisy rejestru w obszarze **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** **(Config/ScratchLocation** and **Config/CloudBackupProvider/ScratchLocation)** do nowej lokalizacji i uruchom ponownie aparat.

## <a name="networking-and-access-support"></a>Obsługa sieci i dostępu

### <a name="url-and-ip-access"></a>Adres URL i dostęp do adresu IP

Agent MARS potrzebuje dostępu do tych adresów URL:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net

I do tych adresów IP:

- 20.190.128.0/18
- 40.126.0.0/18

Dostęp do wszystkich adresów URL i adresów IP wymienionych powyżej używa protokołu HTTPS na porcie 443.

### <a name="azure-expressroute-support"></a>Pomoc techniczna usługi Azure ExpressRoute

Można wywrzeć zapas danych za pomocą usługi Azure ExpressRoute za pomocą publicznej komunikacji równorzędnej (dostępnej dla starych obwodów) i komunikacji równorzędnej firmy Microsoft. Tworzenie kopii zapasowych za pomocą prywatnej komunikacji równorzędnej nie jest obsługiwane.

Komunikacja równorzędna publiczna: zapewnij dostęp do następujących domen/adresów:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

W komunikacji równorzędnej firmy Microsoft wybierz następujące usługi/regiony i odpowiednie wartości społeczności:

- Usługa Azure Active Directory (12076:5060)
- Region Platformy Microsoft Azure (w zależności od lokalizacji magazynu usług odzyskiwania)
- Usługa Azure Storage (w zależności od lokalizacji magazynu usług odzyskiwania)

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące routingu usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>Publiczne peering jest przestarzałe dla nowych obwodów.

### <a name="throttling-support"></a>Obsługa ograniczania przepustowości

**Funkcja** | **Szczegóły**
--- | ---
Kontrola przepustowości | Obsługiwane. W agencie MARS użyj **funkcji Zmień właściwości,** aby dostosować przepustowość.
Ograniczanie przepustowości sieci | Opcja niedostępna dla komputerów z kopiami zapasowymi z systemem Windows Server 2008 R2, Windows Server 2008 z znużenie i windows 7.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

>[!NOTE]
> Agent MARS nie obsługuje podstawowych jednostek SKU systemu Windows Server.

Za pomocą agenta MARS można przeprowadzić wcześniej wcześniej utworzenie kopii zapasowej bezpośrednio na platformie Azure w systemach operacyjnych wymienionych poniżej, które są uruchamiane w serwisie:

1. Lokalne serwery systemu Windows
2. Maszyny wirtualne platformy Azure z systemem Windows

Systemy operacyjne muszą mieć 64 bity i powinny być uruchomione najnowsze pakiety i aktualizacje usług. W poniższej tabeli podsumowano te systemy operacyjne:

**System operacyjny** | **Pliki/foldery** | **Stan systemu** | **Wymagania dotyczące oprogramowania/modułu**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Tak | Nie |  Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu
Windows 8.1 (Enterprise, Pro)| Tak |Nie | Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu
Windows 8 (Enterprise, Pro) | Tak | Nie | Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu
Windows Server 2016 (standard, centrum danych, podstawowe elementy) | Tak | Tak | - .NET 4.5 <br> - Program Windows PowerShell <br> - Najnowsze kompatybilne Microsoft VC ++ Redystrybucyjne <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (standard, centrum danych, fundacja, podstawowe) | Tak | Tak | - .NET 4.5 <br> - Program Windows PowerShell <br> - Najnowsze kompatybilne Microsoft VC ++ Redystrybucyjne <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (standard, centrum danych, fundacja) | Tak | Tak |- .NET 4.5 <br> -Windows PowerShell <br> - Najnowsze kompatybilne Microsoft VC ++ Redystrybucyjne <br> - Microsoft Management Console (MMC) 3.0 <br> - Obsługa obrazu wdrożenia i zarządzanie obrazami (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (standardowy, grupa robocza) | Tak | Nie | - .NET 4.5 <br> - Program Windows PowerShell <br> - Najnowsze kompatybilne Microsoft VC ++ Redystrybucyjne <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (standard, centrum danych, podstawowe informacje) | Tak | Tak | - .NET 4.5 <br> - Program Windows PowerShell <br> - Najnowsze kompatybilne Microsoft VC ++ Redystrybucyjne <br> - Microsoft Management Console (MMC) 3.0

Aby uzyskać więcej informacji, zobacz [Obsługiwane systemy operacyjne MABS i DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Systemy operacyjne na koniec wsparcia

Następujące systemy operacyjne są na końcu pomocy technicznej i zdecydowanie zaleca się uaktualnienie systemu operacyjnego, aby nadal pozostawał chroniony.

Jeśli istniejące zobowiązania uniemożliwiają uaktualnianie systemu operacyjnego, rozważ migrację serwerów systemu Windows na maszyny wirtualne platformy Azure i skorzystaj z kopii zapasowych maszyn wirtualnych platformy Azure, aby nadal być chronionym. Odwiedź [stronę migracji tutaj,](https://azure.microsoft.com/migration/windows-server/) aby uzyskać więcej informacji na temat migracji serwera Windows.

W środowiskach lokalnych lub hostowanych, w których nie można uaktualnić systemu operacyjnego ani przeprowadzić migracji na platformę Azure, należy aktywować rozszerzone aktualizacje zabezpieczeń dla komputerów, aby nadal pozostawały chronione i obsługiwane. Należy zauważyć, że tylko określone wersje kwalifikują się do rozszerzonych aktualizacji zabezpieczeń. Odwiedź [stronę z często zadawanymi pytaniami,](https://www.microsoft.com/cloud-platform/extended-security-updates) aby dowiedzieć się więcej.

| **System operacyjny**                                       | **Pliki/foldery** | **Stan systemu** | **Wymagania dotyczące oprogramowania/modułu**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Tak               | Nie                 | Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu |
| Windows Server 2008 R2 (standard, przedsiębiorstwo, centrum danych, fundacja) | Tak               | Tak                | - .NET 3.5, .NET 4.5 <br>  - Program Windows PowerShell <br>  - Kompatybilny Microsoft VC ++ Redystrybucyjny <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Obsługa obrazu wdrożenia i zarządzanie obrazami (DISM.exe) |
| System Windows Server 2008 z dodatkem SP2 (standard, centrum danych, fundacja)  | Tak               | Nie                 | - .NET 3.5, .NET 4.5 <br>  - Program Windows PowerShell <br>  - Kompatybilny Microsoft VC ++ Redystrybucyjny <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Obsługa obrazu wdrożenia i zarządzanie obrazami (DISM.exe) <br>  - Serwer wirtualny 2005 base + KB948515 |

## <a name="backup-limits"></a>Limity kopii zapasowych

### <a name="size-limits"></a>Limity rozmiaru

Usługa Azure Backup ogranicza rozmiar źródła danych pliku lub folderu, który można utworzyć kopię zapasową. Elementy, które można kopii zapasowej z jednego woluminu nie może przekroczyć rozmiary podsumowane w tej tabeli:

**System operacyjny** | **Limit rozmiaru**
--- | ---
Windows Server 2012 lub nowszy |54 400 GB
Windows Server 2008 R2 SP1 |1700 GB
Windows Server 2008 SP2| 1700 GB
Windows 8 lub nowszy| 54 400 GB
Windows 7| 1700 GB

### <a name="other-limitations"></a>Inne ograniczenia

- Mars nie obsługuje ochrony wielu komputerów o tej samej nazwie do jednego magazynu.

## <a name="supported-file-types-for-backup"></a>Obsługiwane typy plików do tworzenia kopii zapasowych

**Typ** | **Pomoc techniczna**
--- | ---
Szyfrowane<sup>*</sup>| Obsługiwane.
Skompresowane | Obsługiwane.
Rozrzedzone | Obsługiwane.
Skompresowane i rzadkie |Obsługiwane.
Twarde linki| Bez pomocy technicznej. Pominięte.
Punkt ponownej analizy| Bez pomocy technicznej. Pominięte.
Szyfrowane i rzadkie |Bez pomocy technicznej. Pominięte.
Strumień skompresowany| Bez pomocy technicznej. Pominięte.
Strumień rozrzedzony| Bez pomocy technicznej. Pominięte.
OneDrive (zsynchronizowane pliki są rozrzedzone strumienie)| Bez pomocy technicznej.
Foldery z włączoną replikacją systemu plików DFS | Bez pomocy technicznej.

\*Upewnij się, że agent MARS ma dostęp do wymaganych certyfikatów, aby uzyskać dostęp do zaszyfrowanych plików. Niedostępne pliki zostaną pominięte.

## <a name="supported-drives-or-volumes-for-backup"></a>Obsługiwane dyski lub woluminy do tworzenia kopii zapasowych

**Napęd/głośność** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Woluminy tylko do odczytu| Nieobsługiwane | Usługa W tle kopiowania woluminów (VSS) działa tylko wtedy, gdy wolumin jest zapisywalny.
Woluminy w trybie offline| Nieobsługiwane |Usługa VSS działa tylko wtedy, gdy wolumin jest w trybie online.
Udział sieciowy| Nieobsługiwane |Wolumin musi być lokalny na serwerze.
Woluminy zablokowane z funkcji BitLocker| Nieobsługiwane |Wolumin musi zostać odblokowany przed rozpoczęciem tworzenia kopii zapasowej.
Identyfikacja systemu plików| Nieobsługiwane |Obsługiwane jest tylko ntfs.
Nośnik wymienny| Nieobsługiwane |Wszystkie źródła elementów kopii zapasowej muszą mieć *stały* stan.
Deduplikowane dyski | Obsługiwane | Usługa Azure Backup konwertuje deduplikowane dane na normalne dane. Optymalizuje, szyfruje, przechowuje i wysyła dane do magazynu.

## <a name="support-for-initial-offline-backup"></a>Obsługa początkowej kopii zapasowej w trybie offline

Usługa Azure Backup obsługuje *rozmieszczanie w trybie offline* w celu przesyłania początkowych danych kopii zapasowej na platformę Azure przy użyciu dysków. Ta obsługa jest przydatna, jeśli początkowa kopia zapasowa prawdopodobnie mieści się w zakresie rozmiarów terabajtów (TBs). Kopia zapasowa w trybie offline jest obsługiwana dla:

- Bezpośrednia kopia zapasowa plików i folderów na komputerach lokalnych z uruchomionym agentem MARS.
- Tworzenie kopii zapasowych obciążeń i plików z serwera programu DPM lub programu MABS.

Kopii zapasowej w trybie offline nie można używać dla plików stanu systemu.

## <a name="support-for-data-restoration"></a>Obsługa przywracania danych

Korzystając z funkcji [natychmiastowego przywracania](backup-instant-restore-capability.md) usługi Azure Backup, można przywrócić dane, zanim są kopiowane do magazynu. Komputer, którego kopię zapasową, musi być uruchomiony program .NET Framework 4.5.2 lub nowszy.

Kopii zapasowych nie można przywrócić na komputerze docelowym z uruchomioną wcześniejszą wersją systemu operacyjnego. Na przykład kopię zapasową pobraną z komputera z systemem Windows 7 można przywrócić w systemie Windows 8 lub nowszym. Ale kopii zapasowej pobranej z komputera z systemem Windows 8 nie można przywrócić na komputerze z systemem Windows 7.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [architekturze kopii zapasowych korzystającej z agenta MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Dowiedz się, co jest obsługiwane po [uruchomieniu agenta MARS na serwerze MABS lub serwerze programu DPM.](backup-support-matrix-mabs-dpm.md)

---
title: What's new in serwera usługi Microsoft Azure Backup
description: Microsoft Azure Backup Server umożliwia rozszerzone możliwości tworzenia kopii zapasowej na potrzeby ochrony maszyn wirtualnych, pliki i foldery oraz obciążeń. Dowiedz się, jak zainstalować lub uaktualnić do usługi Azure Backup Server w wersji 3.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 5718064994a80266c216ae6040746be29194adc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254706"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>What's new in serwera usługi Microsoft Azure Backup

Serwer usługi Microsoft Azure Backup w wersji 3 (MABS V3) jest najnowszą aktualizację i obejmuje poprawki błędów krytycznych, pomocy technicznej systemu Windows Server 2019, SQL 2017 pomocy technicznej i inne funkcje i ulepszenia. Aby wyświetlić listę usterki usunięte i instrukcje dotyczące instalacji dla serwera usługi Mab w wersji 3, zobacz artykuł bazy wiedzy [4457852](https://support.microsoft.com/en-us/help/4457852/microsoft-azure-backup-server-v3).

Poniższe funkcje dostępne w wersji serwera usługi Mab 3:

## <a name="volume-to-volume-migration"></a>Migracja woluminu do woluminu
Za pomocą nowoczesnych kopii zapasowej magazynu (MB) w wersji 2 serwera usługi Mab, firma Microsoft oferuje najlepszą magazynowi obsługującemu obciążenie skonfiguruje niektórych obciążeń do wykonania kopii zapasowej na potrzeby określonego magazynu, na podstawie właściwości magazynu. Jednak po przeprowadzeniu konfiguracji, może się okazać, aby przenieść kopii zapasowych dla niektórych źródeł danych do innego urządzenia pamięci masowej wykorzystania zasobów zoptymalizowane. Serwera usługi Mab w wersji 3 oferuje możliwość migracji kopii zapasowych i skonfigurować je do zapisania na inny wolumin w [3 krokach](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/).

## <a name="prevent-unexpected-data-loss"></a>Zapobiegaj nieoczekiwanej utraty danych
W przedsiębiorstwach serwera usługi Mab jest zarządzane przez zespół administratorów. Gdy są wyświetlane wskazówki, magazynu, które mają być używane do przechowywania kopii zapasowych, niepoprawny woluminu do serwera usługi Mab jako magazyn kopii zapasowych może prowadzić do utraty danych o kluczowym znaczeniu. Za pomocą serwera usługi Mab w wersji 3 takich scenariuszy można zapobiec przez skonfigurowanie tych woluminów jako te, które nie są dostępne dla magazynu za pomocą [tych poleceń cmdlet programu PowerShell](https://docs.microsoft.com/system-center/dpm/add-storage#volume-exclusion).

## <a name="custom-size-allocation"></a>Rozmiar niestandardowy alokacji
Nowoczesny magazyn kopii zapasowych (MB) korzysta z magazynu alokowane, jak i kiedy potrzebne. Aby to zrobić, serwera usługi Mab oblicza rozmiar danych, tworzona kopia zapasowa, gdy jest skonfigurowany do ochrony. Jednak jeśli wiele plików i folderów tworzona jest kopia zapasowa ze sobą, tak jak w przypadku serwera plików, Obliczanie rozmiaru może potrwać dłuższy czas. Za pomocą serwera usługi Mab w wersji 3 można skonfigurować serwera usługi Mab, aby zaakceptować rozmiaru woluminu jako domyślny, zamiast obliczając rozmiar każdego pliku, dlatego oszczędność czasu.

## <a name="optimized-cc-for-rct-vms"></a>DW zoptymalizowanego dla maszyn wirtualnych
Śledzenie RCT (natywnego śledzenia zmian w funkcji Hyper-V), która zmniejsza potrzebę czasochłonnego sprawdzania spójności sprawdza w scenariuszach ulega awarii maszyny Wirtualnej korzysta z serwera usługi MAB. Śledzenie RCT zapewnia większą odporność niż śledzenie zmian, dostarczone przez tworzenie kopii zapasowych usługi VSS na podstawie migawki. V3 serwera usługi Mab optymalizuje dalszego użycia sieci i magazynu przez przeniesienie tylko dane zmienione podczas kontroli spójności.

## <a name="support-to-tls-12"></a>Obsługa protokołu TLS 1.2
Protokół TLS 1.2 to bezpieczny sposób komunikacji zalecane przez firmę Microsoft przy użyciu najlepszych w klasie szyfrowania. Serwera usługi Mab obsługuje teraz protokół TLS 1.2 komunikacji między serwera usługi Mab i chronione serwery, dla uwierzytelniania opartego na certyfikatach oraz kopie zapasowe w chmurze.

## <a name="vmware-vm-protection-support"></a>Obsługa ochrony maszyn wirtualnych programu VMware
Kopia zapasowa maszyny Wirtualnej VMware jest teraz obsługiwane dla wdrożeń produkcyjnych. Serwera usługi Mab w wersji 3 oferuje ochronę maszyny Wirtualnej VMware z poniższymi:

-   Obsługa programu vCenter i ESXi 6.5, oraz obsługę 5.5 i 6.0.
- Automatyczna ochrona maszyn wirtualnych programu VMware do chmury. Nowe maszyny wirtualne VMware są dodawane do folderu chronione, są automatycznie chronione na dysku i w chmurze.
- Ulepszenia wydajności procesu odzyskiwania VMware alternatywną lokalizację odzyskiwania.

## <a name="sql-2017-support"></a>Obsługa SQL 2017
Jako baza danych serwera usługi Mab z programem SQL 2017 można zainstalować serwera usługi Mab w wersji 3. Uaktualnij program SQL server z programu SQL 2016 do programu SQL 2017 lub zainstaluj go świeżo. Można również wykonać kopię zapasową SQL 2017 obciążenia zarówno w środowisku klastrowanych i nieklastrowanych z serwera usługi Mab w wersji 3.

## <a name="windows-server-2019-support"></a>Obsługa systemu Windows Server 2019
2019 serwera systemu Windows można zainstalować serwera usługi Mab w wersji 3. Za pomocą serwera usługi Mab w wersji 3 WS2019, możesz zaktualizować system operacyjny do WS2019 przed zainstalowaniem/uaktualniania do serwera usługi Mab w wersji 3 lub uaktualnić system operacyjny po 3 na WS2016 instalowanie/uaktualniania.

Serwera usługi Mab w wersji 3 jest pełna wersja i można zainstalować bezpośrednio w systemie Windows Server 2016, Windows 2019 r Server lub można uaktualnić z serwera usługi Mab w wersji 2. Zanim uaktualnisz do lub zainstalować kopii zapasowej serwera w wersji 3, przeczytaj o wymagania wstępne instalacji.
Znajdź więcej informacji na temat instalacji i/lub uaktualnić kroków dla serwera usługi Mab [tutaj](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).


> [!NOTE]
> 
> Serwera usługi Mab ma ten sam kod podstawowy, jak System Center Data Protection Manager. Serwera usługi Mab w wersji 3 jest odpowiednikiem 1807 programu Data Protection Manager.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak przygotować serwer lub rozpoczęciu ochrony obciążenia:
- [Znane problemy w wersji 3 serwera usługi Mab](backup-mabs-release-notes-v3.md)
- [Przygotowywanie obciążeń serwera usługi Backup](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej serwera VMware przy użyciu kopii zapasowej serwera](backup-azure-backup-server-vmware.md)
- [Tworzenie kopii zapasowych programu SQL Server przy użyciu kopii zapasowej serwera](backup-azure-sql-mabs.md)
- [Modern Backup Storage za pomocą kopii zapasowej serwera](backup-mabs-add-storage.md)

---
title: Co nowego w usłudze Microsoft Azure Backup Server
description: Program Microsoft Azure Backup Server zapewnia rozszerzone możliwości tworzenia kopii zapasowych w celu ochrony maszyn wirtualnych, plików i folderów, obciążeń i innych funkcji. Dowiedz się, jak zainstalować lub uaktualnić do usługi Azure Backup Server v3.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582810"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Co nowego w usłudze Microsoft Azure Backup Server

Microsoft Azure Backup Server w wersji 3 (MABS V3) jest najnowszym uaktualnieniem i zawiera krytyczne poprawki błędów, obsługę systemu Windows Server 2019, obsługę programu SQL 2017 oraz inne funkcje i ulepszenia. Aby wyświetlić listę poprawionych błędów i instrukcje instalacji dla MABS V3, zobacz artykuł [KB 4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

W mabs V3 zawiera następujące funkcje:

## <a name="volume-to-volume-migration"></a>Migracja woluminu do woluminu

Dzięki nowoczesnemu magazynowi kopii zapasowych (MBS) w mabs v2 ogłosiliśmy magazyn obsługujący obciążenie, w którym można skonfigurować niektóre obciążenia do utworzenia kopii zapasowej w określonym magazynie na podstawie właściwości magazynu. Jednak po konfiguracji może się okazać, że konieczne jest przeniesienie kopii zapasowych niektórych źródeł danych do innego magazynu w celu zoptymalizowanego wykorzystania zasobów. Mabs V3 umożliwia migrację kopii zapasowych i skonfigurowanie ich do przechowywania na innym woluminie w [trzech krokach.](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)

## <a name="prevent-unexpected-data-loss"></a>Zapobiegaj nieoczekiwanej utracie danych

W przedsiębiorstwach mabs jest zarządzany przez zespół administratorów. Chociaż istnieją wytyczne dotyczące magazynu, które powinny być używane do tworzenia kopii zapasowych, niepoprawny wolumin podany mabs jako magazyn kopii zapasowej może prowadzić do utraty danych krytycznych. Za pomocą programu MABS V3 można zapobiec takim scenariuszom, konfigurując te woluminy jako te, które nie są dostępne do przechowywania przy użyciu [tych poleceń cmdlet programu PowerShell](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Alokacja rozmiaru niestandardowego

Nowoczesny magazyn kopii zapasowych (MBS) zużywa pamięć masową cienko, w razie potrzeby. W tym celu program MABS oblicza rozmiar kopii zapasowej danych, gdy są skonfigurowane do ochrony. Jeśli jednak kopia zapasowa wielu plików i folderów jest archiwizacja razem, tak jak w przypadku serwera plików, obliczanie rozmiaru może zająć dużo czasu. Za pomocą mabs V3 można skonfigurować mabs, aby akceptował rozmiar woluminu jako domyślny, zamiast obliczać rozmiar każdego pliku, oszczędzając w ten sposób czas.

## <a name="optimized-cc-for-rct-vms"></a>Zoptymalizowany CC dla maszyn wirtualnych RCT

Mabs używa RCT (natywne śledzenie zmian w funkcji Hyper-V), co zmniejsza potrzebę czasochłonnej kontroli spójności w scenariuszach, gdy maszyna wirtualna ulega awarii. Śledzenie RCT zapewnia większą odporność niż śledzenie zmian w opartych na migawkach kopiach zapasowych usługi VSS. MABS V3 dodatkowo optymalizuje zużycie sieci i pamięci masowej, przesyłając tylko zmienione dane podczas sprawdzania spójności.

## <a name="support-to-tls-12"></a>Obsługa protokołu TLS 1.2

TLS 1.2 to bezpieczny sposób komunikacji sugerowany przez firmę Microsoft z najlepszym w swojej klasie szyfrowaniem. Usługa MABS obsługuje teraz komunikację TLS 1.2 między usługą MABS a chronionymi serwerami, uwierzytelnianie oparte na certyfikatach i tworzenie kopii zapasowych w chmurze.

## <a name="vmware-vm-protection-support"></a>Obsługa ochrony maszyn wirtualnych VMware

Kopia zapasowa maszyn wirtualnych VMware jest teraz obsługiwana w przypadku wdrożeń produkcyjnych. MABS V3 oferuje następujące informacje dotyczące ochrony maszyn wirtualnych VMware:

- Obsługa vCenter i ESXi 6.5, wraz z obsługą 5.5 i 6.0.
- Automatyczna ochrona maszyn wirtualnych VMware do chmury. Jeśli nowe maszyny wirtualne VMware zostaną dodane do chronionego folderu, są one automatycznie chronione na dysku i w chmurze.
- Poprawa wydajności odzyskiwania dla odzyskiwania alternatywnych lokalizacji VMware.

## <a name="sql-2017-support"></a>Obsługa programu SQL 2017

Mabs V3 może być zainstalowany z sql 2017 jako bazy danych MABS. Serwer SQL można uaktualnić z programu SQL 2016 do programu SQL 2017 lub zainstalować go świeżo. Można również kopii zapasowej sql 2017 obciążenia zarówno w środowisku klastrowanym i nieklastrowane z MABS V3.

## <a name="windows-server-2019-support"></a>Pomoc techniczna systemu Windows Server 2019

Mabs V3 można zainstalować w systemie Windows Server 2019. Aby użyć mas v3 z WS2019, można uaktualnić system operacyjny do WS2019 przed zainstalowaniem/uaktualnieniem do mabs V3 lub uaktualnić system operacyjny po zainstalowaniu/uaktualnieniu V3 na WS2016.

MABS V3 jest pełną wersją i może być zainstalowany bezpośrednio w systemie Windows Server 2016, Windows Server 2019 lub może zostać uaktualniony z mabs V2. Przed uaktualnieniem do lub zainstalowaniem serwera kopii zapasowej v3 przeczytaj o wymaganiach wstępnych instalacji.
Więcej informacji na temat kroków instalacji/uaktualnienia dla mabs [można znaleźć tutaj](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).

> [!NOTE]
>
> MABS ma taką samą bazę kodu jak System Center Data Protection Manager. MABS v3 jest odpowiednikiem Data Protection Manager 1807.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak przygotować serwer lub rozpocząć ochronę obciążenia:

- [Znane problemy w mabs V3](backup-mabs-release-notes-v3.md)
- [Przygotowywanie obciążeń serwera kopii zapasowych](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej serwera VMware za pomocą serwera kopii zapasowych](backup-azure-backup-server-vmware.md)
- [Tworzenie kopii zapasowej programu SQL Server za pomocą serwera kopii zapasowych](backup-azure-sql-mabs.md)
- [Korzystanie z nowoczesnego magazynu kopii zapasowych z serwerem kopii zapasowych](backup-mabs-add-storage.md)

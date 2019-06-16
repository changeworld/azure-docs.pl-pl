---
title: Kopia zapasowa i przywracanie programu SQL Server na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano zagadnienia i przywracania kopii zapasowych baz danych SQL Server uruchomiony na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: ab239d0546508d74874c6b6be03f6afc06b08fa7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60563432"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Tworzenie i przywracanie kopii zapasowych programu SQL Server na maszynach wirtualnych platformy Azure

Ten artykuł zawiera wskazówki na temat tworzenia kopii zapasowych i przywracania opcji dostępnych dla programu SQL Server działa na maszynie wirtualnej Windows na platformie Azure. Usługa Azure Storage przechowuje trzy kopie każdego dysku maszyny Wirtualnej platformy Azure, co gwarantuje ochronę przed utratą danych lub uszkodzenie danych fizycznych. W związku z tym w przeciwieństwie do środowiska lokalnego, nie należy skoncentrować się na awarie sprzętowe. Jednak użytkownik powinien nadal tworzyć kopie zapasowe baz danych programu SQL Server do ochrony przed błędy aplikacji lub użytkownika, takie jak liczba operacji wstawienia danych przypadkowego lub usunięcia. W takiej sytuacji należy mieć możliwość przywracania do określonego punktu w czasie.

Pierwsza część tego artykułu zawiera omówienie dostępnych kopii zapasowych i opcje przywracania. Następuje to sekcje, które zawierają więcej informacji o poszczególnych strategii.

## <a name="backup-and-restore-options"></a>Opcje tworzenia kopii zapasowych i przywracania

Poniższa tabela zawiera informacje na temat różnych opcji tworzenia kopii zapasowych i przywracania dla programu SQL Server, działające na maszynach wirtualnych platformy Azure:

| Strategia | Wersje programu SQL | Opis |
|---|---|---|
| [Automatyczne kopie zapasowe](#automated) | 2014<br/> 2016<br/> 2017 | Zautomatyzowane tworzenie kopii zapasowej umożliwia planowanie regularnych kopii zapasowych dla wszystkich baz danych na maszynę Wirtualną programu SQL Server. Kopie zapasowe są przechowywane w magazynie platformy Azure przez maksymalnie 30 dni. Począwszy od programu SQL Server 2016, automatyczne kopie zapasowe w wersji 2 oferuje dodatkowe opcje, takie jak konfigurowanie ręcznego planowania oraz częstotliwości pełnego i kopie zapasowe dziennika. |
| [Usługa Azure Backup dla maszyn wirtualnych SQL](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Usługa Azure Backup zapewnia możliwości tworzenia kopii zapasowej klasy przedsiębiorstwa dla programu SQL Server uruchamianych na maszynach wirtualnych platformy Azure. Ta usługa umożliwia centralne zarządzanie kopie zapasowe wielu serwerów i tysiącami baz danych. Bazy danych można przywrócić do określonego punktu w czasie w portalu. Oferuje ona zasady przechowywania można dostosowywać, które mogą zachowywać kopie zapasowe lata. Ta funkcja jest obecnie w publicznej wersji zapoznawczej. |
| [Ręczna kopia zapasowa](#manual) | Wszyscy | W zależności od używanej wersji programu SQL Server istnieją różne techniki ręcznie i przywracania kopii zapasowych programu SQL Server uruchomionego na Maszynie wirtualnej platformy Azure. W tym scenariuszu jesteś odpowiedzialny za jak baz danych kopie zapasowe są tworzone i lokalizacji magazynu oraz zarządzania te kopie zapasowe. |

W poniższych sekcjach opisano każdą opcję bardziej szczegółowo. Ostatnia część ten artykuł zawiera podsumowanie w postaci tabeli funkcji.

## <a id="automated"></a> Zautomatyzowane tworzenie kopii zapasowej

Zautomatyzowane tworzenie kopii zapasowej umożliwia automatyczne usługi kopii zapasowej, w przypadku wersji SQL Server Standard i Enterprise działające w maszyny Wirtualnej z systemem Windows na platformie Azure. Ta usługa jest świadczona przez [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md), który jest automatycznie instalowany na obrazy maszyn wirtualnych SQL, Windows Server w witrynie Azure portal.

Wszystkie bazy danych kopię zapasową na konto usługi Azure storage, które można skonfigurować. Kopie zapasowe mogą być szyfrowane i przechowywane przez maksymalnie 30 dni.

SQL Server 2016 i wyższych maszyny wirtualne oferują dodatkowe opcje dostosowywania, dzięki zautomatyzowanej kopii zapasowej w wersji 2. Te ulepszenia obejmują:

- Kopie zapasowe bazy danych systemu
- Ręczne harmonogram tworzenia kopii zapasowych i przedziału czasu
- Pełne i plików dziennika częstotliwość wykonywania kopii zapasowych

Aby przywrócić bazę danych, możesz zlokalizować wymagane pliki kopii zapasowych w ramach konta magazynu i wykonaj operację przywracania na maszynie Wirtualnej SQL przy użyciu programu SQL Server Management Studio (SSMS) lub polecenia języka Transact-SQL.

Aby uzyskać więcej informacji o tym, jak skonfigurować automatyczne tworzenie kopii zapasowej maszyn wirtualnych SQL zobacz jeden z następujących artykułów:

- **SQL Server 2016/2017**: [Automatyczne v2 kopii zapasowych maszyn wirtualnych platformy Azure](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Zautomatyzowane tworzenie kopii zapasowej dla maszyn wirtualnych SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Usługa Azure Backup dla maszyn wirtualnych SQL

[Usługa Azure Backup](/azure/backup/) zapewnia możliwości tworzenia kopii zapasowej klasy Enterprise programu SQL Server uruchomionego na maszynach wirtualnych Azure. Wszystkie kopie zapasowe są przechowywane i zarządzane w magazynie usługi Recovery Services. Istnieje kilka dodatkowych korzyści, udostępnianych przez to rozwiązanie, szczególnie w przypadku przedsiębiorstw:

- **Kopia zapasowa bez żadnej infrastruktury**: Nie trzeba zarządzać serwerami kopii zapasowej lub lokalizacji magazynu.
- **Skala**: Możesz chronić wiele maszyn wirtualnych SQL i tysiącami baz danych.
- **Płatność za rzeczywiste użycie**: Ta funkcja jest oddzielną usługą, dostarczone przez usługę Azure Backup, ale co w przypadku wszystkich usług platformy Azure, płacisz tylko to, czego używasz.
- **Centralne zarządzanie i monitorowanie**: Można centralnie zarządzać wszystkie kopie zapasowe, w tym innych obciążeń, które obsługuje usługę Azure Backup, z poziomu jednego pulpitu nawigacyjnego na platformie Azure.
- **Zasady na podstawie kopii zapasowej i przechowywania**: Utwórz standardowe zasady tworzenia kopii zapasowych regularnego wykonywania kopii zapasowych. Ustanów zasady przechowywania, do obsługi kopii zapasowych przez lata.
- **Obsługa SQL zawsze na**: Wykrywanie i chronić konfiguracji programu SQL Server Always On przestrzegać preferencji tworzenia kopii zapasowej grupy dostępności kopii zapasowych.
- **Cel punktu odzyskiwania 15-minutowy (RPO)** : Skonfiguruj kopie zapasowe dziennika transakcji SQL nawet co 15 minut.
- **Punkt w czasie**: Korzystanie z portalu, aby odzyskać bazy danych do określonego punktu w czasie, bez konieczności ręcznego przywrócenia wielu pełnej, różnicowej i kopie zapasowe dzienników.
- **Skonsolidowane alerty e-mail dotyczące błędów**: Skonfiguruj powiadomienia e-mail skonsolidowane pod kątem awarii.
- **Kontrola dostępu oparta na rolach**: Określ, kto może Zarządzanie kopiami zapasowymi i przywracanie operacji za pośrednictwem portalu.

Aby uzyskać szybki przegląd sposobu działania wraz z wersji demonstracyjnej Obejrzyj poniższy film wideo:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

To rozwiązanie Azure Backup dla maszyn wirtualnych SQL jest ogólnie dostępna. Aby uzyskać więcej informacji, zobacz [Utwórz kopię zapasową bazy danych SQL Server na platformie Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Ręczna kopia zapasowa

Jeśli chcesz ręcznie Zarządzanie kopiami zapasowymi i wykorzystujące operacje na maszynach wirtualnych programu SQL, istnieje kilka opcji, w zależności od wersji programu SQL Server, którego używasz. Omówienie tworzenia kopii zapasowych i przywracania zobacz jeden z następujących artykułów, w zależności od wersji programu SQL Server:

- [Kopia zapasowa i przywracanie dla programu SQL Server 2016 i nowszych wersji](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Kopia zapasowa i przywracanie programu SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Kopia zapasowa i przywracanie programu SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Kopia zapasowa i przywracanie programu SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Kopia zapasowa i przywracanie programu SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

W poniższych sekcjach opisano kilka ręcznego tworzenia kopii zapasowej i przywracanie opcji bardziej szczegółowo.

### <a name="backup-to-attached-disks"></a>Tworzenie kopii zapasowej na dołączonych dysków

Dla serwera SQL uruchomionego na maszynach wirtualnych platformy Azure możesz używać natywnego wykonywania kopii zapasowych i przywracania technik dla miejsca docelowego kopii zapasowej plików przy użyciu dołączonych dysków na maszynie Wirtualnej. Jednak istnieje ograniczenie dotyczące liczby dysków, możesz dołączyć do maszyny wirtualnej platformy Azure, na podstawie [rozmiar maszyny wirtualnej](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Istnieje również obciążenie Zarządzanie dyskami, aby wziąć pod uwagę.

Na przykład jak ręcznie utworzyć pełną kopię zapasową bazy przy użyciu programu SQL Server Management Studio (SSMS) lub języka Transact-SQL zobacz [Utwórz pełną bazę danych kopii zapasowej](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Tworzenie kopii zapasowej na adres URL

Począwszy od pakietu CU2 programu SQL Server 2012 z dodatkiem SP1, można tworzenie kopii zapasowej i przywracania bezpośrednio do magazynu obiektów Blob platformy Azure firmy Microsoft, który jest również znane jako kopii zapasowej do adresu URL. SQL Server 2016 wprowadzono również następujące ulepszenia dla tej funkcji:

| Rozszerzenie 2016 | Szczegóły |
| --- | --- |
| **Rozkładanie** |Podczas tworzenia kopii zapasowej w usłudze Microsoft Azure blob storage, SQL Server 2016 obsługuje tworzenie kopii zapasowych wielu obiektów blob, aby umożliwić tworzenie kopii zapasowych dużych baz danych maksymalnie do 12,8 TB. |
| **Kopii zapasowej migawki** |Za pomocą platformy Azure migawki kopii zapasowej migawki pliku programu SQL Server zapewnia niemal natychmiastowych kopii zapasowych oraz szybkie przywracanie plików bazy danych przechowywane przy użyciu usługi Azure Blob storage. Ta funkcja umożliwia uproszczenie tworzenia kopii zapasowej i przywracania zasady. Kopii zapasowych migawki plików obsługuje również punktu w czasie. Aby uzyskać więcej informacji, zobacz [kopii zapasowych migawki plików baz danych na platformie Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Aby uzyskać więcej informacji zobacz jeden z następujących artykułów, w zależności od wersji programu SQL Server:

- **SQL Server 2016/2017**: [Kopię zapasową serwera SQL do adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 z kopii zapasowej do adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012 z kopii zapasowej do adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Tworzenie zarządzanej kopii zapasowej

Począwszy od programu SQL Server 2014, zarządzanej kopii zapasowej umożliwia zautomatyzowanie tworzenie kopii zapasowych do magazynu platformy Azure. Za kulisami zarządzanej kopii zapasowej sprawia, że korzystanie z kopii zapasowej do adresu URL funkcji opisanych w poprzedniej sekcji tego artykułu. Tworzenie zarządzanej kopii zapasowej jest również funkcja podstawowej, która obsługuje usługę automatyczne kopie zapasowe programu SQL Server VM.

Począwszy od programu SQL Server 2016, zarządzana kopia zapasowa stało się dodatkowe opcje związane z planowaniem, bazy danych systemu tworzenia kopii zapasowej i pełnej i częstotliwość wykonywania kopii zapasowych dziennika.

Aby uzyskać więcej informacji zobacz jeden z następujących artykułów, w zależności od wersji programu SQL Server:

- [Zarządzanych kopii zapasowych w systemie Microsoft Azure dla programu SQL Server 2016 i nowsze](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Tworzenie zarządzanej kopii zapasowej na platformie Microsoft Azure dla programu SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Tabela decyzji

Poniższa tabela zawiera podsumowanie możliwości poszczególnych opcji tworzenia kopii zapasowych i przywracanie maszyn wirtualnych programu SQL Server na platformie Azure.

|| **Automatyczne kopie zapasowe** | **Usługa Azure Backup dla programu SQL** | **Ręczna kopia zapasowa** |
|---|---|---|---|
| Wymaga dodatkowych usług platformy Azure |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konfigurowanie zasad tworzenia kopii zapasowych w witrynie Azure portal | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Przywracanie baz danych w witrynie Azure portal |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Zarządzanie wieloma serwerami na jednym pulpicie nawigacyjnym |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Przywracanie do określonego momentu | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Cel punktu odzyskiwania 15-minutowy (RPO) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Zasady przechowywania kopii zapasowych krótkoterminowych (dni) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Zasady długoterminowego przechowywania kopii zapasowych (miesiące, lata) |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Wbudowana obsługa programu SQL Server Always On |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Tworzenie kopii zapasowej na kontach magazynu Azure | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatyczny) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatyczny) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(zarządzaną przez klienta) |
| Zarządzanie plikami magazynu i kopii zapasowych | | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Tworzenie kopii zapasowej na dołączonych dysków na maszynie Wirtualnej |   |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Centralnej można dostosować raporty kopii zapasowych |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Skonsolidowane wiadomości e-mail dla alertów błędów |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Dostosowywanie monitorowania, w oparciu o dzienniki usługi Azure Monitor |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Monitorowanie zadań tworzenia kopii zapasowej za pomocą skryptów programu SSMS lub języka Transact-SQL | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Przywracanie baz danych za pomocą skryptów programu SSMS lub języka Transact-SQL | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Kolejne kroki

Jeśli planowane jest wdrożenie programu SQL Server na Maszynie wirtualnej platformy Azure, inicjowania obsługi administracyjnej wskazówki można znaleźć w poniższym przewodniku: [Jak aprowizować maszynę wirtualną Windows SQL Server w witrynie Azure portal](virtual-machines-windows-portal-sql-server-provision.md).

Kopia zapasowa i przywracanie można przeprowadzić migrację danych, istnieją potencjalnie łatwiejsze ścieżek migracji danych do programu SQL Server na Maszynie wirtualnej platformy Azure. Aby uzyskać pełne omówienie opcji migracji i zalecenia, zobacz [migracji bazy danych do programu SQL Server na Maszynie wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md).

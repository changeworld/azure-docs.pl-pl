---
title: Tworzenie kopii zapasowych i przywracanie dla programu SQL Server na maszynach wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano zagadnienia dotyczące tworzenia kopii zapasowych i przywracania baz danych programu SQL Server uruchomionych na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: e4c126bbac73accb984f1040a7fea1740d919233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249778"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Tworzenie kopii zapasowych i przywracanie dla programu SQL Server w maszynach wirtualnych platformy Azure

Ten artykuł zawiera wskazówki dotyczące opcji tworzenia kopii zapasowych i przywracania dostępnych dla programu SQL Server działającego na maszynie wirtualnej systemu Windows na platformie Azure. Usługa Azure Storage przechowuje trzy kopie każdego dysku maszyny Wirtualnej platformy Azure, aby zagwarantować ochronę przed utratą danych lub uszkodzeniem danych fizycznych. W związku z tym, w przeciwieństwie do lokalnych, nie trzeba skupić się na awariach sprzętu. Jednak nadal należy wykonać kopię zapasową baz danych programu SQL Server, aby chronić przed błędami aplikacji lub użytkownika, takimi jak przypadkowe wstawienia danych lub usunięcia. W tej sytuacji ważne jest, aby móc przywrócić do określonego punktu w czasie.

Pierwsza część tego artykułu zawiera omówienie dostępnych opcji tworzenia kopii zapasowych i przywracania. Po tym następuje sekcje, które zawierają więcej informacji na temat każdej strategii.

## <a name="backup-and-restore-options"></a>Opcje tworzenia kopii zapasowych i przywracania

Poniższa tabela zawiera informacje na temat różnych opcji tworzenia kopii zapasowych i przywracania dla programu SQL Server uruchomionych na maszynach wirtualnych platformy Azure:

| Strategia | Wersje SQL | Opis |
|---|---|---|
| [Automatyczna kopia zapasowa](#automated) | 2014<br/> 2016<br/> 2017 | Automatyczna kopia zapasowa umożliwia planowanie regularnych kopii zapasowych dla wszystkich baz danych na maszynie Wirtualnej programu SQL Server. Kopie zapasowe są przechowywane w magazynie platformy Azure przez maksymalnie 30 dni. Począwszy od programu SQL Server 2016, automatyczna kopia zapasowa w wersji 2 oferuje dodatkowe opcje, takie jak konfigurowanie ręcznego planowania i częstotliwości tworzenia kopii zapasowych w całości i dziennika. |
| [Usługa Azure Backup dla maszyn wirtualnych SQL](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | Usługa Azure Backup zapewnia możliwość tworzenia kopii zapasowych klasy Enterprise dla programu SQL Server działającego na maszynach wirtualnych platformy Azure. Dzięki tej usłudze można centralnie zarządzać kopiami zapasowymi dla wielu serwerów i tysięcy baz danych. Bazy danych można przywrócić do określonego punktu w czasie w portalu. Oferuje konfigurowalne zasady przechowywania, które mogą obsługiwać kopie zapasowe przez lata. |
| [Ręczna kopia zapasowa](#manual) | Wszystkie | W zależności od wersji programu SQL Server istnieją różne techniki ręcznego tworzenia kopii zapasowych i przywracania programu SQL Server uruchomionego na maszynie Wirtualnej platformy Azure. W tym scenariuszu jesteś odpowiedzialny za sposób tworzenia kopii zapasowych baz danych i lokalizacji magazynu i zarządzania tymi kopiami zapasowymi. |

W poniższych sekcjach opisano każdą opcję bardziej szczegółowo. Ostatnia sekcja tego artykułu zawiera podsumowanie w postaci macierzy funkcji.

## <a name="automated-backup"></a><a id="automated"></a>Automatyczna kopia zapasowa

Automatyczna kopia zapasowa zapewnia usługę automatycznego tworzenia kopii zapasowych dla wersji SQL Server Standard i Enterprise działających na maszynie Wirtualnej systemu Windows na platformie Azure. Ta usługa jest dostarczana przez [rozszerzenie agenta IaaS programu SQL Server,](virtual-machines-windows-sql-server-agent-extension.md)które jest automatycznie instalowane na obrazach maszyn wirtualnych systemu SQL Server windows w portalu Azure.

Wszystkie bazy danych są archiwizowane na skonfigurowanym koncie magazynu platformy Azure. Kopie zapasowe mogą być szyfrowane i przechowywane przez maksymalnie 30 dni.

Sql Server 2016 i wyższe maszyny wirtualne oferują więcej opcji dostosowywania z automatyczną kopią zapasową w wersji 2. Te ulepszenia są następujące:

- Kopie zapasowe bazy danych systemu
- Ręczny harmonogram tworzenia kopii zapasowych i przedział czasu
- Częstotliwość tworzenia kopii zapasowych plików pełnych i dziennika

Aby przywrócić bazę danych, należy zlokalizować wymagane pliki kopii zapasowej na koncie magazynu i wykonać przywracanie na maszynie wirtualnej SQL przy użyciu poleceń SQL Server Management Studio (SSMS) lub Transact-SQL.

Aby uzyskać więcej informacji na temat konfigurowania automatycznej kopii zapasowej dla maszyn wirtualnych SQL, zobacz jeden z następujących artykułów:

- **SQL Server 2016/2017**: [Automatyczna kopia zapasowa w wersji 2 dla maszyn wirtualnych platformy Azure](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Automatyczna kopia zapasowa dla maszyn wirtualnych programu SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a>Usługa Azure Backup dla maszyn wirtualnych SQL

[Usługa Azure Backup](/azure/backup/) zapewnia możliwość tworzenia kopii zapasowych klasy Enterprise dla programu SQL Server działającego na maszynach wirtualnych platformy Azure. Wszystkie kopie zapasowe są przechowywane i zarządzane w magazynie usług odzyskiwania. Istnieje kilka zalet, które zapewnia to rozwiązanie, szczególnie dla przedsiębiorstw:

- **Tworzenie kopii zapasowych z zerową infrastrukturą:** Nie trzeba zarządzać serwerami kopii zapasowych ani lokalizacjami pamięci masowej.
- **Skala:** Chroń wiele maszyn wirtualnych SQL i tysiące baz danych.
- **Płatność zgodnie z rzeczywistym użyciem:** Ta funkcja jest oddzielną usługą świadczoną przez usługę Azure Backup, ale podobnie jak w przypadku wszystkich usług platformy Azure, płacisz tylko za to, czego używasz.
- **Centralne zarządzanie i monitorowanie:** Centralne zarządzanie wszystkimi kopiami zapasowymi, w tym innymi obciążeniami, które obsługuje usługa Azure Backup, z jednego pulpitu nawigacyjnego na platformie Azure.
- Tworzenie standardowych zasad **tworzenia kopii zapasowych i przechowywania opartych**na zasadach: Tworzenie standardowych zasad tworzenia kopii zapasowych dla zwykłych kopii zapasowych. Ustanawiaj zasady przechowywania w celu obsługi kopii zapasowych przez lata.
- **Obsługa sql zawsze włączone:** Wykrywanie i ochrona konfiguracji programu SQL Server Always On i honorowanie preferencji tworzenia kopii zapasowej grupy kopii zapasowych w grupie.
- **15-minutowy cel punktu odzyskiwania (RPO)**: Konfigurowanie kopii zapasowych dziennika transakcji SQL do co 15 minut.
- **Przywracanie punktu w czasie:** Użyj portalu, aby odzyskać bazy danych do określonego punktu w czasie bez konieczności ręcznego przywracania wielu pełnych, różnicowych i dziennika kopii zapasowych.
- **Skonsolidowane alerty e-mail dotyczące błędów:** Konfigurowanie skonsolidowanych powiadomień e-mail dla wszystkich błędów.
- **Kontrola dostępu oparta na rolach:** Określ, kto może zarządzać operacjami tworzenia kopii zapasowych i przywracania za pośrednictwem portalu.

Aby uzyskać szybki przegląd tego, jak działa wraz z prezentacją, obejrzyj następujący film:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

To rozwiązanie usługi Azure Backup dla maszyn wirtualnych SQL jest ogólnie dostępne. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowej bazy danych programu SQL Server na platformie Azure](../../../backup/backup-azure-sql-database.md).

## <a name="manual-backup"></a><a id="manual"></a>Ręczna kopia zapasowa

Jeśli chcesz ręcznie zarządzać operacjami tworzenia kopii zapasowych i przywracania na maszynach wirtualnych SQL, istnieje kilka opcji w zależności od używanej wersji programu SQL Server. Aby uzyskać omówienie tworzenia kopii zapasowych i przywracania, zobacz jeden z następujących artykułów opartych na wersji programu SQL Server:

- [Tworzenie kopii zapasowych i przywracanie dla programu SQL Server 2016 i nowszych](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Tworzenie kopii zapasowych i przywracanie dla programu SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Tworzenie kopii zapasowych i przywracanie dla programu SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Tworzenie kopii zapasowych i przywracanie dla programu SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Tworzenie kopii zapasowych i przywracanie dla programu SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

W poniższych sekcjach opisano kilka opcji ręcznej kopii zapasowej i przywracania bardziej szczegółowo.

### <a name="backup-to-attached-disks"></a>Tworzenie kopii zapasowej na podłączonych dyskach

W przypadku programu SQL Server uruchomionego na maszynach wirtualnych platformy Azure można użyć natywnych technik tworzenia kopii zapasowych i przywracania przy użyciu dołączonych dysków na maszynie wirtualnej dla miejsca docelowego plików kopii zapasowej. Istnieje jednak ograniczenie liczby dysków, które można dołączyć do maszyny wirtualnej platformy Azure, w zależności [od rozmiaru maszyny wirtualnej.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Istnieje również obciążenie związane z zarządzaniem dyskami do rozważenia.

Na przykład sposób ręcznego tworzenia pełnej kopii zapasowej bazy danych przy użyciu programu SQL Server Management Studio (SSMS) lub Transact-SQL zobacz [Tworzenie pełnej kopii zapasowej bazy danych](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Kopia zapasowa do adresu URL

Począwszy od SQL Server 2012 SP1 CU2, można wykonać kopię zapasową i przywrócić bezpośrednio do magazynu obiektów Blob platformy Microsoft Azure, który jest również nazywany kopią zapasową do adresu URL. W programie SQL Server 2016 wprowadzono również następujące ulepszenia dla tej funkcji:

| Ulepszenie 2016 | Szczegóły |
| --- | --- |
| **Rozkładanie** |Podczas tworzenia kopii zapasowej magazynu obiektów blob platformy Microsoft Azure program SQL Server 2016 obsługuje tworzenie kopii zapasowych w wielu obiektach blob, aby umożliwić tworzenie kopii zapasowych dużych baz danych o maksymalnej pojemności 12,8 TB. |
| **Kopia zapasowa migawki** |Dzięki zastosowaniu migawek platformy Azure kopia zapasowa sql server file-snapshot zapewnia niemal natychmiastowe kopie zapasowe i szybkie przywracanie plików bazy danych przechowywanych przy użyciu usługi magazynu obiektów Blob platformy Azure. Ta funkcja umożliwia uproszczenie zasad tworzenia kopii zapasowych i przywracania. Kopia zapasowa migawki pliku obsługuje również przywracanie punktu w czasie. Aby uzyskać więcej informacji, zobacz [Migawka kopii zapasowych plików bazy danych na platformie Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Aby uzyskać więcej informacji, zobacz jeden z następujących artykułów na podstawie wersji programu SQL Server:

- **SQL Server 2016/2017**: [Kopia zapasowa programu SQL Server do adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [Kopia zapasowa programu SQL Server 2014 do adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [Kopia zapasowa programu SQL Server 2012 do adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Zarządzana kopia zapasowa

Począwszy od programu SQL Server 2014, zarządzana kopia zapasowa automatyzuje tworzenie kopii zapasowych w magazynie platformy Azure. W tle zarządzana kopia zapasowa korzysta z funkcji Kopia zapasowa do adresu URL opisana w poprzedniej sekcji tego artykułu. Zarządzana kopia zapasowa jest również podstawową funkcją, która obsługuje usługę automatycznej kopii zapasowej maszyn wirtualnych programu SQL Server.

Począwszy od programu SQL Server 2016, zarządzana kopia zapasowa uzyskała dodatkowe opcje planowania, tworzenia kopii zapasowych bazy danych systemu oraz częstotliwości tworzenia kopii zapasowych w całości i dziennika.

Aby uzyskać więcej informacji, zobacz jeden z następujących artykułów opartych na wersji programu SQL Server:

- [Zarządzana kopia zapasowa na platformie Microsoft Azure dla programu SQL Server 2016 i nowszych](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Zarządzana kopia zapasowa na platformie Microsoft Azure dla programu SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Matryca decyzyjna

W poniższej tabeli podsumowano możliwości każdej opcji tworzenia kopii zapasowej i przywracania dla maszyn wirtualnych programu SQL Server na platformie Azure.

|| **Automatyczna kopia zapasowa** | **Usługa Azure Backup dla języka SQL** | **Ręczna kopia zapasowa** |
|---|---|---|---|
| Wymaga dodatkowej usługi platformy Azure |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konfigurowanie zasad tworzenia kopii zapasowych w witrynie Azure portal | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Przywracanie baz danych w witrynie Azure portal |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Zarządzanie wieloma serwerami na jednym pulpicie nawigacyjnym |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Przywracanie do określonego momentu | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15-minutowy cel punktu odzyskiwania (RPO) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Krótkoterminowe zasady przechowywania kopii zapasowych (dni) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Długoterminowa polityka przechowywania kopii zapasowych (miesiące, lata) |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Wbudowana obsługa programu SQL Server zawsze wł., |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Tworzenie kopii zapasowych na kontach usługi Azure Storage | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatycznie) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatycznie) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(zarządzane przez klienta) |
| Zarządzanie plikami pamięci masowej i kopii zapasowych | | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Tworzenie kopii zapasowej na podłączonych dyskach na maszynie Wirtualnej |   |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Centralne, konfigurowalne raporty kopii zapasowych |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Skonsolidowane alerty e-mail dotyczące błędów |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Dostosowywanie monitorowania na podstawie dzienników usługi Azure Monitor |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Monitorowanie zadań tworzenia kopii zapasowych za pomocą skryptów SSMS lub Transact-SQL | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Przywracanie baz danych za pomocą skryptów SSMS lub Transact-SQL | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Tak](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Następne kroki

Jeśli planujesz wdrożenie programu SQL Server na maszynie Wirtualnej platformy Azure, można znaleźć wskazówki dotyczące inicjowania obsługi administracyjnej w następującym [przewodniku: Jak aprowizować maszynę wirtualną systemu Windows SQL Server w portalu Azure.](virtual-machines-windows-portal-sql-server-provision.md)

Chociaż tworzenie kopii zapasowych i przywracanie może służyć do migracji danych, potencjalnie są łatwiejsze ścieżki migracji danych do programu SQL Server na maszynie Wirtualnej platformy Azure. Aby uzyskać pełne omówienie opcji migracji i zaleceń, zobacz [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md).

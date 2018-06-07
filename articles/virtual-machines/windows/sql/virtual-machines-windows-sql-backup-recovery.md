---
title: Kopia zapasowa i przywracanie programu SQL Server na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera opis zagadnień kopia zapasowa i przywracanie baz danych SQL Server uruchomiony na maszynach wirtualnych platformy Azure.
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
ms.openlocfilehash: 4b90d1b9b2ee64722d3c92bcbd8fa205c9b59ebd
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809611"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Tworzenie i przywracanie kopii zapasowych programu SQL Server na maszynach wirtualnych platformy Azure

Ten artykuł zawiera wskazówki dotyczące kopii zapasowych i przywracania opcje dostępne dla programu SQL Server uruchomiony w maszynach wirtualnych Windows Azure. Magazyn Azure przechowuje trzy kopie każdego dysku maszyny Wirtualnej platformy Azure, aby zagwarantować ochronę przed utratą danych lub uszkodzenie danych fizycznych. W związku z tym w przeciwieństwie do lokalnego, nie trzeba się skupić na awarie sprzętowe. Jednak możesz powinien nadal tworzyć kopie zapasowe baz danych programu SQL Server do ochrony przez błędy aplikacji lub użytkownika, takich jak dane przypadkowej wstawienia lub usunięcia. W takiej sytuacji jest ważne móc przywrócić do określonego punktu w czasie.

Pierwsza część w tym artykule omówiono dostępnych kopii zapasowych oraz opcje przywracania. Następuje to sekcje, które udostępnia dodatkowe informacje o każdej strategii.

## <a name="backup-and-restore-options"></a>Opcje kopii zapasowej i przywracania

Poniższa tabela zawiera informacje na różne opcje tworzenia kopii zapasowej i przywracania dla programu SQL Server uruchomiony na maszynach wirtualnych platformy Azure:

| Strategia | Wersje programu SQL | Opis |
|---|---|---|---|
| [Automatyczne kopie zapasowe](#automated) | 2014 r.<br/> 2016<br/> 2017 | Automatyczne kopie zapasowe umożliwia zaplanowanie regularnych kopii zapasowych dla wszystkich baz danych na maszynie Wirtualnej serwera SQL. Kopie zapasowe są przechowywane w magazynie Azure przez maksymalnie 30 dni. Począwszy od programu SQL Server 2016, v2 automatyczna usługa Backup oferuje dodatkowe opcje, takie jak ręczne planowania i częstotliwości pełnego i kopie zapasowe dziennika. |
| [Kopia zapasowa Azure dla maszyn wirtualnych SQL](#azbackup) | 2012<br/> 2014 r.<br/> 2016<br/> 2017 | Kopia zapasowa Azure zapewnia możliwości tworzenia kopii zapasowej klasy Enterprise programu SQL Server uruchomionych w maszynach wirtualnych platformy Azure. Z tą usługą mogą centralnie zarządzać kopii zapasowych dla wielu serwerów i tysięcy baz danych. Bazy danych można przywrócić do określonego punktu w czasie w portalu. Zapewnia ona zasady przechowywania można dostosowywać, które może przechowywać kopie zapasowe lata. Ta funkcja jest obecnie w wersji zapoznawczej. |
| [Ręczne kopii zapasowej](#manual) | Wszyscy | W zależności od używanej wersji programu SQL Server są różnych technik w celu ręcznego i przywracania kopii zapasowych programu SQL Server uruchomionego na maszynie Wirtualnej platformy Azure. W tym scenariuszu jest odpowiedzialny za jak zapasową baz danych oraz lokalizację magazynu i zarządzania te kopie zapasowe. |

W poniższych sekcjach opisano szczegółowo w każdej z opcji. Ostatnia część ten artykuł zawiera podsumowanie w postaci macierzy funkcji.

## <a id="autoamted"></a> Automatyczne kopie zapasowe

Automatyczne kopie zapasowe zapewnia automatyczne usługi tworzenia kopii zapasowej dla wersji programu SQL Server Standard i Enterprise uruchomione na maszynie Wirtualnej z systemu Windows Azure. Ta usługa jest świadczona przez [rozszerzenia agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md), który jest automatycznie instalowany na obrazy maszyny wirtualnej systemu Windows serwera SQL w portalu Azure.

Wszystkie bazy danych kopię zapasową na konto magazynu Azure, które można skonfigurować. Kopie zapasowe mogą być szyfrowane i przechowywane przez 30 dni.

SQL Server 2016 i wyższe maszyny wirtualne oferują więcej opcji dostosowania v2 automatycznego tworzenia kopii zapasowej. Te ulepszenia obejmują:

- Kopie zapasowe bazy danych systemu
- Ręczne harmonogram tworzenia kopii zapasowych i przedział czasu
- Częstotliwość wykonywania kopii zapasowych pliku dziennika i pełny

Aby przywrócić bazę danych, musi zlokalizować wymagana pliki kopii zapasowej na koncie magazynu i wykonaj operację przywracania na maszynie Wirtualnej SQL przy użyciu poleceń języka Transact-SQL lub SQL Server Management Studio (SSMS).

Aby uzyskać więcej informacji na temat konfigurowania automatycznego tworzenia kopii zapasowej dla maszyn wirtualnych SQL zobacz następujące artykuły:

- **SQL Server 2016/2017**: [automatycznego tworzenia kopii zapasowej w wersji 2 maszyn wirtualnych platformy Azure ](virtual-machines-windows-sql-automated-backup-v2.md)
- **Program SQL Server 2014**: [automatycznego tworzenia kopii zapasowej dla maszyn wirtualnych programu SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Kopia zapasowa Azure dla maszyn wirtualnych SQL (publicznej wersji zapoznawczej)

[Kopia zapasowa Azure](/azure/backup/) zapewnia możliwości tworzenia kopii zapasowej klasy Enterprise programu SQL Server uruchomionego na maszynach wirtualnych Azure. Wszystkie kopie zapasowe są przechowywane i zarządzane w magazynie usług odzyskiwania. Istnieje kilka korzyści, które rozwiązanie to zapewnia, szczególnie dla przedsiębiorstw:

- **Kopia zapasowa infrastruktury zero**: nie masz do zarządzania serwerami kopii zapasowej lub lokalizacji przechowywania.
- **Skala**: ochrona wiele maszyn wirtualnych SQL i tysięcy baz danych.
- **Płatność za rzeczywiste użycie**: Ta funkcja jest osobnym oferowane przez usługi Kopia zapasowa Azure, ale tak jak w przypadku wszystkich usług platformy Azure, płacisz tylko za można użyć.
- **Centralne zarządzanie i monitorowanie**: centralnie zarządzać wszystkich kopii zapasowych, w tym innych obciążeń, które obsługuje usługi Kopia zapasowa Azure, na podstawie pojedynczy pulpit nawigacyjny w systemie Azure.
- **Zasady zmiennych kopii zapasowej i przechowywania**: tworzenie standardowych zasad tworzenia kopii zapasowych regularnego wykonywania kopii zapasowych. Ustanów zasady przechowywania do obsługi kopii zapasowych lata.
- **Obsługa SQL AlwaysOn**: wykrywania i ochrony konfiguracji programu SQL Server AlwaysOn z uwzględnieniem kopii zapasowej preferencji tworzenia kopii zapasowej grupy dostępności.
- **Cel punktu odzyskiwania 15 minut (RPO)**: kopie zapasowe dziennika transakcji SQL skonfigurować maksymalnie co 15 minut.
- **Punkt w czasie przywracania**: można odzyskać bazy danych do określonego punktu w czasie bez konieczności ręcznie przywrócić wiele pełnej, różnicowej, za pomocą portalu i kopii zapasowych dziennika.
- **Skonsolidowane alerty e-mail dotyczące błędów**: Konfigurowanie skonsolidowane powiadomień e-mail o zakończą się niepowodzeniem.
- **Kontrola dostępu oparta na rolach**: ustalić, który można zarządzać kopii zapasowej i przywracanie operacji za pośrednictwem portalu.

Szybkie omówienie jak działa wraz z demonstrację Obejrzyj następujące:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2dNbw]

To rozwiązanie kopia zapasowa Azure dla maszyn wirtualnych SQL jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Utwórz kopię zapasową bazy danych programu SQL Server na platformie Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Ręczne kopii zapasowej

Jeśli chcesz ręcznie zarządzać kopii zapasowej i przywracanie operacji na maszynach wirtualnych programu SQL, dostępnych jest kilka opcji w zależności od wersji programu SQL Server są używane. Omówienie kopii zapasowych i przywracania zobacz następujące artykuły, zależnie od wersji programu SQL Server:

- [Kopia zapasowa i przywracanie programu SQL Server 2016 lub nowszy](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Kopia zapasowa i przywracanie programu SQL Server 2014](https://msdn.microsoft.com/en-us/library/ms187048%28v=sql.120%29.aspx)
- [Kopia zapasowa i przywracanie programu SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Wykonywanie kopii zapasowych i przywracania dla programu SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Kopia zapasowa i przywracanie programu SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

W poniższych sekcjach opisano kilka ręcznego tworzenia kopii zapasowej i przywracanie szczegółowo opcje.

### <a name="backup-to-attached-disks"></a>Wykonywanie kopii zapasowej na dołączonych dysków

Dla programu SQL Server uruchomiony na maszynach wirtualnych Azure można używać natywnego wykonywania kopii zapasowych i przywracania techniki dla miejsca docelowego kopii zapasowej plików za pomocą dołączonych dysków na maszynie Wirtualnej. Jednak istnieje limit liczby dysków, które można dołączyć do maszyny wirtualnej platformy Azure, na podstawie [rozmiar maszyny wirtualnej](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Istnieje również koszty Zarządzanie dyskami, aby wziąć pod uwagę.

Na przykład ręczne tworzenie kopii zapasowej pełnej bazy danych przy użyciu programu SQL Server Management Studio (SSMS) lub języka Transact-SQL, zobacz [Utwórz pełną bazę danych kopii zapasowej](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Wykonywanie kopii zapasowej na adres URL

Począwszy od programu SQL Server 2012 z dodatkiem SP1 CU2 można kopii zapasowej i przywracania bezpośrednio do magazynu obiektów Blob Microsoft Azure, co jest nazywane także kopii zapasowej do adresu URL. SQL Server 2016 również wprowadzono następujące ulepszenia dla tej funkcji:

| Rozszerzenie 2016 | Szczegóły |
| --- | --- |
| **Rozkładanie** |Podczas wykonywania kopii zapasowej do magazynu obiektów blob Microsoft Azure, programu SQL Server 2016 obsługuje tworzenie kopii zapasowych na wiele obiektów blob, aby umożliwić tworzenie kopii zapasowej dużych baz danych, maksymalnie 12,8 TB. |
| **Kopia zapasowa migawki** |Przy użyciu platformy Azure migawki kopii zapasowej migawki pliku programu SQL Server zapewnia niemal natychmiastowe tworzenie kopii zapasowych i szybkiego przywracania plików bazy danych przechowywane przy użyciu usługi magazynu obiektów Blob platformy Azure. Ta funkcja umożliwia uproszczenie kopii zapasowej i przywracanie zasad. Kopia zapasowa migawki pliku obsługuje również punktu w czasie przywracania. Aby uzyskać więcej informacji, zobacz [kopii zapasowych migawki plików bazy danych na platformie Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Aby uzyskać więcej informacji zobacz jedną z następujących artykułów, zależnie od wersji programu SQL Server:

- **SQL Server 2016/2017**: [kopii zapasowej serwera SQL do adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **Program SQL Server 2014**: [kopii zapasowej programu SQL Server 2014 do adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [kopii zapasowej programu SQL Server 2012 do adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Zarządzaną kopię zapasową

Począwszy od programu SQL Server 2014, zarządzanej kopii zapasowej zautomatyzuje tworzenie kopii zapasowych do magazynu Azure. W tle zarządzanej kopii zapasowej korzysta z kopii zapasowej do adresu URL funkcji opisanych w poprzedniej sekcji tego artykułu. Zarządzanej kopii zapasowej jest również funkcja podstawowej, która obsługuje usługę automatycznego kopii zapasowej programu SQL Server maszyny Wirtualnej.

Począwszy od programu SQL Server 2016, zarządzane kopii zapasowej napotkała dodatkowe opcje planowania systemowej bazy danych kopii zapasowej i pełne i częstotliwość wykonywania kopii zapasowych dziennika.

Aby uzyskać więcej informacji zobacz następujące artykuły, zależnie od wersji programu SQL Server:

- [Zarządzane kopia zapasowa Microsoft Azure dla programu SQL Server 2016 lub nowszy](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Zarządzaną kopię zapasową na platformie Microsoft Azure dla programu SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Macierz decyzji

Poniższa tabela zawiera podsumowanie możliwości poszczególnych opcji tworzenia kopii zapasowej i przywracania dla maszyn wirtualnych programu SQL Server na platformie Azure.

|| **Automatyczne kopie zapasowe** | **Kopia zapasowa Azure dla bazy danych SQL** | **Ręczne kopii zapasowej** |
|---|---|---|---|
| Wymaga dodatkowych usług Azure |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Skonfiguruj zasady tworzenia kopii zapasowych w portalu Azure | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Przywracanie bazy danych w portalu Azure |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Zarządzanie wieloma serwerami na jednym pulpicie nawigacyjnym |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Przywracanie do określonego momentu | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Cel punktu odzyskiwania 15 minut (RPO) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Zasady przechowywania kopii zapasowych krótkoterminowych (dni) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Długoterminowe zasad przechowywania kopii zapasowych (miesięcy, lat) |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Wbudowana obsługa programu SQL Server AlwaysOn |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Wykonywanie kopii zapasowej na konta magazynu Azure | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(Automatyczna) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(Automatyczna) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(nabywca zarządzane) |
| Zarządzanie plikami przechowywania i kopii zapasowej | | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Wykonywanie kopii zapasowej na dołączonych dysków na maszynie Wirtualnej |   |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Centralna dostosowania raportów kopii zapasowej |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Alerty e-mail skonsolidowanych błędów |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Dostosuj ustawienia monitorowania, w oparciu o OMS |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Monitorowanie zadań tworzenia kopii zapasowej za pomocą skryptów programu SSMS lub języka Transact-SQL | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Przywracanie bazy danych za pomocą skryptów programu SSMS lub języka Transact-SQL | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Kolejne kroki

W przypadku planowania wdrożenia programu SQL Server w maszynie Wirtualnej platformy Azure, inicjowania obsługi administracyjnej wskazówki można znaleźć w poniższym przewodniku: [udostępnianie maszynę wirtualną systemu Windows programu SQL Server w portalu Azure](virtual-machines-windows-portal-sql-server-provision.md).

Chociaż kopii zapasowej i przywracania może służyć do migracji danych, istnieją potencjalnie łatwiejsze ścieżki migracji danych do programu SQL Server na maszynie Wirtualnej platformy Azure. Pełne omówienie opcji migracji i zalecenia, zobacz [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md).
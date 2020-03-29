---
title: Przenoszenie danych do bazy danych SQL platformy Azure — proces nauki o danych zespołu
description: Przenoszenie danych z plików płaskich (formaty CSV lub TSV) lub z danych przechowywanych w lokalnym programie SQL Server do bazy danych SQL Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722462"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning

W tym artykule opisano opcje przenoszenia danych z plików płaskich (formaty CSV lub TSV) lub z danych przechowywanych w lokalnym programie SQL Server do bazy danych SQL Azure. Te zadania do przenoszenia danych do chmury są częścią procesu nauki o danych zespołu.

W przypadku tematu, który przedstawia opcje przenoszenia danych do lokalnego programu SQL Server for Machine Learning, zobacz [Przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure.](move-sql-server-virtual-machine.md)

W poniższej tabeli podsumowano opcje przenoszenia danych do bazy danych SQL Azure.

| <b>Źródła</b> | <b>MIEJSCE DOCELOWE: Baza danych SQL platformy Azure</b> |
| --- | --- |
| <b>Plik płaski (w formacie CSV lub TSV)</b> |[Zbiorcze wstawianie kwerendy SQL](#bulk-insert-sql-query) |
| <b>Lokalny program SQL Server</b> |1.[Eksport do pliku płaskiego](#export-flat-file)<br> 2. [Kreator migracji bazy danych SQL](#insert-tables-bcp)<br> 3. [Tworzenie kopii zapasowych i przywracanie bazy danych](#db-migration)<br> 4. [Fabryka danych platformy Azure](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Wymagania wstępne
Procedury opisane w tym miejscu wymagają:

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto magazynu platformy Azure**. Do przechowywania danych w tym samouczku używasz konta magazynu platformy Azure. Jeśli nie masz konta magazynu platformy Azure, zobacz artykuł [Tworzenie konta magazynu.](../../storage/common/storage-account-create.md) Po utworzeniu konta magazynu należy uzyskać klucz konta używany do uzyskiwania dostępu do magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
* Dostęp do **bazy danych SQL azure**. Jeśli musisz skonfigurować bazę danych SQL Azure, [wprowadzenie do bazy danych SQL platformy Microsoft Azure](../../sql-database/sql-database-get-started.md) zawiera informacje na temat sposobu aprowizowania nowego wystąpienia bazy danych SQL azure.
* Zainstalowano i skonfigurowano **program Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

**Dane**: Procesy migracji są demonstrowane przy użyciu [zestawu danych TAKSÓWKA NYC](https://chriswhong.com/open-data/foil_nyc_taxi/). Zestaw danych taksówki NYC zawiera informacje o danych podróży i targach i jest dostępny w magazynie obiektów blob platformy Azure: [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). Przykład i opis tych plików znajdują się w [NYC Taxi Trips Dataset Description](sql-walkthrough.md#dataset).

Można dostosować procedury opisane w tym miejscu do zestawu własnych danych lub wykonać kroki opisane przy użyciu zestawu danych taksówki NYC. Aby przekazać zestaw danych taksówki NYC do lokalnej bazy danych programu SQL Server, postępuj zgodnie z procedurą opisaną w [zbiorczej bazie danych importu do bazy danych programu SQL Server](sql-walkthrough.md#dbload). Te instrukcje są dla programu SQL Server na maszynie wirtualnej platformy Azure, ale procedura przekazywania do lokalnego programu SQL Server jest taka sama.

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a>Przenoszenie danych z płaskiego źródła plików do bazy danych SQL usługi Azure
Dane w plikach płaskich (sformatowany w formacie CSV lub TSV) można przenosić do bazy danych SQL Azure przy użyciu zbiorczego wstawiania programu SQL Query.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a>Zbiorcze wstawianie kwerendy SQL
Kroki procedury przy użyciu zbiorczego wstawiania programu SQL Query są podobne do wskazówek dotyczących przenoszenia danych ze źródła pliku płaskiego do programu SQL Server na maszynie Wirtualnej platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Zbiorcze wstawianie programu SQL Query](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="moving-data-from-on-premises-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a>Przenoszenie danych z lokalnego programu SQL Server do bazy danych SQL usługi Azure
Jeśli dane źródłowe są przechowywane w lokalnym programie SQL Server, istnieją różne możliwości przenoszenia danych do bazy danych SQL Azure:

1. [Eksportowanie do pliku płaskiego](#export-flat-file)
2. [Kreator migracji bazy danych SQL](#insert-tables-bcp)
3. [Tworzenie kopii zapasowych i przywracanie bazy danych](#db-migration)
4. [Fabryka danych platformy Azure](#adf)

Kroki dla pierwszych trzech są podobne do tych sekcji w [Przenieś danych do programu SQL Server na maszynie wirtualnej platformy Azure,](move-sql-server-virtual-machine.md) które obejmują te same procedury. Linki do odpowiednich sekcji w tym temacie znajdują się w poniższych instrukcjach.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Eksportowanie do pliku płaskiego
Kroki tego eksportowania do pliku płaskiego są podobne do tych wskazówek, które zostały omówione w [polu Eksportuj do pliku płaskiego](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Kreator migracji bazy danych SQL
Kroki dotyczące korzystania z Kreatora migracji bazy danych SQL są podobne do tych wskazówek, które zostały omówione w [Kreatorze migracji bazy danych SQL](move-sql-server-virtual-machine.md#sql-migration).

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Tworzenie kopii zapasowych i przywracanie bazy danych
Kroki dotyczące korzystania z kopii zapasowej i przywracania bazy danych są podobne do tych wskazówek wymienionych w [programie Kopia zapasowa i przywracanie bazy danych](move-sql-server-virtual-machine.md#sql-backup).

### <a name="azure-data-factory"></a><a name="adf"></a>Fabryka danych platformy Azure
Dowiedz się, jak przenieść dane do bazy danych SQL usługi Azure za pomocą usługi Azure Data Factory (ADF) w tym temacie, [Przenoszenie danych z lokalnego serwera SQL do platformy SQL Azure za pomocą usługi Azure Data Factory.](move-sql-azure-adf.md) W tym temacie pokazano, jak używać usługi ADF do przenoszenia danych z lokalnej bazy danych programu SQL Server do bazy danych SQL Azure za pośrednictwem usługi Azure Blob Storage.

Należy rozważyć użycie usługi ADF, gdy dane muszą być stale migrowane z hybrydowych źródeł lokalnych i chmurowych.  ADF pomaga również, gdy dane wymagają przekształceń lub potrzebuje nowej logiki biznesowej podczas migracji. Usługa ADF umożliwia planowanie i monitorowanie zadań przy użyciu prostych skryptów JSON, które zarządzają przepływem danych okresowo. ADF ma również inne możliwości, takie jak obsługa złożonych operacji.

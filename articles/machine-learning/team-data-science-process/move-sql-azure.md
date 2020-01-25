---
title: Przenoszenie danych do usługi Azure SQL Database — zespołu danych dla celów naukowych
description: Przenoszenie danych z plików prostych (format CSV lub TSV) lub z danych przechowywanych w SQL Server lokalnym do Azure SQL Database.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722462"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning

W tym artykule opisano opcje przeniesienia danych z plików prostych (format CSV lub TSV) lub z danych przechowywanych w SQL Server lokalnym do Azure SQL Database. Te zadania dotyczące przenoszenia danych do chmury są częścią zespołu danych dla celów naukowych.

Temat, który przedstawia opcje przenoszenia danych do lokalnego programu SQL Server dla usługi Machine Learning, można zobaczyć [przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md).

Poniższa tabela podsumowuje Opcje przenoszenia danych do usługi Azure SQL Database.

| <b>ŹRÓDŁO</b> | <b>Miejsce docelowe: Usługi Azure SQL Database</b> |
| --- | --- |
| <b>Plik prosty (pliku CSV lub TSV sformatowane)</b> |[Zapytanie SQL wstawiania zbiorczego](#bulk-insert-sql-query) |
| <b>Na lokalnym serwerze SQL Server</b> |1.[wyeksportować do pliku prostego](#export-flat-file)<br> 2. [Kreator migracji SQL Database](#insert-tables-bcp)<br> 3. [Tworzenie kopii zapasowej i przywracanie bazy danych](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Wymagania wstępne
Procedury opisane w tym miejscu wymagają, że masz:

* **Subskrypcji platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konta usługi Azure storage**. Używasz konta usługi Azure storage do przechowywania danych w ramach tego samouczka. Jeśli nie masz konta usługi Azure storage, zobacz [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) artykułu. Po utworzeniu konta magazynu, należy uzyskać klucz konta, które umożliwiają dostęp do magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
* Dostęp do **usługi Azure SQL Database**. Jeśli musisz skonfigurować usługi Azure SQL Database [wprowadzenie do programu Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) zawiera informacje o udostępnienie nowego wystąpienia usługi Azure SQL Database.
* Zainstalowany i skonfigurowany **programu Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

**Dane**: procesy migracji zostały przedstawione przy użyciu [zestawu danych taksówek NYC](https://chriswhong.com/open-data/foil_nyc_taxi/). Zestaw danych taksówek NYC zawiera informacje na temat danych podróży i targi i jest dostępna w usłudze Azure blob storage: [dane taksówek NYC](https://www.andresmh.com/nyctaxitrips/). Próbki i opisy te pliki znajdują się w [opis zestawu danych podróży taksówek NYC](sql-walkthrough.md#dataset).

Możesz dostosować procedury opisane w tym miejscu do zestawu danych użytkownika lub zgodnie z opisem przy użyciu zestawu danych taksówek NYC postępuj zgodnie z instrukcjami. Aby przekazać zestaw danych taksówek NYC do lokalnej bazy danych programu SQL Server, wykonaj procedury opisane w [zbiorcze importowanie danych do bazy danych serwera SQL](sql-walkthrough.md#dbload). Te instrukcje są przeznaczone dla programu SQL Server na maszynie wirtualnej platformy Azure, ale procedura przekazywania do lokalnego programu SQL Server jest taka sama.

## <a name="file-to-azure-sql-database"></a>Przeniesienie danych z prostego źródła pliku do Azure SQL Database
Dane w plikach prostych (CSV lub TSV) można przenieść do Azure SQL Database przy użyciu zbiorczego zapytania SQL.

### <a name="bulk-insert-sql-query"></a> Zapytanie SQL wstawiania zbiorczego
Kroki procedury korzystającej z zapytania BULK INSERT SQL są podobne do wskazówek dotyczących przeniesienia danych z prostego źródła pliku do SQL Server na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [zapytania SQL wstawiania zbiorczego](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Przeniesienie danych z SQL Server lokalnego do Azure SQL Database
Jeśli dane źródłowe są przechowywane w SQL Server lokalnym, istnieją różne możliwości przeniesienia danych do Azure SQL Database:

1. [Eksportuj do pliku prostego](#export-flat-file)
2. [Kreator migracji bazy danych SQL](#insert-tables-bcp)
3. [Baza danych kopii zapasowej i przywracanie](#db-migration)
4. [Azure Data Factory](#adf)

Kroki dla pierwszych trzech są podobne do tych w sekcjach [Przenieś dane do SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md) , która obejmuje te same procedury. Linki do odpowiednich fragmentów w tym temacie zostały podane w poniższych instrukcjach.

### <a name="export-flat-file"></a>Eksportuj do pliku prostego
Kroki związane z tym eksportowaniem do pliku prostego są podobne do tych wskazówek objętych [eksportowaniem do pliku prostego](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Kreator migracji bazy danych SQL
Kroki opisane w Kreatorze migracji SQL Database są podobne do tych, które opisano w [Kreatorze migracji SQL Database](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Baza danych kopii zapasowej i przywracanie
Procedura tworzenia kopii zapasowej i przywracania bazy danych przypomina te wskazówki wymienione w temacie [Tworzenie kopii zapasowej i przywracanie bazy danych](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Usługi Azure Data Factory
Dowiedz się, jak przenieść dane do Azure SQL Database z Azure Data Factory (ADF) w tym temacie. [Przenieś dane z lokalnego programu SQL Server do usługi SQL Azure za pomocą Azure Data Factory](move-sql-azure-adf.md). W tym temacie pokazano, jak za pomocą funkcji ADF przenieść dane z lokalnej bazy danych SQL Server do Azure SQL Database za pośrednictwem usługi Azure Blob Storage.

Należy rozważyć użycie funkcji ADF, gdy dane wymagają ciągłego migrowania przy użyciu hybrydowych źródeł lokalnych i chmurowych.  Moduł ADF również pomaga, gdy dane wymagają przekształceń lub wymagają nowej logiki biznesowej podczas migracji. ADF umożliwia planowanie i monitorowanie zadań przy użyciu prostych skryptów JSON, które zarządzają przenoszenia danych w regularnych odstępach czasu. ADF ma także inne możliwości, takie jak obsługa złożonych operacji.

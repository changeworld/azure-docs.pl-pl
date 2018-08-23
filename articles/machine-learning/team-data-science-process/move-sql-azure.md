---
title: Przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Tworzenie tabeli SQL i ładowanie danych do tabeli SQL
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2018
ms.author: deguhath
ms.openlocfilehash: d3a7bc59654ace8dbe647b5a780985fa1556f1da
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42061046"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning
W tym temacie opisano opcje przenoszenia danych z plików prostych (w formatach plików CSV lub TSV) albo z danych przechowywanych w na lokalnym serwerze SQL Server do usługi Azure SQL database. Te zadania dotyczące przenoszenia danych do chmury są częścią zespołu danych dla celów naukowych.

Temat, który przedstawia opcje przenoszenia danych do lokalnego programu SQL Server dla usługi Machine Learning, można zobaczyć [przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md).

Następujące **menu** łącza do tematów opisujących sposób pozyskiwania danych w środowiskach docelowych, gdzie dane mogą być przechowywane i przetwarzane podczas procesu do nauki o danych zespołu (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Poniższa tabela podsumowuje Opcje przenoszenia danych do usługi Azure SQL Database.

| <b>ŹRÓDŁO</b> | <b>Miejsce docelowe: Usługi Azure SQL Database</b> |
| --- | --- |
| <b>Plik prosty (pliku CSV lub TSV sformatowane)</b> |[Zapytanie SQL wstawiania zbiorczego](#bulk-insert-sql-query) |
| <b>Na lokalnym serwerze SQL Server</b> |1.[wyeksportować do pliku prostego](#export-flat-file)<br> 2. [Kreator migracji bazy danych SQL](#insert-tables-bcp)<br> 3. [Baza danych kopii zapasowej i przywracanie](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Wymagania wstępne
Procedury opisane w tym miejscu wymagają, że masz:

* **Subskrypcji platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konta usługi Azure storage**. Używasz konta usługi Azure storage do przechowywania danych w ramach tego samouczka. Jeśli nie masz konta usługi Azure storage, zobacz [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md) artykułu. Po utworzeniu konta magazynu, należy uzyskać klucz konta, które umożliwiają dostęp do magazynu. Zobacz [zarządzanie kluczami dostępu do magazynu](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Dostęp do **usługi Azure SQL Database**. Jeśli musisz skonfigurować usługi Azure SQL Database [wprowadzenie do programu Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) zawiera informacje o udostępnienie nowego wystąpienia usługi Azure SQL Database.
* Zainstalowany i skonfigurowany **programu Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

**Dane**: procesy migracji zostały przedstawione przy użyciu [zestawu danych taksówek NYC](http://chriswhong.com/open-data/foil_nyc_taxi/). Zestaw danych taksówek NYC zawiera informacje na temat danych podróży i targi i jest dostępna w usłudze Azure blob storage: [dane taksówek NYC](http://www.andresmh.com/nyctaxitrips/). Próbki i opisy te pliki znajdują się w [opis zestawu danych podróży taksówek NYC](sql-walkthrough.md#dataset).

Możesz dostosować procedury opisane w tym miejscu do zestawu danych użytkownika lub zgodnie z opisem przy użyciu zestawu danych taksówek NYC postępuj zgodnie z instrukcjami. Aby przekazać zestaw danych taksówek NYC do lokalnej bazy danych programu SQL Server, wykonaj procedury opisane w [zbiorcze importowanie danych do bazy danych serwera SQL](sql-walkthrough.md#dbload). Te instrukcje są przeznaczone dla programu SQL Server na maszynie wirtualnej platformy Azure, ale procedura przekazywania do lokalnego programu SQL Server jest taka sama.

## <a name="file-to-azure-sql-database"></a> Przenoszenie danych ze źródła pliku prostego do usługi Azure SQL database
Dane plików prostych (pliku CSV lub TSV sformatowany) mogą być przenoszone do usługi Azure SQL database przy użyciu zapytania SQL wstawiania zbiorczego.

### <a name="bulk-insert-sql-query"></a> Zapytanie SQL wstawiania zbiorczego
Kroki procedury przy użyciu zapytań SQL Wstawianie zbiorcze są podobne do tych opisane w sekcjach przenoszenie danych ze źródła pliku prostego do programu SQL Server na Maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [zapytania SQL wstawiania zbiorczego](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Przenoszenie danych z lokalnego programu SQL Server do usługi Azure SQL database
Jeśli źródło danych jest przechowywana w programie SQL Server w środowisku lokalnym, istnieją różne możliwości przenoszenia danych do usługi Azure SQL database:

1. [Eksportuj do pliku prostego](#export-flat-file)
2. [Kreator migracji bazy danych SQL](#insert-tables-bcp)
3. [Baza danych kopii zapasowej i przywracanie](#db-migration)
4. [Azure Data Factory](#adf)

Pierwsze trzy kroki są bardzo podobne do tych sekcjach [przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md) obejmujących te same procedury. Linki do odpowiednich fragmentów w tym temacie zostały podane w poniższych instrukcjach.

### <a name="export-flat-file"></a>Eksportuj do pliku prostego
Kroki opisane w tym eksportowania do pliku prostego są podobne do tych omówione w [wyeksportować do pliku prostego](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Kreator migracji bazy danych SQL
Kroki dotyczące korzystania z Kreatora migracji bazy danych SQL są podobne do tych objęte [Kreator migracji bazy danych SQL](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Baza danych kopii zapasowej i przywracanie
Kroki dotyczące korzystania z bazy danych kopii zapasowej i przywracania są podobne do tych objęte [bazy danych kopii zapasowej i przywracanie](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Usługi Azure Data Factory
Procedury przenoszenia danych do usługi Azure SQL database za pomocą usługi Azure Data Factory (ADF) znajduje się w temacie [przenoszenie danych z lokalnego programu SQL server do usługi SQL Azure z usługą Azure Data Factory](move-sql-azure-adf.md). W tym temacie pokazano, jak przenieść dane z lokalnej bazy danych programu SQL Server do usługi Azure SQL database za pomocą usługi Azure Blob Storage za pomocą usługi ADF.

Należy rozważyć użycie usługi ADF, gdy dane muszą stale można migrować w scenariuszu hybrydowym, który uzyskuje dostęp do swoich lokalnych i zasobów w chmurze lub w przypadku, gdy dane jest wykonany lub musi być modyfikowany ani logiki biznesowej do niej dodać, gdy migrowane. ADF umożliwia planowanie i monitorowanie zadań przy użyciu prostych skryptów JSON, które zarządzają przenoszenia danych w regularnych odstępach czasu. ADF ma także inne możliwości, takie jak obsługa złożonych operacji.

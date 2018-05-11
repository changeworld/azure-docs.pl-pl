---
title: Przenoszenie danych do bazy danych SQL Azure dla usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Tworzenie tabeli SQL i ładowanie danych do tabeli SQL
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2018
ms.author: deguhath
ms.openlocfilehash: ce349aedc6b733d34ab61eb2e23b378727e01800
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning
W tym temacie opisano opcje przenoszenia danych z plików prostych (w formatach CSV lub TSV) albo z danych przechowywanych w lokalnym serwerze SQL z bazą danych Azure SQL. Te zadania przenoszenie danych w chmurze są częścią procesu nauki danych zespołu.

Aby temacie, który zawiera opcje przenoszenia danych do lokalnego programu SQL Server do uczenia maszynowego, zobacz [przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md).

Następujące **menu** linki do tematów opisujących sposób pozyskiwania danych w środowisku docelowym, gdzie dane mogą być przechowywane i przetwarzane podczas procesu nauki danych zespołu (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

W poniższej tabeli przedstawiono opcje przenoszenia danych do bazy danych SQL Azure.

| <b>ŹRÓDŁO</b> | <b>Miejsce docelowe: Baza danych Azure SQL</b> |
| --- | --- |
| <b>Plik prosty (CSV lub sformatowany TSV)</b> |[Zapytanie SQL wstawiania zbiorczego](#bulk-insert-sql-query) |
| <b>Lokalny serwer SQL</b> |1.[wyeksportować do pliku prostego](#export-flat-file)<br> 2. [Kreator migracji bazy danych SQL](#insert-tables-bcp)<br> 3. [Baza danych kopii zapasowej i przywracanie](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Wymagania wstępne
Czynności opisane w tym miejscu wymagają:

* **Subskrypcji platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto magazynu Azure**. Używasz konta magazynu Azure do przechowywania danych w tym samouczku. Jeśli nie masz konta magazynu platformy Azure, zobacz [Utwórz konto magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account) artykułu. Po utworzeniu konta magazynu, należy uzyskać klucz konto umożliwia dostęp do magazynu. Zobacz [zarządzanie kluczami dostępu do magazynu](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Dostęp do **baza danych Azure SQL**. Jeśli baza danych SQL Azure, należy skonfigurować [wprowadzenie do korzystania z bazy danych SQL Azure Microsoft](../../sql-database/sql-database-get-started.md) zawiera informacje o tym, jak zainicjować nowe wystąpienie bazy danych SQL Azure.
* Zainstalowano i skonfigurowano **programu Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

**Dane**: procesy migracji przedstawiono przy użyciu [dataset taksówki NYC](http://chriswhong.com/open-data/foil_nyc_taxi/). Dataset taksówki NYC zawiera informacje na temat danych podróży i targach i jest dostępny w magazynie obiektów blob platformy Azure: [danych taksówki NYC](http://www.andresmh.com/nyctaxitrips/). Przykładowe i opis te pliki znajdują się w [opis zestawu danych rund taksówki NYC](sql-walkthrough.md#dataset).

Można dostosować procedury opisane w tym miejscu z zestawem danych użytkownika lub wykonaj kroki opisane przy użyciu taksówki NYC zestawu danych. Aby przekazać taksówki NYC zestawu danych do lokalnej bazy danych programu SQL Server, wykonaj procedurę opisaną w [zbiorczego importowania danych do bazy danych serwera SQL](sql-walkthrough.md#dbload). Te instrukcje dotyczą programu SQL Server na maszynie wirtualnej platformy Azure, ale procedura przekazywania do lokalnego serwera SQL jest taka sama.

## <a name="file-to-azure-sql-database"></a> Przenoszenie danych ze źródła pliku prostego do bazy danych Azure SQL
Dane plików prostych (formacie CSV lub TSV) mogą zostać przeniesione do bazy danych Azure SQL za pomocą kwerendy SQL wstawiania zbiorczego.

### <a name="bulk-insert-sql-query"></a> Zapytanie SQL wstawiania zbiorczego
Kroki procedury przy użyciu zapytania SQL wstawiania zbiorczego są podobne do tych opisane w sekcjach przenoszenie danych ze źródła pliku prostego do programu SQL Server na maszynie Wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [zapytania SQL wstawiania zbiorczego](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Przenoszenie danych z lokalnego programu SQL Server z bazą danych Azure SQL
Jeśli źródło danych jest przechowywany w lokalnym serwerze SQL, istnieją różne możliwości przenoszenia danych do bazy danych Azure SQL:

1. [Wyeksportuj do pliku prostego](#export-flat-file)
2. [Kreator migracji bazy danych SQL](#insert-tables-bcp)
3. [Baza danych kopii zapasowej i przywracanie](#db-migration)
4. [Azure Data Factory](#adf)

Pierwsze trzy kroki są bardzo podobne do tych fragmentów w [przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md) obejmujących te same procedury. W poniższych instrukcjach zostały podane linki do odpowiednich fragmentów, w tym temacie.

### <a name="export-flat-file"></a>Wyeksportuj do pliku prostego
Kroki tego eksportowania do pliku prostego są podobne do tych objęte [wyeksportować do pliku prostego](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Kreator migracji bazy danych SQL
Kroki za pomocą Kreatora migracji bazy danych SQL są podobne do tych, których dotyczy [Kreatora migracji bazy danych SQL](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Baza danych kopii zapasowej i przywracanie
W przypadku korzystania z bazy danych kopii zapasowej i przywracania są podobne do tych, których dotyczy [bazy danych kopii zapasowej i przywracania](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Fabryka danych Azure
Procedury przenoszenia danych do bazy danych Azure SQL z fabryki danych Azure (ADF) znajduje się w temacie [przenoszenia danych z lokalnego programu SQL server SQL Azure z fabryką danych Azure](move-sql-azure-adf.md). W tym temacie pokazano, jak przenieść dane z lokalną bazą danych programu SQL Server do bazy danych Azure SQL za pomocą usługi Azure Blob Storage przy użyciu fabryki danych AZURE.

Należy rozważyć użycie ADF podczas dane muszą stale można migrować w scenariuszu hybrydowym, który uzyskuje dostęp do swoich lokalnych i w chmurze zasobów i danych jest nietransakcyjnego lub musi być modyfikowany lub ma do niego dodana gdy migrowane logiki biznesowej. Fabryki danych AZURE umożliwia planowanie i monitorowania zadań przy użyciu prostych skryptów JSON, które zarządzają przepływu danych w regularnych odstępach czasu. ADF ma również innych funkcji, takich jak obsługa złożonych operacji.

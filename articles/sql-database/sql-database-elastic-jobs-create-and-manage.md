---
title: Zarządzanie grupami baz danych Azure SQL | Dokumentacja firmy Microsoft
description: Przeprowadzenie tworzenia i zarządzania nimi elastycznych zadań.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: a95b62ab8f639ad38ee3ac9ace4f30b62bd852bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476072"
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Tworzenie i zarządzanie nimi skalowanych w poziomie usługi Azure SQL Database za pomocą zadań elastycznych (wersja zapoznawcza)


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Zadania elastic Database** uprościć zarządzanie grupami baz danych, wykonując operacje administracyjne, takie jak zmiany schematu, Zarządzanie poświadczeniami, aktualizacje danych referencyjnych, zbierania danych o wydajności lub dane telemetryczne dzierżaw (klientów) Kolekcja. Zadania elastic Database jest obecnie dostępna za pośrednictwem witryny Azure portal i poleceń cmdlet programu PowerShell. Jednakże powierzchnie portalu Azure zmniejszona ograniczone do wykonania dla wszystkich baz danych w funkcji [puli elastycznej](sql-database-elastic-pool.md). Dostęp do dodatkowych funkcji i wykonywania skryptów w grupie baz danych w tym kolekcji niestandardowy lub fragment zestawu (utworzone za pomocą [Biblioteka kliencka Elastic Database](sql-database-elastic-scale-introduction.md)), zobacz [tworzenie i zarządzanie nimi zadania przy użyciu programu PowerShell](sql-database-elastic-jobs-powershell.md). Aby uzyskać więcej informacji o zadaniach, zobacz [Przegląd zadań Elastic Database](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Bezpłatnej wersji próbnej, zobacz [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Pula elastyczna. Zobacz [o elastycznych pulach](sql-database-elastic-pool.md).
* Instalowanie składników usługi zadania elastycznych baz danych. Zobacz [Instalowanie usługi zadania elastycznych baz danych](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Tworzenie zadań
1. Za pomocą [witryny Azure portal](https://portal.azure.com), z istniejącej puli zadania elastycznych baz danych, kliknij przycisk **zadania Utwórz**.
2. Wpisz nazwę użytkownika i hasło administratora bazy danych (utworzona podczas instalacji zadania) dla bazy danych kontroli zadania (Magazyn metadanych dla zadań).
   
    ![Nadaj nazwę zadaniu, wpisz lub Wklej kod i kliknij przycisk Uruchom][1]
3. W **Utwórz zadanie** bloku, wpisz nazwę dla zadania.
4. Wpisz nazwę użytkownika i hasło, aby nawiązać połączenie z docelowymi bazami danych z wystarczającymi uprawnieniami do pomyślnego wykonania skryptów.
5. Wklej lub wpisz skryptu T-SQL.
6. Kliknij przycisk **Zapisz** a następnie kliknij przycisk **Uruchom**.
   
    ![Tworzenie zadań i uruchamianie][5]

## <a name="run-idempotent-jobs"></a>Uruchamianie zadań idempotentne
Jeśli uruchamiasz skrypt zestawu baz danych, należy się upewnić, że skrypt jest idempotentny. Oznacza to, że skryptu musi być można uruchamiać wiele razy, nawet wtedy, gdy go nie powiodła się przed niekompletna. Na przykład, jeśli skrypt zakończy się niepowodzeniem, zadanie zostanie automatycznie ponowione aż do skutku (w granicach, jako ponownych prób logiki po pewnym czasie przestanie ponawianie próby). Sposobem wykonania tych czynności jest użycie klauzuli "Jeśli ISTNIEJE" i Usuń wszystkie znalezione wystąpienia przed utworzeniem nowego obiektu. Poniżej przedstawiono przykład:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Można również użyć klauzuli "Jeśli nie ISTNIEJE" przed utworzeniem nowego wystąpienia:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Ten skrypt następnie aktualizuje tabelę utworzoną wcześniej.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Sprawdzanie stanu zadania
Po rozpoczęciu zadania, można sprawdzić postęp.

1. Na stronie elastycznej puli, kliknij **Zarządzanie zadaniami**.
   
    ![Kliknij pozycję "Zarządzaj zadania"][2]
2. Kliknij nazwę () zadania. **Stan** może być "Ukończone" lub "Nie powiodło się." Szczegóły zadania są wyświetlane (b) za pomocą jego datę i godzinę tworzenia i uruchamiania. Listy (c) poniżej przedstawia postęp skryptu dla każdej bazy danych w puli, dzięki czemu jego szczegóły daty i godziny.
   
    ![Sprawdzanie zakończone zadania][3]

## <a name="checking-failed-jobs"></a>Sprawdzanie zadania zakończone niepowodzeniem
Jeśli zadanie nie powiedzie się, można znaleźć w dzienniku wykonywania. Kliknij nazwę zadania nie powiodło się, aby wyświetlić jego szczegóły.

![Sprawdź zadanie zakończone niepowodzeniem][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png



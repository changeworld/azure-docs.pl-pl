---
title: Jednostki magazynu danych (dwu, cDWUs) w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zalecenia dotyczące wybierania idealne liczbę jednostek magazynu danych (jednostki dwu, cDWUs), aby zoptymalizować cenę i wydajność i jak zmienić liczbę jednostek.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: d20a600951a0fe586e981adf12127072df1b744c
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428014"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Jednostki magazynu danych (dwu) i moc obliczeniową jednostek magazynu danych (cDWUs)

Zalecenia dotyczące wybierania idealne liczbę jednostek magazynu danych (jednostki dwu, cDWUs), aby zoptymalizować cenę i wydajność i jak zmienić liczbę jednostek.

## <a name="what-are-data-warehouse-units"></a>Co to są jednostki magazynu danych

Usługa Azure SQL Data Warehouse Procesora, pamięci i we/wy są połączone w jednostkach obliczeniowych skalowania o nazwie jednostki magazynu danych (dwu). Jednostka DWU reprezentuje abstrakcyjne znormalizowane miara zasoby obliczeniowe i wydajności. Zmiana poziomu usług powoduje zmianę liczby jednostek dwu, które są dostępne w systemie, która z kolei umożliwia dostosowanie wydajności i kosztów, system.

Aby uzyskać większą wydajność można zwiększyć liczbę jednostek magazynu danych. Mniejszą wydajność Zmniejsz liczbę jednostek magazynu danych. Koszty magazynu i mocy obliczeniowej są rozliczane osobno, więc zmiana jednostek magazynu danych nie ma wpływu na koszty magazynowania.

Wydajność w przypadku jednostek magazynu danych opiera się na te metryki obciążenia magazynu danych:

- Jak szybko standardowego zapytania magazynowania danych można przeskanować dużą liczbę wierszy, a następnie wykonaj złożoną agregację. Ta operacja jest intensywnie We/Wy i procesora CPU.
- Jak szybko w magazynie danych umożliwia pobieranie danych z obiektów blob usługi Azure Storage lub Azure Data Lake. Ta operacja jest w sieci i mocy procesora CPU.
- Jak szybko [ `CREATE TABLE AS SELECT` ](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) polecenia T-SQL można skopiować tabelę. Ta operacja wymaga odczytywania danych z magazynu, ich dystrybucję w węzłach urządzenia i ponowne zapisywanie w magazynie. Ta operacja jest procesora CPU, we/wy i intensywnie korzystających z sieci.

Zwiększenie liczby jednostek dwu:

- Liniowo zmiany wydajności systemu pod kątem skanowania, agregacje i instrukcji CTAS
- Zwiększa liczbę czytników i składników zapisywania dla operacji obciążenia funkcji PolyBase
- Zwiększa maksymalną liczbę równoczesnych zapytań oraz liczby gniazd współbieżności.

## <a name="service-level-objective"></a>Cel poziomu usługi

Cel poziomu usług (SLO) jest ustawienie skalowalności, który określa poziom kosztów i wydajności magazynu danych. Poziomy usług dla Gen2 są mierzone w obliczeniowej liczbę jednostek magazynu danych (cDWU), na przykład DW2000c. Poziomy usług Gen1 są mierzone w jednostkach dwu, na przykład DW2000.
  > [!NOTE]
  > Gen2 magazynu danych w usłudze Azure SQL niedawno dodano rozbudowywać możliwości w celu obsługi możliwie jak 100 cDWU warstwy wystąpień obliczeniowych. Obecnie na Gen1 istniejących magazynów danych, wymagające niższym obliczeniowych warstwy teraz przeprowadzić uaktualnienie do Gen2 w regionach, które są obecnie dostępne bez dodatkowych kosztów.  Jeśli w Twoim regionie nie jest jeszcze obsługiwana, nadal można uaktualnić do obsługiwanym regionie. Aby uzyskać więcej informacji, zobacz [uaktualnienie do Gen2](upgrade-to-latest-generation.md).

Języka T-SQL ustawienie SERVICE_OBJECTIVE określa poziom usługi i warstwę wydajności magazynu danych.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Warstwy wydajności i liczbę jednostek magazynu danych

Każda warstwa wydajności używa nieco jednostkę miary dla ich liczbę jednostek magazynu danych. Różnica ta jest uwzględnione na fakturze, jako jednostka skalowania bezpośrednio przekłada się na rozliczanie.

- Magazyny danych Gen1 są mierzone w jednostki magazynu danych (dwu).
- Gen2 — data, magazyny są mierzone w jednostek obliczeniowych magazynu danych (cDWUs).

Zarówno jednostki dwu, jak i cDWUs obsługują skalowanie w usłudze compute w górę lub w dół oraz wstrzymywania obliczeń, gdy nie jest konieczne korzystanie z magazynu danych. Te operacje są wszystkie na żądanie. Gen2 używa lokalnej pamięci podręcznej opartej na dyskach w węzłach obliczeniowych, aby zwiększyć wydajność. Podczas skalowania lub wstrzymać systemu unieważnienia pamięci podręcznej, a więc okres ciepły pamięci podręcznej jest wymagany, zanim optymalna wydajność jest osiągana.  

Jak możesz zwiększyć liczbę jednostek magazynu danych, możesz liniowo coraz więcej zasobów obliczeniowych. Gen2 — zapewnia najlepszą wydajność zapytań i najwyższej skali. Systemy Gen2 upewnij się również większość użycie pamięci podręcznej.

### <a name="capacity-limits"></a>Limity pojemności

Każdy serwer SQL (na przykład myserver.database.windows.net) ma [jednostki transakcji bazy danych (DTU)](../sql-database/sql-database-what-is-a-dtu.md) limit przydziału, który zezwala na określoną liczbę jednostek magazynu danych. Aby uzyskać więcej informacji, zobacz [limitów pojemności zarządzania obciążenia](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Ile jednostek magazynu danych czy muszę mieć

Idealna liczba jednostek magazynu danych zależy od znacznie obciążenie i ilość danych, które zostały załadowane do systemu.

Kroki służące do znajdowania najlepsze jednostek DWU dla obciążenia:

1. Rozpocznij od wybrania mniejszych jednostek DWU.
2. Monitoruj wydajność aplikacji, jak testujesz ładowania danych do systemu, monitorowanie liczby jednostek dwu wybrane w porównaniu do wydajności, których możesz obserwować.
3. Zidentyfikuj wszelkie dodatkowe wymagania okresowe okresów szczytowej aktywności. Obciążeń, które pokazują znaczące osiąga szczytowe użycie i może być konieczne można skalować często koryta w działaniu.

Usługa SQL Data Warehouse to system skalowalnego w poziomie, który można udostępnić ogromne ilości obliczeń i zapytań duże ilości danych. Aby poznać pełnię jej możliwości skalowania, szczególnie w przypadku większej liczby jednostek dwu, zalecamy skalowanie zestawu danych podczas skalowania, aby upewnić się, że masz wystarczającą ilość danych do źródła danych procesorów CPU. W przypadku testowania skalowania, zaleca się przy użyciu co najmniej 1 TB.

> [!NOTE]
>
> Wydajność zapytań tylko zwiększa za pomocą więcej przetwarzania równoległego, gdy praca może zostać podzielony między węzłami obliczeniowymi. Jeśli okaże się, że skalowanie nie ulega zmianie wydajność, może być konieczne dostrojenie układ tabeli i/lub zapytań. Aby uzyskać wskazówki dotyczące dostrajania zapytania, zobacz [zarządzać zapytaniami użytkownika](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Uprawnienia

Zmiana jednostki magazynu danych wymaga uprawnienia opisane w [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

Wbudowane role zasobów platformy Azure, np. Współautor bazy danych SQL i współautor serwera SQL Server można zmienić ustawień jednostek DWU.

## <a name="view-current-dwu-settings"></a>Wyświetlanie bieżących ustawień jednostek DWU

Aby wyświetlić bieżące ustawienie jednostek DWU:

1. Otwórz Eksplorator obiektów SQL Server w programie Visual Studio.
2. Połącz się z główną bazą danych skojarzonych z serwera logicznego bazy danych SQL.
3. Wybierz z dynamicznego widoku zarządzania sys.database_service_objectives. Oto przykład:

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Zmienić liczbę jednostek magazynu danych

### <a name="azure-portal"></a>Azure Portal

Aby zmienić liczbę jednostek dwu lub cDWUs:

1. Otwórz [witryny Azure portal](https://portal.azure.com), Otwórz swoją bazę danych i kliknij przycisk **skalowania**.

2. W obszarze **skalowania**, przesuń suwak w lewo lub kliknij prawym przyciskiem myszy, aby zmienić ustawienie jednostek DWU.

3. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat z potwierdzeniem. Kliknij pozycję **tak**, aby potwierdzić, lub **nie**, aby anulować.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby zmienić liczbę jednostek dwu lub cDWUs, należy użyć [AzSqlDatabase zestaw](/powershell/module/az.sql/set-azsqldatabase) polecenia cmdlet programu PowerShell. Poniższy przykład ustawia cel poziomu usługi DW1000 MySQLDW, która jest przechowywana na serwerze MyServer w bazie danych.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Aby uzyskać więcej informacji, zobacz [poleceń cmdlet programu PowerShell dla usługi SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

Za pomocą języka T-SQL można wyświetlić bieżących ustawień jednostek DWU lub cDWU, Zmień ustawienia i sprawdzić postęp.

Aby zmienić liczbę jednostek dwu lub cDWUs:

1. Połącz się z główną bazą danych skojarzonych z serwera logicznego bazy danych SQL.
2. Użyj [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) instrukcji TSQL. Poniższy przykład ustawia cel poziomu usługi DW1000 MySQLDW bazy danych.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>Interfejsy API REST

Aby zmienić liczby jednostek dwu, użyj [tworzenia lub aktualizacji bazy danych](/rest/api/sql/databases/createorupdate) interfejsu API REST. Poniższy przykład ustawia cel poziomu usługi DW1000 MySQLDW, która jest hostowana na serwerze MyServer w bazie danych. Serwer jest w grupie zasobów platformy Azure o nazwie ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Aby uzyskać więcej przykładów interfejsu API REST, zobacz [interfejsów API REST usługi SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Sprawdzanie stanu zmiany jednostki DWU

Jednostka DWU zmian może potrwać kilka minut. Skalowanie automatyczne można implementować logikę w celu zapewnienia, że niektóre operacje zostały zakończone przed dalszym wykonywaniem innej akcji.

Sprawdzanie stanu bazy danych za pośrednictwem różnych punktów końcowych pozwala na poprawnego zaimplementowania automatyzacji. Portal zapewnia powiadomienie po zakończeniu operacji i bieżący stan bazy danych, ale nie zezwala na programowe sprawdzanie stanu.

Nie można sprawdzić stan bazy danych dla operacji skalowania w poziomie przy użyciu witryny Azure portal.

Aby sprawdzić stan zmiany jednostek DWU:

1. Połącz się z główną bazą danych skojarzonych z serwera logicznego bazy danych SQL.
2. Prześlij następujące zapytanie, aby sprawdzić stan bazy danych.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Prześlij następujące zapytanie, aby sprawdzić stan operacji

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Ten widok DMV zwraca informacje o różnych operacji zarządzania w usłudze SQL Data Warehouse, takie jak operacji i stan operacji, który jest przyjmuje lub ukończone.

## <a name="the-scaling-workflow"></a>Skalowanie przepływu pracy

Po rozpoczęciu operacji skalowania systemu najpierw rozłącza wszystkie otwarte sesje, wycofywanie transakcji otwartych zapewnienie spójnego stanu. Dla operacji skalowania skalowanie występuje tylko po zakończeniu tego wycofywania transakcji.  

- Dla operacji skalowania w górę system Odłącza wszystkie węzły obliczeniowe, przepisy dodatkowych węzłów obliczeniowych i następnie ponowne dołączenie następuje ponowne do warstwy magazynowania.
- Dla operacji skalowania w dół system Odłącza wszystkie węzły obliczeniowe, a następnie ponowne dołączenie następuje ponowne tylko węzły wymagane do warstwy magazynowania.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zarządzania wydajnością, zobacz [klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md) i [limity pamięci i współbieżności](memory-and-concurrency-limits.md).

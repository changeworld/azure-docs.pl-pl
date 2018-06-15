---
title: Jednostki magazynu danych (dwu, cDWUs) w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zalecenia dotyczące wybierania idealne numer jednostki magazynu danych (dwu, cDWUs), aby zoptymalizować cen i wydajności oraz jak zmienić liczbę jednostek.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 94791e4dc3d3c841dde4685d34d4e3fdaf7d9af7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185964"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Jednostki magazynu danych (dwu) i zasobów obliczeniowych jednostki magazynu danych (cDWUs)
Zalecenia dotyczące wybierania idealne numer jednostki magazynu danych (dwu, cDWUs), aby zoptymalizować cen i wydajności oraz jak zmienić liczbę jednostek. 

## <a name="what-are-data-warehouse-units"></a>Co to są jednostki magazynu danych?
Z Procesora magazynu danych SQL, pamięci i we/wy są połączone w jednostki skali obliczeniowe o nazwie jednostki magazynu danych (dwu). Jednostka DWU reprezentuje abstrakcyjne znormalizowane miary zasoby obliczeniowe i wydajność. Zmieniając poziom obsługi można zmienić liczby jednostek dwu, które są przydzielone do systemu, który z kolei dostosowuje wydajności i kosztów, system. 

Aby zapłacić za większą wydajność, można zwiększyć liczbę jednostek magazynu danych. Aby zapłacić za mniej wydajności, Zmniejsz liczbę jednostek magazynu danych. Koszty magazynu i zasobów obliczeniowych są rozliczane oddzielnie, więc zmiana jednostki magazynu danych nie ma wpływu na kosztów magazynowania.

Wydajność w przypadku jednostki magazynu danych jest oparty na te metryki obciążenia magazynu danych:

- Jak szybka może zapytania magazynowania danych standardowych skanowania dużej liczby wierszy i wykonaniu złożoną agregację? Ta operacja jest we/wy i Procesora CPU.
- Szybkość magazynu danych można pozyskiwania danych z obiektów blob magazynu Azure lub usługi Azure Data Lake? Ta operacja jest sieci i Procesora CPU. 
- Jak szybko można [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) polecenia T-SQL kopiowanie tabeli? Ta operacja obejmuje odczytywanie danych z magazynu, dystrybucję w węzłach urządzenia i zapisywania w magazynie ponownie. Ta operacja jest sieć znacznym, we/wy i procesora CPU.

Zwiększa liczbę jednostek dwu:
- Liniowo zmiany wydajności systemu pod kątem skanowania, agregacji i CTAS — instrukcje
- Zwiększa liczbę czytelników i autorzy operacjach obciążenia PolyBase
- Zwiększa maksymalną liczbę równoczesnych zapytań i gniazda współbieżności.

## <a name="service-level-objective"></a>Cel poziomu usługi
Cel poziomu usługi (SLO) jest ustawienie skalowalność, który określa poziom kosztów i wydajności magazynu danych. Poziomy usług dla Gen2 są mierzone w jednostki magazynu danych obliczeń (cDWU), na przykład DW2000c. Poziomy usług Gen1 są mierzone w dwu, na przykład DW2000. 

T-SQL ustawienie SERVICE_OBJECTIVE określa poziom usługi i warstwę wydajności magazynu danych.

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

## <a name="performance-tiers-and-data-warehouse-units"></a>Wydajność warstwy i jednostki magazynu danych

Każda warstwa wydajności używa nieco inne jednostki miary ich jednostki magazynu danych. Ta różnica jest widoczny na fakturze jako jednostki skali bezpośrednio przekłada do rozliczeń.

- Magazyny danych Gen1 są mierzone w jednostki magazynu danych (dwu).
- Warehousesr danych Gen2 są mierzone w obliczeń jednostki magazynu danych (cDWUs). 

Zarówno jednostek dwu i cDWUs obsługuje obliczeń skalowania w górę lub w dół, a wstrzymanie obliczeniowe, gdy nie jest konieczne do użycia w magazynie danych. Operacje te są wszystkie na żądanie. Gen2 używa lokalnej pamięci podręcznej opartej na dysku w węzłach obliczeniowych, aby zwiększyć wydajność. Skalować lub wstrzymać systemu unieważnienia pamięci podręcznej i dlatego okres wstępne pamięci podręcznej jest wymagany, zanim optymalna wydajność jest osiągana.  

Wraz ze zwiększaniem jednostki magazynu danych są liniowo zwiększanie zasobów obliczeniowych. Gen2 zapewnia najlepszą wydajność zapytań i najwyższy skali, ale ma wyższą cenę wpisu. Jest on przeznaczony dla firm, które mają stałą zapotrzebowanie na wydajność. Te systemy wykorzystują większości pamięci podręcznej. 

### <a name="capacity-limits"></a>Limity pojemności
Każdy serwer SQL (na przykład myserver.database.windows.net) ma [jednostka transakcji bazy danych (DTU)](../sql-database/sql-database-what-is-a-dtu.md) przydziału, który umożliwia określoną liczbę jednostki magazynu danych. Aby uzyskać więcej informacji, zobacz [limity pojemności zarządzania obciążenia](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Liczbę jednostek magazynu danych potrzebne?
Nadaje się doskonale liczbę jednostek magazynu danych zależy od znacznie obciążenie i ilość danych, które zostały załadowane do systemu.

Kroki do znajdowania wartość DWU najlepsze dla obciążenia:

1. Rozpocznij od wybrania mniejsze wartości DWU. 
2. Jak przetestowaniu ilości danych do systemu, obserwowania liczby jednostek dwu wybrane w porównaniu do wydajności, które należy obserwować, należy monitorować wydajność aplikacji.
3. Zidentyfikować wszelkie dodatkowe wymagania dla okresowe okresów szczytowego działania. Jeśli obciążenie zawiera znaczące pików i koryta w działaniu i powody często skalowania.

Usługa SQL Data Warehouse to system skalowalnego w poziomie, który można udostępnić czasach ogromne ilości obliczeniowych i zapytania duże ilości danych. Aby wyświetlić jego wartość true, możliwości skalowania, zwłaszcza na większą liczbę jednostek dwu, zaleca się skalowanie zestawu danych podczas skalowania, aby upewnić się, że masz za mało danych do źródła danych procesorów CPU. Do testowania skali, firma Microsoft zaleca używanie co najmniej 1 TB.

> [!NOTE]
>
> Wydajność zapytań tylko zwiększa się wraz z paralelizacja więcej jeśli pracy może zostać podzielony między węzły obliczeń. Jeśli okaże się, że skalowanie nie zmienia się na wydajność, może być konieczne dostrojenie projektu tabeli i/lub zapytań. Dla zapytania dostrajanie wskazówki, zobacz [zarządzać zapytaniami użytkownika](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Uprawnienia

Zmiana jednostki magazynu danych wymaga uprawnienia opisane w [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). 

## <a name="view-current-dwu-settings"></a>Wyświetl bieżące ustawienia Jednostka DWU

Aby wyświetlić bieżące ustawienie wartości DWU:

1. Otwórz Eksplorator obiektów SQL Server w programie Visual Studio.
2. Połączenia z bazą danych master, skojarzone z serwera logicznego bazy danych SQL.
3. Wybierz z sys.database_service_objectives dynamiczny widok zarządzania. Oto przykład: 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Zmień jednostki magazynu danych

### <a name="azure-portal"></a>Azure Portal
Aby zmienić liczbę jednostek dwu lub cDWUs:

1. Otwórz [portalu Azure](https://portal.azure.com), otwórz bazę danych i kliknij przycisk **skali**.

2. W obszarze **skali**, przesuń suwak w lewo lub kliknij prawym przyciskiem myszy, aby zmienić ustawienie wartości DWU.

3. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat z potwierdzeniem. Kliknij pozycję **tak**, aby potwierdzić, lub **nie**, aby anulować.

### <a name="powershell"></a>PowerShell
Aby zmienić liczbę jednostek dwu lub cDWUs, użyj [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) polecenia cmdlet programu PowerShell. Poniższy przykład ustawia cel poziomu usługi DW1000 MySQLDW, który znajduje się na serwerze MyServer bazy danych.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Aby uzyskać więcej informacji, zobacz [poleceń cmdlet programu PowerShell dla usługi SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL
W T-SQL można wyświetlać ustawienia bieżącej wartości DWU lub cDWU, Zmień ustawienia i sprawdzić postęp. 

Aby zmienić liczbę jednostek dwu lub cDWUs:

1. Połączenia z bazą danych master, skojarzona z serwerem logicznym bazy danych SQL.
2. Użyj [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) instrukcji TSQL. Poniższy przykład ustawia cel poziomu usługi DW1000 MySQLDW bazy danych. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>Interfejsy API REST

Aby zmienić liczbę jednostek dwu, użyj [Tworzenie lub aktualizacja bazy danych](/rest/api/sql/databases/createorupdate) interfejsu API REST. Poniższy przykład ustawia cel poziomu usługi DW1000 MySQLDW, która jest hostowana na serwerze MyServer bazy danych. Serwer jest w grupie zasobów platformy Azure o nazwie ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Aby uzyskać więcej przykładów interfejsu API REST, zobacz [interfejsów API REST dla usługi SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Sprawdź stan zmiany jednostka DWU

Jednostka DWU zmian może zająć kilka minut. Skalowanie automatyczne można zastosować logiki, aby upewnić się, czy niektóre operacje zostały zakończone przed kontynuowaniem inną akcję. 

Sprawdzanie stanu bazy danych za pośrednictwem różnych punktów końcowych umożliwia prawidłowo zaimplementować automatyzacji. Portal zawiera powiadomienie po zakończeniu operacji i bieżący stan bazy danych, ale nie jest możliwe programowe sprawdzania stanu. 

Nie można sprawdzić stan bazy danych dla operacji skalowania w poziomie przy użyciu portalu Azure.

Aby sprawdzić stan DWU zmiany:

1. Połączenia z bazą danych master, skojarzona z serwerem logicznym bazy danych SQL.
2. Przedstawia następujące zapytanie, aby sprawdzić stan bazy danych.


```sql
SELECT    *
FROM      sys.databases
;
```

3. Następujące zapytanie, aby sprawdzić stan operacji przesyłania

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Ta DMV zwraca informacje na temat zarządzania różnych operacji na usługą SQL Data Warehouse, takie jak operacji i stan operacji, która jest albo być IN_PROGRESS lub UKOŃCZONA.

## <a name="the-scaling-workflow"></a>Skalowania przepływu pracy

Po zainicjowaniu operacji skalowania systemu najpierw rozłącza wszystkie otwarte sesje, wycofywanie transakcji otwartych zapewnienie spójnego stanu. Dla operacji skalowania skalowanie występuje tylko po zakończeniu tego transakcyjne wycofywania.  

- Dla operacji skalowania w górę, system postanowienia dodatkowy obliczeniowe i następnie reattaches do warstwy magazynu. 
- Dla operacji dół, niepotrzebnych węzłów z magazynu i przyłączając ponownie do pozostałych węzłów.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o zarządzaniu wydajności, zobacz [klasy zasobów do zarządzania obciążenia](resource-classes-for-workload-management.md) i [limity pamięci i](memory-and-concurrency-limits.md).




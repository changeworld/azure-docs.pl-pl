---
title: Jednostki magazynu danych (jednostek dwu, cDWUs) w usłudze Azure Synapse Analytics (dawniej SQL DW)
description: Zalecenia dotyczące wyboru idealnej liczby jednostek magazynu danych (jednostek dwu, cDWUs) w celu zoptymalizowania cen i wydajności oraz sposobu zmiany liczby jednostek.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: caa23d3e86fba86aa45e677f7ab85859cda6ddce
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133162"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Jednostki magazynu danych (jednostek dwu) i jednostki magazynu danych obliczeniowych (cDWUs)

Zalecenia dotyczące wyboru idealnej liczby jednostek magazynu danych (jednostek dwu, cDWUs) w celu zoptymalizowania cen i wydajności oraz sposobu zmiany liczby jednostek.

## <a name="what-are-data-warehouse-units"></a>Co to są jednostki magazynu danych

Pula SQL reprezentuje kolekcję zasobów analitycznych, które są obsługiwane podczas korzystania z usługi [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse). Zasoby analityczne są definiowane jako kombinacja procesora CPU, pamięci i operacji we/wy. Te trzy zasoby są powiązane z jednostkami skali obliczeniowej o nazwie jednostki magazynu danych (jednostek dwu). JEDNOSTEK dwu reprezentuje abstrakcyjną, znormalizowaną miarę zasobów obliczeniowych i wydajności. Zmiana poziomu usługi zmienia liczbę jednostek dwu, które są dostępne dla systemu, co z kolei dostosowuje wydajność i koszt systemu.

Aby uzyskać większą wydajność, można zwiększyć liczbę jednostek magazynu danych. W przypadku mniejszej wydajności Zmniejsz liczbę jednostek magazynu danych. Opłaty za magazyn i opłaty za zasoby obliczeniowe są rozliczane oddzielnie, więc zmiana jednostek magazynu danych nie ma wpływu na koszty magazynowania.

Wydajność dla jednostek magazynu danych jest oparta na następujących metrykach obciążenia:

- Jak Fast kwerenda magazynu danych w warstwie Standardowa może skanować dużą liczbę wierszy, a następnie przeprowadzać złożoną agregację. Ta operacja jest operacją we/wy i intensywnie wykorzystującą procesor CPU.
- Jak szybko magazyn danych może pozyskać dane z obiektów BLOB usługi Azure Storage lub Azure Data Lake. Ta operacja jest intensywnie korzystające z sieci i procesora CPU.
- Jak szybko [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) polecenie t-SQL może skopiować tabelę. Ta operacja obejmuje odczytywanie danych z magazynu, ich dystrybucję w węzłach urządzenia i ponowne zapisywanie w magazynie. Ta operacja polega na procesorach CPU, we/wy i intensywnym wykorzystaniu sieci.

Zwiększanie jednostek dwu:

- Liniowie zmienia wydajność systemu na potrzeby skanów, agregacji i instrukcji CTAS
- Zwiększa liczbę czytników i autorów dla operacji ładowania bazowego
- Zwiększa maksymalną liczbę współbieżnych zapytań i miejsc współbieżności.

## <a name="service-level-objective"></a>Cel poziomu usługi

Cel poziomu usługi (SLO) to ustawienie skalowalności określające koszt i poziom wydajności magazynu danych. Poziomy usługi dla puli SQL Gen2 są mierzone w jednostkach obliczeniowych magazynu danych (cDWU), na przykład DW2000c. Gen1 poziomy usługi puli SQL są mierzone w jednostek dwu, na przykład DW2000.
  > [!NOTE]
  > Pula SQL generacji 2 niedawno dodała dodatkowe możliwości skalowania do obsługi warstw obliczeniowych jako 100 cDWU. Istniejące pule SQL obecnie w usłudze Gen1, które wymagają niższych warstw obliczeń, można teraz uaktualnić do Gen2 w regionach, które są obecnie dostępne bez dodatkowych kosztów.  Jeśli region nie jest jeszcze obsługiwany, można nadal przeprowadzić uaktualnienie do obsługiwanego regionu. Aby uzyskać więcej informacji, zobacz [uaktualnianie do Gen2](upgrade-to-latest-generation.md).

W języku T-SQL ustawienie SERVICE_OBJECTIVE określa poziom usług i warstwę wydajności dla puli SQL.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Warstwy wydajności i jednostki magazynu danych

Każda warstwa wydajności używa nieco innej jednostki miary dla jednostek magazynu danych. Różnica ta jest odzwierciedlana na fakturze, ponieważ jednostka skali bezpośrednio tłumaczy na rozliczenia.

- Gen1 pule SQL są mierzone w jednostkach magazynu danych (jednostek dwu).
- Gen2 pule SQL są mierzone w jednostkach obliczeniowych magazynu danych (cDWUs).

Zarówno jednostek dwu, jak i cDWUs obsługują skalowanie w górę i w dół oraz wstrzymywanie obliczeń, gdy nie trzeba używać puli SQL. Wszystkie te operacje są na żądanie. Gen2 używa lokalnej pamięci podręcznej opartej na dyskach w węzłach obliczeniowych w celu zwiększenia wydajności. W przypadku skalowania lub wstrzymania systemu pamięć podręczna jest unieważniona i dlatego przed osiągnięciem optymalnej wydajności jest wymagane przeprowadzenie pamięci podręcznej.  

W miarę zwiększania liczby jednostek magazynu danych zwiększa się liniowo zasoby obliczeniowe. Gen2 zapewnia najlepszą wydajność zapytań i najwyższą skalę. Systemy Gen2 korzystają również z pamięci podręcznej.

### <a name="capacity-limits"></a>Limity pojemności

Każdy serwer SQL (na przykład myserver.database.windows.net) ma przydział [jednostki transakcji bazy danych (DTU)](../sql-database/sql-database-what-is-a-dtu.md) , który umożliwia określoną liczbę jednostek magazynu danych. Aby uzyskać więcej informacji, zobacz [limity wydajności zarządzania obciążeniami](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Ile jest potrzebnych jednostek magazynu danych

Idealna liczba jednostek magazynu danych zależy znacznie od obciążenia i ilości danych załadowanych do systemu.

Kroki umożliwiające znalezienie najlepszego jednostek dwu dla obciążenia:

1. Zacznij od wybrania mniejszej jednostek dwu.
2. Monitoruj wydajność aplikacji podczas testowania obciążeń danych w systemie, obserwując liczbę jednostek dwu wybranych w porównaniu z podaną wydajnością.
3. Określ dodatkowe wymagania dla okresowych okresów aktywności szczytowej. Obciążenia pokazujące znaczące wartości szczytowe i troughs w działaniu mogą wymagać częstego skalowania.

SQL Analytics to system skalowalny w poziomie, który umożliwia udostępnianie ogromnych ilości danych obliczeniowych i zapytań pokaźną. Aby wyświetlić prawdziwe możliwości skalowania, szczególnie w przypadku większych jednostek dwu, zalecamy skalowanie zestawu danych w miarę skalowania, aby upewnić się, że dane są wystarczające do strumieniowego korzystania z procesorów. W celu przetestowania skali zalecamy użycie co najmniej 1 TB.

> [!NOTE]
>
> Wydajność zapytań zwiększa się tylko o więcej przetwarzanie równoległe, jeśli prace można podzielić między węzłami obliczeniowymi. Jeśli okaże się, że skalowanie nie zmienia wydajności, może być konieczne dostrojenie projektu tabeli i/lub zapytań. Aby uzyskać wskazówki dotyczące dostrajania zapytań, zobacz [Zarządzanie pytaniami użytkowników](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Uprawnienia

Zmiana jednostek magazynu danych wymaga uprawnień opisanych w temacie [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

Wbudowane role dla zasobów platformy Azure, takie jak współautor bazy danych SQL i współautor SQL Server, mogą zmieniać ustawienia jednostek dwu.

## <a name="view-current-dwu-settings"></a>Wyświetlanie bieżących ustawień jednostek dwu

Aby wyświetlić bieżące ustawienie jednostek dwu:

1. Otwórz Eksplorator obiektów SQL Server w programie Visual Studio.
2. Nawiąż połączenie z bazą danych Master skojarzoną z serwerem logicznym SQL Database.
3. Wybierz opcję z dynamicznego widoku zarządzania sys. database_service_objectives. Oto przykład:

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

Aby zmienić jednostek dwu lub cDWUs:

1. Otwórz [Azure Portal](https://portal.azure.com), Otwórz bazę danych, a następnie kliknij pozycję **Skaluj**.

2. W obszarze **skalowanie**przesuń suwak w lewo lub w prawo, aby zmienić ustawienie jednostek dwu.

3. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat z potwierdzeniem. Kliknij pozycję **tak**, aby potwierdzić, lub **nie**, aby anulować.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby zmienić jednostek dwu lub cDWUs, należy użyć polecenia cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) programu PowerShell. Poniższy przykład ustawia cel poziomu usługi wartości DW1000 dla bazy danych MySQLDW, która jest hostowana na serwerze serwera programu.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Aby uzyskać więcej informacji, zobacz [polecenia cmdlet programu PowerShell dla SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

Przy użyciu języka T-SQL można wyświetlić bieżące ustawienia jednostek dwu lub cDWU, zmienić ustawienia i sprawdzić postęp.

Aby zmienić jednostek dwu lub cDWUs:

1. Nawiąż połączenie z bazą danych Master skojarzoną z serwerem logicznym SQL Database.
2. Użyj instrukcji [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL. Poniższy przykład ustawia cel poziomu usługi wartości DW1000 dla bazy danych MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>Interfejsy API REST

Aby zmienić jednostek dwu, użyj interfejsu API REST [tworzenia lub aktualizacji bazy danych](/rest/api/sql/databases/createorupdate) . Poniższy przykład ustawia cel poziomu usługi wartości DW1000 dla bazy danych MySQLDW, która jest hostowana na serwerze serwera programu. Serwer należy do grupy zasobów platformy Azure o nazwie ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Aby uzyskać więcej przykładów interfejsu API REST, zobacz [interfejsy API REST dla SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Sprawdź stan zmian jednostek dwu

JEDNOSTEK dwu zmiany mogą potrwać kilka minut. Jeśli skalowanie odbywa się automatycznie, należy rozważyć zaimplementowanie logiki, aby upewnić się, że niektóre operacje zostały zakończone przed przejściem do innej akcji.

Sprawdzenie stanu bazy danych za pomocą różnych punktów końcowych pozwala na prawidłowe wdrożenie automatyzacji. Portal udostępnia powiadomienie po zakończeniu operacji i bazach danych w bieżącym stanie, ale nie umożliwia programistycznego sprawdzania stanu.

Nie można sprawdzić stanu bazy danych dla operacji skalowania w poziomie przy użyciu Azure Portal.

Aby sprawdzić stan jednostek dwu zmian:

1. Nawiąż połączenie z bazą danych Master skojarzoną z serwerem logicznym SQL Database.

1. Prześlij następujące zapytanie, aby sprawdzić stan bazy danych.

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
    
Ta DMV zwraca informacje o różnych operacjach zarządzania w puli SQL, takich jak operacja i stan operacji, która jest IN_PROGRESS lub UKOŃCZONa.

## <a name="the-scaling-workflow"></a>Przepływ pracy skalowania

Po rozpoczęciu operacji skalowania system najpierw kasuje wszystkie otwarte sesje, wycofywanie wszelkich otwartych transakcji w celu zapewnienia spójnego stanu. W przypadku operacji skalowania skalowanie odbywa się tylko po zakończeniu wycofywania transakcyjnego.  

- W przypadku operacji skalowania system Odłącza wszystkie węzły obliczeniowe, Inicjuje obsługę dodatkowych węzłów obliczeniowych, a następnie dołącza je do warstwy magazynowania.
- W przypadku operacji skalowania w dół system Odłącza wszystkie węzły obliczeniowe, a następnie ponownie dołącza do warstwy magazynowania tylko konieczne węzły.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zarządzaniu wydajnością, zobacz [klasy zasobów dla zarządzania obciążeniami](resource-classes-for-workload-management.md) oraz [limity pamięci i współbieżności](memory-concurrency-limits.md).

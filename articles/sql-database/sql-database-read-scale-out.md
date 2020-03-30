---
title: Odczytywanie zapytań dotyczących replik
description: Usługa Azure SQL Database zapewnia możliwość równoważenia obciążenia tylko do odczytu obciążeń przy użyciu pojemności replik tylko do odczytu — o nazwie Odczyt skalowania w poziomie.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206949"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Używanie repliki tylko do odczytu w celu równoważenia obciążeń w przypadku obciążeń związanych z zapytaniami tylko do odczytu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W ramach [architektury Wysoka dostępność](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)każda baza danych w warstwie usługi Premium i Business Critical jest automatycznie aprowizowana za pomocą repliki podstawowej i kilku replik pomocniczych. Repliki pomocnicze są aprowiowane z tym samym rozmiarem obliczeń co replika podstawowa. Funkcja **Skalowania odczytu w poziomie** umożliwia równoważenie obciążenia obciążeń tylko do odczytu bazy danych SQL przy użyciu pojemności jednej z replik tylko do odczytu zamiast udostępniania repliki odczytu i zapisu. Dzięki temu obciążenie tylko do odczytu zostanie odizolowane od głównego obciążenia do odczytu i zapisu i nie będzie wpływać na jego wydajność. Ta funkcja jest przeznaczona dla aplikacji, które zawierają logicznie oddzielone obciążenia tylko do odczytu, takie jak analizy. W warstwach usług Premium i Business Critical aplikacje mogą uzyskać korzyści z wydajności przy użyciu tej dodatkowej pojemności bez dodatkowych kosztów.

Funkcja **Skalowania w poziomie odczytu** jest również dostępna w warstwie usługi Hiperskali po utworzeniu co najmniej jednej repliki pomocniczej. Wiele replik pomocniczych może służyć, jeśli obciążenia tylko do odczytu wymagają więcej zasobów niż dostępne w jednej replice pomocniczej. Architektura wysokiej dostępności warstwy usług Basic, Standard i General Purpose nie zawiera żadnych replik. Funkcja **Skalowania w poziomie odczytu** nie jest dostępna w tych warstwach usług.

Na poniższym diagramie przedstawiono go przy użyciu bazy danych o znaczeniu krytycznym dla firmy.

![Repliki tylko do odczytu](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Funkcja Skalowania w poziomie odczytu jest domyślnie włączona w nowych bazach danych Premium, Business Critical i Hyperscale. W przypadku hiperskali domyślnie dla nowych baz danych tworzona jest jedna replika pomocnicza. Jeśli ciąg połączenia SQL jest `ApplicationIntent=ReadOnly`skonfigurowany za pomocą aplikacji zostanie przekierowany przez bramę do repliki tylko do odczytu tej bazy danych. Aby uzyskać informacje na `ApplicationIntent` temat używania właściwości, zobacz [Określanie intencji aplikacji](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Jeśli chcesz upewnić się, że aplikacja łączy się `ApplicationIntent` z repliką podstawową, niezależnie od ustawienia w ciągu połączenia SQL, należy jawnie wyłączyć odczyt skalowalnie w poziomie podczas tworzenia bazy danych lub podczas zmiany jej konfiguracji. Na przykład jeśli uaktualnisz bazę danych z warstwy Standardowego lub ogólnego przeznaczenia do warstwy Premium, Krytyczna dla firm lub Hiperskala i chcesz upewnić się, że wszystkie połączenia będą nadal przechodzić do repliki podstawowej, wyłącz skalowanie w poziomie odczytu. Aby uzyskać szczegółowe informacje na temat wyłączania go, zobacz [Włączanie i wyłączanie funkcji Odczytuj skalowanie w poziomie .](#enable-and-disable-read-scale-out)

> [!NOTE]
> Funkcje magazynu danych kwerend, zdarzeń rozszerzonych i programu SQL Profiler nie są obsługiwane w replikach tylko do odczytu.

## <a name="data-consistency"></a>Spójność danych

Jedną z zalet replik jest to, że repliki są zawsze w stanie spójne transakcyjnie, ale w różnych punktach w czasie może być pewne małe opóźnienie między różnymi replikami. Odczyt skalowawczy w poziomie obsługuje spójność na poziomie sesji. Oznacza to, że jeśli sesja tylko do odczytu łączy się ponownie po błędzie połączenia spowodowanym niedostępnością repliki, może zostać przekierowana do repliki, która nie jest w 100% aktualna z repliką odczytu i zapisu. Podobnie jeśli aplikacja zapisuje dane przy użyciu sesji odczytu i zapisu i natychmiast odczytuje je przy użyciu sesji tylko do odczytu, jest możliwe, że najnowsze aktualizacje nie są natychmiast widoczne w replice. Opóźnienie jest spowodowane przez operację ponownego rejestrowania asynchronii.

> [!NOTE]
> Opóźnienia replikacji w regionie są niskie i ta sytuacja jest rzadka.

## <a name="connect-to-a-read-only-replica"></a>Łączenie się z repliką tylko do odczytu

Po włączeniu odczytu skalowaw `ApplicationIntent` w poziomie dla bazy danych, opcja w ciągu połączenia dostarczonych przez klienta określa, czy połączenie jest kierowane do repliki zapisu lub do repliki tylko do odczytu. W szczególności jeśli `ApplicationIntent` wartość `ReadWrite` jest (wartość domyślna), połączenie zostanie przekierowane do repliki odczytu i zapisu bazy danych. Jest to identyczne z istniejącym zachowaniem. Jeśli `ApplicationIntent` wartość `ReadOnly`jest , połączenie jest kierowane do repliki tylko do odczytu.

Na przykład następujący ciąg połączenia łączy klienta z repliką tylko do odczytu (zastępując elementy w nawiasach kątowych odpowiednimi wartościami dla środowiska i upuszczając nawiasy kątowe):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Jeden z następujących ciągów połączeń łączy klienta z repliką odczytu i zapisu (zastępując elementy w nawiasach kątowych odpowiednimi wartościami dla środowiska i upuszczając nawiasy kątowe):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Sprawdzanie, czy połączenie jest z repliką tylko do odczytu

Można sprawdzić, czy masz połączenie z repliką tylko do odczytu, uruchamiając następującą kwerendę. Powróci READ_ONLY po podłączeniu do repliki tylko do odczytu.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> W danym momencie tylko jedna z replik AlwaysON jest dostępna przez sesje ReadOnly.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitorowanie i rozwiązywanie problemów z repliką tylko do odczytu

Po podłączeniu do repliki tylko do odczytu można `sys.dm_db_resource_stats` uzyskać dostęp do metryk wydajności przy użyciu pliku DMV. Aby uzyskać dostęp do statystyk `sys.dm_exec_query_stats` `sys.dm_exec_query_plan` planu `sys.dm_exec_sql_text` kwerend, należy użyć , i DMV.

> [!NOTE]
> DmV `sys.resource_stats` w logicznej głównej bazie danych zwraca użycie procesora CPU i dane magazynu repliki podstawowej.

## <a name="enable-and-disable-read-scale-out"></a>Włączanie i wyłączanie funkcji Skalowanie w poziomie odczytu

Skalowanie w poziomie odczytu jest domyślnie włączone w warstwach usług Premium, Business Critical i Hyperscale. Nie można włączyć skalowania w poziomie odczytu w warstwach usług podstawowego, standardowego lub ogólnego przeznaczenia. Skalowanie w poziomie odczytu jest automatycznie wyłączane w bazach danych na dużą skalę skonfigurowanych za pomocą replik 0.

Można wyłączyć i ponownie włączyć odczyt skalowawczy w poziomie w pojedynczych bazach danych i bazach danych puli elastycznej w warstwie usług Premium lub Business Critical przy użyciu następujących metod.

> [!NOTE]
> Możliwość wyłączenia odczytu skalowawczy w poziomie jest dostępna dla zgodności z powrotem.

### <a name="azure-portal"></a>Portal Azure

Można zarządzać odczytu skalowania w poziomie ustawienie w **bloku Konfigurowanie** bazy danych.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów co najmniej do grudnia 2020.  Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Przedstawianie nowego modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az)

Zarządzanie skalowaniem w poziomie odczytu w programie Azure PowerShell wymaga wydania programu Azure PowerShell z grudnia 2016 r. lub nowszego. Aby zapoznać się z najnowszą wersją programu [PowerShell, zobacz Program Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Można wyłączyć lub ponownie włączyć odczyt skalowaw w poziomie w programie Azure PowerShell, wywołując polecenie cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) i przekazując żądaną wartość — `Enabled` lub `Disabled` -- dla parametru. `-ReadScale`

Aby wyłączyć odczyt skalowacyjny w poziomie w istniejącej bazie danych (zastąpienie elementów w nawiasach kątowych odpowiednimi wartościami dla środowiska i upuszczenie nawiasów kątowych):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Aby wyłączyć odczyt skalowacyjny w nowej bazie danych (zastąpienie elementów w nawiasach kątowych odpowiednimi wartościami dla środowiska i upuszczenie nawiasów kątowych):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Aby ponownie włączyć odczyt skalowacyjny w poziomie w istniejącej bazie danych (zastąpienie elementów w nawiasach kątowych odpowiednimi wartościami dla środowiska i upuszczenie nawiasów kątowych):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>Interfejs API REST

Aby utworzyć bazę danych z wyłączonym skalowaniem w poziomie odczytu lub zmienić ustawienie `Enabled` `Disabled` dla istniejącej bazy danych, należy użyć następującej metody z właściwością ustawioną `readScale` na lub jak w poniższym przykładowym żądaniu.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Aby uzyskać więcej informacji, zobacz [Bazy danych — tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Korzystanie z bazy danych TempDB w replice tylko do odczytu

Baza danych TempDB nie jest replikowana do replik tylko do odczytu. Każda replika ma własną wersję bazy danych TempDB, która jest tworzona podczas tworzenia repliki. Zapewnia, że TempDB jest aktualizowana i mogą być modyfikowane podczas wykonywania kwerendy. Jeśli obciążenie tylko do odczytu zależy od używania obiektów TempDB, należy utworzyć te obiekty jako część skryptu kwerendy.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Używanie skalowania w poziomie odczytu z replikowanymi geograficznie bazami danych

Jeśli używasz odczytu skalowaw w poziomie do równoważenia obciążenia tylko do odczytu obciążeń w bazie danych, która jest replikowana geograficznie (na przykład jako członek grupy trybu failover), upewnij się, że odczyt skalowano w poziomie jest włączona zarówno w podstawowych i replikowanych geograficznie pomocniczych baz danych. Ta konfiguracja zapewni, że to samo środowisko równoważenia obciążenia będzie kontynuowane, gdy aplikacja łączy się z nowym podstawowym po przełączeniu w tryb failover. Jeśli łączysz się z replikowaną geograficznie pomocniczą bazą danych `ApplicationIntent=ReadOnly` z włączoną skalą odczytu, sesje będą kierowane do jednej z replik w taki sam sposób, w jaki rozsyłujemy połączenia w podstawowej bazie danych.  Sesje bez `ApplicationIntent=ReadOnly` będą kierowane do repliki podstawowej repliki pomocniczej replikowanej geograficznie, która jest również tylko do odczytu. Ponieważ replikowana geograficznie pomocnicza baza danych ma inny punkt końcowy niż podstawowa baza danych, historycznie dostęp do pomocniczej nie był wymagany do ustawiania `ApplicationIntent=ReadOnly`. Aby zapewnić zgodność z `sys.geo_replication_links` powrotem, `secondary_allow_connections=2` dmv pokazuje (każde połączenie klienta jest dozwolone).

> [!NOTE]
> Działanie okrężne lub inne routingu z równoważeniem obciążenia między lokalnymi replikami pomocniczej bazy danych nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o ofercie hiperskali bazy danych SQL, zobacz [warstwa usług hiperskali](./sql-database-service-tier-hyperscale.md).

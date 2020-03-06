---
title: Bazy danych Azure Government | Microsoft Docs
description: Zapewnia to porównanie funkcji i wskazówek dotyczących tworzenia aplikacji dla Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: zakramer
manager: liki
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 03/06/2019
ms.author: zakramer
ms.openlocfilehash: cbf3d59bafa5b11be0fa037487dc64fdb9075619
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357463"
---
# <a name="azure-government-databases"></a>Bazy danych Azure Government
## <a name="sql-database"></a>SQL Database
Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [Azure SQL Database](../sql-database/index.yml) <a href="https://msdn.microsoft.com/library/bb510589.aspx"> aparatu SQL Database Security Center firmy Microsoft</a> , aby uzyskać dodatkowe wskazówki dotyczące konfiguracji widoczności metadanych oraz najlepsze rozwiązania w zakresie ochrony.

### <a name="variations"></a>Obsługi
Baza danych SQL V12 jest ogólnie dostępna w Azure Government.

Adres serwerów SQL Azure w Azure Government jest inny:

| Typ usługi | Azure Public | Azure Government |
| --- | --- | --- |
| SQL Database |*. database.windows.net |*. database.usgovcloudapi.net |

### <a name="considerations"></a>Zagadnienia do rozważenia
Poniższe informacje identyfikują granicę Azure Government dla usługi Azure SQL:

| Dozwolone dane regulowane/kontrolowane | Dane regulowane/kontrolowane nie są dozwolone |
| --- | --- |
| Wszystkie dane przechowywane i przetwarzane w Microsoft Azure SQL mogą zawierać dane regulowane Azure Government. Narzędzia bazy danych umożliwiają transfer danych z Azure Government danych. |Metadane usługi Azure SQL nie mogą zawierać danych kontrolowanych eksportem. Te metadane obejmują wszystkie dane konfiguracyjne wprowadzone podczas tworzenia i konserwowania produktu magazynu.  Nie wprowadzaj danych z regulowane/kontrolowane do następujących pól: Nazwa bazy danych, nazwa subskrypcji, grupy zasobów, nazwa serwera, identyfikator logowania administratora serwera, nazwy wdrożeń, nazwy zasobów, Tagi zasobów |

## <a name="sql-data-warehouse"></a>SQL Data Warehouse
Aby uzyskać szczegółowe informacje dotyczące tej usługi i sposobu korzystania z niej, zobacz [dokumentację Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

## <a name="sql-server-stretch-database"></a>SQL Server Stretch Database
Aby uzyskać szczegółowe informacje dotyczące tej usługi i sposobu korzystania z niej, zobacz [dokumentację usługi Azure SQL Server stretch Database](../sql-server-stretch-database/index.md)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
Aby uzyskać szczegółowe informacje dotyczące tej usługi i sposobu korzystania z niej, zobacz [dokumentację Azure Cosmos DB](../cosmos-db/index.yml).

### <a name="variations"></a>Obsługi
Azure Cosmos DB jest ogólnie dostępna w Azure Government z parzystością wersji publicznej. Jedynym wyjątkiem jest obecnie funkcja **Add wyszukiwanie poznawcze platformy Azure** , która nie jest dostępna w Cosmos DB dla Azure Government.

Ponadto adresy URL do uzyskiwania dostępu do Cosmos DB w Azure Government są różne:

| Typ usługi | Azure Public | Azure Government |
| --- | --- | --- |
| Cosmos DB | *. documents.azure.com | *. documents.azure.us |


### <a name="considerations"></a>Zagadnienia do rozważenia
Poniższe informacje identyfikują Azure Government granicy Azure Cosmos DB:

| Dozwolone dane regulowane/kontrolowane | Dane regulowane/kontrolowane nie są dozwolone |
| --- | --- |
| Wszystkie dane przechowywane i przetwarzane w Azure Cosmos DB mogą zawierać dane regulowane Azure Governmentowo. |Metadane Azure Cosmos DB nie mogą zawierać danych kontrolowanych eksportem. Nie wprowadzaj danych z regulowane/kontrolowane do następujących pól: **Nazwa bazy danych, nazwa subskrypcji, grupy zasobów, Tagi zasobów**. |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Aby uzyskać szczegółowe informacje dotyczące tej usługi i sposobu korzystania z niej, zobacz [Azure cache for Redis Documentation](../azure-cache-for-redis/index.yml).

### <a name="variations"></a>Obsługi
Adresy URL służące do uzyskiwania dostępu do pamięci podręcznej platformy Azure dla Redis w Azure Government są różne:

| Typ usługi | Azure Public | Azure Government |
| --- | --- | --- |
| Punkt końcowy pamięci podręcznej |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |

> [!NOTE]
> Wszystkie skrypty i kod muszą uwzględniać odpowiednie punkty końcowe i środowiska. Aby uzyskać więcej informacji, zobacz [jak nawiązać połączenie z innymi chmurami](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).
>
>

### <a name="considerations"></a>Zagadnienia do rozważenia
Poniższe informacje identyfikują granicę Azure Government dla usługi Azure cache for Redis:

| Dozwolone dane regulowane/kontrolowane | Dane regulowane/kontrolowane nie są dozwolone |
| --- | --- |
| Wszystkie dane przechowywane i przetwarzane w usłudze Azure cache for Redis mogą zawierać dane objęte Azure Government. |Pamięć podręczna platformy Azure dla metadanych Redis nie może zawierać danych kontrolowanych eksportem. Nie wprowadzaj danych z regulowane/kontrolowane do następujących pól: **Nazwa pamięci podręcznej, nazwa subskrypcji, grupy zasobów, Tagi zasobów, właściwości Redis**. |

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Aby uzyskać szczegółowe informacje dotyczące tej usługi i sposobu korzystania z niej, zobacz [dokumentację Azure Database for PostgreSQL](../postgresql/index.yml).

### <a name="variations"></a>Obsługi
Zaawansowana ochrona przed zagrożeniami, szczegółowe informacje o wydajności zapytań i zalecenia dotyczące wydajności Azure Database for PostgreSQL **nie** są dostępne w Azure Government.

Adresy URL do uzyskiwania dostępu do Azure Database for PostgreSQL w programie Azure Government i zarządzania nimi są różne:

| Typ usługi | Azure Public | Azure Government |
| --- | --- | --- |
| Punkt końcowy PostgreSQL |*. postgres.database.azure.com |*. postgres.database.usgovcloudapi.net |

### <a name="considerations"></a>Zagadnienia do rozważenia
Poniższe informacje identyfikują Azure Government granicy Azure Database for PostgreSQL:

| Dozwolone dane regulowane/kontrolowane | Dane regulowane/kontrolowane nie są dozwolone |
| --- | --- |
| Wszystkie dane przechowywane i przetwarzane w Azure Database for PostgreSQL mogą zawierać dane regulowane Azure Governmentowo. Narzędzia bazy danych umożliwiają transfer danych z Azure Government danych. |Metadane Azure Database for PostgreSQL nie mogą zawierać danych kontrolowanych eksportem. Te metadane obejmują wszystkie dane konfiguracyjne wprowadzone podczas tworzenia i konserwowania produktu magazynu.  Nie wprowadzaj danych z regulowane/kontrolowane do następujących pól: Nazwa bazy danych, nazwa subskrypcji, grupy zasobów, nazwa serwera, identyfikator logowania administratora serwera, nazwy wdrożeń, nazwy zasobów, Tagi zasobów. |

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
Aby uzyskać szczegółowe informacje dotyczące tej usługi i sposobu korzystania z niej, zobacz [dokumentację Azure Database for MariaDB](../mariadb/index.yml).

### <a name="variations"></a>Obsługi
Szczegółowe informacje o wydajności zapytań i zalecenia dotyczące wydajności Azure Database for MariaDB **nie** są dostępne w Azure Government.

Adresy URL do uzyskiwania dostępu do Azure Database for MariaDB w programie Azure Government i zarządzania nimi są różne:

| Typ usługi | Azure Public | Azure Government |
| --- | --- | --- |
| Punkt końcowy MariaDB |*. mariadb.database.azure.com |*. mariadb.database.usgovcloudapi.net |

### <a name="considerations"></a>Zagadnienia do rozważenia
Poniższe informacje identyfikują Azure Government granicy Azure Database for MariaDB:

| Dozwolone dane regulowane/kontrolowane | Dane regulowane/kontrolowane nie są dozwolone |
| --- | --- |
| Wszystkie dane przechowywane i przetwarzane w Azure Database for MariaDB mogą zawierać dane regulowane Azure Governmentowo. Narzędzia bazy danych umożliwiają transfer danych z Azure Government danych. |Metadane Azure Database for MariaDB nie mogą zawierać danych kontrolowanych eksportem. Te metadane obejmują wszystkie dane konfiguracyjne wprowadzone podczas tworzenia i konserwowania produktu magazynu.  Nie wprowadzaj danych z regulowane/kontrolowane do następujących pól: Nazwa bazy danych, nazwa subskrypcji, grupy zasobów, nazwa serwera, identyfikator logowania administratora serwera, nazwy wdrożeń, nazwy zasobów, Tagi zasobów. |

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Aby uzyskać szczegółowe informacje dotyczące tej usługi i sposobu korzystania z niej, zobacz [dokumentację Azure Database for MySQL](../mysql/index.yml).

### <a name="variations"></a>Obsługi
Zaawansowana ochrona przed zagrożeniami, szczegółowe informacje o wydajności zapytań i zalecenia dotyczące wydajności Azure Database for MySQL **nie** są dostępne w Azure Government.

Adresy URL do uzyskiwania dostępu do Azure Database for MySQL w programie Azure Government i zarządzania nimi są różne:

| Typ usługi | Azure Public | Azure Government |
| --- | --- | --- |
| Punkt końcowy MySQL |*. mysql.database.azure.com |*. mysql.database.usgovcloudapi.net |

### <a name="considerations"></a>Zagadnienia do rozważenia
Poniższe informacje identyfikują Azure Government granicy Azure Database for MySQL:

| Dozwolone dane regulowane/kontrolowane | Dane regulowane/kontrolowane nie są dozwolone |
| --- | --- |
| Wszystkie dane przechowywane i przetwarzane w Azure Database for MySQL mogą zawierać dane regulowane Azure Governmentowo. Narzędzia bazy danych umożliwiają transfer danych z Azure Government danych. |Metadane Azure Database for MySQL nie mogą zawierać danych kontrolowanych eksportem. Te metadane obejmują wszystkie dane konfiguracyjne wprowadzone podczas tworzenia i konserwowania produktu magazynu.  Nie wprowadzaj danych z regulowane/kontrolowane do następujących pól: Nazwa bazy danych, nazwa subskrypcji, grupy zasobów, nazwa serwera, identyfikator logowania administratora serwera, nazwy wdrożeń, nazwy zasobów, Tagi zasobów. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe informacje i aktualizacje, zasubskrybuj <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog Microsoft Azure Government.</a>

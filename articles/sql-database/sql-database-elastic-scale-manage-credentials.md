---
title: Zarządzanie poświadczeniami w bibliotece klienckiej elastycznej bazy danych | Dokumentacja firmy Microsoft
description: Jak ustawić odpowiedni poziom poświadczeń administratora tylko do odczytu, w przypadku elastycznych baz danych aplikacji
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
ms.date: 01/03/2019
ms.openlocfilehash: 8a62ec95c715c08a8fddc09f0c8e5f5bba368556
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241754"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Poświadczenia używane do dostępu do biblioteki klienckiej Elastic Database

[Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md) używa trzy różne rodzaje poświadczeń w celu uzyskania dostępu do [Menedżera mapowań fragmentów](sql-database-elastic-scale-shard-map-management.md). W zależności od potrzeb za pomocą poświadczeń najniższy poziom dostępu jest możliwe.

* **Poświadczenia zarządzania**: do tworzenia lub modyfikowania Menedżera mapowań fragmentów. (Zobacz [słownik](sql-database-elastic-scale-glossary.md).)
* **Poświadczenia dostępu**: aby dostęp do istniejących Menedżera mapowań fragmentów w celu uzyskania informacji na temat fragmentów.
* **Poświadczenia połączenia**: połączyć się z fragmentów.

Zobacz też [Zarządzanie bazami danych i nazwami logowania w usłudze Azure SQL Database](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Poświadczenia zarządzania — informacje

Poświadczenia zarządzania są używane do tworzenia **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) obiektu dla aplikacji, które manipulują mapowań fragmentów. (Na przykład zobacz [dodanie fragmentu, za pomocą narzędzi elastycznych baz danych](sql-database-elastic-scale-add-a-shard.md) i [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md)). Użytkownik biblioteki klienta elastycznej skali tworzy SQL użytkowników i kont logowania SQL i upewnia się, że każdy otrzymuje uprawnienia odczytu/zapisu w bazie danych globalnych niezależnego fragmentu mapy i wszystkie również niezależnego fragmentu bazy danych. Te poświadczenia są używane do obsługi mapowania fragmentów globalnych i mapowań fragmentów w postaci lokalnej, gdy wykonywane są zmiany do mapy fragmentów. Na przykład użyć poświadczeń zarządzania można utworzyć obiektu Menedżera mapy fragmentów (przy użyciu **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Zmienna **smmAdminConnectionString** ciąg połączenia, który zawiera poświadczenia zarządzania. Identyfikator użytkownika i hasło, podaj odczytu i zapisu do bazy danych mapowań fragmentów i poszczególne fragmenty. Parametry połączenia management zawiera również nazwę serwera i nazwę bazy danych, aby zidentyfikować bazy danych z mapowań fragmentów globalnego. Poniżej przedstawiono typowe parametry połączenia, w tym celu:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Nie używaj wartości w formie "username@server" — zamiast tego użyć wartości "nazwa_użytkownika".  To dlatego poświadczenia muszą pracować z bazy danych Menedżera mapowań fragmentów i poszczególnych fragmentów, które mogą znajdować się na różnych serwerach.

## <a name="access-credentials"></a>Poświadczenia dostępu

Podczas tworzenia Menedżera mapowań fragmentów, w aplikacji, która nie administrować mapowań fragmentów w postaci, Użyj poświadczeń, które mają uprawnienia tylko do odczytu na globalne podzielonej na fragmenty mapy. Informacje uzyskane od globalnej podzielonej na fragmenty mapy w ramach tych poświadczeń jest używany dla [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md) i w celu wypełnienia pamięci podręcznej mapy fragmentów na komputerze klienckim. Poświadczenia są realizowane za pośrednictwem tego samego wzorca wywołania do **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Zwróć uwagę na użycie **smmReadOnlyConnectionString** aby odzwierciedlić użycie innych poświadczeń dla tego dostępu w imieniu **nieadministracyjna** użytkowników: te poświadczenia nie może zapewnić uprawnień do zapisu w mapowania fragmentów globalnego.

## <a name="connection-credentials"></a>Poświadczenia połączenia

Dodatkowe poświadczenia są wymagane w przypadku korzystania z **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) metodę, aby dostęp do fragmentu skojarzony klucz fragmentowania. Wymagane są poświadczenia zapewniające uprawnienia dostępu tylko do odczytu do tabel mapy fragmentów lokalnego znajdującej się na fragmentu. Jest to niezbędne do wykonywania sprawdzania poprawności połączenia dla fragmentu routing zależny od danych. Ten fragment kodu umożliwia dostęp do danych w kontekście routingu zależnego od danych:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

W tym przykładzie **smmUserConnectionString** zawiera parametry połączenia dla poświadczeń użytkownika. Dla bazy danych SQL Azure poniżej przedstawiono typowe parametry połączenia dla poświadczeń użytkownika:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Jak przy użyciu poświadczeń administracyjnych, nie używaj wartości w formie "username@server". Zamiast tego można użyć "nazwa_użytkownika".  Należy również zauważyć, że parametry połączenia nie zawiera nazwę serwera i nazwę bazy danych. To dlatego, że **OpenConnectionForKey** wywołanie automatycznie kieruje połączenia poprawne fragmentu na podstawie klucza. Dzięki temu Nazwa bazy danych i nazwa serwera nie są dostarczane.

## <a name="see-also"></a>Zobacz także

[Zarządzanie bazami danych i nazwami logowania w usłudze Azure SQL Database](sql-database-manage-logins.md)

[Zabezpieczanie bazy danych SQL](sql-database-security-overview.md)

[Zadania elastic Database](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

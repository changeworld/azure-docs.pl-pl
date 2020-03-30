---
title: Zarządzanie poświadczeniami w bibliotece klienta elastycznej bazy danych
description: Jak ustawić odpowiedni poziom poświadczeń, administratora tylko do odczytu, dla aplikacji elastycznej bazy danych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91689a32a128584aade8081905e3d1aa3ecb0a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823583"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Poświadczenia używane do uzyskiwania dostępu do biblioteki klienta elastycznej bazy danych

[Biblioteka klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md) używa trzech różnych rodzajów poświadczeń, aby uzyskać dostęp do [menedżera map niezależnego fragmentu.](sql-database-elastic-scale-shard-map-management.md) W zależności od potrzeb użyj poświadczeń o najniższym możliwym poziomie dostępu.

* **Poświadczenia zarządzania:** do tworzenia lub manipulowania menedżerem mapy niezależnego fragmentu. (Zobacz [słowniczek](sql-database-elastic-scale-glossary.md).)
* **Dostęp do poświadczeń:** aby uzyskać dostęp do istniejącego menedżera map niezależnego fragmentu, aby uzyskać informacje o fragmentach.
* **Poświadczenia połączenia:** aby połączyć się z fragmentami.

Zobacz też [Zarządzanie bazami danych i logowaniami w usłudze Azure SQL Database](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Poświadczenia zarządzania – informacje

Poświadczenia zarządzania są używane do tworzenia **obiektu ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) dla aplikacji, które manipulują mapami niezależnego fragmentu. (Na przykład zobacz [Dodawanie fragmentu przy użyciu narzędzi elastycznej bazy danych](sql-database-elastic-scale-add-a-shard.md) i [routingu zależnego od danych).](sql-database-elastic-scale-data-dependent-routing.md) Użytkownik biblioteki klienta skali elastycznej tworzy użytkowników SQL i sql logowania i upewnia się, że każdy jest przyznawany uprawnienia do odczytu/zapisu w globalnej bazie danych mapy niezależnego fragmentu i wszystkie bazy danych niezależnego fragmentu, jak również. Poświadczenia te są używane do obsługi mapy niezależnego fragmentu globalnego i mapy niezależnego fragmentu lokalnego po wprowadzeniu zmian na mapie niezależnego fragmentu. Na przykład użyj poświadczeń zarządzania, aby utworzyć obiekt menedżera map niezależnego fragmentu (przy użyciu **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Zmienna **smmAdminConnectionString** jest ciągiem połączenia zawierającym poświadczenia zarządzania. Identyfikator użytkownika i hasło zapewniają dostęp do odczytu/zapisu zarówno do bazy danych mapy niezależnego fragmentu, jak i poszczególnych fragmentów. Parametry połączenia zarządzania zawierają również nazwę serwera i nazwę bazy danych w celu zidentyfikowania globalnej bazy danych map niezależnego fragmentu. Oto typowy ciąg połączenia w tym celu:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Nie używaj wartości wusername@serverpostaci " "— zamiast tego po prostu użyj wartości "nazwa użytkownika".  Jest tak, ponieważ poświadczenia muszą działać zarówno w bazie danych menedżera map niezależnego fragmentu, jak i pojedynczych fragmentów, które mogą znajdować się na różnych serwerach.

## <a name="access-credentials"></a>Poświadczenia dostępu

Podczas tworzenia menedżera mapy niezależnego fragmentu w aplikacji, która nie administruje mapami niezależnego fragmentu, należy użyć poświadczeń, które mają uprawnienia tylko do odczytu na mapie niezależnego fragmentu globalnego. Informacje pobrane z mapy niezależnego fragmentu globalnego w ramach tych poświadczeń są używane do [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md) i do wypełniania pamięci podręcznej mapy niezależnego fragmentu na kliencie. Poświadczenia są dostarczane za pośrednictwem tego samego wzorca wywołania **do GetSqlShardMapManager:**

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Należy zwrócić uwagę na użycie **smmReadOnlyConnectionString** do odzwierciedlenia użycia różnych poświadczeń dla tego dostępu w imieniu użytkowników **niebędących administratorami:** te poświadczenia nie powinny zapewniać uprawnień do zapisu na mapie niezależnego fragmentu globalnego.

## <a name="connection-credentials"></a>Poświadczenia połączenia

Dodatkowe poświadczenia są potrzebne podczas korzystania z **metody OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) w celu uzyskania dostępu do niezależnego fragmentu skojarzonego z kluczem dzielenia na fragment. Te poświadczenia muszą zapewnić uprawnienia dostępu tylko do odczytu do tabel mapy niezależnego fragmentu lokalnego zamieszkałych na niezależnego fragmentu. Jest to potrzebne do wykonywania sprawdzania poprawności połączenia dla routingu zależnego od danych na niezależnego fragmentu. Ten fragment kodu umożliwia dostęp do danych w kontekście routingu zależnego od danych:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

W tym przykładzie **smmUserConnectionString** przechowuje parametry połączenia dla poświadczeń użytkownika. W przypadku usługi Azure SQL DB oto typowy ciąg połączenia dla poświadczeń użytkownika:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Podobnie jak w przypadku poświadczeń administratora, nie należy używać wartości w postaci "username@server". Zamiast tego po prostu użyj "nazwy użytkownika".  Należy również zauważyć, że parametry połączenia nie zawierają nazwy serwera i nazwy bazy danych. Dzieje się tak, ponieważ wywołanie **OpenConnectionForKey** automatycznie kieruje połączenie do poprawnego niezależnego fragmentu na podstawie klucza. W związku z tym nazwa bazy danych i nazwa serwera nie są podane.

## <a name="see-also"></a>Zobacz też

[Zarządzanie bazami danych i logowaniami w usłudze Azure SQL Database](sql-database-manage-logins.md)

[Zabezpieczanie bazy danych SQL](sql-database-security-overview.md)

[Zadania elastycznej bazy danych](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

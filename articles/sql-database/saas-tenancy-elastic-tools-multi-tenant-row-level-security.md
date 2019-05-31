---
title: Aplikacje z wieloma dzierżawami przy użyciu zabezpieczeń na poziomie wiersza i narzędzi elastycznej bazy danych | Dokumentacja firmy Microsoft
description: Użyj narzędzi elastycznej bazy danych z zabezpieczeniami na poziomie wiersza do tworzenia aplikacji za pomocą wysoce skalowalną warstwą danych.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 4834688496330210b273f40f1d6f11230a6ae1c8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234127"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Wielodostępne aplikacje za pomocą narzędzi elastycznych baz danych i zabezpieczenia na poziomie wiersza

[Narzędzia elastycznych baz danych](sql-database-elastic-scale-get-started.md) i [zabezpieczenia (RLS)] [ rls] współpracują w celu włączenia skalowania warstwy danych aplikacji z wieloma dzierżawami przy użyciu usługi Azure SQL Database. Razem te technologie pomocne w tworzeniu aplikacji, która ma wysoce skalowalną warstwą danych. Warstwa danych obsługuje wielodostępne fragmenty i używa **ADO.NET SqlClient** lub **Entity Framework**. Aby uzyskać więcej informacji, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS przy użyciu usługi Azure SQL Database](saas-tenancy-app-design-patterns.md).

- **Narzędzia elastycznych baz danych** umożliwia deweloperom skalowanie w poziomie warstwy danych przy użyciu standardowych procedur fragmentowania, przy użyciu bibliotek programu .NET i szablonów usług platformy Azure. Zarządzanie fragmentami przy użyciu [Biblioteka kliencka Elastic Database] [ s-d-elastic-database-client-library] pomaga zautomatyzować i uprościć wiele zadań infrastrukturalnych, zwykle skojarzone z dzielenia na fragmenty.
- **Zabezpieczenia** umożliwia deweloperom bezpieczne przechowywanie danych w wielu dzierżaw w tej samej bazy danych. Zasady zabezpieczeń na poziomie filtrowanie wierszy, które nie należą do dzierżawy, wykonywanie zapytania. Centralizowanie logikę filtrowania wewnątrz bazy danych upraszcza konserwację i zmniejsza ryzyko błędów zabezpieczeń. Alternatywne opierając się na cały kod klienta do wymuszania zabezpieczeń jest ryzykowne.

Korzystając z tych funkcji razem, aplikacja może przechowywać dane dla wielu dzierżaw w tej samej bazy danych fragmentów. To kosztuje mniej na dzierżawę dzierżawcy udostępniania bazy danych. Jeszcze ta sama aplikacja także zaoferować jego dzierżaw premium możliwość płacenia za swoje własne dedykowane fragmentu pojedynczej dzierżawy. Jedną z zalet pojedynczej dzierżawy izolacji jest lepszych gwarancji wydajności. W bazie danych pojedynczej dzierżawy nie istnieje żadne inne dzierżawy, rywalizując o zasoby.

Celem jest użycie Biblioteka kliencka elastic database [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md) interfejsów API, aby automatycznie łączyć się poprawne fragmentu bazy danych z każdej danej dzierżawy. Tylko jeden fragment zawiera konkretną wartość TenantId dla danej dzierżawy. Identyfikator dzierżawy jest *klucz fragmentowania*. Po nawiązaniu połączenia zasady zabezpieczeń na poziomie bazy danych gwarantuje, że danej dzierżawy mogą uzyskać dostęp tylko wiersze danych, które zawierają jego identyfikator dzierżawy.

> [!NOTE]
> Identyfikator dzierżawy może składać się z więcej niż jedną kolumnę. Dla wygody to tej dyskusji, założono nieformalnie identyfikatora dzierżawy z jedną kolumną.

![Architektura aplikacji do obsługi blogów][1]

## <a name="download-the-sample-project"></a>Pobierz przykładowy projekt

### <a name="prerequisites"></a>Wymagania wstępne

- Używanie programu Visual Studio (2012 lub nowszym)
- Utwórz trzy bazy danych Azure SQL
- Pobierz przykładowy projekt: [Narzędzia elastycznej bazy danych dla usług SQL Azure - fragmentów z wieloma dzierżawcami](https://go.microsoft.com/?linkid=9888163)
  - Wprowadź informacje dla baz danych na początku **Program.cs**

Ten projekt rozszerza to opisane w [narzędzia elastyczne bazy danych SQL Azure - Entity Framework integracji](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) przez dodanie obsługi wielodostępnych fragmentu bazy danych. Projekt zostanie skompilowany prostej aplikacji konsolowej do tworzenia blogów i wpisów. Projekt obejmuje czterema dzierżawami oraz dwóch fragmentów wielodostępnych baz danych. Ta konfiguracja została przedstawiona na powyższym diagramie.

Skompiluj i uruchom aplikację. Ten przebieg używa Menedżera mapowań fragmentów narzędzi elastycznej bazy danych do ładowania i wykonuje następujące testy:

1. Przy użyciu platformy Entity Framework i LINQ, tworzenie nowego bloga, a następnie wyświetlić wszystkie blogi dla każdego dzierżawcy
2. Za pomocą ADO.NET SqlClient, wyświetlić wszystkie blogi dla dzierżawy
3. Próba wstawienia blogu do nieprawidłowej dzierżawy sprawdzić, czy został zgłoszony błąd

Zwróć uwagę, że ponieważ zabezpieczenia na poziomie wiersza nie ma jeszcze włączona w bazach danych fragmentów, tych testów, co spowoduje wyświetlenie problem: dzierżawcy będą mogli zobaczyć blogów, które nie należą do nich, a aplikacja jest możliwe wstawianie blog dla nieprawidłowej dzierżawy. W dalszej części tego artykułu opisano sposób rozwiązać te problemy za wymuszania izolacji dzierżawców, zabezpieczenia na poziomie wiersza. Istnieją dwa kroki:

1. **Warstwa aplikacji**: Modyfikowanie kodu aplikacji, aby zawsze ustawić bieżący identyfikator dzierżawy w sesji\_KONTEKSTU po otwarciu połączenia. Przykładowy projekt jest już Ustawia identyfikator dzierżawy w ten sposób.
2. **Warstwa danych**: Tworzenie zasad zabezpieczeń na poziomie w każdej bazie danych fragmentu, aby filtrowanie wierszy, w oparciu o identyfikator dzierżawy, przechowywane w sesji\_KONTEKSTU. Utwórz zasadę dla każdej z baz danych fragmentów, w przeciwnym razie wierszy w wielodostępne fragmenty nie są filtrowane.

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Warstwa aplikacji: Ustaw identyfikator dzierżawy w sesji\_KONTEKSTU

Najpierw połączysz do fragmentu bazy danych przy użyciu interfejsów API routingu zależnego od danych biblioteki klienta elastycznej bazy danych. Aplikacja nadal musisz poinformować bazie których identyfikator dzierżawy jest połączenie. Identyfikator dzierżawy informuje zasady zabezpieczeń na poziomie wierszy, które muszą być odfiltrowane jako należące do innych dzierżaw. Bieżący identyfikator dzierżawy w Store [sesji\_KONTEKSTU](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) połączenia.

Alternatywa dla sesji\_KONTEKSTU jest użycie [KONTEKSTU\_informacje](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Ale sesji\_kontekst jest lepszym rozwiązaniem. SESJA\_kontekst jest łatwiejszy w obsłudze, domyślnie zwraca wartość NULL i obsługuje pary klucz wartość.

### <a name="entity-framework"></a>Entity Framework

W przypadku aplikacji przy użyciu platformy Entity Framework to najłatwiejsza metoda jest ustanowienie sesji\_KONTEKSTU w ramach zastępowania ElasticScaleContext opisanego w [dane zależne od routing przy użyciu programu EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Tworzenie i wykonywanie SqlCommand, określająca identyfikator dzierżawy w sesji\_kontekst shardingKey określonych dla połączenia. Następnie wróć połączeń obsługiwanych przez brokera na wykorzystaniu routingu zależnego od danych. Dzięki temu wystarczy napisać kod raz, aby ustawić sesji\_KONTEKSTU.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

Teraz sesji\_KONTEKSTU jest automatycznie ustawiana za pomocą określonego identyfikatora dzierżawcy, zawsze wtedy, gdy zostanie wywołana ElasticScaleContext:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

Dla aplikacji za pomocą ADO.NET SqlClient należy utworzyć funkcję otokę wokół metoda ShardMap.OpenConnectionForKey. Otoka automatycznie ustawiony identyfikator dzierżawy w sesji mają\_kontekst bieżącego identyfikatora dzierżawy przed zwróceniem połączenia. Aby upewnić się, sesji\_kontekst ma zawsze wartość, należy otworzyć tylko połączenia za pomocą tej funkcji otoki.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Warstwa danych: Tworzenie zasad zabezpieczeń na poziomie wiersza

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Tworzenie zasad zabezpieczeń, aby filtrować wiersze, które mogą uzyskiwać dostęp do każdego dzierżawcy

Teraz, gdy aplikacja ustawia sesji\_kontekst o bieżący identyfikator dzierżawy przed wykonaniem kwerendy, można filtrować zasady zabezpieczeń na poziomie, zapytań i wykluczyć wiersze, w których różne identyfikator dzierżawy.

Zabezpieczenia na poziomie wiersza jest zaimplementowana w języku Transact-SQL. Funkcji zdefiniowanej przez użytkownika zdefiniowana logika dostępu, a zasady zabezpieczeń powiązania tej funkcji do dowolnej liczby tabel. Dla tego projektu:

1. Funkcja sprawdza, czy aplikacja jest połączona z bazą danych i czy identyfikator dzierżawy są przechowywane w sesji\_KONTEKSTU odpowiada identyfikatorowi TenantId w danym wierszu.
    - Aplikacja jest połączona zamiast innych użytkowników SQL.

2. Predykat filtru umożliwia wierszy, które spełniają filtru TenantId dopuszczone do aktualizacji, wybierz kolejno pozycje i usuwanie zapytań.
    - Predykat bloku zapobiega wierszy, które nie spełniają filtr miałyby INSERTed ani zaktualizowane.
    - Jeśli SESJA\_nie ustawiono KONTEKSTU, funkcja zwraca wartość NULL, a żadne wiersze nie są widoczne lub mógł zostać wstawiona.

Aby włączyć zabezpieczenia na poziomie wiersza na wszystkich fragmentów, należy wykonać przy użyciu programu Visual Studio (SSDT), programu SSMS lub skrypt programu PowerShell dołączony w projekcie języka T-SQL. Czy używasz [zadania Elastic Database](elastic-jobs-overview.md), można zautomatyzować wykonywanie tego języka T-SQL na wszystkich fragmentów.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> W projekcie złożone, może być konieczne Dodaj predykat na setkach tabel, która może być żmudne. Brak procedury przechowywane pomocnika, która automatycznie generuje zasady zabezpieczeń i dodaje predykat dla wszystkich tabel w schemacie. Aby uzyskać więcej informacji, zobacz wpis w blogu [stosowanie zabezpieczeń na poziomie wiersza do wszystkich tabel - pomocnika skryptu (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script).

Jeśli uruchomisz ponownie za pomocą aplikacji przykładowej, dzierżaw pojawić tylko wiersze, które należą do nich. Ponadto aplikacja nie można wstawić wierszy, które należą do innych niż ten, który został podłączony do fragmentu bazy danych dzierżaw. Ponadto aplikacji nie można zaktualizować identyfikatora dzierżawy w żadnych wierszy, które mogą być widoczne. Jeśli aplikacja próbuje wykonać jedną, zgłaszany jest DbUpdateException.

Jeśli później dodasz nową tabelę, należy zmienić zasady zabezpieczeń, aby dodać filtr i BLOK predykatów na nowej tabeli.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Dodawanie ograniczenia domyślne, aby automatycznie wypełnić TenantId dla operacji wstawienia

Możesz umieścić domyślne ograniczenie dla każdej tabeli, aby automatycznie wypełnić identyfikator dzierżawy o wartości przechowywane w sesji\_kontekście podczas wstawiania wierszy. Poniżej przedstawiono przykład.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Teraz aplikacja nie trzeba określać identyfikatora dzierżawy podczas wstawiania wierszy:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Jeśli używasz domyślne ograniczenia dla projektu platformy Entity Framework, zaleca się że *nie* zawierała kolumnę identyfikatora dzierżawy w modelu danych EF. To zalecenie wynika Entity Framework automatycznie zapytania dostaw wartości domyślne, które zastępują ograniczenia domyślne utworzone w języku T-SQL, korzystających z sesji\_KONTEKSTU.
> Aby użyć domyślnego ograniczenia w przykładowym projekcie, na przykład należy usunąć TenantId DataClasses.cs (oraz wykonywania migracji Dodaj w konsoli Menedżera pakietów) i użyj T-SQL, aby upewnić się, że pole istnieje tylko w tabelach bazy danych. W ten sposób podczas wstawiania danych, EF automatycznie dostarczyć niepoprawne wartości domyślne.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcjonalnie) Włącz *administratora* na dostęp do wszystkich wierszy

Niektóre aplikacje, warto utworzyć *administratora* kto ma dostęp do wszystkich wierszy. Administratora może włączyć raportowanie dla wszystkich dzierżaw na wszystkich fragmentów. Lub administratora może wykonać operacji dzielenia i scalania na fragmenty, które tłumaczą wierszy dzierżawy między bazami danych.

Aby włączyć administratora, Utwórz nowego użytkownika SQL (`superuser` w tym przykładzie) w każdej bazie danych fragmentów. Następnie należy zmienić zasady zabezpieczeń z nowej funkcji predykatu, który umożliwia użytkownikowi dostęp do wszystkich wierszy. Taka funkcja znajduje się obok.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Konserwacja

- **Dodawanie nowych fragmentów**: Wykonywanie skryptu T-SQL, aby włączyć zabezpieczenia na poziomie wiersza w żadnych nowych fragmentów, w przeciwnym razie zapytań na tych fragmentach nie są filtrowane.
- **Dodawanie nowych tabel**: Dodaj predykat filtru i bloku zasady zabezpieczeń na wszystkich fragmentów zawsze wtedy, gdy tworzona jest nowa tabela. W przeciwnym razie kwerendy dla nowej tabeli nie są filtrowane. To dodawanie można zautomatyzować za pomocą wyzwalacza DDL, zgodnie z opisem w [stosowanie zabezpieczeń na poziomie wiersza automatycznie z nowo utworzonego tabelami (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Podsumowanie

Narzędzi elastycznej bazy danych i zabezpieczenia na poziomie wiersza mogą być używane razem do skalowania w poziomie warstwy danych aplikacji dzięki obsłudze zarówno z wieloma dzierżawami i jedną dzierżawą fragmentów. Wielodostępne fragmenty może służyć do przechowywania danych bardziej efektywnie. Wydajność jest widoczny, gdzie mają tylko kilka wierszy danych w dużej liczby dzierżawców. Fragmenty pojedynczej dzierżawy może obsługiwać dzierżaw — wersja premium, które mają bardziej rygorystyczne wydajności i wymagań izolacji. Aby uzyskać więcej informacji, zobacz [odniesienia zabezpieczeń na poziomie wiersza][rls].

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Co to jest pula elastyczna Azure?](sql-database-elastic-pool.md)
- [Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)](sql-database-elastic-scale-introduction.md)
- [Wzorce projektowe dla wielodostępnych aplikacji SaaS wykorzystujących usługę Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Uwierzytelnianie w aplikacjach wielodostępnych za pomocą usługi Azure AD i OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplikacja Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Pytania i sugestie funkcji

Masz pytania, skontaktuj się z nami na [forum bazy danych SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). I Dodaj wszelkie żądania funkcji [forum z opiniami bazy danych SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

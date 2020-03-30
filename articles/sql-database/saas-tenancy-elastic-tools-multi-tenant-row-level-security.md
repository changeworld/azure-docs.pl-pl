---
title: Aplikacje dla wielu dzierżawców z narzędziami RLS i elastycznej bazy danych
description: Użyj narzędzi elastycznej bazy danych z zabezpieczeniami na poziomie wiersza, aby utworzyć aplikację z wysoce skalowalną warstwą danych.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a5fe5d6d4076c5d82d33737d05bb95ede0a89c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822032"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplikacje wielodostępne z elastycznymi narzędziami bazy danych i zabezpieczeniami na poziomie wiersza

[Narzędzia elastycznej bazy danych](sql-database-elastic-scale-get-started.md) i [zabezpieczenia na poziomie wiersza (RLS)][rls] współpracują ze sobą, aby umożliwić skalowanie warstwy danych aplikacji wielodostępnej za pomocą usługi Azure SQL Database. Razem te technologie ułatwiają tworzenie aplikacji, która ma wysoce skalowalną warstwę danych. Warstwa danych obsługuje fragmenty wielu dzierżawców i używa **ADO.NET SqlClient** lub **Entity Framework.** Aby uzyskać więcej informacji, zobacz [Wzorce projektowe dla aplikacji SaaS z wieloma dzierżawcami z usługą Azure SQL Database](saas-tenancy-app-design-patterns.md).

- **Narzędzia elastycznej bazy danych** umożliwiają deweloperom skalowanie w poziomie warstwy danych za pomocą standardowych rozwiązań dzielenia na fragmenty przy użyciu bibliotek platformy .NET i szablonów usługi platformy Azure. Zarządzanie fragmentami przy użyciu [biblioteki klienta elastycznej bazy danych][s-d-elastic-database-client-library] pomaga zautomatyzować i usprawnić wiele zadań infrastrukturalnych zwykle skojarzonych z dzielenia na fragmenty.
- **Zabezpieczenia na poziomie wiersza** umożliwia deweloperom bezpiecznie przechowywać dane dla wielu dzierżaw w tej samej bazie danych. Zasady zabezpieczeń RLS odfiltrowują wiersze, które nie należą do dzierżawy wykonującej kwerendę. Centralizacja logiki filtru wewnątrz bazy danych upraszcza konserwację i zmniejsza ryzyko błędu zabezpieczeń. Alternatywa polegania na całym kodzie klienta w celu wymuszenia zabezpieczeń jest ryzykowna.

Za pomocą tych funkcji razem, aplikacja może przechowywać dane dla wielu dzierżaw w tej samej bazie danych niezależnego fragmentu. Kosztuje mniej na dzierżawę, gdy dzierżawcy współużytkować bazę danych. Jednak ta sama aplikacja może również zaoferować swoim dzierżawcom premium możliwość płacenia za własny dedykowany fragment pojedynczej dzierżawy. Jedną z zalet izolacji pojedynczej dzierżawy jest mocniejsze gwarancje wydajności. W bazie danych z jedną dzierżawą nie ma innej dzierżawy konkurującej o zasoby.

Celem jest użycie interfejsów API [routingu zależne od danych](sql-database-elastic-scale-data-dependent-routing.md) biblioteki klienta elastycznej bazy danych, aby automatycznie połączyć każdą daną dzierżawę z poprawną bazą danych niezależnego fragmentu. Tylko jeden fragment zawiera określoną wartość TenantId dla danej dzierżawy. Identyfikator dzierżawy jest *kluczem dzielenia na fragmenty*. Po nawiązaniu połączenia zasady zabezpieczeń RLS w bazie danych zapewniają, że dana dzierżawa może uzyskać dostęp tylko do tych wierszy danych, które zawierają jego Identyfikator Dzierżawy.

> [!NOTE]
> Identyfikator dzierżawy może składać się z więcej niż jednej kolumny. Dla wygody jest to omówienie, nieformalnie zakładamy jednokolumnowego Identyfikatora dzierżawy.

![Architektura aplikacji blogów][1]

## <a name="download-the-sample-project"></a>Pobierz przykładowy projekt

### <a name="prerequisites"></a>Wymagania wstępne

- Korzystanie z programu Visual Studio (2012 lub nowszego)
- Tworzenie trzech baz danych SQL platformy Azure
- Pobierz przykładowy projekt: [Elastyczne narzędzia bazy danych dla usługi Azure SQL — odłamki z wieloma dzierżawkami](https://go.microsoft.com/?linkid=9888163)
  - Wypełnij informacje dotyczące baz danych na początku **Program.cs**

Ten projekt rozszerza ten opisany w [narzędziach elastycznych baz danych dla integracji programu Azure SQL — Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) przez dodanie obsługi baz danych niezależnego fragmentu wielu dzierżawców. Projekt tworzy prostą aplikację konsoli do tworzenia blogów i wpisów. Projekt obejmuje czterech dzierżawców oraz dwie wielodostępne bazy danych niezależnego fragmentu. Ta konfiguracja jest zilustrowana na poprzednim diagramie.

Skompiluj i uruchom aplikację. Ten uruchom bootstraps menedżera mapy niezależnej bazy danych narzędzia elastycznej bazy danych i wykonuje następujące testy:

1. Korzystając z entity framework i LINQ, utwórz nowy blog, a następnie wyświetl wszystkie blogi dla każdej dzierżawy
2. Za pomocą ADO.NET SqlClient, wyświetlić wszystkie blogi dla dzierżawy
3. Spróbuj wstawić bloga dla niewłaściwej dzierżawy, aby sprawdzić, czy błąd jest zgłaszany

Należy zauważyć, że ponieważ RLS nie została jeszcze włączona w bazach danych niezależnego fragmentu, każdy z tych testów ujawnia problem: dzierżawcy są w stanie zobaczyć blogi, które nie należą do nich, a aplikacja nie jest uniemożliwiona wstawiania blogu dla niewłaściwej dzierżawy. W dalszej części tego artykułu opisano, jak rozwiązać te problemy, wymuszając izolację dzierżawy za pomocą protokołu RLS. Istnieją dwa kroki:

1. **Warstwa aplikacji**: Zmodyfikuj kod aplikacji, aby zawsze ustawiać bieżący identyfikator dzierżawy w kontekście sesji\_po otwarciu połączenia. Przykładowy projekt już ustawia TenantId w ten sposób.
2. **Warstwa danych:** Utwórz zasady zabezpieczeń RLS w każdej bazie danych niezależnego\_fragmentu, aby filtrować wiersze na podstawie identyfikatora TenantId przechowywanego w kontekście sesji. Tworzenie zasad dla każdej z baz danych niezależnego fragmentu, w przeciwnym razie wiersze w fragmentach wielu dzierżawców nie są filtrowane.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. Warstwa aplikacji: Ustaw\_identyfikator dzierżawy w kontekście sesji

Najpierw połączyć się z bazą danych niezależnego fragmentu przy użyciu zależnych od danych routingu interfejsów API biblioteki klienta elastycznej bazy danych. Aplikacja nadal musi poinformować bazę danych, który Identyfikator dzierżawy używa połączenia. Identyfikator dzierżawy informuje zasady zabezpieczeń RLS, które wiersze muszą być odfiltrowane jako należące do innych dzierżawców. Przechowywać bieżący identyfikator dzierżawy w [kontekście sesji\_](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) połączenia.

Alternatywą dla\_SESSION CONTEXT jest użycie [CONTEXT\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Ale\_SESSION CONTEXT jest lepszym rozwiązaniem. SESSION\_CONTEXT jest łatwiejszy w użyciu, domyślnie zwraca wartość NULL i obsługuje pary klucz-wartość.

### <a name="entity-framework"></a>Entity Framework

W przypadku aplikacji korzystających z entity framework\_najprostszym rozwiązaniem jest ustawienie kontekstu sesji w ramach zastąpienia ElasticScaleContext opisanego w [routingu zależnym od danych przy użyciu ef DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Tworzenie i wykonywanie SqlCommand, który ustawia\_TenantId w kontekście sesji do shardingKey określony dla połączenia. Następnie zwraca połączenie pośredniczące za pośrednictwem routingu zależnego od danych. W ten sposób wystarczy napisać kod tylko\_raz, aby ustawić kontekst sesji.

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

Teraz kontekst\_sesji jest automatycznie ustawiany z określonym identyfikatorem tenantid, gdy wywoływany jest elasticscalecontext:

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

W przypadku aplikacji korzystających ADO.NET SqlClient utwórz funkcję otoki wokół metody ShardMap.OpenConnectionForKey. Otoka automatycznie ustaw tenantid w\_kontekście sesji do bieżącego TenantId przed zwróceniem połączenia. Aby upewnić\_się, że session context jest zawsze ustawiony, należy otwierać tylko połączenia przy użyciu tej funkcji otoki.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Warstwa danych: tworzenie zasad zabezpieczeń na poziomie wiersza

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Tworzenie zasad zabezpieczeń w celu filtrowania wierszy, do których może uzyskiwać dostęp każda dzierżawa

Teraz, gdy aplikacja\_jest ustawienie session context z bieżącego TenantId przed kwerendą, zasady zabezpieczeń RLS można filtrować kwerendy i wykluczyć wiersze, które mają inny Identyfikator TenantId.

RLS jest zaimplementowany w Transact-SQL. Funkcja zdefiniowana przez użytkownika definiuje logikę dostępu, a zasady zabezpieczeń wiążą tę funkcję z dowolną liczbą tabel. Dla tego projektu:

1. Funkcja sprawdza, czy aplikacja jest podłączona do bazy danych i\_czy Identyfikator dzierżawy przechowywany w session context pasuje do identyfikatora tenantid danego wiersza.
    - Aplikacja jest połączona, a nie inny użytkownik SQL.

2. Predykat FILTRU umożliwia wierszom, które spełniają tenantid filtru przejść przez dla kwerend SELECT, UPDATE i DELETE.
    - Predykat BLOKU zapobiega wierszom, które nie mogą być inserted lub updated.
    - Jeśli\_kontekst sesji nie został ustawiony, funkcja zwraca wartość NULL, a żadne wiersze nie są widoczne ani można wstawić.

Aby włączyć RLS na wszystkich fragmentów, należy wykonać następujące T-SQL przy użyciu programu Visual Studio (SSDT), SSMS lub skrypt programu PowerShell zawarte w projekcie. Lub jeśli używasz [zadania elastycznej bazy danych,](elastic-jobs-overview.md)można zautomatyzować wykonywanie tego T-SQL na wszystkich fragmentów.

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
> W złożonym projekcie może być konieczne dodanie predykatu na setkach tabel, co może być uciążliwe. Istnieje procedura składowana pomocnika, która automatycznie generuje zasady zabezpieczeń i dodaje predykat dla wszystkich tabel w schemacie. Aby uzyskać więcej informacji, zobacz wpis w blogu w temacie Stosowanie zabezpieczeń na [poziomie wiersza do wszystkich tabel — skrypt pomocniczy (blog).](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script)

Teraz po ponownym uruchomieniu przykładowej aplikacji dzierżawcy zobaczą tylko wiersze, które do nich należą. Ponadto aplikacja nie można wstawić wiersze, które należą do dzierżaw innych niż obecnie połączone z bazy danych niezależnego fragmentu. Ponadto aplikacja nie może zaktualizować Identyfikatora dzierżawy w wierszach, które może zobaczyć. Jeśli aplikacja próbuje zrobić albo, DbUpdateException jest wywoływana.

Jeśli później dodasz nową tabelę, zmień zasady zabezpieczeń, aby dodać predykaty FILTER i BLOCK w nowej tabeli.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Dodawanie ograniczeń domyślnych w celu automatycznego wypełniania identyfikatora tenantid dla insertów

Można umieścić domyślne ograniczenie w każdej tabeli, aby automatycznie wypełnić TenantId\_z wartością aktualnie przechowywaną w kontekście sesji podczas wstawiania wierszy. Poniższy przykład.

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

Teraz aplikacja nie musi określać Identyfikatora Dzierżawcy podczas wstawiania wierszy:

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
> Jeśli używasz ograniczeń domyślnych dla projektu entity framework, zaleca się, aby *nie* dołączyć TenantId kolumny w modelu danych EF. To zalecenie jest, ponieważ kwerendy Entity Framework automatycznie podać wartości domyślne, które zastępują domyślne ograniczenia utworzone w T-SQL, które używają session\_context.
> Aby użyć ograniczeń domyślnych w przykładowym projekcie, na przykład należy usunąć identyfikator dzierżawy z DataClasses.cs (i uruchomić migrację dodatków w konsoli Menedżera pakietów) i użyć funkcji T-SQL, aby upewnić się, że pole istnieje tylko w tabelach bazy danych. W ten sposób EF automatycznie dostarcza nieprawidłowe wartości domyślne podczas wstawiania danych.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcjonalnie) Włącz *superużytnik,* aby uzyskać dostęp do wszystkich wierszy

Niektóre aplikacje mogą chcieć utworzyć *superużytnika,* który może uzyskać dostęp do wszystkich wierszy. Superużytkownik może włączyć raportowanie wszystkich dzierżaw na wszystkich fragmentów. Lub superuser może wykonywać operacje scalania podziału na fragmenty, które obejmują przenoszenie wierszy dzierżawy między bazami danych.

Aby włączyć superużytnika, utwórz`superuser` nowego użytkownika SQL (w tym przykładzie) w każdej bazie danych niezależnego fragmentu. Następnie zmień zasady zabezpieczeń za pomocą nowej funkcji predykatu, która umożliwia temu użytkownikowi dostęp do wszystkich wierszy. Taka funkcja jest podana dalej.

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

- **Dodawanie nowych fragmentów:** Wykonaj skrypt T-SQL, aby włączyć RLS na nowe fragmenty, w przeciwnym razie kwerendy na tych fragmentów nie są filtrowane.
- **Dodawanie nowych tabel:** Dodaj predykat FILTER i BLOCK do zasad zabezpieczeń na wszystkich fragmentach przy każdym utworzeniu nowej tabeli. W przeciwnym razie kwerendy w nowej tabeli nie są filtrowane. Ten dodatek można zautomatyzować za pomocą wyzwalacza DDL, zgodnie z opisem w [Zastosuj zabezpieczenia na poziomie wiersza automatycznie do nowo utworzonych tabel (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Podsumowanie

Narzędzia elastycznej bazy danych i zabezpieczenia na poziomie wiersza mogą być używane razem do skalowania w poziomie warstwy danych aplikacji z obsługą fragmentów wielu dzierżawców i pojedynczych dzierżawców. Fragmenty wielu dzierżawców mogą służyć do bardziej efektywnego przechowywania danych. Ta wydajność jest wymawiane, gdy duża liczba dzierżawców ma tylko kilka wierszy danych. Fragmenty pojedynczej dzierżawy mogą obsługiwać dzierżawy premium, które mają bardziej rygorystyczne wymagania dotyczące wydajności i izolacji. Aby uzyskać więcej informacji, zobacz [odwołanie do zabezpieczeń na poziomie wiersza][rls].

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Co to jest pula elastyczna Azure?](sql-database-elastic-pool.md)
- [Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)](sql-database-elastic-scale-introduction.md)
- [Wzorce projektowe dla wielodostępnych aplikacji SaaS wykorzystujących usługę Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Uwierzytelnianie w aplikacjach wielodostępnych za pomocą usługi Azure AD i OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplikacja Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Pytania i prośby o funkcje

W przypadku pytań skontaktuj się z nami na [forum bazy danych SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). I dodać wszelkie żądania funkcji do [forum opinii bazy danych SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

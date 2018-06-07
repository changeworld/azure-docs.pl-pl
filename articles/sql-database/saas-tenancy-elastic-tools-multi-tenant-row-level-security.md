---
title: Aplikacje wielodostępne kontrola dostępu i narzędzi elastycznej bazy danych | Dokumentacja firmy Microsoft
description: Użyj narzędzi elastycznej bazy danych z zabezpieczeniami na poziomie wiersza, aby skompilować aplikację z warstwą danych wysokiej skalowalności.
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
manager: craigg
author: tmullaney
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: thmullan
ms.openlocfilehash: 02ad01185a86aa5a975be2a66b54a214029dd73f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645814"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplikacje wielodostępne z narzędzi elastycznej bazy danych i zabezpieczenia na poziomie wiersza

[Narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md) i [zabezpieczeń na poziomie wiersza] [ rls] współpracują w celu włączenia skalowania warstwy danych wielodostępnych aplikacji z bazy danych SQL Azure. Ze sobą te technologie pomocne w tworzeniu aplikacji, która ma warstwy danych wysokiej skalowalności. Warstwa danych obsługuje wielodostępne odłamków i wykorzystuje **ADO.NET SqlClient** lub **Entity Framework**. Aby uzyskać więcej informacji, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS przy użyciu usługi Azure SQL Database](saas-tenancy-app-design-patterns.md).

- **Narzędzi elastycznej bazy danych** umożliwia deweloperom skalowania warstwy danych z rozwiązaniami standardowe dzielenia na fragmenty, przy użyciu bibliotek .NET i szablony usługi Azure. Zarządzanie za pomocą odłamków [elastycznej bazy danych klienta biblioteki] [ s-d-elastic-database-client-library] pomaga zautomatyzować i uprościć wiele zadań infrastrukturalne zwykle skojarzone z dzielenia na fragmenty.
- **Wiersz poziomu zabezpieczeń** umożliwia deweloperom bezpiecznie przechowywać dane dla wielu dzierżawców w tej samej bazy danych. Zasady zabezpieczeń zabezpieczenia na poziomie wiersza odfiltrować wiersze, które nie należą do dzierżawy wykonywanie zapytania. Scentralizowany logiki filtru w bazie danych upraszcza konserwację i zmniejsza ryzyko błędu zabezpieczeń. Alternatywą dla jednostki uzależnionej na wszystkich kod klienta w celu zabezpieczenia enfore jest ryzykowne.

Przy użyciu tych funkcji ze sobą, aplikacja może przechowywać dane dla wielu dzierżawców w bazie danych sam identyfikator niezależnego fragmentu. Kosztuje mniej dla każdego dzierżawcy podczas dzierżawcami współużytkują bazę danych. Jeszcze tej samej aplikacji można również umożliwiają jego dzierżaw premium płatność za swoje własne dedykowane niezależnych pojedynczej dzierżawy. Korzyścią izolacji pojedynczej dzierżawy jest lepszych gwarancji wydajności. W bazie danych pojedynczej dzierżawy nie ma żadnych innych dzierżawy konkurowanie o zasoby.

Celem jest korzystanie z biblioteki klienta elastycznej bazy danych [routingu zależne od danych](sql-database-elastic-scale-data-dependent-routing.md) interfejsów API automatyczne łączenie się poprawny identyfikator niezależnego fragmentu bazy danych z każdej danej dzierżawy. Tylko jeden identyfikator niezależnego fragmentu zawiera konkretną wartość TenantId dla danej dzierżawy. TenantId jest *klucza dzielenia na fragmenty*. Po nawiązaniu połączenia zasady zabezpieczeń zabezpieczenia na poziomie wiersza w bazie danych zapewnia, że danej dzierżawy mają dostęp do tylko te wiersze danych, które zawierają jego identyfikatora dzierżawcy.

> [!NOTE]
> Identyfikator dzierżawy może składać się z więcej niż jedną kolumnę. Dla wygody jest rozważania, przyjęto założenie nieformalnego TenantId pojedynczej kolumny.

![Architektura aplikacji obsługi blogów][1]

## <a name="download-the-sample-project"></a>Pobierz przykładowy projekt

### <a name="prerequisites"></a>Wymagania wstępne

- Za pomocą programu Visual Studio (2012 lub nowszy) 
- Utwórz trzy bazy danych SQL Azure 
- Pobierz przykładowy projekt: [elastyczne narzędzia bazy danych dla bazy danych SQL Azure - odłamków wielodostępne](http://go.microsoft.com/?linkid=9888163)
  - Wprowadź informacje dla baz danych na początku **Program.cs** 

Ten projekt rozszerza opisanych w [elastyczne narzędzia bazy danych dla bazy danych SQL Azure - Entity Framework integracji](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) przez dodanie obsługi wielodostępne niezależnego fragmentu bazy danych. Projekt tworzy prostej aplikacji konsolowej tworzenia blogów i wpisów. Projekt zawiera cztery dzierżawców, a także dwóch niezależnych wielodostępnych baz danych. Na powyższym diagramie przedstawiono tę konfigurację. 

Skompiluj i uruchom aplikację. Tego uruchomienia używa do ładowania menedżera map niezależnego fragmentu narzędzi elastycznej bazy danych, a następnie wykonuje następujące testy: 

1. Przy użyciu programu Entity Framework i LINQ, Utwórz nowy blog, a następnie Wyświetl wszystkie blogi dla każdego dzierżawcy
2. Za pomocą ADO.NET SqlClient, wyświetlić wszystkie blogi dla dzierżawy
3. Próba wstawienia blogu dla niewłaściwego dzierżawcy sprawdzić, czy błąd jest zgłaszany  

Powiadomienie, że ponieważ zabezpieczenia na poziomie wiersza nie została jeszcze włączona w bazach danych niezależnego fragmentu, tych testów ujawnia problem: dzierżawcy będą mogli zobaczyć blogów, które nie należą do nich, a aplikacja nie będzie mógł Wstawianie blogu dla niewłaściwego dzierżawcy. W dalszej części tego artykułu opisano, jak rozwiązać te problemy przez wymuszania izolacji dzierżawców odświeżania. Istnieją dwa kroki: 

1. **Warstwy aplikacji**: modyfikowanie kodu aplikacji, aby zawsze ustawić bieżącego identyfikatora dzierżawcy w sesji\_KONTEKSTU po otwarciu połączenia. Przykładowy projekt już ustawia TenantId w ten sposób. 
2. **Warstwa danych**: Tworzenie zasad zabezpieczeń zabezpieczenia na poziomie wiersza w każdej bazy danych niezależnych do filtru wierszy oparty na TenantId przechowywanych w sesji\_KONTEKSTU. Tworzenie zasad dla każdej z baz danych niezależnego fragmentu, w przeciwnym razie wierszy w wielu dzierżawcy fragmentów nie są filtrowane. 

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Warstwy aplikacji: zestaw TenantId w sesji\_KONTEKSTU

Najpierw należy nawiązać niezależnego fragmentu bazy danych przy użyciu interfejsów API routingu zależne od danych biblioteki klienta elastycznej bazy danych. Aplikacja nadal należy wskazać bazy danych które identyfikatora dzierżawcy jest przy użyciu połączenia. Identyfikatora dzierżawy określa, że zasady zabezpieczeń zabezpieczenia na poziomie wiersza wiersze, które muszą odfiltrowane jako należące do innych dzierżawców. Przechowywanie bieżącego identyfikatora dzierżawcy w [sesji\_KONTEKSTU](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) połączenia.

Zamiast sesji\_KONTEKSTU jest użycie [KONTEKSTU\_informacji](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Ale sesji\_kontekst jest lepszym rozwiązaniem. SESJA\_kontekst jest łatwiejsza w użyciu, domyślnie zwraca wartość NULL i obsługuje pary klucz wartość.

### <a name="entity-framework"></a>Entity Framework

Dla aplikacji za pomocą programu Entity Framework, najprostszym podejście jest skonfigurowanie sesji\_KONTEKSTU w zastąpienie ElasticScaleContext opisanego w [danych zależne od routingu przy użyciu EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Tworzenie i wykonywanie SqlCommand, która ustawia dla identyfikatora dzierżawcy w sesji\_kontekst shardingKey określony dla połączenia. Następnie wróć połączenia przeprowadzana za pośrednictwem zależne od danych routingu. Dzięki temu wystarczy raz napisać kod, aby ustawić sesji\_KONTEKSTU. 

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

Teraz sesji\_KONTEKSTU jest ustawiany automatycznie z określonego identyfikatora dzierżawcy przy każdym wywołaniu ElasticScaleContext: 

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

Dla aplikacji za pomocą ADO.NET SqlClient Utwórz funkcję otoki wokół metody ShardMap.OpenConnectionForKey. Otoki automatycznie ustawione dla identyfikatora dzierżawcy w sesji mają\_kontekst do bieżącego identyfikatora dzierżawcy przed zwróceniem połączenia. Zapewnienie, sesji\_KONTEKSTU ma zawsze wartość, należy otworzyć tylko połączeń za pomocą tej funkcji otoki.

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

Teraz, gdy aplikacja jest ustawienie sesji\_KONTEKSTU za pomocą bieżącego identyfikatora dzierżawcy przed wykonaniem kwerendy, można filtrować zasady zabezpieczeń zabezpieczenia na poziomie wiersza, zapytań i Wyklucz wiersze, które mają różne identyfikatora dzierżawcy.  

Kontrola dostępu jest zaimplementowana w języku Transact-SQL. Logika dostępu definiuje funkcję zdefiniowaną przez użytkownika, a zasady zabezpieczeń wiąże tę funkcję na dowolną liczbę tabel. Dla tego projektu:

1. Funkcja sprawdza, czy aplikacja jest połączony z bazą danych i przechowywanie TenantId w sesji\_KONTEKSTU identyfikatora dzierżawcy danego wiersza jest zgodna.
    - Aplikacja jest połączony, zamiast innego użytkownika SQL.

2. Predykat filtru umożliwia wierszy, które spełnia filtru TenantId do przekazywania do SELECT, UPDATE i usuwanie kwerend.
    - Predykat bloku zapobiega wierszy, które nie są filtr z wstawione lub zaktualizowane.
    - Jeśli SESJA\_KONTEKSTU nie został ustawiony, funkcja zwraca wartość NULL i nie ma wierszy widocznych lub możliwe do wstawienia. 

Aby włączyć zabezpieczenia na poziomie wiersza na wszystkich fragmentów, należy wykonać T-SQL przy użyciu programu Visual Studio (SSDT), SSMS lub skrypt programu PowerShell dołączony do projektu. Lub jeśli używasz [zadania elastyczne bazy danych](sql-database-elastic-jobs-overview.md), można zautomatyzować wykonywanie tego T-SQL na wszystkich fragmentów.

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
> W złożonych projektu, który może być konieczne jest dodanie predykat na setki tabel, która może być niewygodny. Brak procedury przechowywane pomocnika, która automatycznie generuje zasady zabezpieczeń i dodaje predykat dla wszystkich tabel w schemacie. Aby uzyskać więcej informacji, zobacz blogu w [zastosowania zabezpieczeń na poziomie wiersza do wszystkich tabel - pomocnika skryptu (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Teraz po uruchomieniu aplikacji przykładowej ponownie dzierżaw widoczne tylko wiersze, które należą do nich. Ponadto aplikacja nie można wstawić wierszy, które należą do innych niż aktualnie połączony z bazą danych niezależnych dzierżawcy. Ponadto aplikacja nie można zaktualizować identyfikatora dzierżawcy w wszystkie wiersze, które mogą być widoczne. Jeśli aplikacja próbuje zrobić, DbUpdateException jest wywoływane.

Jeśli później Dodaj nową tabelę zmienić zasady zabezpieczeń, aby dodać predykatów filtru i bloku je w nowej tabeli.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Dodaj domyślne ograniczenia, aby automatycznie wypełnić TenantId dla operacji wstawienia

Możesz też zaznaczyć ograniczeniu domyślnym w każdej tabeli, aby automatycznie wypełnić TenantId o wartości przechowywane w sesji\_kontekście podczas wstawiania wierszy. Przykładem jest zgodna. 

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

Teraz aplikacji nie trzeba określać identyfikatora dzierżawcy podczas wstawiania wierszy: 

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
> Jeśli jest używane domyślne ograniczenia projektu programu Entity Framework, zaleca się że *nie* zawierać kolumny identyfikatora dzierżawcy w modelu danych EF. To zalecenie wynika Entity Framework automatycznie kwerendy podaj wartości domyślne, które zastępują domyślne ograniczenia utworzone w T-SQL korzystających z sesji\_KONTEKSTU.
> Aby użyć domyślnego ograniczenia w przykładowy projekt, na przykład, należy usunąć TenantId z DataClasses.cs (i wykonywania Add-Migration w konsoli Menedżera pakietów) i użyj T-SQL, aby upewnić się, że pole istnieje tylko w tabelach bazy danych. Dzięki temu podczas wstawiania danych, EF automatycznie dostarczyć niepoprawne wartości domyślne.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcjonalnie) Włącz *administratora* można uzyskać dostępu do wszystkich wierszy

Niektóre aplikacje może być konieczne utworzenie *administratora* kto ma dostęp do wszystkich wierszy. Administratora może włączyć raportowania we wszystkich dzierżawców na wszystkich fragmentów. Lub administratora może wykonać operacji scalania podziału na odłamków obejmujących przesuwanie wierszy dzierżawy między bazami danych.

Aby włączyć administratora, utworzenie nowego użytkownika SQL (`superuser` w tym przykładzie) w każdym niezależnego fragmentu bazy danych. Następnie należy zmienić zasady zabezpieczeń z nowej funkcji predykatu, która umożliwia użytkownikom dostęp do wszystkich wierszy. Dalej znajduje się takiej funkcji.

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

- **Dodawanie nowych odłamków**: wykonanie skryptu T-SQL, aby włączyć zabezpieczenia na poziomie wiersza na wszelkich nowych fragmentów, w przeciwnym razie kwerendy dotyczące tych fragmentów nie są filtrowane.
- **Dodawanie nowych tabel**: dodanie predykatu filtru i bloku zasady zabezpieczeń na wszystkich odłamków nowa tabela została utworzona. W przeciwnym razie zapytań w nowej tabeli nie są filtrowane. To dodawanie można zautomatyzować za pomocą wyzwalacza DDL, zgodnie z opisem w [zastosowania zabezpieczeń na poziomie wiersza automatycznie na nowo utworzony tabele (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Podsumowanie

Narzędzi elastycznej bazy danych i zabezpieczenia na poziomie wiersza mogą być używane razem do skalowania aplikacji warstwy danych o obsługę zarówno wielodostępnych i pojedynczego dzierżawcy fragmentów. Wielodostępne odłamków może służyć do przechowywania danych bardziej efektywnie. Takie zwiększenie efektywności jest widoczny, gdzie wiele dzierżaw mieć tylko kilka wierszy danych. Pojedynczego dzierżawcy fragmentów może obsługiwać dzierżaw premium, których bardziej rygorystyczne wydajności i wymagania dotyczące izolacji.  Aby uzyskać więcej informacji, zobacz [informacje dotyczące zabezpieczeń na poziomie wiersza][rls].

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Co to jest puli elastycznej platformy Azure?](sql-database-elastic-pool.md)
- [Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)](sql-database-elastic-scale-introduction.md)
- [Wzorce projektowe dla wielodostępnych aplikacji SaaS wykorzystujących usługę Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Uwierzytelnianie w aplikacjach wielodostępnych za pomocą usługi Azure AD i OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplikacja Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Pytania i żądania funkcji

Odpowiedzi na pytania, skontaktuj się z nami na [forum bazy danych SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). I dodać wszystkie żądania funkcji, aby [forum opinii bazy danych SQL](https://feedback.azure.com/forums/217321-sql-database/).


<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md


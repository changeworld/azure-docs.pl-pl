---
title: Korzystanie z biblioteki klienta elastycznej bazy danych z Dapper | Dokumentacja firmy Microsoft
description: Za pomocą biblioteki klienta elastycznej bazy danych z Dapper.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 6619f2dfe5f58cd23dbd0ffe6e2b545b803f3acc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647932"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Korzystanie z biblioteki klienta elastycznej bazy danych z Dapper
Ten dokument jest przeznaczony dla deweloperów, które zależą od Dapper do tworzenia aplikacji, ale chce również obejmować [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md) do tworzenia aplikacji w tej implementacji dzielenia na fragmenty do skalowania w poziomie ich warstwy danych.  Ten dokument przedstawia zmiany w aplikacjach opartych na Dapper, które są niezbędne do integracji z narzędzi elastycznej bazy danych. Nasze skoncentrować się na tworzenie elastycznej bazy danych zarządzania niezależnego fragmentu i zależne od danych routingu z Dapper. 

**Przykładowy kod**: [narzędzi elastycznej bazy danych dla usługi Azure SQL Database — integracja Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrowanie **Dapper** i **DapperExtensions** w elastycznej bazie danych jest łatwe Biblioteka klienta w bazie danych SQL Azure. Aplikacje mogą korzystać z zależne od danych routingu przez zmianę tworzenia i otwierania nowych [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) obiektów do użycia [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) wywoływać z [biblioteki klienta ](http://msdn.microsoft.com/library/azure/dn765902.aspx). Ogranicza to zmian w aplikacji tylko w przypadku, gdy nowe połączenia są tworzone i otworzyć. 

## <a name="dapper-overview"></a>Dapper — omówienie
**Dapper** jest mapowania obiektów relacyjnych. Mapuje obiekty .NET z aplikacji relacyjnej bazy danych (i na odwrót). Pierwsza część przykładowy kod przedstawia sposób integrowania biblioteki klienta elastycznej bazy danych z aplikacji opartych na Dapper. Druga część przykładowy kod ilustruje sposób integracji podczas używania zarówno Dapper i DapperExtensions.  

Funkcjonalność mapowania Dapper udostępnia metody rozszerzenia na połączenia z bazą danych, które upraszczają przesyłanie instrukcje T-SQL do wykonania lub zapytań bazy danych. Na przykład Dapper ułatwia mapowanie między obiektów platformy .NET i parametrów instrukcji SQL dla **Execute** wywołań, lub użycie wyników zapytań SQL na obiekty .NET przy użyciu **zapytania** wywołania z Dapper. 

Korzystając z DapperExtensions, nie trzeba podać instrukcji SQL. Metody rozszerzenia, takie jak **GetList** lub **Wstaw** za pośrednictwem połączenia z bazą danych tworzenia instrukcji SQL, w tle.

Inną zaletą Dapper, a także DapperExtensions jest, że aplikacja kontroluje tworzenie połączenia z bazą danych. Dzięki temu interakcję z biblioteki klienta elastycznej bazy danych, która brokerzy bazy danych połączenia na podstawie mapowania shardlets do baz danych.

Zestawy Dapper, można znaleźć [Dapper kropka net](http://www.nuget.org/packages/Dapper/). Dapper rozszerzeń, zobacz [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Krótki przegląd biblioteki klienta elastycznej bazy danych
Za pomocą biblioteki klienta elastycznej bazy danych, można definiować partycje danych aplikacji o nazwie *shardlets*mapować je do baz danych i identyfikację przez *klucze dzielenia na fragmenty*. Może mieć dowolną liczbę baz danych i rozpowszechniają shardlets z tych baz danych. Mapowanie dzielenia na fragmenty wartości klucza do bazy danych jest przechowywany przez mapy niezależnego fragmentu udostępniane przez interfejsy API biblioteki. Ta funkcja jest wywoływana **zarządzania mapy niezależnego fragmentu**. Mapa niezależnego fragmentu służy również jako broker połączeń z bazą danych dla żądań zawierających klucz dzielenia na fragmenty. Ta funkcja jest określana jako **routingu zależne od danych**.

![Mapy niezależnego fragmentu i zależne od danych routingu][1]

Menedżer mapy niezależnego fragmentu chroni użytkowników z widoków niespójne w shardlet dane, które mogą wystąpić, gdy shardlet równoczesnych operacji zarządzania są wykonywane w bazach danych. Aby to zrobić, mapy niezależnego fragmentu broker połączeń bazy danych dla aplikacji skompilowanej za pomocą biblioteki. Podczas operacji zarządzania niezależnego fragmentu może mieć wpływ na shardlet, dzięki temu funkcja mapy niezależnego fragmentu automatycznie kill połączenia z bazą danych. 

Zamiast używać w tradycyjny sposób, aby utworzyć połączenia dla Dapper, należy użyć [metody OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). Dzięki temu wszystkie weryfikacji ma miejsce a połączenia są zarządzane prawidłowo, gdy wszystkie dane są przenoszone między fragmentów.

### <a name="requirements-for-dapper-integration"></a>Wymagania dotyczące integracji Dapper
Podczas pracy z biblioteki klienta elastycznej bazy danych i Dapper interfejsów API, ma zostać zachowany następujące właściwości:

* **Skalowanie w poziomie**: chcemy, aby dodać lub usunąć baz danych z aplikacji podzielonej odpowiednio do potrzeb pojemność aplikacji warstwy danych. 
* **Spójność**: ponieważ aplikacja jest skalowana w poziomie przy użyciu dzielenia na fragmenty, należy przeprowadzić zależne od danych routingu. Chcemy używać funkcji routingu zależne od danych biblioteki, aby to zrobić. W szczególności ma zostać zachowany sprawdzania poprawności i spójność gwarantuje podał połączeń, które są obsługiwane przez brokera za pośrednictwem Menedżera mapy niezależnego fragmentu Aby uniknąć uszkodzenia lub wyników zapytania niewłaściwy. Dzięki temu, że połączenia z danym shardlet są odrzucone lub zatrzymana, jeśli (na przykład) shardlet obecnie zostanie przeniesiony na inny identyfikator niezależnego fragmentu przy użyciu interfejsów API podziału/Merge.
* **Mapowanie obiektu**: Jeśli chcesz zachować wygodę mapowania podał Dapper do tłumaczenia klas w aplikacji i podstawowej struktury bazy danych. 

Poniższa sekcja zawiera wskazówki dotyczące tych wymagań aplikacji na podstawie **Dapper** i **DapperExtensions**.

## <a name="technical-guidance"></a>Wskazówki techniczne
### <a name="data-dependent-routing-with-dapper"></a>Zależne od danych routingu z Dapper
Z Dapper aplikacja jest zazwyczaj odpowiedzialny za tworzenie i otwieranie połączenia z podstawowej bazy danych. Określony typ T przez aplikację, Dapper zwraca wyniki zapytania kolekcji .NET typu T. Dapper wykonuje mapowanie z wierszy wynik T-SQL do obiektów typu T. Podobnie Dapper mapuje obiekty .NET do wartości SQL lub parametrów dla instrukcji języka manipulacji danych. Dapper oferuje tę funkcję za pomocą metod rozszerzenia na zwykłej [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) obiektu z bibliotek klienta SQL ADO .NET. Połączenie SQL zwrócony przez interfejsy API elastycznego skalowania dla DDR są także regularne [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) obiektów. Pozwala on bezpośrednio przy użyciu rozszerzenia Dapper przez typ zwracany przez interfejs API DDR biblioteki klienckiej, ponieważ jest on również prostego połączenia klienta SQL.

Uwagi te należy bezpośrednie użycie połączeń obsługiwanych przez brokera biblioteki klienta elastycznej bazy danych w przypadku Dapper.

W tym przykładzie kodu (z towarzyszącym próbki) przedstawiono podejście, gdzie klucz dzielenia na fragmenty jest dostarczany przez aplikację do biblioteki broker połączenie prawym niezależnego fragmentu.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

Wywołanie [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) interfejsu API zastępuje domyślną tworzenia i otwierania połączenia klienta SQL. [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) wywołania przyjmuje argumenty, które są wymagane dla routingu zależne od danych: 

* Mapa niezależnych można uzyskać dostępu do interfejsów routingu zależne od danych
* Klucz dzielenia na fragmenty, aby zidentyfikować shardlet
* Poświadczenia (nazwę użytkownika i hasło), aby nawiązać połączenie niezależnego fragmentu

Obiekt mapy niezależnego fragmentu tworzy połączenie niezależnego fragmentu, przechowujący shardlet klucza danego dzielenia na fragmenty. Interfejsów API klienta elastycznej bazy danych również tagu połączenia do wykonania jego gwarancje spójności. Od czasu wywołania [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) zwraca regularne obiektu połączenia klienta SQL, kolejne wywołanie **Execute** — metoda rozszerzenia z Dapper wykonuje standardową praktyką Dapper.

Zapytania działają bardzo podobnie jak — otwieranie połączenia za pomocą [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) z klienta interfejsu API. Następnie użyj metody regularne rozszerzenia Dapper mapować na obiekty .NET wyników kwerendy SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Należy pamiętać, że **przy użyciu** zablokować z zakresami połączenia DDR wszystkie operacje bazy danych w bloku do jednego niezależnego fragmentu, gdzie jest przechowywana tenantId1. Zapytanie zwraca tylko blogi przechowywane na bieżący identyfikator niezależnego fragmentu, ale nie te przechowywane na dowolnej liczbie fragmentów. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Zależne od danych routingu z Dapper i DapperExtensions
Dapper pochodzi z ekosystemem dodatkowe rozszerzenia, które udostępniają dalsze zwiększenie wygody działania i abstrakcji z bazy danych podczas opracowywania aplikacji bazy danych. Przykładem jest DapperExtensions. 

W aplikacji przy użyciu DapperExtensions nie zmienia sposób połączenia bazy danych są tworzone i zarządzane. Nadal jest odpowiedzialny za aplikacji, aby otworzyć aplet połączenia, a regularne obiekty połączenia klienta SQL są oczekiwane przez metody rozszerzenia. Firma Microsoft może polegać na [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) w sposób opisany powyżej. Jak wyświetlić następujące przykłady kodu, jedyna różnica polega na nie będzie trzeba zapisać instrukcje T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

A Oto przykładowy kod zapytania: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Obsługa błędów przejściowych
Zespół Microsoft Patterns & wskazówki opublikowane [bloku aplikacji obsługi błędów przejściowych](http://msdn.microsoft.com/library/hh680934.aspx) ułatwia deweloperom aplikacji ograniczyć typowe warunki wystąpienia błędu przejściowego napotkała podczas uruchamiania w chmurze. Aby uzyskać więcej informacji, zobacz [Perseverance, klucz tajny wszystkie sukcesy: za pomocą bloku przejściowej aplikacji obsługi błędów](http://msdn.microsoft.com/library/dn440719.aspx).

Przykładowy kod korzysta z biblioteki błędu przejściowego, aby zapewnić ochronę przed błędów przejściowych. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** w powyższym kodzie jest zdefiniowany jako **SqlDatabaseTransientErrorDetectionStrategy** z liczbą ponowień równą 10 i 5 sekund oczekiwania czasu między kolejnymi próbami. Jeśli używane są transakcje, upewnij się, zakres ponawiania Przechodzi wstecz na początku transakcji w przypadku wystąpienia błędu przejściowego.

## <a name="limitations"></a>Ograniczenia
Metod opisanych w tym dokumencie pociąga za sobą kilka ograniczeń:

* Przykładowy kod dla tego dokumentu nie przedstawiają sposób zarządzania schematu w fragmentów.
* Biorąc pod uwagę na żądanie, przyjęto założenie, że jego przetwarzanie bazy danych znajduje się w obrębie jednego niezależnego fragmentu określonej za pomocą klucza dzielenia na fragmenty udostępnionego przez żądanie. Jednak to założenie nie zawsze przechowuje, na przykład, gdy nie jest możliwe do udostępnienia klucza dzielenia na fragmenty. Aby rozwiązać ten problem, biblioteki klienta elastycznej bazy danych obejmuje [MultiShardQuery klasy](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Klasa implementuje abstrakcji połączenia, na potrzeby zapytań przez kilka fragmentów. Przy użyciu MultiShardQuery w połączeniu z Dapper wykracza poza zakres tego dokumentu.

## <a name="conclusion"></a>Podsumowanie
Aplikacji przy użyciu Dapper i DapperExtensions można łatwo korzystać z narzędzi elastycznej bazy danych dla bazy danych SQL Azure. Kroki opisane w niniejszym dokumencie te aplikacje mogą używać narzędzia możliwości zależne od danych routingu przez zmianę tworzenia i otwierania nowych [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) obiektów do użycia [ OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) wywołania biblioteki klienta elastycznej bazy danych. Ogranicza to wymagane do tych miejscach, w którym nowych połączeń są tworzone i otworzyć zmian w aplikacji. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png

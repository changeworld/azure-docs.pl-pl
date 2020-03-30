---
title: Korzystanie z elastycznej biblioteki klienta bazy danych z dapper
description: Korzystanie z elastycznej biblioteki klienta bazy danych z Dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 83d24d45d7628a2e02068c8757fa6568d6d3fc37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823472"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Korzystanie z elastycznej biblioteki klienta bazy danych z dapper
Ten dokument jest przeznaczony dla deweloperów, którzy polegają na Dapper do tworzenia aplikacji, ale również chcą objąć [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md) do tworzenia aplikacji, które implementują dzielenia na fragmenty do skalowania w poziomie ich warstwy danych.  Ten dokument ilustruje zmiany w aplikacjach opartych na dapper, które są niezbędne do integracji z narzędziami elastycznej bazy danych. Koncentrujemy się na redagowaniu zarządzania fragmentami elastycznej bazy danych i routingu zależnego od danych za pomocą Dapper. 

**Przykładowy kod:** [Narzędzia elastycznej bazy danych dla usługi Azure SQL Database — integracja dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integracja **Dapper** i **DapperExtensions** z biblioteką klienta elastycznej bazy danych dla usługi Azure SQL Database jest prosta. Aplikacje mogą korzystać z routingu zależnego od danych, zmieniając tworzenie i otwieranie nowych obiektów [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) w celu użycia wywołania [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) z [biblioteki klienta](https://msdn.microsoft.com/library/azure/dn765902.aspx). Ogranicza to zmiany w aplikacji tylko wtedy, gdy nowe połączenia są tworzone i otwierane. 

## <a name="dapper-overview"></a>Dapper przegląd
**Dapper** jest obiekt-relacyjny maper. Mapuje obiekty platformy .NET z aplikacji do relacyjnej bazy danych (i odwrotnie). Pierwsza część przykładowego kodu ilustruje, jak można zintegrować bibliotekę klienta elastycznej bazy danych z aplikacjami opartymi na dapper. Druga część przykładowego kodu ilustruje sposób integracji podczas korzystania z dapper i DapperExtensions.  

Funkcja mapowania w Dapper udostępnia metody rozszerzenia połączeń bazy danych, które upraszczają przesyłanie instrukcji T-SQL do wykonania lub wykonywania kwerend bazy danych. Na przykład Dapper ułatwia mapowanie między obiektami .NET a parametrami instrukcji SQL dla **wykonywania** wywołań lub korzystanie z wyników zapytań SQL do obiektów .NET przy użyciu **wywołań query** z Dapper. 

Podczas korzystania DapperExtensions, nie trzeba już podać instrukcje SQL. Rozszerzenia metody, takie jak **GetList** lub **Wstaw** za pośrednictwem połączenia bazy danych utworzyć instrukcje SQL w tle.

Inną zaletą Dapper, a także DapperExtensions jest to, że aplikacja kontroluje tworzenie połączenia z bazą danych. Pomaga to w interakcji z biblioteki klienta elastycznej bazy danych, które brokerów połączeń bazy danych na podstawie mapowania shardlets do baz danych.

Aby uzyskać zestawy Dapper, zobacz [Dapper dot net](https://www.nuget.org/packages/Dapper/). Rozszerzenia Dapper można znaleźć w zobacz [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Szybkie spojrzenie na bibliotekę klienta elastycznej bazy danych
Za pomocą biblioteki klienta elastycznej bazy danych można zdefiniować partycje danych aplikacji o nazwie *shardlets*, mapować je do baz danych i identyfikować je za pomocą *kluczy dzielenia na fragmenty.* Możesz mieć tyle baz danych, ile potrzebujesz i rozpowszechniać shardlets w tych bazach danych. Mapowanie wartości klucza dzielenia na fragmenty do baz danych jest przechowywany przez mapę niezależnego fragmentu dostarczone przez interfejsy API biblioteki. Ta funkcja jest nazywana **zarządzaniem mapami niezależnego fragmentu**. Mapa niezależnego fragmentu służy również jako broker połączeń bazy danych dla żądań, które zawierają klucz dzielenia na fragmenty. Ta funkcja jest określana jako **routing zależny od danych**.

![Mapy niezależnego fragmentu i routingu zależnego od danych][1]

Menedżer mapy niezależnego fragmentu chroni użytkowników przed niespójnymi widokami do danych shardlet, które mogą wystąpić, gdy równoczesne operacje zarządzania shardlet dzieje się w bazach danych. W tym celu mapy niezależnego fragmentu broker połączeń bazy danych dla aplikacji utworzonej za pomocą biblioteki. Gdy operacje zarządzania fragmentami może mieć wpływ na shardlet, dzięki temu funkcje mapy niezależnego fragmentu automatycznie zabić połączenie z bazą danych. 

Zamiast używać tradycyjnego sposobu tworzenia połączeń dla Dapper, należy użyć [metody OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn824099.aspx). Gwarantuje to, że wszystkie sprawdzanie poprawności ma miejsce i połączenia są zarządzane poprawnie, gdy wszystkie dane przenoszone między fragmentami.

### <a name="requirements-for-dapper-integration"></a>Wymagania dotyczące integracji Dapper
Podczas pracy z biblioteką klienta elastycznej bazy danych i interfejsami API Dapper chcesz zachować następujące właściwości:

* **Skalowaj w poziomie:** Chcemy dodać lub usunąć bazy danych z warstwy danych podzielonej aplikacji, jeśli jest to konieczne dla zapotrzebowania aplikacji na pojemność. 
* **Spójność:** Ponieważ aplikacja jest skalowana w poziomie przy użyciu dzielenia na fragmenty, należy wykonać routing zależny od danych. Chcemy użyć funkcji routingu zależnego od danych biblioteki, aby to zrobić. W szczególności chcesz zachować gwarancje sprawdzania poprawności i spójności zapewniane przez połączenia, które są brokerowane za pośrednictwem menedżera mapy niezależnego fragmentu, aby uniknąć uszkodzenia lub nieprawidłowych wyników kwerendy. Gwarantuje to, że połączenia z danym shardlet są odrzucane lub zatrzymywane, jeśli (na przykład) shardlet jest obecnie przenoszony do innego niezależnego fragmentu przy użyciu interfejsów API podziału/scalania.
* **Mapowanie obiektów:** Chcemy zachować wygodę mapowania dostarczonych przez Dapper do tłumaczenia między klasami w aplikacji i podstawowych struktur bazy danych. 

Poniższa sekcja zawiera wskazówki dotyczące tych wymagań dla aplikacji opartych na **Dapper** i **DapperExtensions**.

## <a name="technical-guidance"></a>Wskazówki techniczne
### <a name="data-dependent-routing-with-dapper"></a>Routing zależny od danych z dapperem
Z Dapper, aplikacja jest zazwyczaj odpowiedzialny za tworzenie i otwieranie połączeń do podstawowej bazy danych. Biorąc pod uwagę typ T przez aplikację, Dapper zwraca wyniki kwerendy jako .NET kolekcje typu T. Dapper wykonuje mapowanie z wierszy wynik T-SQL do obiektów typu T. Podobnie Dapper mapuje obiekty .NET na wartości SQL lub parametry dla instrukcji języka manipulowania danymi (DML). Dapper oferuje tę funkcję za pomocą metod rozszerzenia na zwykły obiekt [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) z bibliotek klienta SQL .NET. Połączenie SQL zwracane przez interfejsy API skali elastycznej dla DDR są również zwykłymi obiektami [SqlConnection.](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) Dzięki temu możemy bezpośrednio używać rozszerzeń Dapper nad typem zwracanym przez interfejs API DDR biblioteki klienta, ponieważ jest to również proste połączenie klienta SQL.

Te obserwacje sprawiają, że łatwo używać połączeń brokered przez bibliotekę klienta elastycznej bazy danych dla Dapper.

W tym przykładzie kodu (z towarzyszącego przykładu) ilustruje podejście, gdzie klucz dzielenia na fragmenty jest dostarczany przez aplikację do biblioteki do brokera połączenia z prawym fragmentem.   

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

Wywołanie interfejsu API [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) zastępuje domyślne tworzenie i otwieranie połączenia klienta SQL. [Wywołanie OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) przyjmuje argumenty wymagane dla routingu zależnego od danych: 

* Mapa niezależnego fragmentu w celu uzyskania dostępu do interfejsów routingu zależnych od danych
* Klucz dzielenia na fragmenty w celu zidentyfikowania shardlet
* Poświadczenia (nazwa użytkownika i hasło) do łączenia się z fragmentem

Obiekt mapy niezależnego fragmentu tworzy połączenie z niezależnego fragmentu, który przechowuje shardlet dla danego klucza dzielenia na fragmenty. Interfejsy API klienta elastycznej bazy danych również tag połączenia do zaimplementowania jego gwarancji spójności. Ponieważ wywołanie [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) zwraca zwykły obiekt połączenia klienta SQL, kolejne wywołanie metody rozszerzenia **Execute** z Dapper następuje standardowa praktyka Dapper.

Zapytania działają bardzo podobnie - najpierw otwórz połączenie za pomocą [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) z interfejsu API klienta. Następnie należy użyć zwykłych metod rozszerzenia Dapper do mapowania wyników kwerendy SQL do obiektów .NET:

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

Należy zauważyć, że **using** bloku z zakresu połączenia DDR wszystkie operacje bazy danych w bloku do jednego niezależnego fragmentu, gdzie tenantId1 jest przechowywany. Kwerenda zwraca tylko blogi przechowywane na bieżącym niezależnuzyku, ale nie te przechowywane na innych fragmentów. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Routing zależny od danych z dapper i DapperExtensions
Dapper jest wyposażony w ekosystem dodatkowych rozszerzeń, które mogą zapewnić dalszą wygodę i abstrakcję z bazy danych podczas tworzenia aplikacji bazy danych. DapperExtensions jest przykładem. 

Za pomocą DapperExtensions w aplikacji nie zmienia sposobu tworzenia połączeń bazy danych i zarządzania. Nadal jest odpowiedzialny za aplikację do otwierania połączeń i regularnych obiektów połączenia klienta SQL są oczekiwane przez metody rozszerzenia. Możemy polegać na [OpenConnectionForKey,](https://msdn.microsoft.com/library/azure/dn807226.aspx) jak opisano powyżej. Jak pokazują następujące przykłady kodu, jedyną zmianą jest to, że nie trzeba już pisać instrukcji T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

A oto przykład kodu dla kwerendy: 

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

### <a name="handling-transient-faults"></a>Obsługa usterek przejściowych
Zespół microsoft patterns & Practices opublikował [blok aplikacji obsługi błędów przejściowych,](https://msdn.microsoft.com/library/hh680934.aspx) aby pomóc deweloperom aplikacji w łagodzeniu typowych stanów błędu przejściowego występujących podczas uruchamiania w chmurze. Aby uzyskać więcej informacji, zobacz [Wytrwałość, Tajemnica wszystkich triumfów: Korzystanie z bloku aplikacji obsługi błędów przejściowych](https://msdn.microsoft.com/library/dn440719.aspx).

Przykładowy kod opiera się na bibliotece błędów przejściowych w celu ochrony przed błędami przejściowymi. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** w powyższym kodzie jest zdefiniowany jako **SqlDatabaseTransientErrorDetectionStrategy** z liczbą ponownych prób 10 i 5 sekund czasu oczekiwania między ponownych prób. Jeśli używasz transakcji, upewnij się, że zakres ponawiania powraca do początku transakcji w przypadku błędu przejściowego.

## <a name="limitations"></a>Ograniczenia
Podejścia przedstawione w niniejszym dokumencie wiążą się z kilkoma ograniczeniami:

* Przykładowy kod dla tego dokumentu nie pokazuje, jak zarządzać schematem między fragmentami.
* Biorąc pod uwagę żądanie, zakładamy, że wszystkie jego przetwarzania bazy danych jest zawarty w jednym niezależnego fragmentu, jak identyfikowane przez klucz dzielenia na fragmenty dostarczone przez żądanie. Jednak to założenie nie zawsze posiada, na przykład, gdy nie jest możliwe udostępnienie klucza dzielenia na fragmenty. Aby rozwiązać ten problem, biblioteka klienta elastycznej bazy danych zawiera [klasę MultiShardQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Klasa implementuje abstrakcję połączenia do wykonywania zapytań przez kilka fragmentów. Korzystanie MultiShardQuery w połączeniu z Dapper wykracza poza zakres tego dokumentu.

## <a name="conclusion"></a>Podsumowanie
Aplikacje korzystające z dapper i DapperExtensions mogą łatwo korzystać z narzędzi elastycznej bazy danych dla usługi Azure SQL Database. W krokach opisanych w tym dokumencie aplikacje te mogą korzystać z możliwości narzędzia do routingu zależnego od danych, zmieniając tworzenie i otwieranie nowych obiektów [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) w celu użycia wywołania [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) biblioteki klienta elastycznej bazy danych. Ogranicza to zmiany aplikacji wymagane do tych miejsc, w których tworzone i otwierane są nowe połączenia. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png

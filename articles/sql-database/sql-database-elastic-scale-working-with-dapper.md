---
title: Za pomocą biblioteki klienckiej elastycznej bazy danych z programem Dapper | Dokumentacja firmy Microsoft
description: Używanie biblioteki klienckiej elastycznej bazy danych z programem Dapper.
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
ms.date: 12/04/2018
ms.openlocfilehash: c6ca7637c8e251fa29781503ffc18227c51bb4da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335309"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Za pomocą biblioteki klienckiej elastycznej bazy danych z programem Dapper
Ten dokument stanowi dla deweloperów, które zależą od programem Dapper do tworzenia aplikacji, ale także wykorzystywać [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md) do tworzenia aplikacji fragmentowania tej implementacji, aby skalować warstwę danych.  Ten dokument przedstawia zmiany w aplikacji opartych na programem Dapper, które są niezbędne w celu integracji z narzędziami elastycznej bazy danych. Naszym głównym celem jest tworzenie zarządzania fragmentami elastycznych baz danych i routingu zależnego od danych z programem Dapper. 

**Przykładowy kod**: [Narzędzia elastycznych baz danych usługi Azure SQL Database — Integracja z programem Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrowanie **programem Dapper** i **DapperExtensions** za pomocą elastycznej bazy danych biblioteki klienta usługi Azure SQL Database jest proste. Twoje aplikacje mogą używać routingu zależnego od danych przez zmianę tworzenia i otwierania nowych [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) obiektów pod kątem [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) wywołać z [biblioteki klienckiej ](https://msdn.microsoft.com/library/azure/dn765902.aspx). Ogranicza to zmian w aplikacji tylko w przypadku, gdy nowe połączenia są tworzone i otwierane. 

## <a name="dapper-overview"></a>Omówienie programem dapper
**Programem dapper** to maper obiektowo relacyjny. Jest on mapowany obiektów platformy .NET z poziomu aplikacji w relacyjnej bazie danych (i na odwrót). Pierwsza część przykładowego kodu ilustruje, jak można zintegrować Biblioteka klienta elastycznej bazy danych z aplikacji z programem Dapper. Druga część przykładowego kodu ilustruje sposób integracji, korzystając z programem Dapper i DapperExtensions.  

Funkcja mapowania w programem Dapper udostępnia metody rozszerzenia dla połączenia z bazą danych, które upraszczają przesyłanie instrukcji języka T-SQL do wykonania lub zapytanie do bazy danych. Na przykład programem Dapper ułatwia mapowania obiektów .NET i parametry instrukcji SQL dla **Execute** wywołań, lub korzystanie z wyników zapytań SQL do obiektów platformy .NET przy użyciu **zapytania** połączenia z programem Dapper. 

Korzystając z DapperExtensions, nie jest już konieczne jest zapewnienie instrukcji SQL. Metody rozszerzenia, takie jak **GetList** lub **Wstaw** za pośrednictwem połączenia z bazą danych tworzenia instrukcji SQL w tle.

Kolejną korzyścią wynikającą z programem Dapper, a także DapperExtensions to, że aplikacja kontroluje tworzenie połączenia z bazą danych. Dzięki temu można wchodzić w interakcje za pomocą biblioteki klienckiej elastycznej bazy danych, które brokerów bazy danych połączenia na podstawie mapowania podfragmentów z bazami danych.

Programem Dapper zestawów, można znaleźć [programem Dapper dot net](https://www.nuget.org/packages/Dapper/). Programem Dapper rozszerzeń, zobacz [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Krótkie omówienie Biblioteka kliencka elastic database
Za pomocą biblioteki klienckiej elastycznej bazy danych, zdefiniuj partycje dane Twoich aplikacji o nazwie *podfragmentów*zmapować je z bazami danych i ich przez zidentyfikowanie *kluczy fragmentowania*. Może mieć dowolną liczbę baz danych zgodnie z potrzebami i dystrybuować swoje podfragmentów w tych bazach danych. Mapowanie wartości klucza fragmentowania do baz danych jest przechowywany przez mapowania fragmentów w postaci dostarczone przez interfejsy API biblioteki. Ta funkcja jest wywoływana **procesu zarządzania mapą fragmentów**. Mapowania fragmentów służy również jako broker połączeń z bazą danych dla żądań, które zawierają klucz fragmentowania. Ta funkcja jest nazywana **routingu zależnego od danych**.

![Mapy fragmentów i routingu zależnego od danych][1]

Menedżera mapowań fragmentów uniemożliwia użytkownikom niespójne widoków w dane podfragmentu może wystąpić, gdy podfragmentu równoczesnych operacji zarządzania mają miejsce w bazach danych. Aby to zrobić, mapowań fragmentów w postaci brokera połączeń bazy danych dla aplikacji skompilowanych przy użyciu biblioteki. Podczas operacji zarządzania fragmentami może mieć wpływ na podfragmentu, dzięki funkcji mapy fragmentów automatycznie kill połączenie z bazą danych. 

Zamiast korzystać z tradycyjnego pod kątem tworzenia połączeń dla programem Dapper, należy użyć [metoda OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn824099.aspx). Dzięki temu, sprawdzania poprawności ma miejsce, a połączenia są zarządzane prawidłowo, gdy wszystkie dane są przenoszone między fragmentami.

### <a name="requirements-for-dapper-integration"></a>Wymagania dotyczące integracji z programem Dapper
Podczas pracy z programem Dapper interfejsów API i Biblioteka kliencka elastic database, chcesz zachować następujące właściwości:

* **Skalowanie w poziomie**: Chcemy dodać lub usunąć bazy danych z warstwy danych aplikacji podzielonej na fragmenty, stosownie do potrzeb pojemność aplikacji. 
* **Spójność**: Ponieważ aplikacja będzie skalowana w poziomie za pomocą fragmentowania, należy przeprowadzić routing zależny od danych. Chcemy użyć możliwości routingu zależnego od danych biblioteki, aby to zrobić. W szczególności, aby zachować sprawdzanie poprawności i spójności gwarantuje dostarczone przez połączeń, które są obsługiwane przez brokera za pośrednictwem Menedżera mapowań fragmentów w celu uniknięcia uszkodzenia lub wyniki kwerendy problem. Daje to gwarancję, że połączenia z danym podfragmentu są odrzucone lub zatrzymany, jeśli (na przykład) podfragmentu aktualnie jest przenoszony w innym fragmencie, za pomocą interfejsów API dzielenia i scalania.
* **Obiekt mapowanie**: Chcemy zachować wygody mapowania, dostarczone przez programem Dapper tłumaczenie z klas w aplikacji i podstawowe struktury bazy danych. 

W poniższej sekcji przedstawiono wskazówki dotyczące tych wymagań dla aplikacji na podstawie **programem Dapper** i **DapperExtensions**.

## <a name="technical-guidance"></a>Wskazówki techniczne
### <a name="data-dependent-routing-with-dapper"></a>Routing zależny od danych z programem Dapper
Z programem Dapper aplikacja jest zazwyczaj odpowiedzialny za tworzenie i otwieranie połączenia z podstawową bazą danych. Przez aplikację, biorąc pod uwagę typu T, programem Dapper zwraca wyniki zapytania kolekcji .NET typu T. programem Dapper przeprowadza mapowanie z wiersze wynikowe języka T-SQL do obiektów typu T. Podobnie programem Dapper mapuje obiektów platformy .NET do wartości SQL lub parametrów dla instrukcji języka (DML) manipulacji danych. Programem Dapper oferuje tę funkcję za pomocą metod rozszerzenia w zwykłych [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) obiekt z biblioteki ADO .NET SQL Client. Połączenie SQL dla rekordu DDR zwracane przez interfejsy API elastycznego skalowania są również regularne [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) obiektów. Dzięki temu bezpośrednio przy użyciu rozszerzenia z programem Dapper nad typem zwracanym przez interfejs API DDR biblioteki klienta, ponieważ pełni on również prostego połączenia klienta SQL.

Uwagi te stają się proste do korzystania z połączeń obsługiwanych przez brokera, biblioteka kliencka elastic database dla programem Dapper.

Ten przykładowy kod (z towarzyszącym próbki) przedstawia podejście, w którym klucz fragmentowania jest udostępniany przez aplikację do biblioteki brokera połączeń do prawego fragmentu.   

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

Wywołanie [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) API zastępuje domyślne tworzenia i otwierania połączenia klienta SQL. [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) wywołanie przyjmuje argumenty, które są wymagane do routingu zależnego od danych: 

* Mapowania fragmentów, aby dostęp do interfejsów routingu zależnego od danych
* Klucz fragmentowania tak, aby zidentyfikować podfragmentu
* Poświadczenia (nazwę użytkownika i hasło), aby nawiązać połączenie z fragmentem

Obiektu mapy fragmentów tworzy połączenie do przechowujący podfragmentu klucza fragmentowania danego fragmentu. Interfejsów API klienta elastycznej bazy danych również oznaczać połączenia do zaimplementowania jego gwarancje spójności. Od czasu wywołania [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) zwraca regularne obiektu połączenia klienta SQL, kolejne wywołanie **Execute** metoda rozszerzenia z programem Dapper następuje standardową praktyką programem Dapper.

Zapytania działają bardzo podobnie jak — Otwórz połączenie za pomocą [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) z klienta interfejsu API. Następnie przy użyciu metody regularnego programem Dapper rozszerzenia mapowania wyniki zapytania SQL do obiektów platformy .NET:

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

Należy pamiętać, że **przy użyciu** blokowania z zakresami połączenia DDR wszystkie operacje bazy danych w ramach bloku w jednym fragmencie, gdzie są przechowywane tenantId1. Zapytanie zwraca tylko blogi przechowywanych w bieżącym fragmencie, ale nie te, przechowywane w innych fragmentach. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Routing zależny od danych z programem Dapper i DapperExtensions
Programem Dapper jest powiązana z ekosystemu dodatkowe rozszerzenia, które może zapewnić dalsze zwiększenie wygody działania i abstrakcji z bazy danych podczas opracowywania aplikacji baz danych. DapperExtensions znajduje się przykład. 

W aplikacji przy użyciu DapperExtensions nie zmienia sposobu połączenia z bazą danych są tworzone i zarządzane. Nadal jest odpowiedzialność aplikacji, aby otworzyć aplet połączenia i regularnego obiekty połączenia klienta SQL są oczekiwane przez metody rozszerzenia. Firma Microsoft może polegać na [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) zgodnie z powyższymi informacjami. Co zostało przedstawione w następujących przykładach kodu, jedyna różnica polega, że nie trzeba pisać instrukcje języka T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

A Oto przykład kodu służącego do kwerendy: 

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
Opublikowane przez zespół Microsoft Patterns i praktyki [blok aplikacji obsługi błędów przejściowych](https://msdn.microsoft.com/library/hh680934.aspx) pomagające deweloperom aplikacji rozwiązać typowe warunki błędów przejściowych podczas uruchamiania w chmurze. Aby uzyskać więcej informacji, zobacz [Perseverance, klucza tajnego sukcesy wszystkich: Przy użyciu bloku aplikacji obsługi błędów przejściowych](https://msdn.microsoft.com/library/dn440719.aspx).

Przykładowy kod zależy od biblioteki błędów przejściowych, aby zapewnić ochronę przed błędów przejściowych. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** w powyższym kodzie jest zdefiniowany jako **SqlDatabaseTransientErrorDetectionStrategy** z liczbą ponowień równą 10 i 5 sekund czas między ponownymi próbami oczekiwania. Jeśli używasz transakcji, upewnij się, czy zakres ponawiania prób powraca do stanu sprzed transakcji w przypadku błędów przejściowych.

## <a name="limitations"></a>Ograniczenia
Metod opisanych w tym dokumencie pociąga za sobą kilka ograniczeń:

* Przykładowy kod dla tego dokumentu nie przedstawiono tu sposób zarządzania schematu między fragmentami.
* Biorąc pod uwagę na żądanie, przyjęto założenie, że jego przetwarzania bazy danych jest zawarta w pojedynczy fragment, jak identyfikowany przez klucz fragmentowania dostarczonej przez żądanie. Jednak to założenie nie zawsze przechowuje, na przykład, gdy nie jest możliwe udostępnić klucz fragmentowania. Aby rozwiązać ten problem, zawiera Biblioteka kliencka elastic database [klasy MultiShardQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Klasa implementuje abstrakcji połączenie, podczas wykonywania zapytań w wielu fragmentach. Używanie MultiShardQuery w połączeniu z programem Dapper wykracza poza zakres tego dokumentu.

## <a name="conclusion"></a>Podsumowanie
Aplikacjami korzystającymi z programem Dapper oraz DapperExtensions można łatwo korzystać z narzędzi elastycznych baz danych usługi Azure SQL Database. Kroki opisane w niniejszym dokumencie te aplikacje mogą używać tego narzędzia, możliwości do routingu zależnego od danych przez zmianę tworzenia i otwierania nowych [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) obiektów pod kątem [ OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) wywołanie Biblioteka kliencka elastic database. Ogranicza to zmian aplikacji wymagane do tych miejscach, w którym nowe połączenia są tworzone i otwierane. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png

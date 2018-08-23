---
title: Elastyczne zapytanie — dostęp do danych w usłudze Azure SQL Data Warehouse z usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Poznaj najlepsze rozwiązania dotyczące przy użyciu zapytania elastycznego dostępu do danych w usłudze Azure SQL Data Warehouse z usługi Azure SQL Database.
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/11/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: 344cb1bed56b0b6af7bd3704f8674ae30695f885
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058814"
---
# <a name="best-practices-for-using-elastic-query-in-azure-sql-database-to-access-data-in-azure-sql-data-warehouse"></a>Najlepsze rozwiązania dotyczące dostępu do danych w usłudze Azure SQL Data Warehouse przy użyciu zapytania elastycznego usługi Azure SQL Database
Poznaj najlepsze rozwiązania dotyczące przy użyciu zapytania elastycznego dostępu do danych w usłudze Azure SQL Data Warehouse z usługi Azure SQL Database. 

## <a name="what-is-an-elastic-query"></a>Co to jest zapytanie elastyczne?
Zapytanie elastyczne umożliwia napisać zapytanie w bazie danych Azure SQL, zdalnie wysyłany do usługi Azure SQL data warehouse za pomocą języka T-SQL i tabele zewnętrzne. Za pomocą tej funkcji zapewnia oszczędności kosztów i więcej architektur wydajna, w zależności od scenariusza.

Ta funkcja umożliwia dwa podstawowe scenariusze:

1. Izolacji domeny
2. Wykonanie zapytania zdalnego

### <a name="domain-isolation"></a>Izolacji domeny

Izolacja domeny odnosi się do scenariusza składnicy danych klasycznego. W niektórych scenariuszach jeden warto udostępnić domeny logicznej danych podrzędnego użytkowników, którzy są odizolowane od pozostałej części w magazynie danych z różnych powodów takich jak między innymi:

1. Izolację zasobów — bazy danych SQL jest zoptymalizowany do obsługi dużych base równoczesnych użytkowników, obsługujący obciążenia nieco inne niż dużych zapytań analitycznych, które w magazynie danych jest zarezerwowany dla. Izolacja zapewnia, że właściwe pakiety robocze są obsługiwane przez odpowiednie narzędzia.
2. Izolacja na potrzeby zabezpieczeń — do oddzielania podzbiór danych autoryzowanych selektywnie za pośrednictwem niektórych schematów.
3. Tryb piaskownicy — Podaj Przykładowy zestaw danych jako "— informacje i testowanie" do eksplorowania zapytania produkcyjne itp.

Elastyczne zapytanie może zapewnić możliwość łatwo wybrać podzbiór danych magazynu danych SQL i przenieś go w wystąpieniu bazy danych SQL. Ponadto ta izolacja nie wyklucza możliwość również włączyć wykonywanie zapytania zdalnego, co zapewnia bardziej interesujące scenariuszy "pamięć podręczną".

### <a name="remote-query-execution"></a>Wykonanie zapytania zdalnego

Elastyczne zapytanie umożliwia wykonanie zapytania zdalnego w z wystąpieniem usługi SQL data warehouse. Jeden mogą wykorzystywać najlepsze cechy zarówno bazy danych SQL database i SQL data warehouse, oddzielając gorące i zimne dane między dwiema bazami danych. Użytkownikom można pozostawić najświeższe dane w bazie danych SQL, który może służyć raporty i dużą liczbę użytkowników biznesowych średniej. Jednak w przypadku wymagane jest więcej danych lub obliczenie użytkownika można odciążyć część zapytania, do których na dużą skalę agregacji mogą być przetwarzane znacznie szybciej i wydajniej wystąpienie magazynu danych SQL.

## <a name="elastic-query-process"></a>Proces zapytania elastycznego
Elastyczne zapytanie może służyć do udostępnienie danych znajdującymi się w danych programu SQL, magazynu wystąpień bazy danych SQL. Elastyczne zapytanie umożliwia zapytania z bazy danych SQL odnoszą się do tabel w zdalne wystąpienie magazynu danych SQL. 

Pierwszym krokiem jest, aby utworzyć definicję źródła danych zewnętrznych, która odwołuje się do wystąpienie magazynu danych SQL, który używa istniejące poświadczenia użytkownika w usłudze SQL data warehouse. Żadne zmiany nie są niezbędne w zdalnym wystąpieniu magazynu danych SQL. 

> [!IMPORTANT] 
> 
> Musi mieć uprawnienie ALTER ANY zewnętrznego źródła danych. To uprawnienie jest dołączony do uprawnienie ALTER DATABASE. Aby odwołać się do zdalnych źródeł danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.

Następnie należy utworzyć definicję zdalnego tabeli zewnętrznej w wystąpieniu bazy danych SQL, które wskazuje do tabeli zdalnej usługi SQL data warehouse. Zapytanie używa tabeli zewnętrznej, część zapytania, odnoszące się do tabeli zewnętrznej są wysyłane do wystąpienia magazynu danych SQL do przetworzenia. Po ukończeniu zapytania zestaw wyników jest wysyłane z powrotem do wywołującego wystąpienie bazy danych SQL. Aby uzyskać krótki samouczek konfigurowania zapytanie elastyczne między bazy danych SQL database i SQL data warehouse, zobacz [Konfigurowanie zapytania elastycznego z usługą SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse].

Aby uzyskać więcej informacji na temat zapytania elastycznego z usługą SQL database, zobacz [Przegląd zapytanie elastyczne w usłudze Azure SQL Database][Azure SQL Database elastic query overview].

## <a name="best-practices"></a>Najlepsze praktyki
Użyj tych najlepszych rozwiązań skutecznie użycie elastycznego zapytania.

### <a name="general"></a>Ogólne

- Podczas korzystania z zapytania zdalnego wykonania, upewnij się, jest tylko wybranie wymaganych kolumn i stosowanie filtrów prawo. Nie tylko jest to zwiększenie niezbędne zasoby obliczeniowe, ale też zwiększa rozmiar zestawu wyników, i w związku z tym ilość danych, które mają zostać przeniesione między dwoma wystąpieniami.
- Obsługa danych w celach analitycznych do bazy danych SQL Database i SQL Data Warehouse w klastrowanego magazynu kolumn dla analytiIcal wydajności.
- Upewnij się, że tabel źródłowych są podzielone na partycje w przypadku przenoszenia zapytań i danych.
- Upewnij się, używane jako pamięci podręcznej wystąpienia bazy danych SQL są podzielone na partycje umożliwia bardziej szczegółową aktualizacje i łatwiejsze zarządzanie. 
- Najlepiej używać PremiumRS baz danych, ponieważ zapewniają one analitycznych korzyści wynikające z klastrowanego magazynu kolumn, indeksowanie, ze szczególnym uwzględnieniem obciążeń intensywnie korzystających z operacji We/Wy w obniżonej cenie z bazy danych Premium.
- Po obciążeń korzystaj z obciążenia lub kolumny identyfikacji Data rozpoczęcia obowiązywania wykonuje operację UPSERT w wystąpieniach bazy danych SQL, aby zachować integralność źródła pamięci podręcznej. 
- Utwórz osobny identyfikator logowania i użytkownika w wystąpienie magazynu danych SQL o poświadczenia logowania zdalnego bazy danych SQL, zdefiniowane w źródle danych zewnętrznych. 

### <a name="elastic-querying"></a>Zapytania elastyczne

- W wielu przypadkach przydatne może być zarządzanie typem rozciąganej tabeli, gdzie jest częścią tabeli w bazie danych SQL jako dane wydajności z pozostałą częścią danych przechowywanych w usłudze SQL Data Warehouse w pamięci podręcznej. Potrzebujesz dwóch obiektów w bazie danych SQL: tabeli zewnętrznej w bazie danych SQL, który odwołuje się do tabeli podstawowej w SQL Data Warehouse oraz "buforowane" część tabeli w bazie danych SQL. Należy rozważyć utworzenie widoku w górnej części pamięci podręcznej, tabeli i tabeli zewnętrznej, która złoży tabele i ma zastosowanie filtrów, które oddzielenie danych zmaterializowanego danych SQL Database i SQL Data Warehouse dostępna za pośrednictwem tabel zewnętrznych.

  Załóżmy, że chcesz zachować ostatniego roku danych w wystąpieniu bazy danych SQL. **Zewnętrznego Zamówienia** odwołania do tabeli w magazynie danych porządkuje tabel. **Dbo. Zamówienia** reprezentuje ostatnich lat pomagają w zrealizowaniu danych w wystąpieniu bazy danych SQL. Zamiast prosząc użytkowników o zdecyduj, czy zapytanie jednej tabeli lub innych, należy utworzyć widok w górnej części obie tabele w punkcie partycji z ostatniego roku.

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  Widok generowane w taki sposób, teraz kompilatora zapytania ustalić, czy serwer musi używać wystąpienie magazynu danych w odpowiedzi na zapytania użytkowników. 

  Brak dodatkowych kosztów związanych z przesyłania, kompilowanie, uruchamianie i przenoszenia danych skojarzonych z każdym elastyczne zapytanie względem wystąpienie magazynu danych. Być świadome, że każde zapytanie elastyczne zmniejsza swoje gniazd współbieżności i używa zasobów.  


- Jeśli jeden plany przejść do szczegółów dalej do zestawu wyników z wystąpienie magazynu danych, należy wziąć pod uwagę materializowanie go w tabeli tymczasowej w bazie danych SQL, wydajności i aby zapobiec użyciu zbędnych zasobów.

### <a name="moving-data"></a>Przenoszenie danych 

- Jeśli to możliwe należy zachować zarządzanie danymi łatwiejsze w przypadku tabel źródłowych tylko do dołączania tak, aby aktualizacje były łatwe do utrzymania między wystąpieniami dane magazynu i bazy danych.
- Flush przenoszenie danych na poziomie partycji przy użyciu i poziom i ilości danych, przenieść, aby zapewnić aktualność wystąpienie bazy danych magazynu wypełnienia semantykę, aby zminimalizować koszty zapytania na danych. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Kiedy należy wybrać vs usług Azure Analysis Services bazy danych SQL

Korzystanie z systemu Azure Analysis Services, gdy:

- Zamierzasz korzystać z pamięci podręcznej za pomocą narzędzia do analizy Biznesowej, która prześle dużą liczbę małych zapytań
- Potrzebujesz subsecond kwerendami
- Masz doświadczenie w zarządzaniu/opracowywanie modeli usług Analysis Services 

Korzystanie z usługi Azure SQL bazy danych, gdy:

- Chcesz wykonywać zapytania danych przy użyciu języka SQL
- Potrzebujesz zdalne wykonywanie kodu dla niektórych kwerend
- Mają większe wymagania dotyczące pamięci podręcznej

## <a name="faq"></a>Często zadawane pytania

P: czy mogę używać baz danych w puli elastycznej przy użyciu zapytania elastycznego?

Odp. Tak. Bazy danych SQL w ramach puli elastycznej za pomocą elastycznych kwerendy. 

P: czy istnieje limit liczby baz danych można używać dla zapytania elastyczne?

Odp.: nie jest brak twardych limit liczby baz danych może służyć do zapytań elastycznych. Jednak każde zapytanie elastyczne (zapytania, które trafień SQL Data Warehouse) będą przekładać limitów współbieżności normalny.

P: czy istnieją limity liczby jednostek DTU związane z elastycznego zapytania?

A: limity liczby jednostek DTU nie są nakładane na dowolny inny sposób przy użyciu zapytania elastycznego. Standardowa zasada jest taka, że serwerami logicznymi mają limity liczby jednostek DTU w miejscu, aby zapobiec przypadkowemu przekraczają założeń budżetowych klientów. Jeśli włączasz kilka baz danych dla zapytania elastycznego wraz z wystąpieniem programu SQL Data Warehouse można napotkać zakończenie nieoczekiwanie. W takiej sytuacji należy przesłać żądanie, aby zwiększyć limit liczby jednostek DTU na serwerze logicznym. Możesz zwiększyć limit przydziału przez [utworzeniem biletu pomocy technicznej](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) i wybierając polecenie *przydziału* jako typ żądania

P: czy mogę używać wiersza poziomu zabezpieczeń/dynamiczne dane maskowanie przy użyciu zapytania elastycznego?

Odp.: klientów, którzy chcą korzystać z bardziej zaawansowane funkcje zabezpieczeń w usłudze SQL Database to zrobić pierwszy przenosząc i przechowywanie danych w bazie danych SQL. Nie można obecnie stosować zabezpieczenia na poziomie wiersza lub DDM, na danych pobieranych za pośrednictwem tabel zewnętrznych. 

P: czy można napisać z Moje wystąpienia bazy danych SQL do wystąpienia usługi data warehouse?

Odp.: obecnie ta funkcja nie jest obsługiwana. Odwiedź nasze [strona opinii o] [ Feedback page] do tworzenia/głosu dla tej funkcji, jeśli jest to funkcja ma się pojawić w przyszłości. 

P: czy można używać typów przestrzennych, takie jak Geometria/lokalizacji geograficznej?

Odp.: można przechowywać typów przestrzennych w usłudze SQL Data Warehouse jako wartości varbinary(max). Kiedy wykonujesz zapytanie o tych kolumn przy użyciu zapytania elastycznego, należy przekonwertować je na odpowiednich typów w czasie wykonywania.

![typów przestrzennych](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)

<!--Article references-->

[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Configure Elastic Query with SQL Data Warehouse]: tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md



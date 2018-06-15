---
title: Zapytanie elastycznej — uzyskiwanie dostępu do danych w magazynie danych SQL Azure z bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, najlepsze rozwiązania dotyczące korzystania z bazy danych SQL Azure przy użyciu elastycznej zapytania uzyskują dostęp do danych w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/11/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: ceda0399ae98e2a36fd41b954a741e0379c77fe7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31797162"
---
# <a name="best-practices-for-using-elastic-query-in-azure-sql-database-to-access-data-in-azure-sql-data-warehouse"></a>Najlepsze rozwiązania dotyczące uzyskują dostęp do danych w usłudze Azure SQL Data Warehouse przy użyciu elastycznej kwerendy w bazie danych SQL Azure
Dowiedz się, najlepsze rozwiązania dotyczące używania elastycznej zapytania uzyskują dostęp do danych w magazynie danych SQL Azure z bazy danych SQL Azure. 

## <a name="what-is-an-elastic-query"></a>Co to jest elastyczny zapytania?
Elastyczny zapytania umożliwia zapisywanie kwerendy w bazie danych Azure SQL zdalnie wysyłany do usługi Azure SQL data warehouse przy użyciu T-SQL i tabel zewnętrznych. Za pomocą tej funkcji zawiera oszczędności kosztów i więcej architektur wydajności, w zależności od scenariusza.

Ta funkcja umożliwia dwa podstawowe scenariusze:

1. Izolacji domeny
2. Wykonywanie zapytania zdalnego

### <a name="domain-isolation"></a>Izolacji domeny

Izolacji domeny odwołuje się do scenariusza składnicy danych klasycznego. W niektórych scenariuszach co chcesz wyrazić domeny logicznej danych podrzędne użytkowników, którzy są odizolowane od pozostałej części magazynu danych z różnych powodów takich jak między innymi:

1. Izolację zasobów — bazy danych SQL jest zoptymalizowany do obsługi dużych base równoczesnych użytkowników, obsługujący obciążenia nieco inne niż dużych analitycznych zapytań, które w magazynie danych jest zastrzeżona na potrzeby. Izolacja gwarantuje, że prawo obciążeń są obsługiwane przez odpowiednich narzędzi.
2. Izolacja zabezpieczeń — do oddzielania podzbiór danych autoryzowanych selektywnie za pośrednictwem niektórych schematów.
3. Sandboxing - udostępniają zestaw przykładowych danych jako "Plac zabaw dla" Aby zapoznać się z produkcyjnym zapytania itp.

Elastyczne zapytań zapewniają możliwość łatwo wybrać podzbiór danych magazynu danych SQL i przenieś go do wystąpienia bazy danych SQL. Ponadto izolacja nie wyklucza możliwości należy również włączyć wykonywanie zapytania zdalnego, co zapewnia bardziej interesujące scenariusze "pamięci podręcznej".

### <a name="remote-query-execution"></a>Wykonywanie zapytania zdalnego

Elastyczne zapytania umożliwia wykonanie zapytania zdalnego w wystąpieniu magazynu danych SQL. Jeden wykorzystywać najlepsze bazy danych SQL i magazyn danych SQL oddzielając gorącego i zimnych danych między dwiema bazami danych. Użytkownicy mogą zachować nowych danych w bazie danych SQL, który może obsługiwać raporty i dużej liczby użytkowników biznesowych średnia. Jednak gdy potrzebny jest więcej danych lub obliczeń, użytkownik może odciążania części zapytania do wystąpienia magazynu danych SQL, gdzie na dużą skalę agreguje mogą być przetwarzane znacznie szybciej i bardziej efektywnie.

## <a name="elastic-query-process"></a>Proces elastycznej zapytania
Elastyczne zapytania można udostępnić danych znajdujących się w danych serwera SQL magazynu wystąpień bazy danych programu SQL. Umożliwia elastyczne zapytania kwerend z bazy danych SQL odwołujące się do tabel w zdalnym wystąpieniu magazynu danych SQL. 

Pierwszym krokiem jest do tworzenia definicji źródła danych zewnętrznych, która odwołuje się do wystąpienia magazynu danych SQL, która używa istniejących poświadczeń użytkownika w usłudze SQL data warehouse. Żadne zmiany nie są niezbędne w wystąpieniu zdalnym magazynu danych SQL. 

> [!IMPORTANT] 
> 
> Musi mieć uprawnienie ALTER ANY zewnętrznego źródła danych. To uprawnienie jest dołączany uprawnienie ALTER DATABASE. Aby odwołać się do zdalnych źródeł danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.

Następnie można utworzyć definicji tabeli zewnętrznej zdalnego w wystąpieniu bazy danych SQL, wskazujący na tabeli zdalnej w usłudze SQL data warehouse. Zapytanie używa tabelę zewnętrzną, część zapytań odwołujących się do tabeli zewnętrznej są wysyłane do wystąpienia magazynu danych SQL do przetworzenia. Po ukończeniu zapytania zestawu wyników są wysyłane do wywoływania wystąpienia bazy danych SQL. Krótki samouczek konfigurowania elastycznej zapytania między bazą danych SQL i magazyn danych SQL, zobacz [skonfigurować elastycznej zapytania SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse].

Aby uzyskać więcej informacji na elastyczne zapytania z bazy danych SQL, zobacz [omówienie zapytania elastycznej bazy danych SQL Azure][Azure SQL Database elastic query overview].

## <a name="best-practices"></a>Najlepsze praktyki
Użyj następujące najlepsze rozwiązania, aby efektywnie korzystać z zapytania elastycznej.

### <a name="general"></a>Ogólne

- Używając zapytania zdalnego wykonywania, upewnij się, jest tylko wybierając niezbędne kolumn i zastosowaniu prawa filtrów. Nie tylko jest to zwiększenie niezbędnych obliczeń, ale również zwiększa rozmiar zestawu wyników i w związku z tym ilość danych, które mają być przenoszone między dwa wystąpienia.
- Obsługa danych do analiz w magazynie danych programu SQL i bazy danych SQL w klastrowanego magazynu kolumn dla analytiIcal wydajności.
- Upewnij się, że są podzielone na partycje tabel źródłowych dla przepływu zapytań i danych.
- Upewnij się, wystąpień bazy danych programu SQL używane jako pamięci podręcznej są podzielone na partycje umożliwia bardziej szczegółowego aktualizacji i łatwiejsze zarządzanie. 
- Najlepiej użyć PremiumRS baz danych, ponieważ udostępniają one analitycznych zalet klastrowanego magazynu kolumn indeksowania, w której priorytetowo potraktowano obciążeń intensywnie wykonujących operacje We/Wy w obniżonej cenie z bazy danych — warstwa Premium.
- Po obciążeń wykorzystywać obciążenia lub daty wprowadzenia identyfikacji kolumn dla upserts w wystąpienia bazy danych SQL w celu zachowania integralności źródła pamięci podręcznej. 
- Utwórz oddzielne logowania i użytkownika w wystąpieniu magazynu danych SQL dla poświadczenia logowania zdalnego bazy danych SQL, zdefiniowanych w źródle danych zewnętrznych. 

### <a name="elastic-querying"></a>Elastyczne zapytań

- W wielu przypadkach przydatne może być zarządzanie typu rozciąganej tabeli, gdzie część tabeli znajduje się w bazie danych SQL jako buforowanych danych dotyczących wydajności z resztą danych przechowywanych w usłudze SQL Data Warehouse. Należy dwa obiekty w bazie danych SQL: tabeli zewnętrznej w bazie danych SQL, który odwołuje się do tabeli podstawowej w magazynie danych programu SQL i "pamięci podręcznej" część tabeli w bazie danych SQL. Należy wziąć pod uwagę tworzenia widoku w górnej części pamięci podręcznej tabeli i tabeli zewnętrznej które unie zarówno tabele i stosuje filtry, których dane zmaterializowany w ramach bazy danych SQL i usługi SQL Data Warehouse danych za pośrednictwem tabel zewnętrznych.

  Załóżmy, że chcesz zachować roku najnowszych danych w wystąpieniu bazy danych SQL. **Zewnętrzne Zamówienia** odwołania do tabeli w magazynie danych porządkuje tabel. **Dbo. Zamówienia** reprezentuje najbardziej aktualną wartość lat danych w wystąpieniu bazy danych SQL. Zamiast prosząc użytkowników o podjęcie decyzji o zapytanie jednej tabeli lub innych, należy utworzyć widok w górnej części obu tabel na punkcie partycji w ostatnim roku.

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

  Widok utworzony w taki sposób, umożliwia kompilatora zapytania ustalić, czy musi on mieć wystąpienie magazynu danych odpowiedzi na zapytanie użytkowników. 

  Jest większe obciążenie przesyłania, kompilowanie, uruchamiania i przenoszenia danych skojarzonych z każdego elastycznej zapytanie wystąpienie magazynu danych. Być świadome, że każdy elastycznej zapytań wlicza się z gniazda współbieżności i używa zasobów.  


- Jeśli jeden planuje przechodzenie dalej do zestawu wyników z wystąpienia magazynu danych, należy wziąć pod uwagę materializowania go w tabeli tymczasowej w bazie danych SQL, wydajności i użycia zasobów niepotrzebnych zapobiec.

### <a name="moving-data"></a>Przenoszenie danych 

- Jeśli to możliwe należy zachować zarządzanie danymi łatwiejsze w przypadku tabel źródłowych tylko Dołącz tak, aby aktualizacje były łatwego w utrzymaniu między wystąpieniami danych magazynu oraz bazy danych.
- Przenosi dane na poziomie partycji z opróżnić i poziom i ilość danych, przenieść, aby zapewnić aktualność wystąpienie bazy danych magazynu semantyki wypełnienia do minimalizuje to koszt zapytania na danych. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Kiedy należy wybrać w porównaniu bazy danych SQL Azure Analysis Services

Użyj usługi Azure Analysis Services, przy:

- Planowanie przy użyciu pamięci podręcznej z narzędziem BI prześle dużą liczbę małych zapytań
- Należy subsecond opóźnienia zapytania
- Masz doświadczenie w zarządzaniu/tworzenie modeli dla usług Analysis Services 

Użyj usługi Azure SQL bazy danych, gdy:

- Aby wysłać zapytanie danych pamięci podręcznej za pomocą programu SQL
- Zdalne wykonywanie kodu jest wymagane dla niektórych kwerend
- Mieć większe wymagania w pamięci podręcznej

## <a name="faq"></a>Często zadawane pytania

Pytanie: czy w puli elastycznej zapytania o elastycznej można używać baz danych?

Odp. Tak. Baz danych w puli elastycznej za pomocą elastycznych kwerendy. 

Pytanie: czy istnieje limit liczby baz danych I można użyć dla elastycznej zapytania?

A: w nie mają żadnych twardych zakończenia liczbę baz danych może służyć do elastycznej zapytania. Jednak każdego zapytania elastycznej (zapytań, które trafień SQL Data Warehouse) będą uwzględniane limity normalne współbieżności.

Pytanie: czy istnieją limitów jednostek DTU związanego z zapytaniem elastycznej?

A: limitów jednostek DTU nie są narzucone żadnego inaczej z zapytaniem elastycznej. Standardowych zasad jest taka, że serwery logiczne mają limitów jednostek DTU w miejscu, aby zapobiec przypadkowemu przekroczenia wydatków klientów. Jeśli włączasz szereg baz danych dla elastycznej zapytania równolegle z wystąpieniem usługi SQL Data Warehouse można napotkać zakończenie nieoczekiwanie. W takim przypadku przesłać żądanie, aby zwiększyć limit jednostek dtu w warstwie na serwerze w sieci logicznej. Może spowodować zwiększenie limitu przydziału przez [tworzenie biletu pomocy technicznej](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) i wybierając *przydziału* jako typ żądania

Pytanie: czy można użyć wiersza poziomu zabezpieczeń/dynamicznych danych maskowania elastycznej zapytania?

A: klientów, którzy chcą korzystać z bardziej zaawansowane funkcje zabezpieczeń w usłudze SQL Database to zrobić przez pierwszy przenoszenie i przechowywanie danych w bazie danych SQL. Obecnie nie można zastosować zabezpieczeń na poziomie wiersza lub DDM na danych pobieranych przez tabel zewnętrznych. 

Pytanie: czy można napisać z mojej wystąpienia bazy danych SQL do wystąpienia magazynu danych?

Odpowiedź: obecnie ta funkcja nie jest obsługiwana. Można znaleźć w naszych [strony] [ Feedback page] do tworzenia/głosu dla tej funkcji, jeśli jest to funkcja, które chcesz wyświetlić w przyszłości. 

Pytanie: czy można używać typów przestrzennych, takie jak Geometria/geograficzne?

Odpowiedź: można przechowywać typy przestrzenne w usłudze SQL Data Warehouse jako wartości varbinary(max). Kwerendy te kolumny za pomocą zapytania elastycznej, należy przekonwertować je na odpowiednich typów w czasie wykonywania.

![typy przestrzenne](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)

<!--Article references-->

[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Configure Elastic Query with SQL Data Warehouse]: tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md



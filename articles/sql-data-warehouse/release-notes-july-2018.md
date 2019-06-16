---
title: Usługa Azure SQL Data Warehouse — informacje o wersji lipca 2018 r. | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/06/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4ec90bef9228eccb9556d07bb5680deb00e2550e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65912174"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Co nowego w usłudze Azure SQL Data Warehouse? Lipiec 2018 r.
Usługa Azure SQL Data Warehouse odbiera ulepszenia stale. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w lipca 2018 r.

## <a name="lightning-fast-query-performance"></a>Wydajność zapytań szybkie pod kątem obsługi
[Usługa Azure SQL Data Warehouse](https://aka.ms/sqldw) Ustawia nowe testy porównawcze wydajności wraz z wprowadzeniem natychmiastowy dostęp do danych, który ulepsza operacji mieszania. Natychmiastowy dostęp do danych zmniejsza obciążenie związane z operacje przenoszenia danych za pomocą bezpośredniego programu SQL Server do obsługi operacji danych natywnych programu SQL Server. Integracja z aparatem programu SQL Server bezpośrednio w przypadku przenoszenia danych oznacza, że usługa SQL Data Warehouse teraz **67% szybsze niż usługi Amazon Redshift** przy użyciu obciążenia pochodną standardem branżowym dobrze rozpoznawalnemu [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/).

![Usługa Azure SQL Data Warehouse jest szybsze i tańsze niż usługi Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>źródła: [Raport analityka badawczy Joego: Data Warehouse za pomocą testu wydajności chmury](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

Poza wydajność środowiska uruchomieniowego [badań Joego](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) raportu także zmierzony stosunek ceny do wydajności określenie koszt USD konkretnych obciążeń. Usługa SQL Data Warehouse została **co najmniej 23% tańsza** niż Redshift pojemności 30 TB obciążeń. Z funkcją SQL Data Warehouse elastycznie skalować zasoby obliczeniowe oraz wstrzymywanie i wznawianie obciążeń klienci płacą tylko wtedy, gdy usługa jest w użyciu, dalsze zmniejszenie kosztów.
![Usługa Azure SQL Data Warehouse jest szybsze i tańsze niż usługi Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>źródła: [Raport analityka badawczy Joego: Data Warehouse za pomocą testu wydajności chmury](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

### <a name="query-concurrency"></a>Zapytanie współbieżności
Usługa SQL Data Warehouse gwarantuje również, że dane są dostępne w Twojej organizacji. Microsoft oferuje ulepszone usługi dotyczące obsługi nawet 128 zapytań jednoczesnych, dzięki czemu większej liczby użytkowników mogą wysyłać zapytania tej samej bazy danych i nie zablokowane przez innych żądań. W odróżnieniu od usługi Amazon Redshift ogranicza maksymalna liczba jednoczesnych kwerend do 50, ograniczanie dostępu do danych w organizacji.

Usługa SQL Data Warehouse zapewnia te zapytania wydajności i zapytania współbieżności zyski bez żadnych wzrost ceny i tworzenie po jego unikatowa architektura Dzięki rozdzieleniu magazynu i mocy obliczeniowej.

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Bardziej szczegółowy dla wielu regionów i serwera
Możesz teraz przywrócić różnych regionach i serwerami przy użyciu dowolnego punktu przywracania zamiast zaznaczania geograficznie nadmiarowych kopii zapasowych, które są wykonywane co 24 godziny. Krzyżowe przywracania region i serwera są obsługiwane w przypadku obu punktów przywracania zdefiniowane przez użytkownika lub automatyczne, włączenie bardziej szczegółowy dla dodatkowej ochrony danych. Za pomocą więcej dostępnych punktów przywracania możesz mieć pewność, że magazyn danych będzie logicznie spójnego Przywracanie między regionami.

![Przywracanie usługi Azure SQL Data Warehouse Command -](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![opcje przywracania — usługa Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Aby uzyskać więcej informacji, zobacz [Accelerated i punkty przywracania elastyczne](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) wpis w blogu.

## <a name="20-minute-restorations"></a>Operacje przywracania 20 minut
Usługa SQL Data Warehouse oferuje teraz Przywracanie każdy magazyn danych jest **mniej niż 20 minut** w tym samym regionie, niezależnie od rozmiaru bazy danych. Czas przywracania dotyczy zarówno przywracania jest w tej samej lub różnych serwera logicznego w ramach tego samego regionu. Ponadto proces migawki został ulepszony, aby zmniejszyć ilość czasu, jaki zajmuje utworzenie punktu przywracania. W dolnym jest ulepszanie poziomy wydajności (niższe ustawień jednostek DWU) *redukcji 2 x* w czasie tworzenia migawki.

Aby uzyskać więcej informacji, zobacz [Accelerated i punkty przywracania elastyczne](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) wpis w blogu.

## <a name="custom-restoration-configurations"></a>Konfiguracje niestandardowe przywracania
Podczas przywracania w witrynie Azure portal, można teraz zmienić poziom wydajności (magazynu danych DWU). Możesz również przywrócić uaktualnionego Gen2 data warehouse. Przywracając wystąpienia Gen 2, możesz teraz ocenić wpływ Gen2 przed [Uaktualnianie magazynu danych Gen1](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Konfiguracja przywracania niestandardowe — Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
[Sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) procedury składowanej jest często używana przez narzędzia można uzyskać metadanych o parametrach w zadaniu wsadowym języka Transact-SQL. Procedura zwraca jeden wiersz dla każdego parametru w usłudze batch z typem ustalonym informacje dla tego parametru. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Zestaw wyników zawiera metadane dotyczące `@id` parametr (wyniki częściowe pokazano)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
[Polecenia sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) procedury składowanej aktualizacji metadanych dla obiektu bazy danych, jeśli odpowiednie metadane stał się nieaktualne z powodu zmian obiektów. Może to wystąpić, jeśli są modyfikowane tabel podstawowych w celu wyświetlenia i widoku nie zostały utworzone ponownie. Zapisuje dany krok porzucenie i ponowne utworzenie obiektów zależnych.

W poniższym przykładzie pokazano widok, który staje się nieaktualne z powodu zmiany tabeli podstawowej. Można zauważyć, że dane są poprawne dla pierwszego zmiany kolumn (1-Mollie), ale nazwa kolumny jest nieprawidłowa i druga kolumna nie jest obecny. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

## <a name="next-steps"></a>Kolejne kroki
Po użytkownik podstawową wiedzę na temat usługi SQL Data Warehouse, Dowiedz się, jak szybko [utworzyć SQL Data Warehouse][create a SQL Data Warehouse]. Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure][Azure glossary] może pomóc Ci zaznajomić się z nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

* [Historie sukcesu klientów]
* [Blogi]
* [Żądania funkcji]
* [Filmy wideo]
* [Blogi zespołu doradczego klientów]
* [Forum Stack Overflow]
* [Twitter]


[Blogi]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogi zespołu doradczego klientów]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Historie sukcesu klientów]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Żądania funkcji]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

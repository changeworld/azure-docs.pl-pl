---
title: Co to jest Azure SQL Data Warehouse? | Microsoft Docs
description: Rozproszona baza danych klasy korporacyjnej, która może przetwarzać woluminy zawierające petabajty danych relacyjnych i nierelacyjnych. Jest to pierwszy magazyn danych w chmurze w branży, którym umożliwia powiększanie, zmniejszanie i wstrzymanie w ciągu kilku sekund.
services: sql-data-warehouse
author: igorstanko
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 04/17/2018
ms.author: igorstan
ms.reviewer: igorstan
ms.openlocfilehash: 1937d96db96c00af7f004ef4c22c4985499e393e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476111"
---
# <a name="what-is-azure-sql-data-warehouse"></a>Co to jest Azure SQL Data Warehouse?

Usługa SQL Data Warehouse to oparty na chmurze magazyn danych przedsiębiorstwa (EDW), który wykorzystuje architekturę MPP (Massively Parallel Processing) do szybkiego uruchamiania złożonych zapytań obejmujących petabajty danych. Użyj usługi SQL Data Warehouse jako głównego składnika rozwiązania danych big data. Zaimportuj dane big data do usługi SQL Data Warehouse przy użyciu prostego [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) zapytań T-SQL, a następnie użyj zaawansowanych funkcji architektury MPP do uruchomienia analizy o wysokiej wydajności. Podczas integracji i analizy hurtownia danych stanie się pojedynczym źródłem danych, na którym Twoja firma może polegać przy szukaniu szczegółowych informacji.  


## <a name="key-component-of-big-data-solution"></a>Kluczowy składnik rozwiązania danych big data
Usługa SQL Data Warehouse to kluczowy składnik całościowego rozwiązania danych big data w chmurze.

![Rozwiązanie magazynu danych](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

W rozwiązaniu danych w chmurze dane są pozyskiwane do magazynów danych big data z różnych źródeł. Platformy Hadoop, Spark i algorytmy uczenia maszynowego przygotowują i przystosowują dane, gdy te już znajdą się w magazynie danych big data. Jeśli dane są gotowe do kompleksowej analizy, usługa SQL Data Warehouse używa programu PolyBase do wykonywania zapytań kierowanych do magazynów danych big data. Program PolyBase używa standardowych zapytań T-SQL do przenoszenia danych do usługi SQL Data Warehouse.
 
Usługa SQL Data Warehouse przechowuje dane w tabelach relacyjnych z magazynem kolumnowym. Ten format znacznie zmniejsza koszty przechowywania danych i poprawia wydajność zapytań. Gdy dane są przechowywane w usłudze SQL Data Warehouse, możesz uruchomić analitykę na dużą skalę. W porównaniu do tradycyjnych systemów bazy danych, zapytania analizy kończą się w ciągu sekund zamiast minut lub godzin zamiast dni. 

Wyniki analizy można odnieść do ogólnoświatowych baz danych raportowania lub aplikacji. Analitycy biznesowi mogą zatem uzyskiwać wyniki analizy w celu świadomego podejmowania decyzji biznesowych.


## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz już podstawową wiedzę na temat usługi SQL Data Warehouse, możesz dowiedzieć się, jak szybko [utworzyć bazę danych w usłudze SQL Data Warehouse][create a SQL Data Warehouse] i [ładowanie danych przykładowych][load sample data]. Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure][Azure glossary] może pomóc Ci zaznajomić się z nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

* [Historie sukcesu klientów]
* [Blogi]
* [Żądania funkcji]
* [Filmy wideo]
* [Blogi zespołu doradczego klientów]
* [Tworzenie biletu pomocy technicznej]
* [Forum MSDN]
* [Forum Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Tworzenie biletu pomocy technicznej]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Historie sukcesu klientów]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogi]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogi zespołu doradczego klientów]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Żądania funkcji]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/

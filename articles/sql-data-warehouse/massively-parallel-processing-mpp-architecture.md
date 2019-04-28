---
title: Usługa Azure SQL Data Warehouse — architektura MPP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Azure SQL Data Warehouse łączy masowego przetwarzania równoległego (MPP) z usługą Azure storage w celu osiągnięcia wysokiej wydajności i skalowalności.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0c2ad7e5a707c20db2773324e8047eedaad1a48b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077122"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Usługa Azure SQL Data Warehouse — równoległego (MPP) architekturę przetwarzania
Dowiedz się, jak Azure SQL Data Warehouse łączy masowego przetwarzania równoległego (MPP) z usługą Azure storage w celu osiągnięcia wysokiej wydajności i skalowalności. 

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="mpp-architecture-components"></a>Składniki architektury MPP
Usługa SQL Data Warehouse korzysta z skalowania w poziomie architekturę do dystrybucji obliczeniową przetwarzania danych w wielu węzłach. Jednostka skalowania jest klasą abstrakcyjną mocy obliczeniowej, który jest znany jako [data warehouse Unit, jednostka](what-is-a-data-warehouse-unit-dwu-cdwu.md). Usługa SQL Data Warehouse oddziela obliczeń z usługi storage umożliwia skalowanie zasobów obliczeniowych niezależnie od danych w Twoim systemie.

![Architektura usługi SQL Data Warehouse](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Usługa SQL Data Warehouse korzysta z architektury oparte na węzłach. Aplikacje łączenia i wysyłania poleceń języka T-SQL do węzeł kontrolny jest pojedynczym punktem wejścia dla magazynu danych. Węzeł kontrolny działa aparat MPP, który optymalizuje zapytania do przetwarzania równoległego, a następnie przekazuje operacje do węzłów obliczeniowych, które wykonują swoje zadania równolegle. Węzły obliczeniowe przechowują wszystkie dane użytkownika w usłudze Azure Storage i uruchamiania zapytań równoległych. Data Movement Service (DMS) to usługa wewnętrzny poziom systemu, która przenosi dane w węzłach, co jest niezbędne do uruchamiania zapytań równolegle i zwracać prawidłowych wyników. 

Dzięki oddzieleniu magazynu od zasobów obliczeniowych usługa SQL Data Warehouse może wykonywać następujące zadania:

* Niezależnie od siebie rozmiar obliczeniowa niezależnie od potrzeb dotyczących magazynu.
* Zwiększać i zmniejszać moc obliczeniową bez przenoszenia danych.
* Wstrzymywać moc jest obliczeniową ingerowania w dane, dzięki czemu płacisz tylko za magazyn.
* Wznawiać moc obliczeniową w godzinach działania.

### <a name="azure-storage"></a>Azure Storage
Usługa SQL Data Warehouse używa usługi Azure storage, aby zapewnić bezpieczeństwo danych użytkowników.  Ponieważ dane są przechowywane i zarządzane przez usługę Azure storage, SQL Data Warehouse opłaty oddzielnie za zużycie miejsca w magazynie. Dane są podzielone na fragmenty w **dystrybucje** zoptymalizować wydajność systemu. Możesz wybrać wzorzec fragmentowania, których można użyć, aby dystrybuować dane podczas definiowania tabeli. Usługa SQL Data Warehouse obsługuje tych wzorców dzielenia na fragmenty:

* Skrót
* Działanie okrężne
* Replikowanie

### <a name="control-node"></a>Węzeł kontrolny

Węzeł kontrolny jest mózg w magazynie danych. Jest to fronton współdziałający ze wszystkimi aplikacjami i połączeniami. Aparat MPP działa na węzeł kontrolny, aby optymalizować i koordynowania zapytania równolegle. Po przesłaniu zapytania T-SQL w usłudze SQL Data Warehouse węzeł kontrolny przekształca je w zapytania, które uruchamiane względem każdej dystrybucji równolegle.

### <a name="compute-nodes"></a>Węzły obliczeniowe

Węzły obliczeniowe zapewniają moc obliczeniową. Mapa dystrybucji do węzłów obliczeniowych do przetworzenia. Zgodnie z którym płacisz za więcej zasobów obliczeniowych, usługa SQL Data Warehouse ponownie mapuje dystrybucji do dostępnych węzłów obliczeniowych. Liczba obliczeniowe węzłów z zakresu od 1 do 60 i zależy od poziomu usługi dla magazynu danych.

Każdy węzeł obliczeniowy ma identyfikator węzła, który jest widoczny w widokach systemu. Identyfikator węzła obliczeń możesz zobaczyć, wyszukując w kolumnie $node_id widoki systemowe, których nazwy zaczynają się od sys.pdw_nodes. Aby uzyskać listę tych widoków systemowych, zobacz [widoki systemowe MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Usługi przenoszenia danych
Data Movement Service (DMS) to technologia transport danych, która służy do koordynowania przenoszenia danych między węzłami obliczeniowymi. Niektóre zapytania wymaga przenoszenia danych, aby upewnić się, zwracają dokładne wartości zapytania równolegle. Podczas przenoszenia danych jest wymagana, usługa DMS gwarantuje, że odpowiednie dane są pobierane do odpowiedniej lokalizacji. 

## <a name="distributions"></a>Dystrybucje

Dystrybucja to podstawowa jednostka magazynu i przetwarzanie równoległe zapytania uruchamiane w danych rozproszonych. Po uruchomieniu zapytania SQL Data Warehouse pracy jest podzielony na 60 mniejsze zapytania, które są uruchamiane równolegle. Każdy z 60 mniejszych zapytaniach jest uruchamiane na jednym z dystrybucji danych. Każdy węzeł obliczeniowy zarządza co najmniej 60 dystrybucji. Magazyn danych z zasobów obliczeniowych maksymalna ma dystrybucjami na węźle obliczeniowym. Magazyn danych z zasobów obliczeniowych minimalne ma wszystkie dystrybucje w węźle obliczeniowym jeden.  

## <a name="hash-distributed-tables"></a>Tabele rozproszone wyznaczania wartości skrótu
Rozproszona Tabela skrótów zapewnia najwyższą wydajność zapytań do sprzęgania i agregacji w dużych tabel. 

Współdzielenie danych do tabeli dystrybucji skrótów SQL Data Warehouse używa funkcji skrótu do przypisze w ten sposób każdy wiersz do dystrybucjami. W definicji tabeli jedna z kolumn jest wyznaczony jako kolumna dystrybucji. Funkcja skrótu używa wartości w kolumnie dystrybucji można przypisać każdego wiersza do dystrybucji.

Na poniższym diagramie przedstawiono, jak pełne (tabelę nierozpowszechniony) zostaje zapisany jako tabelę rozproszonych wyznaczania wartości skrótu. 

![Tabela rozproszonych](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "tabeli rozproszonych")  

* Każdy wiersz należy do jednego dystrybucji.  
* Algorytm skrótu deterministyczne przypisuje każdego wiersza dystrybucjami.  
* Liczba wierszy tabeli dystrybucji różni się, jak to przedstawiono w różnych rozmiarach tabel.

Istnieją pewne zagadnienia dotyczące wydajności, wybór kolumny dystrybucji, takich jak odrębności niesymetryczność danych i typów kwerend, które są uruchamiane w systemie.

## <a name="round-robin-distributed-tables"></a>Działanie okrężne rozproszone tabele
Tabela działanie okrężne jest najprostszym tabeli, aby utworzyć i zapewnia wysoką wydajność, gdy jest używana jako tabelę tymczasową obciążeń roboczych.

Działanie okrężne dystrybuowanej tabeli rozprowadza dane równomiernie między tabeli, ale bez żadnych dalsza optymalizacja. Dystrybucji najpierw jest wybierany losowo, a następnie buforów wiersze są przypisane do dystrybucji sekwencyjnie. Jest to szybkie ładowanie danych do tabeli działania okrężnego, ale wydajność zapytań można lepiej korzystać z tabel skrótów rozproszonych. Sprzężenia w tabele wykorzystujące działanie okrężne wymagają losowego grupowania danych, a ta zajmuje dodatkowy czas.


## <a name="replicated-tables"></a>Zreplikowane tabele
Replikowanej tabeli zapewnia największą wydajność zapytań dla małych tabel.

Tabela, która jest replikowana buforuje pełną kopię tabeli w każdym węźle obliczeniowym. W związku z tym replikowanie tabeli eliminuje potrzebę na przesyłanie danych między węzłami obliczeniowymi przed przystąpieniem do dołączania lub agregacji. Zreplikowane tabele najlepiej są wykorzystywane przy użyciu małe tabele. Magazyn dodatkowy jest wymagana i dodatkowe obciążenie, który jest naliczany podczas zapisywania danych, co dużych tabel, że to niepraktyczne.  

Na poniższym diagramie przedstawiono replikowanej tabeli. Usługi SQL Data Warehouse replikowanej tabeli są buforowane na pierwszym dystrybucji na każdym węźle obliczeniowym.  

![Tabela zreplikowany](media/sql-data-warehouse-distributed-data/replicated-table.png "zreplikowany tabeli") 

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
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/

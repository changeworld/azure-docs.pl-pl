---
title: Usługa Azure SQL Data Warehouse — architektura MPP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak magazyn danych SQL Azure łączy masowego przetwarzania równoległego (MPP) z usługą Azure storage w celu osiągnięcia wysokiej wydajności i skalowalności.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: e8fef156f4b78c9f7241c9eb9623e061f5a31fe7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799281"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Usługa Azure SQL Data Warehouse — równoległemu przetwarzaniu architektura (MPP)
Dowiedz się, jak magazyn danych SQL Azure łączy masowego przetwarzania równoległego (MPP) z usługą Azure storage w celu osiągnięcia wysokiej wydajności i skalowalności. 

## <a name="mpp-architecture-components"></a>Składniki architektury MPP
Usługi SQL Data Warehouse używa skalowania w poziomie architekturę do dystrybucji obliczeniową przetwarzania danych w wielu węzłach. Jednostka skalowania jest poboru mocy obliczeniowej, znany jako jednostki magazynu danych. Usługa SQL Data Warehouse oddziela obliczeniowe z magazynu, co pozwoli na skalowanie obliczeń niezależnie od danych w systemie.

![Architektura usługi SQL Data Warehouse](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Usługa SQL Data Warehouse korzysta z architektury oparte na węzłach. Aplikacje połączenia i wydać polecenia T-SQL węzeł kontrolny, który jest pojedynczy punkt wejścia dla magazynu danych. Węzeł kontrolny uruchamia aparat MPP, który optymalizuje zapytania w celu równoległego przetwarzania, a następnie przekazuje operacje z węzłami obliczeniowymi w pracy równolegle. Węzły obliczeniowe przechowywane są wszystkie dane użytkownika w usłudze Azure Storage i uruchamiania zapytań równoległych. Usługi przenoszenia danych (DMS) to usługa wewnętrzny poziom systemu, która przenosi dane między węzły w razie potrzeby uruchom zapytania równolegle i zwracać prawidłowych wyników. 

Dzięki oddzieleniu magazynu od zasobów obliczeniowych usługa SQL Data Warehouse może wykonywać następujące zadania:

* Rozmiar obliczeniowe niezależnie zasilania niezależnie od potrzeb magazynu.
* Zwiększać i zmniejszać moc obliczeniową bez przenoszenia danych.
* Wstrzymaj obliczania pojemności pozostawiając bez zmian, danych, więc płacisz tylko za magazynu.
* Wznawiać moc obliczeniową w godzinach działania.

### <a name="azure-storage"></a>Azure Storage
Usługa SQL Data Warehouse używa magazynu Azure do zapewniania bezpieczeństwa danych użytkownika.  Ponieważ dane są przechowywane i zarządzane przez usługę Magazyn Azure, Magazyn danych SQL oddzielnie opłaty za wykorzystania magazynu. Dane są podzielonej na **dystrybucje** w celu zoptymalizowania wydajności systemu. Można wybrać wzorzec dzielenia na fragmenty, które do użycia przy dystrybucji danych podczas definiowania tabeli. Magazyn danych SQL obsługuje tych wzorców dzielenia na fragmenty:

* Skrót
* Działanie okrężne
* Replikowanie

### <a name="control-node"></a>Węzeł kontrolny

Węzeł kontrolny jest inteligencji magazynu danych. Jest to fronton współdziałający ze wszystkimi aplikacjami i połączeniami. Aparat MPP jest uruchamiany na węzeł kontrolny zoptymalizować oraz koordynować zapytania równoległe. Po przesłaniu zapytania T-SQL do SQL Data Warehouse węzeł kontrolny przekształca je w zapytania uruchamiane przed każdym dystrybucji równolegle.

### <a name="compute-nodes"></a>Węzły obliczeniowe

Węzły obliczeniowe Podaj moc obliczeniową. Dystrybucje mapowania węzłami obliczeniowymi w celu przetwarzania. Jak płacisz za większą ilością zasobów obliczeniowych, SQL Data Warehouse ponownie mapuje dystrybucji do dostępnych węzłów obliczeniowych. Liczba obliczeniowe węzłów może się wahać od 1 do 60 i zależy od poziomu usług dla magazynu danych.

Każdy węzeł obliczeniowy ma identyfikator węzła, który jest widoczny w widokach systemu. Identyfikator węzła obliczeń widoczny szukając w kolumnie node_id widoków systemowych, których nazwy zaczynają się od sys.pdw_nodes. Aby uzyskać listę tych widoków systemowych, zobacz [widoków systemowych MPP](sql-data-warehouse-reference-tsql-statements.md).

### <a name="data-movement-service"></a>Usługa przenoszenia danych
Usługi przenoszenia danych (DMS) to technologia transportu danych, która koordynuje przenoszenia danych między węzły obliczeń. Kilka zapytań wymaga przenoszenia danych w celu zapewnienia zapytania równoległe zwracać prawidłowych wyników. Podczas przenoszenia danych jest wymagane, DMS gwarantuje, że pobiera odpowiednie dane do odpowiedniej lokalizacji. 

## <a name="distributions"></a>Dystrybucje

Dystrybucji to podstawowa jednostka magazynu i przetwarzanie równoległe zapytania uruchamiane w danych rozproszonych. Po uruchomieniu kwerendy SQL Data Warehouse pracy jest podzielona na 60 mniejsze zapytania, które są uruchamiane równolegle. Każdy z 60 mniejsze zapytania jest uruchamiany na jednym z dystrybucji danych. Każdy węzeł obliczeniowy zarządza co najmniej jednego z 60 dystrybucji. Magazyn danych z zasoby obliczeniowe maksymalną ma jeden dystrybucji na węźle obliczeń. Magazyn danych z zasoby obliczeniowe minimalna ma wszystkie dystrybucje w węźle obliczeń jeden.  

## <a name="hash-distributed-tables"></a>Tabele rozproszonych wyznaczania wartości skrótu
Rozproszone tablicy skrótów zapewnia najwyższą wydajność zapytań do sprzęgania i agregacji na dużych tabel. 

Współdzielenie danych do tabeli rozpowszechniane skrót SQL Data Warehouse używa funkcji skrótu sposób niejednoznaczny przypisać każdego wiersza do jednego. W definicji tabeli jednej z kolumn jest wyznaczona jako kolumna dystrybucji. Funkcja skrótu używa wartości w kolumnie dystrybucji można przypisać każdego wiersza do dystrybucji.

Na poniższym diagramie przedstawiono, jak Pełna (z systemem innym niż rozproszonych tabeli) pobiera przechowywane jako tabelę rozpowszechniane skrót. 

![Tabela rozproszone](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "tabeli rozproszonych")  

* Każdy wiersz należy do jednego.  
* Algorytm wyznaczania wartości skrótu deterministyczne przypisuje każdego wiersza do jednego.  
* Liczba wierszy tabeli w dystrybucji zależy, jak to przedstawiono różne rozmiary tabel.

Brak zagadnienia dotyczące wydajności wybór kolumny do dystrybucji, na przykład odrębności, Pochylenie danych oraz typy zapytań, które są uruchamiane w systemie.

## <a name="round-robin-distributed-tables"></a>Tabele rozproszonej okrężnego
Tabela okrężnego jest najprostsza tabeli, aby utworzyć i zapewnia dobrą wydajność, gdy jest używany jako tabela przemieszczania pod kątem obciążeń.

Tabeli rozproszonej okrężnego równomiernie rozdziela danych w tabeli, ale bez żadnych dalsza optymalizacja. Dystrybucji najpierw jest wybierany losowo, a następnie buforów wierszy są przypisane do dystrybucji sekwencyjnie. Jest szybkie ładowanie danych do tabeli okrężnego, ale wydajność zapytań można lepiej z tabelami skrótów rozproszonych. Sprzężenia w tabelach okrężnego wymagają losowego grupowania danych i to zajmuje dodatkowy czas.


## <a name="replicated-tables"></a>Zreplikowane tabele
Zreplikowanej tabeli zapewnia największą wydajność kwerend w przypadku małych tabel.

Tabela, która jest replikowana buforuje pełną kopię tabeli w każdym węźle obliczeń. W rezultacie replikowanie tabeli eliminuje to potrzebę na przesyłanie danych między węzłami obliczeniowymi przed join lub agregacji. Najlepiej realizować zreplikowanych tabel z tabelami mała. Wymagane jest dodatkowe miejsce do magazynowania i czy istnieją dodatkowe koszty ogólne ponoszone podczas zapisywania danych, które dużych tabel niepraktyczne.  

Na poniższym diagramie przedstawiono zreplikowanej tabeli. Dla usługi SQL Data Warehouse zreplikowanej tabeli są buforowane na pierwszym dystrybucji w każdym węźle obliczeń.  

![Tabela zreplikowane](media/sql-data-warehouse-distributed-data/replicated-table.png "zreplikowane tabeli") 

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
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/

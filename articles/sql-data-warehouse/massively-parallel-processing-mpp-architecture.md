---
title: Architektura usługi Azure Synapse Analytics (dawniej SQL DW) | Microsoft Docs
description: Dowiedz się, jak usługa Azure Synapse Analytics (dawniej SQL DW) łączy masowe przetwarzanie równoległe (MPP) z usługą Azure Storage w celu uzyskania wysokiej wydajności i skalowalności.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b463b0806d39ba20ae714c8785e5c0d227ce481b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466389"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Architektura usługi Azure Synapse Analytics (dawniej SQL DW) 

Azure Synapse to nieograniczona usługa analizy, która łączy magazyn danych w przedsiębiorstwie i analizę danych Big Data. Zapewnia to swobodę wykonywania zapytań dotyczących danych na Twoich warunkach, korzystając z niezależnych od serwera zasobów na żądanie lub aprowizacji. Usługa Azure Synapse udostępnia te dwa światy wraz z ujednoliconym doświadczeniem do pozyskiwania, przygotowywania, zarządzania i obsługi danych w celu natychmiastowej analizy biznesowej i uczenia maszynowego.

 Usługa Azure Synapse ma cztery składniki:
- Analiza SQL: Pełna analiza oparta na języku T-SQL 
    - Pula SQL (płatność za jednostek DWUd) — ogólnie dostępna
    - SQL na żądanie (płatność za 1 TB przetworzonych) — (wersja zapoznawcza)
- Spark: głębokie zintegrowane Apache Spark (wersja zapoznawcza) 
- Integracja danych: integracja danych hybrydowych (wersja zapoznawcza)
- Studio: ujednolicone środowisko użytkownika.  (Wersja zapoznawcza)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>Składniki architektury MPP usługi SQL Analytics

[Analiza SQL](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) wykorzystuje architekturę skalowania w poziomie do dystrybucji obliczeniowego przetwarzania danych w wielu węzłach. Jednostka skali jest abstrakcją mocy obliczeniowej, która jest znana jako [Jednostka magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md). Obliczenia są niezależne od magazynu, co umożliwia skalowanie obliczeniowe niezależnie od danych w systemie.

![Architektura usługi SQL Analytics](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

W analizie SQL jest stosowana Architektura oparta na węzłach. Aplikacje nawiązują połączenie i wydają polecenia T-SQL do węzła kontrolnego, który jest pojedynczym punktem wejścia dla usługi SQL Analytics. Węzeł kontrolny uruchamia aparat MPP, który optymalizuje zapytania do przetwarzania równoległego, a następnie przekazuje operacje do węzłów obliczeniowych w celu wykonywania równoległych zadań. 

Węzły obliczeniowe przechowują wszystkie dane użytkownika w usłudze Azure Storage i uruchamiają zapytania równoległe. Usługa przenoszenia danych (DMS) to wewnętrzna usługa na poziomie systemu, która przenosi dane między węzłami w razie potrzeby, aby uruchamiać zapytania równolegle i zwracać dokładne wyniki. 

Korzystając z oddzielonego magazynu i obliczeń, w przypadku korzystania z usługi SQL Analytics można:

* Niezależnie od potrzeb związanych z magazynem bez względu na wielkość mocy obliczeniowej.
* Zwiększ lub Zmniejsz moc obliczeniową w puli SQL (hurtowni danych) bez przeniesienia danych.
* Wstrzymaj pojemność obliczeniową, pozostawiając nienaruszone dane, dzięki czemu płacisz tylko za magazyn.
* Wznawiać moc obliczeniową w godzinach działania.

### <a name="azure-storage"></a>Azure Storage

Usługa SQL Analytics korzysta z usługi Azure Storage, aby zapewnić bezpieczeństwo danych użytkownika.  Ponieważ dane są przechowywane i zarządzane przez usługę Azure Storage, istnieje oddzielna opłata za użycie magazynu. Same dane są podzielonej na fragmenty do **dystrybucji** w celu zoptymalizowania wydajności systemu. Można wybrać, który wzorzec fragmentowania ma być używany do dystrybucji danych podczas definiowania tabeli. Te wzorce fragmentowania są obsługiwane:

* Skrót
* Działanie okrężne
* Replikowanie

### <a name="control-node"></a>Węzeł kontrolny

Węzeł kontrolny jest mózgiem architektury. Jest to fronton współdziałający ze wszystkimi aplikacjami i połączeniami. Aparat MPP jest uruchamiany w węźle kontroli w celu optymalizacji i koordynowania zapytań równoległych. Po przesłaniu zapytania T-SQL do usługi SQL Analytics węzeł kontrolny przekształca go w zapytania, które są wykonywane równolegle do każdej dystrybucji.

### <a name="compute-nodes"></a>Węzły obliczeniowe

Węzły obliczeniowe zapewniają moc obliczeniową. Dystrybucje są mapowane na węzły obliczeniowe do przetwarzania. Gdy płacisz za więcej zasobów obliczeniowych, program SQL Analytics ponownie mapuje dystrybucje do dostępnych węzłów obliczeniowych. Liczba węzłów obliczeniowych z zakresu od 1 do 60 i jest określana na podstawie poziomu usług dla usługi SQL Analytics.

Każdy węzeł obliczeniowy ma identyfikator węzła, który jest widoczny w widokach systemu. IDENTYFIKATOR węzła obliczeniowego można zobaczyć, szukając kolumny NODE_ID w widokach systemowych, których nazwy zaczynają się od pliku sys. PDW _nodes. Aby zapoznać się z listą tych widoków systemowych, zobacz [widoki systemowe MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Usługa przenoszenia danych
Usługa przenoszenia danych (DMS) to technologia transportu danych, która koordynuje przenoszenie danych między węzłami obliczeniowymi. Niektóre zapytania wymagają przeniesienia danych, aby upewnić się, że zapytania równoległe zwracają dokładne wyniki. Gdy przenoszenie danych jest wymagane, usługa DMS gwarantuje, że odpowiednie dane będą pobierane do odpowiedniej lokalizacji. 

## <a name="distributions"></a>Dystrybucji

Dystrybucja to podstawowa jednostka magazynu i przetwarzania dla zapytań równoległych, które są uruchamiane na danych rozproszonych. Gdy analityczne SQL uruchamia kwerendę, prace są podzielone na 60 mniejsze zapytania, które są uruchamiane równolegle. 

Każda z 60 mniejszych zapytań jest uruchamiana na jednym z dystrybucji danych. Każdy węzeł obliczeniowy zarządza jedną lub większą liczbą dystrybucji 60. Pula SQL z maksymalną ilość zasobów obliczeniowych ma jedną dystrybucję na każdy węzeł obliczeniowy. Pula SQL o minimalnych zasobach obliczeniowych ma wszystkie dystrybucje w jednym węźle obliczeniowym.  

## <a name="hash-distributed-tables"></a>Tabele rozproszone z mieszaniem
Tabela rozproszona w skrócie może dostarczyć najwyższą wydajność zapytań dla sprzężeń i agregacji w dużych tabelach. 

Aby fragmentu dane do tabeli rozproszonej przez funkcję mieszania, usługa SQL Analytics używa funkcji skrótu do niejednoznacznego przypisywania każdego wiersza do jednej dystrybucji. W definicji tabeli jedna z kolumn jest oznaczona jako kolumna dystrybucji. Funkcja hash używa wartości w kolumnie dystrybucja do przypisywania każdego wiersza do dystrybucji.

Na poniższym diagramie przedstawiono sposób przechowywania pełnej (nierozproszonej tabeli) jako tabeli rozproszonej przez funkcję tworzenia skrótów. 

![Tabela rozproszona](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabela rozproszona")  

* Każdy wiersz należy do jednej dystrybucji.  
* Algorytm wyznaczania wartości skrótu przypisuje każdy wiersz do jednej dystrybucji.  
* Liczba wierszy tabeli na dystrybucję jest różna, jak pokazano w różnych rozmiarach tabel.

Istnieją zagadnienia dotyczące wydajności dla wyboru kolumny dystrybucji, takie jak odrębność, pochylenie danych i typy zapytań, które są uruchamiane w systemie.

## <a name="round-robin-distributed-tables"></a>Rozproszone tabele działające w trybie okrężnym
Tabela okrężna jest najprostszą tabelą umożliwiającą tworzenie i dostarczanie szybkiej wydajności, gdy jest używana jako tabela przejściowa dla obciążeń.

Rozproszona tabela w trybie okrężnym dystrybuuje dane równomiernie w całej tabeli, ale bez żadnej dalszej optymalizacji. Najpierw wybierana jest dystrybucja, a następnie bufory wierszy są przypisywane sekwencyjnie. Ładowanie danych do tabeli okrężnej jest szybkie, ale wydajność zapytań może być często lepsza w przypadku tabel rozproszonych za pomocą skrótów. Sprzężenia w tabelach działania okrężnego wymagają danych reshuffling i trwają dodatkowy czas.


## <a name="replicated-tables"></a>Zreplikowane tabele
Replikowana tabela zapewnia najszybszą wydajność zapytań dla małych tabel.

Replikowana pamięć podręczna zawiera pełną kopię tabeli w każdym węźle obliczeniowym. W związku z tym replikowanie tabeli eliminuje konieczność transferu danych między węzłami obliczeniowymi przed przypisaniem lub agregacją. Zreplikowane tabele są najlepiej wykorzystane w małych tabelach. Dodatkowy magazyn jest wymagany i istnieje dodatkowe obciążenie, które są naliczane podczas zapisywania danych, co sprawia, że duże tabele nie są praktyczne.  

Na poniższym diagramie przedstawiono zreplikowaną tabelę, która jest buforowana w pierwszej dystrybucji w każdym węźle obliczeniowym.  

![Zreplikowane tabele](media/sql-data-warehouse-distributed-data/replicated-table.png "Zreplikowane tabele") 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już usługę Azure Synapse, Dowiedz się, jak szybko [utworzyć pulę SQL][create a SQL pool] i [załadować przykładowe dane][load sample data]. Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure][Azure glossary] może pomóc Ci zaznajomić się z nową terminologią. Lub zapoznaj się z innymi zasobami usługi Azure Synapse.  

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
[create a SQL pool]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
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
[SLA for Azure Synapse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/

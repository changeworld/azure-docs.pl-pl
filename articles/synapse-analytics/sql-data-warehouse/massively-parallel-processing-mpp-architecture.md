---
title: Architektura usługi Azure Synapse Analytics (dawniej SQL DW)
description: Dowiedz się, jak usługa Azure Synapse Analytics (dawniej SQL DW) łączy w sobie przetwarzanie równoległe (MPP) z usługą Azure Storage w celu osiągnięcia wysokiej wydajności i skalowalności.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a42ec523bb1f77c48f7382283a52565c9c9273b6
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584498"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Architektura usługi Azure Synapse Analytics (dawniej SQL DW) 

Azure Synapse to nieograniczona usługa analizy, która łączy magazynowanie danych przedsiębiorstwa z analizą danych big data. Zapewnia swobodę wykonywania zapytań dotyczących danych na Twoich warunkach, korzystając z bezserwerowych zasobów na żądanie lub aprowizowanych zasobów — w dużej skali. Usługa Azure Synapse łączy te dwa światy z ujednoliconym doświadczeniem w zakresie pozyskiwania, przygotowywania, zarządzania i obsługi danych na potrzeby natychmiastowej analizy biznesowej i uczenia maszynowego.

 Usługa Azure Synapse ma cztery składniki:
- Synapse SQL: Kompletna analiza oparta na języku T-SQL 
    - Pula SQL (płatność za aprowi bez obsługi dwu) — ogólnie dostępne
    - SQL na żądanie (płatność za TB przetwarzane) - (Wersja zapoznawcza)
- Iskra: Głęboko zintegrowana platforma Apache Spark (wersja zapoznawcza)
- Integracja danych: hybrydowa integracja danych (wersja zapoznawcza)
- Studio: ujednolicone środowisko użytkownika.  (Wersja zapoznawcza)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>Składniki architektury Synapse SQL MPP

[Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) wykorzystuje architekturę skalowalną w poziomie do dystrybucji obliczeniowego przetwarzania danych w wielu węzłach. Jednostka skali jest abstrakcją mocy obliczeniowej, która jest znana jako [jednostka magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md). Obliczeń jest oddzielona od magazynu, co umożliwia skalowanie obliczeń niezależnie od danych w systemie.

![Architektura SQL synapsy](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Synapse SQL używa architektury opartej na węźle. Aplikacje łączą i wydają polecenia T-SQL do węzła Control, który jest pojedynczym punktem wejścia dla puli SQL Synapse. Węzeł Control uruchamia aparat MPP, który optymalizuje zapytania do przetwarzania równoległego, a następnie przekazuje operacje do węzłów obliczeniowych, aby wykonywać swoją pracę równolegle. 

Węzły obliczeniowe przechowują wszystkie dane użytkowników w usłudze Azure Storage i wykonują zapytania równoległe. Usługa przenoszenia danych (ang. Data Movement Service, DMS) to wewnętrzna usługa działająca na poziomie systemu, która przenosi dane pomiędzy węzłami w sposób wymagany do równoległego wykonywania zapytań i zwracania prawidłowych wyników. 

Z oddzielonych pamięci masowej i obliczeń, podczas korzystania z puli Synapse SQL można:

* Niezależnie od wielkości moc obliczeniowa, niezależnie od potrzeb pamięci masowej.
* Zwiększanie lub zmniejszanie mocy obliczeniowej w puli SQL (magazyn danych) bez przenoszenia danych.
* Wstrzymywać zasoby obliczeniowe bez wpływu na dane, płacąc tylko za przestrzeń dyskową.
* Wznawiać moc obliczeniową w godzinach działania.

### <a name="azure-storage"></a>Azure Storage

Synapse SQL wykorzystuje usługę Azure Storage, aby zapewnić bezpieczeństwo danych użytkownika.  Ponieważ dane są przechowywane i zarządzane przez usługę Azure Storage, istnieje osobna opłata za zużycie magazynu. Dane są podzielone na **dystrybucje** w celu optymalizacji wydajności systemu. Można wybrać wzorzec dzielenia na fragmenty, który ma być używany do dystrybucji danych podczas definiowania tabeli. Te wzorce dzielenia na fragmenty są obsługiwane:

* Skrót
* Działanie okrężne
* Replikowanie

### <a name="control-node"></a>Węzeł kontrolny

Węzeł Control jest mózgiem architektury. Jest to fronton współdziałający ze wszystkimi aplikacjami i połączeniami. Aparat MPP działa na węźle kontrolnym, optymalizując i koordynując zapytania równolegle. Podczas przesyłania kwerendy T-SQL, węzeł Control przekształca go w kwerendy, które są uruchamiane względem każdej dystrybucji równolegle.

### <a name="compute-nodes"></a>Węzły obliczeniowe

Węzły obliczeniowe zapewniają moc obliczeniową. Dystrybucje są mapowane do węzłów obliczeniowych do przetwarzania. Ponieważ płacisz za więcej zasobów obliczeniowych, dystrybucje są mapowane na dostępne węzły obliczeniowe. Liczba węzłów obliczeniowych waha się od 1 do 60 i jest określana przez poziom usługi dla Synapse SQL.

Każdy węzeł obliczeniowy ma identyfikator węzła, który jest widoczny w widokach systemu. Identyfikator węzła obliczeń można wyświetlić, wyszukując kolumnę node_id w widokach systemowych, których nazwy zaczynają się od pliku sys.pdw_nodes. Aby uzyskać listę tych widoków systemu, zobacz [Widoki systemu MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Usługa przenoszenia danych

Usługa przenoszenia danych (DMS) to technologia transportu danych, która koordynuje przenoszenie danych między węzłami obliczeniowymi. Niektóre zapytania wymagają przenoszenia danych, aby upewnić się, że zapytania równoległe zwracają dokładne wyniki. Gdy wymagany jest ruch danych, DMS zapewnia, że odpowiednie dane trafią do właściwej lokalizacji. 

## <a name="distributions"></a>Dystrybucji

Dystrybucja to podstawowa jednostka magazynowania i przetwarzania zapytań równoległych wykonywanych na danych rozproszonych. Po uruchomieniu kwerendy praca jest podzielona na 60 mniejszych zapytań, które są uruchamiane równolegle. 

Każdy z 60 mniejszych zapytań jest uruchamiany w jednej z dystrybucji danych. Każdy węzeł obliczeniowy zarządza jedną lub kilkoma z 60 dystrybucji. Pula SQL z maksymalnymi zasobami obliczeniowymi ma jedną dystrybucję na węzeł obliczeniowy. Pula SQL z minimalnymi zasobami obliczeniowymi ma wszystkie dystrybucje w jednym węźle obliczeniowym.  

## <a name="hash-distributed-tables"></a>Tabele dystrybuowane przy użyciu skrótu

Tabela dystrybuowana przy użyciu skrótu może zapewniać najwyższą wydajność zapytań w przypadku sprzężeń i agregacji w dużych tabelach. 

Aby podzielić dane do tabeli rozproszonej mieszania, funkcja mieszania jest używana do deterministycznie przypisać każdy wiersz do jednej dystrybucji. W definicji tabeli jedna kolumna zostaje wyznaczona jako kolumna dystrybucji. Funkcja skrótu używa wartości z kolumny dystrybucji, aby przypisać każdy wiersz do określonej dystrybucji.

Na poniższym diagramie przedstawiono sposób, w jaki pełna (tabela nie rozproszona) jest przechowywana jako tabela rozproszona mieszania. 

![Tabela rozproszona](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Tabela rozproszona")  

* Każdy wiersz należy do jednej dystrybucji.  
* Deterministyczny algorytm mieszania przypisuje każdy wiersz do jednej dystrybucji.  
* Liczba wierszy tabeli na dystrybucję różni się w zależności od różnych rozmiarów tabel.

Istnieją zagadnienia dotyczące wydajności dla wyboru kolumny dystrybucji, takie jak odrębność, pochylenie danych i typy zapytań uruchamianych w systemie.

## <a name="round-robin-distributed-tables"></a>Tabele dystrybuowane przy użyciu działania okrężnego

Tabela okrężna jest najprostszą tabelą do tworzenia i zapewnia szybką wydajność, gdy jest używana jako tabela przemieszczania dla obciążeń.

W tabeli dystrybuowanej przy użyciu działania okrężnego dane są dystrybuowane równomiernie w całej tabeli, bez dodatkowej optymalizacji. Dystrybucja jest najpierw wybierana losowo, a następnie bufory wierszy są przypisywane do dystrybucji sekwencyjnie. Ładowanie danych do tabeli z działaniem okrężnym jest szybkie, ale wydajność zapytań jest często lepsza w tabelach dystrybuowanych przy użyciu skrótu. Sprzężenia w tabelach okrężnych wymagają przetasowania danych, co zajmuje więcej czasu.

## <a name="replicated-tables"></a>Tabele replikowane

Tabela replikowana zapewnia najszybsze wykonywanie zapytań w przypadku niewielkich tabel.

Tabela, która jest replikowana buforuje pełną kopię tabeli w każdym węźle obliczeniowym. Dlatego replikowanie tabeli eliminuje konieczność przesyłania danych między węzłami obliczeniowymi przed operacją sprzężenia lub agregacji. Replikacja sprawdza się najlepiej w przypadku małych tabel. Dodatkowe miejsce do magazynowania jest wymagane i istnieje dodatkowe obciążenie, które jest ponoszone podczas zapisywania danych, które sprawiają, że duże tabele niepraktyczne.  

Poniższy diagram przedstawia replikowaną tabelę, która jest buforowana w pierwszej dystrybucji w każdym węźle obliczeniowym.  

![Tabela replikowana](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Tabela replikowana") 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz trochę o usłudze Azure Synapse, dowiedz się, jak szybko [utworzyć pulę SQL](create-data-warehouse-portal.md) i [załadować przykładowe dane](load-data-from-azure-blob-storage-using-polybase.md). Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure](../../azure-glossary-cloud-terminology.md) może pomóc Ci zaznajomić się z nową terminologią. Lub spójrz na niektóre z tych innych zasobów synapse platformy Azure.  

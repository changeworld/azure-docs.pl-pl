---
title: Usługa Azure SQL Data Warehouse — informacje o wersji grudnia 2018 r | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 0fa7004b9b0dd1ecf087566e7ce8a7d6ebf9be93
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842111"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>Co nowego w usłudze Azure SQL Data Warehouse? Grudzień 2018 r.
Usługa Azure SQL Data Warehouse odbiera ulepszenia stale. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w grudnia 2018 r.

## <a name="virtual-network-service-endpoints-generally-available"></a>Sieć wirtualna punktów końcowych usługi ogólnie dostępne
Ta wersja zawiera ogólną dostępność punktów końcowych usługi sieci wirtualnej (VNet) dla usługi Azure SQL Data Warehouse we wszystkich regionach platformy Azure. Punkty końcowe usługi sieci wirtualnej umożliwiają izolowanie łączność z serwerem logicznym z danej podsieci lub zestawu podsieci w sieci wirtualnej. Ruch do usługi Azure SQL Data Warehouse z sieci wirtualnej, zawsze pozostaje w obrębie sieci szkieletowej platformy Azure. Ta trasa bezpośrednie będzie mieć pierwszeństwo określonej trasy, przyjmujące ruch internetowy za pośrednictwem wirtualnych urządzeń sieciowych lub w środowisku lokalnym. Opłaty za nie dodatkowe opłaty są naliczane dla dostępu do sieci wirtualnych za pośrednictwem punktów końcowych usługi. Bieżący model cen [Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) stosowany jest.

W tej wersji umożliwiliśmy również łączność PolyBase [usługi Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS) za pośrednictwem [systemu plików obiektów Blob Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) sterownika (ABFS). Azure Data Lake magazynu Gen2 zapewnia wszystkich klas, które są wymagane przez pełny cykl życia analizy danych do usługi Azure Storage. Funkcje dwa istniejące usługi Azure storage, Azure Blob Storage i Azure Data Lake Storage Gen1 są połączone. Funkcje z [usługi Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), takich jak semantyki systemu plików, pliku poziom zabezpieczeń i skalowania są połączone z ekonomicznych, warstwowego magazynu i możliwości odzyskiwania wysoka dostępność/po awarii z [ Usługa Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). 

Przy użyciu technologii Polybase można również zaimportować dane do usługi Azure SQL Data Warehouse z usługi Azure Storage, chronione w sieci wirtualnej. Podobnie eksportowanie danych z usługi Azure SQL Data Warehouse do usługi Azure Storage, chronione w sieci wirtualnej jest również obsługiwana za pomocą programu Polybase. 

Aby uzyskać więcej informacji na temat punktów końcowych usługi sieci wirtualnej w usłudze Azure SQL Data Warehouse, zobacz [wpis w blogu](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) lub [dokumentacji](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json).

## <a name="automatic-performance-monitoring-preview"></a>Automatyczne (wersja zapoznawcza) do monitorowania wydajności
[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) jest teraz dostępna w wersji zapoznawczej dla usługi Azure SQL Data Warehouse. Query Store zaprojektowano w celu udzielenia odpowiedzi na zapytanie Rozwiązywanie problemów z wydajnością przez śledzenie zapytań, planom zapytań, statystyki środowiska uruchomieniowego i historię kwerendę, ułatwiające monitorowanie aktywności i wydajności magazynu danych. Query Store to zestaw wewnętrznych sklepach i dynamicznych widoków zarządzania (DMV), które umożliwiają:

- Identyfikowanie i dostosowywanie najważniejsze zapytania korzystające z zasobów
- Identyfikuj i Zwiększaj obciążeń ad-hoc
- W celu oceny wydajności zapytań i wpływu zgodnie z planem zmian w danych statystycznych, indeksów lub rozmiar systemu (ustawienie jednostek DWU)
- Zobacz pełne zapytanie w języku tekstu dla wszystkich zapytań wykonywanych

Store zapytania zawiera trzy rzeczywiste magazynów:  
- Planowanie magazynu potrzeby utrwalania informacji o planie wykonywania 
- Sklep statystyki środowiska uruchomieniowego potrzeby utrwalania informacji statystyk wykonywania
- Magazyn statystyki oczekiwania utrwalanie informacji statystyki oczekiwania. 

Usługa SQL Data Warehouse automatycznie zarządza tymi sklepami i podaj nieograniczonej liczby zapytań storied z ostatnich siedmiu dni bez dodatkowych opłat. Włączanie kwerenda Store jest tak proste, jak działająca instrukcja ALTER DATABASE języka T-SQL:

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
Aby uzyskać więcej informacji na temat Store zapytania w usłudze Azure SQL Data Warehouse, zobacz artykuł [monitorowanie wydajności za pomocą Store zapytania](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017), a Query Store dynamicznych widoków zarządzania, takich jak [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017). Oto [wpis w blogu](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) ogłoszenie.

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>Niższe warstwy wystąpień obliczeniowych dla usługi Azure SQL Data Warehouse Gen2
Gen2 magazynu danych w usłudze Azure SQL obsługuje teraz dolnej warstwy wystąpień obliczeniowych. Klienci będą mogli doświadczyć wiodącej wydajności, elastyczności i funkcji zabezpieczeń, począwszy od 100 cDWU usługi Azure SQL Data Warehouse ([jednostek magazynu danych](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)) i skalowanie do ponad 30 000 cDWU w ciągu kilku minut. Od połowy grudnia 2018 r. klienci mogą korzystać z Gen2 wydajność i elastyczność dzięki małe obliczeń warstw w [regionów](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions), za pomocą pozostałych regionach, dostępne podczas 2019 r.

Upuszczając punkt wejścia dla magazynowania danych następnej generacji, Microsoft otwiera drzwi oparte na wartości klientom, którzy chcesz ocenić wszystkie zalety hurtowni danych bezpieczne i o wysokiej wydajności, bez zgadywania, które środowisko w wersji próbnej jest najlepsze dla nich. Klienci może rozpocząć możliwie jak 100 cDWU w dół od bieżącego punktu wejścia 500 cDWU. Gen2 magazynu danych SQL w dalszym ciągu obsługują wstrzymywanie i wznawianie operacji i zbliża się poza po prostu elastyczność obliczeń. Gen2 obsługuje również magazynu kolumn nieograniczoną pojemność magazynu oraz 2,5 raza więcej pamięci na zapytanie, nawet 128 zapytań jednoczesnych i [buforowanie adaptacyjne](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/) funkcji. Te funkcje średnio Przenieś pięciokrotnie wyższą wydajność w porównaniu do tej samej jednostki magazynu danych na Gen1 w tej samej cenie. Geograficznie nadmiarowe kopie zapasowe są standardem Gen2 z ochroną danych gwarantowana wbudowane. Gen2 magazynu danych w usłudze Azure SQL jest gotowa do skalowania podczas pracy.

## <a name="columnstore-background-merge"></a>Scalanie tła magazynu kolumn
Domyślnie, Azure SQL Data Warehouse (Azure SQL DW) przechowuje dane w formacie kolumnowym micro-partycji o nazwie [rowgroups](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression). Czasami z powodu pamięci ogranicza pod indeksem czas ładowania kompilacji lub danych, może być skompresowany rowgroups z mniej niż optymalny rozmiar miliona wierszy. Rowgroups również może ulec fragmentacji z powodu usunięcia. Małe lub pofragmentowany rowgroups doprowadzić do wyższych zużycie pamięci, a także wykonywanie zapytania nieefektywne. W tej wersji usługi Azure SQL data Warehouse zadanie konserwacji magazynu kolumn w tle scala małych skompresowanych rowgroups do tworzenia większych rowgroups lepsze wykorzystanie pamięci i przyspieszyć wykonywanie zapytania.

## <a name="next-steps"></a>Kolejne kroki
Po użytkownik podstawową wiedzę na temat usługi SQL Data Warehouse, Dowiedz się, jak szybko [utworzyć SQL Data Warehouse][create a SQL Data Warehouse]. Jeśli jesteś nowym użytkownikiem platformy Azure, może się okazać [słownik platformy Azure] [ Azure glossary] przydatne, kiedy zdobędziesz doświadczenie nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

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

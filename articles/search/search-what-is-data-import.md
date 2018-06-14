---
title: Importowanie danych w usłudze Azure Search | Microsoft Docs
description: Dowiedz się, jak przekazywać dane do indeksu w usłudze Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: heidist
ms.openlocfilehash: e54b89d31042d43a14379dea6daffd3e7763a64a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796965"
---
# <a name="indexing-in-azure-search"></a>Indeksowanie w usłudze Azure Search
> [!div class="op_single_selector"]
> * [Omówienie](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

W usłudze Azure Search zapytania są wykonywane na zawartości załadowanej do [indeksu wyszukiwania](search-what-is-an-index.md). W tym artykule omówiono dwa podstawowe podejścia do ładowania zawartości do indeksu: *wypychanie* danych do indeksu programistycznie lub ustawienie [indeksatora usługi Azure Search](search-indexer-overview.md) tak, aby wskazywał obsługiwane źródło danych, i *ściąganie* danych.

## <a name="pushing-data-to-an-index"></a>Wypychanie danych do indeksu
Model wypychania, używany w celu programistycznego przesyłania danych do usługi Azure Search, jest najbardziej elastycznym podejściem. Po pierwsze nie ma żadnych ograniczeń dotyczących typu źródła danych. Do usługi Azure Search można wypchnąć dowolny zestaw danych złożony z dokumentów JSON, zakładając, że każdy dokument w zestawie danych ma pola zamapowane na pola zdefiniowane w schemacie indeksu. Po drugie nie ma żadnych ograniczeń dotyczących częstotliwości wykonywania. Możesz wypychać zmiany do indeksu tak często, jak tylko chcesz. W przypadku aplikacji wymagających bardzo niskich opóźnień (na przykład w razie konieczności synchronizowania operacji wyszukiwania z dynamicznymi bazami danych zapasów) model wypychania jest jedynym rozwiązaniem.

To podejście jest bardziej elastyczne niż model polegający na ściąganiu, ponieważ możesz przekazywać dokumenty pojedynczo lub w partiach (maksymalnie 1000 dokumentów na partię lub 16 MB zależnie od tego, który limit zostanie osiągnięty jako pierwszy). Model polegający na wypychaniu umożliwia również przekazywanie dokumentów do usługi Azure Search niezależnie od tego, gdzie znajdują się dane.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Jak wypychać dane do indeksu usługi Azure Search

Możesz załadować jeden lub wiele dokumentów do indeksu przy użyciu następujących interfejsów API:

+ [Dodawanie, aktualizowanie lub usuwanie dokumentów (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Klasa indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) lub [klasa indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Obecnie nie istnieje wsparcie narzędziowe wypychania danych za pośrednictwem portalu.

Aby zapoznać się z wprowadzeniem do każdej z metodologii, zobacz [Importowanie danych przy użyciu usługi REST](search-import-data-rest-api.md) lub [Importowanie danych przy użyciu platformy .NET](search-import-data-dotnet.md).


## <a name="pulling-data-into-an-index"></a>Ściąganie danych do indeksu
Model polegający na ściąganiu obejmuje przeszukiwanie obsługiwanego źródła danych i automatyczne przekazywanie danych do indeksu. W usłudze Azure Search ta możliwość jest implementowana za pośrednictwem *indeksatorów*, które są obecnie dostępne dla następujących platform:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer)
+ [Baza danych Azure SQL i program SQL Server na maszynach wirtualnych platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indeksatory łączą indeks ze źródłem danych (zwykle tabelą, widokiem lub równoważną strukturą) i mapują pola źródłowe na równoważne pola w indeksie. W czasie wykonywania zestaw wierszy jest automatycznie przekształcany w dane w formacie JSON i ładowany do określonego indeksu. Wszystkie indeksatory obsługują planowanie, dzięki czemu możesz określić, jak często dane mają być odświeżane. Większość indeksatorów udostępnia śledzenie zmian, jeśli źródło danych obsługuje tę funkcję. Dzięki śledzeniu zmian i usuwania istniejących dokumentów oraz rozpoznawaniu nowych dokumentów indeksatory eliminują konieczność aktywnego zarządzania danymi w indeksie. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Jak ściągać dane do indeksu usługi Azure Search

Funkcja indeksatora jest udostępniona w witrynie [Azure Portal](search-import-data-portal.md), interfejsie [API REST](/rest/api/searchservice/Indexer-operations) i zestawie [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations). 

Zaletą korzystania z portalu jest to, że usługa Azure Search zazwyczaj może wygenerować domyślny schemat indeksu, odczytując metadane zestawu źródła danych. Wygenerowany indeks można modyfikować, dopóki indeks jest przetwarzany, po czym dozwolone są tylko te zmiany schematu, które nie wymagają ponownego indeksowania. Jeśli żądane zmiany wpływają bezpośrednio na schemat, konieczne będzie odbudowanie indeksu. 

## <a name="verify-data-import-with-search-explorer"></a>Weryfikowanie importowania danych przy użyciu Eksploratora danych

Szybkim sposobem wykonania wstępnego sprawdzenia przekazywania dokumentu jest użycie **Eksploratora wyszukiwania** w portalu. Eksplorator umożliwia wykonywanie zapytań względem indeksu bez konieczności pisania kodu. Środowisko wyszukiwania jest oparte na ustawieniach domyślnych, takich jak [prosta składnia](/rest/api/searchservice/simple-query-syntax-in-azure-search) i domyślny parametr zapytania [searchMode](/rest/api/searchservice/search-documents). Wyniki są zwracane w formacie JSON, co umożliwia inspekcję całego dokumentu.

> [!TIP]
> Wiele [przykładów kodu usługi Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) obejmuje osadzone lub szybko dostępne zestawy danych, oferując łatwy sposób na rozpoczęcie pracy. Portal udostępnia również przykładowy indeksator i źródło danych składające się z małego zestawu danych nieruchomości (o nazwie „realestate-us-sample”). Po uruchomieniu wstępnie skonfigurowanego indeksatora na przykładowym źródle danych indeks zostaje utworzony i załadowany przy użyciu dokumentów, które następnie można odpytywać w Eksploratorze wyszukiwania lub za pomocą napisanego kodu.

## <a name="see-also"></a>Zobacz także

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Przewodnik po portalu: tworzenie i ładowanie indeksu oraz wykonywanie na nim zapytań](search-get-started-portal.md)
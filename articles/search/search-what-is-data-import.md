---
title: Importowanie danych w celu pozyskiwania danych do indeksu wyszukiwania — usługa Azure Search
description: Wypełnij i przekazywać dane do indeksu w usłudze Azure Search z zewnętrznych źródeł danych.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 83ca0c11ab0065929d939b7345cbd15869740bb3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024355"
---
# <a name="data-import-overview---azure-search"></a>Importowanie danych przegląd — usługa Azure Search

W usłudze Azure Search zapytania są wykonywane na zawartości ładowane i zapisywane w [indeksu wyszukiwania](search-what-is-an-index.md). W tym artykule sprawdza, czy dwa podstawowe podejścia do wypełniania indeksu: *wypychania* dane do indeksu programistycznie, lub punktów [indeksator usługi Azure Search](search-indexer-overview.md) na obsługiwanego źródła danych do  *ściągnięcia* w danych.

W przypadku każdej metody celem jest *ładowanie danych* z zewnętrznego źródła danych do indeksu usługi Azure Search. Usługa Azure Search umożliwi utworzenie pustego indeksu, ale do momentu wypchnąć ani ściągnąć dane do niej, nie jest obsługą zapytań.

## <a name="pushing-data-to-an-index"></a>Wypychanie danych do indeksu
Model wypychania, używany w celu programistycznego przesyłania danych do usługi Azure Search, jest najbardziej elastycznym podejściem. Po pierwsze nie ma żadnych ograniczeń dotyczących typu źródła danych. Do usługi Azure Search można wypchnąć dowolny zestaw danych złożony z dokumentów JSON, zakładając, że każdy dokument w zestawie danych ma pola zamapowane na pola zdefiniowane w schemacie indeksu. Po drugie nie ma żadnych ograniczeń dotyczących częstotliwości wykonywania. Możesz wypychać zmiany do indeksu tak często, jak tylko chcesz. W przypadku aplikacji wymagających bardzo niskich opóźnień (na przykład w razie konieczności synchronizowania operacji wyszukiwania z dynamicznymi bazami danych zapasów) model wypychania jest jedynym rozwiązaniem.

To podejście jest bardziej elastyczne niż model polegający na ściąganiu, ponieważ możesz przekazywać dokumenty pojedynczo lub w partiach (maksymalnie 1000 dokumentów na partię lub 16 MB zależnie od tego, który limit zostanie osiągnięty jako pierwszy). Model polegający na wypychaniu umożliwia również przekazywanie dokumentów do usługi Azure Search niezależnie od tego, gdzie znajdują się dane.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Jak wypychać dane do indeksu usługi Azure Search

Możesz załadować jeden lub wiele dokumentów do indeksu przy użyciu następujących interfejsów API:

+ [Dodawanie, aktualizowanie lub usuwanie dokumentów (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Klasa indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) lub [klasa indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Obecnie nie istnieje wsparcie narzędziowe wypychania danych za pośrednictwem portalu.

Aby zapoznać się z wprowadzeniem do każdej z metodologii, zobacz [Szybki Start: Tworzenie indeksu usługi Azure Search przy użyciu programu PowerShell i interfejsu API REST](search-create-index-rest-api.md) lub [Szybki Start: Tworzenie indeksu usługi Azure Search w C# ](search-import-data-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Operacje indeksowania: przekazanie, scalenie, mergeOrUpload, Usuń

Typ akcji indeksowania na podstawie poszczególnych dokumentów, można kontrolować określenie, czy dokument powinny zostać przekazane w pełni, scalone z istniejącą zawartością dokumentu lub usunięte.

W interfejsie API REST wysyłania żądań HTTP POST zawierających treść żądań JSON do adresu URL punktu końcowego indeksu usługi Azure Search. Poszczególne obiekty JSON w tablicy "wartość" zawiera klucz dokumentu i określa akcję indeksowania Dodawanie, aktualizowanie i lub usuwa zawartość dokumentu. Dla przykładu kodu zobacz [ładowanie dokumentów](search-create-index-rest-api.md#load-documents).

Zestaw .NET SDK spakować dane do `IndexBatch` obiektu. `IndexBatch` Hermetyzuje kolekcję `IndexAction` obiektów, z których każdy zawiera dokument i właściwości, które informują usługę Azure Search, jaką akcję wykonać na tym dokumencie. Dla przykładu kodu zobacz [konstruowania IndexBatch](search-import-data-dotnet.md#construct-indexbatch).


| @search.action | Opis | Wymagane pola dla każdego dokumentu | Uwagi |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Akcja `upload` jest podobna do akcji „upsert”, co oznacza, że dokument zostanie wstawiony, jeśli jest nowy, albo zaktualizowany/zastąpiony, jeśli już istnieje. |pole klucza oraz inne pola, które chcesz zdefiniować |Podczas aktualizowania/zastępowania istniejącego dokumentu każde pole, które nie jest określone w żądaniu, zostanie ustawione na wartość `null`. Dzieje się tak nawet wtedy, gdy pole było wcześniej ustawione na wartość inną niż null. |
| `merge` |Aktualizuje istniejący dokument o określone pola. Jeśli dokument nie istnieje w indeksie, scalanie zakończy się niepowodzeniem. |pole klucza oraz inne pola, które chcesz zdefiniować |Wszystkie pola, które określisz w żądaniu scalania, zastąpią istniejące pola w dokumencie. W zestawie SDK platformy .NET, w tym pól typu `DataType.Collection(DataType.String)`. W interfejsie API REST, w tym pól typu `Collection(Edm.String)`. Jeśli na przykład dokument zawiera pole `tags` o wartości `["budget"]` i wykonywane jest scalanie z wartością `["economy", "pool"]` dla pola `tags`, końcowa wartość pola `tags` będzie równa `["economy", "pool"]`. Nie będzie to `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Ta akcja działa jak akcja `merge`, jeśli dokument o danym kluczu już istnieje w indeksie. Jeśli dokument nie istnieje, działa jak akcja `upload` dla nowego dokumentu. |pole klucza oraz inne pola, które chcesz zdefiniować |- |
| `delete` |Usuwa określony dokument z indeksu. |tylko pole klucza |Wszystkie pola, które określisz oprócz pola klucza, zostaną zignorowane. Jeśli chcesz usunąć pojedyncze pole z dokumentu, zamiast tej akcji użyj akcji `merge` i po prostu jawnie ustaw dla pola wartość null. |

## <a name="decide-which-indexing-action-to-use"></a>Wybieranie akcji indeksowania do użycia
Aby zaimportować dane przy użyciu zestawu .NET SDK, (przekazywania, scalanie, usuwania i mergeOrUpload). W zależności od tego, którą z poniższych akcji wybierzesz, tylko określone pola muszą być uwzględnione w danym dokumencie:


### <a name="formulate-your-query"></a>Formułowanie zapytania
Istnieją dwie metody [przeszukiwania indeksu przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Pierwsza z nich polega na wysłaniu żądania HTTP POST, w ramach którego parametry zapytania są definiowane w obiekcie JSON w treści żądania. Druga metoda obejmuje wysłanie żądania HTTP GET, w ramach którego parametry zapytania są definiowane w adresie URL żądania. W przypadku żądania POST limity dotyczące rozmiaru parametrów zapytania są [luźniejsze](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) niż dla żądania GET. Z tego powodu zaleca się używanie żądania POST, o ile nie występują specjalne okoliczności, w których korzystanie z żądania GET jest wygodniejsze.

Zarówno dla żądania POST, jak i GET zawartość adresu URL żądania musi obejmować *nazwę usługi*, *nazwę indeksu* oraz odpowiednią *wersję interfejsu API* (w momencie publikowania tego dokumentu aktualna wersja interfejsu API to `2019-05-06`). W przypadku żądania GET parametry zapytania są określane w *ciągu zapytania* na końcu adresu URL. Format adresu URL został przedstawiony poniżej:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

Format dla żądania POST jest taki sam, ale parametry ciągu zapytania zawierają tylko element api-version.


## <a name="pulling-data-into-an-index"></a>Ściąganie danych do indeksu
Model polegający na ściąganiu obejmuje przeszukiwanie obsługiwanego źródła danych i automatyczne przekazywanie danych do indeksu. W usłudze Azure Search ta możliwość jest implementowana za pośrednictwem *indeksatorów*, które są obecnie dostępne dla następujących platform:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Baza danych Azure SQL Database i program SQL Server na maszynach wirtualnych platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indeksatory łączą indeks ze źródłem danych (zwykle tabelą, widokiem lub równoważną strukturą) i mapują pola źródłowe na równoważne pola w indeksie. W czasie wykonywania zestaw wierszy jest automatycznie przekształcany w dane w formacie JSON i ładowany do określonego indeksu. Wszystkie indeksatory obsługują planowanie, dzięki czemu możesz określić, jak często dane mają być odświeżane. Większość indeksatorów udostępnia śledzenie zmian, jeśli źródło danych obsługuje tę funkcję. Dzięki śledzeniu zmian i usuwania istniejących dokumentów oraz rozpoznawaniu nowych dokumentów indeksatory eliminują konieczność aktywnego zarządzania danymi w indeksie. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Jak ściągać dane do indeksu usługi Azure Search

Funkcja indeksatora jest udostępniona w witrynie [Azure Portal](search-import-data-portal.md), interfejsie [API REST](/rest/api/searchservice/Indexer-operations) i zestawie [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Zaletą korzystania z portalu jest to, że usługa Azure Search zazwyczaj może wygenerować domyślny schemat indeksu, odczytując metadane zestawu źródła danych. Wygenerowany indeks można modyfikować, dopóki indeks jest przetwarzany, po czym dozwolone są tylko te zmiany schematu, które nie wymagają ponownego indeksowania. Jeśli żądane zmiany wpływają bezpośrednio na schemat, konieczne będzie odbudowanie indeksu. 

## <a name="verify-data-import-with-search-explorer"></a>Weryfikowanie importowania danych za pomocą Eksploratora wyszukiwania

Szybkim sposobem wykonania wstępnego sprawdzenia przekazywania dokumentu jest użycie **Eksploratora wyszukiwania** w portalu. Eksplorator umożliwia wykonywanie zapytań względem indeksu bez konieczności pisania kodu. Środowisko wyszukiwania jest oparte na ustawieniach domyślnych, takich jak [prosta składnia](/rest/api/searchservice/simple-query-syntax-in-azure-search) i domyślny parametr zapytania [searchMode](/rest/api/searchservice/search-documents). Wyniki są zwracane w formacie JSON, co umożliwia inspekcję całego dokumentu.

> [!TIP]
> Wiele [przykładów kodu usługi Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) obejmuje osadzone lub szybko dostępne zestawy danych, oferując łatwy sposób na rozpoczęcie pracy. Portal udostępnia również przykładowy indeksator i źródło danych składające się z małego zestawu danych nieruchomości (o nazwie „realestate-us-sample”). Po uruchomieniu wstępnie skonfigurowanego indeksatora na źródło danych przykładowych, indeks jest utworzony i załadowany przy użyciu dokumentów, które następnie można odpytywać w Eksploratorze wyszukiwania lub przez kod, który można zapisać.

## <a name="see-also"></a>Zobacz także

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Przewodnik po portalu: tworzenie i ładowanie indeksu oraz wykonywanie na nim zapytań](search-get-started-portal.md)

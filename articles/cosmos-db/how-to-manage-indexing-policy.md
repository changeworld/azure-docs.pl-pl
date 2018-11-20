---
title: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 97fb5c2558d55b3f80f2e771971faa109a930c5f
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626488"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>Zarządzanie indeksowaniem w usłudze Azure Cosmos DB

W usłudze Azure Cosmos DB możesz wybrać, czy wszystkie elementy w kontenerze mają być indeksowane automatycznie, czy nie. Domyślnie wszystkie elementy w kontenerze usługi Azure Cosmos są indeksowane automatycznie, ale można wyłączyć automatyczne indeksowanie. Przy wyłączonym indeksowaniu można uzyskać dostęp do elementów za pomocą ich linków własnych lub za pośrednictwem zapytań z użyciem identyfikatora elementu, na przykład identyfikatora dokumentu. Można jawnie żądać dostarczenia wyników bez użycia indeksu, wprowadzając nagłówek **x-ms-documentdb-enable-scan**  w interfejsie API REST lub używając opcji żądania **EnableScanInQuery** w zestawie SDK .NET.

Przy wyłączonym indeksowaniu nadal możesz selektywnie dodawać określone elementy do indeksu. Możesz także pozostawić automatyczne indeksowanie włączone, a następnie selektywnie wykluczyć określone elementy. Konfiguracje z włączonym/wyłączonym indeksowaniem są przydatne w sytuacjach, gdy potrzebne jest wykonywanie zapytań względem podzestawu elementów.  

Liczba jednostek żądań i przepływność zapisu są proporcjonalne do liczby wartości, które mają być indeksowane, co określa zestaw uwzględniony w zasadach indeksowania. Jeśli dobrze znasz wzorce zapytań, możesz jawnie wybrać podzestaw ścieżek do uwzględnienia lub wyłączenia, aby poprawić przepływność zapisu.

## <a name="manage-indexing-using-azure-portal"></a>Zarządzanie indeksowaniem przy użyciu witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Utwórz nowe konto usługi Azure Cosmos lub wybierz istniejące.

3. Otwórz okienko **Eksplorator danych**.

4. Wybierz istniejący kontener, rozwiń go, a następnie zmodyfikuj następujące wartości:

   * Otwórz okno **Skalowanie i ustawienia**.
   * Zmień wartość **indexingMode** z *consistent* na *none* albo dołącz do indeksowania lub wyklucz z indeksowania określone ścieżki.
   * Kliknij przycisk **OK**, aby zapisać zmiany.

   ![Zarządzanie indeksowaniem przy użyciu witryny Azure Portal](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>Zarządzanie indeksowaniem przy użyciu zestawów SDK platformy Azure

### <a id="dotnet"></a>.NET SDK

Poniższy przykład pokazuje, jak jawnie dołączyć element, używając [zestawu SDK interfejsu API SQL dla platformy .NET](sql-api-sdk-dotnet.md) oraz właściwości [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective).

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

* [Indeksowanie — omówienie](index-overview.md)
* [Zasady indeksowania](index-policy.md)
* [Typy indeksów](index-types.md)
* [Ścieżki indeksów](index-paths.md)

---
title: Indeksowanie magazynu tabel Azure z usługi Azure Search | Dokumentacja firmy Microsoft
description: Dowiedz się, jak indeksowanie danych przechowywanych w magazynie tabel platformy Azure z usługi Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: a171bdd11cd2de030937927eef34d5ad9e0507af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182076"
---
# <a name="index-azure-table-storage-with-azure-search"></a>Magazyn tabel Azure indeksu z usługi Azure Search
W tym artykule pokazano, jak używać usługi Azure Search do indeksowania danych przechowywanych w magazynie tabel platformy Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Konfigurowanie indeksowania magazynu tabel Azure

Można skonfigurować indeksator magazynu tabel Azure przy użyciu tych zasobów:

* [Azure Portal](https://ms.portal.azure.com)
* Usługa Azure Search [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Usługa Azure Search [zestawu .NET SDK](https://aka.ms/search-sdk)

W tym miejscu przedstawiony przepływ przy użyciu interfejsu API REST. 

### <a name="step-1-create-a-datasource"></a>Krok 1: Utworzenie źródła danych

Źródło danych określa, które dane do indeksu, uzyskać poświadczeń potrzebnych do dostępu do danych i zasady, które umożliwiają wyszukiwanie Azure, aby wydajnie zidentyfikować zmiany danych.

Dla tabeli indeksowania, źródło danych musi mieć następujące właściwości:

- **Nazwa** jest unikatowa nazwa źródła danych w ramach usługi wyszukiwania.
- **Typ** musi być `azuretable`.
- **poświadczenia** parametr zawiera parametry połączenia konta magazynu. Zobacz [Określ poświadczenia](#Credentials) sekcji, aby uzyskać szczegółowe informacje.
- **kontener** ustawia nazwę tabeli i opcjonalnie zapytania.
    - Określ nazwę tabeli, używając `name` parametru.
    - Opcjonalnie określ zapytania przy użyciu `query` parametru. 

> [!IMPORTANT] 
> Jeśli to możliwe, użyj filtru na PartitionKey w celu poprawy wydajności. Inne zapytania wykonuje skanowanie pełne tabeli, co spowoduje niską wydajność dużych tabel. Zobacz [zagadnienia dotyczące wydajności](#Performance) sekcji.


Aby utworzyć źródło danych:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia źródła danych, zobacz [utworzyć źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Można określać poświadczeń ####

Można podać poświadczenia dla tabeli w jeden z następujących sposobów: 

- **Parametry połączenia konta magazynu pełny dostęp**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` parametry połączenia można pobrać z portalu Azure, przechodząc do **bloku konto magazynu** > **ustawienia**  >  **Klucze** (dla kont magazynu classic) lub **ustawienia** > **klucze dostępu** (w przypadku magazynu usługi Azure Resource Manager konta).
- **Konto magazynu udostępnionego ciąg połączenia podpisu dostępu**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` sygnatury dostępu współdzielonego powinny mieć listy i uprawnienia do odczytu z kontenerów (tabele w tym przypadku) i obiektów (wiersze tabeli).
-  **Sygnatury dostępu współdzielonego tabeli**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` sygnatury dostępu współdzielonego powinni mieć uprawnienia zapytania (odczyt) w tabeli.

Aby uzyskać więcej informacji w magazynie udostępnionym sygnatur dostępu, zobacz [używanie sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Jeśli używasz poświadczeń sygnatury dostępu współdzielonego, konieczne będzie okresowo aktualizowany poświadczenia źródła danych za pomocą podpisów odnowionego, aby zapobiec ich wygaśnięciem. Po wygaśnięciu poświadczeń sygnatury dostępu współdzielonego, indeksatora nie powiedzie się z komunikatem o błędzie podobny do "Poświadczenia dostarczone w parametrach połączenia są nieprawidłowe lub wygasły."  

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu
Indeks określa pola w dokumencie, atrybuty i wystąpić inne konstrukcje, które kształtu wyszukiwania.

Aby utworzyć indeks:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Aby uzyskać więcej informacji na temat tworzenia indeksów, zobacz [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Krok 3: Utwórz indeksator
Indeksator łączy źródła danych z indeksem wyszukiwania docelowych i zapewnia harmonogramu do automatyzowania odświeżania danych. 

Po utworzeniu indeks i źródło danych, możesz przystąpić do tworzenia indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator jest uruchamiane co dwie godziny. (Interwał harmonogramu jest ustawiony na "PT2H"). Aby uruchomić indeksatora co 30 minut, należy ustawić interwał o "PT30M". Najkrótszy obsługiwany interwał to pięć minut. Harmonogram jest opcjonalna. Pominięcie indeksatora działa tylko wtedy, gdy po jego utworzeniu. Można jednak uruchomić indeksatora na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na tworzenie interfejsu API indeksatora, zobacz [Utwórz indeksator](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Postępowania w przypadku nazwy innego pola
Czasami nazwy pól w indeksie istniejących różnią się od nazwy właściwości w tabeli. Pole — mapowanie służy do mapowania nazw właściwości z tabeli do nazwy pól w indeksie wyszukiwania. Aby dowiedzieć się więcej na temat mapowań pól, zobacz [mapowań pól indeksator usługi Azure Search zestawiania różnice między źródeł danych i wyszukiwania indeksów](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Obsługa kluczy dokumentu
W usłudze Azure Search klucz dokumentu unikatowo identyfikuje dokumentu. Każdy indeks musi mieć dokładnie jedno pole klucza typu `Edm.String`. Pole klucza jest wymagana dla każdego dokumentu, który jest dodawany do indeksu. (W rzeczywistości jest to jedyne pole wymagane.)

Ponieważ wiersze tabeli klucza złożonego, usługi Azure Search generuje syntetycznych pole o nazwie `Key` jest złączeniem klucza i wiersza wartości kluczy partycji. Na przykład, jeśli wiersz PartitionKey jest `PK1` i RowKey `RK1`, a następnie `Key` jest wartość pola `PK1RK1`.

> [!NOTE]
> `Key` Wartość może zawierać znaków, które są nieprawidłowe w dokumencie kluczy, takich jak łączniki. Nieprawidłowe znaki mogą dotyczyć przy użyciu `base64Encode` [funkcja mapowania pól](search-indexer-field-mappings.md#base64EncodeFunction). Jeśli to zrobisz, należy również użyć kodowanie Base64 bezpieczny adres URL podczas przekazywania dokumentów kluczy w interfejsie API wywołuje takie jak wyszukiwanie.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Przyrostowe wykrywania indeksowanie i usuwaniem.
Podczas konfigurowania uruchamiane zgodnie z harmonogramem indeksatora tabeli reindexes go tylko nowe lub zaktualizowane wierszy, zgodnie z wiersza `Timestamp` wartość. Nie trzeba określać zasady wykrywania zmian. Indeksowanie przyrostowe jest włączona automatycznie.

Aby wskazać, że niektóre dokumenty muszą zostać usunięte z indeksu, używając strategii usuwania nietrwałego. Zamiast usuwać wiersza, Dodaj właściwości, aby wskazać, że został usunięty i skonfigurować zasady usuwania nietrwałego wykrywania w elemencie datasource. Na przykład następujące zasady uzna, że wiersz został usunięty, jeśli wiersz zawiera właściwości `IsDeleted` z wartością `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Domyślnie usługi Azure Search korzysta następujący filtr zapytania: `Timestamp >= HighWaterMarkValue`. Ponieważ na tabelach platformy Azure nie ma pomocniczy indeks `Timestamp` pola tego typu zapytań wymaga skanowania pełnego tabeli i dlatego jest wolny dla dużych tabel.


Poniżej przedstawiono dwa możliwe podejścia do zwiększania wydajności indeksowania tabeli. Obie metody zależy od używania partycji tabeli: 

- Jeśli dane mogą być partycjonowane naturalnie do kilku zakresów partycji, Utwórz źródło danych i odpowiedniego indeksatora dla każdego zakresu partycji. Każdego indeksatora ma teraz przetworzyć tylko określonej partycji zakres, co zapewnia lepszą wydajność kwerend. Jeśli dane, które muszą zostać pomyślnie zindeksowane ma niewielkiej liczby partycji stały, jeszcze bardziej poprawić jakość: każdego indeksatora jest tylko skanowanie partycji. Na przykład, aby utworzyć źródło danych do przetwarzania zakres partycji z kluczami z `000` do `100`, użyj zapytania następująco: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Jeśli dane jest podzielona na partycje według czasu (na przykład możesz utworzyć nową partycję, każdego dnia lub tygodnia), należy wziąć pod uwagę następujące podejście: 
    - Za pomocą kwerendy w postaci: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitorować postęp indeksatora za pomocą [uzyskać indeksatora API stanu](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)i co pewien czas zaktualizować `<TimeStamp>` warunek kwerendy na podstawie najnowszych pomyślne-znacznik limitu górnego wartości. 
    - Z tej metody Jeśli chcesz wyzwolić pełną indeksowanie, należy zresetować zapytania źródła danych oprócz zresetować indeksatora. 


## <a name="help-us-make-azure-search-better"></a>Pomóż nam udoskonalić usługę Azure Search
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszeń przesłać je w naszym [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

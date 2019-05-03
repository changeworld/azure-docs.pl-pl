---
title: Indeksowanie zawartości z usługi Azure Table storage w celu wyszukiwania pełnotekstowego — usługa Azure Search
description: Dowiedz się, jak i indeksowanie danych przechowywanych w usłudze Azure Table storage za pomocą indeksatora usługi Azure Search.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 5f0e7feb52b34a4bd29bef01925bf9ea8f84d7db
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024797"
---
# <a name="index-azure-table-storage-with-azure-search"></a>Indeks usługi Azure Table storage z usługą Azure Search
W tym artykule pokazano, jak używać usługi Azure Search do indeksowania danych przechowywanych w usłudze Azure Table storage.

## <a name="set-up-azure-table-storage-indexing"></a>Konfigurowanie usługi Azure Table storage indeksowania

Indeksator usługi Azure Table storage można skonfigurować przy użyciu tych zasobów:

* [Azure Portal](https://ms.portal.azure.com)
* Usługa Azure Search [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Usługa Azure Search [zestawu SDK platformy .NET](https://aka.ms/search-sdk)

Tutaj pokażemy przepływ przy użyciu interfejsu API REST. 

### <a name="step-1-create-a-datasource"></a>Krok 1: Tworzenie źródła danych

Źródło danych określa danych do indeksu poświadczenia wymagane do dostępu do danych i zasady, które umożliwiają wydajne zidentyfikować zmiany danych w usłudze Azure Search.

Dla tabeli indeksowania, źródło danych musi mieć następujące właściwości:

- **Nazwa** unikatowa nazwa źródła danych w ramach usługi wyszukiwania.
- **Typ** musi być `azuretable`.
- **poświadczenia** parametr zawiera parametry połączenia konta magazynu. Zobacz [Określ poświadczenia](#Credentials) sekcji, aby uzyskać szczegółowe informacje.
- **kontener** ustawia nazwę tabeli i opcjonalne zapytanie.
    - Określ nazwę tabeli, używając `name` parametru.
    - Opcjonalnie określ kwerendę za pomocą `query` parametru. 

> [!IMPORTANT] 
> Zawsze, gdy jest to możliwe, użyj filtru dla właściwości PartitionKey w celu zapewnienia lepszej wydajności. Jakiekolwiek inne zapytanie wykonuje pełne skanowanie tabeli, co spowoduje niską wydajność dużych tabel. Zobacz [zagadnienia związane z wydajnością](#Performance) sekcji.


Aby utworzyć źródło danych:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia źródła danych, zobacz [Utwórz źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Sposoby, aby określić poświadczenia ####

Można podać poświadczenia dla tabeli w jednym z następujących sposobów: 

- **Parametry połączenia konta magazynu pełny dostęp**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Parametry połączenia można uzyskać w witrynie Azure portal, przechodząc do **blok konto magazynu** > **ustawienia** > **klucze** (w przypadku klasycznego konta magazynu) lub **ustawienia** > **klucze dostępu** (dla kont magazynu usługi Azure Resource Manager).
- **Konto magazynu udostępnionych parametrów połączenia sygnatury dostępu**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` Sygnatura dostępu współdzielonego powinni mieć listy i uprawnienia do odczytu w kontenerach (tabele, w tym przypadku) i obiekty (wiersze tabeli).
-  **Sygnatura dostępu współdzielonego tabeli**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` Sygnatura dostępu współdzielonego musi mieć uprawnienia do zapytań (odczyt) w tabeli.

Aby uzyskać więcej informacji na temat magazynu udostępnionego sygnatury dostępu, zobacz [używanie sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Jeśli używasz poświadczeń sygnatury dostępu współdzielonego, należy okresowo aktualizowany poświadczenia źródła danych za pomocą odnowione podpisów, aby zapobiec ich wygaśnięciem. Jeśli wygaśnięcie poświadczeń sygnatury dostępu współdzielonego, indeksator zakończy się niepowodzeniem z komunikatem o błędzie podobny do "Poświadczenia dostarczone w parametrach połączenia są nieprawidłowe lub wygasły."  

### <a name="step-2-create-an-index"></a>Krok 2: Tworzenie indeksu
Indeks określa pola w dokumencie, atrybuty, i innych konstrukcji, które kształt wyszukiwania środowiska.

Aby utworzyć indeks:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
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

### <a name="step-3-create-an-indexer"></a>Krok 3: Tworzenie indeksatora
Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i zapewnia harmonogram w celu zautomatyzowania odświeżania danych. 

Po utworzeniu indeksu i źródła danych, możesz przystąpić do tworzenia indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator jest uruchamiane co dwie godziny. (Interwał harmonogramu jest ustawiony na "PT2H"). Aby uruchomić indeksatora co 30 minut, należy ustawić interwał o "PT30M". Najkrótszy obsługiwany interwał wynosi pięć minut. Harmonogram jest opcjonalna. w przypadku pominięcia indeksatora jest uruchamiany tylko raz, po jego utworzeniu. Można jednak uruchomić indeksatora, na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat tworzenia interfejsu API indeksatora, zobacz [tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Zajmuje o nazwach innego pola
Czasami nazwy pól w indeksie istniejących różnią się od nazwy właściwości w tabeli. Mapowania pól służy do mapowania nazw właściwości z tabeli nazw pól w indeksie wyszukiwania. Aby dowiedzieć się więcej na temat mapowań pól, zobacz [mapowań pól indeksator usługi Azure Search zestawiania różnice między źródeł danych i wyszukiwania indeksów](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Obsługa kluczy dokumentu
W usłudze Azure Search klucz dokumentu jednoznacznie identyfikuje dokumentu. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu `Edm.String`. Pole klucza jest wymagana dla każdego dokumentu, który jest dodawany do indeksu. (W rzeczywistości to jedyne wymagane pole.)

Ponieważ wiersze tabeli klucza złożonego, usługa Azure Search generuje syntetycznych pole o nazwie `Key` to składa się z klucza i wiersz wartości kluczy partycji. Na przykład, jeśli wiersz PartitionKey jest `PK1` i RowKey `RK1`, a następnie `Key` jest wartość pola `PK1RK1`.

> [!NOTE]
> `Key` Wartość może zawierać znaków, które są nieprawidłowe w kluczach dokumentu, takich jak kreski. Poradzenie sobie z nieprawidłowych znaków za pomocą `base64Encode` [pola mapowania funkcji](search-indexer-field-mappings.md#base64EncodeFunction). Jeśli to zrobisz, należy również użyć kodowania Base64 bezpieczny adres URL, podczas przekazywania dokumentów kluczy w interfejsie API wywołuje takie jak wyszukiwanie.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Przyrostowe wykrywanie indeksowania i usuwanie
Po skonfigurowaniu uruchamiane zgodnie z harmonogramem indeksator table indeksuje ponownie tylko nowych lub zaktualizowanych wierszy, zgodnie z ustaleniami wiersza `Timestamp` wartość. Nie trzeba określać zasady wykrywania zmian. Indeksowanie przyrostowe jest włączona dla Ciebie automatycznie.

Aby wskazać, że niektóre dokumenty muszą zostać usunięte z indeksu, można użyć strategii usuwania nietrwałego. Zamiast usuwać wiersz, Dodaj właściwość, aby wskazać, że ma usunięty i skonfigurować zasady usuwania nietrwałego wykrywania na źródle danych. Na przykład, następujące zasady uzna, że wiersz zostanie usunięta, jeśli wiersz zawiera właściwości `IsDeleted` wartością `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
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

Domyślnie usługa Azure Search używa następujących filtr zapytania: `Timestamp >= HighWaterMarkValue`. Ponieważ tabele platformy Azure nie ma indeks pomocniczy `Timestamp` pola tego typu zapytania wymaga pełne skanowanie tabeli i dlatego jest wolny dla dużych tabel.


Poniżej przedstawiono dwa możliwe podejścia do poprawy wydajności indeksowania tabeli. Obie metody zależy od używania partycji tabeli: 

- Jeśli dane naturalnie można podzielić na partycje w kilku zakresów partycji, należy utworzyć źródło danych i odpowiedniego indeksatora dla każdego zakresu partycji. Każdego indeksatora ma teraz przetworzyć tylko w określonej partycji zakresie skutkuje lepszą wydajność zapytań. Jeśli dane, które muszą zostać pomyślnie zindeksowane niewielkiej liczby partycji stały, jeszcze lepsze: każdego indeksatora jest tylko skanowanie partycji. Na przykład, aby utworzyć źródło danych do przetwarzania zakres partycji przy użyciu kluczy z `000` do `100`, użyj zapytania następująco: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Jeśli danych jest podzielona na partycje według czasu (na przykład możesz utworzyć nową partycję, każdego dnia lub tygodnia), należy wziąć pod uwagę następujące podejście: 
    - Użyj kwerendy w postaci: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitorować postęp indeksatora za pomocą [uzyskać stan interfejsu API indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)i co pewien czas zaktualizować `<TimeStamp>` warunek kwerendy na podstawie najnowszych pomyślne — znacznik limitu górnego wartości. 
    - W przypadku tej metody Jeśli chcesz wyzwolić pełną indeksowanie należy zresetować zapytanie źródła danych, oprócz zresetować indeksatora. 


## <a name="help-us-make-azure-search-better"></a>Pomóż nam ulepszyć usługę Azure Search
Jeśli masz sugestie funkcji lub pomysły dotyczące ulepszeń, zgłoś je na naszych [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

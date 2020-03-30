---
title: Wyszukiwanie zawartości magazynu tabel platformy Azure
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować dane przechowywane w magazynie tabel platformy Azure za pomocą indeksatora usługi Azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8f6c0454497b1cb1d62417e566e9662469c56d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113002"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Jak indeksować tabele z magazynu tabel platformy Azure za pomocą usługi Azure Cognitive Search

W tym artykule pokazano, jak używać usługi Azure Cognitive Search do indeksowania danych przechowywanych w magazynie tabel platformy Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Konfigurowanie indeksowania magazynu tabel platformy Azure

Indeksatora usługi Azure Table Storage można skonfigurować przy użyciu następujących zasobów:

* [Portal Azure](https://ms.portal.azure.com)
* [Interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) usługi Azure Cognitive Search
* Zestaw [SDK](https://aka.ms/search-sdk) usługi Azure Cognitive Search .NET

W tym miejscu możemy zademonstrować przepływ przy użyciu interfejsu API REST. 

### <a name="step-1-create-a-datasource"></a>Krok 1: Tworzenie źródła danych

Źródło danych określa, które dane mają być indeksowane, poświadczenia potrzebne do uzyskania dostępu do danych i zasady, które umożliwiają usługi Azure Cognitive Search efektywne identyfikowanie zmian w danych.

W przypadku indeksowania tabel źródło danych musi mieć następujące właściwości:

- **nazwa** jest unikatową nazwą źródła danych w usłudze wyszukiwania.
- **musi** być `azuretable`.
- parametr **credentials** zawiera parametry połączenia konta magazynu. Zobacz [określ poświadczenia](#Credentials) sekcji, aby uzyskać szczegółowe informacje.
- **kontener** ustawia nazwę tabeli i kwerendę opcjonalną.
    - Określ nazwę tabeli `name` przy użyciu parametru.
    - Opcjonalnie należy określić kwerendę przy użyciu parametru. `query` 

> [!IMPORTANT] 
> Jeśli to możliwe, użyj filtru na PartitionKey, aby uzyskać lepszą wydajność. Każde inne zapytanie wykonuje skanowanie pełnej tabeli, co powoduje niską wydajność dużych tabel. Zobacz [zagadnienia dotyczące wydajności](#Performance) sekcji.


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

Aby uzyskać więcej informacji na temat interfejsu API tworzenia źródła danych, zobacz [Tworzenie źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Sposoby określania poświadczeń ####

Poświadczenia dla tabeli można podać w jeden z następujących sposobów: 

- **Parametry połączenia konta magazynu pełnego dostępu:** `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` ciąg połączenia można uzyskać z witryny Azure Portal, przechodząc do**kluczy** **ustawień** >  **bloku** > konta magazynu (dla klasycznych kont magazynu) lub**kluczy dostępu do** **ustawień** > (dla kont magazynu usługi Azure Resource Manager).
- **Parametry połączenia sygnatury dostępu współdzielonego konta magazynu:** `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` Sygnatura dostępu współdzielonego powinna mieć uprawnienia do listy i odczytu w kontenerach (tabele w tym przypadku) i obiektach (wiersze tabeli).
-  **Podpis dostępu współdzielonego tabeli:** `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` W tabeli powinny znajdować się uprawnienia kwerendy (odczytu) kwerendy.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego magazynu, zobacz [Korzystanie z podpisów dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Jeśli używasz poświadczeń podpisu dostępu współdzielonego, należy okresowo aktualizować poświadczenia źródła danych za pomocą odnowionych podpisów, aby zapobiec ich wygaśnięciu. Jeśli poświadczenia podpisu dostępu udostępnionego wygasną, indeksator kończy się niepowodzeniem z komunikatem o błędzie podobnym do "Poświadczenia podane w ciągu połączenia są nieprawidłowe lub wygasły."  

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu
Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania.

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

Aby uzyskać więcej informacji na temat tworzenia indeksów, zobacz [Tworzenie indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Krok 3: Tworzenie indeksatora
Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i udostępnia harmonogram automatyzacji odświeżania danych. 

Po utworzeniu indeksu i źródła danych można przystąpić do tworzenia indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator działa co dwie godziny. (Interwał harmonogramu jest ustawiony na "PT2H".) Aby uruchomić indeksator co 30 minut, należy ustawić interwał na "PT30M". Najkrótszy obsługiwany interwał wynosi pięć minut. Harmonogram jest opcjonalny; jeśli pominięto, indeksator uruchamia się tylko raz podczas jego tworzenia. Jednak można uruchomić indeksatora na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia indeksatora, zobacz [Tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Aby uzyskać więcej informacji na temat definiowania harmonogramów indeksatora, zobacz [Jak zaplanować indeksatory dla usługi Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Radzić sobie z różnymi nazwami pól
Czasami nazwy pól w istniejącym indeksie różnią się od nazw właściwości w tabeli. Mapowania pól można używać do mapowania nazw właściwości z tabeli na nazwy pól w indeksie wyszukiwania. Aby dowiedzieć się więcej o mapowaniach pól, zobacz [Mapowania pól usługi Azure Cognitive Search wypełniają różnice między źródłami danych a indeksami wyszukiwania.](search-indexer-field-mappings.md)

## <a name="handle-document-keys"></a>Obsługa kluczy dokumentu
W usłudze Azure Cognitive Search klucz dokumentu jednoznacznie identyfikuje dokument. Każdy indeks wyszukiwania musi mieć dokładnie `Edm.String`jedno kluczowe pole typu . Pole klucza jest wymagane dla każdego dokumentu, który jest dodawany do indeksu. (W rzeczywistości jest to jedyne wymagane pole.)

Ponieważ wiersze tabeli mają klucz złożony, usługa Azure `Key` Cognitive Search generuje syntetyczne pole o nazwie, które jest łączenia wartości klucza partycji i klucza wiersza. Na przykład, jeśli wiersz PartitionKey `PK1` jest i `RK1`RowKey `Key` jest , `PK1RK1`a następnie wartość pola jest .

> [!NOTE]
> Wartość `Key` może zawierać znaki, które są nieprawidłowe w kluczach dokumentu, takie jak kreski. Nieprawidłowe znaki można mieć `base64Encode` za pomocą [funkcji mapowania pól](search-indexer-field-mappings.md#base64EncodeFunction). Wykonując tę czynność, pamiętaj również, aby użyć kodowania Base64 bezpiecznego dla adresu URL podczas przekazywania kluczy dokumentów w wywołaniach interfejsu API, na przykład podczas wyszukiwania.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Wykrywanie indeksowania przyrostowego i usuwania
Po skonfigurowaniu indeksatora tabel do uruchamiania zgodnie z harmonogramem ponownie indeksuje tylko nowe lub `Timestamp` zaktualizowane wiersze, zgodnie z wartością wiersza. Nie trzeba określać zasady wykrywania zmian. Indeksowanie przyrostowe jest włączone automatycznie.

Aby wskazać, że niektóre dokumenty muszą zostać usunięte z indeksu, można użyć strategii usuwania nietrwałego. Zamiast usuwać wiersz, dodaj właściwość, aby wskazać, że jest ona usunięta, i skonfiguruj zasady wykrywania usuwania nietrwale w źródle danych. Na przykład następująca zasada uwzględnia, że wiersz jest usuwany, jeśli wiersz ma właściwość `IsDeleted` o wartości: `"true"`

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

Domyślnie usługa Azure Cognitive Search używa `Timestamp >= HighWaterMarkValue`następującego filtru zapytań: . Ponieważ tabele platformy Azure nie mają `Timestamp` indeksu pomocniczego w polu, ten typ kwerendy wymaga pełnego skanowania tabeli i dlatego jest powolny dla dużych tabel.


Oto dwa możliwe podejścia do poprawy wydajności indeksowania tabel. Oba te podejścia opierają się na użyciu partycji tabeli: 

- Jeśli dane mogą być naturalnie podzielone na kilka zakresów partycji, należy utworzyć źródło danych i odpowiadający mu indeksator dla każdego zakresu partycji. Każdy indeksator musi teraz przetwarzać tylko określony zakres partycji, co powoduje lepszą wydajność kwerendy. Jeśli dane, które muszą być indeksowane ma niewielką liczbę stałych partycji, nawet lepiej: każdy indeksator tylko nie skanowania partycji. Na przykład, aby utworzyć źródło danych do przetwarzania `000` `100`zakresu partycji z kluczami od do , użyj kwerendy w ten sposób: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Jeśli dane są podzielone na partycje według czasu (na przykład tworzysz nową partycję codziennie lub tydzień), należy wziąć pod uwagę następujące podejście: 
    - Użyj kwerendy formularza: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitoruj postęp indeksatora przy użyciu interfejsu API `<TimeStamp>` stanu [indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)i okresowo aktualizuj stan kwerendy na podstawie najnowszej pomyślnej wartości znaku wysokiej wody. 
    - Dzięki takiemu podejściu, jeśli trzeba wyzwolić pełną reindexing, należy zresetować kwerendę źródła danych oprócz resetowania indeksatora. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Pomóż nam poprawić usługę Azure Cognitive Search
Jeśli masz prośby o ulepszenia lub pomysły dotyczące ulepszeń, prześlij je na naszej [stronie UserVoice.](https://feedback.azure.com/forums/263029-azure-search/)

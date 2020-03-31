---
title: Konfigurowanie pamięci podręcznej i wzbogacenia przyrostowego (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Włącz buforowanie i zachowaj stan wzbogaconej zawartości do kontrolowanego przetwarzania w zestawie umiejętności poznawczych. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989556"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Jak skonfigurować buforowanie w celu przyrostowego wzbogacenia w usłudze Azure Cognitive Search

> [!IMPORTANT] 
> Przyrostowe wzbogacenie jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Ta funkcja zapewnia [interfejs API REST w wersji 2019-05-06-Preview.](search-api-preview.md) Obecnie nie ma obsługi portalu ani SDK .NET.

W tym artykule pokazano, jak dodać buforowanie do potoku wzbogacania, dzięki czemu można stopniowo modyfikować kroki bez konieczności przebudowy za każdym razem. Domyślnie zestaw umiejętności jest bezstanowy, a zmiana dowolnej części jego składu wymaga pełnego ponownego uruchomienia indeksatora. Dzięki przyrostowe wzbogacenie indeksatora można określić, które części drzewa dokumentu muszą być odświeżane na podstawie zmian wykrytych w skillset lub definicje indeksatora. Istniejące przetworzone dane wyjściowe są zachowywane i ponownie publikowane tam, gdzie jest to możliwe. 

Zawartość w pamięci podręcznej jest umieszczana w usłudze Azure Storage przy użyciu podawania informacji o koncie. Kontener o `ms-az-search-indexercache-<alpha-numerc-string>`nazwie , jest tworzony po uruchomieniu indeksatora. Należy go uznać za wewnętrzny składnik zarządzany przez usługę wyszukiwania i nie może być modyfikowany.

Jeśli nie jesteś zaznajomiony z konfigurowaniem indeksatorów, zacznij od [przeglądu indeksatora,](search-indexer-overview.md) a następnie kontynuuj do [skillsets,](cognitive-search-working-with-skillsets.md) aby dowiedzieć się więcej o potokach wzbogacania. Aby uzyskać więcej informacji na temat kluczowych pojęć, zobacz [wzbogacenie przyrostowe](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Włączanie buforowania w istniejącym indeksatorze

Jeśli masz istniejący indeksator, który ma już zestaw umiejętności, wykonaj kroki opisane w tej sekcji, aby dodać buforowanie. Jako operacja jednorazowa, trzeba będzie zresetować i ponownie indeksatora w całości, zanim przyrostowe przetwarzania może wejść w życie.

> [!TIP]
> Jako dowód koncepcji, można uruchomić za pośrednictwem tego [portalu Szybki start](cognitive-search-quickstart-blob.md) do tworzenia niezbędnych obiektów, a następnie za pomocą postmana lub portalu do wprowadzania aktualizacji. Można dołączyć zasób usług Cognitive Services podlegać rozliczane. Uruchamianie indeksatora wiele razy wyczerpie bezpłatną alokację dzienną, zanim będzie można wykonać wszystkie kroki.

### <a name="step-1-get-the-indexer-definition"></a>Krok 1: Pobierz definicję indeksatora

Zacznij od prawidłowego, istniejącego indeksatora, który ma następujące składniki: źródło danych, zestaw umiejętności, indeks. Indeksator powinien być uruchamiany. 

Za pomocą klienta interfejsu API, skonstruować [żądanie get indeksatora,](https://docs.microsoft.com/rest/api/searchservice/get-indexer) aby uzyskać bieżącą konfigurację indeksatora. Podczas korzystania z wersji interfejsu API w wersji `cache` zapoznawczej do programu GET indeksatora, właściwość ustawiona na wartość null jest dodawany do definicji.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Skopiuj definicję indeksatora z odpowiedzi.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Krok 2: Modyfikowanie właściwości pamięci podręcznej w definicji indeksatora

Domyślnie `cache` właściwość ma wartość null. Użyj klienta interfejsu API, aby ustawić konfigurację pamięci podręcznej (portal nie obsługuje tej aktualizacji cząstek stałych). 

Zmodyfikuj obiekt pamięci podręcznej, aby uwzględnić następujące właściwości wymagane i opcjonalne: 

+ Jest `storageConnectionString` to wymagane i musi być ustawiona na ciąg połączenia magazynu platformy Azure. 
+ Właściwość logiczna `enableReprocessing` jest`true` opcjonalna (domyślnie) i wskazuje, że wzbogacenie przyrostowe jest włączone. W razie potrzeby można `false` ustawić go, aby zawiesić przetwarzanie przyrostowe, podczas gdy inne operacje intensywnie korzystające `true` z zasobów, takie jak indeksowanie nowych dokumentów, są w toku, a następnie przerzucić go z powrotem na później.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>Krok 3: Resetowanie indeksatora

Zresetowanie indeksatora jest wymagane podczas konfigurowania przyrostowego wzbogacenia dla istniejących indeksatorów, aby upewnić się, że wszystkie dokumenty są w spójnym stanie. Można użyć portalu lub klienta interfejsu API i [resetowania interfejsu API REST indeksatora](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) dla tego zadania.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Krok 4: Zapisz zaktualizowaną definicję

[Zaktualizuj indeksatora](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) za pomocą żądania PUT, treść żądania powinna zawierać zaktualizowaną definicję indeksatora, która ma właściwość pamięci podręcznej. Jeśli masz 400, sprawdź definicję indeksatora, aby upewnić się, że wszystkie wymagania są spełnione (źródło danych, skillset, index).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Jeśli teraz wydać inne żądanie GET w indeksatorze, odpowiedź `ID` z usługi będzie zawierać właściwość w obiekcie pamięci podręcznej. Ciąg alfanumeryczny jest dołączany do nazwy kontenera zawierającego wszystkie buforowane wyniki i stan pośredni każdego dokumentu przetwarzanego przez ten indeksator. Identyfikator będzie używany do unikatowej nazwy pamięci podręcznej w magazynie obiektów Blob.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>Krok 5: Uruchom indeksator

Aby uruchomić indeksatora, można użyć portalu lub interfejsu API. W portalu z listy indeksatorów wybierz indeksator i kliknij przycisk **Uruchom**. Jedną z zalet korzystania z portalu jest to, że można monitorować stan indeksatora, zanotować czas trwania zadania i liczbę dokumentów są przetwarzane. Strony portalu są odświeżane co kilka minut.

Alternatywnie można użyć REST, aby [uruchomić indeksator:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Po uruchomieniu indeksatora można znaleźć pamięć podręczną w magazynie obiektów Blob platformy Azure. Nazwa kontenera jest w następującym formacie:`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Resetowanie i ponowne uruchomienie indeksatora powoduje pełną przebudowę, dzięki czemu zawartość może być buforowana. Wszystkie wzbogacenia poznawcze zostaną ponownie uruchomić na wszystkich dokumentach.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Krok 6: Modyfikowanie zestawu umiejętności i potwierdzanie przyrostowego wzbogacenia

Aby zmodyfikować zestaw umiejętności, można użyć portalu lub interfejsu API. Na przykład, jeśli używasz tłumaczenia tekstu, `en` prosta `es` zmiana w tekście z lub innego języka jest wystarczająca do testowania weryfikacji koncepcji wzbogacenia przyrostowego.

Uruchom indeksator ponownie. Aktualizowane są tylko te części drzewa wzbogaconego dokumentu. Jeśli korzystasz z [portalu Szybki start](cognitive-search-quickstart-blob.md) jako dowód koncepcji, modyfikując umiejętność tłumaczenia tekstu na "es", zauważysz, że zamiast oryginalnej 14 zaktualizowano tylko 8 dokumentów. Pliki obrazów, na które nie ma wpływu proces tłumaczenia, są ponownie zaczesane z pamięci podręcznej.

## <a name="enable-caching-on-new-indexers"></a>Włączanie buforowania w nowych indeksatorach

Aby skonfigurować przyrostowe wzbogacenie dla nowego indeksatora, `cache` wystarczy uwzględnić właściwość w ładowności definicji indeksatora podczas wywoływania [create indexer (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer). 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Sprawdzanie danych wyjściowych w pamięci podręcznej

Pamięć podręczna jest tworzona, używana i zarządzana przez indeksatora, a jej zawartość nie jest reprezentowana w formacie, który jest czytelny dla człowieka. Najlepszym sposobem, aby ustalić, czy pamięć podręczna jest używana jest przez uruchomienie indeksatora i porównać metryki przed i po dla czasu wykonywania i liczby dokumentów. 

Załóżmy na przykład zestaw umiejętności rozpoczynający się od analizy obrazu i optycznego rozpoznawania znaków (OCR) zeskanowanych dokumentów, a następnie analiza wynikowego tekstu. Jeśli zmodyfikujesz niższą umiejętność tekstową, indeksator może pobrać całą wcześniej przetworzoną zawartość obrazu i OCR z pamięci podręcznej, aktualizując i przetwarzając tylko zmiany związane z tekstem wskazane przez zmiany. Można oczekiwać, aby zobaczyć mniej dokumentów w liczbie dokumentów (na przykład 8/8 w przeciwieństwie do 14/14 w oryginalnym przebiegu), krótszy czas wykonywania i mniej opłat na rachunku.

## <a name="working-with-the-cache"></a>Praca z pamięcią podręczną

Gdy pamięć podręczna jest operacyjna, indeksatory sprawdzić pamięci podręcznej, gdy [uruchom indeksator](https://docs.microsoft.com/rest/api/searchservice/run-indexer) jest wywoływana, aby zobaczyć, które części istniejących danych wyjściowych mogą być używane. 

W poniższej tabeli podsumowano, jak różne interfejsy API odnoszą się do pamięci podręcznej:

| interfejs API           | Wpływ pamięci podręcznej     |
|---------------|------------------|
| [Tworzenie indeksatora (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | Tworzy i uruchamia indeksator przy pierwszym użyciu, w tym tworzenie pamięci podręcznej, jeśli definicja indeksatora określa go. |
| [Uruchom indeksatora](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | Wykonuje potok wzbogacania na żądanie. Ten interfejs API odczytuje z pamięci podręcznej, jeśli istnieje lub tworzy pamięć podręczną, jeśli dodano buforowanie do zaktualizowanej definicji indeksatora. Po uruchomieniu indeksatora, który ma buforowanie włączone, indeksator pomija kroki, jeśli buforowane dane wyjściowe mogą być używane. Można użyć ogólnie dostępnej lub w wersji interfejsu API w wersji zapoznawczej tego interfejsu API.|
| [Resetowanie indeksatora](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| Czyści indeksator wszelkich przyrostowych informacji indeksowania. Następne uruchomienie indeksatora (na żądanie lub harmonogram) jest pełne ponowne przetwarzanie od podstaw, w tym ponowne uruchomienie wszystkich umiejętności i odbudowanie pamięci podręcznej. Jest funkcjonalnie równoważne do usuwania indeksatora i odtworzenia go. Można użyć ogólnie dostępnej lub w wersji interfejsu API w wersji zapoznawczej tego interfejsu API.|
| [Resetowanie umiejętności (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | Określa, które umiejętności mają być ponownie uruchamiane przy następnym uruchomieniu indeksatora, nawet jeśli nie zmodyfikowano żadnych umiejętności. Pamięć podręczna jest odpowiednio aktualizowana. Dane wyjściowe, takie jak magazyn wiedzy lub indeks wyszukiwania, są odświeżane przy użyciu danych wielokrotnego użycia z pamięci podręcznej oraz nowej zawartości według zaktualizowanej umiejętności. |

Aby uzyskać więcej informacji na temat kontrolowania, co dzieje się z pamięcią podręczną, zobacz [Zarządzanie pamięcią podręczną](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Następne kroki

Przyrostowe wzbogacenie ma zastosowanie do indeksatorów, które zawierają skillsets. W następnym kroku odwiedź dokumentację zestawu umiejętności, aby zrozumieć pojęcia i kompozycję. 

Ponadto po włączeniu pamięci podręcznej, należy wiedzieć o parametrach i interfejsach API, które mają wpływ na buforowanie, w tym jak zastąpić lub wymusić określone zachowania. Aby uzyskać więcej informacji, zobacz poniższe łącza.

+ [Koncepcje i skład skillset](cognitive-search-working-with-skillsets.md)
+ [Jak utworzyć zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Wprowadzenie do przyrostowego wzbogacania i buforowania](cognitive-search-incremental-indexing-conceptual.md)

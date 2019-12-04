---
title: Dodaj indeksowanie przyrostowe (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Włącz śledzenie zmian i Zachowaj stan wzbogaconej zawartości dla kontrolowanego przetwarzania w zestawu umiejętności poznawczym. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 348bc2d92f636d1f3c3b50ea31334355da59a60f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790504"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>Jak skonfigurować przyrostowe indeksowanie ulepszonych dokumentów na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT] 
> Indeksowanie przyrostowe jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi portalu lub zestawu SDK platformy .NET.

W tym artykule opisano sposób dodawania stanu i buforowania do dokumentów wzbogaconych za pośrednictwem potoku wzbogacenia Wyszukiwanie poznawcze platformy Azure, dzięki czemu można przyrostowo indeksować dokumenty z dowolnego obsługiwanego źródła danych. Domyślnie zestawu umiejętności jest bezstanowy i zmiana dowolnej części jej składu wymaga pełnego ponownego uruchomienia indeksatora. Dzięki funkcji indeksowania przyrostowego indeksator może określić, które części potoku uległy zmianie, ponownie używając istniejących wzbogaceń dla niezmienionych części i skorygować wzbogacanie dla kroków, które się zmieniają. Zawartość pamięci podręcznej jest umieszczana w usłudze Azure Storage.

Jeśli nie masz doświadczenia w konfigurowaniu indeksatorów, Zacznij od [omówienia indeksatora](search-indexer-overview.md) , a następnie przejdź do [umiejętności](cognitive-search-working-with-skillsets.md) , aby dowiedzieć się więcej na temat potoków wzbogacania. Aby uzyskać więcej informacji na temat kluczowych pojęć, zobacz [indeksowanie przyrostowe](cognitive-search-incremental-indexing-conceptual.md).

## <a name="modify-an-existing-indexer"></a>Modyfikowanie istniejącego indeksatora

Jeśli masz istniejący indeksator, wykonaj następujące kroki, aby włączyć indeksowanie przyrostowe.

### <a name="step-1-get-the-indexer"></a>Krok 1. Uzyskiwanie indeksatora

Rozpocznij z prawidłowym istniejącym indeksatorem, który ma już zdefiniowane źródło danych i indeks. Indeksator powinien być możliwy do uruchomienia. Za pomocą klienta interfejsu API Utwórz [żądanie Get](https://docs.microsoft.com/rest/api/searchservice/get-indexer) , aby uzyskać bieżącą konfigurację indeksatora, do którego chcesz dodać indeksowanie przyrostowe.

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>Krok 2. Dodawanie właściwości pamięci podręcznej

< < < < < < < NAGŁÓWKowym edytować odpowiedź z żądania GET, aby dodać właściwość `cache` do indeksatora. Obiekt pamięci podręcznej wymaga tylko pojedynczej właściwości, `storageConnectionString` która jest parametrami połączenia do konta magazynu. = = = = = = = Edytuj odpowiedź z żądania GET, aby dodać właściwość `cache` do indeksatora. Obiekt pamięci podręcznej wymaga tylko pojedynczej właściwości, a to parametry połączenia z kontem usługi Azure Storage.
>>>>>>> 3519a330aa86b6827d31403690529105825b1b16

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": {
        "configuration": {
            "enableAnnotationCache": true
        }
    }
}
```
#### <a name="enable-reporocessing"></a>Włącz reporocessing

Opcjonalnie można ustawić właściwość `enableReprocessing` Boolean w pamięci podręcznej, która domyślnie ma wartość true. Flaga `enableReprocessing` pozwala kontrolować zachowanie indeksatora. W scenariuszach, w których indeksator ma określić priorytety dodawania nowych dokumentów do indeksu, należy ustawić flagę na wartość false. Gdy indeksator zostanie przechwycony przy użyciu nowych dokumentów, przerzucenie flagi do wartości true spowoduje, że indeksator rozpocznie działanie istniejących dokumentów w celu zapewnienia spójności ostatecznej. W czasie, gdy flaga `enableReprocessing` ma wartość false, indeksator zapisuje tylko w pamięci podręcznej, ale nie przetwarza żadnych istniejących dokumentów na podstawie zidentyfikowanych zmian potoku wzbogacania.

### <a name="step-3-reset-the-indexer"></a>Krok 3. Resetowanie indeksatora

> [!NOTE]
> Zresetowanie indeksatora spowoduje ponowne przetworzenie wszystkich dokumentów w źródle danych, dzięki czemu zawartość może być buforowana. Wszystkie wzbogacenia poznawcze zostaną uruchomione na wszystkich dokumentach.
>

Resetowanie indeksatora jest wymagane podczas konfigurowania indeksowania przyrostowego dla istniejących indeksatorów, aby upewnić się, że wszystkie dokumenty są w spójnym stanie. Zresetuj indeksator przy użyciu [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>Krok 4. zapisywanie zaktualizowanej definicji

Zaktualizuj definicję indeksatora przy użyciu żądania PUT, treść żądania powinna zawierać zaktualizowaną definicję indeksatora.

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

Jeśli masz teraz inne żądanie GET w indeksatorze, odpowiedź z usługi będzie zawierać właściwość `cacheId` w obiekcie pamięci podręcznej. `cacheId` jest nazwą kontenera, który będzie zawierać wszystkie buforowane wyniki i stan pośredni każdego dokumentu przetworzonego przez ten indeksator.

## <a name="enable-incremental-indexing-on-new-indexers"></a>Włącz indeksowanie przyrostowe dla nowych indeksatorów

Aby skonfigurować indeksowanie przyrostowe dla nowego indeksatora, Uwzględnij Właściwość `cache` w ładunku definicji indeksatora. Upewnij się, że używasz wersji `2019-05-06-Preview` interfejsu API.

## <a name="overriding-incremental-indexing"></a>Zastępowanie indeksowania przyrostowego

Po skonfigurowaniu, indeksowanie przyrostowe śledzi zmiany w potoku indeksowania i kieruje dokumenty do spójności ostatecznej w indeksie i projekcjach. W niektórych przypadkach należy zastąpić to zachowanie, aby upewnić się, że indeksator nie wykonuje dodatkowej pracy w wyniku aktualizacji potoku indeksowania. Na przykład aktualizacja parametrów połączenia DataSource wymaga zresetowania indeksatora i ponownego indeksowania wszystkich dokumentów w miarę zmiany źródła danych. Ale jeśli aktualizujesz tylko parametry połączenia przy użyciu nowego klucza, nie chcesz, aby zmiana powodowała jakiekolwiek aktualizacje istniejących dokumentów. Z kolei można chcieć, aby indeksator unieważnił pamięć podręczną i wzbogacał dokumenty, nawet jeśli nie wprowadzono żadnych zmian w potoku indeksowania. Na przykład możesz chcieć unieważnić indeksator, jeśli chcesz ponownie wdrożyć niestandardową umiejętność z nowym modelem i chciałem przeprowadzić ponowne uruchomienie na wszystkich dokumentach.

### <a name="override-reset-requirement"></a>Zastąp wymaganie resetowania

Podczas wprowadzania zmian w potoku indeksowania wszystkie zmiany powodujące unieważnienie pamięci podręcznej wymagają zresetowania indeksatora. Jeśli wprowadzasz zmiany do potoku indeksatora i nie chcesz, aby śledzenie zmian zostało unieważnione w pamięci podręcznej, musisz ustawić parametr `ignoreResetRequirement` QueryString, aby `true` dla operacji na indeksatorze lub źródle danych.

### <a name="override-change-detection"></a>Przesłoń wykrywanie zmian

Podczas wprowadzania aktualizacji do zestawu umiejętności, które mogłyby spowodować, że dokumenty są oflagowane jako niespójne, na przykład po ponownym wdrożeniu adresu URL niestandardowej umiejętności, należy ustawić parametr `disableCacheReprocessingChangeDetection` ciągu zapytania, aby `true` aktualizacje zestawu umiejętności.

### <a name="force-change-detection"></a>Wymuś wykrywanie zmian

Wystąpienie w przypadku, gdy chcesz, aby potok indeksowania rozpoznał zmianę w jednostce zewnętrznej, np. wdrożyć nową wersję niestandardowej umiejętności, należy zaktualizować zestawu umiejętności i "Touch" odpowiednią umiejętność, edytując definicję umiejętności, w konkretnym adresie URL do wymuszenia Wykryj zmiany i Unieważnij pamięć podręczną dla tej umiejętności.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano indeksowanie przyrostowe dla indeksatorów, które zawierają umiejętności. Aby jeszcze bardziej zaokrąglić swoją wiedzę, Przejrzyj artykuły dotyczące ponownego indeksowania, które mają zastosowanie do wszystkich scenariuszy indeksowania na platformie Azure Wyszukiwanie poznawcze.

+ [Jak skompilować indeks wyszukiwanie poznawcze platformy Azure](search-howto-reindex.md). 
+ [Jak indeksować duże zestawy danych w usłudze Azure wyszukiwanie poznawcze](search-howto-large-index.md). 

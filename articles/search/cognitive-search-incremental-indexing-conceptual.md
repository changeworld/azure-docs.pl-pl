---
title: Indeksowanie przyrostowe (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Skonfiguruj potok wzbogacenia dysku AI, aby zwiększyć spójność danych w celu obsługi wszelkich aktualizacji umiejętności, umiejętności, indeksatorów lub źródeł danych. Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 76ab8784f04f3c67e4ea8062505931783048dea1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113587"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Co to jest indeksowanie przyrostowe w usłudze Azure Wyszukiwanie poznawcze?

> [!IMPORTANT] 
> Indeksowanie przyrostowe jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi portalu lub zestawu SDK platformy .NET.

Indeksowanie przyrostowe to nowa funkcja platformy Azure Wyszukiwanie poznawcze, która dodaje pamięć podręczną i stan do wzbogaconej zawartości w zestawu umiejętności poznawczym, co zapewnia kontrolę nad przetwarzaniem i ponownym przetwarzaniem poszczególnych kroków w potoku wzbogacania. Nie tylko gwarantuje to, że inwestycje pieniężne są przetwarzane, ale również zapewnia bardziej wydajny system. Gdy struktury i zawartość są buforowane, indeksator może ustalić, które umiejętności uległy zmianie, i uruchamiać tylko te, które zostały zmodyfikowane, a także wszelkie podrzędne umiejętności zależne. 

W przypadku indeksowania przyrostowego bieżąca wersja potoku wzbogacania zapewnia najmniejszą ilość pracy, która gwarantuje spójność wszystkich dokumentów w indeksie. W scenariuszach, w których chcesz mieć pełną kontrolę, możesz użyć precyzyjnych kontrolek, aby przesłonić oczekiwane zachowania. Aby uzyskać więcej informacji na temat konfiguracji, zobacz [Konfigurowanie przyrostowego indeksowania](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Pamięć podręczna indeksatora

Indeksowanie przyrostowe dodaje pamięć podręczną indeksatora do potoku wzbogacania. Indeksator buforuje wyniki z krakingu dokumentu oraz dane wyjściowe każdej umiejętności dla każdego dokumentu. Gdy zestawu umiejętności zostanie zaktualizowana, nastąpi ponowne uruchomienie tylko zmienionych lub podrzędnych. Zaktualizowane wyniki są zapisywane w pamięci podręcznej, a dokument zostanie zaktualizowany w indeksie i w sklepie wiedzy.

Fizycznie pamięć podręczna to konto magazynu. Wszystkie indeksy w ramach usługi wyszukiwania mogą współużytkować to samo konto magazynu dla pamięci podręcznej indeksatora. Każdy indeksator ma przypisany unikatowy i niezmienny identyfikator pamięci podręcznej.

### <a name="cache-configuration"></a>Konfiguracja pamięci podręcznej

Aby rozpocząć korzystając z indeksowania przyrostowego, należy ustawić właściwość `cache` indeksatora. Poniższy przykład ilustruje indeksator z włączonym buforowaniem. Określone części tej konfiguracji są opisane w poniższych sekcjach.

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
    "parameters":{}
}
```

Ustawienie tej właściwości po raz pierwszy na istniejącym indeksatorze będzie wymagało jego zresetowania, co spowoduje ponowne przetworzenie wszystkich dokumentów w źródle danych. Celem indeksowania przyrostowego jest udostępnienie dokumentów w indeksie spójnych ze źródłem danych i bieżącą wersją zestawu umiejętności. Resetowanie indeksu to pierwszy krok w kierunku tej spójności, ponieważ eliminuje ona wszystkie dokumenty wzbogacone przez poprzednie wersje zestawu umiejętności. Indeksator należy zresetować, aby rozpocząć od spójnej linii bazowej.

### <a name="cache-lifecycle"></a>Cykl życia pamięci podręcznej

Cykl życia pamięci podręcznej jest zarządzany przez indeksator. Jeśli właściwość `cache` indeksatora ma wartość null lub zmieniono parametry połączenia, istniejąca pamięć podręczna zostanie usunięta. Cykl życia pamięci podręcznej jest również powiązany z cyklem życia indeksatora. Jeśli indeksator zostanie usunięty, skojarzona pamięć podręczna również zostanie usunięta.

### <a name="indexer-cache-mode"></a>Tryb pamięci podręcznej indeksatora

Pamięć podręczna indeksatora może działać w trybie, w którym dane są zapisywane tylko w pamięci podręcznej lub dane są zapisywane w pamięci podręcznej i używane do ponownego wzbogacania dokumentów.  Można tymczasowo wstrzymać wzbogacanie przyrostowe przez ustawienie właściwości `enableReprocessing` w pamięci podręcznej na `false`, a później wznowienie przyrostowego wzbogacania i przywrócenie ostatecznej spójności przez ustawienie jej na `true`. Ten formant jest szczególnie przydatny, gdy chcesz określić priorytety indeksowania nowych dokumentów, zapewniając spójność w korpus dokumentów.

## <a name="change-detection-override"></a>Zmiana — przesłonięcie wykrywania

Indeksowanie przyrostowe zapewnia szczegółową kontrolę nad wszystkimi aspektami potoku wzbogacania. Ta kontrolka pozwala na zaradzenie sobie z sytuacją, w której zmiana może mieć niezamierzone konsekwencje. Na przykład edytowanie zestawu umiejętności i aktualizowanie adresu URL dla niestandardowej umiejętności spowoduje, że indeksator unieważnia wyniki z pamięci podręcznej dla tej umiejętności. Jeśli przenosisz punkt końcowy na inną maszynę wirtualną lub ponownie wdrażasz swoją umiejętność przy użyciu nowego klucza dostępu, naprawdę nie chcesz przetworzyć żadnych istniejących dokumentów.

Aby upewnić się, że indeksator wykonuje tylko wzbogacania, które są jawnie wymagane, aktualizacje zestawu umiejętności mogą opcjonalnie ustawić parametr `disableCacheReprocessingChangeDetection` QueryString, aby `true`. Po ustawieniu ten parametr zapewni zatwierdzenie tylko aktualizacji zestawu umiejętności, a zmiana nie zostanie oceniona pod kątem efektów istniejących korpus.

Poniższy przykład ilustruje użycie QueryString. Jest to część żądania z parami wartości klucza rozdzielonych &. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Unieważnianie pamięci podręcznej

W tym scenariuszu jest to, gdzie można wdrożyć nową wersję niestandardowej umiejętności, nic w ramach potoku wzbogacenia, ale potrzebna jest niesprawdzona ważność i wszystkie dokumenty, których dotyczy zmiana, zostaną przetworzone w celu odzwierciedlenia korzyści ze zaktualizowanego modelu. W takich przypadkach można wywołać operację Unieważnij umiejętność na zestawu umiejętności. Interfejs API resetowania umiejętności akceptuje żądanie POST z listą danych wyjściowych w pamięci podręcznej, które powinny być unieważnione. Aby uzyskać więcej informacji dotyczących interfejsu API resetowania umiejętności, zobacz [Reset indeksatora (Search API REST)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>Dwukierunkowa wykrywanie zmian

Indeksatory nie tylko przenoś i przetwarzają nowe dokumenty, ale teraz mogą przenosić do tyłu i przetwarzać wcześniej przetworzone dokumenty w celu zapewnienia spójności. Dzięki tej nowej funkcji ważne jest, aby zrozumieć, jak zmiany w składnikach potoku wzbogacania powodują działanie indeksatora. Indeksator zostanie umieszczony w kolejce do wykonania, gdy zidentyfikuje zmianę, która unieważnia lub nie jest spójna względem zawartości w pamięci podręcznej.

### <a name="invalidating-changes"></a>Unieważnianie zmian

Unieważnienie zmian jest rzadkie, ale ma znaczny wpływ na stan potoku wzbogacania. Zmiana na unieważnienie jest taka, gdzie cała pamięć podręczna nie jest już prawidłowa. Przykładem zmiany unieważnienia jest to, gdzie źródło danych jest aktualizowane. W przypadku scenariuszy, gdy wiadomo, że zmiana nie powinna unieważnić pamięci podręcznej, na przykład obracając klucz na koncie magazynu, parametr `ignoreResetRequirement` QueryString powinien być ustawiony na `true` operacji aktualizacji określonego zasobu, aby upewnić się, że operacja jest nie odrzucono.

Oto pełna lista zmian, które spowodują unieważnienie pamięci podręcznej:

* Zmień typ źródła danych
* Zmień na kontener źródła danych
* Poświadczenia źródła danych
* Zasady wykrywania zmian źródła danych
* Zasady wykrywania usuwania źródeł danych
* Mapowania pól indeksatora
* Parametry indeksatora
    * Tryb analizowania
    * Wykluczone rozszerzenia nazw plików
    * Indeksowane rozszerzenia nazw plików
    * Indeksuj metadane magazynu tylko dla powiększonych dokumentów
    * Rozdzielane nagłówki tekstu
    * Rozdzielany ogranicznik tekstu
    * Katalog główny dokumentu
    * Akcja obrazu (zmiany w sposobie wyodrębniania obrazów)

### <a name="inconsistent-changes"></a>Niespójne zmiany

Przykładem niespójnej zmiany jest aktualizacja zestawu umiejętności, która modyfikuje umiejętność. Modyfikacja może spowodować niespójność części pamięci podręcznej. Indeksator zidentyfikuje swoją służbę, aby ponownie uczynić elementy.  

Pełna lista zmian spowodowanych niespójnością pamięci podręcznej:

* Umiejętność w zestawu umiejętności ma inny typ. Typ OData kwalifikacji został zaktualizowany
* Zaktualizowane parametry dotyczące umiejętności, na przykład adres URL, wartości domyślne lub inne parametry
* Zmiany w danych wyjściowych kwalifikacji, umiejętność zwraca dodatkowe lub różne dane wyjściowe
* Aktualizacje umiejętności, które różnią się od pochodzenie, zostały zmienione w łańcuchu umiejętności, tj. Dane wejściowe kwalifikacji
* Wszelkie unieważnienia kwalifikacji dla wszystkich nadrzędnych, jeśli umiejętność dostarczająca dane wejściowe do tej umiejętności zostanie zaktualizowana
* Aktualizacje lokalizacji projekcji magazynu wiedzy, wyniki w dokumencie
* Zmiany w projekcjach w sklepie wiedzy, wyniki dotyczące przeprojektowania dokumentów
* Mapowania pól wyjściowych zostały zmienione w indeksatorze w wyniku przeprojektowania dokumentów do indeksu

## <a name="rest-api-reference-for-incremental-indexing"></a>Dokumentacja interfejsu API REST dla indeksowania przyrostowego

`api-version=2019-05-06-Preview` REST udostępnia interfejsy API do indeksowania przyrostowego, z dodatkiem do indeksatorów, umiejętności i źródeł danych. Dokumentacja referencyjna nie obejmuje obecnie tych dodatków. W poniższej sekcji opisano zmiany w interfejsie API.

### <a name="indexers"></a>Indexers (Indeksatory)

[Tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer) i [indeksatora aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-indexer) spowoduje teraz uwidocznienie nowych właściwości dotyczących pamięci podręcznej:

* `StorageAccountConnectionString`: parametry połączenia z kontem magazynu, które będą używane do buforowania wyników pośrednich.

* `CacheId`: `cacheId` jest identyfikatorem kontenera w ramach konta usługi `annotationCache` Storage, który będzie używany jako pamięć podręczna dla tego indeksatora. Ta pamięć podręczna będzie unikatowa dla tego indeksatora i jeśli indeksator zostanie usunięty i ponownie utworzony przy użyciu tej samej nazwy, `cacheId` zostanie wygenerowane ponownie. Nie można ustawić `cacheId`, jest on zawsze generowany przez usługę.

* `EnableReprocessing`: domyślnie ustawiona na `true`, gdy ustawiona na `false`, dokumenty będą nadal zapisywane w pamięci podręcznej, ale żaden z istniejących dokumentów nie zostanie przetworzony w oparciu o dane pamięci podręcznej.

Niektóre indeksatory (za pośrednictwem [źródeł danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source)) pobierają dane za pośrednictwem zapytań. W przypadku zapytań, które pobierają dane, indeksatory obsługują również nowy parametr ciągu zapytania: `ignoreResetRequirement` powinna być ustawiona na `true`, gdy akcja aktualizacji nie powinna unieważniać pamięci podręcznej.

### <a name="skillsets"></a>Zestawy umiejętności

Umiejętności nie będzie obsługiwać żadnych nowych operacji, ale będzie obsługiwały nowy parametr QueryString: `disableCacheReprocessingChangeDetection` powinna być ustawiona na `true`, gdy nie ma żadnych aktualizacji istniejących dokumentów na podstawie bieżącej akcji.

### <a name="datasources"></a>Źródła danych

Źródła danych nie będą obsługiwać żadnych nowych operacji, ale będą obsługiwać nowe parametry QueryString: `ignoreResetRequirement` powinna być ustawiona na `true`, gdy akcja aktualizacji nie powinna unieważniać pamięci podręcznej.

## <a name="best-practices"></a>Najlepsze praktyki

Zalecanym podejściem do korzystania z indeksowania przyrostowego jest skonfigurowanie indeksowania przyrostowego przez ustawienie właściwości pamięci podręcznej w nowym indeksatorze lub zresetowanie istniejącego indeksatora i ustawienie właściwości pamięci podręcznej.

Używaj `ignoreResetRequirement` oszczędnie, ponieważ może to doprowadzić do niezamierzonej niespójności danych, która nie zostanie łatwo wykryta.

## <a name="takeaways"></a>Wnioski

Indeksowanie przyrostowe to zaawansowana funkcja, która rozszerza śledzenie zmian ze źródła danych na wszystkie aspekty potoku wzbogacania, w tym źródło danych, bieżącą wersję zestawu umiejętności i indeksator. W miarę rozwoju umiejętności, umiejętności i wzbogacania potok wzbogacenia gwarantuje najmniejszą możliwą ilość pracy, jednocześnie zapewniając spójność dokumentów.

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z indeksowania przyrostowego, dodając pamięć podręczną do istniejącego indeksatora lub Dodaj pamięć podręczną podczas definiowania nowego indeksatora.

> [!div class="nextstepaction"]
> [Konfigurowanie indeksowania przyrostowego](search-howto-incremental-index.md)

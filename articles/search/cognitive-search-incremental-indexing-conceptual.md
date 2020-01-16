---
title: Wzbogacanie przyrostowe (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Buforuj zawartość pośrednią oraz przyrostowe zmiany z potoku wzbogacania AI w usłudze Azure Storage, aby zachować inwestycje w istniejące przetworzone dokumenty. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 285b3608bc57d88ca2e81ed14355923436ed9d8d
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028513"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Wprowadzenie do przyrostowego wzbogacania i buforowania na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT] 
> Wzbogacanie przyrostowe jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi portalu lub zestawu SDK platformy .NET.

Przyrostowe wzbogacanie dodaje buforowanie i stanowe do potoku wzbogacenia, zachowując inwestycje w istniejące dane wyjściowe, zmieniając jednocześnie tylko te dokumenty, na które wpływa dana zmiana. Nie tylko gwarantuje to, że inwestycje pieniężne są przetwarzane (w szczególności w przypadku przetwarzania OCR i obrazów), ale również w celu zapewnienia bardziej wydajnego systemu. Gdy struktury i zawartość są buforowane, indeksator może ustalić, które umiejętności uległy zmianie, i uruchamiać tylko te, które zostały zmodyfikowane, a także wszelkie podrzędne umiejętności zależne. 

## <a name="indexer-cache"></a>Pamięć podręczna indeksatora

Wzbogacanie przyrostowe dodaje pamięć podręczną do potoku wzbogacania. Indeksator buforuje wyniki z krakingu dokumentu oraz dane wyjściowe każdej umiejętności dla każdego dokumentu. Gdy zestawu umiejętności zostanie zaktualizowana, nastąpi ponowne uruchomienie tylko zmienionych lub podrzędnych. Zaktualizowane wyniki są zapisywane w pamięci podręcznej, a dokument zostanie zaktualizowany w indeksie wyszukiwania lub w sklepie merytorycznym.

Fizycznie pamięć podręczna jest przechowywana w kontenerze obiektów BLOB na koncie usługi Azure Storage. Wszystkie indeksy w ramach usługi wyszukiwania mogą współużytkować to samo konto magazynu dla pamięci podręcznej indeksatora. Każdy indeksator ma przypisany unikatowy i niezmienny identyfikator pamięci podręcznej do kontenera, którego używa.

## <a name="cache-configuration"></a>Konfiguracja pamięci podręcznej

Musisz ustawić właściwość `cache` indeksatora, aby rozpocząć korzystając z wzbogacania przyrostowego. Poniższy przykład ilustruje indeksator z włączonym buforowaniem. Określone części tej konfiguracji są opisane w poniższych sekcjach. Aby uzyskać więcej informacji, zobacz [Konfigurowanie stopniowego wzbogacania](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Ustawienie tej właściwości na istniejącym indeksatorze wymaga zresetowania i ponownego uruchomienia indeksatora, co spowoduje ponowne przetworzenie wszystkich dokumentów w źródle danych. Ten krok jest niezbędny do wyeliminowania dokumentów wzbogaconych przez poprzednie wersje zestawu umiejętności. 

## <a name="cache-management"></a>Zarządzanie pamięcią podręczną

Cykl życia pamięci podręcznej jest zarządzany przez indeksator. Jeśli właściwość `cache` indeksatora ma wartość null lub parametry połączenia są zmieniane, istniejąca pamięć podręczna zostanie usunięta z następnego uruchomienia indeksatora. Cykl życia pamięci podręcznej jest również powiązany z cyklem życia indeksatora. Jeśli indeksator zostanie usunięty, skojarzona pamięć podręczna również zostanie usunięta.

Chociaż przyrostowe wzbogacanie jest przeznaczone do wykrywania i reagowania na zmiany bez interwencji w Twojej części, istnieją parametry, których można użyć do przesłonięcia zachowań domyślnych:

+ Ustalanie priorytetów nowych dokumentów
+ Pomiń testy zestawu umiejętności
+ Pomiń sprawdzanie źródła danych
+ Wymuś Obliczanie zestawu umiejętności

### <a name="prioritize-new-documents"></a>Ustalanie priorytetów nowych dokumentów

Ustaw właściwość `enableReprocessing` na kontrolowanie przetwarzania dokumentów przychodzących, które są już reprezentowane w pamięci podręcznej. Gdy `true` (domyślnie), dokumenty znajdujące się już w pamięci podręcznej są ponownie przetwarzane po ponownym uruchomieniu indeksatora, zakładając, że aktualizacja umiejętności ma wpływ na ten dokument. 

Gdy `false`, istniejące dokumenty nie są ponownie przetwarzane, skutecznie priorytetyzacja nowej, przychodzącej zawartości przez istniejącą zawartość. Należy ustawić tylko `enableReprocessing` na tymczasową `false`. Aby zapewnić spójność w korpus, `enableReprocessing` powinna być `true` większość czasu, dzięki czemu wszystkie dokumenty, zarówno nowe, jak i istniejące, są prawidłowe dla bieżącej definicji zestawu umiejętności.

### <a name="bypass-skillset-evaluation"></a>Pomiń Obliczanie zestawu umiejętności

Modyfikowanie zestawu umiejętności i przetwarzanie tego zestawu umiejętności zwykle jest dostępne. Jednak niektóre zmiany w zestawu umiejętności nie powinny powodować ponownego przetwarzania (na przykład w celu wdrożenia niestandardowej umiejętności w nowej lokalizacji lub z nowym kluczem dostępu). Najprawdopodobniej są to modyfikacje peryferyjne, które nie mają oryginalnego wpływu na istotę zestawu umiejętności. 

Jeśli wiesz, że zmiana zestawu umiejętności jest rzeczywiście równa, należy zastąpić ocenę zestawu umiejętności, ustawiając parametr `disableCacheReprocessingChangeDetection` na `true`:

1. Zadzwoń do zestawu umiejętności Update i zmodyfikuj definicję zestawu umiejętności.
1. Dołącz parametr `disableCacheReprocessingChangeDetection=true` w żądaniu.
1. Prześlij zmianę.

Ustawienie tego parametru gwarantuje, że tylko aktualizacje definicji zestawu umiejętności są zatwierdzane, a zmiana nie zostanie oceniona pod kątem efektów istniejących korpus.

Poniższy przykład przedstawia żądanie aktualizacji zestawu umiejętności z parametrem:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Pomiń sprawdzanie poprawności źródła danych

Większość zmian w definicji źródła danych spowoduje unieważnienie pamięci podręcznej. Jednak w przypadku scenariuszy, w których wiadomo, że zmiana nie powinna unieważnić pamięci podręcznej, takich jak zmiana parametrów połączenia lub obracanie klucza na koncie magazynu, należy dołączyć parametr`ignoreResetRequirement` w aktualizacji źródła danych. Ustawienie tego parametru na `true` pozwala na przechodzenie przez proces zatwierdzania, bez wyzwalania warunku resetowania, który spowodowałaby odbudowanie wszystkich obiektów i wypełnienie ich od podstaw.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Wymuś Obliczanie zestawu umiejętności

Celem pamięci podręcznej jest uniknięcie niepotrzebnego przetwarzania, ale Załóżmy, że wprowadzasz zmiany w umiejętności, którą indeksator nie wykrywa (na przykład w przypadku zmiany elementu w kodzie zewnętrznym, np. z niestandardową umiejętnością).

W takim przypadku można użyć możliwości [resetowania](preview-api-resetskills.md) w celu wymuszenia przetworzenia konkretnej umiejętności, w tym wszelkich umiejętności podrzędnych, które są zależne od danych wyjściowych tej umiejętności. Ten interfejs API akceptuje żądanie POST z listą umiejętności, które powinny być unieważnione i oznaczone do ponownego przetworzenia. Po zresetowaniu umiejętności Uruchom indeksator w celu wywołania potoku.

## <a name="change-detection"></a>Wykrywanie zmian

Po włączeniu pamięci podręcznej indeksator oblicza zmiany w kompozycji potoku, aby określić, która zawartość może zostać ponownie użyta i które wymaga ponownego przetworzenia. Ta sekcja wylicza zmiany, które unieważnią pamięć podręczną, a następnie zmiany wyzwalające przetwarzanie przyrostowe. 

### <a name="changes-that-invalidate-the-cache"></a>Zmiany, które unieważnią pamięć podręczną

Zmiana na unieważnienie jest taka, gdzie cała pamięć podręczna nie jest już prawidłowa. Przykładem zmiany unieważnienia jest to, gdzie źródło danych jest aktualizowane. Oto pełna lista zmian, które spowodują unieważnienie pamięci podręcznej:

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

### <a name="changes-that-trigger-incremental-processing"></a>Zmiany wyzwalające przetwarzanie przyrostowe

Przetwarzanie przyrostowe szacuje definicję zestawu umiejętności i decyduje o tym, które umiejętności należy ponownie uruchomić, co umożliwi wybiórcze aktualizowanie odpowiednich części drzewa dokumentów. Oto kompletna lista zmian wynikających z wzbogacania przyrostowego:

* Umiejętność w zestawu umiejętności ma inny typ. Typ OData kwalifikacji został zaktualizowany
* Zaktualizowane parametry dotyczące umiejętności, na przykład adres URL, wartości domyślne lub inne parametry
* Zmiany w danych wyjściowych kwalifikacji, umiejętność zwraca dodatkowe lub różne dane wyjściowe
* Aktualizacje umiejętności, które różnią się od pochodzenie, zostały zmienione w łańcuchu umiejętności, tj. Dane wejściowe kwalifikacji
* Wszelkie unieważnienia kwalifikacji dla wszystkich nadrzędnych, jeśli umiejętność dostarczająca dane wejściowe do tej umiejętności zostanie zaktualizowana
* Aktualizacje lokalizacji projekcji magazynu wiedzy, wyniki w dokumencie
* Zmiany w projekcjach w sklepie wiedzy, wyniki dotyczące przeprojektowania dokumentów
* Mapowania pól wyjściowych zostały zmienione w indeksatorze w wyniku przeprojektowania dokumentów do indeksu

## <a name="api-reference-content-for-incremental-enrichment"></a>Zawartość referencyjna interfejsu API na potrzeby wzbogacania przyrostowego

`api-version=2019-05-06-Preview` REST udostępnia interfejsy API do wzbogacania przyrostowego, z dodatkiem do indeksatorów, umiejętności i źródeł danych. [Oficjalne dokumenty referencyjne](https://docs.microsoft.com/rest/api/searchservice/) są przeznaczone dla ogólnie dostępnych interfejsów API i nie obejmują funkcji w wersji zapoznawczej. Poniższa sekcja zawiera zawartość referencyjną dla interfejsów API, których dotyczy problem.

Informacje o użyciu i przykłady można znaleźć w temacie [Konfigurowanie buforowania na potrzeby wzbogacania przyrostowego](search-howto-incremental-index.md).

### <a name="indexers"></a>Indeksatory

[Tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer) i [indeksatora aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-indexer) spowoduje teraz uwidocznienie nowych właściwości dotyczących pamięci podręcznej:

+ `StorageAccountConnectionString`: parametry połączenia z kontem magazynu, które będą używane do buforowania wyników pośrednich.

+ `EnableReprocessing`: domyślnie ustawiona na `true`, gdy ustawiona na `false`, dokumenty będą nadal zapisywane w pamięci podręcznej, ale żaden z istniejących dokumentów nie zostanie przetworzony w oparciu o dane pamięci podręcznej.

+ `ID` (tylko do odczytu): `ID` jest identyfikatorem kontenera w ramach konta usługi `annotationCache` Storage, który będzie używany jako pamięć podręczna dla tego indeksatora. Ta pamięć podręczna będzie unikatowa dla tego indeksatora i jeśli indeksator zostanie usunięty i ponownie utworzony przy użyciu tej samej nazwy, `ID` zostanie wygenerowane ponownie. Nie można ustawić `ID`, jest on zawsze generowany przez usługę.

### <a name="skillsets"></a>Zestawy umiejętności

+ [Aktualizacja zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/update-skillset) obsługuje nowy parametr żądania: `disableCacheReprocessingChangeDetection`, który powinien zostać ustawiony na `true`, gdy chcesz, aby żadne aktualizacje istniejących dokumentów nie były aktualizowane w oparciu o bieżącą akcję.

+ [Resetowanie umiejętności](preview-api-resetskills.md) to nowa operacja służąca do unieważnienia zestawu umiejętności.

### <a name="datasources"></a>Źródła danych

+ Niektóre indeksatory pobierają dane za poorednictwem zapytań. W przypadku zapytań, które pobierają dane, [Aktualizacja źródła danych](https://docs.microsoft.com/rest/api/searchservice/update-data-source) obsługuje nowy parametr na `ignoreResetRequirement`żądania, który powinien zostać ustawiony na `true`, gdy akcja aktualizacji nie powinna unieważnić pamięci podręcznej.

Używaj `ignoreResetRequirement` oszczędnie, ponieważ może to doprowadzić do niezamierzonej niespójności danych, która nie zostanie łatwo wykryta.

## <a name="next-steps"></a>Następne kroki

Przyrostowe wzbogacanie to zaawansowana funkcja, która rozszerza śledzenie zmian na umiejętności i wzbogacanie AI. Jak umiejętności się, wzbogacanie przyrostowe gwarantuje najmniejszą możliwą ilość pracy, jednocześnie zapewniając spójność dokumentów.

Zacznij od dodania pamięci podręcznej do istniejącego indeksatora lub Dodaj pamięć podręczną podczas definiowania nowego indeksatora.

> [!div class="nextstepaction"]
> [Skonfiguruj buforowanie na potrzeby wzbogacania przyrostowego](search-howto-incremental-index.md)

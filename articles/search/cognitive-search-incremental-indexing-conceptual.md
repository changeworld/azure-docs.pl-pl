---
title: Wzbogacenie przyrostowe (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Buforuj zawartość pośrednią i przyrostowe zmiany z potoku wzbogacania sztucznej inteligencji w usłudze Azure Storage w celu zachowania inwestycji w istniejące przetworzone dokumenty. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024147"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Wprowadzenie do przyrostowego wzbogacania i buforowania w usłudze Azure Cognitive Search

> [!IMPORTANT] 
> Przyrostowe wzbogacenie jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Ta funkcja zapewnia [interfejs API REST w wersji 2019-05-06-Preview.](search-api-preview.md) Obecnie nie ma obsługi portalu ani SDK .NET.

Przyrostowe wzbogacenie dodaje buforowania i statefulness do potoku wzbogacania, zachowując inwestycje w istniejącej produkcji, zmieniając tylko te dokumenty wpływ konkretnej modyfikacji. Nie tylko chroni to inwestycje pieniężne w przetwarzanie (w szczególności OCR i przetwarzanie obrazu), ale także zapewnia bardziej efektywny system. Gdy struktury i zawartość są buforowane, indeksator może określić, które umiejętności zostały zmienione i uruchomić tylko te, które zostały zmodyfikowane, a także wszelkie umiejętności zależne niższego rzędu. 

## <a name="indexer-cache"></a>Pamięć podręczna indeksatora

Przyrostowe wzbogacanie dodaje pamięć podręczną do potoku wzbogacania. Indeksator buforuje wyniki z pękanie dokumentu plus dane wyjściowe każdej umiejętności dla każdego dokumentu. Po zaktualizowaniu zestawu umiejętności tylko zmienione lub niższego rzędu umiejętności są ponownie przycinane. Zaktualizowane wyniki są zapisywane w pamięci podręcznej, a dokument jest aktualizowany w indeksie wyszukiwania lub w magazynie wiedzy.

Fizycznie pamięć podręczna jest przechowywana w kontenerze obiektów blob na koncie usługi Azure Storage. Pamięć podręczna używa również magazynu tabel dla wewnętrznego rekordu przetwarzania aktualizacji. Wszystkie indeksy w ramach usługi wyszukiwania mogą współużytkować to samo konto magazynu dla pamięci podręcznej indeksatora. Każdy indeksator jest przypisany unikatowy i niezmienny identyfikator pamięci podręcznej do kontenera, który jest używany.

## <a name="cache-configuration"></a>Konfiguracja pamięci podręcznej

Musisz ustawić `cache` właściwość w indeksatorze, aby rozpocząć korzystanie z przyrostowego wzbogacenia. Poniższy przykład ilustruje indeksator z włączoną buforowania. Określone części tej konfiguracji są opisane w poniższych sekcjach. Aby uzyskać więcej informacji, zobacz [Konfigurowanie wzbogacenia przyrostowego](search-howto-incremental-index.md).

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

Ustawienie tej właściwości na istniejącym indeksatorze będzie wymagać zresetowania i ponownego uruchomienia indeksatora, co spowoduje ponowne przetworzenie wszystkich dokumentów w źródle danych. Ten krok jest konieczne, aby wyeliminować wszelkie dokumenty wzbogacone o poprzednie wersje skillset. 

## <a name="cache-management"></a>Zarządzanie pamięcią podręczną

Cykl życia pamięci podręcznej jest zarządzany przez indeksatora. Jeśli `cache` właściwość w indeksatorze jest ustawiona na wartość null lub ciąg połączenia zostanie zmieniony, istniejąca pamięć podręczna zostanie usunięta przy następnym uruchomieniu indeksatora. Cykl życia pamięci podręcznej jest również powiązany z cyklem życia indeksatora. Jeśli indeksator zostanie usunięty, skojarzona pamięć podręczna jest również usuwana.

Podczas gdy wzbogacenie przyrostowe jest przeznaczone do wykrywania i reagowania na zmiany bez interwencji z Twojej strony, istnieją parametry, których można użyć do zastąpienia domyślnych zachowań:

+ Ustalanie priorytetów nowym dokumentom
+ Pomijanie kontroli zestawów umiejętności
+ Pomijanie kontroli źródeł danych
+ Wymuszanie oceny umiejętności

### <a name="prioritize-new-documents"></a>Ustalanie priorytetów nowym dokumentom

Ustaw `enableReprocessing` właściwość, aby kontrolować przetwarzanie nad przychodzącymi dokumentami już reprezentowane w pamięci podręcznej. Gdy `true` (domyślnie), dokumenty już w pamięci podręcznej są ponownie przetwarzane po ponownym uruchomieniu indeksatora, przy założeniu, że aktualizacja umiejętności wpływa na ten dokument. 

Gdy `false`istniejące dokumenty nie są ponownie przetwarzane, skutecznie nadając priorytet nowej, przychodzącej zawartości istniejącej zawartości. Należy ustawić `enableReprocessing` tylko `false` tymczasowo. Aby zapewnić spójność w `enableReprocessing` całym `true` korpusie, powinno być przez większość czasu, zapewniając, że wszystkie dokumenty, zarówno nowe, jak i istniejące, są ważne zgodnie z aktualną definicją zestawu umiejętności.

### <a name="bypass-skillset-evaluation"></a>Omiń ocenę skillset

Modyfikowanie zestawu umiejętności i ponowne przetwarzanie tego zestawu umiejętności zazwyczaj idą w parze. Jednak niektóre zmiany w zestaw umiejętności nie powinny powodować ponownego przetwarzania (na przykład wdrażania umiejętności niestandardowych do nowej lokalizacji lub z nowym kluczem dostępu). Najprawdopodobniej są to modyfikacje peryferyjne, które nie mają rzeczywistego wpływu na istotę samego zestawu umiejętności. 

Jeśli wiesz, że zmiana zestawu umiejętności jest rzeczywiście powierzchowna, należy zastąpić `disableCacheReprocessingChangeDetection` ocenę `true`skillset, ustawiając parametr na:

1. Wywołaj polecenie aktualizuj skillset i zmodyfikuj definicję zestawu umiejętności.
1. Dołącz `disableCacheReprocessingChangeDetection=true` parametr na żądanie.
1. Prześlij zmianę.

Ustawienie tego parametru gwarantuje, że tylko aktualizacje definicji zestaw umiejętności są zatwierdzone i zmiana nie jest oceniana pod kątem wpływu na istniejący korpus.

W poniższym przykładzie przedstawiono żądanie update skillset z parametrem:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Pomijanie sprawdzania poprawności źródła danych

Większość zmian w definicji źródła danych spowoduje unieważnienie pamięci podręcznej. Jednak w przypadku scenariuszy, w których wiadomo, że zmiana nie powinna unieważniać pamięci podręcznej — na`ignoreResetRequirement` przykład zmiana ciągu połączenia lub obracanie klucza na koncie magazynu — dołącz parametr w aktualizacji źródła danych. Ustawienie tego `true` parametru umożliwia commit przejść, bez wyzwalania warunek resetowania, które mogłyby spowodować wszystkie obiekty są przebudowywane i wypełniane od podstaw.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Wymuszanie oceny umiejętności

Celem pamięci podręcznej jest uniknięcie niepotrzebnego przetwarzania, ale załóżmy, że wprowadzasz zmiany do umiejętności, której indeksator nie wykrywa (na przykład zmiana czegoś w kodzie zewnętrznym, takiego jak niestandardowa umiejętność).

W takim przypadku można użyć [resetowania umiejętności,](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) aby wymusić ponowne przetworzenie określonej umiejętności, w tym wszelkie umiejętności niższego rzędu, które są zależne od wyników tej umiejętności. Ten interfejs API akceptuje żądanie POST z listą umiejętności, które powinny zostać unieważnione i oznaczone do ponownego przetworzenia. Po zresetowaniu umiejętności uruchom indeksatora, aby wywołać potok.

## <a name="change-detection"></a>Wykrywanie zmian

Po włączeniu pamięci podręcznej indeksator ocenia zmiany w składzie potoku, aby określić, która zawartość może być ponownie życiówka, a która wymaga ponownego przetworzenia. W tej sekcji wylicza się zmiany, które unieważniają pamięć podręczną wprost, a następnie zmiany, które wyzwalają przetwarzanie przyrostowe. 

### <a name="changes-that-invalidate-the-cache"></a>Zmiany unieważniają pamięć podręczną

Zmiana unieważniająca to zmiana, w której cała pamięć podręczna nie jest już prawidłowa. Przykładem unieważnienia zmiany jest taki, w którym źródło danych jest aktualizowane. Oto pełna lista zmian, które unieważniłyby pamięć podręczną:

* Zmiana typu źródła danych
* Zmiana w kontenerze źródła danych
* Poświadczenia dostępu do źródła danych
* Zasady wykrywania zmian źródła danych
* Zasady wykrywania usuwania źródeł danych
* Mapowania pól indeksatora
* Parametry indeksatora
    * Tryb analizowania
    * Wykluczone rozszerzenia nazw plików
    * Rozszerzenia nazw plików indeksowanych
    * Metadane magazynu indeksu tylko dla dokumentów ponadgabarytowych
    * Rozdzielane nagłówki tekstu
    * Ogranicznik tekstu rozdzielanych
    * Katalog główny dokumentu
    * Akcja obrazu (zmiany sposobu wyodrębniania obrazów)

### <a name="changes-that-trigger-incremental-processing"></a>Zmiany wyzwalające przetwarzanie przyrostowe

Przetwarzanie przyrostowe ocenia definicję zestawu umiejętności i określa, które umiejętności należy ponownie uruchomić, selektywnie aktualizując dotknięte fragmenty drzewa dokumentów. Oto pełna lista zmian skutkujących stopniowym wzbogaceniem:

* Umiejętność w zestawie umiejętności ma inny typ. Typ umiejętności odata jest aktualizowany
* Zaktualizowano parametry specyficzne dla umiejętności, na przykład adres URL, wartości domyślne lub inne parametry
* Zmiany wyników umiejętności, umiejętności zwracają dodatkowe lub różne wyniki
* Wynikające z tego aktualizacje umiejętności to różne pochodzenie, łańcuch umiejętności uległ zmianie tj. wkłady umiejętności
* Wszelkie upstream unieważnienie umiejętności, jeśli umiejętność, która zapewnia wkład do tej umiejętności jest aktualizowana
* Aktualizacje lokalizacji projekcji magazynu wiedzy, wyniki w ponownym projektowaniu dokumentów
* Zmiany w projekcjach magazynu wiedzy, wyniki w ponownym projektowaniu dokumentów
* Mapowania pól wyjściowych zmienione w indeksatorze skutkują ponownym projektowaniem dokumentów do indeksu

## <a name="api-reference"></a>Odwołanie API

Wersja `2019-05-06-Preview` interfejsu API REST zapewnia przyrostowe wzbogacenie za pośrednictwem dodatkowych właściwości indeksatorów, skillsets i źródeł danych. Oprócz dokumentacji referencyjnej zobacz [Konfigurowanie buforowania dla przyrostowego wzbogacenia,](search-howto-incremental-index.md) aby uzyskać szczegółowe informacje na temat wywoływania interfejsów API.

+ [Utwórz indeksator (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Indeksator aktualizacji (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Aktualizacja Skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (nowy parametr URI na żądanie)

+ [Resetowanie umiejętności (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Indeksatory bazy danych (Azure SQL, Cosmos DB). Niektóre indeksatory pobierają dane za pośrednictwem zapytań. W przypadku kwerend pobieranych [danych, Aktualizacja źródła danych](https://docs.microsoft.com/rest/api/searchservice/update-data-source) obsługuje nowy parametr na żądanie **ignoreResetRequirement**, który powinien być ustawiony na `true` kiedy akcja aktualizacji nie powinna unieważniać pamięci podręcznej. 

  Użyj **ignoreResetRequirement** oszczędnie, ponieważ może to prowadzić do niezamierzonych niespójności w danych, które nie zostaną wykryte łatwo.

## <a name="next-steps"></a>Następne kroki

Przyrostowe wzbogacenie to potężna funkcja, która rozszerza śledzenie zmian na zestawy umiejętności i wzbogacanie SI. AIncremental wzbogacenie umożliwia ponowne wykorzystanie istniejącej przetworzonej zawartości, jak iterować nad projektowania skillset.

W następnym kroku włącz buforowanie istniejącego indeksatora lub dodaj pamięć podręczną podczas definiowania nowego indeksatora.

> [!div class="nextstepaction"]
> [Konfigurowanie buforowania dla wzbogacenia przyrostowego](search-howto-incremental-index.md)

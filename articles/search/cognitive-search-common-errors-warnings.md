---
title: Typowe błędy i ostrzeżenia
titleSuffix: Azure Cognitive Search
description: Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas wzbogacania danych AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 08d15f20f69c0c42d8b4dd4bac72e7d9f367a957
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72787977"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Typowe błędy i ostrzeżenia dotyczące potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze

Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas wzbogacania danych AI na platformie Azure Wyszukiwanie poznawcze.

## <a name="errors"></a>Błędy
Indeksowanie jest przerywane, gdy licznik błędów przekracza wartość ["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Jeśli chcesz, aby indeksatory ignorował te błędy (i pominąć "dokumenty nieudane"), rozważ zaktualizowanie `maxFailedItems` i `maxFailedItemsPerBatch`, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Każdy uszkodzony dokument wraz z jego kluczem dokumentu (jeśli jest dostępny) będzie wyświetlany jako błąd w stanie wykonywania indeksatora. Możesz użyć [interfejsu API indeksu](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) , aby ręcznie przekazać dokumenty w późniejszym momencie, jeśli ustawiono indeksator, aby tolerował błędy.

Poniższe sekcje mogą pomóc w rozwiązywaniu problemów, co umożliwia kontynuowanie indeksowania.

### <a name="could-not-read-document"></a>Nie można odczytać dokumentu
Indeksator nie mógł odczytać dokumentu ze źródła danych. Przyczyną może być:

| Przyczyna | Przykład | Działanie |
| --- | --- | --- |
| niespójne typy pól w różnych dokumentach | Typ wartości jest niezgodny z typem kolumny. Nie można zapisać `'{47.6,-122.1}'` w kolumnie autorów.  Oczekiwany typ to JArray. | Upewnij się, że typ każdego pola jest taki sam w różnych dokumentach. Na przykład jeśli pierwszy dokument `'startTime'` ma wartość DateTime, a w drugim dokumencie jest ciągiem, ten błąd zostanie trafiony. |
| błędy usługi źródłowej źródła danych | (z Cosmos DB) `{"Errors":["Request rate is large"]}` | Sprawdź wystąpienie magazynu, aby upewnić się, że jest w dobrej kondycji. Może być konieczne dostosowanie skalowania/partycjonowania. |
| problemy przejściowe | Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. (Dostawca: Dostawca TCP, błąd: 0 — istniejące połączenie zostało wymuszone przez hosta zdalnego | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

### <a name="could-not-extract-document-content"></a>Nie można wyodrębnić zawartości dokumentu
Indeksator ze źródłem danych obiektu BLOB nie mógł wyodrębnić zawartości z dokumentu (na przykład pliku PDF). Przyczyną może być:

| Przyczyna | Przykład | Działanie |
| --- | --- | --- |
| rozmiar obiektu BLOB przekracza limit. | Dokument ma `'150441598'` bajtów, który przekracza maksymalny rozmiar `'134217728'` bajty na potrzeby wyodrębniania dokumentów dla bieżącej warstwy usług. | [Błędy indeksowania obiektów BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Obiekt BLOB ma nieobsługiwany typ zawartości | Dokument zawiera nieobsługiwany typ zawartości `'image/png'` | [Błędy indeksowania obiektów BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Obiekt BLOB jest zaszyfrowany | Nie można przetworzyć dokumentu — może on być zaszyfrowany lub chroniony hasłem. | [ustawienia obiektu BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| problemy przejściowe | Wystąpił błąd podczas przetwarzania obiektu BLOB: żądanie zostało przerwane: żądanie zostało anulowane. | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

### <a name="could-not-parse-document"></a>Nie można przeanalizować dokumentu
Indeksator odczytuje dokument ze źródła danych, ale wystąpił problem podczas konwertowania zawartości dokumentu do określonego schematu mapowania pól. Przyczyną może być:

| Przyczyna | Przykład | Działanie |
| --- | --- | --- |
| Brak klucza dokumentu | Brak klucza dokumentu lub jest on pusty | Upewnij się, że wszystkie dokumenty mają prawidłowe klucze dokumentu |
| Klucz dokumentu jest nieprawidłowy | Klucz dokumentu nie może mieć więcej niż 1024 znaków | Zmodyfikuj klucz dokumentu, aby spełniał wymagania dotyczące weryfikacji. |
| Nie można zastosować mapowania pola do pola | Nie można zastosować funkcji mapowania `'functionName'` do pola `'fieldName'`. Tablica nie może mieć wartości null. Nazwa parametru: bajty | Dokładnie sprawdź [mapowania pól](search-indexer-field-mappings.md) zdefiniowane w indeksatorze i porównaj z danymi określonego pola dokumentu, który się nie powiódł. Może być konieczne zmodyfikowanie mapowań pól lub danych dokumentu. |
| Nie można odczytać wartości pola | Nie można odczytać wartości kolumny `'fieldName'` przy indeksie `'fieldIndex'`. Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. (Dostawca: Dostawca TCP, błąd: 0 — istniejące połączenie zostało wymuszone przez hosta zdalnego). | Te błędy są zwykle spowodowane nieoczekiwanymi problemami z łącznością z usługą źródłową źródła danych. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

### <a name="could-not-execute-skill"></a>Nie można wykonać umiejętności
Indeksator nie mógł uruchomić umiejętności w zestawu umiejętności.

| Przyczyna | Przykład | Działanie |
| --- | --- | --- |
| Przejściowe problemy z łącznością | Wystąpił błąd przejściowy. Spróbuj ponownie później. | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |
| Potencjalna usterka produktu | Wystąpił nieoczekiwany błąd. | Oznacza to nieznaną klasę błędu i może oznaczać, że występuje usterka produktu. Zapoznaj się z [biletem pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc. |
| Umiejętność napotkała błąd podczas wykonywania | (Z poziomu umiejętności scalania) Co najmniej jedna wartość przesunięcia była nieprawidłowa i nie można jej przeanalizować. Wstawiono elementy na końcu tekstu | Aby rozwiązać ten problem, użyj informacji w komunikacie o błędzie. Ten rodzaj błędu będzie wymagał działania do rozwiązania. |

### <a name="could-not-execute-skill-because-the-web-api-request-failed"></a>Nie można wykonać umiejętności, ponieważ żądanie internetowego interfejsu API nie powiodło się
Wykonanie kwalifikacji nie powiodło się, ponieważ wywołanie interfejsu API sieci Web nie powiodło się. Zazwyczaj ta klasa awarii występuje, gdy są używane niestandardowe umiejętności, w takim przypadku konieczne będzie debugowanie niestandardowego kodu w celu rozwiązania problemu. Jeśli zamiast tego błąd pochodzi z wbudowanej umiejętności, zapoznaj się z komunikatem o błędzie, aby uzyskać pomoc w rozwiązywaniu problemu.

### <a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Nie można wykonać umiejętności, ponieważ odpowiedź dotycząca umiejętności interfejsu API sieci Web jest nieprawidłowa
Wykonanie kwalifikacji nie powiodło się, ponieważ wywołanie interfejsu API sieci Web zwróciło nieprawidłową odpowiedź. Zazwyczaj ta klasa awarii występuje, gdy są używane niestandardowe umiejętności, w takim przypadku konieczne będzie debugowanie niestandardowego kodu w celu rozwiązania problemu. Jeśli zamiast tego niepowodzenie pochodzi z wbudowanej umiejętności, Utwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc.

### <a name="skill-did-not-execute-within-the-time-limit"></a>Umiejętność nie została wykonana w ramach limitu czasu
Istnieją dwa przypadki, w których może wystąpić ten komunikat o błędzie, z których każdy powinien być traktowany inaczej. Postępuj zgodnie z poniższymi instrukcjami, w zależności od tego, jaką umiejętność zwróciła ten błąd.

#### <a name="built-in-cognitive-service-skills"></a>Wbudowane umiejętności usługi poznawczej
Wiele wbudowanych umiejętności poznawczych, takich jak wykrywanie języka, rozpoznawanie jednostek lub OCR, są obsługiwane przez punkt końcowy interfejsu API usługi poznawczej. Czasami występują przejściowe problemy z tymi punktami końcowymi i upłynął limit czasu żądania. W przypadku problemów przejściowych nie ma żadnych naprawienia, z wyjątkiem oczekiwania i spróbuj ponownie. Jako środek zaradczy należy rozważyć skonfigurowanie indeksatora do [uruchamiania zgodnie z harmonogramem](search-howto-schedule-indexers.md). Zaplanowane indeksowanie jest odbierane w miejscu, w którym zostało pozostawione. Przy założeniu, że przejściowe problemy są rozwiązywane, indeksowanie i przetwarzanie umiejętności poznawcze powinny mieć możliwość kontynuowania przy następnym zaplanowanym uruchomieniu.

#### <a name="custom-skills"></a>Umiejętności niestandardowe
Jeśli wystąpi błąd przekroczenia limitu czasu z utworzoną niestandardową umiejętnością, istnieje kilka rzeczy, które można wypróbować. Najpierw przejrzyj swoją niestandardową umiejętność i upewnij się, że nie jest ona zablokowana w pętli nieskończonej i że zwraca wynik konsekwentnie. Po potwierdzeniu, że jest to przypadek, ustal, jaki jest czas wykonywania danej umiejętności. Jeśli wartość `timeout` nie została jawnie ustawiona w niestandardowej definicji umiejętności, domyślnie `timeout` wynosi 30 sekund. Jeśli 30 sekund nie jest wystarczająco długi, aby można było wykonać swoją umiejętność, możesz określić wyższą wartość `timeout` w niestandardowej definicji umiejętności. Oto przykład niestandardowej definicji umiejętności, w której limit czasu jest ustawiony na 90 sekund:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

Maksymalna wartość, którą można ustawić dla parametru `timeout` wynosi 230 sekund.  Jeśli niestandardowa umiejętność nie będzie działać spójnie w ciągu 230 sekund, możesz rozważyć zmniejszenie `batchSize` niestandardowej umiejętności, dzięki czemu będzie ona mogła przetwarzać mniejsze dokumenty w ramach jednego wykonania.  Jeśli ustawiono już `batchSize` do 1, należy ponownie napisać umiejętność, aby można było wykonać ją w mniej niż 230 sekund lub w inny sposób podzielić ją na wiele umiejętności niestandardowych, tak aby czas wykonywania dla każdej pojedynczej umiejętności niestandardowej wynosił maksymalnie 230 sekund. Zapoznaj się z dokumentacją dotyczącą [niestandardowych umiejętności](cognitive-search-custom-skill-web-api.md) , aby uzyskać więcej informacji.

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>Nie można "`MergeOrUpload`" | dokument "`Delete`" w indeksie wyszukiwania

Dokument został odczytany i przetworzony, ale indeksator nie mógł go dodać do indeksu wyszukiwania. Przyczyną może być:

| Przyczyna | Przykład | Działanie |
| --- | --- | --- |
| Termin w dokumencie jest większy niż [limit 32 KB](search-limits-quotas-capacity.md#api-request-limits) | Pole zawiera termin, który jest zbyt duży | Można uniknąć tego ograniczenia, upewniając się, że pole nie jest skonfigurowane jako możliwe do filtrowania, tworzenia i sortowania.
| Dokument jest większy niż [Maksymalny rozmiar żądania interfejsu API](search-limits-quotas-capacity.md#api-request-limits) | Dokument jest zbyt duży, aby można go było zindeksować | [Jak indeksować duże zestawy danych](search-howto-large-index.md)
| Problem z nawiązaniem połączenia z docelowym indeksem (który utrzymuje się po ponownych próbach), ponieważ usługa jest w innym załadowaniu, na przykład w przypadku wykonywania zapytań lub indeksowania. | Nie można ustanowić połączenia w celu zaktualizowania indeksu. Usługa wyszukiwania jest w dużym obciążeniu. | [Skalowanie w górę usługi wyszukiwania](search-capacity-planning.md)
| Trwa poprawianie usługi wyszukiwania w ramach aktualizacji usługi lub jest ona w trakcie ponownej konfiguracji topologii. | Nie można ustanowić połączenia w celu zaktualizowania indeksu. Usługa wyszukiwania jest obecnie wyłączona/usługa wyszukiwania przechodzi do przejścia. | Skonfiguruj usługę z co najmniej 3 replikami na 99,9% dostępności na potrzeby [dokumentacji umowy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Niepowodzenie w źródłowym zasobów obliczeniowych/sieciowych (rzadkich) | Nie można ustanowić połączenia w celu zaktualizowania indeksu. Wystąpił nieznany błąd. | Skonfiguruj indeksatory do [uruchomienia zgodnie z harmonogramem](search-howto-schedule-indexers.md) , aby przeprowadzić pobieranie z niepowodzenia.

### <a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Nie można indeksować dokumentu, ponieważ dane indeksatora są nieprawidłowe

Dokument został odczytany i przetworzony, ale z powodu niezgodności konfiguracji pól indeksu i charakteru danych wyodrębnionych przez indeksator, nie można go dodać do indeksu wyszukiwania. Przyczyną może być:

| Przyczyna | Przykład
| --- | ---
| Typ danych pól wyodrębnionych przez indeksator jest niezgodny z modelem danych odpowiedniego docelowego pola indeksu. | Pole danych "_Data_" w dokumencie z kluczem "_Data_" ma nieprawidłową wartość "typu EDM. String" ". Oczekiwany typ to "Kolekcja (EDM. String)". |
| Nie można wyodrębnić żadnej jednostki JSON z wartości ciągu. | Nie można przeanalizować wartości "typu" EDM. String "" dla pola "_Data_" jako obiektu JSON. Błąd: "po przeanalizowaniu wartości napotkano nieoczekiwany znak:" ". Ścieżka "_Path_", wiersz 1, pozycja 3162. " |
| Nie można wyodrębnić kolekcji jednostek JSON z wartości ciągu.  | Nie można przeanalizować wartości "typu" EDM. String "" dla pola "_Data_" jako tablicy JSON. Błąd: "po przeanalizowaniu wartości napotkano nieoczekiwany znak:" ". Ścieżka ' [0] ', wiersz 1, pozycja 27. ' |
| W dokumencie źródłowym odnaleziono nieznany typ. | Nieznany typ "_nieznany_" nie może być indeksowany |
| W dokumencie źródłowym użyto niezgodnej notacji punktów geograficznych. | Literały ciągu punktu WKT nie są obsługiwane. Zamiast tego użyj literałów punktu GEOJSON |

We wszystkich tych przypadkach należy zapoznać się z [obsługiwanymi typami danych (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i [mapą typów danych dla indeksatorów w Azure Search](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) , aby upewnić się, że schemat indeksu został prawidłowo skonstruowany i skonfigurowano odpowiednie [mapowania pól indeksatora](search-indexer-field-mappings.md). Komunikat o błędzie będzie zawierać szczegóły, które mogą pomóc w śledzeniu źródła niezgodności.

##  <a name="warnings"></a>Ostrzeżeni
Ostrzeżenia nie zatrzymują indeksowania, ale wskazują warunki, które mogą spowodować nieoczekiwane wyniki. Niezależnie od tego, czy podejmujesz akcję, czy nie zależą od danych i Twojego scenariusza.

### <a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>Nie można wykonać umiejętności, ponieważ dane wejściowe dotyczące kwalifikacji były nieprawidłowe
Indeksator nie mógł uruchomić umiejętności w zestawu umiejętności, ponieważ brakuje w nim danych wejściowych, niewłaściwego typu lub w inny sposób.

Umiejętności poznawcze mają wymagane dane wejściowe i opcjonalne dane wejściowe. Na przykład [umiejętność wyodrębniania frazy klucza](cognitive-search-skill-keyphrases.md) ma dwa wymagane dane wejściowe `text`, `languageCode` i brak opcjonalnych danych wejściowych. Jeśli jakiekolwiek wymagane dane wejściowe są nieprawidłowe, umiejętność zostanie pominięta i wygeneruje ostrzeżenie. Pominięte umiejętności nie generują żadnych danych wyjściowych, więc jeśli inne umiejętności korzystają z wyników pominiętych umiejętności, mogą generować dodatkowe ostrzeżenia.

Jeśli chcesz podać wartość domyślną w przypadku braku danych wejściowych, możesz użyć [umiejętności warunkowej](cognitive-search-skill-conditional.md) do wygenerowania wartości domyślnej, a następnie użyć danych wyjściowych z [umiejętności warunkowej](cognitive-search-skill-conditional.md) jako danych wejściowych umiejętności.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Przyczyna | Przykład | Działanie |
| --- | --- | --- |
| Dane wejściowe umiejętności są niewłaściwego typu | Wymagane `X` danych wejściowych kwalifikacji nie ma oczekiwanego typu `String`. Wymagane `X` danych wejściowych kwalifikacji nie ma oczekiwanego formatu. | Pewne umiejętności oczekują danych wejściowych określonych typów, na przykład [umiejętność tonacji](cognitive-search-skill-sentiment.md) oczekuje, że `text` być ciągiem. Jeśli dane wejściowe określają wartość różną od ciągu, wówczas umiejętność nie zostanie wykonana i nie wygeneruje żadnych danych wyjściowych. Upewnij się, że zestaw danych zawiera wartości wejściowe, które są jednorodne w typie, lub Użyj [niestandardowej umiejętności interfejsu API sieci Web](cognitive-search-custom-skill-web-api.md) , aby wstępnie przetworzyć dane wejściowe. Jeśli Iteracja jest przeprowadzana przez tablicę, sprawdź kontekst umiejętności i wprowadź `*` w poprawnych pozycjach. Zwykle zarówno kontekst, jak i źródło danych wejściowych powinny kończyć się `*` dla tablic. |
| Brak danych wejściowych kwalifikacji | Brak wymaganego `X` wejściowego umiejętności. | Jeśli wszystkie dokumenty otrzymają to ostrzeżenie, prawdopodobnie występuje literówka w ścieżkach wejściowych i należy dokładnie sprawdzić wielkość liter nazwy właściwości, dodatkowe lub brakujące `*` w ścieżce, a dokumenty ze źródła danych definiują wymagane dane wejściowe. |
| Dane wejściowe kodu języka umiejętności są nieprawidłowe | @No__t_0 danych wejściowych umiejętności zawiera następujące kody języka `X,Y,Z`, co najmniej jeden z nich jest nieprawidłowy. | Zobacz więcej szczegółów [poniżej](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Dane wejściowe umiejętności "languageCode" zawierają następujące kody języka: "X, Y, Z", co najmniej jeden z nich jest nieprawidłowy.
Co najmniej jedna wartość przeniesiona do opcjonalnego wejścia `languageCode` z poziomu umiejętności podrzędnej nie jest obsługiwana. Taka sytuacja może wystąpić, jeśli przekazujesz dane wyjściowe [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) do kolejnych umiejętności, a dane wyjściowe składają się z większej liczby języków niż jest to obsługiwane w tych umiejętnościach podrzędnych.

Jeśli wiesz, że zestaw danych znajduje się w jednym języku, należy usunąć [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) i dane wejściowe dotyczące umiejętności `languageCode` i użyć dla nich parametru umiejętności `defaultLanguageCode`, przy założeniu, że język jest obsługiwany dla tej umiejętności.

Jeśli wiesz, że zestaw danych zawiera wiele języków i w ten sposób potrzebujesz danych wejściowych [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) i `languageCode`, rozważ dodanie [ConditionalSkill](cognitive-search-skill-conditional.md) w celu odfiltrowania tekstu w językach, które nie są obsługiwane przed przekazaniem w tekst na kwalifikacje podrzędne.  Oto przykład tego, co może wyglądać jak w przypadku EntityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Poniżej przedstawiono niektóre odwołania do obecnie obsługiwanych języków dla każdej z umiejętności, które mogą generować ten komunikat o błędzie:
* [Obsługiwane języki analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (dla [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)i [SentimentSkill](cognitive-search-skill-sentiment.md))
* [Języki obsługiwane przez translatora](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (dla [tekstu TranslationSkill](cognitive-search-skill-text-translation.md))
* [SplitSkill tekstu](cognitive-search-skill-textsplit.md) Obsługiwane języki: `da, de, en, es, fi, fr, it, ko, pt`

### <a name="skill-input-was-truncated"></a>Dane wejściowe kwalifikacji zostały obcięte
Umiejętności poznawcze mają ograniczone długość tekstu, który można analizować jednocześnie. Jeśli wprowadzanie tekstu tych umiejętności przekracza ten limit, spowoduje to obcinanie tekstu w celu spełnienia limitu, a następnie przeprowadzenie wzbogacania tego tekstu. Oznacza to, że umiejętność jest wykonywana, ale nie na wszystkich Twoich danych.

W poniższym przykładzie LanguageDetectionSkill pole wejściowe `'text'` może wyzwolić to ostrzeżenie, jeśli przekracza limit liczby znaków. Limity danych wejściowych kwalifikacji można znaleźć w dokumentacji dotyczącej [umiejętności](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Jeśli chcesz upewnić się, że cały tekst jest analizowany, rozważ użycie opcji [Podziel umiejętność](cognitive-search-skill-textsplit.md).

### <a name="web-api-skill-response-contains-warnings"></a>Odpowiedź na umiejętność interfejsu API sieci Web zawiera ostrzeżenia
Indeksator mógł uruchomić umiejętność w zestawu umiejętności, ale odpowiedź z żądania internetowego interfejsu API wskazywała, że wystąpiły ostrzeżenia podczas wykonywania. Zapoznaj się z ostrzeżeniami, aby zrozumieć, w jaki sposób wpływają dane oraz czy nie są wymagane akcje.

### <a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>Bieżąca konfiguracja indeksatora nie obsługuje przyrostowego postępu
To ostrzeżenie występuje tylko w przypadku Cosmos DB źródeł danych.

Przyrostowy postęp podczas indeksowania zapewnia, że jeśli wykonywanie indeksatora zostanie przerwane przez przejściowe błędy lub limit czasu wykonywania, indeksator może zostać pobrany w miejscu, w którym zostanie pozostawiony po następnym uruchomieniu, zamiast konieczności ponownego indeksowania całej kolekcji od podstaw. Jest to szczególnie ważne podczas indeksowania dużych kolekcji.

Możliwość wznowienia nieukończonego zadania indeksowania jest predykatem według dokumentów uporządkowanych według kolumny `_ts`. Indeksator używa sygnatury czasowej, aby określić, który dokument powinien zostać pobrany dalej. Jeśli brakuje kolumny `_ts` lub jeśli indeksator nie może określić, czy kwerenda niestandardowa jest uporządkowana, indeksator rozpoczyna się od początku i zobaczysz to ostrzeżenie.

Możliwe jest przesłonięcie tego zachowania, co umożliwia stopniowe postęp i pomijanie tego ostrzeżenia przy użyciu właściwości konfiguracja `assumeOrderByHighWatermarkColumn`.

[Więcej informacji na temat Cosmos DB postępu przyrostowego i zapytań niestandardowych.](https://go.microsoft.com/fwlink/?linkid=2099593)
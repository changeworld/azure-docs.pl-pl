---
title: Typowe błędy i ostrzeżenia — Azure Search
description: Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas wzbogacania AI w Azure Search.
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.subservice: cognitive-search
ms.openlocfilehash: 62dd3440deaf31f3739ad5d8fde1d8b54a20197e
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828264"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Typowe błędy i ostrzeżenia dotyczące potoku wzbogacenia AI w Azure Search

Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas wzbogacania AI w Azure Search.

## <a name="errors"></a>błędy
Indeksowanie jest przerywane, gdy licznik błędów przekracza wartość ["maxfaileditems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). Poniższe sekcje mogą pomóc w rozwiązywaniu problemów, co umożliwia kontynuowanie indeksowania.

### <a name="could-not-read-document"></a>Nie można odczytać dokumentu
Indeksator nie mógł odczytać dokumentu ze źródła danych. Przyczyną może być:

| Przyczyna | Przykład | Akcja |
| --- | --- | --- |
| niespójne typy pól w różnych dokumentach | Typ wartości jest niezgodny z typem kolumny. Nie można zapisać `'{47.6,-122.1}'` w kolumnie autorów.  Oczekiwany typ to JArray. | Upewnij się, że typ każdego pola jest taki sam w różnych dokumentach. Na przykład jeśli pierwszy dokument `'startTime'` ma wartość DateTime, a w drugim dokumencie jest ciągiem, ten błąd zostanie trafiony. |
| błędy usługi źródłowej źródła danych | (od Cosmos DB) `{"Errors":["Request rate is large"]}` | Sprawdź wystąpienie magazynu, aby upewnić się, że jest w dobrej kondycji. Może być konieczne dostosowanie skalowania/partycjonowania. |
| problemy przejściowe | Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. (Dostawca: Dostawca TCP, błąd: 0 — istniejące połączenie zostało wymuszone przez hosta zdalnego | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

### <a name="could-not-extract-document-content"></a>Nie można wyodrębnić zawartości dokumentu
Indeksator ze źródłem danych obiektu BLOB nie mógł wyodrębnić zawartości z dokumentu (na przykład pliku PDF). Przyczyną może być:

| Przyczyna | Przykład | Akcja |
| --- | --- | --- |
| rozmiar obiektu BLOB przekracza limit. | Dokument ma `'150441598'` bajtów, który przekracza maksymalny rozmiar `'134217728'` bajty na potrzeby wyodrębniania dokumentów dla bieżącej warstwy usług. | [Błędy indeksowania obiektów BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Obiekt BLOB ma nieobsługiwany typ zawartości | Dokument zawiera nieobsługiwany typ zawartości `'image/png'` | [Błędy indeksowania obiektów BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Obiekt BLOB jest zaszyfrowany | Nie można przetworzyć dokumentu — może on być zaszyfrowany lub chroniony hasłem. | [ustawienia obiektu BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| problemy przejściowe | Wystąpił błąd podczas przetwarzania obiektu BLOB: żądanie zostało przerwane: żądanie zostało anulowane. | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

### <a name="could-not-parse-document"></a>Nie można przeanalizować dokumentu
Indeksator odczytuje dokument ze źródła danych, ale wystąpił problem podczas konwertowania zawartości dokumentu do określonego schematu mapowania pól. Przyczyną może być:

| Przyczyna | Przykład | Akcja |
| --- | --- | --- |
| Brak klucza dokumentu | Brak klucza dokumentu lub jest on pusty | Upewnij się, że wszystkie dokumenty mają prawidłowe klucze dokumentu |
| Klucz dokumentu jest nieprawidłowy | Klucz dokumentu nie może mieć więcej niż 1024 znaków | Zmodyfikuj klucz dokumentu, aby spełniał wymagania dotyczące weryfikacji. |
| Nie można zastosować mapowania pola do pola | Nie można zastosować funkcji mapowania `'functionName'` do pola `'fieldName'`. Tablica nie może mieć wartości null. Nazwa parametru: bajty | Dokładnie sprawdź [mapowania pól](search-indexer-field-mappings.md) zdefiniowane w indeksatorze i porównaj z danymi określonego pola dokumentu, który się nie powiódł. Może być konieczne zmodyfikowanie mapowań pól lub danych dokumentu. |
| Nie można odczytać wartości pola | Nie można odczytać wartości kolumny `'fieldName'` przy indeksie `'fieldIndex'`. Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. (Dostawca: Dostawca TCP, błąd: 0 — istniejące połączenie zostało wymuszone przez hosta zdalnego). | Te błędy są zwykle spowodowane nieoczekiwanymi problemami z łącznością z usługą źródłową źródła danych. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

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

##  <a name="warnings"></a>Ostrzeżenia
Ostrzeżenia nie zatrzymują indeksowania, ale wskazują warunki, które mogą spowodować nieoczekiwane wyniki. Niezależnie od tego, czy podejmujesz akcję, czy nie zależą od danych i Twojego scenariusza.

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
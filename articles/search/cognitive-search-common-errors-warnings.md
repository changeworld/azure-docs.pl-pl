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
ms.openlocfilehash: 4e31f818e96ae9f13e3ce8892e575318831848f6
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329385"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Typowe błędy i ostrzeżenia dotyczące potoku wzbogacenia AI w Azure Search

Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas wzbogacania AI w Azure Search.

## <a name="errors"></a>Błędy
Indeksowanie jest przerywane, gdy licznik błędów przekracza wartość ["maxfaileditems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). Poniższe sekcje mogą pomóc w rozwiązywaniu problemów, co umożliwia kontynuowanie indeksowania.

### <a name="could-not-read-document"></a>Nie można odczytać dokumentu
Indeksator nie mógł odczytać dokumentu ze źródła danych. Przyczyną może być:

| Reason | Przykład | Action |
| --- | --- | --- |
| niespójne typy pól w różnych dokumentach | Typ wartości jest niezgodny z typem kolumny. Nie można zapisać `'{47.6,-122.1}'` w kolumnie autorów.  Oczekiwany typ to JArray. | Upewnij się, że typ każdego pola jest taki sam w różnych dokumentach. Na przykład jeśli pierwszy dokument `'startTime'` ma wartość DateTime, a w drugim dokumencie jest ciągiem, ten błąd zostanie trafiony. |
| błędy usługi źródłowej źródła danych | (od Cosmos DB) `{"Errors":["Request rate is large"]}` | Sprawdź wystąpienie magazynu, aby upewnić się, że jest w dobrej kondycji. Może być konieczne dostosowanie skalowania/partycjonowania. |
| problemy przejściowe | Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. dostawcy Dostawca TCP, błąd: 0 — istniejące połączenie zostało wymuszone przez hosta zdalnego | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

### <a name="could-not-extract-document-content"></a>Nie można wyodrębnić zawartości dokumentu
Indeksator ze źródłem danych obiektu BLOB nie mógł wyodrębnić zawartości z dokumentu (na przykład pliku PDF). Przyczyną może być:

| Reason | Przykład | Action |
| --- | --- | --- |
| rozmiar obiektu BLOB przekracza limit. | Dokument ma `'150441598'` bajtów, który przekracza maksymalny rozmiar `'134217728'` bajty na potrzeby wyodrębniania dokumentów dla bieżącej warstwy usług. | [Błędy indeksowania obiektów BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Obiekt BLOB ma nieobsługiwany typ zawartości | Dokument zawiera nieobsługiwany typ zawartości `'image/png'` | [Błędy indeksowania obiektów BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Obiekt BLOB jest zaszyfrowany | Nie można przetworzyć dokumentu — może on być zaszyfrowany lub chroniony hasłem. | [ustawienia obiektu BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| problemy przejściowe | Błąd podczas przetwarzania obiektu BLOB: Żądanie zostało przerwane: Żądanie zostało anulowane. | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

### <a name="could-not-parse-document"></a>Nie można przeanalizować dokumentu
Indeksator odczytuje dokument ze źródła danych, ale wystąpił problem podczas konwertowania zawartości dokumentu do określonego schematu mapowania pól. Przyczyną może być:

| Reason | Przykład | Action |
| --- | --- | --- |
| Brak klucza dokumentu | Brak klucza dokumentu lub jest on pusty | Upewnij się, że wszystkie dokumenty mają prawidłowe klucze dokumentu |
| Klucz dokumentu jest nieprawidłowy | Klucz dokumentu nie może mieć więcej niż 1024 znaków | Zmodyfikuj klucz dokumentu, aby spełniał wymagania dotyczące weryfikacji. |
| Nie można zastosować mapowania pola do pola | Nie można zastosować funkcji mapowania `'functionName'` do pola `'fieldName'`. Tablica nie może mieć wartości null. Nazwa parametru: bajty | Dokładnie sprawdź [mapowania pól](search-indexer-field-mappings.md) zdefiniowane w indeksatorze i porównaj z danymi określonego pola dokumentu, który się nie powiódł. Może być konieczne zmodyfikowanie mapowań pól lub danych dokumentu. |
| Nie można odczytać wartości pola | Nie można odczytać wartości kolumny `'fieldName'` przy indeksie `'fieldIndex'`. Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. dostawcy Dostawca TCP, błąd: 0 — istniejące połączenie zostało wymuszone przez hosta zdalnego. | Te błędy są zwykle spowodowane nieoczekiwanymi problemami z łącznością z usługą źródłową źródła danych. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

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
---
title: Metoda języków mowy interfejsu API Microsoft Translator | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: Metoda języków interfejsu API mowy Translator firmy Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/18
ms.author: v-jansko
ms.openlocfilehash: 5396e3be17345c3c36197a9b6cbace86e1f574c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349752"
---
# <a name="speech-api-languages"></a>Mowy interfejsu API: języków

Microsoft Translator rozszerza stale listę języków obsługiwanych przez jej usług. Ten interfejs API umożliwia odnajdywanie zbioru języków aktualnie dostępnych do użycia z usługą tłumaczenia mowy.

Przykłady kodu prezentacja Użyj interfejsu API, aby uzyskać dostępne języki są dostępne z [witryny Microsoft Translator Github](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Uwagi dotyczące implementacji

Pobierz /languages 

Wielu różnych języków jest dostępna wykonać transkrypcji mowy, umożliwia tłumaczenie tekstu transcribed i powodować syntezatora mowy tłumaczenia.

Klient używa `scope` parametr, aby określić, który określa języki on jest zainteresowana zapytania.

* **Mowy na tekst:** , użyj parametru zapytania `scope=speech` można pobrać zestawu wersji językowych dostępnych wykonać transkrypcji mowy na tekst.
* **Tłumaczenie tekstu:** , użyj parametru zapytania `scope=text` można pobrać zestawu wersji językowych dostępnych umożliwia tłumaczenie tekstu przetłumaczone.
* **Tekst na mowę:** , użyj parametru zapytania `scope=tts` można pobrać zestaw języków i dostępne do syntetyzowania przetłumaczony tekst na mowę głosy.

Klient może pobrać wiele zestawów jednocześnie, określając rozdzielaną przecinkami listę dostępnych opcji. Na przykład `scope=speech,text,tts`.

Odpowiedź oznaczająca Powodzenie jest obiekt JSON z właściwością dla każdego żądany zestaw.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Ponieważ klient może używać `scope` parametru zapytania do wybrania który określa języki, które ma zostać zwrócony, właściwą odpowiedź może zawierać tylko podzbiór właściwości wszystkich przedstawionych powyżej.

Podana wartość każdej właściwości ma następującą składnię.

### <a name="speech-to-text-speech"></a>Mowy na tekst (mowy)

Wartość skojarzona z właściwością mowy na tekst `speech`, jest słownikiem (klucz, wartość) pary. Każdy klucz identyfikuje obsługuje mowy do tekstu. Klucz jest identyfikator ten klient przechodzi do interfejsu API. Wartość skojarzoną z kluczem jest obiektem z następującymi właściwościami:

* `name`: Nazwa wyświetlana języka.
* `language`: Znacznik języka skojarzonego zapisywane języka. "Tekst transakcja" są wymienione poniżej.
Przykładem jest:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Tłumaczenie tekstu (tekst)

Wartość skojarzoną z `text` właściwości jest również słownika, gdzie każdy klucz identyfikuje z językiem obsługiwanym tłumaczenie tekstu. Wartość skojarzoną z kluczem opisuje język:

* `name`: Nazwa wyświetlana języka.
* `dir`: Kierunkowość, czyli `rtl` dla języków od prawej do lewej lub `ltr` dla języków od lewej do prawej.

Przykładem jest:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Tekst na mowę (tts)

Wartość skojarzona z właściwością tekst na mowę, tts, jest również słownika, gdzie każdy klucz identyfikuje obsługiwane głosu. Atrybuty obiektu głosu są:

* `displayName`: Nazwa wyświetlana głosu.
* `gender`: Płeć głosu (męskiego lub żeńskiego).
* `locale`: Znacznik języka głosu tag podrzędny podstawowy język i region tag podrzędny.
* `language`: Znacznik języka skojarzonego zapisywane języka.
* `languageName`: Nazwa wyświetlana języka.
* `regionName`: Nazwa wyświetlana regionu dla tego języka.

Przykładem jest:

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Lokalizacja
Usługa zwraca wszystkie nazwy w języku nagłówek "Accept-Language", dla wszystkich języków obsługiwanych przez tłumaczenie tekstu.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Obiekt opisujący zestaw obsługiwanych języków.

Wartość ModelExample: 

Langagues {mowy (obiekt, opcjonalny), tekst (obiekt, opcjonalny), tts (obiekt, opcjonalny)}

### <a name="headers"></a>Nagłówki

|Nagłówek|Opis|Typ|
:--|:--|:--|
Identyfikator żądania X|Wartość wygenerowany przez serwer, aby zidentyfikować żądania i używane na potrzeby rozwiązywania problemów.|ciąg|

### <a name="parameters"></a>Parametry

|Parametr|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|
|wersja interfejsu API    |Wersja interfejsu API żądanego przez klienta. Dozwolone wartości to: `1.0`.|query|ciąg|
|scope  |Zestawy obsługiwanych języków lub głosy do zwrócenia do klienta. Ten parametr jest określony jako listę rozdzielaną przecinkami słów kluczowych. Dostępne są następujące słowa kluczowe:<ul><li>`speech`: Zawiera zestaw języków obsługiwanych wykonać transkrypcji mowy.</li><li>`tts`: Zawiera zbiór głosy są obsługiwane na potrzeby konwersji tekstu na mowę.</li><li>`text`: Zawiera zbiór języki obsługiwane w przypadku tłumaczenie tekstu.</li></ul>Jeśli wartość nie jest określony, wartość `scope` domyślnie `text`.|query|ciąg|
|X ClientTraceId    |Identyfikator GUID generowany przez klienta używane do śledzenia żądanie. W celu ułatwienia rozwiązywania problemów, klientów należy udostępnić nową wartość każdego żądania i zalogować go.|nagłówek|ciąg|
|Zaakceptuj języka    |Niektóre pola w odpowiedzi są nazwy języków lub regionach. Ten parametr służy do określenia języka, w których nazwy są zwracane. Język jest określić, wprowadzając poprawnie sformułowanym znacznik języka BCP 47. Wybierz z listy identyfikatorów języka zwracane z tag `text` zakresu. Dla języków nieobsługiwanych nazwy znajdują się w języku angielskim.<br/>Przykładowo, użyj wartości `fr` na żądanie nazwy w języku francuskim, lub użyj wartości `zh-Hant` na żądanie nazwy w języku chińskim tradycyjnym.|nagłówek|ciąg|
    
### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe, aby upewnić się, że są one prawidłowe. Obiekt odpowiedzi zawiera bardziej szczegółowy opis błędu.|
|429|Za dużo żądań.|
|500|Wystąpił błąd. Jeśli błąd będzie się powtarzać, raportu o identyfikatorze śledzenia klienta (X-ClientTraceId) lub identyfikator (X-RequestId) żądania.|
|503|Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, raportu o identyfikatorze śledzenia klienta (X-ClientTraceId) lub identyfikator (X-RequestId) żądania.|

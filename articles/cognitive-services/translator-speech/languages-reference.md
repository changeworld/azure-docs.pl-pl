---
title: Metoda języków interfejs API mowy usługi Translator
titleSuffix: Azure Cognitive Services
description: Metoda języków interfejsu API mowy usługi Translator.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: conceptual
ms.date: 05/18/18
ms.author: v-jansko
ROBOTS: NOINDEX
ms.openlocfilehash: 9fbbba7ed5e81cae1d30c5a480b9f7f7a13342c5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986425"
---
# <a name="translator-speech-api-languages"></a>Interfejs API mowy usługi Translator: języków

Mowy usługi Translator nieustannie rozszerza listę języków obsługiwanych przez jego usług. Aby odnaleźć zbiór języków dostępnych do użycia z usługą mowy usługi Translator, należy użyć tego interfejsu API.

Przykłady kodu, demonstrując korzystanie z interfejsu API, aby uzyskać dostępne języki są dostępne z [witryny Github w usłudze Translator firmy Microsoft](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Uwagi dotyczące implementacji

Pobierz /languages 

Szerokiego zestawu języków jest dostępna dla transkrypcja mowy, tłumaczenie uzyskanego tekstu i wygenerować syntezatora mowy tłumaczenia.

Klient używa `scope` parametr do definiowania, który konfiguruje języków jest zainteresowany zapytania.

* **Mowy na tekst:** należy użyć parametru zapytania `scope=speech` można pobrać zestaw dostępnych języków do transkrypcja mowy na tekst.
* **Tłumaczenie tekstu:** należy użyć parametru zapytania `scope=text` można pobrać zestawu języków dostępnych do translacji uzyskanego tekstu.
* **Zamiana tekstu na mowę:** należy użyć parametru zapytania `scope=tts` można pobrać zestawu języków i głosy dostępne do syntetyzowania przetłumaczonego tekstu na mowę.

Klient może pobrać wiele zestawów jednocześnie, określając rozdzielaną przecinkami listę wyboru. Na przykład `scope=speech,text,tts`.

Odpowiedź oznaczająca Powodzenie jest właściwością obiektu JSON dla każdego żądanego zestawu.

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

Ponieważ klient może używać `scope` parametr zapytania, aby wybrać, który konfiguruje obsługiwanych języków, które ma zostać zwrócone, właściwą odpowiedź może zawierać tylko podzbiór wszystkich właściwości przedstawionych powyżej.

Podana wartość każdej właściwości jest w następujący sposób.

### <a name="speech-to-text-speech"></a>Mowy na tekst (mowy)

Wartość skojarzona z właściwością mowy na tekst, `speech`, jest słownikiem (klucz, wartość) pary. Każdy klucz identyfikuje język obsługiwane w przypadku mowy na tekst. Klucz jest identyfikatorem tego Klient przechodzi do interfejsu API. Wartość skojarzoną z kluczem jest obiektem z następującymi właściwościami:

* `name`: Służy do wyświetlania nazwę języka.
* `language`: Tag języka skojarzonego napisane w języku. Zobacz "Transakcja tekst" poniżej.
Przykładem jest:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Tłumaczenie tekstu (tekst)

Wartość skojarzona z `text` właściwość jest również słownika, gdzie każdy klucz identyfikuje język obsługiwane w przypadku tłumaczenie tekstu. Wartość skojarzoną z kluczem opisuje język:

* `name`: Służy do wyświetlania nazwę języka.
* `dir`: Kierunkowość, czyli `rtl` w językach od prawej do lewej lub `ltr` w językach od lewej do prawej.

Przykładem jest:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Zamiana tekstu na mowę (tts)

Wartość skojarzona z właściwością zamiany tekstu na mowę, tts, jest również słownika, gdzie każdy klucz identyfikuje obsługiwane głosu. Dostępne są następujące atrybuty obiektu głosu:

* `displayName`: Nazwa wyświetlana głosu.
* `gender`: Płeć głosowych (męskiego i żeńskiego).
* `locale`: Tag języka głosu przy użyciu atrybutu podstawowego języka i regionu atrybutu.
* `language`: Tag języka skojarzonego napisane w języku.
* `languageName`: Służy do wyświetlania nazwę języka.
* `regionName`: Służy do wyświetlania nazwy obszaru dla tego języka.

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
Usługa zwraca wszystkie nazwy w języku nagłówek "Accept-Language", wszystkie języki obsługiwane w tłumaczeniu tekstu.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Obiekt opisujący zestaw obsługiwanych języków.

Wartość ModelExample: 

Langagues {mowy (object, opcjonalnie), tekst (object, opcjonalnie), tts (object, opcjonalnie)}

### <a name="headers"></a>Nagłówki

|Nagłówek|Opis|Typ|
:--|:--|:--|
Identyfikator żądania X|Wartość wygenerowany przez serwer w celu zidentyfikowania żądania i używana na potrzeby rozwiązywania problemów.|ciąg|

### <a name="parameters"></a>Parametry

|Parametr|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|
|wersja interfejsu API    |Wersja interfejsu API zażądane przez klienta. Dozwolone wartości to: `1.0`.|query|ciąg|
|scope  |Zestawy obsługiwane języki lub głosy do zwrócenia do klienta. Ten parametr jest określony jako rozdzielana przecinkami lista słów kluczowych. Dostępne są następujące słowa kluczowe:<ul><li>`speech`: Zawiera zbiór języków obsługiwanych na transkrypcja mowy.</li><li>`tts`: Zawiera zbiór głosy są obsługiwane dla konwersji tekstu na mowę.</li><li>`text`: Zawiera zbiór języków obsługiwanych w przypadku tłumaczenia tekstu.</li></ul>Jeśli wartość nie jest określony, wartość `scope` wartość domyślna to `text`.|query|ciąg|
|X ClientTraceId    |Identyfikator GUID generowany przez klienta umożliwia śledzenie żądań. W celu ułatwienia rozwiązywania problemów, klientów należy podać nową wartość z każdym żądaniem i ją.|nagłówek|ciąg|
|Zaakceptuj języka    |Niektóre pola w odpowiedzi są nazwami językach i regionach. Użyj tego parametru, aby określić język, w której zwracane są nazwy. Język jest określony, podając tag języka sformułowany BCP 47. Wybierz tag z listy identyfikatorów języka zwrócono `text` zakresu. Dla języków nieobsługiwanych nazw znajdują się w języku angielskim.<br/>Na przykład użyj wartości `fr` do żądania nazw w języku francuskim, lub użyj wartości `zh-Hant` do żądania nazw w chińskim tradycyjnym.|nagłówek|ciąg|
    
### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe, aby upewnić się, że są one prawidłowe. Obiekt odpowiedzi zawiera bardziej szczegółowy opis błędu.|
|429|Zbyt wiele żądań.|
|500|Wystąpił błąd. Jeśli błąd będzie się powtarzać, raportowania identyfikator śledzenia klienta (X-ClientTraceId) lub identyfikator (identyfikator żądania X) żądania.|
|503|Serwer jest tymczasowo niedostępny. Ponów próbę żądania. Jeśli błąd będzie się powtarzać, raportowania identyfikator śledzenia klienta (X-ClientTraceId) lub identyfikator (identyfikator żądania X) żądania.|

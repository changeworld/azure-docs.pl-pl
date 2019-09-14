---
title: Metoda interfejs API tłumaczenia mowy w usłudze Translator Languages
titleSuffix: Azure Cognitive Services
description: Użyj metody interfejs API tłumaczenia mowy w usłudze Translator Languages.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 46ac3928238382f56db5a799226bd3d9243b7ca2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966412"
---
# <a name="translator-speech-api-languages"></a>Interfejs API tłumaczenia mowy w usłudze Translator: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Tłumaczenie mowy w usłudze Translator ciągle rozszerza listę języków obsługiwanych przez jej usługi. Ten interfejs API umożliwia odnalezienie zestawu języków, które są obecnie dostępne do użycia z usługą tłumaczenie mowy w usłudze Translator.

Przykłady kodu ilustrujące korzystanie z interfejsu API do uzyskiwania dostępnych języków są dostępne w [witrynie GitHub usługi Microsoft Translator](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Uwagi dotyczące implementacji

### <a name="get-languages"></a>Pobierz/Languages

Dostępny jest szeroki zestaw języków umożliwiających transkrypcja mowy, tłumaczenie uzyskanego tekstu oraz generowanie wygenerowanej mowy tłumaczenia.

Klient używa parametru zapytania `scope` , aby określić, które zestawy języków interesują.

* **Zamiana mowy na tekst:** Użyj parametru `scope=speech` zapytania, aby pobrać zestaw języków dostępnych do transkrypcja mowy na tekst.
* **Tłumaczenie tekstu:** Użyj parametru `scope=text` zapytania, aby pobrać zestaw języków dostępnych do tłumaczenia tekstu uzyskanego.
* **Zamiana tekstu na mowę:**  Użyj parametru `scope=tts` zapytania, aby pobrać zestaw języków i głosów dostępnych do tłumaczenia przetłumaczonego tekstu z powrotem na mowę.

Klient może pobrać wiele zestawów jednocześnie, określając listę opcji rozdzielonych przecinkami. Na przykład `scope=speech,text,tts`.

Pomyślna odpowiedź to obiekt JSON z właściwością dla każdego żądanego zestawu.

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

Ponieważ klient może użyć `scope` parametru zapytania, aby wybrać, które zestawy obsługiwanych języków należy zwrócić, rzeczywista odpowiedź może zawierać tylko podzbiór wszystkich właściwości przedstawionych powyżej.

Wartość podana dla każdej właściwości jest następująca.

### <a name="speech-to-text-speech"></a>Zamiana mowy na tekst (Speech)

Wartość skojarzona z właściwością zamiany mowy na tekst, `speech`jest słownikiem par (klucz, wartość). Każdy klucz identyfikuje język obsługiwany dla zamiany mowy na tekst. Klucz jest identyfikatorem przekazywanym przez klienta do interfejsu API. Wartość skojarzona z kluczem jest obiektem o następujących właściwościach:

* `name`: Nazwa wyświetlana języka.
* `language`: Tag języka skojarzonego języka pisania. Zobacz sekcję "transakcja tekstowa" poniżej.
Przykład:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Tłumaczenie tekstu (tekst)

Wartość skojarzona z `text` właściwością jest również słownikiem, gdzie każdy klucz identyfikuje język obsługiwany przez tłumaczenie tekstu. Wartość skojarzona z kluczem zawiera opis języka:

* `name`: Nazwa wyświetlana języka.
* `dir`: Kierunkowość `ltr` w przypadku języków pisanych od prawej do lewej lub języków od lewej do prawej. `rtl`

Przykład:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Zamiana tekstu na mowę (TTS)

Wartość skojarzona z właściwością zamiany tekstu na mowę jest również słownikiem, gdzie każdy klucz identyfikuje obsługiwany głos. Atrybuty obiektu głosowego są następujące:

* `displayName`: Nazwa wyświetlana głosu.
* `gender`: Płeć głosu (samców lub samic).
* `locale`: Tag języka głosu z atrybutu języka podstawowego i regionu atrybutu.
* `language`: Tag języka skojarzonego języka pisania.
* `languageName`: Nazwa wyświetlana języka.
* `regionName`: Nazwa wyświetlana regionu dla tego języka.

Przykład:

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
Usługa zwraca wszystkie nazwy w języku nagłówka "Accept-Language" dla wszystkich języków obsługiwanych w tłumaczeniu tekstowym.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Obiekt opisujący zestaw obsługiwanych języków.

Wartość ModelExample:

Langagues {Speech (Object, Optional), text (Object, Optional), TTS (Object, Optional)}

### <a name="headers"></a>Nagłówki

|nagłówek|Opis|Type|
:--|:--|:--|
X-RequestId|Wartość wygenerowana przez serwer w celu zidentyfikowania żądania i użycia na potrzeby rozwiązywania problemów.|ciąg|

### <a name="parameters"></a>Parametry

|Parametr|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|
|api-version    |Wersja interfejsu API żądana przez klienta. Dozwolone wartości to: `1.0`.|query|ciąg|
|scope  |Zestawy obsługiwanych języków lub głosów do zwrócenia do klienta programu. Ten parametr jest określany jako rozdzielana przecinkami lista słów kluczowych. Dostępne są następujące słowa kluczowe:<ul><li>`speech`: Zawiera zestaw języków obsługiwanych przez funkcję transkrypcja mowy.</li><li>`tts`: Zawiera zestaw głosów, które są obsługiwane w przypadku konwersji mowy tekstu.</li><li>`text`: Zawiera zestaw języków obsługiwanych na potrzeby tłumaczenia tekstu.</li></ul>Jeśli wartość nie jest określona, wartość `scope` `text`domyślna to.|query|ciąg|
|X-ClientTraceId    |Wygenerowany przez klienta identyfikator GUID służący do śledzenia żądania. Aby ułatwić rozwiązywanie problemów, klienci powinni zapewnić nową wartość przy użyciu każdego żądania i rejestrować ją.|nagłówek|ciąg|
|Zaakceptuj — język    |Niektóre pola w odpowiedzi są nazwami języków lub regionów. Ten parametr służy do definiowania języka, w którym są zwracane nazwy. Język jest określony przez udostępnienie poprawnie sformułowanego znacznika języka BCP 47. Wybierz tag z listy identyfikatorów języka zwracanych z `text` zakresem. W przypadku nieobsługiwanych języków nazwy są udostępniane w języku angielskim.<br/>Na przykład użyj wartości `fr` , aby zażądać nazw w języku francuskim, lub użyj wartości `zh-Hant` , aby zażądać nazw w języku chińskim tradycyjnym.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe, aby upewnić się, że są prawidłowe. Obiekt Response zawiera bardziej szczegółowy opis błędu.|
|429|Zbyt wiele żądań.|
|500|Wystąpił błąd. Jeśli błąd będzie się powtarzać, zgłoś go za pomocą identyfikatora śledzenia klienta (X-ClientTraceId) lub identyfikatora żądania (X-Numer_id_żądania).|
|503|Serwer jest tymczasowo niedostępny. Spróbuj ponownie wykonać żądanie. Jeśli błąd będzie się powtarzać, zgłoś go za pomocą identyfikatora śledzenia klienta (X-ClientTraceId) lub identyfikatora żądania (X-Numer_id_żądania).|

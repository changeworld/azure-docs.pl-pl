---
title: Metoda języków tekstu interfejsu API Microsoft Translator | Dokumentacja firmy Microsoft
description: Metoda Translator językach interfejsu API firmy Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 93c06218a560faf439f05903438d021b372ce257
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348721"
---
# <a name="text-api-30-languages"></a>Tekst interfejsu API 3.0: języków

Pobiera zestaw języków, które są obecnie obsługiwane przez inne operacje interfejsu API tekstu. 

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania są parametry przekazywane w ciągu kwerendy:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wersja interfejsu API</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API żądanego przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Opcjonalny parametr*.<br/>Rozdzielaną przecinkami listę nazw zdefiniowanie grupy języków do zwrócenia. Dozwolone są nazwy grup: `translation`, `transliteration` i `dictionary`. Jeśli żaden zakres nie jest podany, zwracane są wszystkie grupy, który jest odpowiednikiem przekazywanie `scope=translation,transliteration,dictionary`. Aby zdecydować, który zestaw obsługiwanych języków jest odpowiednia dla danego scenariusza, zobacz opis [obiekt odpowiedzi](#response-body).</td>
  </tr>
</table> 

Nagłówki żądania są:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Zaakceptuj języka</td>
    <td>*Nagłówek żądania opcjonalne*.<br/>Język do użycia dla ciągów interfejsu użytkownika. Niektóre pola w odpowiedzi są nazwy języków lub nazwy regionów. Ten parametr służy do określenia języka, w którym są zwracane te nazwy. Język jest określić, wprowadzając poprawnie sformułowanym znacznik języka BCP 47. Przykładowo, użyj wartości `fr` na żądanie nazwy w języku francuskim, lub użyj wartości `zh-Hant` na żądanie nazwy w języku chińskim tradycyjnym.<br/>Nazwy znajdują się w języku angielskim, jeśli nie określono języka docelowego lub gdy lokalizacja nie jest dostępna.
    </td>
  </tr>
  <tr>
    <td>X ClientTraceId</td>
    <td>*Nagłówek żądania opcjonalne*.<br/>Generowane przez klientów identyfikator GUID, aby jednoznacznie zidentyfikować żądania.</td>
  </tr>
</table> 

Uwierzytelnianie nie jest wymagane uzyskanie zasobów językowych.

## <a name="response-body"></a>Treść odpowiedzi

Klient używa `scope` parametr do zdefiniowania grupy języków, które go jest zainteresowana zapytania.

* `scope=translation` udostępnia języki obsługiwane umożliwia tłumaczenie tekstu z jednego języka na inny język;

* `scope=transliteration` zapewnia możliwości konwersji tekstu w jednym języku z jednego skryptu inny skrypt;

* `scope=dictionary` udostępnia kierunki, dla którego `Dictionary` operacji zwracanych danych.

Klient może pobrać kilku grup jednocześnie, określając rozdzielana przecinkami lista nazw. Na przykład `scope=translation,transliteration,dictionary` zwróci obsługiwanych języków dla wszystkich grup.

Odpowiedź oznaczająca Powodzenie to obiekt JSON z jedną właściwość dla każdej żądanej grupy:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

Wartość dla każdej właściwości ma następującą składnię.

* `translation` Właściwość

  Wartość `translation` właściwości jest słownikiem (klucz, wartość) pary. Każdy klucz jest znacznik języka BCP 47. Klucz określa język, dla którego tekst może być przekonwertowana na lub translacji z. Wartość skojarzoną z kluczem jest obiekt JSON z właściwościami, które opisują język:

  * `name`: Nazwa wyświetlana język w ustawieniach regionalnych za pośrednictwem `Accept-Language` nagłówka.

  * `nativeName`: Nazwa wyświetlana języka zgodnie z ustawieniami regionalnymi macierzystego dla tego języka.

  * `dir`: Kierunek tekstu, który jest `rtl` dla języków od prawej do lewej lub `ltr` dla języków od lewej do prawej.

  Przykładem jest:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration` Właściwość

  Wartość `transliteration` właściwości jest słownikiem (klucz, wartość) pary. Każdy klucz jest znacznik języka BCP 47. Klucz określa język, dla którego można przekonwertować tekst z jednego skryptu do innego skryptu. Wartość skojarzoną z kluczem jest obiekt JSON z właściwościami, które opisują języka i jego obsługiwanych skryptów:

  * `name`: Nazwa wyświetlana język w ustawieniach regionalnych za pośrednictwem `Accept-Language` nagłówka.

  * `nativeName`: Nazwa wyświetlana języka zgodnie z ustawieniami regionalnymi macierzystego dla tego języka.

  * `scripts`: Lista skryptów do przekonwertowania z. Każdy element `scripts` lista zawiera właściwości:

    * `code`: Kod identyfikujący skryptu.

    * `name`: Nazwa wyświetlana skryptu w ustawieniach regionalnych za pośrednictwem `Accept-Language` nagłówka.

    * `nativeName`: Nazwa wyświetlana języka macierzystego ustawień regionalnych dla języka.

    * `dir`: Kierunek tekstu, który jest `rtl` dla języków od prawej do lewej lub `ltr` dla języków od lewej do prawej.

    * `toScripts`: Lista skryptów do konwertowania tekstu. Każdy element `toScripts` ma listę właściwości `code`, `name`, `nativeName`, i `dir` zgodnie z wcześniejszym opisem.

  Przykładem jest:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary` Właściwość

  Wartość `dictionary` właściwości jest słownikiem (klucz, wartość) pary. Każdy klucz jest znacznik języka BCP 47. Klucz określa język, dla którego dostępne są alternatywne tłumaczenia i tłumaczeń Wstecz. Wartość jest obiekt JSON, który opisuje języka źródłowego i docelowego języków tłumaczeń dostępne:

  * `name`: Nazwa wyświetlana języka źródłowego zgodnie z ustawieniami regionalnymi za pośrednictwem `Accept-Language` nagłówka.

  * `nativeName`: Nazwa wyświetlana języka zgodnie z ustawieniami regionalnymi macierzystego dla tego języka.

  * `dir`: Kierunek tekstu, który jest `rtl` dla języków od prawej do lewej lub `ltr` dla języków od lewej do prawej.

  * `translations`: Lista języków tłumaczeń alternatywny i przykłady dla zapytania, wyrażone w języku źródła. Każdy element `translations` lista zawiera właściwości:

    * `name`: Nazwa wyświetlana języka docelowego w ustawieniach regionalnych za pośrednictwem `Accept-Language` nagłówka.

    * `nativeName`: Nazwa wyświetlana języka docelowego w ustawieniach regionalnych macierzystego dla języka docelowego.

    * `dir`: Kierunek tekstu, który jest `rtl` dla języków od prawej do lewej lub `ltr` dla języków od lewej do prawej.
    
    * `code`: Kod języka identyfikujący języka docelowego.

  Przykładem jest:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

Struktura obiekt odpowiedzi nie zmieni się bez zmian w wersji interfejsu API. Dla tej samej wersji interfejsu API listę dostępnych języków może zmienić wraz z upływem czasu, ponieważ Microsoft Translator stale rozszerza listę języków obsługiwanych przez jej usług.

Listę obsługiwanych języków nie będzie często zmieniana. Aby oszczędzić przepustowość sieci i zwiększyć elastyczność, aplikacja kliencka należy wziąć pod uwagę buforowanie zasobów językowych i odpowiedniego tagu jednostki (`ETag`). Następnie aplikacja kliencka może się okresowo (na przykład co 24 godziny) wysłać zapytania do usługi, aby pobrać najnowszy zestaw obsługiwanych języków. Przekazywanie bieżącego `ETag` wartość w `If-None-Match` pole nagłówka umożliwi usługę, aby zoptymalizować odpowiedzi. Jeśli zasób nie został zmodyfikowany, usługa zwróci kod stanu 304 i treści pustą odpowiedź.

## <a name="response-headers"></a>Nagłówki odpowiedzi

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>ETag</td>
    <td>Bieżąca wartość tagu jednostki dla żądanej grupy obsługiwanych języków. Aby kolejne żądania bardziej wydajne, klient może wysyłać `ETag` wartość w `If-None-Match` pola nagłówka.
    </td>
  </tr>
  <tr>
    <td>Identyfikator żądania X</td>
    <td>Wartość wygenerowana przez usługę, aby zidentyfikować żądania. Jest on używany na potrzeby rozwiązywania problemów.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie. 

<table width="100%">
  <th width="20%">Kod stanu</th>
  <th>Opis</th>
  <tr>
    <td>200</td>
    <td>Powodzenie.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Zasób nie został zmodyfikowany od czasu wersji określonej przez nagłówki żądania `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrów zapytania jest lub jest ona nieprawidłowa. Popraw parametry żądania przed ponowną próbą.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Obiekt wywołujący wysyła zbyt wiele żądań.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go przy użyciu: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`, a identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, zgłoś go przy użyciu: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`, a identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Przykłady

Poniższy przykład pokazuje, jak pobrać języki obsługiwane w przypadku tłumaczenie tekstu.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---

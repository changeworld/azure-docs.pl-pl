---
title: Metoda tłumaczenia języków interfejsu API tekstu
titleSuffix: Azure Cognitive Services
description: Languages Metoda pobiera zestaw języków obecnie obsługiwanych przez inne operacje interfejsu API tekstu translatora.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 37f70399e8125db559098869cdfffdf4533498d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73835828"
---
# <a name="translator-text-api-30-languages"></a>Translator Text API 3.0: Języki

Pobiera zestaw języków obecnie obsługiwanych przez inne operacje interfejsu API tekstu translatora. 

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` prośbę na:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przekazywane na ciąg zapytania są:

<table width="100%">
  <th width="20%">Parametr kwerendy</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td><em>Wymagany parametr</em>.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi `3.0`być .</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Parametr opcjonalny*.<br/>Oddzielona przecinkami lista nazw definiujących grupę języków do zwrócenia. Dozwolone nazwy grup `translation`to: , `transliteration` i `dictionary`. Jeśli nie podano zakresu, zwracane są wszystkie grupy, co jest równoznaczne z przekazywaniem `scope=translation,transliteration,dictionary`. Aby zdecydować, który zestaw obsługiwanych języków jest odpowiedni dla danego scenariusza, zobacz opis [obiektu odpowiedzi](#response-body).</td>
  </tr>
</table> 

Nagłówki żądań to:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Opcjonalny nagłówek żądania*.<br/>Język ciągów interfejsu użytkownika. Niektóre pola w odpowiedzi to nazwy języków lub nazwy regionów. Ten parametr służy do definiowania języka, w którym te nazwy są zwracane. Język jest określony przez zapewnienie dobrze sformułowanego tagu języka BCP 47. Na przykład użyj `fr` wartości, aby zażądać nazw `zh-Hant` w języku francuskim lub użyj tej wartości, aby zażądać nazw w języku chińskim tradycyjnym.<br/>Nazwy są podane w języku angielskim, gdy język docelowy nie jest określony lub gdy lokalizacja nie jest dostępna.
    </td>
  </tr>
  <tr>
    <td>Identyfikator X-ClientTraceId</td>
    <td>*Opcjonalny nagłówek żądania*.<br/>Identyfikator GUID wygenerowany przez klienta, aby jednoznacznie zidentyfikować żądanie.</td>
  </tr>
</table> 

Uwierzytelnianie nie jest wymagane do uzyskania zasobów językowych.

## <a name="response-body"></a>Treść odpowiedzi

Klient używa parametru `scope` kwerendy do zdefiniowania grup języków, które go interesują.

* `scope=translation`zapewnia języki obsługiwane do tłumaczenia tekstu z jednego języka na inny;

* `scope=transliteration`zapewnia możliwości konwersji tekstu w jednym języku z jednego skryptu na inny skrypt;

* `scope=dictionary`zawiera pary językowe, dla których `Dictionary` operacje zwracają dane.

Klient może pobrać kilka grup jednocześnie, określając listę nazw oddzielonych przecinkami. Na przykład `scope=translation,transliteration,dictionary` zwróci obsługiwane języki dla wszystkich grup.

Pomyślna odpowiedź to obiekt JSON z jedną właściwością dla każdej żądanej grupy:

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

Wartość dla każdej właściwości jest następująca.

* `translation`Właściwość

  Wartość `translation` właściwości jest słownikiem par (klucz, wartość). Każdy klucz jest tagiem języka BCP 47. Klucz identyfikuje język, dla którego tekst może być tłumaczony lub tłumaczony. Wartość skojarzona z kluczem jest obiektem JSON z właściwościami opisującymi język:

  * `name`: Wyświetlana nazwa języka w ustawieniach `Accept-Language` regionalnych żądanych za pośrednictwem nagłówka.

  * `nativeName`: Wyświetlana nazwa języka w środowisku lokalnym dla tego języka.

  * `dir`: Kierunkowość, `rtl` która jest dla języków od `ltr` prawej do lewej lub dla języków od lewej do prawej.

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

* `transliteration`Właściwość

  Wartość `transliteration` właściwości jest słownikiem par (klucz, wartość). Każdy klucz jest tagiem języka BCP 47. Klucz identyfikuje język, dla którego tekst może być konwertowany z jednego skryptu na inny skrypt. Wartość skojarzona z kluczem jest obiektem JSON z właściwościami opisującymi język i jego obsługiwane skrypty:

  * `name`: Wyświetlana nazwa języka w ustawieniach `Accept-Language` regionalnych żądanych za pośrednictwem nagłówka.

  * `nativeName`: Wyświetlana nazwa języka w środowisku lokalnym dla tego języka.

  * `scripts`: Lista skryptów do konwersji. Każdy element `scripts` listy ma właściwości:

    * `code`: Kod identyfikujący skrypt.

    * `name`: Wyświetlana nazwa skryptu w ustawieniach regionalnych żądanych za pośrednictwem `Accept-Language` nagłówka.

    * `nativeName`: Wyświetlana nazwa języka w środowisku lokalnym dla języka.

    * `dir`: Kierunkowość, `rtl` która jest dla języków od `ltr` prawej do lewej lub dla języków od lewej do prawej.

    * `toScripts`: Lista skryptów dostępnych do konwersji tekstu. Każdy element `toScripts` listy ma `code`właściwości `name` `nativeName`, `dir` , i jak opisano wcześniej.

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

* `dictionary`Właściwość

  Wartość `dictionary` właściwości jest słownikiem par (klucz, wartość). Każdy klucz jest tagiem języka BCP 47. Klucz określa język, dla którego dostępne są alternatywne tłumaczenia i tłumaczenia wsteczne. Wartość jest obiektem JSON, który opisuje język źródłowy i języki docelowe z dostępnymi tłumaczeniami:

  * `name`: Wyświetlana nazwa języka źródłowego w `Accept-Language` ustawieniach regionalnych żądanych za pośrednictwem nagłówka.

  * `nativeName`: Wyświetlana nazwa języka w środowisku lokalnym dla tego języka.

  * `dir`: Kierunkowość, `rtl` która jest dla języków od `ltr` prawej do lewej lub dla języków od lewej do prawej.

  * `translations`: Lista języków z przemiennych tłumaczeń i przykłady kwerendy wyrażone w języku źródłowym. Każdy element `translations` listy ma właściwości:

    * `name`: Wyświetlana nazwa języka docelowego w `Accept-Language` ustawieniach regionalnych żądanych za pośrednictwem nagłówka.

    * `nativeName`: Wyświetlana nazwa języka docelowego w środowisku lokalnym dla języka docelowego.

    * `dir`: Kierunkowość, `rtl` która jest dla języków od `ltr` prawej do lewej lub dla języków od lewej do prawej.
    
    * `code`: Kod języka identyfikujący język docelowy.

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

Struktura obiektu odpowiedzi nie zmieni się bez zmiany wersji interfejsu API. W przypadku tej samej wersji interfejsu API lista dostępnych języków może ulec zmianie w czasie, ponieważ usługa Microsoft Translator stale rozszerza listę języków obsługiwanych przez jego usługi.

Lista obsługiwanych języków nie będzie się często zmieniać. Aby zaoszczędzić przepustowość sieci i poprawić szybkość reakcji, aplikacja kliencka powinna`ETag`rozważyć buforowanie zasobów językowych i odpowiedniego znacznika encji ( ). Następnie aplikacja kliencka może okresowo (na przykład raz na 24 godziny) wysyłać zapytania do usługi, aby pobrać najnowszy zestaw obsługiwanych języków. Przekazanie bieżącej `ETag` wartości `If-None-Match` w polu nagłówka umożliwi usłudze optymalizację odpowiedzi. Jeśli zasób nie został zmodyfikowany, usługa zwróci kod stanu 304 i pustą treść odpowiedzi.

## <a name="response-headers"></a>Nagłówki odpowiedzi

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Etag</td>
    <td>Bieżąca wartość tagu encji dla żądanych grup obsługiwanych języków. Aby kolejne żądania były bardziej wydajne, klient może wysłać `ETag` wartość w polu nagłówka. `If-None-Match`
    </td>
  </tr>
  <tr>
    <td>X-RequestId (ida żądania X)</td>
    <td>Wartość generowana przez usługę w celu zidentyfikowania żądania. Jest on używany do rozwiązywania problemów.</td>
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
    <td>Zasób nie został zmodyfikowany, ponieważ wersja `If-None-Match`określona przez nagłówki żądań .</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy. Popraw parametry żądania przed ponowieniem próby.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Serwer odrzucił żądanie, ponieważ klient przekroczył limity żądań.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzał, zgłoś go z: datą i godziną awarii, identyfikatorem żądania z nagłówka `X-RequestId`odpowiedzi i identyfikatorem klienta z nagłówka `X-ClientTraceId`żądania .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer tymczasowo niedostępny. Ponów próbę żądania. Jeśli błąd będzie się powtarzał, zgłoś go z: datą i godziną awarii, identyfikatorem żądania z nagłówka `X-RequestId`odpowiedzi i identyfikatorem klienta z nagłówka `X-ClientTraceId`żądania .</td>
  </tr>
</table> 

Jeśli wystąpi błąd, żądanie zwróci również odpowiedź na błąd JSON. Kod błędu to 6-cyfrowy numer łączący 3-cyfrowy kod stanu HTTP, po którym następuje 3-cyfrowy numer w celu dalszej kategoryzowania błędu. Typowe kody błędów można znaleźć na [stronie odwołania do interfejsu API tekstu tłumacza translatora](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Przykłady

W poniższym przykładzie pokazano, jak pobrać języki obsługiwane do tłumaczenia tekstu.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

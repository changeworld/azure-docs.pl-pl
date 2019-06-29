---
title: Metoda języków interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Użyj metody interfejsu API usługi Translator tekstu/wszystkie języki.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 7bac97b7edbfa0f7a03a65e462106b33160c6d1a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435079"
---
# <a name="translator-text-api-30-languages"></a>Translator Text API 3.0: Języki

Pobiera zestaw języki, które są obecnie obsługiwane przez inne operacje interfejsu API tłumaczenia tekstu. 

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` limit czasu żądania:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania, parametry przekazane w ciągu zapytania są:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td><em>Wymagany parametr</em>.<br/>Wersja interfejsu API zażądane przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Opcjonalny parametr*.<br/>Rozdzielana przecinkami lista nazw zdefiniowanie grupy języków do zwrócenia. Dozwolone są nazwy grup: `translation`, `transliteration` i `dictionary`. Jeśli żaden zakres nie jest podany, a następnie zwracane są wszystkie grupy, który jest odpowiednikiem przekazywanie `scope=translation,transliteration,dictionary`. Aby zdecydować, które zestaw obsługiwanych języków jest odpowiednie dla danego scenariusza, zobacz opis [obiekt odpowiedzi](#response-body).</td>
  </tr>
</table> 

Nagłówki żądania są:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Zaakceptuj języka</td>
    <td>*Nagłówek żądania opcjonalne*.<br/>Język ciągów interfejsu użytkownika. Niektóre pola w odpowiedzi są nazwy języków lub nazw regionów. Użyj tego parametru, aby określić język, w której zwracane są te nazwy. Język jest określony, podając tag języka sformułowany BCP 47. Na przykład użyj wartości `fr` do żądania nazw w języku francuskim, lub użyj wartości `zh-Hant` do żądania nazw w chińskim tradycyjnym.<br/>Nazwy są dostarczane w języku angielskim, gdy język docelowy nie jest określony lub gdy lokalizacja jest niedostępna.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Nagłówek żądania opcjonalne*.<br/>Generowane przez klienta identyfikator GUID do unikatowego identyfikowania żądania.</td>
  </tr>
</table> 

Można pobrać zasobów języka nie jest wymagane uwierzytelnienie.

## <a name="response-body"></a>Treść odpowiedzi

Klient używa `scope` parametr do definiowania, które grupy języków jest zainteresowany zapytania.

* `scope=translation` zawiera języki obsługiwane umożliwia tłumaczenie tekstu z jednego języka na inny język;

* `scope=transliteration` zapewnia funkcje do konwertowania tekstu w jednym języku z jednego skryptu na inny skrypt;

* `scope=dictionary` udostępnia kierunki, dla którego `Dictionary` operacje zwracanych danych.

Klient może pobrać kilku grup jednocześnie, określając listę rozdzielonych przecinkami nazw. Na przykład `scope=translation,transliteration,dictionary` zwróci obsługiwane języki dla wszystkich grup.

Odpowiedź oznaczająca Powodzenie to obiekt JSON z jednej właściwości dla każdej żądanej grupy:

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

Wartość dla każdej właściwości jest w następujący sposób.

* `translation` Właściwość

  Wartość `translation` właściwość jest słownikiem (klucz, wartość) pary. Każdy klucz jest tag języka BCP 47. Klucz identyfikuje język, dla którego tekst mogą być tłumaczone lub przetłumaczone z. Wartość skojarzoną z kluczem jest obiekt JSON z właściwościami, które opisują języka:

  * `name`: Nazwa wyświetlana języka w ustawieniach regionalnych za pośrednictwem `Accept-Language` nagłówka.

  * `nativeName`: Nazwa wyświetlana języka w ustawieniach regionalnych natywne dla tego języka.

  * `dir`: Kierunek tekstu, który jest `rtl` w językach od prawej do lewej lub `ltr` w językach od lewej do prawej.

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

  Wartość `transliteration` właściwość jest słownikiem (klucz, wartość) pary. Każdy klucz jest tag języka BCP 47. Klucz identyfikuje język, dla którego można przekonwertować tekst z jednego skryptu do innego skryptu. Wartość skojarzoną z kluczem jest obiekt JSON z właściwościami, które opisują, języka i jego obsługiwanych skryptów:

  * `name`: Nazwa wyświetlana języka w ustawieniach regionalnych za pośrednictwem `Accept-Language` nagłówka.

  * `nativeName`: Nazwa wyświetlana języka w ustawieniach regionalnych natywne dla tego języka.

  * `scripts`: Lista skryptów, aby przekonwertować. Każdy element obiektu `scripts` liście ma właściwości:

    * `code`: Kod identyfikujący skryptu.

    * `name`: Nazwa wyświetlana skryptu w ustawieniach regionalnych za pośrednictwem `Accept-Language` nagłówka.

    * `nativeName`: Wyświetlana nazwa języka w ustawieniach regionalnych natywne dla języka.

    * `dir`: Kierunek tekstu, który jest `rtl` w językach od prawej do lewej lub `ltr` w językach od lewej do prawej.

    * `toScripts`: Lista dostępnych do konwertowania tekstu do skryptów. Każdy element obiektu `toScripts` liście ma właściwości `code`, `name`, `nativeName`, i `dir` zgodnie z wcześniejszym opisem.

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

  Wartość `dictionary` właściwość jest słownikiem (klucz, wartość) pary. Każdy klucz jest tag języka BCP 47. Klucz identyfikuje język, dla którego alternatywnych tłumaczeń i ponownie tłumaczenia są dostępne. Wartość jest obiekt JSON, który opisuje języka źródłowego i docelowego języków dostępne tłumaczenia:

  * `name`: Nazwa wyświetlana języka źródłowego w ustawieniach regionalnych za pośrednictwem `Accept-Language` nagłówka.

  * `nativeName`: Nazwa wyświetlana języka w ustawieniach regionalnych natywne dla tego języka.

  * `dir`: Kierunek tekstu, który jest `rtl` w językach od prawej do lewej lub `ltr` w językach od lewej do prawej.

  * `translations`: Lista języków za pomocą alternatywny tłumaczenia i przykłady dla zapytania wyrażona w języku źródła. Każdy element obiektu `translations` liście ma właściwości:

    * `name`: Nazwa wyświetlana języka docelowego w ustawieniach regionalnych za pośrednictwem `Accept-Language` nagłówka.

    * `nativeName`: Wyświetlana nazwa języka docelowego w ustawieniach regionalnych natywne dla języka docelowego.

    * `dir`: Kierunek tekstu, który jest `rtl` w językach od prawej do lewej lub `ltr` w językach od lewej do prawej.
    
    * `code`: Identyfikowanie język docelowy kodu języka.

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

Struktura obiekt odpowiedzi nie zmieni się bez zmian w wersji interfejsu API. Dla tej samej wersji interfejsu API listę dostępnych języków czasem ulec zmianie, ponieważ usługa Microsoft Translator nieustannie rozszerza listę języków obsługiwanych przez jego usług.

Listę obsługiwanych języków nie zmieni się często. Aby oszczędzić przepustowość sieci i zwiększyć szybkość reakcji, aplikacja kliencka należy wziąć pod uwagę buforowanie zasobów językowych i odpowiednim tagu jednostki (`ETag`). Następnie aplikacja kliencka może okresowo (na przykład co 24 godziny) zapytań do usługi, aby pobrać najnowszy zestaw obsługiwanych języków. Przekazywanie bieżącego `ETag` wartość w `If-None-Match` pole nagłówka umożliwi usługi zoptymalizować odpowiedzi. Jeśli zasób nie został zmodyfikowany, usługa zwróci kod stanu 304 i pusta treść odpowiedzi.

## <a name="response-headers"></a>Nagłówki odpowiedzi

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Element eTag</td>
    <td>Bieżąca wartość tagu jednostki dla żądanej grupy obsługiwanych języków. Aby kolejnych żądań bardziej wydajne, klient może wysyłać `ETag` wartość w `If-None-Match` pola nagłówka.
    </td>
  </tr>
  <tr>
    <td>X-RequestId</td>
    <td>Wartość generowane przez usługę w celu zidentyfikowania żądania. Jest on używany na potrzeby rozwiązywania problemów.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Kody stanów odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie. 

<table width="100%">
  <th width="20%">Kod stanu:</th>
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
    <td>To jeden z parametrów zapytania, lub jest on nieprawidłowy. Popraw parametry żądania przed ponowieniem próby.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Serwer odrzucił żądanie, ponieważ klienta przekroczyła limity żądań.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`oraz identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, zgłoś go: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`oraz identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
</table> 

Jeśli wystąpi błąd, żądanie zwróci błąd odpowiedź w formacie JSON. Kod błędu to łączenie liczb 6-cyfrowym, 3-cyfrowy kod stanu HTTP następuje 3-cyfrowy numer do dalszego kategoryzowanie błędu. Typowe kody błędów można znaleźć na [strona referencyjna interfejsu API tłumaczenia tekstu w wersji 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Przykłady

Poniższy przykład pokazuje, jak pobrać języki obsługiwane w przypadku tłumaczenie tekstu.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---

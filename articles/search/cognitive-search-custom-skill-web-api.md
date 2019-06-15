---
title: Umiejętności niestandardowe wyszukiwanie kognitywne — usługa Azure Search
description: Rozszerz możliwości dokładniejsze wyszukiwanie kognitywne przez wywołanie metody się z interfejsami API sieci Web
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seojan2018
ms.openlocfilehash: e5f7ee172563a81d45e3a35da2cfc7e8731de48d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023854"
---
# <a name="custom-web-api-skill"></a>Niestandardowy interfejs API sieci Web umiejętności

**Niestandardowego interfejsu API sieci Web** umiejętności pozwala rozszerzyć usłudze wyszukiwania poznawczego wywoływania interfejsu API sieci Web punktu końcowego, zapewniając operacjach niestandardowych. Podobnie jak wbudowane umiejętności **interfejsu API sieci Web usługi Custom** umiejętności zawiera dane wejściowe i wyjściowe. W zależności od danych wejściowych, internetowy interfejs API odbiera ładunek w formacie JSON, po uruchomieniu indeksatora i zwraca ładunek JSON jako odpowiedź, wraz z kodem stanu Powodzenie. Odpowiedź jest powinny mieć określone przez Twoje umiejętności niestandardowe dane wyjściowe. Inne odpowiedzi jest uznawany za błąd i wzbogacenia nie są wykonywane.

Struktura ładunków JSON to dokładniejszym opisem zawartym w dół w tym dokumencie.

> [!NOTE]
> Indeksator ponowi próbę w pewnych standardowych kodów stanu HTTP zwrócony z interfejsu API sieci Web. Te kody stanu HTTP są: 
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| Identyfikator URI | Identyfikator URI interfejsu API sieci Web, do którego _JSON_ ładunku będą wysyłane. Tylko **https** schemat identyfikatora URI jest dozwolone. |
| HttpMethod | Metoda do użycia podczas wysyłania ładunku. Dozwolone metody to `PUT` lub `POST` |
| httpHeaders | Kolekcja par klucz wartość, gdzie klucze reprezentują nagłówek nazwy i wartości reprezentują wartości nagłówka, które będą wysyłane do internetowego interfejsu API wraz z ładunku. Następujące nagłówki mają zakaz znajdujące się w tej kolekcji: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| timeout | (Opcjonalnie) Jeśli zostanie określony, wskazuje limit czasu wywołania interfejsu API klienta http. Musi być sformatowany jako wartość XSD "dayTimeDuration" (ograniczony podzestaw [czas trwania ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) wartości). Na przykład `PT60S` 60 sekund. Jeśli nie zostanie wybrany zestaw wartości domyślnej równej 30 sekund. Limit czasu można ustawić maksymalnie 90 sekund i co najmniej 1 sekundę. |
| batchSize | (Opcjonalnie) Wskazuje, ile "rekordy data" (zobacz _JSON_ struktura poniższego schematu ładunku) będą wysyłane na wywołania interfejsu API. Jeśli nie zostanie wybrany zestaw, domyślnie 1000. Firma Microsoft zaleca, aby użyć tego parametru, aby osiągnąć odpowiednie zależność między przepływność indeksowania i obciążenie interfejsu API |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

Nie ma żadnych "wstępnie zdefiniowanego" dane wejściowe dla tej umiejętności. Możesz wybrać jedno lub więcej pól, które będą już dostępne w czasie wykonywania tej umiejętności jako dane wejściowe i _JSON_ obciążenie wysyłane do interfejsu API sieci Web będzie miał innego pola.

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności

Nie ma żadnych "wstępnie zdefiniowanego" danych wyjściowych dla tej umiejętności. W zależności od odpowiedzi zwróci interfejsu API sieci Web, Dodaj pola danych wyjściowych, tak, aby ich mogą być pobierane z _JSON_ odpowiedzi.


## <a name="sample-definition"></a>Przykładowa definicja

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can count the number of words or characters or lines in text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "countOf",
            "source": "/document/propertyToCount"
          }
        ],
        "outputs": [
          {
            "name": "count",
            "targetName": "countOfThings"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Przykładowa struktura danych wejściowych w JSON

To _JSON_ struktury reprezentuje ładunek, który zostanie wysłany do interfejsu API sieci Web.
Zawsze będzie przestrzegany tych warunków ograniczających:

* Jednostki najwyższego poziomu jest nazywany `values` i będzie Tablica obiektów. Liczba obiektów jest co najwyżej `batchSize`
* Każdy obiekt w `values` odniesie tablicy
    * A `recordId` właściwość, która jest **unikatowy** ciąg, używany do identyfikowania tego rekordu.
    * A `data` właściwość, która jest _JSON_ obiektu. Pola `data` właściwość odpowiada "nazwy" określona w `inputs` sekcja definicji umiejętności. Wartości tych pól, zostanie ona nadana `source` tych pól, (które mogą być z polem w dokumencie lub potencjalnie umiejętności inny)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "countOf": "words"
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "countOf": "characters"
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world \r\n Hi World",
             "language": "en",
             "countOf": "lines"
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "countOf": null
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Przykładowe dane wyjściowe JSON struktury

"Wyjście" odnosi się do odpowiedź zwrócona z internetowego interfejsu API. Internetowy interfejs API powinien zwrócić tylko _JSON_ ładunek (zweryfikowane, analizując `Content-Type` nagłówek odpowiedzi) i powinny spełniać następujące ograniczenia:

* Powinna istnieć jednostkę najwyższego poziomu o nazwie `values` powinien być Tablica obiektów.
* Liczba obiektów w tablicy powinna być taka sama jak liczba obiektów wysyłanych do interfejsu API sieci Web.
* Każdy obiekt powinien mieć:
   * A `recordId` właściwości
   * A `data` właściwość, która jest obiektem, w których pola są wzbogacenia dopasowywania "nazwy" w `output` i których wartość jest uważana za wzbogacanie.
   * `errors` Właściwość, tablicę z listą wszystkich wykrytych błędów zostaną dodane do historii wykonywania indeksatora. Ta właściwość jest wymagana, ale może mieć `null` wartość.
   * A `warnings` właściwość, tablica ofercie wszelkie ostrzeżenia napotkano, które zostaną dodane do historii wykonywania indeksatora. Ta właściwość jest wymagana, ale może mieć `null` wartość.
* Obiekty w `values` tablicy nie musi być w tej samej kolejności, jak obiekty w `values` tablicy wysyłane jako żądanie do interfejsu API sieci Web. Jednak `recordId` służy do korelacji, dzięki czemu dowolny rekord w odpowiedzi, zawierający `recordId` który nie jest częścią oryginalne żądanie do interfejsu API sieci Web zostaną odrzucone.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "Cannot understand what needs to be counted"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "count": 2
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "count": 6
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "count": 11
            },
            "errors": null,
            "warnings": null
        },
    ]
}

```

## <a name="error-cases"></a>W przypadku wystąpienia błędów
Oprócz interfejsu API sieci Web jest niedostępna lub wysyłania się kodów stanu bez powodzenia poniżej są uważane za błędne przypadków:

* Jeśli internetowy interfejs API zwraca kod stanu powodzenia, ale odpowiedź wskazuje, że nie jest `application/json` odpowiedzi jest uznawane za nieprawidłowe i nie wzbogacenia zostaną wykonane.
* W przypadku **nieprawidłowy** (przy użyciu `recordId` niepodlegających oryginalne żądanie lub ze zduplikowanymi wartościami) rekordy w odpowiedzi `values` tablicy, wzbogacanie nie zostaną wykonane na **tych** rekordów.

W przypadkach, gdy interfejsu API sieci Web jest niedostępna lub zwraca błąd HTTP, przyjazne błąd wszystkie dostępne szczegółowe informacje o błędzie HTTP zostanie dodany do historii wykonywania indeksatora.

## <a name="see-also"></a>Zobacz także

+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Dodaj umiejętności niestandardowe do wyszukiwania kognitywnego](cognitive-search-custom-skill-interface.md)
+ [Tworzenie niestandardowych umiejętności, przy użyciu interfejsu API tłumaczenia tekstu](cognitive-search-create-custom-skill-example.md)
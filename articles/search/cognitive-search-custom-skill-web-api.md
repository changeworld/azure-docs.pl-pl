---
title: Niestandardowa umiejętność wyszukiwania poznawczego — Azure Search
description: Zwiększaj możliwości umiejętności wyszukiwania poznawczego, wywołując do interfejsów API sieci Web
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: bd38dacd9161812032512fcb8bfdcbe90d64731c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639870"
---
# <a name="custom-web-api-skill"></a>Niestandardowa umiejętność interfejsu API sieci Web

Niestandardowa umiejętność **interfejsu API sieci Web** umożliwia przeprowadzenie wyszukiwania poznawczego przez wywoływanie do punktu końcowego interfejsu API sieci Web dostarczającego operacje niestandardowe. Podobnie jak w przypadku wbudowanych umiejętności, niestandardowa umiejętność **interfejsu API sieci Web** ma dane wejściowe i wyjściowe. W zależności od danych wejściowych internetowy interfejs API odbiera ładunek JSON podczas uruchamiania indeksatora i wyprowadza ładunek JSON jako odpowiedź wraz z kodem stanu sukcesu. Oczekiwano, że odpowiedź będzie miała dane wyjściowe określone przez niestandardową umiejętność. Jakakolwiek inna odpowiedź jest traktowana jako błąd i nie są wykonywane żadne wzbogacania.

Struktura ładunków JSON została opisana w dalszej części tego dokumentu.

> [!NOTE]
> Indeksator zostanie dwukrotnie ponowiony dla niektórych standardowych kodów stanu HTTP zwróconych z internetowego interfejsu API. Kody stanu HTTP: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| identyfikator URI | Identyfikator URI internetowego interfejsu API, do którego zostanie wysłany ładunek _JSON_ . Dozwolony jest tylko schemat URI **https** |
| httpMethod | Metoda do użycia podczas wysyłania ładunku. Dozwolone metody to `PUT` lub`POST` |
| httpHeaders | Kolekcja par klucz-wartość, gdzie klucze reprezentują nazwy i wartości nagłówków, reprezentujące wartości nagłówka, które będą wysyłane do internetowego interfejsu API wraz z ładunkiem. `Accept`Następujące nagłówki nie mogą być w tej kolekcji: `Content-Type` `Content-Length` `Accept-Encoding` `Accept-Charset`, `Cookie` ,,`TE`,,,,,, `Host` `Upgrade``Via` |
| limit czasu | Obowiązkowe Gdy ta wartość jest określona, wskazuje limit czasu dla klienta http wykonującego wywołanie interfejsu API. Musi być sformatowana jako wartość XSD "dayTimeDuration" (ograniczony podzbiór wartości [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Na przykład `PT60S` przez 60 sekund. Jeśli nie zostanie ustawiona, zostanie wybrana wartość domyślna wynosząca 30 sekund. Limit czasu można ustawić na maksymalnie 230 sekund i co najmniej 1 sekundę. |
| batchSize | Obowiązkowe Wskazuje, ile "rekordów danych" (patrz struktura ładunku _JSON_ poniżej) zostanie wysłana na wywołanie interfejsu API. Jeśli nie zostanie ustawiona, zostanie wybrana wartość domyślna 1000. Zalecamy użycie tego parametru, aby osiągnąć odpowiednią kompromis między indeksowaniem przepływności i obciążeniem interfejsu API |

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

Brak "wstępnie zdefiniowanych" danych wejściowych dla tej umiejętności. Można wybrać jedno lub więcej pól, które będą już dostępne w momencie wykonywania tego umiejętności, ponieważ dane wejściowe i ładunek _JSON_ wysyłane do internetowego interfejsu API będą mieć różne pola.

## <a name="skill-outputs"></a>Wyniki umiejętności

Brak "wstępnie zdefiniowanych" danych wyjściowych dla tej umiejętności. W zależności od odpowiedzi zwracanej przez internetowy interfejs API Dodaj pola wyjściowe, aby można je było pobrać z odpowiedzi _JSON_ .


## <a name="sample-definition"></a>Definicja Przykładowa

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
## <a name="sample-input-json-structure"></a>Przykładowa wejściowa struktura JSON

Ta struktura _JSON_ reprezentuje ładunek, który zostanie wysłany do internetowego interfejsu API.
Zawsze będą przestrzegane następujące ograniczenia:

* Jednostka najwyższego poziomu jest wywoływana `values` i będzie tablicą obiektów. Liczba takich obiektów nie może być większa niż`batchSize`
* Każdy obiekt w `values` tablicy będzie miał
    * Właściwość, która jest unikatowym ciągiem używanym do identyfikowania tego rekordu. `recordId`
    * Właściwość, która jest obiektem _JSON._ `data` Pola `data` właściwości będą odpowiadać nazwie "names" określonej `inputs` w sekcji definicji umiejętności. Wartości tych pól będą pochodzić z `source` tych pól (które mogą pochodzić z pola w dokumencie lub potencjalnie z innej umiejętności)

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

## <a name="sample-output-json-structure"></a>Przykładowa wyjściowa struktura JSON

"Wynik" odpowiada odpowiedzi zwróconej przez internetowy interfejs API. Internetowy interfejs API powinien zwrócić tylko ładunek _JSON_ (zweryfikowany, przeglądając `Content-Type` nagłówek odpowiedzi) i powinien spełniać następujące ograniczenia:

* Powinna istnieć jednostka najwyższego poziomu o nazwie `values` , która powinna być tablicą obiektów.
* Liczba obiektów w tablicy powinna być taka sama jak liczba obiektów wysyłanych do internetowego interfejsu API.
* Każdy obiekt powinien mieć:
   * `recordId` Właściwość
   * Właściwość, która jest obiektem, w którym pola są wzbogacane pasujące do "names" `output` w i którego wartość jest uważana za wzbogacanie. `data`
   * `errors` Właściwość tablica zawierająca błędy, które zostaną dodane do historii wykonywania indeksatora. Ta właściwość jest wymagana, ale może mieć `null` wartość.
   * `warnings` Właściwość tablica zawierająca wszystkie napotkane ostrzeżenia, które zostaną dodane do historii wykonywania indeksatora. Ta właściwość jest wymagana, ale może mieć `null` wartość.
* Obiekty w `values` tablicy nie muszą znajdować się w tej samej kolejności, co obiekty `values` w tablicy wysyłane jako żądanie do internetowego interfejsu API. Jednak jest używany do korelacji, dlatego każdy rekord w odpowiedzi `recordId` zawierającej, który nie jest częścią oryginalnego żądania do internetowego interfejsu API zostanie odrzucony. `recordId`

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

## <a name="error-cases"></a>Przypadki błędów
Oprócz nieprawidłowego interfejsu API sieci Web lub wysyłania kodów stanu, które nie zostały pomyślnie, są uważane za błędne przypadki:

* Jeśli internetowy interfejs API zwraca kod stanu sukcesu, ale odpowiedź wskazuje, że nie `application/json` jest, odpowiedź jest uznawana za nieprawidłową i nie zostaną wykonane żadne wzbogacania.
* Jeśli w tablicy odpowiedzi `values` są **nieprawidłowe** (z `recordId` niezawartymi w oryginalnym żądaniu lub duplikatami wartości), do **tych** rekordów nie będą wykonywane żadne wzbogacanie.

W przypadku niedostępności internetowego interfejsu API lub zwrócenie błędu HTTP, przyjazny błąd ze wszystkimi dostępnymi szczegółami dotyczącymi błędu HTTP zostanie dodany do historii wykonywania indeksatora.

## <a name="see-also"></a>Zobacz także

+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Dodaj niestandardową umiejętność wyszukiwania poznawczego](cognitive-search-custom-skill-interface.md)
+ [Przykład: Tworzenie niestandardowej umiejętności wyszukiwania poznawczego](cognitive-search-create-custom-skill-example.md)
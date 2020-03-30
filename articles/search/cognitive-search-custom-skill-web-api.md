---
title: Niestandardowe umiejętności interfejsu API sieci Web w zestawach umiejętności
titleSuffix: Azure Cognitive Search
description: Rozszerzanie możliwości zestawów umiejętności usługi Azure Cognitive Search przez wywoływanie interfejsów API sieci Web. Użyj niestandardowego interfejsu API sieci Web, aby zintegrować kod niestandardowy.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74484111"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Niestandardowe umiejętności interfejsu API sieci Web w potoku wzbogacania usługi Azure Cognitive Search

**Niestandardowe web API** umiejętności pozwala rozszerzyć wzbogacanie AI przez wywołanie do punktu końcowego interfejsu API sieci Web zapewniając operacje niestandardowe. Podobnie jak wbudowane umiejętności, **niestandardowe** web api umiejętności ma dane wejściowe i wyjścia. W zależności od danych wejściowych interfejs API sieci Web odbiera ładunek JSON po uruchomieniu indeksatora i wyprowadza ładunek JSON jako odpowiedź, wraz z kodem stanu sukcesu. Oczekuje się, że odpowiedź ma dane wyjściowe określone przez umiejętności niestandardowe. Każda inna odpowiedź jest uważana za błąd i nie są wykonywane żadne wzbogacenia.

Struktura ładunków JSON są opisane w dalszej części tego dokumentu.

> [!NOTE]
> Indeksator ponowi próbę dwukrotnie dla niektórych standardowych kodów stanu HTTP zwróconych z interfejsu API sieci Web. Te kody stanu HTTP są następujące: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| Identyfikator uri | Identyfikator URI interfejsu API sieci Web, do którego zostanie wysłany ładunek _JSON._ Dozwolony jest tylko schemat identyfikatora URI **https** |
| HttpMethod | Metoda używana podczas wysyłania ładunku. Dozwolone metody `PUT` są lub`POST` |
| httpHeaders ( | Kolekcja par klucza wartość, gdzie klucze reprezentują nazwy nagłówków i wartości reprezentują wartości nagłówka, które zostaną wysłane do interfejsu API sieci Web wraz z ładunkiem. W kolekcji nie wolno umieszczać następujących `Accept` `Accept-Charset`nagłówków: , `Host` `TE`, `Upgrade` `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, , , , ,`Via` |
| timeout | (Opcjonalnie) Po określeniu wskazuje limit czasu dla klienta http, który nawiązuje wywołanie interfejsu API. Musi być sformatowany jako XSD "dayTimeDuration" wartość (ograniczony podzbiór [iso 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) wartość czasu trwania). Na przykład `PT60S` przez 60 sekund. Jeśli nie jest ustawiona, wybrana jest domyślna wartość 30 sekund. Limit czasu można ustawić maksymalnie 230 sekund i co najmniej 1 sekundę. |
| batchSize | (Opcjonalnie) Wskazuje, ile "rekordów danych" (zobacz struktura ładunku _JSON_ poniżej) zostanie wysłanych na wywołanie interfejsu API. Jeśli nie jest ustawiona, zostanie wybrana wartość domyślna 1000. Zaleca się korzystanie z tego parametru w celu osiągnięcia odpowiedniego kompromisu między indeksowania przepływności i obciążenia interfejsu API |
| stopieńParalelizm | (Opcjonalnie) Po określeniu wskazuje liczbę wywołań, które indeksator będzie wykonywać równolegle do podanego punktu końcowego. Można zmniejszyć tę wartość, jeśli punkt końcowy kończy się niepowodzeniem w zbyt wysokim obciążeniu żądania lub podnieść go, jeśli punkt końcowy jest w stanie zaakceptować więcej żądań i chcesz zwiększyć wydajność indeksatora.  Jeśli nie jest ustawiona, używana jest wartość domyślna 5. StopieńOfParallelism można ustawić maksymalnie 10 i co najmniej 1. |

## <a name="skill-inputs"></a>Wprowadzanie umiejętności

Nie ma żadnych "wstępnie zdefiniowanych" danych wejściowych dla tej umiejętności. Można wybrać jedno lub więcej pól, które byłyby już dostępne w momencie wykonywania tej umiejętności jako dane wejściowe, a ładunek _JSON_ wysyłany do interfejsu API sieci Web będzie miał różne pola.

## <a name="skill-outputs"></a>Wyniki umiejętności

Nie ma żadnych "wstępnie zdefiniowanych" wyjść dla tej umiejętności. W zależności od odpowiedzi interfejsu API sieci Web zwróci, dodać pola wyjściowe, dzięki czemu mogą być pobierane z odpowiedzi _JSON._


## <a name="sample-definition"></a>Przykładowa definicja

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
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
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Przykładowa struktura JSON wejściowa

Ta struktura _JSON_ reprezentuje ładunek, który zostanie wysłany do interfejsu API sieci Web.
Zawsze będzie przestrzegać tych ograniczeń:

* Jednostka najwyższego poziomu `values` jest wywoływana i będzie tablicą obiektów. Liczba takich obiektów będzie co najwyżej`batchSize`
* Każdy obiekt `values` w tablicy będzie miał
    * Właściwość, `recordId` która jest **unikatowy** ciąg, używany do identyfikowania tego rekordu.
    * Właściwość, `data` która jest obiektem _JSON._ Pola `data` właściwości będą odpowiadać "nazw" określonych `inputs` w sekcji definicji umiejętności. Wartość tych pól będzie pochodzić `source` z tych pól (które mogą pochodzić z pola w dokumencie lub potencjalnie z innej umiejętności)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Przykładowa struktura JSON wyprowadzania

"Dane wyjściowe" odpowiada odpowiedzi zwróconej z interfejsu API sieci Web. Interfejs API sieci Web powinien zwracać tylko ładunek `Content-Type` _JSON_ (zweryfikowany przez przeglądanie nagłówka odpowiedzi) i powinien spełniać następujące ograniczenia:

* Powinna istnieć jednostka najwyższego `values` poziomu wywoływana, która powinna być tablicą obiektów.
* Liczba obiektów w tablicy powinna być taka sama jak liczba obiektów wysyłanych do interfejsu API sieci Web.
* Każdy obiekt powinien mieć:
   * Właściwość `recordId`
   * Właściwość, `data` która jest obiektem, w którym pola są `output` wzbogacania pasujące do "nazwy" w i którego wartość jest uważany za wzbogacenie.
   * Właściwość, `errors` tablica zawierająca listę napotkanych błędów, które zostaną dodane do historii wykonywania indeksatora. Ta właściwość jest wymagana, `null` ale może mieć wartość.
   * Właściwość, `warnings` tablica zawierająca listę napotkanych ostrzeżeń, które zostaną dodane do historii wykonywania indeksatora. Ta właściwość jest wymagana, `null` ale może mieć wartość.
* Obiekty w `values` tablicy nie muszą być w tej `values` samej kolejności, co obiekty w tablicy wysyłane jako żądanie do interfejsu API sieci Web. Jednak `recordId` jest używany do korelacji, więc każdy rekord `recordId` w odpowiedzi zawierającej, który nie był częścią oryginalnego żądania do interfejsu API sieci Web zostaną odrzucone.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Przypadki błędów
Oprócz niedostępności interfejsu API sieci Web lub wysyłania kodów stanu, które nie powiodły się, następujące przypadki są uważane za błędne przypadki:

* Jeśli interfejs API sieci Web zwraca kod stanu sukcesu, `application/json` ale odpowiedź wskazuje, że nie jest, odpowiedź jest uznawana za nieprawidłową i nie zostaną wykonane żadne wzbogacenia.
* Jeśli istnieją **nieprawidłowe** `recordId` (nie w oryginalnym żądaniu lub z `values` duplikatami wartości) rekordy w tablicy odpowiedzi, nie zostanie wykonane wzbogacenie dla **tych** rekordów.

W przypadkach, gdy interfejs API sieci Web jest niedostępny lub zwraca błąd HTTP, przyjazny błąd z wszelkimi dostępnymi szczegółami dotyczącymi błędu HTTP zostanie dodany do historii wykonywania indeksatora.

## <a name="see-also"></a>Zobacz też

+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Dodawanie umiejętności niestandardowych do potoku wzbogacania si](cognitive-search-custom-skill-interface.md)
+ [Przykład: Tworzenie niestandardowej umiejętności wzbogacania SI](cognitive-search-create-custom-skill-example.md)

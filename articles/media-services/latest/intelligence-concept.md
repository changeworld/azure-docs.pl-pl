---
title: Analizy multimediów Azure | Dokumentacja firmy Microsoft
description: Korzystając z usługi Azure Media Services, można analizować użytkownika przy użyciu AudioAnalyzerPreset i VideoAnalyzerPreset contnet audio i wideo.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2018
ms.author: juliako
ms.openlocfilehash: c488060b9db0ba482d12eee2394e5149b918950e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331524"
---
# <a name="media-intelligence"></a>Inteligentna analiza multimediów

Usługi Azure Media Services REST API v3 umożliwia analizowanie zawartości audio i wideo. Do analizowania zawartości, należy utworzyć **przekształcenie** i przesyłanie **zadania** jednym predefiniowane: **AudioAnalyzerPreset** lub **VideoAnalyzerPreset** . 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** umożliwia wyodrębnienie wielu audio szczegółowych informacji z pliku audio i wideo. Dane wyjściowe zawiera plik JSON (z wszystkich szczegółowych danych) i VTT w pliku audio zapis. To ustawienie wstępne akceptuje właściwość, która określa język pliku wejściowego w formie [BCP47](https://tools.ietf.org/html/bcp47) ciągu. Audio szczegółowych danych obejmują:

* Przekształcenia audio — zapisu rozmowy słów z sygnaturami czasowymi. Wiele języków są obsługiwane.
* Osoby mówiącej indeksowania — mapowania głośniki i odpowiednie rozmowy wyrazów
* Przeprowadzić analizę wskaźniki nastrojów klientów mowy — dane wyjściowe analizy wskaźniki nastrojów klientów na przekształcania audio
* Słowa kluczowe — słów kluczowych, które są wyodrębniane z przekształcania audio.

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** umożliwia wyodrębnienie wielu insights audio i wideo z pliku wideo. Dane wyjściowe zawiera plik JSON (o wszystkich szczegółowych danych), plik VTT wykaz wideo i kolekcji miniatur. To ustawienie wstępne również akceptuje [BCP47](https://tools.ietf.org/html/bcp47) string (reprezentujący język wideo) jako właściwość. Wideo szczegółowych danych obejmują wszystkie wymienione powyżej insights audio i następujące dodatkowe elementy:

* Śledzenie krój — czas, w którym znajdują się w wideo powierzchni. Każda powierzchnia ma identyfikator krój i odpowiedniej kolekcji miniatur
* Visual tekst — tekst, który zostanie wykryte za pomocą OCR. Tekst jest czas sygnaturą i również używany do wyodrębnienia słowa kluczowe (oprócz audio wykaz)
* Kluczowych — kolekcja kluczowych, które są wyodrębniane z wideo
* Visual łagodzenia zawartości — część wideo, które zostały oznaczone jako dla dorosłych lub luksusowych charakteru
* Adnotacja — wyniku adnotacji wideo oparte na modelu obiektu wstępnie zdefiniowane

##  <a name="insightsjson-elements"></a>elementy insights.JSON

Dane wyjściowe zawiera plik JSON (insights.json) z wszystkich szczegółowych danych, które zostały znalezione w wideo lub audio. Kod json może zawierać następujące elementy:

### <a name="transcript"></a>zapis

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator wiersza.|
|tekst|Wykaz, samej siebie.|
|język|Język zapis. Przeznaczony do obsługi wykaz, w którym każdy wiersz może mieć inną wersją językową.|
|wystąpienia|Lista przedziałów czasu, w której znajdowały się tego wiersza. Jeśli wystąpienie jest zapisu, będzie mieć tylko 1 wystąpieniem.|

Przykład:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>rozpoznawania

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator Rozpoznawania wiersza.|
|tekst|Tekst Rozpoznawania.|
|Zaufania|Zaufanie rozpoznawania.|
|język|Język Rozpoznawania.|
|wystąpienia|Lista przedziałów czasu, w której znajdowały się tym Rozpoznawania (tego samego Rozpoznawania może pojawić się wiele razy).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="keywords"></a>słowa kluczowe

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator — słowo kluczowe.|
|tekst|Tekst — słowo kluczowe.|
|Zaufania|Słowo kluczowe rozpoznawania zaufania.|
|język|Język — słowo kluczowe (po translacji).|
|wystąpienia|Lista przedziałów czasu, w której znajdowały się to słowo kluczowe (słowo kluczowe może pojawić się wiele razy).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 

```

### <a name="faces"></a>Kroje

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator twarzy na obrazie.|
|name|Nazwa czcionki. Może to być "Nieznany #0", zidentyfikowanych renomy lub osoby przeszkolone klienta.|
|Zaufania|Zaufanie identyfikacji twarzy na obrazie.|
|description|W przypadku renomy jego opis. |
|thumbnalId|Identyfikator miniatury napotykane przez.|
|knownPersonId|W przypadku znanych osoby, jego identyfikator wewnętrzny.|
|Identyfikator odwołania|W przypadku Bing gwiazdach ekranu, jego identyfikator Bing.|
|Typ odwołania|Obecnie tylko Bing.|
|tytuł|W przypadku renomy tytułu (na przykład "Prezes firmy Microsoft").|
|imageUrl|W przypadku renomy jej adres url obrazu.|
|wystąpienia|Są to wystąpień z którym kroju znajdowały się w danym okresie. Każde wystąpienie ma również thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="labels"></a>etykiety

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator etykiety.|
|name|Nazwa etykiety (na przykład "Komputer", "TV").|
|język|Etykieta nazwy języka (po translacji). NARZĘDZIE BCP 47|
|wystąpienia|Lista przedziałów czasu, w której znajdowały się etykieta (etykiety może pojawić się wiele razy). Każde wystąpienie ma pole zaufania. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="shots"></a>zrzuty

|Name (Nazwa)|Opis|
|---|---|
|id|Zrzut identyfikatora.|
|Klatek|Lista klatek kluczowych w zrzut (każda ma identyfikator i listę wystąpień przedziałów czasu).|
|wystąpienia|Lista zakresów czasu ten zrzut (zrzuty jest tylko 1 wystąpienie).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```


### <a name="sentiments"></a>opinie

Opinie są agregowane według ich pola sentimentType (Neutral/plus/minus). Na przykład, 0-0,1 0,2 0,1.

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator wskaźniki nastrojów klientów.|
|Średnia |Średnia wszystkie wyniki wszystkich wystąpień tego typu wskaźniki nastrojów klientów - Neutral/plus/minus|
|wystąpienia|Lista przedziałów czasu, w której znajdowały się tym wskaźniki nastrojów klientów.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Analizowanie plików wideo za pomocą usługi Azure Media Services](analyze-videos-tutorial-with-api.md)

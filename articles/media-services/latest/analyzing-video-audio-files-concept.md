---
title: Analizowanie plików audio i wideo w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Korzystając z usługi Azure Media Services, możesz analizować swoje contnet audio i wideo za pomocą AudioAnalyzerPreset i VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 9154e5d58a36bde1827d63d11d57a77b4289a781
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689370"
---
# <a name="analyzing-video-and-audio-files"></a>Analizowanie plików audio i wideo

Azure Media Services v3 umożliwia prowadzenie analiz plików audio i wideo za pomocą indeksatora wideo za pośrednictwem usługi AMS v3 analizatora ustawień wstępnych (opisanych w tym artykule). Aby uzyskać bardziej szczegółowe informacje, można korzystać bezpośrednio z usługi Video Indexer. Aby ustalić, kiedy należy używać usługi Video Indexer zamiast ustawień wstępnych analizatora usługi Media Services, zapoznaj się z [dokumentem z porównaniem](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Aby analizować zawartością za pomocą usługi Media Services v3 ustawienia, należy utworzyć **Przekształcanie** i Prześlij **zadania** który używa tych ustawień: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) lub **AudioAnalyzerPreset**. Następujący artykuł pokazuje sposób użycia **VideoAnalyzerPreset**: [Samouczek: Analizowanie klipów wideo w usłudze Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Jeśli używasz wstępnych ustawień analizatora wideo lub dźwięku, skorzystaj z witryny Azure Portal i ustaw na koncie 10 jednostek zarezerwowanych multimediów S3. Aby uzyskać więcej informacji, zobacz temat [Scale media processing (Skalowanie przetwarzania multimediów)](media-reserved-units-cli-how-to.md).

## <a name="built-in-presets"></a>Wbudowane ustawienia wstępne

Usługa Media Services obsługuje obecnie następujące ustawienia analizatora wbudowane:  

|**Nazwa ustawienia wstępnego**|**Scenariusz**|**Szczegóły**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analiza audio|Ustawienie ma zastosowanie wstępnie zdefiniowanego zestawu analiz oparte na sztucznej Inteligencji operacje, w tym transkrypcja mowy. Obecnie usługa ustawienia wstępnego obsługuje przetwarzania zawartości za pomocą pojedynczej ścieżki audio, który zawiera mowy w jednym języku. Możesz określić język dla ładunku audio w danych wejściowych w formacie BCP 47 "region tagu języka". Obsługiwane języki są angielski ("en US" i "en-GB"), hiszpański ("es-ES" i "es-MX"), włoski ("it-IT"), niemiecki ("de-DE"), japoński ("ja-JP"), chiński ("zh-CN"), francuski ("fr-FR"), portugalski ("pt-BR"), arabski ("ar — na przykład"), rosyjski ("ru-RU"), Hindi ("cześć IN" ) i koreańskiego ("ko-KR").<br/><br/> Jeśli język nie jest określony lub ustawiony na język o wartości null, automatyczne wykrywanie będzie wybierz pierwszy język wykryte i przetwarzania w czasie trwania pliku przy użyciu wybranego języka. Funkcja wykrywania automatycznego języka obsługuje obecnie angielski, chiński, francuski, niemiecki, włoski, japoński, hiszpański, rosyjski i portugalski. Go nie obsługuje obecnie dynamicznie przełączania się między językami po wykryciu pierwszy język. Funkcja wykrywania automatycznego działa najlepiej z nagrania audio, za pomocą wyraźnie zauważalny mowy. W przypadku niepowodzenia automatycznego wykrywania języka można znaleźć języka transkrypcji powróci do języka angielskiego.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analiza audio i wideo|Wyodrębnia informacje (obszerne metadane) z audio i wideo, a następnie generuje plik formatu JSON. Można określić, czy mają zostać wyodrębnione audio insights podczas przetwarzania pliku wideo. Aby uzyskać więcej informacji, zobacz [analiza wideo](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Wykrywanie twarzy w filmie wideo|W tym artykule opisano ustawienia, które ma być używany podczas analizowania pliku wideo w celu wykrycia wszystkich powierzchni obecne.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

Ustawienia wstępnego, można tworzyć wiele analizy audio z pliku audio lub wideo. Dane wyjściowe obejmują plik JSON (z wszystkie szczegółowe informacje), a plik VTT transkrypcjach audio. To ustawienie wstępne akceptuje właściwość, która określa język pliku wejściowego w formie [BCP47](https://tools.ietf.org/html/bcp47) ciągu. Audio szczegółowe informacje obejmują:

* Transkrypcję audio — transkrypcji wypowiadanych słów z sygnaturami czasowymi. Obsługuje wiele języków
* Osoby mówiącej indeksowania — mapowanie prelegentów i odpowiednie wypowiadanych słów
* Analiza tonacji mowy — dane wyjściowe analiza tonacji, wykonywane na transkrypcję audio
* Słowa kluczowe — słów kluczowych, które są wyodrębniane z transkrypcję audio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Ustawienia wstępnego, można tworzyć wiele analizy audio i wideo z pliku wideo. Dane wyjściowe obejmują plik w formacie JSON (z wszystkie szczegółowe informacje), plik VTT dla transkrypcji wideo i kolekcji miniatur. To ustawienie wstępne akceptuje także [BCP47](https://tools.ietf.org/html/bcp47) reprezentującym (język wideo) jako właściwość. Informacjom o filmie wideo obejmują wszystkie audio, wymienionych powyżej szczegółowe informacje i dodatkowe następujące elementy:

* Śledzenie twarzy — czas, w którym znajdują się w wideo twarzy. Każdej twarzy ma identyfikator twarzy i odpowiednią kolekcję miniatur
* Wizualne tekst — tekst, który zostanie wykryte za pomocą funkcji optycznego rozpoznawania znaków. Tekst jest czas sygnaturę i umożliwia również wyodrębnianie słów kluczowych (oprócz transkrypcjach audio)
* Klatki kluczowe — kolekcja kluczowych klatek, które są wyodrębniane z wideo
* Moderowanie zawartości Visual — część filmów wideo, które zostały oznaczone jako treści dla dorosłych lub erotycznej z natury
* Adnotacja — wynik elementu Dodawanie adnotacji do filmów wideo na podstawie modelu wstępnie zdefiniowanych obiektów

##  <a name="insightsjson-elements"></a>elementy insights.JSON

Dane wyjściowe obejmują plik w formacie JSON (insights.json) przy użyciu wszystkie szczegółowe informacje, które zostały znalezione w wideo lub audio. Za pomocą pliku json może zawierać następujące elementy:

### <a name="transcript"></a>transkrypcji

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator wiersza.|
|tekst|Transkrypcja sam.|
|language|Język transkrypcji. Przeznaczony do obsługi transkrypcji, w którym każdy wiersz może mieć inny język.|
|wystąpienia|Lista zakresów czasu, w którym znajdowały się tego wiersza. Jeśli wystąpienie jest transkrypcji, będzie miał tylko 1 wystąpienia.|

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

### <a name="ocr"></a>optyczne rozpoznawanie znaków

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator wiersza optyczne rozpoznawanie znaków|
|tekst|Optyczne rozpoznawanie znaków tekstu.|
|zaufania|Rozpoznawanie zaufania.|
|language|Język optyczne rozpoznawanie znaków.|
|wystąpienia|Lista zakresów czasu, w którym znajdowały się tym optyczne rozpoznawanie znaków (OCR tego samego mogą pojawiać się wielokrotnie).|

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

### <a name="faces"></a>twarzy

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator twarzy.|
|name|Nazwa czcionki. Może to być "Nieznany #0", osobistości zidentyfikowanych lub osoby uczonego klienta.|
|zaufania|Zaufanie identyfikacji twarzy.|
|description|Opis osobistości. |
|thumbnailId|Identyfikator miniatury napotykane przez.|
|knownPersonId|Jeśli jest znane osoby wewnętrzny identyfikator.|
|referenceId|Jeśli jest Bing rozpoznawanie osobistości, jego identyfikator Bing.|
|Element referenceType|Obecnie tylko Bing.|
|title|Jeśli jest rozpoznawanie osobistości, jego tytuł (na przykład "Prezes firmy Microsoft").|
|imageUrl|Jeśli jest rozpoznawanie osobistości, jej adres url obrazu.|
|wystąpienia|Są to wystąpienia elementu gdzie twarz znajdowały się w danym okresie. Każde wystąpienie ma również thumbnailsId. |

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

### <a name="shots"></a>zrzuty

|Name (Nazwa)|Opis|
|---|---|
|id|Zrzut identyfikatora.|
|keyFrames|Lista klatek kluczowych w obrębie zrzut (każda ma identyfikator i listę zakresów czasu wystąpienia). Klatki kluczowe wystąpienia ma pola thumbnailId ze miniaturę ramki kluczowej identyfikatora.|
|wystąpienia|Lista zakresy czasu to zrzut (zrzuty mają tylko 1 wystąpienie).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
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
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statystyki

|Name (Nazwa)|Opis|
|---|---|
|CorrespondenceCount|Liczba korelacji w trakcie filmu wideo.|
|WordCount|Liczba słów na osoby mówiącej.|
|SpeakerNumberOfFragments|Ilość fragmenty osoby mówiącej ma w filmie wideo.|
|SpeakerLongestMonolog|Najdłuższy produktu monolog osoby mówiącej. Jeśli osoby mówiącej silences wewnątrz produktu monolog jest dołączony. Wyciszenia na początku i końcu produktu monolog zostaną usunięte.| 
|SpeakerTalkToListenRatio|Obliczanie opiera się na czas spędzony na produktu monolog osoby mówiącej (bez wyciszenia pomiędzy) podzielony przez łącznego czasu filmu wideo. Czas jest zaokrąglana do trzeciego punktu dziesiętnego.|


### <a name="sentiments"></a>tonacje

Opinie są agregowane według ich pola sentimentType (neutralna/plus/minus). Na przykład 0-0.1, 0.2 0,1.

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator opinii.|
|Średnia |Średnia wszystkie wyniki wszystkich wystąpień tego typu tonacji - neutralna/plus/minus|
|wystąpienia|Lista zakresów czasu, w których pojawiły się tym wskaźniki nastrojów klientów.|
|sentimentType |Typ może być "Pozytywny", "Neutralnym" lub "Ujemne".|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>etykiety

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator etykiety.|
|name|Nazwa etykiety (na przykład "Computer", "TV").|
|language|Etykieta nazwy język (w przypadku przetłumaczona). BCP-47|
|wystąpienia|Lista zakresów czasu, w których pojawiły się tej etykiety (etykieta może wystąpić wiele razy). Każde wystąpienie ma pole zaufania. |


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

### <a name="keywords"></a>słowa kluczowe

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator — słowo kluczowe.|
|tekst|Tekst — słowo kluczowe.|
|zaufania|Słowo kluczowe rozpoznawania zaufania.|
|language|Język — słowo kluczowe (w przypadku tłumaczonym).|
|wystąpienia|Lista zakresów czasu, w którym znajdowały się to słowo kluczowe (słowo kluczowe może pojawić się wiele razy).|

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

#### <a name="visualcontentmoderation"></a>visualContentModeration

Blok visualContentModeration zawiera zakresy czasu, które indeksator wideo odnaleziono potencjalnie mieć treści dla dorosłych. VisualContentModeration jest pusta, czy nie zawartości dla dorosłych, która została zidentyfikowana.

Filmy wideo, które znajdują się na zawierać treści dla dorosłych lub erotycznej może być dostępna dla prywatnych tylko w widoku. Użytkownicy mają możliwość złożyć wniosek o dokonywanie przeglądu zawartości, w których przypadku atrybutu IsAdult będzie zawierać wynik przeglądu przez ludzi.

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator visual moderowanie zawartości.|
|adultScore|Treści dla dorosłych wynik (od pakietu content moderator).|
|racyScore|Erotyka — ocena (z moderowanie zawartości).|
|wystąpienia|Lista zakresów czasu, w których pojawiły się to visual moderowanie zawartości.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>Kolejne kroki

[Samouczek: Analizowanie wideo za pomocą usługi Azure Media Services](analyze-videos-tutorial-with-api.md)

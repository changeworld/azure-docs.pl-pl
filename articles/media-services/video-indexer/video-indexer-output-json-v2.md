---
title: Sprawdź dane wyjściowe indeksatora wideo produkowane przez interfejs API w wersji 2 — Azure
titleSuffix: Azure Media Services
description: W tym temacie sprawdza dane wyjściowe indeksatora wideo usługi Azure Media Services produkowane przez interfejs API w wersji 2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/09/2019
ms.author: juliako
ms.openlocfilehash: 2fac5e07f9646c4fc0fac7b1be53b5a5ac1ea803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245930"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>Sprawdź dane wyjściowe indeksatora wideo wytwarzane przez interfejs API

Po wywołaniu **Get Video Index** INTERFEJSU API i stan odpowiedzi jest OK, otrzymasz szczegółowe dane wyjściowe JSON jako zawartość odpowiedzi. Zawartość JSON zawiera szczegóły określonych szczegółowych informacji wideo. Spostrzeżenia obejmują: transkrypcje, KTZ, twarze, tematy, bloki itp. Każdy typ wglądu zawiera wystąpienia zakresów czasu, które pokazują, gdy wgląd pojawia się w filmie. 

1. Aby pobrać plik JSON, zadzwoń [do interfejsu API pobierz indeks wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. Jeśli jesteś również zainteresowany określonymi artefaktami, zadzwoń [do interfejsu API adresu URL pobierania artefaktów wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    W wywołaniu interfejsu API określ żądany typ artefaktu (OCR, Twarze, Klatki kluczowe itp.)

Możesz również wizualnie sprawdzić podsumowane statystyki filmu, naciskając przycisk **Odtwórz** na wideo w witrynie [indeksatora wideo.](https://www.videoindexer.ai/) Aby uzyskać więcej informacji, zobacz [Wyświetlanie i edytowanie statystyk wideo](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

W tym artykule ocieka zawartość JSON zwróconą przez interfejs API **Pobierz indeks wideo.** 

> [!NOTE]
> Wygaśnięcie wszystkich tokenów dostępu w indeksatorze wideo wynosi jedną godzinę.


## <a name="root-elements"></a>Elementy główne

|Nazwa|Opis|
|---|---|
|accountId|Identyfikator konta VI listy odtwarzania.|
|id|Identyfikator listy odtwarzania.|
|name|Nazwa listy odtwarzania.|
|description|Opis listy odtwarzania.|
|userName|Nazwa użytkownika, który utworzył listę odtwarzania.|
|Utworzone|Czas tworzenia listy odtwarzania.|
|tryb prywatności|Tryb prywatności listy odtwarzania (prywatny/publiczny).|
|state|Lista odtwarzania (przekazana, przetwarzająca, przetwarzana, nie powiodła się, poddana kwarantannie).|
|isOwned|Wskazuje, czy lista odtwarzania została utworzona przez bieżącego użytkownika.|
|Iseditable|Wskazuje, czy bieżący użytkownik jest upoważniony do edytowania listy odtwarzania.|
|isBase (Baza danych)|Wskazuje, czy lista odtwarzania jest podstawową listą odtwarzania (filmem), czy z listy odtwarzania z innych filmów (pochodnych).|
|durationInSeconds|Całkowity czas trwania listy odtwarzania.|
|podsumowanieInsights|Zawiera jedną [sumęInsights](#summarizedinsights).
|wideo|Lista [filmów](#videos) konstruujących listę odtwarzania.<br/>Jeśli ta lista odtwarzania skonstruowanych zakresów czasu innych filmów (pochodnych), filmy na tej liście będą zawierać tylko dane z uwzględnionych zakresów czasu.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>podsumowanieInsights

W tej sekcji przedstawiono podsumowanie szczegółowych informacji.

|Atrybut | Opis|
|---|---|
|name|Nazwa filmu. Na przykład azure monitor.|
|id|Identyfikator filmu. Na przykład 63c6d532ff.|
|tryb prywatności|Podział może mieć jeden z następujących trybów: **Prywatny,** **Publiczny**. **Public** - film jest widoczny dla wszystkich osób na Twoim koncie i dla każdego, kto ma link do filmu. **Prywatny** - film jest widoczny dla wszystkich na Twoim koncie.|
|czas trwania|Zawiera jeden czas trwania, który opisuje czas wystąpienia wglądu. Czas trwania jest w sekundach.|
|thumbnailVideoId|Identyfikator filmu, z którego pobrano miniaturę.
|thumbnailId|Identyfikator miniatury filmu. Aby uzyskać rzeczywistą miniaturę, zadzwoń [do Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) i przekaż jej miniaturęFilm i thumbnailId.|
|Twarze|Może zawierać zero lub więcej ścian. Aby uzyskać bardziej szczegółowe informacje, zobacz [twarze](#faces).|
|keywords (słowa kluczowe)|Może zawierać zero lub więcej słów kluczowych. Aby uzyskać bardziej szczegółowe informacje, zobacz [słowa kluczowe](#keywords).|
|Uczucia|Może zawierać zero lub więcej tonacji. Aby uzyskać bardziej szczegółowe informacje, zobacz [opinie](#sentiments).|
|efekty dźwiękowe| Może zawierać zero lub więcej efektów dźwiękowych. Aby uzyskać bardziej szczegółowe informacje, zobacz [audioEffects](#audioEffects).|
|Etykiety| Może zawierać zero lub więcej etykiet. Aby uzyskać więcej informacji, zobacz [etykiety](#labels).|
|Marek| Może zawierać zero lub więcej marek. Aby uzyskać bardziej szczegółowe informacje, zobacz [marki](#brands).|
|statystyki | Aby uzyskać bardziej szczegółowe informacje, zobacz [statystyki](#statistics).|
|Emocje| Może zawierać zero lub więcej emocji. Aby uzyskać bardziej szczegółowe informacje, zobacz [emocje](#emotions).|
|Tematy|Może zawierać zero lub więcej tematów. Informacje [na tematach.](#topics)|

## <a name="videos"></a>wideo

|Nazwa|Opis|
|---|---|
|accountId|Identyfikator konta VI filmu.|
|id|Identyfikator filmu.|
|name|Nazwa filmu.
|state|Stan filmu (przesłany, przetwarzanie, przetworzenie, nie powiodło się, poddane kwarantannie).|
|processingProgress (przetwarzanieProgress)|Postęp przetwarzania podczas przetwarzania (na przykład 20%).|
|błądKod|Kod błędu, jeśli nie można przetworzyć (na przykład "UnsupportedFileType").|
|awariaNastarczanie|Komunikat o błędzie, jeśli nie można przetworzyć.|
|identyfikator zewnętrzny|Zewnętrzny identyfikator filmu (jeśli jest określony przez użytkownika).|
|Externalurl|Zewnętrzny adres URL filmu (jeśli jest określony przez użytkownika).|
|metadane|Zewnętrzne metadane filmu (jeśli są określone przez użytkownika).|
|isAdult ( isAdult )|Wskazuje, czy film został ręcznie zweryfikowany i zidentyfikowany jako film dla dorosłych.|
|Spostrzeżenia|Obiekt insights. Aby uzyskać więcej informacji, zobacz [szczegółowe informacje](#insights).|
|thumbnailId|Identyfikator miniatury filmu. Aby uzyskać rzeczywistą miniaturę wywołania [Pobierz miniaturę](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) i przekazać ją identyfikatorem wideo i identyfikatorem miniatur.|
|opublikowanoUrl|Adres URL do przesyłania strumieniowego wideo.|
|opublikowanoUrlProxy|Adres URL do przesyłania strumieniowego wideo z (dla urządzeń Apple).|
|viewToken (Widok)|Token widoku krótkotrwałego do przesyłania strumieniowego wideo.|
|źródłoLanguage|Język źródłowy filmu.|
|language|Rzeczywisty język filmu (tłumaczenie).|
|indexingPreset|Ustawienie wstępne używane do indeksu wideo.|
|streamingPreset|Preset używany do publikowania wideo.|
|linguisticModelId|Model CRIS używany do transkrypcji wideo.|
|statystyki | Aby uzyskać więcej informacji, zobacz [statystyki](#statistics).|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>Spostrzeżenia

Każdy wgląd (na przykład wiersze transkrypcji, twarze, marki itp.) zawiera listę unikatowych elementów (na przykład face1, face2, face3), a każdy element ma własne metadane i listę jego wystąpień (które są zakresami czasu z dodatkowymi opcjonalnymi metadanymi).

Ściana może mieć identyfikator, nazwę, miniaturę, inne metadane i listę jej wystąpień czasowych (na przykład: 00:00:05 – 00:00:10, 00:01:00 – 00:02:30 i 00:41:21 – 00:41:49.) Każde wystąpienie czasowe może mieć dodatkowe metadane. Na przykład współrzędne prostokąta ściany (20,230,60,60).

|Wersja|Wersja kodu|
|---|---|
|źródłoLanguage|Język źródłowy filmu (przy założeniu jednego języka głównego). W postaci ciągu [BCP-47.](https://tools.ietf.org/html/bcp47)|
|language|Język szczegółowych informacji (przetłumaczony z języka źródłowego). W postaci ciągu [BCP-47.](https://tools.ietf.org/html/bcp47)|
|Zapis|Wgląd [transkrypcji.](#transcript)|
|Ocr|Wgląd [OCR.](#ocr)|
|keywords (słowa kluczowe)|Informacje [o słowach kluczowych.](#keywords)|
|bloki|Może zawierać jeden lub więcej [bloków](#blocks)|
|Twarze|Wgląd [w twarze.](#faces)|
|Etykiety|Wgląd [w etykiety.](#labels)|
|Zdjęć|Wgląd [strzałów.](#shots)|
|Marek|Wgląd [w marki.](#brands)|
|efekty dźwiękowe|[AudioEffects](#audioEffects) wgląd.|
|Uczucia|Wgląd [sentymentów.](#sentiments)|
|visualContentModeration|[Wgląd w wygląd visualContentModeration.](#visualcontentmoderation)|
|textualContentModeration|[TextualContentModeration](#textualcontentmoderation) wgląd.|
|Emocje| Wgląd [emocji.](#emotions)|
|Tematy|Informacje [na tematach.](#topics)|

Przykład:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>bloki

Atrybut | Opis
---|---
id|Identyfikator bloku.|
Wystąpień|Lista zakresów czasu tego bloku.|

#### <a name="transcript"></a>Zapis

|Nazwa|Opis|
|---|---|
|id|Identyfikator wiersza.|
|tekst|Sam zapis.|
|language|Język transkrypcji. Przeznaczone do obsługi transkrypcji, gdzie każdy wiersz może mieć inny język.|
|Wystąpień|Lista zakresów czasu, w których pojawiła się ta linia. Jeśli wystąpienie jest transkrypcja, będzie miał tylko 1 wystąpienie.|

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

#### <a name="ocr"></a>Ocr

|Nazwa|Opis|
|---|---|
|id|Identyfikator linii OCR.|
|tekst|Tekst OCR.|
|ufność|Zaufanie do uznania.|
|language|Język OCR.|
|Wystąpień|Lista zakresów czasu, w których pojawił się ten OCR (ten sam OCR może pojawić się wiele razy).|
|height|Wysokość prostokąta OCR|
|top|Górna lokalizacja w px|
|left| Lewa lokalizacja w px|
|szerokość|Szerokość prostokąta OCR|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>keywords (słowa kluczowe)

|Nazwa|Opis|
|---|---|
|id|Identyfikator słowa kluczowego.|
|tekst|Tekst słowa kluczowego.|
|ufność|Zaufanie do rozpoznawania słowa kluczowego.|
|language|Język słowa kluczowego (po przetłumaczeniu).|
|Wystąpień|Lista zakresów czasu, w których pojawiło się to słowo kluczowe (słowo kluczowe może pojawić się wiele razy).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="faces"></a>Twarze

|Nazwa|Opis|
|---|---|
|id|Identyfikator twarzy.|
|name|Nazwa twarzy. Może to być "Nieznany #0, zidentyfikowane sławna lub osoba przeszkolona klienta.|
|ufność|Pewność identyfikacji twarzy.|
|description|Opis celebrytki. |
|thumbnailId|Identyfikator miniatury tej twarzy.|
|knownPersonId|Jeśli jest to znana osoba, jej wewnętrzny identyfikator.|
|referenceId|Jeśli jest to gwiazda Bing, jej Bing ID.|
|typ odwołania|Obecnie tylko Bing.|
|title|Jeśli jest sławna, jej tytuł (na przykład "Ceo Microsoft").|
|Imageurl|Jeśli jest sławna, jej url obrazu.|
|Wystąpień|Są to przypadki, w których twarz pojawiła się w danym przedziale czasowym. Każde wystąpienie ma również thumbnailsId. |

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

#### <a name="labels"></a>Etykiety

|Nazwa|Opis|
|---|---|
|id|Identyfikator etykiety.|
|name|Nazwa etykiety (na przykład "Komputer", "TV").|
|language|Język nazwy etykiety (po przetłumaczeniu). BCP-47|
|Wystąpień|Lista zakresów czasu, w których pojawiła się ta etykieta (etykieta może pojawić się wiele razy). Każde wystąpienie ma pole zaufania. |


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

#### <a name="scenes"></a>Sceny

|Nazwa|Opis|
|---|---|
|id|Identyfikator sceny.|
|Wystąpień|Lista zakresów czasu tej sceny (scena może mieć tylko 1 wystąpienie).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>Zdjęć

|Nazwa|Opis|
|---|---|
|id|Identyfikator strzału.|
|Klatek kluczowych|Lista klatek kluczowych w kadrze (każda z nich ma identyfikator i listę zakresów czasu wystąpień). Każde wystąpienie elementu keyFrame ma pole thumbnailId, które zawiera identyfikator miniatury klatki kluczowej.|
|Wystąpień|Lista zakresów czasowych tego strzału (strzał może mieć tylko 1 instancję).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>Marek

Nazwy firm i produktów wykryte w transkrypcji mowy na tekst i/lub wideo oCR. Nie obejmuje to wizualnego rozpoznawania marek ani wykrywania logo.

|Nazwa|Opis|
|---|---|
|id|Identyfikator marki.|
|name|Nazwa marki.|
|referenceId | Przyrostek adresu URL wikipedii marki. Na przykład "Target_Corporation" jest przyrostkiem . [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)
|referenceUrl | Adres URL Wikipedii marki, jeśli istnieje. Na przykład [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description|Opis marki.|
|tags|Lista wstępnie zdefiniowanych tagów skojarzonych z tą marką.|
|ufność|Wartość zaufania detektora marki Video Indexer (0-1).|
|Wystąpień|Lista zakresów czasowych tej marki. Każde wystąpienie ma markęType, która wskazuje, czy ta marka pojawiła się w transkrypcji lub w OCR.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statystyki

|Nazwa|Opis|
|---|---|
|Konto korespondencyjne|Liczba korespondencji w filmie.|
|Liczba głośników|Liczba słów na mówcę.|
|GłośnikNumberOfFragments|Ilość fragmentów, które głośnik ma w filmie.|
|GłośnikLongestMonolog|Najdłuższy monolog prelegenta. Jeśli głośnik ma wyciszenia wewnątrz monologu jest on dołączony. Cisza na początku i koniec monologu jest usuwany.| 
|GłośnikTalkToListenRatio|Obliczenia są oparte na czasie spędzonym na monologu prelegenta (bez ciszy pomiędzy) podzielonym przez całkowity czas filmu. Czas jest zaokrąglany do trzeciego przecinka dziesiętnego.|

#### <a name="audioeffects"></a><a id="audioEffects"/>efekty dźwiękowe

|Nazwa|Opis|
|---|---|
|id|Identyfikator efektu audio.|
|type|Typ efektu audio (na przykład Klaskanie, Mowa, Wyciszenie).|
|Wystąpień|Lista zakresów czasu, w których pojawił się ten efekt dźwiękowy.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>Uczucia

Nastroje są agregowane według ich sentimentType pola (dodatnie/neutralne/ujemne). Na przykład 0-0.1, 0.1-0.2.

|Nazwa|Opis|
|---|---|
|id|Identyfikator sentymentu.|
|średniaSkus |Średnia wszystkich wyników wszystkich wystąpień tego typu tonacji - Dodatnia/Neutralna/Ujemna|
|Wystąpień|Lista zakresów czasu, w których pojawił się ten sentyment.|
|sentimentType |Typ może być "Dodatni", "Neutralny" lub "Ujemny".|

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

#### <a name="visualcontentmoderation"></a>visualContentModeration

VisualContentModeration blok zawiera zakresy czasu, które Indeksator wideo znaleźć potencjalnie treści dla dorosłych. Jeśli visualContentModeration jest pusta, nie ma żadnych treści dla dorosłych, który został zidentyfikowany.

Filmy, które zawierają treści dla dorosłych lub treści rasistowskich, mogą być dostępne tylko do wyświetlania prywatnego. Użytkownicy mają możliwość przesłania żądania ludzkiego przeglądu treści, w którym to przypadku atrybut IsAdult będzie zawierał wynik przeglądu ludzkiego.

|Nazwa|Opis|
|---|---|
|id|Identyfikator moderowania zawartości wizualnej.|
|adultScore (dorosłyScore)|Wynik dla dorosłych (od moderatora treści).|
|racyScore (racyScore)|Racy wynik (z moderacji treści).|
|Wystąpień|Lista zakresów czasu, w których pojawiła się ta umiar zawartości wizualnej.|

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

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Nazwa|Opis|
|---|---|
|id|Tekstowa identyfikator moderacji zawartości.|
|bannedWordsCount |Liczba zakazanych słów.|
|bannedWordsRatio |Stosunek od całkowitej liczby słów.|

#### <a name="emotions"></a>Emocje

Indeksator wideo identyfikuje emocje na podstawie sygnałów mowy i dźwięku. Zidentyfikowane emocje mogą być: radość, smutek, gniew lub strach.

|Nazwa|Opis|
|---|---|
|id|Identyfikator emocji.|
|type|Moment emocji, który został zidentyfikowany na podstawie mowy i sygnałów dźwiękowych. Emocja może być: radość, smutek, gniew lub strach.|
|Wystąpień|Lista przedziałów czasowych, w których pojawiły się te emocje.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>Tematy

Indeksator wideo sprawia, że wnioskowanie głównych tematów z transkrypcji. Jeśli to możliwe, w cenę zakwaterowania wliczono taksonomię [IPTC](https://iptc.org/standards/media-topics/) drugiego poziomu. 

|Nazwa|Opis|
|---|---|
|id|Identyfikator tematu.|
|name|Nazwa tematu, na przykład: "Farmaceutyki".|
|referenceId|Pokrewna nawigarze odzwierciedlająca hierarchię tematów. Na przykład: "Zdrowie i dobre samopoczucie / Medycyna i opieka zdrowotna / Farmaceutyki".|
|ufność|Wynik zaufania w zakresie [0,1]. Wyższy jest bardziej pewny siebie.|
|language|Język używany w temacie.|
|nazwa iptcName|Nazwa kodu nośnika IPTC, jeśli zostanie wykryta.|
|Wystąpień |Obecnie indeksator wideo nie indeksuje tematu do interwałów czasowych, więc cały film jest używany jako interwał.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>Następne kroki

[Portal deweloperów indeksatora wideo](https://api-portal.videoindexer.ai)

Aby uzyskać informacje na temat osadzania widżetów w aplikacji, zobacz [Osadzanie widżetów indeksatora wideo w aplikacjach](video-indexer-embed-widgets.md). 


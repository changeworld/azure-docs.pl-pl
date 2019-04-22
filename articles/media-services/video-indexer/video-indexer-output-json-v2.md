---
title: Zbadanie danych wyjściowych indeksatora wideo utworzone przez interfejs API w wersji 2
titlesuffix: Azure Media Services
description: W tym temacie analizuje danych wyjściowych indeksatora wideo utworzone przez interfejs API w wersji 2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/07/2019
ms.author: juliako
ms.openlocfilehash: 91cd8ab0565279f88a0949f873d6e44d564427af
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280217"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>Zbadanie danych wyjściowych indeksatora wideo utworzone przez interfejs API

Gdy wywołujesz **uzyskać indeks wideo** interfejsu API i stan odpowiedzi jest OK, Uzyskaj szczegółowe dane wyjściowe JSON jako treść odpowiedzi. Zawartość JSON zawiera szczegółowe informacje o określonym informacjom o filmie wideo. Szczegółowe informacje zawierają wymiary, takie jak: zapisy, OCRs, twarzy, tematy, bloki itp. Wymiary są wystąpienia zakresy czasu, przedstawiających podczas każdego wymiaru pojawiły się w trakcie filmu wideo.  

Można również wizualnie sprawdzić insights podsumowań wideo, naciskając klawisz **Odtwórz** znajdujący się na film wideo na [Video Indexer](https://www.videoindexer.ai/) witryny sieci Web. Aby uzyskać więcej informacji, zobacz [wyświetlanie i edytowanie informacji szczegółowych wideo](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

W tym artykule sprawdza, czy zawartość JSON zwróconą przez **uzyskać indeks wideo** interfejsu API. 

> [!NOTE]
> Czas wygaśnięcia tokenów dostępu w Video Indexer jest jedną godzinę.


## <a name="root-elements"></a>Elementy główne

|Name (Nazwa)|Opis|
|---|---|
|accountId|Identyfikatorem konta VI listy odtwarzania|
|id|Identyfikator listy odtwarzania.|
|name|Nazwa listy odtwarzania.|
|description|Opis listy odtwarzania.|
|userName|Nazwa użytkownika, który utworzył listę odtwarzania.|
|utworzone|Godzina utworzenia listy odtwarzania.|
|privacyMode|Lista odtwarzania trybie prywatności (Private/Public).|
|state|Lista odtwarzania (przekazany, przetwarzania, przetwarzania, nie powiodło się; poddane kwarantannie).|
|isOwned|Wskazuje, czy lista została utworzona przez bieżącego użytkownika.|
|iseditable —|Wskazuje, czy bieżący użytkownik jest autoryzowany do edytowania listy odtwarzania.|
|isBase|Wskazuje, czy lista odtwarzania jest podstawowy listy odtwarzania (wideo) lub listę odtwarzania wykonane z innych plików wideo (derived).|
|durationInSeconds|Łączny czas trwania listy odtwarzania.|
|summarizedInsights|Zawiera jeden [summarizedInsights](#summarizedinsights).
|wideo|Lista [wideo](#videos) konstruowania listy odtwarzania.<br/>Jeśli ta lista odtwarzania z skonstruowany z zakresy czasu (derived) inne filmy wideo, filmów wideo na tej liście będzie zawierać tylko dane z zakresów czasu uwzględnione.|

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

## <a name="summarizedinsights"></a>summarizedInsights

W tej sekcji przedstawiono podsumowanie szczegółowych danych.

|Atrybut | Opis|
|---|---|
|name|Nazwa filmu wideo. Na przykład usługa Azure Monitor.|
|id|Identyfikator filmu wideo. Na przykład 63c6d532ff.|
|privacyMode|Podział usługi może mieć jedną z następujących trybów: **Prywatne**, **publicznych**. **Publiczne** — film wideo jest widoczny dla wszystkich użytkowników w swoje konto i każda osoba, która zawiera link do filmu wideo. **Prywatne** — film wideo jest widoczny dla wszystkich użytkowników na Twoim koncie.|
|czas trwania|Zawiera jeden czas trwania, opisujący godzina wystąpienia w szczegółowe informacje. Czas trwania to w ciągu kilku sekund.|
|thumbnailVideoId|Identyfikator wideo, z których pochodzi się miniaturę.
|thumbnailId|Miniatura wideo identyfikatora. Aby uzyskać rzeczywiste miniatury, należy wywołać [miniaturę Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) i przekaż go thumbnailVideoId i thumbnailId.|
|twarzy|Może zawierać zero lub więcej twarzy. Aby uzyskać więcej informacji, zobacz [twarzy](#faces).|
|słowa kluczowe|Może zawierać zero lub więcej słów kluczowych. Aby uzyskać więcej informacji, zobacz [słowa kluczowe](#keywords).|
|tonacje|Może zawierać zero lub więcej tonacji. Aby uzyskać więcej informacji, zobacz [tonacji](#sentiments).|
|audioEffects| Może zawierać zero lub więcej audioEffects. Aby uzyskać więcej informacji, zobacz [audioEffects](#audioEffects).|
|etykiety| Może zawierać zero lub więcej etykiet. Aby uzyskać szczegółowe więcej informacji, zobacz [etykiety](#labels).|
|marek| Może zawierać zero lub więcej marek. Aby uzyskać więcej informacji, zobacz [marek](#brands).|
|statystyki | Aby uzyskać więcej informacji, zobacz [statystyki](#statistics).|
|emocji| Może zawierać zero lub więcej emocji. Aby uzyskać więcej informacji, zobacz [emocji](#emotions).|
|Tematy|Może zawierać zero lub więcej tematów. [Tematy](#topics) wymiaru.|

## <a name="videos"></a>wideo

|Name (Nazwa)|Opis|
|---|---|
|accountId|Identyfikatorem konta VI wideo|
|id|Identyfikator wideo.|
|name|Nazwa filmu wideo.
|state|Stan wideo (przekazany, przetwarzania, przetwarzania, nie powiodło się; poddane kwarantannie).|
|processingProgress|Postęp przetwarzania podczas przetwarzania (np. 20%).|
|Kod błędu|Kod błędu, jeśli nie przetworzył (na przykład "UnsupportedFileType").|
|failureMessage|Komunikat o błędzie, jeśli nie można przetworzyć.|
|externalId|Identyfikator zewnętrzny wideo (jeśli jest określony przez użytkownika).|
|externalUrl|Wideo zewnętrznego adresu url (jeśli jest określony przez użytkownika).|
|metadane|Wideo metadane zewnętrznych (jeśli jest określony przez użytkownika).|
|isAdult|Wskazuje, czy wideo został ręcznie przejrzeć i zidentyfikowane jako przeznaczonej dla osób dorosłych wideo.|
|szczegółowe informacje|Obiekt szczegółowych informacji. Aby uzyskać więcej informacji, zobacz [insights](#insights).|
|thumbnailId|Miniatura wideo identyfikatora. Aby uzyskać rzeczywiste wywołanie miniatury [miniaturę Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) i przekaż go, identyfikator i thumbnailId wideo.|
|publishedUrl|Adres url do przesyłania strumieniowego wideo.|
|publishedUrlProxy|Adres url do przesyłania strumieniowego wideo z (dla urządzeń firmy Apple).|
|viewToken|Token krótkotrwałe widoku służącą do strumieniowego przesyłania wideo.|
|sourceLanguage|Język źródłowy klip wideo.|
|language|Rzeczywiste język wideo (tłumaczenia).|
|indexingPreset|Ustawienie wstępne, używane do indeksowania filmu wideo.|
|streamingPreset|Ustawienie wstępne, używany do publikowania filmu wideo.|
|linguisticModelId|Model CRI umożliwia także filmu wideo.|
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
### <a name="insights"></a>szczegółowe informacje

Szczegółowe informacje są zestawem wymiarów (na przykład linii transkrypcji, twarzy, marek itp.), gdzie każdy wymiar jest listę unikatowych elementów (na przykład face1, pierwszy plan 2, pierwszy plan 3), a każdy element ma swój własny metadanych i listę jego wystąpienia (które są zakresy czasu z dodatkowe opcjonalne metadane).

Krój może mieć identyfikator, nazwę, miniatury, inne metadane i listę swoich wystąpień danych czasowych (na przykład: 00: 00:05 — 00:00:10, 00:01:00-00:02:30 i 00:41:21 – 00:41:49.) Każde wystąpienie danych czasowych może mieć dodatkowe metadane. Na przykład obrys twarzy współrzędne (20,230,60,60).

|Wersja|Wersja kodu|
|---|---|
|sourceLanguage|Język źródłowy wideo (przy założeniu jeden język master). W formie [BCP 47](https://tools.ietf.org/html/bcp47) ciągu.|
|language|Język insights (tłumaczenia go z języka źródłowego). W formie [BCP 47](https://tools.ietf.org/html/bcp47) ciągu.|
|transkrypcji|[Transkrypcji](#transcript) wymiaru.|
|optyczne rozpoznawanie znaków|[Optyczne rozpoznawanie znaków](#ocr) wymiaru.|
|słowa kluczowe|[Słowa kluczowe](#keywords) wymiaru.|
|bloki|Może zawierać jeden lub więcej [bloków](#blocks)|
|twarzy|[Twarzy](#faces) wymiaru.|
|etykiety|[Etykiety](#labels) wymiaru.|
|zrzuty|[Zrzuty](#shots) wymiaru.|
|marek|[Marek](#brands) wymiaru.|
|audioEffects|[AudioEffects](#audioEffects) wymiaru.|
|tonacje|[Tonacji](#sentiments) wymiaru.|
|visualContentModeration|[VisualContentModeration](#visualcontentmoderation) wymiaru.|
|textualContentModeration|[TextualContentModeration](#textualcontentmoderation) wymiaru.|
|emocji| [Emocji](#emotions) wymiaru.|
|Tematy|[Tematy](#topics) wymiaru.|

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
wystąpienia|Lista zakresów czasu tego bloku.|

#### <a name="transcript"></a>transkrypcji

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

#### <a name="ocr"></a>optyczne rozpoznawanie znaków

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator wiersza optyczne rozpoznawanie znaków|
|tekst|Optyczne rozpoznawanie znaków tekstu.|
|zaufania|Rozpoznawanie zaufania.|
|language|Język optyczne rozpoznawanie znaków.|
|wystąpienia|Lista zakresów czasu, w którym znajdowały się tym optyczne rozpoznawanie znaków (OCR tego samego mogą pojawiać się wielokrotnie).|
|Wysokość|Wysokość prostokąta optyczne rozpoznawanie znaków|
|top|Najważniejsze lokalizacji w piks.|
|po lewej stronie| Po lewej stronie lokalizacji w piks.|
|Szerokość|Szerokość prostokąta optyczne rozpoznawanie znaków|

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

#### <a name="keywords"></a>słowa kluczowe

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

#### <a name="faces"></a>twarzy

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator twarzy.|
|name|Nazwa powierzchnię. Może być "nieznany #, 0, osobistości zidentyfikowanych lub osoby uczonego klienta.|
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

#### <a name="labels"></a>etykiety

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

#### <a name="scenes"></a>sceny

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator sceny.|
|wystąpienia|Lista zakresów czasu to sceny (sceny może mieć tylko 1 wystąpienie).|

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

#### <a name="shots"></a>zrzuty

|Name (Nazwa)|Opis|
|---|---|
|id|Zrzut identyfikatora.|
|keyFrames|Lista ramek kluczowych w obrębie zrzut (każda ma identyfikator i listę zakresów czasu wystąpienia). Każde wystąpienie ramki kluczowej ma pole thumbnailId, której znajduje się miniaturę ramki kluczowej identyfikatora.|
|wystąpienia|Lista zakresów czasu to zrzut (zrzut może mieć tylko wystąpienia: 1).|

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

#### <a name="brands"></a>marek

Firmy i produkt nazw marek wykryte w zamiana mowy na tekst transkrypcji i/lub optyczne rozpoznawanie znaków w wideo. Nie dotyczy to visual rozpoznawanie marek lub wykrywania logo.

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator marki|
|name|Nazwa marki.|
|referenceId | Sufiks adresu url wikipedia marki. Na przykład "Target_Corporation" jest to sufiks [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | Dostępne w Wikipedii adresu url, jeśli istnieje. Na przykład [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description|Opis marek.|
|tags|Listę wstępnie zdefiniowanych tagi, które zostały skojarzone z tym marki.|
|zaufania|Wartość ufności wykrywacz marki Video Indexer (0 – 1).|
|wystąpienia|Lista zakresów czasu to marki. Każde wystąpienie ma brandType, która wskazuje, czy znak pojawiły się w tej transkrypcji lub optyczne rozpoznawanie znaków.|

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

|Name (Nazwa)|Opis|
|---|---|
|CorrespondenceCount|Liczba korelacji w trakcie filmu wideo.|
|SpeakerWordCount|Liczba słów na osoby mówiącej.|
|SpeakerNumberOfFragments|Ilość fragmenty osoby mówiącej ma w filmie wideo.|
|SpeakerLongestMonolog|Najdłuższy produktu monolog osoby mówiącej. Jeśli osoby mówiącej silences wewnątrz produktu monolog jest dołączony. Wyciszenia na początku i końcu produktu monolog zostaną usunięte.| 
|SpeakerTalkToListenRatio|Obliczanie opiera się na czas spędzony na produktu monolog osoby mówiącej (bez wyciszenia pomiędzy) podzielony przez łącznego czasu filmu wideo. Czas jest zaokrąglana do trzeciego punktu dziesiętnego.|

#### <a name="a-idaudioeffectsaudioeffects"></a><a id="audioEffects"/>audioEffects

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator efekt audio.|
|type|Typ efektu audio (na przykład wyciszenia Clapping, mowy,).|
|wystąpienia|Lista zakresów czasu, w którym znajdowały się w tym celu audio.|

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

#### <a name="sentiments"></a>tonacje

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

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator moderowanie zawartości tekstowej.|
|bannedWordsCount |Liczba zakazanych wyrazów.|
|bannedWordsRatio |Stosunek od całkowitej liczby słów.|

#### <a name="emotions"></a>emocji

Usługa Video Indexer identyfikuje emocji oparte na mowę i dźwięk podpowiedzi. Może to być zidentyfikowany rozpoznawania emocji: przynosi radość do, smutek, gniew lub strach.

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator emocji.|
|type|Obecnie rozpoznawania emocji, który został zidentyfikowany na podstawie rozpoznawania mowy i podpowiedzi audio. Mogą to być emocje takie jak radość, smutek, gniew czy strach.|
|wystąpienia|Lista zakresów czasu, w których pojawiły się tym emocji.|

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

Usługa Video Indexer sprawia, że wnioskowania główne tematy z transkrypcji. Jeśli to możliwe, poziom 1 [IPTC](https://iptc.org/standards/media-topics/) taksonomii jest dołączony. 

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator tematu.|
|name|Nazwa tematu, na przykład: "Pharmaceuticals".|
|referenceId|Linki do stron nadrzędnych odzwierciedlający hierarchii tematów. Na przykład: "Kondycji i dobro / medycyna i opiece zdrowotnej / stacjonarnym".|
|zaufania|Współczynnik ufności z zakresu [0,1]. Nowszy jest większa pewność.|
|language|Język używany w tym temacie.|
|iptcName|Nośnik IPTC kodu nazwy, w przypadku wykrycia.|
|wystąpienia |Obecnie usługa Video Indexer nie indeksowania tematu na przedziały czasu, więc całe wideo jest używany jako interwał.|

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

## <a name="next-steps"></a>Kolejne kroki

[Portal dla deweloperów usługa Video Indexer](https://api-portal.videoindexer.ai)

Aby uzyskać informacje o sposobie osadzanie widżetów w aplikacji, zobacz [widżetów osadzić indeksatora wideo we własnych aplikacjach](video-indexer-embed-widgets.md). 


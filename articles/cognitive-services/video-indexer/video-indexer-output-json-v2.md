---
title: Sprawdź dane wyjściowe indeksatora wideo Azure utworzonej przez interfejs API w wersji 2 | Dokumentacja firmy Microsoft
description: W tym temacie sprawdza, czy dane wyjściowe indeksatora wideo, generowane przez interfejs API w wersji 2.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cfowler
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 87301e4ce3c5b7db054b3dd86e8ee1ac5d90d3ca
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309064"
---
# <a name="examine-the-video-indexer-output-produced-by-v2-api"></a>Sprawdź, czy wyjście wideo indeksatora produkowane przez interfejs API w wersji 2

> [!Note]
> Interfejsy API V1 indeksatora wideo są teraz przestarzałe i zostanie usunięte w dniu 1 sierpnia 2018. Należy rozpocząć przy użyciu interfejsów API w wersji 2 indeksatora wideo, aby uniknąć zakłóceń.
>
> Aby opracować z interfejsami API v2 indeksatora wideo, można zapoznać się z instrukcjami znaleziono [tutaj](https://api-portal.videoindexer.ai/). 

Podczas wywoływania **uzyskać indeks wideo** interfejsu API i stan odpowiedzi jest prawidłowy, uzyskać szczegółowe dane wyjściowe JSON jako treść odpowiedzi. Zawartość JSON zawiera szczegółowe informacje o określonym szczegółowych danych wideo. Szczegółowych danych zawierają wymiary, takich jak: zapisy ocrs, kroje, tematy, bloków, itp. Wymiary są wystąpienia przedziałów czasu, przedstawiających podczas każdego wymiaru znajdowały się w wideo.  

Można również wizualnie sprawdzić insights podsumowań wideo, naciskając klawisz **odtwarzanie** przycisk wideo w portalu wideo indeksatora. Aby uzyskać więcej informacji, zobacz [wyświetlanie i edytowanie wideo insights](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

W tym artykule sprawdza, czy zawartość JSON zwrócony przez **uzyskać indeks wideo** interfejsu API. 

> [!NOTE]
> Wygaśnięcie wszystkich tokenów dostępu indeksatora wideo to jedna godzina.


## <a name="root-elements"></a>Elementy główne

|Name (Nazwa)|Opis|
|---|---|
|accountId|Lista odtwarzania VI konta identyfikatora.|
|id|Identyfikator listy odtwarzania.|
|name|Nazwa listy odtwarzania.|
|description|Opis elementu listy odtwarzania.|
|userName|Nazwa użytkownika, który utworzył listy odtwarzania.|
|utworzone|Czas utworzenia listy odtwarzania.|
|privacyMode|Lista odtwarzania trybie prywatności (Private/Public).|
|state|Lista odtwarzania (przekazanego, przetwarzanie, przetworzone, nie powiodło się, poddane kwarantannie).|
|isOwned|Określa, czy lista odtwarzania został utworzony przez bieżącego użytkownika.|
|isEditable|Określa, czy bieżący użytkownik jest uwierzytelniony do edycji listy odtwarzania.|
|isBase|Określa, czy lista odtwarzania jest podstawowy listy odtwarzania (klip wideo) lub listy odtwarzania z innych plików wideo (pochodnych).|
|durationInSeconds|Łączny czas trwania listy odtwarzania.|
|summarizedInsights|Zawiera jeden [summarizedInsights](#summarizedinsights).
|wideo|Lista [wideo](#videos) konstruowania listy odtwarzania.<br/>Jeśli ta lista odtwarzania z utworzone z przedziałów czasu innych plików wideo (ustalona) wideo na tej liście zawierają tylko dane z przedziałów czasu uwzględnione.|

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
|name|Nazwa obrazu wideo. Na przykład Azure Monitor.|
|shortId|Identyfikator wideo. Na przykład 63c6d532ff.|
|privacyMode|Podział użytkownika może mieć jeden z następujących trybów: **prywatnej**, **publicznego**. **Publiczny** -wideo jest widoczny dla wszystkich użytkowników, konta i każda osoba, która zawiera link do wideo. **Prywatne** -wideo jest widoczny dla wszystkich osób w ramach Twojego konta.|
|czas trwania|Zawiera jeden czas trwania, który opisuje godzina wystąpienia szczegółowe informacje. Czas trwania jest w sekundach.|
|thumbnailUrl|Miniatur wideo pełny adres URL. Na przykład "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Zwróć uwagę, że jeśli wideo jest prywatny, adres URL zawiera token dostępu jedną godzinę. Po upływie godziny, adres URL nie będzie już prawidłowy i należy uzyskać podział ponownie, podając nowy adres url lub wywołać GetAccessToken uzyskać nowy token dostępu i ręcznie utworzyć pełny adres url ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken] ").|
|kroje|Może zawierać jeden lub więcej. Aby uzyskać więcej informacji, zobacz [kroje](#faces).|
|— Tematy|Może zawierać jeden lub więcej tematów. Aby uzyskać więcej informacji, zobacz [tematy](#topics).|
|opinie|Może zawierać jeden lub więcej opinie. Aby uzyskać więcej informacji, zobacz [opinie](#sentiments).|
|audioEffects| Może zawierać jeden lub więcej audioEffects. Aby uzyskać więcej informacji, zobacz [audioEffects](#audioeffects).|
|marek| Może zawierać zero lub więcej marek. Aby uzyskać więcej informacji, zobacz [marek](#brands).|
|Statystyki | Aby uzyskać więcej informacji, zobacz [statystyki](#statistics).|

### <a name="statistics"></a>Statystyki

|Name (Nazwa)|Opis|
|---|---|
|CorrespondenceCount|Liczba korelacji w wideo.|
|WordCount|Liczba słów na prelegenta.|
|SpeakerNumberOfFragments|Ilość fragmenty prelegenta ma w pliku wideo.|
|SpeakerLongestMonolog|Najdłuższy monolog prelegenta. Jeśli prelegenta ma silences wewnątrz monolog jest dołączony. Cisza na początku i końcu monolog zostaną usunięte.| 
|SpeakerTalkToListenRatio|Obliczenia są oparte na czas spędzony na monolog osoby mówiącej (bez wyciszenia między) podzielony przez całkowity czas wideo. Czas jest zaokrąglana do trzeciego punktu dziesiętnego.|

## <a name="videos"></a>wideo

|Name (Nazwa)|Opis|
|---|---|
|accountId|VI wideo konta identyfikatora.|
|id|Identyfikator wideo.|
|name|Nazwa wideo.
|state|Stan wideo (przekazanego, przetwarzanie, przetworzone, nie powiodło się, poddane kwarantannie).|
|processingProgress|Postęp przetwarzania podczas przetwarzania (na przykład 20%).|
|Kod błędu|Kod błędu, jeśli nie przetworzył (na przykład "UnsupportedFileType").|
|failureMessage|Komunikat o błędzie, jeśli nie można przetworzyć.|
|externalId|Identyfikator zewnętrznego wideo (jeśli jest określona przez użytkownika).|
|zewnętrznego adresu URL|Wideo zewnętrznego adresu url (jeśli jest określona przez użytkownika).|
|metadane|Metadane zewnętrznych wideo (jeśli jest określona przez użytkownika).|
|isAdult|Czy wideo został ręcznie przejrzeć i zidentyfikowane jako wideo dla dorosłych.|
|szczegółowe informacje|Obiekt szczegółowych informacji.|
|thumbnailUrl|Miniatur wideo pełny adres URL. Na przykład "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Zwróć uwagę, że jeśli wideo jest prywatny, adres URL zawiera token dostępu jedną godzinę. Po upływie godziny, adres URL nie będzie już prawidłowy i należy uzyskać podział ponownie, podając nowy adres url lub wywołać GetAccessToken uzyskać nowy token dostępu i ręcznie utworzyć pełny adres url ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken] ").|
|publishedUrl|Adres url do strumienia wideo.|
|publishedUrlProxy|Adres url do strumienia wideo z (w przypadku urządzeń firmy Apple).|
|viewToken|Token krótko widoku do strumieniowego przesyłania wideo.|
|sourceLanguage|Język źródła wideo.|
|Język|Wideo języku (tłumaczenia).|
|indexingPreset|Ustawienia domyślne używane do indeksowania wideo.|
|streamingPreset|Ustawienia domyślne używane do publikowania wideo.|
|linguisticModelId|Model CRI używane wykonać transkrypcji wideo.|

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

Szczegółowe dane będą zbiór wymiarów (na przykład wierszy wykaz, kroje, marek itp.), gdzie każdy wymiar to listy elementów unikatowe (na przykład face1, pierwszy plan 2, pierwszy plan 3), a każdy element ma własną metadanych i listy jego wystąpienia (które są przedziałów czasu z dodatkowe metadane opcjonalne)

Krój może mieć identyfikator, nazwę miniaturę, inne metadane i listę jego wystąpień danych czasowych (na przykład: 00:00:05 — 00:00:10: 00:01:00 - 00:02:30 i 00:41:21 – 00:41:49.) Każde wystąpienie danych czasowych może mieć dodatkowe metadane. Na przykład prostokąt na powierzchni współrzędne (20,230,60,60).

|Wersja|Wersja kodu|
|---|---|
|sourceLanguage|Język źródła wideo (przy założeniu jeden język głównym). W formie [BCP 47](https://tools.ietf.org/html/bcp47) ciągu.|
|Język|Język insights (translacji z języka źródłowego). W formie [BCP 47](https://tools.ietf.org/html/bcp47) ciągu.|
|zapis|[Wykaz](#transcript) wymiaru.|
|rozpoznawania|[Rozpoznawania](#ocr) wymiaru.|
|Słowa kluczowe|[Słowa kluczowe](#keywords) wymiaru.|
|kroje|[Kroje](#faces) wymiaru.|
|Etykiety|[Etykiety](#labels) wymiaru.|
|zrzuty|[Zrzuty](#shots) wymiaru.|
|marek|[Marek](#brands) wymiaru.|
|audioEffects|[AudioEffects](#audioEffects) wymiaru.|
|opinie|[Opinie](#sentiments) wymiaru.|
|visualContentModeration|[VisualContentModeration](#visualcontentmoderation) wymiaru.|
|textualConentModeration|[TextualConentModeration](#textualconentmoderation) wymiaru.|

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
  "textualConentModeration": ...
}
```

#### <a name="transcript"></a>zapis

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator wiersza.|
|tekst|Wykaz, samej siebie.|
|Język|Język zapis. Przeznaczony do obsługi wykaz, w którym każdy wiersz może mieć inną wersją językową.|
|wystąpienia|Lista przedziałów czasu, w której znajdowały się tego wiersza. Jeśli wystąpienie jest wykaz, będzie mieć tylko 1 wystąpieniem.|

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

#### <a name="ocr"></a>rozpoznawania

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator Rozpoznawania wiersza.|
|tekst|Tekst Rozpoznawania.|
|Zaufania|Zaufanie rozpoznawania.|
|Język|Język Rozpoznawania.|
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

#### <a name="keywords"></a>Słowa kluczowe

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator — słowo kluczowe.|
|tekst|Tekst — słowo kluczowe.|
|Zaufania|Słowo kluczowe rozpoznawania zaufania.|
|Język|Język — słowo kluczowe (po translacji).|
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

#### <a name="faces"></a>kroje

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator twarzy na obrazie.|
|name|Nazwa czcionki. Może to być "Nieznany #0", zidentyfikowanych renomy lub osoby przeszkolone klienta.|
|Zaufania|Zaufanie identyfikacji twarzy na obrazie.|
|description|Jeśli renomy, może być opis: "Satya Nadella powstał na...". |
|thumbnalId|Identyfikator miniatury napotykane przez.|
|knownPersonId|Jeśli jest znane osoby, jego identyfikator wewnętrzny.|
|Identyfikator odwołania|Jeśli jest Bing gwiazdach ekranu, jego identyfikator Bing.|
|Typ odwołania|Obecnie tylko Bing.|
|tytuł|Jeśli renomy, jego tytuł (na przykład "Prezes firmy Microsoft").|
|ImageUrl|Jeśli renomy, jej adres url obrazu.|
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

#### <a name="labels"></a>Etykiety

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator etykiety.|
|name|Nazwa etykiety (na przykład "Komputer", "TV").|
|Język|Etykieta nazwy języka (po translacji). NARZĘDZIE BCP 47|
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

#### <a name="shots"></a>zrzuty

|Name (Nazwa)|Opis|
|---|---|
|id|Zrzut identyfikatora.|
|Klatek|Lista klatek kluczowych w zrzut (każda ma identyfikator i listę wystąpień przedziałów czasu). Klatek kluczowych wystąpień ma pola thumbnailId ze miniatur klatki kluczowej identyfikatora.|
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

#### <a name="brands"></a>marek

Działalności biznesowej i produktu firmowe wykryte w wykaz tekstu i/lub wideo Rozpoznawania mowy. Nie ma visual rozpoznawania marek lub wykrywania logo.

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator marki|
|name|Nazwa marek.|
|Identyfikator odwołania | Sufiks adresu URL wikipedia marki. Na przykład "Target_Corporation" jest to sufiks [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | Produkt firmy Wikipedia adres url, jeśli istnieje. Na przykład [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description|Opis marek.|
|tags|Listy wstępnie zdefiniowanych tagi, które zostały skojarzone z tym marki.|
|Zaufania|Wartość zaufania detektora brand indeksatora wideo (0-1).|
|wystąpienia|Lista zakresów czasu znak. Każde wystąpienie ma brandType, która wskazuje, czy znak znajdowały się w wykazie lub Rozpoznawania.|

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

#### <a name="audioeffects"></a>audioEffects

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator efekt audio.|
|type|Typ efektu audio (na przykład Clapping, mowy, wyciszenia).|
|wystąpienia|Lista przedziałów czasu, w której znajdowały się w tym celu audio.|

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

#### <a name="sentiments"></a>opinie

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

#### <a name="visualcontentmoderation"></a>visualContentModeration

Blok visualContentModeration zawiera przedziałów czasu, które indeksatora wideo mogących mieć zawartość dla dorosłych. Jeśli visualContentModeration jest pusta, nie ma żadnej zawartości dla dorosłych, która została zidentyfikowana.

Pliki wideo, które zostały znalezione ma zawierać zawartość dla dorosłych lub luksusowych mogą być dostępne dla prywatnych tylko w widoku. Użytkownicy mają opcję, aby przesłać żądanie dla człowieka przeglądu zawartości, w których przypadku atrybut IsAdult będzie zawierać wyników przeglądu człowieka.

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator visual łagodzenia zawartości.|
|adultScore|Wynik dla dorosłych (od moderatora zawartości).|
|racyScore|Wynik luksusowych (od łagodzenia zawartości).|
|wystąpienia|Lista przedziałów czasu, w której znajdowały się tym visual łagodzenia zawartości.|

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

#### <a name="textualconentmoderation"></a>textualConentModeration 

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator łagodzenia zawartości tekstowej.|
|bannedWordsCount |Liczba wyrazów zabronione.|
|bannedWordsRatio |Współczynnik od całkowitą liczbę słów.|


## <a name="next-steps"></a>Kolejne kroki

[Indeksator wideo interfejsu API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

Aby dowiedzieć się, jak osadzić elementy widget w aplikacji, zobacz [elementy widget indeksatora osadzania wideo do aplikacji](video-indexer-embed-widgets.md). 


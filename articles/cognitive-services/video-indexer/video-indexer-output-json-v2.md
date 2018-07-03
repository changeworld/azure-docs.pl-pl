---
title: Sprawdź dane wyjściowe Azure Video Indexer produkowane przez interfejsy API wersji 2 | Dokumentacja firmy Microsoft
description: W tym temacie analizuje danych wyjściowych indeksatora wideo utworzone przez interfejs API w wersji 2.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cfowler
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 9e66bffe1a547e72143201f4afb8e5ae99065df9
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341702"
---
# <a name="examine-the-video-indexer-output-produced-by-v2-api"></a>Zbadanie danych wyjściowych indeksatora wideo utworzone przez interfejs API w wersji 2

> [!Note]
> Interfejsy API wersji 1 dla indeksatora wideo są one przestarzałe i zostanie usunięte w dniu 1 sierpnia 2018. Należy rozpocząć przy użyciu interfejsów API w wersji 2 indeksatora wideo, aby uniknąć przerw w działaniu.
>
> Tworzenie za pomocą interfejsów API w wersji 2 indeksatora wideo, można zapoznać się z instrukcjami znaleziono [tutaj](https://api-portal.videoindexer.ai/). 

Gdy wywołujesz **uzyskać indeks wideo** interfejsu API i stan odpowiedzi jest OK, Uzyskaj szczegółowe dane wyjściowe JSON jako treść odpowiedzi. Zawartość JSON zawiera szczegółowe informacje o określonym informacjom o filmie wideo. Szczegółowe informacje zawierają wymiary, takie jak: zapisy ocrs, twarzy, tematy, bloki, itp. Wymiary są wystąpienia zakresy czasu, przedstawiających podczas każdego wymiaru pojawiły się w trakcie filmu wideo.  

Można również wizualnie sprawdzić insights podsumowań wideo, naciskając klawisz **Odtwórz** przycisk film wideo, w portalu usługi Video Indexer. Aby uzyskać więcej informacji, zobacz [wyświetlanie i edytowanie informacji szczegółowych wideo](video-indexer-view-edit.md).

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
|shortId|Identyfikator filmu wideo. Na przykład 63c6d532ff.|
|privacyMode|Podział usługi może mieć jedną z następujących trybów: **prywatnej**, **publicznych**. **Publiczne** — film wideo jest widoczny dla wszystkich użytkowników w swoje konto i każda osoba, która zawiera link do filmu wideo. **Prywatne** — film wideo jest widoczny dla wszystkich użytkowników na Twoim koncie.|
|czas trwania|Zawiera jeden czas trwania, opisujący godzina wystąpienia w szczegółowe informacje. Czas trwania to w ciągu kilku sekund.|
|thumbnailUrl|Miniatura wideo pełny adres URL. Na przykład "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Należy zauważyć, że jeśli wideo jest prywatny, adres URL zawiera token dostępu jedną godzinę. Po upływie godziny, adres URL nie będzie już prawidłowy i należy uzyskać podział ponownie, podając nowy adres url lub wywołać GetAccessToken uzyskać nowy token dostępu i ręcznie utworzyć pełny adres url ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken] ").|
|twarzy|Może zawierać jeden lub więcej twarzy. Aby uzyskać więcej informacji, zobacz [twarzy](#faces).|
|Tematy|Może zawierać jeden lub więcej tematów. Aby uzyskać więcej informacji, zobacz [tematy](#topics).|
|opinie|Może zawierać co najmniej jeden tonacji. Aby uzyskać więcej informacji, zobacz [tonacji](#sentiments).|
|audioEffects| Może zawierać jeden lub więcej audioEffects. Aby uzyskać więcej informacji, zobacz [audioEffects](#audioeffects).|
|marek| Może zawierać zero lub więcej marek. Aby uzyskać więcej informacji, zobacz [marek](#brands).|
|statystyki | Aby uzyskać więcej informacji, zobacz [statystyki](#statistics).|

### <a name="statistics"></a>statystyki

|Name (Nazwa)|Opis|
|---|---|
|CorrespondenceCount|Liczba korelacji w trakcie filmu wideo.|
|WordCount|Liczba słów na osoby mówiącej.|
|SpeakerNumberOfFragments|Ilość fragmenty osoby mówiącej ma w filmie wideo.|
|SpeakerLongestMonolog|Najdłuższy produktu monolog osoby mówiącej. Jeśli osoby mówiącej silences wewnątrz produktu monolog jest dołączony. Wyciszenia na początku i końcu produktu monolog zostaną usunięte.| 
|SpeakerTalkToListenRatio|Obliczanie opiera się na czas spędzony na produktu monolog osoby mówiącej (bez wyciszenia pomiędzy) podzielony przez łącznego czasu filmu wideo. Czas jest zaokrąglana do trzeciego punktu dziesiętnego.|

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
|szczegółowe informacje|Obiekt szczegółowych informacji.|
|thumbnailUrl|Miniatura wideo pełny adres URL. Na przykład "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Należy zauważyć, że jeśli wideo jest prywatny, adres URL zawiera token dostępu jedną godzinę. Po upływie godziny, adres URL nie będzie już prawidłowy i należy uzyskać podział ponownie, podając nowy adres url lub wywołać GetAccessToken uzyskać nowy token dostępu i ręcznie utworzyć pełny adres url ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken] ").|
|publishedUrl|Adres url do przesyłania strumieniowego wideo.|
|publishedUrlProxy|Adres url do przesyłania strumieniowego wideo z (dla urządzeń firmy Apple).|
|viewToken|Token krótkotrwałe widoku służącą do strumieniowego przesyłania wideo.|
|sourceLanguage|Język źródłowy klip wideo.|
|Język|Rzeczywiste język wideo (tłumaczenia).|
|indexingPreset|Ustawienie wstępne, używane do indeksowania filmu wideo.|
|streamingPreset|Ustawienie wstępne, używany do publikowania filmu wideo.|
|linguisticModelId|Model CRI umożliwia także filmu wideo.|

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

Krój może mieć identyfikator, nazwę, miniatury, inne metadane i listę swoich wystąpień danych czasowych (na przykład: 00:00:05 — 00:00:10: 00:01:00 - 00:02:30 i 00:41:21 – 00:41:49.) Każde wystąpienie danych czasowych może mieć dodatkowe metadane. Na przykład obrys twarzy współrzędne (20,230,60,60).

|Wersja|Wersja kodu|
|---|---|
|sourceLanguage|Język źródłowy wideo (przy założeniu jeden język master). W formie [BCP 47](https://tools.ietf.org/html/bcp47) ciągu.|
|Język|Język insights (tłumaczenia go z języka źródłowego). W formie [BCP 47](https://tools.ietf.org/html/bcp47) ciągu.|
|transkrypcji|[Transkrypcji](#transcript) wymiaru.|
|optyczne rozpoznawanie znaków|[Optyczne rozpoznawanie znaków](#ocr) wymiaru.|
|słowa kluczowe|[Słowa kluczowe](#keywords) wymiaru.|
| bloki|Może zawierać jeden lub więcej [bloków](#blocks)|
|twarzy|[Twarzy](#faces) wymiaru.|
|etykiety|[Etykiety](#labels) wymiaru.|
|zrzuty|[Zrzuty](#shots) wymiaru.|
|marek|[Marek](#brands) wymiaru.|
|audioEffects|[AudioEffects](#audioEffects) wymiaru.|
|opinie|[Tonacji](#sentiments) wymiaru.|
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

#### <a name="blocks"></a>bloki

Atrybut | Opis
---|---
id|Identyfikator bloku.
wiersze|Może zawierać jeden lub więcej [wierszy](#lines)
sentimentIds|**SentimentIds** atrybutu jest zarezerwowana do użytku w przyszłości.
thumbnailIds|**ThumbnailIds** atrybutu jest zarezerwowana do użytku w przyszłości.
wskaźniki nastrojów klientów|Wskaźniki nastrojów klientów w bloku (0 – 1, dodatnia lub ujemna).
twarzy|Może zawierać jeden lub więcej [twarzy](#faces).
ocrs|Może zawierać jeden lub więcej [ocrs](#ocrs).
audioEffectInstances|Może zawierać jeden lub więcej [audioEffectInstances](#audioEffectInstances).
sceny|Może zawierać jeden lub więcej [sceny](#scenes).
Adnotacje|Może zawierać zero lub więcej [adnotacje](#annotations).

#### <a name="transcript"></a>transkrypcji

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator wiersza.|
|tekst|Transkrypcja sam.|
|Język|Język transkrypcji. Przeznaczony do obsługi transkrypcji, w którym każdy wiersz może mieć inny język.|
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
|Język|Język optyczne rozpoznawanie znaków.|
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

#### <a name="keywords"></a>słowa kluczowe

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator — słowo kluczowe.|
|tekst|Tekst — słowo kluczowe.|
|zaufania|Słowo kluczowe rozpoznawania zaufania.|
|Język|Język — słowo kluczowe (w przypadku tłumaczonym).|
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
|name|Nazwa czcionki. Może to być "Nieznany #0", osobistości zidentyfikowanych lub osoby uczonego klienta.|
|zaufania|Zaufanie identyfikacji twarzy.|
|description|Opis osobistości. |
|thumbnalId|Identyfikator miniatury napotykane przez.|
|knownPersonId|Jeśli jest znane osoby wewnętrzny identyfikator.|
|Identyfikator odwołania|Jeśli jest Bing rozpoznawanie osobistości, jego identyfikator Bing.|
|Element referenceType|Obecnie tylko Bing.|
|tytuł|Jeśli jest rozpoznawanie osobistości, jego tytuł (na przykład "Prezes firmy Microsoft").|
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
|Język|Etykieta nazwy język (w przypadku przetłumaczona). NARZĘDZIE BCP 47|
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

#### <a name="shots"></a>zrzuty

|Name (Nazwa)|Opis|
|---|---|
|id|Zrzut identyfikatora.|
|klatki kluczowe|Lista klatek kluczowych w obrębie zrzut (każda ma identyfikator i listę zakresów czasu wystąpienia). Klatki kluczowe wystąpienia ma pola thumbnailId ze miniaturę ramki kluczowej identyfikatora.|
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

#### <a name="brands"></a>marek

Firmy i produkt nazw marek wykryte w zamiana mowy na tekst transkrypcji i/lub optyczne rozpoznawanie znaków w wideo. Nie dotyczy to visual rozpoznawanie marek lub wykrywania logo.

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator marki|
|name|Nazwa marki.|
|Identyfikator odwołania | Sufiks adresu url wikipedia marki. Na przykład "Target_Corporation" jest to sufiks [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
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

#### <a name="audioeffects"></a>audioEffects

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

#### <a name="sentiments"></a>opinie

Opinie są agregowane według ich pola sentimentType (neutralna/plus/minus). Na przykład 0-0.1, 0.2 0,1.

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator opinii.|
|Średnia |Średnia wszystkie wyniki wszystkich wystąpień tego typu tonacji - neutralna/plus/minus|
|wystąpienia|Lista zakresów czasu, w których pojawiły się tym wskaźniki nastrojów klientów.|

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

#### <a name="textualconentmoderation"></a>textualConentModeration 

|Name (Nazwa)|Opis|
|---|---|
|id|Identyfikator moderowanie zawartości tekstowej.|
|bannedWordsCount |Liczba zakazanych wyrazów.|
|bannedWordsRatio |Stosunek od całkowitej liczby słów.|


## <a name="next-steps"></a>Kolejne kroki

[Interfejsu API indeksatora wideo](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

Aby uzyskać informacje o sposobie osadzanie widżetów w aplikacji, zobacz [widżetów osadzić indeksatora wideo we własnych aplikacjach](video-indexer-embed-widgets.md). 


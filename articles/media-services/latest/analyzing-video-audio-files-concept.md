---
title: Analizowanie plików wideo i audio
titleSuffix: Azure Media Services
description: Dowiedz się, jak analizować zawartość audio i wideo przy użyciu audioanalyzerPreset i VideoAnalyzerPreset w usłudze Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/30/2020
ms.author: juliako
ms.openlocfilehash: 1d28fc37b98493322b9e201ac899b7911dd1d705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269889"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analizowanie plików wideo i audio za pomocą usługi Azure Media Services

Usługa Azure Media Services w wersji 3 umożliwia wyodrębnianie szczegółowych informacji z plików wideo i audio za pomocą indeksatora wideo. W tym artykule opisano predefiniowane ustawienia analizatora usługi Media Services w wersji 3 używane do wyodrębniania tych szczegółowych informacji. Aby uzyskać bardziej szczegółowe informacje, można korzystać bezpośrednio z usługi Video Indexer. Aby zrozumieć, kiedy używać predefiniowanych ustawień analizatora wideo i programu Media Services, zapoznaj się z [dokumentem porównawczym](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Aby analizować zawartość przy użyciu ustawień predefiniowanych usługi Media Services w wersji 3, należy utworzyć **transformowanie** i przesłać **zadanie,** które używa jednego z następujących ustawień: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) lub **AudioAnalyzerPreset**. Aby zapoznać się z samouczkiem przedstawiającym sposób korzystania z **funkcji VideoAnalyzerPreset**, zobacz [Analizowanie klipów wideo za pomocą usługi Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Jeśli używasz wstępnych ustawień analizatora wideo lub dźwięku, skorzystaj z witryny Azure Portal i ustaw na koncie 10 jednostek zarezerwowanych multimediów S3. Aby uzyskać więcej informacji, zobacz temat [Scale media processing (Skalowanie przetwarzania multimediów)](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Zgodność, ochrona prywatności i zabezpieczenia

Jako ważne przypomnienie musisz przestrzegać wszystkich obowiązujących przepisów podczas korzystania z indeksatora wideo i nie możesz używać indeksatora wideo ani żadnej innej usługi platformy Azure w sposób, który narusza prawa innych osób lub może być szkodliwy dla innych osób. Przed przesłaniem jakichkolwiek filmów, w tym jakichkolwiek danych biometrycznych, do usługi Video Indexer w celu przetwarzania i przechowywania, Użytkownik musi mieć wszystkie odpowiednie prawa, w tym wszystkie odpowiednie zgody, od osób w filmie. Aby dowiedzieć się więcej o zgodności, prywatności i bezpieczeństwie w programie Video Indexer, [postanowienia dotyczące usług Microsoft Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). W przypadku zobowiązań firmy Microsoft w zakresie ochrony prywatności i postępowania z danymi użytkownika należy zapoznać się z [Zasadami zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement)firmy Microsoft, [Postanowieniami dotyczącymi usług online](https://www.microsoft.com/licensing/product-licensing/products) ("OST") i [uzupełnieniem dotyczącym przetwarzania danych](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Dodatkowe informacje o prywatności, w tym dotyczące przechowywania, usuwania/niszczenia danych, są dostępne w OST i [tutaj](../video-indexer/faq.md). Korzystając z Video Indexer, użytkownik wyraża zgodę na przestrzeganie Warunków świadczenia usług Cognitive Services, OST, DPA i Polityki prywatności.

## <a name="built-in-presets"></a>Wbudowane ustawienia wstępne

Usługi Media Services obsługują obecnie następujące wbudowane ustawienia predefiniowane analizatora:  

|**Nazwa predefiniowane**|**Scenariusz**|**Szczegóły**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analizowanie dźwięku|Predefiniowane stosuje wstępnie zdefiniowany zestaw operacji analizy opartych na si, w tym transkrypcji mowy. Obecnie preset obsługuje przetwarzanie zawartości za pomocą jednej ścieżki audio, która zawiera mowę w jednym języku. Język ładunku audio można określić w danych wejściowych przy użyciu formatu BCP-47 "regionu tagu języka". Obsługiwane języki to angielski ("en-US" i "en-GB"), hiszpański ("es-ES" i "es-MX"), francuski ("fr-FR"), włoski ("it-IT"), japoński ("es-ES" i "es-MX"), francuski ("fr-FR"), włoski ("it-IT"), japoński ("es-ES" ja-JP"),portugalski ("pt-BR"), chiński ("zh-CN"), niemiecki ("de-DE"), arabski ("ar-EG" i "ar-SY"), rosyjski ("ru-RU"), hindi ("hi-IN") i koreański ("ko-KR").<br/><br/> Jeśli język nie jest określony lub ustawiony na wartość null, automatyczne wykrywanie języka wybiera pierwszy wykryty język i kontynuuje z wybranym językiem przez cały czas trwania pliku. Funkcja automatycznego wykrywania języka obsługuje obecnie język angielski, chiński, francuski, niemiecki, włoski, japoński, hiszpański, rosyjski i portugalski. Nie obsługuje dynamicznego przełączania między językami po wykryciu pierwszego języka. Funkcja automatycznego wykrywania języka działa najlepiej z nagraniami audio z wyraźnie dostrzegalną mową. Jeśli automatyczne wykrywanie języka nie może znaleźć języka, transkrypcja powróci do języka angielskiego.|
|[WideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analizowanie dźwięku i wideo|Wyodrębnia szczegółowe informacje (bogate metadane) zarówno z audio, jak i wideo i wyprowadza plik w formacie JSON. Można określić, czy podczas przetwarzania pliku wideo mają być wyodrębniane tylko szczegółowe informacje audio. Aby uzyskać więcej informacji, zobacz [Analizowanie wideo](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset (Przedstawianie twarzy)](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Wykrywanie twarzy obecnych w filmie|Opisuje ustawienia, które mają być używane podczas analizowania wideo w celu wykrycia wszystkich obecnych twarzy.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

Predefiniowane ustawienia umożliwiają wyodrębnianie wielu szczegółowych informacji audio z pliku audio lub wideo. Dane wyjściowe zawierają plik JSON (ze wszystkimi spostrzeżeniami) i plik VTT dla transkrypcji audio. To ustawienie wstępne akceptuje właściwość, która określa język pliku wejściowego w postaci ciągu [BCP47.](https://tools.ietf.org/html/bcp47) Szczegółowe informacje audio obejmują:

* **Transkrypcja audio:** Transkrypcja wypowiedzianych słów z sygnaturami czasowymi. Obsługiwanych jest wiele języków.
* **Indeksowanie prelegentów:** Mapowanie głośników i odpowiadających im słów mówionych.
* **Analiza tonacji mowy:** Dane wyjściowe analizy tonacji wykonywanej na transkrypcji audio.
* **Słowa kluczowe:** Słowa kluczowe wyodrębnione z transkrypcji audio.

### <a name="videoanalyzerpreset"></a>WideoAnalyzerPreset

Predefiniowane ustawienia umożliwiają wyodrębnianie wielu szczegółowych informacji audio i wideo z pliku wideo. Dane wyjściowe obejmują plik JSON (ze wszystkimi spostrzeżeniami), plik VTT dla transkrypcji wideo i zbiór miniatur. To ustawienie wstępne akceptuje również ciąg [BCP47](https://tools.ietf.org/html/bcp47) (reprezentujący język wideo) jako właściwość. Szczegółowe informacje wideo obejmują wszystkie powyższe spostrzeżenia audio oraz następujące dodatkowe elementy:

* **Śledzenie twarzy:** czas, w którym twarze są obecne w filmie. Każda twarz ma identyfikator twarzy i odpowiednią kolekcję miniatur.
* **Tekst wizualny:** tekst wykryty za pomocą optycznego rozpoznawania znaków. Tekst jest oznaczony sygnaturą czasową i służy również do wyodrębniania słów kluczowych (oprócz transkrypcji audio).
* **Klatki kluczowe:** zbiór klatek kluczowych wyodrębnionych z filmu.
* **Moderowanie treści wizualnych:** część filmów oznaczonych jako dorosła lub rasowa.
* **Adnotacja:** Wynik dodawania adnotacji do filmów na podstawie wstępnie zdefiniowanego modelu obiektu

## <a name="insightsjson-elements"></a>insights.json elementy

Dane wyjściowe zawierają plik JSON (insights.json) ze wszystkimi spostrzeżeniami znalezionymi w wideo lub audio. JSON może zawierać następujące elementy:

### <a name="transcript"></a>Zapis

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

### <a name="ocr"></a>Ocr

|Nazwa|Opis|
|---|---|
|id|Identyfikator linii OCR.|
|tekst|Tekst OCR.|
|ufność|Zaufanie do uznania.|
|language|Język OCR.|
|Wystąpień|Lista zakresów czasu, w których pojawił się ten OCR (ten sam OCR może pojawić się wiele razy).|

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

### <a name="faces"></a>Twarze

|Nazwa|Opis|
|---|---|
|id|Identyfikator twarzy.|
|name|Nazwa twarzy. Może to być "Nieznany #0", zidentyfikowana celebrytka lub osoba przeszkolona przez klienta.|
|ufność|Pewność identyfikacji twarzy.|
|description|Opis celebrytki. |
|thumbnailId|Identyfikator miniatury tej twarzy.|
|knownPersonId|Wewnętrzny identyfikator (jeśli jest to znana osoba).|
|referenceId|Bing ID (jeśli jest to gwiazda Bing).|
|typ odwołania|Obecnie tylko Bing.|
|title|Tytuł (jeśli jest sławna , na przykład "Ceo Microsoftu").|
|Imageurl|Adres URL obrazu, jeśli jest gwiazdą.|
|Wystąpień|Przypadki, w których twarz pojawiła się w danym przedziale czasu. Każde wystąpienie ma również thumbnailsId. |

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

### <a name="shots"></a>Zdjęć

|Nazwa|Opis|
|---|---|
|id|Identyfikator strzału.|
|Klatek kluczowych|Lista klatek kluczowych w kadrze (każda z nich ma identyfikator i listę zakresów czasu wystąpień). Wystąpienia ramek kluczowych mają pole miniaturid z identyfikatorem miniatury klatki kluczowej.|
|Wystąpień|Lista zakresów czasowych tego strzału (strzały mają tylko 1 instancję).|

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

|Nazwa|Opis|
|---|---|
|Konto korespondencyjne|Liczba korespondencji w filmie.|
|Wordcount|Liczba słów na mówcę.|
|GłośnikNumberOfFragments|Ilość fragmentów, które głośnik ma w filmie.|
|GłośnikLongestMonolog|Najdłuższy monolog prelegenta. Jeśli głośnik ma wyciszenia wewnątrz monologu jest dołączony. Cisza na początku i koniec monologu jest usuwany.|
|GłośnikTalkToListenRatio|Obliczenia są oparte na czasie spędzonym na monologu prelegenta (bez ciszy pomiędzy) podzielonym przez całkowity czas filmu. Czas jest zaokrąglany do trzeciego przecinka dziesiętnego.|


### <a name="sentiments"></a>Uczucia

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

### <a name="labels"></a>Etykiety

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

### <a name="keywords"></a>keywords (słowa kluczowe)

|Nazwa|Opis|
|---|---|
|id|Identyfikator słowa kluczowego.|
|tekst|Tekst słowa kluczowego.|
|ufność|Zaufanie do rozpoznawania słowa kluczowego.|
|language|Język słowa kluczowego (po przetłumaczeniu).|
|Wystąpień|Lista zakresów czasu, w których pojawiło się to słowo kluczowe (słowo kluczowe może pojawić się wiele razy).|

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

VisualContentModeration blok zawiera zakresy czasu, które Indeksator wideo znaleźć potencjalnie treści dla dorosłych. Jeśli visualContentModeration jest pusty, nie ma żadnych treści dla dorosłych, który został zidentyfikowany.

Filmy, które zawierają treści dla dorosłych lub treści rasistowskich, mogą być dostępne tylko do wyświetlania prywatnego. Użytkownicy mogą przesłać wniosek o przegląd treści przez `IsAdult` człowieka, w którym to przypadku atrybut będzie zawierał wynik ludzkiej recenzji.

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
## <a name="next-steps"></a>Następne kroki

[Samouczek: analizowanie wideo za pomocą usługi Azure Media Services](analyze-videos-tutorial-with-api.md)

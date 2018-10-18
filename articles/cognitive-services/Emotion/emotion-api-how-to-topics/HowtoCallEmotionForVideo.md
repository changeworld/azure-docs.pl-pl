---
title: 'Przykład: wywoływanie interfejsu API rozpoznawania emocji dla wideo'
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak wywoływać interfejs API rozpoznawania emocji dla wideo w usługach Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: sample
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 2687145a89c11efb4a3bcb1494a39806e9aae551
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238611"
---
# <a name="example-call-emotion-api-for-video"></a>Przykład: wywoływanie interfejsu API rozpoznawania emocji dla wideo

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostanie wycofany w dniu 15 lutego 2019 r. Rozpoznawanie emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/). 

W tym przewodniku pokazano, jak wywołać interfejs API rozpoznawania emocji dla wideo. Przykłady napisano w języku C# z użyciem biblioteki klienckiej interfejsu API rozpoznawania emocji dla wideo.

### <a name="Prep">Przygotowywanie</a>
Aby można było użyć interfejsu API rozpoznawania emocji dla wideo, konieczne będzie wideo z zarejestrowanymi osobami, najlepiej z twarzami zwróconymi w stronę kamery.

### <a name="Step1">Krok 1. Autoryzacja wywołania interfejsu API</a>
Każde wywołanie interfejsu API rozpoznawania emocji dla wideo wymaga klucza subskrypcji. Ten klucz musi zostać albo przekazany przez parametr ciągu zapytania, albo określony w nagłówku żądania. Aby przekazać klucz subskrypcji w ciągu zapytania, użyj jako przykładu adresu URL żądania do interfejsu API rozpoznawania emocji dla wideo:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

Klucz subskrypcji możesz też określić nagłówku żądania HTTP:

```
ocp-apim-subscription-key: <Your subscription key>
```

Jeśli korzystasz z biblioteki klienckiej, klucz subskrypcji jest przekazywany za pośrednictwem konstruktora klasy VideoServiceClient. Na przykład:

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Aby uzyskać klucz subskrypcji, zobacz [Subskrypcje] (https://azure.microsoft.com/try/cognitive-services/).

### <a name="Step2">Krok 2. Przekazywanie wideo do usługi i sprawdzanie stanu</a>
Najbardziej podstawowym sposobem wykonywania dowolnych wywołań interfejsu API rozpoznawania emocji dla wideo jest bezpośrednie przekazanie wideo. Robi się to przez wysłanie żądania „POST” z typem zawartości application/octet-stream wraz z danymi odczytanymi z pliku wideo. Maksymalny rozmiar wideo to 100 MB.

Gdy używana jest biblioteka kliencka, stabilizacja za pomocą przekazywania jest realizowana przez przekazanie obiektu strumienia. Zapoznaj się z poniższym przykładem:

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Pamiętaj, że metoda CreateOperationAsync klasy VideoServiceClient jest asynchroniczna. Także metoda wywołująca powinna być oznaczona jako asynchroniczna, aby można było użyć klauzuli await.
Jeśli wideo jest już w Internecie i ma publiczny adres URL, dostęp do interfejsu API rozpoznawania emocji dla wideo jest możliwy przez podanie adresu URL. W tym przykładzie treścią żądania będzie ciąg JSON, który zawiera adres URL.

Gdy używana jest biblioteka kliencka, stabilizacji za pomocą adresu URL może być łatwo zrealizowana przez użycie innego przeciążenia metody CreateOperationAsync.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Ta metoda przekazywania będzie taka sama dla wszystkich wywołań interfejsu API rozpoznawania emocji dla wideo.

Po przekazaniu wideo następną operacją do wykonania jest sprawdzenie jego stanu. Ponieważ pliki wideo są zwykle większe i bardziej zróżnicowane niż inne pliki, użytkownicy mogą spodziewać się na tym etapie długiego czasu przetwarzania. Czas zależy od rozmiaru i długość pliku.

Gdy używana jest biblioteka kliencka, możesz pobrać stan i wynik operacji, stosując metodę GetOperationResultAsync.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Zazwyczaj po stronie klienta okresowo pobierany jest stan operacji do czasu, aż będzie on miał wartość „Succeeded” lub „Failed”.

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

Gdy stanem elementu VideoOperationResult będzie „Succeeded”, wynik będzie można pobrać przez rzutowanie elementu VideoOperationResult na element VideoOperationInfoResult<VideoAggregateRecognitionResult> i uzyskanie dostępu do pola ProcessingResult.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">Krok 3. Pobieranie i interpretowanie danych wyjściowych JSON rozpoznawania i śledzenia emocji</a>

Wynik wyjściowy zawiera metadane w formacie JSON dotyczące twarzy w pliku.

Zgodnie z opisem w kroku 2, dane wyjściowe JSON są dostępne w polu ProcessingResult elementu OperationResult, gdy jego stan to „Succeeded”.

Dane JSON wykrywania i śledzenia twarzy zawierają następujące atrybuty:

Atrybut | Opis
-------------|-------------
Version | Odnosi się do wersji danych JSON interfejsu API rozpoznawania emocji dla wideo.
Skala czasu | Liczba „taktów” na sekundę wideo.
Offset  |Przesunięcie czasu dla sygnatur czasowych. W wersji 1.0 interfejsu API rozpoznawania emocji dla wideo ta wartość będzie zawsze równa 0. W przyszłych scenariuszach ta wartość może ulec zmianie.
Framerate | Liczba klatek na sekundę w wideo.
Fragments   | Metadane są cięte na różne mniejsze kawałki nazywane fragmentami. Każdy fragment zawiera rozpoczęcie, czas trwania, wartość interwału i zdarzenia.
Start   | Czas rozpoczęcia pierwszego zdarzenia, w taktach.
Duration |  Długość fragmentu, w taktach.
Interval |  Długość każdego zdarzenia w obrębie fragmentu, w taktach.
Zdarzenia  | Tablica zdarzeń. Zewnętrzna tablica reprezentuje jeden interwał czasu. Wewnętrzna tablica składa się z 0 lub większej liczby zdarzeń, które wystąpiły w danym momencie.
windowFaceDistribution |    Procent twarzy z określoną emocją podczas zdarzenia.
windowMeanScores |  Średnia ocena każdej emocji dla twarzy na obrazie.

Przyczyną sformatowania kodu JSON w ten sposób jest przygotowanie interfejsów API pod kątem przyszłych scenariuszy, w których może być ważne szybkie pobieranie metadanych i zarządzanie dużym strumieniem wyników. To formatowanie korzysta z techniki fragmentacji (umożliwia podział metadanych na porcje bazujące na czasie, pozwalając pobrać tylko potrzebne dane) i segmentacji (umożliwia podział zdarzeń, gdy staną się zbyt duże). W transformacji danych może pomóc kilka prostych obliczeń. Przykładowo: zdarzenie rozpoczęło się w punkcie 6300 (taktów), skala czasu to 2997 (taktów/s), a szybkość klatek to 29,97 (klatki/s):

*   Pocżątek/skala czasu= 2,1 s
*   Liczba sekund x (szybkość klatek/skala czasu) = 63 klatki

Poniżej przedstawiono prosty przykład wyodrębniania danych JSON do formatu ramek na potrzeby wykrywania i śledzenia twarzy:

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Ponieważ emocje z czasem ulegają wygładzeniu, jeśli kiedykolwiek zdecydujesz się utworzyć wizualizację do nałożenia uzyskanych wyników na wideo, odejmij 250 milisekund od podanych sygnatur czasowych.

### <a name="Summary">Podsumowanie</a>
W tym przewodniku przedstawiono funkcje interfejsu API rozpoznawania emocji dla wideo: jak przekazywać wideo, sprawdzać jego stan i pobierać metadane rozpoznawania emocji.

Aby uzyskać więcej szczegółowych informacji o interfejsie API, zobacz przewodnik z dokumentacją interfejsu API „[Emotion API for Video Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)” (Dokumentacja interfejsu API rozpoznawania emocji dla wideo).

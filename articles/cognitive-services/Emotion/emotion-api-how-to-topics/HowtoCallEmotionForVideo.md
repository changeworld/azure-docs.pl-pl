---
title: Wywołania emocji interfejsu API dla wideo | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wywołać interfejs API rozpoznawania emocji — warstwa wideo w usługach kognitywnych.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 0875013b2061a84e3e23ae90c1106382672fdca6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347581"
---
# <a name="how-to-call-emotion-api-for-video"></a>Wywoływanie interfejsu API emocji wideo

> [!IMPORTANT]
> Interfejs API podglądu kończy się 30 października 2017 r. Testowanie nowego [wideo indeksatora interfejsu API w wersji zapoznawczej](https://azure.microsoft.com/services/cognitive-services/video-indexer/) można łatwo wyodrębnić szczegółowych informacji z wideo i celu ułatwienia pracy funkcję odnajdowania zawartości, takich jak wyniki wyszukiwania został określony poprzez wykrycie słowa rozmowy, kroje znaków i emocji. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

W tym przewodniku pokazano, jak wywołać interfejs API rozpoznawania emocji — warstwa wideo. Przykłady są zapisywane w języku C# przy użyciu interfejsu API rozpoznawania emocji — warstwa wideo klienta biblioteki.

### <a name="Prep">Przygotowanie</a> 
Aby korzystać z interfejsu API rozpoznawania emocji — warstwa wideo, konieczne będzie wideo, zawierającą osoby, najlepiej wideo, gdy osoby stoją aparatu.

### <a name="Step1">Krok 1: Autoryzowanie wywołania interfejsu API</a> 
Każdego wywołania funkcji API rozpoznawania emocji — warstwa wideo wymaga klucza subskrypcji. Ten klucz musi być przekazywane przy użyciu parametru ciągu zapytania albo określonym w nagłówku żądania. Aby przekazać klucz subskrypcji przy użyciu ciągu zapytania, można znaleźć adres URL żądania poniżej dla interfejsu API rozpoznawania emocji — warstwa wideo na przykład:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

Alternatywnie można również określić w nagłówku żądania HTTP klucza subskrypcji:

```
ocp-apim-subscription-key: <Your subscription key>
```

Podczas korzystania z biblioteki klienta, klucz subskrypcji jest przekazywana w konstruktorze klasy VideoServiceClient. Na przykład:

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Aby uzyskać klucz subskrypcji, zobacz [subskrypcji] (https://azure.microsoft.com/try/cognitive-services/). 

### <a name="Step2">Krok 2: Przekazywanie pliku wideo do usługi i sprawdź stan</a>
Najprostszym sposobem wykonaj jedną z interfejsu API rozpoznawania emocji — warstwa dla wywołań wideo jest bezpośrednio przekazywanie pliku wideo. Jest to realizowane przez wysłanie żądania "POST" z typem zawartości application/octet-stream oraz dane odczytane z pliku wideo. Maksymalny rozmiar wideo wynosi 100MB.

Za pomocą biblioteki klienta, stabilizacji za pomocą przekazywanie odbywa się przez przekazywanie obiektu strumienia. Zobacz poniższy przykład:

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Należy pamiętać, że metoda CreateOperationAsync VideoServiceClient jest asynchroniczne. Wywołanie metody powinien być oznaczony jako async oraz aby można było używać klauzuli await.
Jeśli wideo jest już w sieci web i publiczny adres URL, podając adres URL są dostępne API rozpoznawania emocji — warstwa wideo. W tym przykładzie treści żądania będzie ciągu JSON, który zawiera adres URL.

Za pomocą biblioteki klienta, stabilizacji za pomocą adresu URL można łatwo można wykonać przy użyciu innego przeciążenia metody CreateOperationAsync.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Ta metoda przekazywania będzie taka sama dla wszystkich API rozpoznawania emocji — warstwa dla wywołań wideo. 

Zostały przekazane wideo, następnej operacji, które można wykonać po sprawdź jej status. Ponieważ pliki wideo są zwykle większych i bardziej różnorodnych niż pozostałe pliki, użytkownicy mogą oczekiwać długi czas przetwarzania w tym kroku. Czas zależy od rozmiaru i długość pliku.

Za pomocą biblioteki klienta, możesz pobrać stan operacji i wyników przy użyciu metody GetOperationResultAsync.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Zwykle po stronie klienta należy okresowo pobrać stan operacji, dopóki nie zostanie wyświetlony stan "Powodzenie" lub "Niepowodzenie".

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

Gdy stan VideoOperationResult jest wyświetlana jako "Powiodło się" wynik mogą zostać pobrane przez rzutowanie VideoOperationResult do VideoOperationInfoResult<VideoAggregateRecognitionResult> i uzyskiwania dostępu do pola ProcessingResult.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">Krok 3: Pobieranie i opis rozpoznawania emocji i śledzenie danych wyjściowych JSON</a>

Wynik danych wyjściowych zawiera metadanych z powierzchni w danym pliku w formacie JSON.

Zgodnie z objaśnieniem w kroku 2, dane wyjściowe JSON jest dostępna w polu ProcessingResult klasy OperationResult, gdy jego stan jest wyświetlany jako "Powodzenie".

Wykrywanie twarzy na obrazie i śledzenie JSON zawiera następujące atrybuty:

Atrybut | Opis
-------------|-------------
Wersja | Odwołuje się do wersji interfejsu API rozpoznawania emocji — warstwa dla formatu JSON wideo.
Skali czasu | "Impulsów" na sekundę wideo.
Przesunięcie  |Przesunięcie czasu sygnatur czasowych. W wersji 1.0 API rozpoznawania emocji — warstwa filmy ta będzie zawsze równa 0. W przyszłości obsługiwane scenariusze i może zmienić tę wartość.
Szybkość klatek | Klatek na sekundę wideo.
Fragmenty   | Metadane zostanie obcięty w na różnych mniejsze części o nazwie fragmenty. Każdy fragmentu zawiera rozpoczęcia, czas trwania, liczba interwałów i zdarzenia.
Uruchamianie   | Godzina rozpoczęcia pierwsze zdarzenie, znaczniki osi.
Czas trwania |  Długość w taktach fragmentu.
Interwał |  Długość każdego zdarzenia w obrębie fragmentu, w taktach.
Zdarzenia  | Tablica zdarzeń. Zewnętrzne tablicy reprezentuje jeden interwał czasu. Wewnętrzny tablicy składa się z 0 lub więcej zdarzeń, które wystąpiły w danym momencie.
windowFaceDistribution |    Procent kroje ma konkretnego emocji podczas zdarzenia.
windowMeanScores |  Średnie wyniki dla każdego emocji kroje w obrazie.

Przyczyna formatowania JSON w ten sposób jest ustawienie interfejsów API dla przyszłych scenariuszy, której będzie on potrzebny do pobierania metadanych szybko i zarządzania nimi dużych strumień wyników. Formatowanie jest przy użyciu technik fragmentacji (umożliwiając podzielić metadanych na podstawie czasu części, której można pobrać tylko potrzebnych) i segmentacji (dzięki czemu można podzielić zdarzenia, jeśli otrzymują zbyt duży). Niektórych prostych obliczeń może pomóc przekształcania danych. Na przykład, jeśli zdarzenie rozpoczęty o godzinie 6300 (znaczniki) z skali czasu 2997 (Takty/s) i szybkość klatek z 29,97 (ramek na sekundę), następnie:

*   Start/skali czasu = sekund 2.1
*   Sekund x (szybkość klatek/skali czasu) = 63 ramki

Poniżej przedstawiono prosty przykład wyodrębniania w formacie JSON na format ramki wykrywania twarzy na obrazie i śledzenia:

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Ponieważ emocji są wygładzane wraz z upływem czasu, jeśli kiedykolwiek utworzyć wizualizację do nakładki wyniki w górnej części oryginalnego wideo, odjąć 250 milisekund z podanych sygnatur czasowych.

### <a name="Summary">Podsumowanie</a>
W tym przewodniku uzyskanych o funkcje API rozpoznawania emocji — warstwa wideo: w jaki sposób można przekazać pliku wideo, sprawdź jej stan, pobierania metadanych rozpoznawania emocji.

Aby uzyskać więcej informacji o szczegóły interfejsu API, zobacz Podręcznik interfejsu API "[API rozpoznawania emocji — warstwa wideo odwołania](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)".

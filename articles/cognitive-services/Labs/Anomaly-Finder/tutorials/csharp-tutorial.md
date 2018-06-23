---
title: Aplikacja wykrywania C# anomalii — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Poznaj aplikacji C#, który używa interfejsu API wykrywania anomalii w kognitywnych usług firmy Microsoft. Wyślij oryginalnego punkty danych do interfejsu API i uzyskać oczekiwaną wartością i punkty anomalii.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 2e4100fd7d8e85a6b103c31000176aaaeb3d7151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348192"
---
# <a name="anomaly-detection-c-application"></a>Anomalii wykrywania aplikację języka C#

Poznaj podstawowe aplikacji systemu Windows, wykorzystujący interfejs API wykrywania anomalii do wykrywania anomalii z danych wejściowych. Przykład przesyła dane serii czas do interfejsu API wykrywania anomalii kluczem subskrypcji, a następnie pobiera wszystkie punkty anomalii i oczekiwana wartość dla każdego punktu danych z interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Przykład został opracowany dla programu .NET Framework za pomocą [programu Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie wykrywania anomalii i uzyskiwanie klucza subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Pobierz i skorzystaj z przykładu

Można sklonować wykrywania anomalii Przykładowa aplikacja na komputerze z [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Zainstaluj przykładu

Otwórz Sample\AnomalyDetectionSample.sln w pulpicie GitHub.

<a name="Step2"></a>
### <a name="build-the-example"></a>Przykład kompilacji

Naciśnij klawisze Ctrl + Shift + B, lub kliknij menu wstążki kompilacji, a następnie wybierz Kompiluj rozwiązanie.

<a name="Step3"></a>
### <a name="run-the-example"></a>Uruchomić przykład

1. Po ukończeniu kompilacji, naciśnij klawisz **F5** lub kliknij przycisk **Start** menu wstążki do uruchamiania w przykładzie.
2. Zlokalizuj wykrywania anomalii okna interfejsu użytkownika z pole edycji tekstu odczytywania "{your_subscription_key}".
3. Zastąp plik request.json, który zawiera dane przykładowe z użyciem własnych danych, a następnie kliknij przycisk "Wyślij". Microsoft odbiera dane, przekazywanie i używać ich do wykrycia punktów anomalii między następnie. Zaktualizowane dane nie zostaną utrwalone w serwera firmy Microsoft. Do wykrywania anomalii punktu ponownie, możesz należy przekazać dane ponownie.
4. Jeśli dane są dobrym, będą dostępne wynik wykrywania anomalii w polu "Odpowiedzi". W przypadku błędu informacje o błędzie będzie wyświetlane w tym polu odpowiedzi.

<a name="Review"></a>
### <a name="read-the-result"></a>Wynik do odczytu

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Przejrzyj i Dowiedz się więcej

Teraz, gdy masz uruchomioną aplikację teraz Przejrzyj jak przykładową aplikację integruje się z technologii usług kognitywnych. W tym kroku zostanie ułatwiają kontynuować tworzenie na tej aplikacji albo utworzyć własną aplikację przy użyciu wykrywania anomalii w usłudze Microsoft.

Ta aplikacja przykład korzysta z interfejsu API Restful wykrywania anomalii punktu końcowego.

Przegląd interfejsu API Restful pobiera używania w przykładowej aplikacji, Przyjrzyjmy się fragment kodu z **AnomalyDetectionClient.cs**. Plik zawiera komentarze w kodzie wskazujący "Klucz przykładowy kod URUCHAMIA tutaj" i "Klucz przykładowy kod kończy się tutaj" Aby zlokalizować kod, który wstawki przedstawionym poniżej.

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
**Request(...)**  Poniższego kodu, fragment kodu przedstawia sposób użycia HttlClient przesłać subskrypcji klucza i danych punkty do punktu końcowego interfejsu API wykrywania anomalii.

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)

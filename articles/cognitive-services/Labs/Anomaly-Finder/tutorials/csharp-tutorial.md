---
title: Anomalii wykrywania języka C# aplikacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Zapoznaj się z aplikacją C#, która korzysta z interfejsu API wykrywania anomalii w usługach Microsoft Cognitive Services. Wyślij oryginalnego punkty danych do interfejsu API i uzyskać oczekiwaną wartość i anomalii punktów.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 7d4f6a12c94620f447b5d6df4d7715d32eac2d98
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "41988456"
---
# <a name="anomaly-detection-c-application"></a>Anomalii wykrywania aplikacji języku C#

Poznaj podstawowe aplikacji Windows, która używa interfejsu API wykrywania anomalii w celu wykrycia anomalii w danych wejściowych. Przykład przesyła danych szeregów czasowych do interfejsu API wykrywania anomalii z kluczem subskrypcji, a następnie pobiera wszystkie punkty anomalii i oczekiwana wartość dla każdego punktu danych z interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Przykład został opracowany dla programu .NET Framework za pomocą [programu Visual Studio 2017, wersja Community](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie do wykrywania anomalii i Uzyskaj klucz subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Pobierz i skorzystaj z tego przykładu

Można sklonować wykrywania anomalii przykładowej aplikacji na komputer z [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Przykład instalacji

Otwórz Sample\AnomalyDetectionSample.sln w pulpit usługi GitHub.

<a name="Step2"></a>
### <a name="build-the-example"></a>Budowanie przykładu

Naciśnij klawisze Ctrl + Shift + B, lub kliknij polecenie kompilacji w menu wstążki, a następnie wybierz rozwiązanie kompilacji.

<a name="Step3"></a>
### <a name="run-the-example"></a>Uruchomić przykład

1. Po ukończeniu kompilacji, naciśnij przycisk **F5** lub kliknij przycisk **Start** w menu wstążki, aby uruchomić przykład.
2. Znajdź okno interfejsu użytkownika wykrywanie anomalii przy użyciu pole edycji tekstu odczytu "{your_subscription_key}".
3. Zastąp plik request.json, który zawiera przykładowe dane z użyciem własnych danych, a następnie kliknij przycisk "Wyślij". Firma Microsoft odbierze danych, przekazywanie i ich używać, aby wykryć wszystkie punkty anomalii między następnie. Dane, które ładujesz nie zostaną utrwalone w server firmy Microsoft. Do wykrywania anomalii punkt ponownie, możesz należy przekazać dane ponownie.
4. Jeśli dane są dobre, znajdziesz wynik wykrywania anomalii w polu "Response". Jeśli wystąpi jakiś błąd, informacje o błędzie będą wyświetlane w tym polu odpowiedzi.

<a name="Review"></a>
### <a name="read-the-result"></a>Przeczytaj wynik

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Przejrzyj i Dowiedz się więcej

Skoro masz uruchomioną aplikację, Podsumujmy, jak przykładowa aplikacja integruje się z technologii usług Cognitive Services. W tym kroku ułatwi tworzenie własnych aplikacji przy użyciu wykrywania anomalii w usłudze firmy Microsoft lub Kontynuuj tworzenie na tę aplikację.

Ta przykładowa aplikacja korzysta z interfejsu API Restful wykrywania anomalii punktu końcowego.

Przegląd, jak interfejs API Restful zostanie wykorzystany w przykładowej aplikacji, Przyjrzyjmy się fragment kodu z **AnomalyDetectionClient.cs**. Plik zawiera komentarze w kodzie wskazujący "Klucz przykładowy kod URUCHAMIA tutaj" i "Klucz przykładowy kod kończy się tutaj" Aby zlokalizować kod, który fragmenty przedstawionym poniżej.

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
### <a name="request"></a>**Żądanie**
Poniższy fragment kodu przedstawia sposób użycia klasy HttpClient przesłać Twojej subskrypcji klucza i danych punktów do punktu końcowego interfejsu API wykrywania anomalii.

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

---
title: 'Samouczek: wykrywanie anomalii, C#'
titlesuffix: Azure Cognitive Services
description: Zapoznaj się z aplikacją w języku C#, która korzysta z interfejsu API wykrywania anomalii. Wyślij oryginalne punkty danych do interfejsu API i uzyskaj oczekiwaną wartość oraz punkty anomalii.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 64183ea09203ea965bb889fe4741fdaf4dbd1e02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709941"
---
# <a name="tutorial-anomaly-detection-with-c-application"></a>Samouczek: wykrywanie anomalii przy użyciu aplikacji języka C#

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Poznaj podstawową aplikację systemu Windows, która używa interfejsu API wykrywania anomalii w celu wykrycia anomalii w danych wejściowych. Przykład przesyła dane szeregów czasowych do interfejsu API wykrywania anomalii za pomocą klucza subskrypcji, a następnie pobiera wszystkie punkty anomalii i oczekiwaną wartość dla każdego punktu danych z interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Przykład został opracowany dla programu .NET Framework za pomocą [programu Visual Studio 2017 w wersji Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie wykrywania anomalii i uzyskiwanie klucza subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Pobieranie przykładu i korzystanie z niego

Można sklonować przykładową aplikację wykrywania anomalii na swój komputer z usługi [GitHub](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Instalowanie przykładu

W programie GitHub Desktop otwórz plik Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>Kompilowanie przykładu

Naciśnij klawisze Ctrl+Shift+B lub kliknij polecenie Kompiluj w menu wstążki, a następnie wybierz pozycję Kompiluj rozwiązanie.

<a name="Step3"></a>
### <a name="run-the-example"></a>Uruchamianie przykładu

1. Po ukończeniu kompilacji naciśnij przycisk **F5** lub kliknij przycisk **Start** w menu wstążki, aby uruchomić przykład.
2. Znajdź okno interfejsu użytkownika wykrywania anomalii przy użyciu pola edycji tekstu zawierającego „{Twój_klucz_subskrypcji}”.
3. Zastąp plik request.json, który zawiera przykładowe dane, własnymi danymi, a następnie kliknij przycisk Wyślij. Firma Microsoft odbierze przekazane dane i użyje ich, aby wykryć między nimi wszelkie punkty anomalii. Dane, które ładujesz, nie zostaną utrwalone na serwerze firmy Microsoft. Aby ponownie wykryć punkt anomalii, musisz ponownie przekazać dane.
4. Jeśli dane są dobre, znajdziesz wynik wykrywania anomalii w polu Odpowiedź. Jeśli wystąpi jakikolwiek błąd, informacje o błędzie zostaną również wyświetlone w polu odpowiedzi.

<a name="Review"></a>
### <a name="read-the-result"></a>Odczytywanie wyniku

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Przeglądanie i nauka

Teraz, gdy masz uruchomioną aplikację, sprawdźmy, jak przykładowa aplikacja integruje się z technologią usług Cognitive Services. Ten krok ułatwi zarówno kontynuację tworzenia na podstawie tej aplikacji, jak i opracowanie własnej aplikacji przy użyciu wykrywania anomalii firmy Microsoft.

Ta przykładowa aplikacja korzysta z punktu końcowego interfejsu API Restful wykrywania anomalii.

Sprawdzając, jak interfejs API Restful zostanie wykorzystany w przykładowej aplikacji, przyjrzyjmy się fragmentowi kodu z pliku **AnomalyDetectionClient.cs**. Plik zawiera komentarze w kodzie wskazujące „PRZYKŁADOWY KOD KLUCZA ZACZYNA SIĘ TUTAJ” i „PRZYKŁADOWY KOD KLUCZA KOŃCZY SIĘ TUTAJ”pomagające zlokalizować fragmenty kodu przedstawione poniżej.

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
Poniższy fragment kodu przedstawia sposób użycia klasy HttpClient do przesyłania klucza subskrypcji i punktów danych do punktu końcowego interfejsu API wykrywania anomalii.

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

---
title: Sposób użycia interfejsu API Finder anomalii języku C# — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu języka C# i interfejsu API Finder anomalii w usługach kognitywnych.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 867ce4d0262c94de8da0dadeb8de71c28a8295d5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348173"
---
# <a name="use-the-anomaly-finder-api-with-c"></a>Za pomocą wyszukiwania anomalii interfejsu API języka C#

Ten artykuł zawiera informacje i przykłady kodu w celu szybkiego rozpoczęcie pracy przy użyciu interfejsu API Finder anomalii w języku C# w zadania pobierania wyników anomalii czasu serii danych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-c"></a>Pobieranie punktów anomalii z interfejsem API Finder anomalii przy użyciu języka C#

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data-points"></a>Przykład czasu punktów danych w serii.

Przykład czasu punktów danych serii ma następującą składnię.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-c-example"></a>Analizowanie danych i uzyskać anomalii punktów C# przykład

Dostępne są następujące kroki w przykładzie.

1. Utwórz nowe rozwiązanie konsoli w programie Visual Studio.
2. Zastąp plik Program.cs następującym kodem, a następnie dodaj odwołanie do System.Net.Http.
3. Zastąp `[YOUR_SUBSCRIPTION_KEY]` wartości z klucza ważnej subskrypcji.
4. Zastąp `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` z punktami danych.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading.Tasks;

namespace Console
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

        const string endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

        // Replace the request data with your real data.
        const string requestData = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";
        static void Main(string[] args)
        {
            var match = Regex.Match(endpoint, "(?<BaseAddress>https://[^/]+)(?<Url>/*.+)");
            if (match.Success)
            {
                var res = Request(
                    match.Groups["BaseAddress"].Value,
                    match.Groups["Url"].Value,
                    subscriptionKey,
                    requestData).Result;
                System.Console.Write(res);
            }
            else
            {
                System.Console.Write("Incorrect endpoint.");
            }
        }

        /// <summary>
        /// Call API to detect the anomaly points
        /// </summary>
        /// <param name="baseAddress">Base address of the API endpoint.</param>
        /// <param name="endpoint">The endpoint of the API</param>
        /// <param name="subscriptionKey">The subscription key applied  </param>
        /// <param name="requestData">The JSON string for requet data points</param>
        /// <returns>The JSON string for anomaly points and expected values.</returns>
        static async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
        {
            using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
            {
                client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

                var content = new StringContent(requestData, Encoding.UTF8, "application/json");
                var res = await client.PostAsync(endpoint, content);
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
    }
}

```

### <a name="example-response"></a>Przykładowa odpowiedź

Odpowiedź oznaczająca Powodzenie jest zwracana w formacie JSON. Poniżej przedstawiono przykład odpowiedzi.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [C# aplikacji](../tutorials/csharp-tutorial.md)

---
title: Wykrywanie anomalii aplikacji Javascript - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Poznaj aplikacji sieci Web, wykorzystujący interfejs API wykrywania anomalii w kognitywnych usług firmy Microsoft. Wyślij oryginalnego punkty danych do interfejsu API i uzyskać oczekiwaną wartością i punkty anomalii.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 42c3941a05efe8b74f818cd99f3606b3073892a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348176"
---
# <a name="anomaly-detection-javascript-application"></a>Aplikacja Javascript wykrywania anomalii

Poznaj aplikacji sieci Web, która używa interfejsu API REST wykrywania anomalii do wykrywania anomalii. Przykład przesyła dane serii czas do interfejsu API wykrywania anomalii kluczem subskrypcji, a następnie pobiera wszystkie punkty anomalii i oczekiwanej wartości dla każdego punktu danych z interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

W tym samouczku został opracowany za pomocą edytora zwykłego tekstu.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie wykrywania anomalii i uzyskiwanie klucza subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Pobierz i skorzystaj z przykładu

Ten samouczek zawiera dwa scenariusze wykrywania anomalii danych serii czasu. Zacznijmy od początku.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Pobierz samouczek projektu

Klonowanie [kognitywnych samouczek wykrywania anomalii JavaScript usług](https://github.com/MicrosoftAnomalyDetection/javascript-sample), lub Pobierz plik zip i wyodrębnić je do pustego katalogu.

<a name="Step2"></a>
### <a name="run-the-example"></a>Uruchomić przykład

Istnieją dwa scenariusze, możesz spróbować przykładzie.
1. Umieść Twojej **klucza subskrypcji** w polu klucza subskrypcji na wykrywanie funkcji na anomalydetection.html.
2. Umieść punkt końcowy interfejsu API wykrywania anomalii i sprawdź, czy używasz poprawny region w regionie subskrypcji.
3. Otwórz **anomalydetection.html** pliku w przeglądarce sieci Web.

**Scenariusz 1 Wykryj tygodniowy czas serii danych**
1. W polu okresu danych wejściowych okres **7**. 
2. Zamienić przykładowe dane z Twojego co tydzień czasu serii punktów danych (Json) w polu punkty, lub użyj przykładowych danych bezpośrednio.
3. Kliknij przycisk wykrywania anomalii i sprawdź wynik w polu tekstowym odpowiedzi prawo.

**Scenariusz 2 Wykryj danych w serii. czas bez kropki**
1. Pozostaw pusty w okresie czasu usterki, założono Nieznana okres szeregów czasowych.
2. Przy użyciu tych samych danych serii czas, jak w scenariuszu 1.
3. Kliknij przycisk wykrywania anomalii i sprawdź pole okres, w polu tekstowym odpowiedzi prawo.

<a name="Step3"></a>
### <a name="read-the-result"></a>Wynik do odczytu

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Przejrzyj i Dowiedz się więcej

Teraz otrzymasz działającej aplikacji. Przejrzyj umożliwia jak przykładową aplikację integruje się z technologii usług kognitywnych. Ten krok ułatwi o kontynuowanie, korzystając z tej aplikacji albo utworzyć własną aplikację przy użyciu wykrywania anomalii w usłudze Microsoft.
Ta aplikacja przykład korzysta z interfejsu API Restful wykrywania anomalii punktu końcowego.
Przegląd interfejsu API Restful pobiera używania w przykładowej aplikacji, Przyjrzyjmy się fragment kodu z anomalydetection.html.
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)

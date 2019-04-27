---
title: 'Samouczek: wykrywanie anomalii przy użyciu języka Javascript'
titlesuffix: Azure Cognitive Services
description: Zapoznaj się z aplikacją internetową, która używa interfejsu API wykrywania anomalii. Wyślij oryginalne punkty danych do interfejsu API i uzyskaj oczekiwaną wartość oraz punkty anomalii.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 9e66b24987b2318f3022404d951fbb911e7b592d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709872"
---
# <a name="tutorial-anomaly-detection-with-javascript-application"></a>Samouczek: wykrywanie anomalii przy użyciu aplikacji Javascript

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Zapoznaj się z aplikacją internetową, która używa interfejsu API REST do wykrywania anomalii. Przykład przesyła dane szeregów czasowych do interfejsu API wykrywania anomalii za pomocą klucza subskrypcji, a następnie pobiera z interfejsu API wszystkie punkty anomalii i oczekiwaną wartość dla każdego punktu danych.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Ten samouczek został opracowany przy użyciu prostego edytora tekstów.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie wykrywania anomalii i uzyskiwanie klucza subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Pobieranie przykładu i korzystanie z niego

Ten samouczek obejmuje dwa scenariusze wykrywania anomalii w danych szeregów czasowych. Zacznijmy.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Pobieranie projektu samouczka

Sklonuj [samouczek wykrywania anomalii w języku Javascript dla usługi Cognitive Services](https://github.com/MicrosoftAnomalyDetection/javascript-sample) lub pobierz plik zip i wyodrębnij jego zawartość do pustego katalogu.

<a name="Step2"></a>
### <a name="run-the-example"></a>Uruchamianie przykładu

Istnieją dwa scenariusze wypróbowywania przykładu.
1. Podaj swój **klucz subskrypcji** w polu klucza subskrypcji w funkcji detect w pliku anomalydetection.html.
2. Podaj punkt końcowy interfejsu API wykrywania anomalii i sprawdź w regionie subskrypcji, czy używasz poprawnego regionu.
3. Otwórz plik **anomalydetection.html** w przeglądarce internetowej.

**Scenariusz 1. Wykrywanie danych tygodniowych szeregów czasowych**
1. W polu Period (Okres) wprowadź okres **7**. 
2. Zastąp przykładowe dane własnymi punktami danych szeregu czasowego (Json) w polu Points (Punkty) lub bezpośrednio użyj przykładowych danych.
3. Kliknij przycisk Anomaly Detection (Wykrywanie anomalii) i sprawdź wynik w polu tekstowym Response (Odpowiedź) po prawej.

**Scenariusz 2. Wykrywanie danych szeregów czasowych bez okresu**
1. Pozostaw pusty okres w polu Period (Pole). Oznacza to, że okres szeregu czasowego jest nieznany.
2. Użyte zostaną te same dane szeregu czasowego co w scenariuszu 1.
3. Kliknij przycisk Anomaly Detection (Wykrywanie anomalii) i sprawdź pole Period (Okres) w polu tekstowym Response (Odpowiedź) po prawej.

<a name="Step3"></a>
### <a name="read-the-result"></a>Odczytywanie wyniku

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Przeglądanie i nauka

Teraz masz działającą aplikację. Zobaczmy, jak przykładowa aplikacja integruje się z technologią Cognitive Services. Ten krok ułatwi rozbudowę tej aplikacji lub utworzenie własnej aplikacji przy użyciu funkcji wykrywania anomalii firmy Microsoft.
Ta przykładowa aplikacja korzysta z punktu końcowego interfejsu API Restful wykrywania anomalii.
Aby zobaczyć, jak interfejs API Restful jest używany w przykładowej aplikacji, zerknijmy na fragment kodu z pliku anomalydetection.html.
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

---
title: Wykrywanie anomalii aplikacji Javascript — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Zapoznaj się z aplikacją sieci Web, która używa interfejsu API wykrywania anomalii w usługach Microsoft Cognitive Services. Wyślij oryginalnego punkty danych do interfejsu API i uzyskać oczekiwaną wartość i anomalii punktów.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 5bb123648a683454597b0561f9f82dffb70eab04
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248367"
---
# <a name="anomaly-detection-javascript-application"></a>Aplikacja Javascript wykrywania anomalii

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Zapoznaj się z aplikacji sieci Web, która używa interfejsu API REST wykrywania anomalii do wykrywania anomalii. Przykład przesyła danych szeregów czasowych do interfejsu API wykrywania anomalii z kluczem subskrypcji, a następnie pobiera wszystkie punkty anomalii i oczekiwana wartość dla każdego punktu danych z interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

W tym samouczku został opracowany przy użyciu prostego edytora tekstów.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie do wykrywania anomalii i Uzyskaj klucz subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Pobierz i skorzystaj z tego przykładu

Ten samouczek zawiera dwa scenariusze wykrywanie anomalii w czasie serii danych. Zaczynajmy.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Pobierz samouczek projekt

Klonuj [samouczek wykrywania anomalii w usłudze Cognitive usługi języka JavaScript](https://github.com/MicrosoftAnomalyDetection/javascript-sample), lub Pobierz plik zip i wyodrębnić je do pusty katalog.

<a name="Step2"></a>
### <a name="run-the-example"></a>Uruchomić przykład

Istnieją dwa scenariusze, które możesz wypróbować przykładu.
1. Umieść swoje **klucz subskrypcji** w polu Klucz subskrypcji na wykrywanie funkcji na anomalydetection.html.
2. Umieść punkt końcowy interfejsu API wykrywania anomalii i sprawdź, czy używasz poprawny region, w obszarze subskrypcji.
3. Otwórz **anomalydetection.html** pliku w przeglądarce sieci Web.

**Scenariusz 1 Wykryj tygodniowe dane szeregów czasowych**
1. W polu okresu wprowadź okres **7**. 
2. Zastąp przykładowe dane z usługi co tydzień czas serii punktów danych (Json) w polu punkty lub bezpośrednio użyj przykładowych danych.
3. Kliknij przycisk wykrywanie anomalii i sprawdź wynik w polu tekstowym odpowiednie odpowiedzi.

**Scenariusz 2 Wykryj danych szeregów czasowych bez kropki**
1. Pozostaw pola puste w okresie czasu, założono, Nieznana okresu szeregów czasowych.
2. Przy użyciu tych samych danych szeregów czasowych, jak w scenariuszu 1.
3. Kliknij przycisk wykrywanie anomalii i sprawdź polu okres, w polu tekstowym odpowiednie odpowiedzi.

<a name="Step3"></a>
### <a name="read-the-result"></a>Przeczytaj wynik

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Przejrzyj i Dowiedz się więcej

Teraz możesz uzyskać uruchomionej aplikacji. Podsumujmy, jak przykładowa aplikacja integruje się z technologii usług Cognitive Services. W tym kroku ułatwi Kontynuuj realizowanie w tej aplikacji lub tworzenie własnych aplikacji przy użyciu wykrywania anomalii w usłudze firmy Microsoft.
Ta przykładowa aplikacja korzysta z interfejsu API Restful wykrywania anomalii punktu końcowego.
Przegląd, jak interfejs API Restful zostanie wykorzystany w przykładowej aplikacji, Przyjrzyjmy się fragment kodu z anomalydetection.html.
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

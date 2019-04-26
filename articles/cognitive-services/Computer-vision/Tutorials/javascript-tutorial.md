---
title: 'Samouczek: wykonywanie operacji na obrazach — JavaScript'
titlesuffix: Azure Cognitive Services
description: Zapoznaj się z podstawową wersją aplikacji JavaScript, która korzysta z interfejsu API przetwarzania obrazów w usługach Azure Cognitive Services. Wykonaj optyczne rozpoznawanie znaków (OCR), twórz miniatury i korzystaj z funkcji wizualnych na obrazie.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 7ac8b9e28996c14e702730b72265fd6165798227
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201296"
---
# <a name="tutorial-computer-vision-api-javascript"></a>Samouczek: interfejs API przetwarzania obrazów w języku JavaScript

W tym samouczku przedstawiono funkcje interfejsu API REST przetwarzania obrazów usług Azure Cognitive Services.

Zapoznaj się z aplikacją JavaScript, w której zastosowano interfejs API REST przetwarzania obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych na obrazie, w tym twarzy. Ten przykład umożliwia przesłanie adresu URL obrazu w celu przeprowadzenia jego analizy lub przetwarzania. Możesz zastosować ten przykład „open source” jako szablon do tworzenia własnej aplikacji w języku JavaScript, używającej interfejsu API REST przetwarzania obrazów.

Aplikacja formularza JavaScript została już napisana. Nie ma ona jednak wbudowanych żadnych funkcji przetwarzania obrazów. W tym samouczku dodasz odpowiedni kod do obsługi interfejsu API REST przetwarzania obrazów w celu uzupełnienia funkcjonalności aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Ten samouczek został opracowany przy użyciu prostego edytora tekstów.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Subskrybowanie interfejsu API przetwarzania obrazów i uzyskiwanie klucza subskrypcji 

Przed utworzeniem przykładu musisz zasubskrybować interfejs API przetwarzania obrazów, który jest częścią usług Azure Cognitive Services. Aby uzyskać szczegółowe informacje na temat subskrypcji i zarządzania kluczami, zobacz temat [Subskrypcje](https://azure.microsoft.com/try/cognitive-services/). Na potrzeby tego samouczka dopuszczalne są klucze podstawowe i pomocnicze. 

## <a name="acquire-the-incomplete-tutorial-project"></a>Uzyskiwanie niekompletnego projektu samouczka

### <a name="download-the-tutorial-project"></a>Pobieranie projektu samouczka

Sklonuj [Samouczek przetwarzania obrazów JavaScript usługi Cognitive Services](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial) lub pobierz plik ZIP i wyodrębnij go do pustego katalogu.

Jeśli wolisz korzystać z ukończonego samouczka z całym dodanym kodem, możesz użyć plików znajdujących się w folderze **Ukończone**.

## <a name="add-the-tutorial-code-to-the-project"></a>Dodawanie kodu z samouczka do projektu

Aplikacja języka JavaScript jest skonfigurowana w postaci sześciu plików HTML — po jednym dla każdej funkcji. Każdy plik służy do demonstracji innej funkcji przetwarzania obrazów (analiza, optyczne rozpoznawanie znaków itp.). Te sześć sekcji samouczka nie zależy wzajemnie od siebie, dzięki czemu kod samouczka można dodać do jednego, wszystkich sześciu lub tylko niektórych plików. Na dodatek kod samouczka można dodawać do plików w dowolnej kolejności.

Zacznijmy.

### <a name="analyze-an-image"></a>Analizowanie obrazu

Funkcja Analiza w ramach przetwarzania obrazów skanuje obraz pod kątem ponad 2000 rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Po zakończeniu funkcja Analiza zwraca obiekt JSON, który opisuje obraz między innymi za pomocą opisowych znaczników, analizy koloru i podpisów.

Aby posłużyć się funkcją Analiza w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Dodanie kodu procedury obsługi zdarzeń do przycisku formularza

Otwórz plik **analyze.html** w edytorze tekstów i w dolnej części pliku znajdź funkcję **analyzeButtonClick**.

Funkcja obsługi zdarzeń **analyzeButtonClick** czyści formularz, wyświetla obraz określony w adresie URL, a następnie wywołuje funkcję **AnalyzeImage**, aby przeprowadzić analizę obrazu.

Skopiuj i wklej następujący kod do funkcji **analyzeButtonClick**.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Funkcja **AnalyzeImage** opakowuje wywołanie interfejsu API REST do analizy obrazu. Po prawidłowym wykonaniu w określonym obszarze tekstowym (textarea) zostanie wyświetlony sformatowany obiekt JSON, a podpis pojawi się w określonym obszarze span.

Skopiuj i wklej kod funkcji **AnalyzeImage** bezpośrednio poniżej funkcji **analyzeButtonClick**.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Uruchamianie aplikacji

Zapisz plik **analyze.html** i otwórz go w przeglądarce sieci Web. W polu **Klucz subskrypcji** umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Region subskrypcji** korzystasz z poprawnego regionu. Wprowadź adres URL obrazu do analizy, a następnie kliknij przycisk **Analyze Image** (Analizuj obraz), aby przeprowadzić analizę obrazu i wyświetlić jej wynik.

### <a name="recognize-a-landmark"></a>Rozpoznawanie punktów terenowych

Funkcja Punkt terenowy przetwarzania obrazów analizuje obraz pod kątem wykrywania naturalnych i sztucznych charakterystycznych elementów krajobrazu, takich jak góry lub znane budowle. Po zakończeniu analizy funkcja Punkt terenowy zwraca obiekt JSON, który identyfikuje znalezione na obrazie charakterystyczne elementy krajobrazu.

Aby posłużyć się funkcją Punkt terenowy w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Dodanie kodu procedury obsługi zdarzeń do przycisku formularza

Otwórz plik **landmark.html** w edytorze tekstów i w dolnej części pliku znajdź funkcję **landmarkButtonClick**.

Funkcja obsługi zdarzeń **landmarkButtonClick** czyści formularz, wyświetla obraz określony w adresie URL, a następnie wywołuje funkcję **IdentifyLandmarks**, aby przeprowadzić analizę obrazu.

Skopiuj i wklej następujący kod do funkcji **landmarkButtonClick**.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Funkcja **IdentifyLandmarks** opakowuje wywołanie interfejsu API REST do analizy obrazu. Po prawidłowym wykonaniu w określonym obszarze tekstowym (textarea) zostanie wyświetlony sformatowany obiekt JSON, a podpis pojawi się w określonym obszarze span.

Skopiuj i wklej kod funkcji **IdentifyLandmarks** bezpośrednio poniżej funkcji **landmarkButtonClick**.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Uruchamianie aplikacji

Zapisz plik **landmark.html** i otwórz go w przeglądarce sieci Web. W polu **Klucz subskrypcji** umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Region subskrypcji** korzystasz z poprawnego regionu. Wprowadź adres URL obrazu do analizy, a następnie kliknij przycisk **Analizowanie obrazu**, aby przeprowadzić proces analizy obrazu i wyświetlić jego wynik.

### <a name="recognize-celebrities"></a>Rozpoznawanie celebrytów

Funkcja Osobistości w ramach przetwarzania obrazów analizuje obraz pod kątem występowania na nim sławnych osób. Po zakończeniu analizy funkcja Osobistości zwraca obiekt JSON, który identyfikuje znalezione na obrazie znane osoby.

Aby skorzystać z funkcji Osobistości w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Dodanie kodu procedury obsługi zdarzeń do przycisku formularza

Otwórz plik **celebrities.html** w edytorze tekstów i w dolnej części pliku znajdź funkcję **celebritiesButtonClick**.

Funkcja obsługi zdarzeń **celebritiesButtonClick** czyści formularz, wyświetla obraz określony w adresie URL, a następnie wywołuje funkcję **IdentifyCelebrities**, aby przeprowadzić analizę obrazu.

Skopiuj i wklej następujący kod do funkcji **celebritiesButtonClick**.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Uruchamianie aplikacji

Zapisz plik **celebrities.html** i otwórz go w przeglądarce sieci Web. W polu **Klucz subskrypcji** umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Region subskrypcji** korzystasz z poprawnego regionu. Wprowadź adres URL obrazu do analizy, a następnie kliknij przycisk **Analyze Image** (Analizuj obraz), aby przeprowadzić analizę obrazu i wyświetlić jej wynik.

### <a name="intelligently-generate-a-thumbnail"></a>Inteligentne generowanie miniatur

Funkcja Miniatura przetwarzania obrazów generuje miniaturę na podstawie obrazu. Korzystając z funkcji **Inteligentne przycinanie**, funkcja Miniatura zidentyfikuje obszar zainteresowania w obszarze i wycentruje miniaturę na ten obszar, aby wygenerować bardziej zadowalające wizualnie obrazy miniatur.

Aby skorzystać z funkcji Miniatura w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Dodanie kodu procedury obsługi zdarzeń do przycisku formularza

Otwórz plik **thumbnail.html** w edytorze tekstów i w dolnej części pliku znajdź funkcję **thumbnailButtonClick**.

Funkcja obsługi zdarzeń **thumbnailButtonClick** czyści formularz, wyświetla obraz określony w adresie URL, a następnie dwa razy wywołuje funkcję **getThumbnail**, aby utworzyć dwie miniatury — jedną z wykorzystaniem inteligentnego przycinania, a drugą bez.

Skopiuj i wklej następujący kod do funkcji **thumbnailButtonClick**.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Funkcja **getThumbnail** opakowuje wywołanie interfejsu API REST do analizy obrazu. Po pomyślnym zakończeniu miniatura będzie wyświetlana w określonym elemencie img.

Skopiuj i wklej następującą funkcję **getThumbnail** bezpośrednio poniżej funkcji **thumbnailButtonClick**.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumbnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

#### <a name="run-the-application"></a>Uruchamianie aplikacji

Zapisz plik **thumbnail.html** i otwórz go w przeglądarce sieci Web. W polu **Klucz subskrypcji** umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Region subskrypcji** korzystasz z poprawnego regionu. Wprowadź adres URL obrazu do analizy, a następnie kliknij przycisk **Generowanie miniatur**, aby przeprowadzić proces analizy obrazu i wyświetlić jego wynik.

### <a name="read-printed-text-ocr"></a>Odczytywanie tekstu drukowanego (OCR)

Funkcja optycznego rozpoznawania znaków (OCR) w ramach przetwarzania obrazów analizuje obraz tekstu drukowanego. Po zakończeniu analizy funkcja OCR zwraca obiekt JSON, który zawiera tekst i jego lokalizację na obrazie.

Aby skorzystać z funkcji OCR w aplikacji z samouczka, wykonaj następujące czynności:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>OCR — krok 1. Dodanie kodu procedury obsługi zdarzeń do przycisku formularza

Otwórz plik **ocr.html** w edytorze tekstów i w dolnej części pliku znajdź funkcję **ocrButtonClick**.

Funkcja obsługi zdarzeń **ocrButtonClick** czyści formularz, wyświetla obraz określony w adresie URL, a następnie wywołuje funkcję **ReadOcrImage**, aby przeprowadzić analizę obrazu.

Skopiuj i wklej następujący kod do funkcji **ocrButtonClick**.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Funkcja **ReadOcrImage** opakowuje wywołanie interfejsu API REST do analizy obrazu. Po pomyślnym zakończeniu w określonym obszarze textarea zostanie wyświetlony sformatowany obiekt JSON i informacja o położeniu tekstu.

Skopiuj i wklej następującą funkcję **ReadOcrImage** bezpośrednio poniżej funkcji **ocrButtonClick**.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Uruchamianie aplikacji

Zapisz plik **ocr.html** i otwórz go w przeglądarce sieci Web. W polu **Klucz subskrypcji** umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Region subskrypcji** korzystasz z poprawnego regionu. Wprowadź adres URL obrazu tekstu do analizy, a następnie kliknij przycisk **Czytanie obrazu**, aby przeprowadzić proces analizy obrazu i wyświetlić jego wynik.

### <a name="read-handwritten-text-handwriting-recognition"></a>Odczytywanie tekstu ręcznego (rozpoznawanie pisma ręcznego)

Funkcja Rozpoznawanie pisma ręcznego przetwarzania obrazów analizuje obraz tekstu ręcznego. Po zakończeniu analizy funkcja Rozpoznawanie pisma ręcznego zwraca obiekt JSON, który zawiera tekst i jego lokalizację na obrazie.

Aby skorzystać z funkcji Rozpoznawanie pisma ręcznego w aplikacji z samouczka, wykonaj następujące czynności:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Dodanie kodu procedury obsługi zdarzeń do przycisku formularza

Otwórz plik **handwriting.html** w edytorze tekstów i w dolnej części pliku znajdź funkcję **handwritingButtonClick**.

Funkcja obsługi zdarzeń **handwritingButtonClick** czyści formularz, wyświetla obraz określony w adresie URL, a następnie wywołuje funkcję **HandwritingImage**, aby przeprowadzić analizę obrazu.

Skopiuj i wklej następujący kod do funkcji **handwritingButtonClick**.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Dodanie otoki dla wywołania interfejsu API REST

Funkcja **ReadOcrImage** opakowuje dwa wywołania interfejsu API REST niezbędne do analizy obrazu. Ponieważ rozpoznawanie pisma ręcznego jest procesem czasochłonnym, jest on podzielony na dwa etapy. Pierwsze wywołanie przesyła obraz do przetworzenia; drugie pobiera wykryty tekst po zakończeniu przetwarzania.

Po pobraniu tekstu w określonym obszarze textarea zostanie wyświetlony sformatowany obiekt JSON z tekstem i informacją o jego położeniu.

Skopiuj i wklej następującą funkcję **ReadHandwrittenImage** bezpośrednio poniżej funkcji **handwritingButtonClick**.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Uruchamianie aplikacji

Zapisz plik **handwriting.html** i otwórz go w przeglądarce sieci Web. W polu **Klucz subskrypcji** umieść swój klucz subskrypcji, a następnie sprawdź, czy w polu **Region subskrypcji** korzystasz z poprawnego regionu. Wprowadź adres URL obrazu tekstu do analizy, a następnie kliknij przycisk **Czytanie obrazu**, aby przeprowadzić proces analizy obrazu i wyświetlić jego wynik.

## <a name="next-steps"></a>Kolejne kroki

- [Computer Vision API C&#35; Tutorial (Samouczek dla języka C# dotyczący interfejsu API przetwarzania obrazów)](CSharpTutorial.md)
- [Computer Vision API Python Tutorial (Samouczek dla języka Python dotyczący interfejsu API przetwarzania obrazów)](PythonTutorial.md)

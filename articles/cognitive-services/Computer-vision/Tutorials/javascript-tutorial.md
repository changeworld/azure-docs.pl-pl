---
title: Samouczek API JavaScript przetwarzania obrazów komputera | Dokumentacja firmy Microsoft
description: Poznaj podstawowe aplikacji JavaScript, wykorzystujący interfejs API wizji komputera w usługach kognitywnych firmy Microsoft. Wykonać rozpoznawanie, tworzenie miniatur i pracy z funkcjami visual obrazu.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 09/19/2017
ms.author: kefre
ms.openlocfilehash: 89bdc0524e07c1cb6a1473e0a52791fe20271e06
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347225"
---
# <a name="computer-vision-api-javascript-tutorial"></a>Samouczek API JavaScript przetwarzania obrazów komputera

W tym samouczku przedstawiono funkcje programu Microsoft kognitywnych usług komputera wizji interfejsu API REST.

Poznaj aplikacji JavaScript, która używa interfejsu API REST wizji komputera do wykonać OCR (Rozpoznawania), utworzyć przycięte inteligentnych miniatur plus wykrywanie, klasyfikowanie tag i opisano visual funkcje, takie jak kroje, obrazu. W tym przykładzie umożliwia Prześlij adres URL obrazu dla analizy lub przetwarzania. Można w tym przykładzie typu open source jako szablon do tworzenia aplikacji w języku JavaScript za pomocą interfejsu API REST wizji komputera.

Formularz JavaScript został już zapisany, ale ma żadne funkcje wizji komputera. W tym samouczku należy dodać kodu określonych do interfejsu API REST wizji komputera do ukończenia działania aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

W tym samouczku został opracowany za pomocą edytora zwykłego tekstu.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Subskrybowanie API przetwarzania obrazów komputera i uzyskiwanie klucza subskrypcji 

Przed utworzeniem w przykładzie, należy subskrybować interfejsu API wizji komputera, który jest częścią usługi kognitywnych firmy Microsoft. Dla subskrypcji i zarządzania kluczami szczegółów, zobacz [subskrypcje](https://azure.microsoft.com/try/cognitive-services/). Można użyć w tym samouczku są klucze podstawowe i pomocnicze. 

## <a name="download-the-tutorial-project"></a>Pobierz samouczek projektu

Klonowanie [kognitywnych usług JavaScript komputera wizji samouczek](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial), lub Pobierz plik zip i wyodrębnić je do pustego katalogu.

Jeśli chcesz używać Zakończono samouczek z całego kodu samouczek dodane, można użyć plików w **Ukończono** folderu.

## <a name="add-the-tutorial-code"></a>Dodaj kod samouczka

Aplikacja JavaScript jest skonfigurowane z sześciu pliki HTML, po jednym dla każdej funkcji. Każdy plik demonstruje innej funkcji wizja komputera (analiza, Rozpoznawania itp.). Sześć sekcji samouczka nie ma zależnościami, dzięki czemu można dodać samouczek kodu do jednego pliku, wszystkie pliki sześciu lub tylko kilka plików. I kod samouczka można dodać do plików w dowolnej kolejności.

Zacznijmy od początku.

## <a name="analyze-an-image"></a>Analizowanie obrazu

Funkcja analizy wizja komputera analizuje obrazu ponad 2000 rozpoznawalnych obiektów, istot życia dekoracje i akcje. Po zakończeniu analizy Analizuj zwraca obiekt JSON, który opisuje obraz z opisowe tagi, analizę koloru, podpisów i.

Aby wykonać funkcję analizy samouczek aplikacji, wykonaj następujące czynności:

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>Analizowanie krok 1: Dodaj kod obsługi zdarzeń dla przycisku formularza

Otwórz **analyze.html** plik w edytorze tekstów i Znajdź **analyzeButtonClick** funkcji w dolnej części pliku.

**AnalyzeButtonClick** funkcji procedury obsługi zdarzeń czyści formularza, wyświetla obrazu określonego w adresie URL, następnie wywołuje **AnalyzeImage** funkcji do analizowania obrazu.

Skopiuj i wklej następujący kod do **analyzeButtonClick** funkcji.

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

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>Analizowanie krok 2: Dodaj otoki dla wywołania interfejsu API REST

**AnalyzeImage** funkcja opakowuje wywołania interfejsu API REST do analizowania obrazu. Po powrocie pomyślne sformatowany analizy JSON zostaną wyświetlone w określonym textarea, a podpis pojawi się w określonym zakresie.

Skopiuj i Wklej **AnalyzeImage** funkcji kod, aby dokładnie pod **analyzeButtonClick** funkcji.

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

### <a name="analyze-step-3-run-the-application"></a>Analizowanie krok 3: uruchamianie aplikacji

Zapisz **analyze.html** pliku i otwórz go w przeglądarce sieci Web. Umieść swój klucz subskrypcji do **klucza subskrypcji** pola i upewnij się, że używasz poprawny region w **Region subskrypcji**. Wprowadź adres URL obrazu do analizy, a następnie kliknij przycisk **analizowanie obrazu** przycisk, aby przeanalizować obrazu i wyświetlić wyniki.

## <a name="recognize-a-landmark"></a>Rozpoznaje dzielnicę

Funkcja dzielnicę wizja komputera analizuje obrazu naturalne i sztuczne punkty orientacyjne, takie jak góry lub Słynne budynków. Po zakończeniu analizy dzielnicę zwraca obiekt JSON, który identyfikuje punkty orientacyjne, znalezione w obrazie.

Aby wykonać funkcję dzielnicę samouczek aplikacji, wykonaj następujące czynności:

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>Punkty krok 1: Dodaj kod obsługi zdarzeń dla przycisku formularza

Otwórz **landmark.html** plik w edytorze tekstów i Znajdź **landmarkButtonClick** funkcji w dolnej części pliku.

**LandmarkButtonClick** funkcji procedury obsługi zdarzeń czyści formularza, wyświetla obrazu określonego w adresie URL, następnie wywołuje **IdentifyLandmarks** funkcji do analizowania obrazu.

Skopiuj i wklej następujący kod do **landmarkButtonClick** funkcji.

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

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>Punkty krok 2: Dodaj otoki dla wywołania interfejsu API REST

**IdentifyLandmarks** funkcja opakowuje wywołania interfejsu API REST do analizowania obrazu. Po powrocie pomyślne sformatowany analizy JSON zostaną wyświetlone w określonym textarea, a podpis pojawi się w określonym zakresie.

Skopiuj i Wklej **IdentifyLandmarks** funkcji kod, aby dokładnie pod **landmarkButtonClick** funkcji.

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

### <a name="landmark-step-3-run-the-application"></a>Punkty krok 3: uruchamianie aplikacji

Zapisz **landmark.html** pliku i otwórz go w przeglądarce sieci Web. Umieść swój klucz subskrypcji do **klucza subskrypcji** pola i upewnij się, że używasz poprawny region w **Region subskrypcji**. Wprowadź adres URL obrazu do analizy, a następnie kliknij przycisk **analizowanie obrazu** przycisk, aby przeanalizować obrazu i wyświetlić wyniki.

## <a name="recognize-celebrities"></a>Rozpoznawanie osobistości

Funkcja znanych osób wizja komputera analizuje obrazu dla Słynne osób. Po zakończeniu analizy znanych osób zwraca obiekt JSON, który identyfikuje znanych osób, znalezione w obrazie.

Aby wykonać funkcję znanych osób samouczek aplikacji, wykonaj następujące czynności:

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>Znanych osób krok 1: Dodaj kod obsługi zdarzeń dla przycisku formularza

Otwórz **celebrities.html** plik w edytorze tekstów i Znajdź **celebritiesButtonClick** funkcji w dolnej części pliku.

**CelebritiesButtonClick** funkcji procedury obsługi zdarzeń czyści formularza, wyświetla obrazu określonego w adresie URL, następnie wywołuje **IdentifyCelebrities** funkcji do analizowania obrazu.

Skopiuj i wklej następujący kod do **celebritiesButtonClick** funkcji.

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

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>Znanych osób krok 2: Dodaj otoki dla wywołania interfejsu API REST

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

### <a name="celebrities-step-3-run-the-application"></a>Znanych osób krok 3: uruchamianie aplikacji

Zapisz **celebrities.html** pliku i otwórz go w przeglądarce sieci Web. Umieść swój klucz subskrypcji do **klucza subskrypcji** pola i upewnij się, że używasz poprawny region w **Region subskrypcji**. Wprowadź adres URL obrazu do analizy, a następnie kliknij przycisk **analizowanie obrazu** przycisk, aby przeanalizować obrazu i wyświetlić wyniki.

## <a name="intelligently-generate-a-thumbnail"></a>Inteligentnie generowanie miniatur

Funkcja miniatur wizja komputera generuje miniaturę obrazu. Za pomocą **inteligentne przycięcia** funkcji, funkcja miniatur określi obszaru zainteresowania Centrum miniatur w obszarze, aby wygenerować bardziej aesthetically czytelnych obrazy miniatur i obraz.

Aby wykonać funkcję miniatur samouczek aplikacji, wykonaj następujące czynności:

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>Miniatury krok 1: Dodaj kod obsługi zdarzeń dla przycisku formularza

Otwórz **thumbnail.html** plik w edytorze tekstów i Znajdź **thumbnailButtonClick** funkcji w dolnej części pliku.

**ThumbnailButtonClick** funkcji procedury obsługi zdarzeń czyści formularza, wyświetla obrazu określonego w adresie URL, następnie wywołuje **getThumbnail** funkcja dwa razy, aby utworzyć dwa miniatur, co inteligentnych przycięte i jeden bez inteligentne przycięcia.

Skopiuj i wklej następujący kod do **thumbnailButtonClick** funkcji.

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

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>Miniatury krok 2: Dodaj otoki dla wywołania interfejsu API REST

**GetThumbnail** funkcja opakowuje wywołania interfejsu API REST do analizowania obrazu. Po powrocie pomyślne miniatury będą wyświetlane w element img określony.

Skopiuj i wklej następujący **getThumbnail** funkcji, aby dokładnie pod **thumbnailButtonClick** funkcji.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumnail image.
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

### <a name="thumbnail-step-3-run-the-application"></a>Miniatury krok 3: uruchamianie aplikacji

Zapisz **thumbnail.html** pliku i otwórz go w przeglądarce sieci Web. Umieść swój klucz subskrypcji do **klucza subskrypcji** pola i upewnij się, że używasz poprawny region w **Region subskrypcji**. Wprowadź adres URL obrazu do analizy, a następnie kliknij przycisk **generowanie miniatur** przycisk, aby przeanalizować obrazu i wyświetlić wyniki.

## <a name="read-printed-text-ocr"></a>Odczytaj tekst wydruku (Rozpoznawania)

Funkcja OCR (Rozpoznawania) komputera wizja analizuje obrazu drukowanymi tekstu. Po zakończeniu analizy Rozpoznawania zwraca obiekt JSON, który zawiera tekstu i położenie tekstu w obrazie.

Aby wykonać funkcję Rozpoznawania samouczek aplikacji, wykonaj następujące czynności:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>Rozpoznawania krok 1: Dodaj kod obsługi zdarzeń dla przycisku formularza

Otwórz **ocr.html** plik w edytorze tekstów i Znajdź **ocrButtonClick** funkcji w dolnej części pliku.

**OcrButtonClick** funkcji procedury obsługi zdarzeń czyści formularza, wyświetla obrazu określonego w adresie URL, następnie wywołuje **ReadOcrImage** funkcji do analizowania obrazu.

Skopiuj i wklej następujący kod do **ocrButtonClick** funkcji.

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

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>Rozpoznawania krok 2: Dodaj otoki dla wywołania interfejsu API REST

**ReadOcrImage** funkcja opakowuje wywołania interfejsu API REST do analizowania obrazu. Po pomyślnym zwraca sformatowany kod JSON opisujące tekst i lokalizacji tekstu pojawi się w określonym textarea.

Skopiuj i wklej następujący **ReadOcrImage** funkcji, aby dokładnie pod **ocrButtonClick** funkcji.

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

### <a name="ocr-step-3-run-the-application"></a>Rozpoznawania krok 3: uruchamianie aplikacji

Zapisz **ocr.html** pliku i otwórz go w przeglądarce sieci Web. Umieść swój klucz subskrypcji do **klucza subskrypcji** pola i upewnij się, że używasz poprawny region w **Region subskrypcji**. Wprowadź adres URL obrazu tekstu do odczytu, a następnie kliknij przycisk **obrazu odczytu** przycisk, aby przeanalizować obrazu i wyświetlić wyniki.

## <a name="read-handwritten-text-handwriting-recognition"></a>Odczytaj tekst odręcznie (rozpoznawania pisma ręcznego)

Funkcja rozpoznawania pisma ręcznego komputera wizja analizuje obraz pisma ręcznego. Po zakończeniu analizy rozpoznawania pisma ręcznego zwraca obiekt JSON, który zawiera tekstu i położenie tekstu w obrazie.

Aby wykonać funkcję rozpoznawania pisma ręcznego samouczek aplikacji, wykonaj następujące czynności:

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>Rozpoznawania pisma ręcznego — krok 1: Dodaj kod obsługi zdarzeń dla przycisku formularza

Otwórz **handwriting.html** plik w edytorze tekstów i Znajdź **handwritingButtonClick** funkcji w dolnej części pliku.

**HandwritingButtonClick** funkcji procedury obsługi zdarzeń czyści formularza, wyświetla obrazu określonego w adresie URL, następnie wywołuje **HandwritingImage** funkcji do analizowania obrazu.

Skopiuj i wklej następujący kod do **handwritingButtonClick** funkcji.

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

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>Rozpoznawania pisma ręcznego — krok 2: Dodaj otoki dla wywołania interfejsu API REST

**ReadHandwrittenImage** funkcja opakowuje dwóch wywołań interfejsu API REST potrzebne do analizowania obrazu. Ponieważ rozpoznawania pisma ręcznego jest procesem czasochłonne, proces dwóch kroku jest używana. Pierwsze wywołanie przesyła obraz do przetwarzania; drugie wywołanie pobiera tekst wykrytego po zakończeniu przetwarzania.

Po pobraniu tekst sformatowany kod JSON opisujące tekstu i położenie tekstu pojawi się w określonym textarea.

Skopiuj i wklej następujący **ReadHandwrittenImage** funkcji, aby dokładnie pod **handwritingButtonClick** funkcji.

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

    // This operation requrires two REST API calls. One to submit the image for processing,
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

### <a name="handwriting-recognition-step-3-run-the-application"></a>Rozpoznawania pisma ręcznego — krok 3: uruchamianie aplikacji

Zapisz **handwriting.html** pliku i otwórz go w przeglądarce sieci Web. Umieść swój klucz subskrypcji do **klucza subskrypcji** pola i upewnij się, że używasz poprawny region w **Region subskrypcji**. Wprowadź adres URL obrazu tekstu do odczytu, a następnie kliknij przycisk **obrazu odczytu** przycisk, aby przeanalizować obrazu i wyświetlić wyniki.

## <a name="next-steps"></a>Kolejne kroki

- [Komputer wizji interfejsu API C&#35; samouczka](CSharpTutorial.md)
- [Samouczek Python interfejsu API wizji komputera](PythonTutorial.md)

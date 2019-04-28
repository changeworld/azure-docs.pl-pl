---
title: 'Samouczek: jak przekazać obraz — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: W samouczku szczegółowo omówiono poszczególne etapy procesu przekazywania obrazu do usługi Bing oraz analizowania i wyświetlania odpowiedzi.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 42a7db316e844e5dbd09fb75a07e1c7883a9cec9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829569"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Samouczek: Przekazywanie obrazów do interfejsu API wyszukiwania wizualnego Bing

Interfejs API wyszukiwania wizualnego Bing umożliwia wyszukiwanie w Internecie obrazów podobnych do tych, które zostaną przekazane. Przy użyciu tego samouczka możesz utworzyć aplikację internetową, która wysyła obraz do interfejsu API i wyświetla wyniki na stronie internetowej. Pamiętaj, że ta aplikacja nie spełnia wszystkich [wymagań dotyczących użycia i wyświetlania Bing](../bing-web-search/use-display-requirements.md) dla używania interfejsu API.

Możesz znaleźć pełny kod źródłowy dla tego przykładu z obsługi dodatkowych błędów i adnotacji w [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

Aplikacja samouczka ilustruje sposób wykonywania następujących czynności:

> [!div class="checklist"]
> * Przekazywanie obrazu do interfejsu API wyszukiwania wizualnego Bing
> * Wyświetlanie wyników wyszukiwania obrazu w aplikacji internetowej
> * Przeglądanie szczegółowych informacji dostarczonych przez interfejs API

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Tworzenie strony internetowej i określanie jej struktury

Utwórz stronę HTML, która wysyła obraz do interfejsu API wyszukiwania wizualnego Bing odbiera szczegółowe informacje, a następnie wyświetli je. W ulubionym edytorze lub IDE Utwórz plik o nazwie "uploaddemo.html". Dodaj podstawowe następującą strukturę kodu HTML do pliku:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Podziel stronie do sekcji żądania, w którym użytkownik udostępnia wszystkie informacje, które są wymagane dla żądania, i sekcję odpowiedzi, gdzie są wyświetlane szczegółowe informacje. Dodaj następujące tagi `<div>` do sekcji `<body>`. `<hr>` Tag wizualnie oddziela sekcji żądania z sekcji odpowiedzi:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Dodaj `<script>` tag `<head>` tag, aby zawierać kod JavaScript dla aplikacji:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Pobieranie pliku przekazywania

Aby umożliwić użytkownikowi wybranie obrazu do przekazania, aplikacja używa tagu `<input>` z atrybutem type o wartości `file`. Interfejs użytkownika musi w jasny sposób poinformować, że w aplikacji do wyszukiwania wyników jest używana usługa Bing.

Dodaj następujący kod `<div>` do `requestSection` `<div>`. Tag input z atrybutem type o wartości file akceptuje pojedynczy plik dowolnego typu obrazu (na przykład, JPG, GIF, PNG). Zdarzenie `onchange` określa procedurę obsługi, która jest wywoływana, gdy użytkownik wybiera plik.

`<output>` Tag jest używany do wyświetlania miniatury wybranego obrazu:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Tworzenie procedury obsługi plików

Utwórz funkcję obsługi mogącą odczytać obraz, który chcesz przekazać. Podczas iterowania po plikach w obiekcie `FileList` procedura obsługi powinna sprawdzić, czy wybrany plik jest obrazem, a jego rozmiar to 1 MB lub mniej. Jeśli obraz, który jest większy, należy zmniejszyć rozmiar przed przekazaniem go. Ponadto program obsługi Wyświetla miniaturę obrazu:

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Dodawanie i przechowywanie klucza subskrypcji

Aplikacja wymaga klucza subskrypcji do wykonywania wywołań do interfejsu API wyszukiwania wizualnego Bing. Na potrzeby tego samouczka podasz go w interfejsie użytkownika. Dodaj następujący kod `<input>` tag (w atrybucie type z ustawioną wartość text) `<body>` tuż poniżej pliku `<output>` tag:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Korzystając z obrazu i klucza subskrypcji, możesz wywołać usługę wyszukiwania wizualnego Bing, aby pobrać szczegółowe informacje o obrazie. W tym samouczku korzysta z rynku domyślne wywołania (`en-us`) i wartość bezpieczne wyszukiwanie (`moderate`).

Ta aplikacja zawiera opcję zmiany tych wartości. Dodaj następujący kod `<div>` poniżej klucz subskrypcji `<div>`. W aplikacji jest używany tag `<select>` do zapewnienia listy rozwijanej do określenia wartości rynku i wartości bezpiecznego wyszukiwania. Obie listy wyświetlają domyślną wartość.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Dodawanie opcji wyszukiwania na stronie internetowej

Aplikacja ukrywa listach zwijany `<div>` który jest kontrolowany przez łącze opcji zapytania. Po kliknięciu łącza Opcje zapytania, `<div>` rozwija, dzięki czemu możesz wyświetlić i zmodyfikować opcje zapytania. Jeśli ponownie, kliknij łącze Opcje zapytania `<div>` zwija i jest ukryty. Poniższy fragment kodu przedstawia łącze Opcje zapytania `onclick` programu obsługi. Formanty programu obsługi czy `<div>` jest rozwinięta czy zwinięta. Dodaj tę procedurę obsługi do sekcji `<script>`. Program obsługi jest używany przez wszystkie zwijany `<div>` sekcje w pokazie.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>Wywołaj `onclick` procedury obsługi

Dodaj następujący kod `"Get insights"` znajdujący się poniżej opcje `<div>` w treści. Przycisk umożliwia zainicjowanie wywołania. Po kliknięciu przycisku, kursor zostanie zmieniony na obrotowych kursor oczekiwania i `onclick` program obsługi jest wywoływany.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Dodawanie przycisku `onclick` obsługi `handleQuery()` do `<script>` tagu.

## <a name="handle-the-query"></a>Obsługa zapytania

Program obsługi `handleQuery()` zapewnia, że klucz subskrypcji znajduje się i jest 32 znaków i że obraz jest zaznaczona. Ponadto czyści wszystkie szczegółowe informacje z poprzedniego zapytania. Następnie wywołuje funkcję `sendRequest()`, aby wykonać wywołanie.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>Wysyłanie żądania wyszukiwania

`sendRequest()` Funkcja formatów adresu URL punktu końcowego zestawy `Ocp-Apim-Subscription-Key` nagłówek, aby klucz subskrypcji dołącza obrazu, aby przekazać plik binarny, określa obsługi odpowiedzi i wywołuje tę funkcję:

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>Pobieranie i obsługa odpowiedzi interfejsu API

Funkcja `handleResponse()` obsługuje odpowiedź z wywołania wyszukiwania wizualnego Bing. Jeśli wywołanie zakończy się powodzeniem, analizuje odpowiedź JSON, wprowadzając ją do poszczególnych tagów ze szczegółowymi informacjami. Następnie dodaje wyniki wyszukiwania do strony. Następnie aplikacja tworzy zwijany `<div>` dla każdego znacznika do zarządzania, jak dużo danych jest wyświetlana. Dodaj tę procedurę obsługi do sekcji `<script>`.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>Analizowanie odpowiedzi

Funkcja `parseResponse` konwertuje odpowiedź JSON do obiektu słownika przez iterację po pliku `json.tags`.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Tworzenie sekcji tagów

`buildTagSections()` Funkcja wykonuje iterację przez przeanalizowany tagów JSON i wywołuje `buildDiv()` funkcję, aby tworzyć `<div>` dla każdego znacznika. Każdy tag jest wyświetlany jako link. Gdy link zostanie kliknięty, tag rozwinie się i wyświetli skojarzone z nim szczegółowe informacje. Ponowne kliknięcie łącza spowoduje sekcji zwinąć.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>Wyświetlanie wyników wyszukiwania na stronie internetowej

`buildDiv()` Wywołaniach funkcji `addDivContent` funkcję, aby tworzyć zawartość każdego znacznika użytkownika zwijany `<div>`.

Zawartość tagu zawiera dane JSON z odpowiedzi dla tagu. Początkowo wyświetlane są tylko 100 pierwszych znaków za pomocą pliku JSON, ale możesz kliknąć pozycję ciągu JSON, aby wyświetlić wszystkie dane JSON. Jeśli klikniesz go ponownie, ciąg JSON ponownie zwinie się do 100 znaków.

Następnie dodaj typy akcji znalezione w tagu. Dla każdego typu akcji wywołania odpowiednie funkcje, aby dodać jego szczegółowe informacje:

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Wyświetlanie szczegółowych informacji dla różnych akcji

Następujące funkcje wyświetlają szczegółowe informacje dla różnych akcji. Funkcje zapewniają klikalny obraz albo klikalny link, który prowadzi do strony internetowej z dodatkowymi informacjami na temat obrazu. Ta strona jest hostowana w witrynie Bing.com albo w pierwotnej witrynie internetowej obrazu. W tej aplikacji nie są wyświetlane wszystkie dane ze szczegółowych informacji. Aby wyświetlić wszystkie pola dostępne, aby uzyskać szczegółowe informacje, zobacz [obrazy — wyszukiwanie wizualne](https://aka.ms/bingvisualsearchreferencedoc) odwołania.

> [!NOTE]
> Obowiązuje minimalna ilość szczegółowych informacji, którą należy wyświetlić na stronie. Zobacz [Użyj interfejsu API wyszukiwania Bing i wyświetlają wymagania dotyczące](../bing-web-search/use-display-requirements.md) Aby uzyskać więcej informacji.

### <a name="relatedimages-insights"></a>Szczegółowe informacje dotyczące akcji RelatedImages

`addRelatedImages()` Funkcja tworzy tytuł dla każdej z witryn sieci Web hostingu obraz powiązane przez iterację na liście `RelatedImages` akcje i dołączanie `<img>` tag na zewnątrz `<div>` dla każdego:

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>Szczegółowe informacje dotyczące akcji PagesIncluding

`addPagesIncluding()` Funkcja służy do tworzenia linku dla każdej z witryn sieci Web hosting przekazanego obrazu przez iterację na liście `PagesIncluding` akcje i dołączanie `<img>` tag na zewnątrz `<div>` dla każdego:

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>Szczegółowe informacje dotyczące akcji RelatedSearches

`addRelatedSearches()` Funkcja tworzy link do witryny sieci Web hostingu obrazu, przez iterację na liście `RelatedSearches` akcje i dołączanie `<img>` tag na zewnątrz `<div>` dla każdego:

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Szczegółowe informacje dotyczące przepisów

`addRecipes()` Funkcja służy do tworzenia linku dla każdego z przepisami zwrócony przez iterację na liście `Recipes` akcje i dołączanie `<img>` tag na zewnątrz `<div>` dla każdego:

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Szczegółowe informacje dotyczące zakupów

`addShopping()` Funkcja służy do tworzenia linku dla dowolnego zwrócił przez iterację na liście wyników zakupów `RelatedImages` akcje i dołączanie `<img>` tag na zewnątrz `<div>` dla każdego:

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Szczegółowe informacje dotyczące produktów

`addProducts()` Funkcja służy do tworzenia linku dla dowolnego zwrócił przez iterację na liście wyników produktów `Products` akcje i dołączanie `<img>` tag na zewnątrz `<div>` dla każdego:

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>Szczegółowe informacje dotyczące akcji TextResult

`addTextResult()` Funkcja wyświetla dowolny tekst, który został rozpoznany w obrazie:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

`addEntity()` Funkcji zostanie wyświetlony link umożliwiający przejście użytkownika do Bing.com gdzie mogą uzyskać szczegółowe informacje dotyczące typu jednostki na ilustracji, jeśli żadnego została wykryta:

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

`addImageWithWebSearchUrl()` Funkcja wyświetla możesz klikać obrazu do `<div>` przyjmującej użytkownikowi wyniki w witrynie Bing.com wyszukiwania:

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>Dodawanie stylu CSS

Dodaj następujący kod `<style>` sekcji `<head>` tagów do organizowania układ strony sieci Web:

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Kolejne kroki

>[!div class="nextstepaction"]
> [Samouczek: Znajdź podobne obrazy z poprzednich wyników wyszukiwania za pomocą ImageInsightsToken](./tutorial-visual-search-insights-token.md)

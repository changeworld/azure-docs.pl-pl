---
title: Przewodnik Szybki start dla języka PHP dotyczący analizowania obrazów przy użyciu interfejsu API przetwarzania obrazów | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: W tym przewodniku Szybki start przeprowadzisz analizę obrazu, korzystając z funkcji przetwarzania obrazów i języka PHP w usługach Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 114674f47ee945717e866d97ffed747ae45decc8
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43771951"
---
# <a name="quickstart-analyze-a-remote-image---rest-php"></a>Szybki start: analizowanie obrazu zdalnego — REST, PHP

W tym przewodniku Szybki start przeprowadzisz analizę obrazu w celu wyodrębnienia elementów wizualnych przy użyciu funkcji przetwarzania obrazów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z funkcji przetwarzania obrazów, musisz mieć klucz subskrypcji — zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-image-request"></a>Żądanie Analyze Image

[Metoda Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) umożliwia wyodrębnienie elementów wizualnych na podstawie zawartości obrazu. Możesz przekazać obraz lub określić adres URL obrazu i wybrać, które elementy mają zostać zwrócone. Może być to na przykład:

* Szczegółowa lista tagów związanych z zawartością obrazu.
* Opis zawartości obrazu w formie pełnego zdania.
* Współrzędne, płeć i wiek wszystkich twarzy znajdujących się na obrazie.
* Typ obrazu (clipart lub rysunek).
* Dominujący kolor, kolor akcentu lub to, czy obraz jest czarno-biały.
* Kategoria zdefiniowana w tej [taksonomii](../Category-Taxonomy.md).
* Czy na obrazie znajduje się zawartość przeznaczona dla osób dorosłych lub o charakterze seksualnym?

Aby uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujący kod do edytora.
1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Zmień element `uriBase`, aby użyć lokalizacji, z której uzyskano klucze subskrypcji, jeśli jest to potrzebne.
1. Opcjonalnie możesz ustawić element `imageUrl` na obraz do analizy.
1. Opcjonalnie możesz zmienić język odpowiedzi (`'language' => 'en'`).
1. Zapisz plik z rozszerzeniem `.php`.
1. Otwórz plik w przeglądarce, która obsługuje język PHP.

W tym przykładzie użyto pakietu PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2).

```php
<html>
<head>
    <title>Analyze Image Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'visualFeatures' => 'Categories,Description',
    'details' => '',
    'language' => 'en'
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="analyze-image-response"></a>Odpowiedź na żądanie Analyze Image

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, na przykład:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "ebf5a1bc-3ba2-4c56-99b4-bbd20ba28705",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsami API przetwarzania obrazów używanymi do analizy obrazu, wykrywania osobistości i charakterystycznych elementów krajobrazu, tworzenia miniatur oraz wyodrębniania tekstu drukowanego i odręcznego. Aby szybko zacząć eksperymentować z interfejsami API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Poznaj interfejsy API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

---
title: 'Szybki start: Rozpoznawanie emocji na twarzach na obrazie — interfejs API rozpoznawania emocji, PHP'
titlesuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API rozpoznawania emocji z językiem PHP.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 3acf47ee26974ddff4f6063eef95bb29be61fce3
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215499"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Szybki start: Tworzenie aplikacji do rozpoznawania emocji na twarzach na obrazie.

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostanie wycofany w dniu 15 lutego 2019 r. Rozpoznawanie emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/). 

Ten artykuł zawiera informacje i przykłady kodu, które pomogą w szybkim rozpoczęciu korzystania z języka PHP i [metody Recognize interfejsu API rozpoznawania emocji](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) do rozpoznawania emocji wyrażonych przez co najmniej jedną osobę na obrazie.

## <a name="prerequisite"></a>Wymagania wstępne
* Pobierz swój bezpłatny klucz subskrypcji [stąd](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-php-example-request"></a>Przykładowe żądanie rozpoznawania emocji w języku PHP

Zmień adres URL REST, aby użyć lokalizacji, z której zostały uzyskane klucze subskrypcji. Zmień treść na adres URL obrazu, który ma zostać przetestowany. Zastąp wartość „Ocp-Apim-Subscription-Key” prawidłowym kluczem subskrypcji.

```php
<?php
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

// NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
//   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
//   URL below with "westcentralus".
$request = new Http_Request2('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$request->setBody('{"url": "http://www.example.com/images/image.jpg"}');

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}

?>
```

## <a name="recognize-emotions-sample-response"></a>Przykładowa odpowiedź dotycząca rozpoznawania emocji
Pomyślne wywołanie zwraca tablicę wpisów twarzy oraz skojarzonych z nimi ocen emocji uporządkowanych według rozmiaru prostokąta twarzy w kolejności malejącej. Pusta odpowiedź wskazuje, że nie wykryto żadnych twarzy. Wpis rozpoznawania emocji zawiera następujące pola:
* faceRectangle — lokalizacja prostokąta twarzy na obrazie.
* scores — ocena emocji dla każdej twarzy na obrazie.

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]

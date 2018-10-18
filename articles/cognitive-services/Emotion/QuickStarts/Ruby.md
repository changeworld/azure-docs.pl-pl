---
title: 'Szybki start: rozpoznawanie emocji na twarzach na obrazie — interfejs API rozpoznawania emocji, Ruby'
titlesuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API rozpoznawania emocji z językiem Ruby.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: bcab24334c1ee4e47061ce6ea28bd60039e17b3f
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239033"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Szybki start: tworzenie aplikacji do rozpoznawania emocji na twarzach na obrazie.

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostanie wycofany w dniu 15 lutego 2019 r. Rozpoznawanie emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/). 

Ten artykuł zawiera informacje i przykłady kodu, które pomogą w szybkim rozpoczęciu korzystania z [metody Recognize interfejsu API rozpoznawania emocji](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) z językiem Ruby do rozpoznawania emocji wyrażonych przez co najmniej jedną osobę na obrazie.

## <a name="prerequisite"></a>Wymagania wstępne
* Pobierz swój bezpłatny klucz subskrypcji [stąd](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-ruby-example-request"></a>Przykładowe żądanie rozpoznawania emocji w języku Ruby

Zmień adres URL REST, aby użyć lokalizacji, z której zostały uzyskane klucze subskrypcji. Zastąp wartość „Ocp-Apim-Subscription-Key” prawidłowym kluczem subskrypcji. Dodaj adres URL zdjęcia do zmiennej `body`.

```ruby
require 'net/http'

# NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
#   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
#   URL below with "westcentralus".
uri = URI('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize')
uri.query = URI.encode_www_form({
})

request = Net::HTTP::Post.new(uri.request_uri)
# Request headers
request['Content-Type'] = 'application/json'
# NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '13hc77781f7e4b19b5fcdd72a8df7156'
# Request body
request.body = "{\"url\":\"http://example.com/1.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body

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

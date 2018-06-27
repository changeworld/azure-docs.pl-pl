---
title: Szybki start Ruby interfejsu API rozpoznawania emocji — warstwa | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API rozpoznawania emocji — warstwa z Ruby w usługach kognitywnych.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 733127bb3656d86a7f3f57cd26c72909900f4899
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021053"
---
# <a name="emotion-api-ruby-quick-start"></a>Szybki Start Ruby interfejsu API rozpoznawania emocji — warstwa

> [!IMPORTANT]
> Interfejs API podglądu kończy się 30 października 2017 r. Testowanie nowego [wideo indeksatora interfejsu API w wersji zapoznawczej](https://azure.microsoft.com/services/cognitive-services/video-indexer/) można łatwo wyodrębnić szczegółowych informacji z wideo i celu ułatwienia pracy funkcję odnajdowania zawartości, takich jak wyniki wyszukiwania został określony poprzez wykrycie słowa rozmowy, kroje znaków i emocji. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ten artykuł zawiera informacje i przykłady kodu, aby szybko rozpocząć korzystanie z [metody rozpoznaje interfejsu API rozpoznawania emocji — warstwa](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) z Ruby do rozpoznawania emocji wyrażonych przez osoby na obrazie.

## <a name="prerequisite"></a>Wymagania wstępne
* Uzyskaj swój klucz bezpłatnej subskrypcji [tutaj](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-ruby-example-request"></a>Rozpoznaje emocji dopisków fonetycznych przykładowe żądanie

Zmień adres URL usługi REST, aby użyć lokalizacji, w którym uzyskać klucze subskrypcji, zastąp wartość "Ocp-Apim-subskrypcji — klucz" po klucz ważnej subskrypcji i Dodaj adres URL fotografii do `body` zmiennej.

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

## <a name="recognize-emotions-sample-response"></a>Rozpoznaje emocji przykładowa odpowiedź
Pomyślnego połączenia zwraca tablicę wpisów krój oraz wyniki emocji skojarzony, uszeregowane według rozmiaru prostokąt krój w kolejności malejącej. Pustą odpowiedź wskazuje, że zostały wykryte nie kroje. Wpis emocji zawiera następujące pola:
* faceRectangle - lokalizacji prostokąta powierzchni w obrazie.
* wyniki - emocji wyniki dla każdej powierzchni w obrazie. 

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

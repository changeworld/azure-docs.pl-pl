---
title: Wywołanie i odpowiedź — Ruby — Szybki Start dla usługi kognitywnych Azure, interfejsu API Bing sieci Web wyszukiwania | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API wyszukiwania usługi Bing sieci Web w kognitywnych usług Microsoft Azure.
services: cognitive-services
documentationcenter: ''
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: b0f5c395fcdf043f4111f63ef16f0d33d5257e74
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347537"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-ruby"></a>Wywołania i odpowiedzi: pierwszego zapytania wyszukiwania usługi Bing w sieci Web w języku Ruby

Interfejs API wyszukiwania usługi Bing sieci Web zapewnia podobny do Bing.com/Search zwracając się, że wyniki wyszukiwania, które określa Bing są istotne dla kwerendy użytkownika. Wyniki mogą obejmować stron sieci Web, obrazów, klipów wideo, wiadomości i jednostek, wraz z zapytania wyszukiwania powiązanych, pisowni stref czasowych, konwersja jednostek, tłumaczenia i obliczeń. Rodzaje otrzymane wyniki na podstawie ich przydatności i warstwy subskrybowanych interfejsy API wyszukiwania usługi Bing.

Ten artykuł zawiera prostej aplikacji konsolowej wykonuje zapytania interfejsu API Bing sieci Web wyszukiwania, który wyświetla zwrócone nieprzetworzone wyniki wyszukiwania, które są w formacie JSON. Ta aplikacja jest napisany w języku Ruby, interfejsu API jest zgodny z żadnego języka programowania, które mogą wysyłać żądania HTTP i przeanalizować składni JSON usługi sieci RESTful Web. 

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [Ruby 2.4 lub nowszym](https://www.ruby-lang.org/en/downloads/) do uruchomienia przykładu kodu.

Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania usługi Bing**. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="running-the-application"></a>Uruchamianie aplikacji

Aby uruchomić tę aplikację, wykonaj następujące kroki.

1. Utwórz nowy projekt dopisków fonetycznych w ulubionych IDE lub edytora.
2. Dodaj podany kod.
3. Zastąp `accessKey` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```ruby
require 'net/https'
require 'uri'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the accessKey string value with your valid access key.
accessKey = "enter key here"

# Verify the endpoint URI.  At this writing, only one endpoint is used for Bing
# search APIs.  In the future, regional endpoints may be available.  If you
# encounter unexpected authorization errors, double-check this value against
# the endpoint for your Bing Web search instance in your Azure dashboard.

uri  = "https://api.cognitive.microsoft.com"
path = "/bing/v7.0/search"

term = "Microsoft Cognitive Services"

if accessKey.length != 32 then
    puts "Invalid Bing Search API subscription key!"
    puts "Please paste yours into the source code."
    abort
end

uri = URI(uri + path + "?q=" + URI.escape(term))

puts "Searching the Web for: " + term

request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = accessKey

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts "\nRelevant Headers:\n\n"
response.each_header do |key, value|
    # header names are coerced to lowercase
    if key.start_with?("bingapis-") or key.start_with?("x-msedge-") then
        puts key + ": " + value
    end
end

puts "\nJSON Response:\n\n"
puts JSON::pretty_generate(JSON(response.body))
```

## <a name="json-response"></a>Odpowiedź w formacie JSON

Przykładowa odpowiedź jest zgodna. Aby ograniczyć długość JSON, jest wyświetlany tylko jeden wynik i inne części odpowiedzi zostały obcięte. 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face API",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face API from Microsoft Azure. ... Cognitive Services; Face API;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek aplikacji jednej strony wyszukiwania usługi Bing w sieci Web](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

[Wyszukiwania usługi Bing w sieci Web — Omówienie](../overview.md)  
[Wypróbuj](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Pobierz klucz bezpłatnej wersji próbnej dostępu](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)  
[Dokumentacja interfejsu API wyszukiwania sieci Web usługi Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)

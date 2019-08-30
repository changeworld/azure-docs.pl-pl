---
title: 'Szybki start: Wywoływanie interfejsu API analizy tekstu przy użyciu języka Ruby'
titleSuffix: Azure Cognitive Services
description: Uzyskaj informacje i przykłady kodu, aby szybko rozpocząć korzystanie z interfejs API analizy tekstu na platformie Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 9c7c85ae9573efa202f5fc27ae78aee57fa67ab8
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142694"
---
# <a name="quickstart-using-ruby-to-call-the-text-analytics-cognitive-service"></a>Szybki start: Wywoływanie analizy tekstu usługi Cognitive Service przy użyciu języka Ruby
<a name="HOLTop"></a>

W tym artykule przedstawiono, jak [wykrywać język](#Detect), [analizować tonację](#SentimentAnalysis), [wyodrębniać kluczowe frazy](#KeyPhraseExtraction) i [identyfikować połączone jednostki](#Entities) przy użyciu [interfejsów API analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) i języka Ruby.

Zapoznaj się z tematem [API definitions (Definicje interfejsu API)](//go.microsoft.com/fwlink/?LinkID=759346), zawierającym dokumentację techniczną interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Wykryj język

Interfejs API wykrywania języka wykrywa język dokumentu tekstowego przy użyciu [metody Detect Language](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Utwórz zmienne `TEXT_ANALYTICS_SUBSCRIPTION_KEY` środowiskowe `TEXT_ANALYTICS_ENDPOINT` i klucz subskrypcji dla Twojego zasobu. Jeśli te zmienne środowiskowe zostały utworzone po rozpoczęciu edytowania aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę używaną w celu uzyskania dostępu do zmiennych środowiskowych.
1. Utwórz nowy projekt Ruby w ulubionym środowisku IDE.
1. Dodaj kod przedstawiony poniżej.
1. Uruchom program.

```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if (!ENV[key_var])
    raise "Please set/export the following environment variable: " + key_var
else
    subscription_key = ENV[key_var]
end

endpoint_var = "TEXT_ANALYTICS_ENDPOINT"
if (!ENV[endpoint_var])
    raise "Please set/export the following environment variable: " + endpoint_var
else
    endpoint = ENV[endpoint_var]
end

path = '/text/analytics/v2.1/languages'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'text' => 'This is a document written in English.' },
    { 'id' => '2', 'text' => 'Este es un document escrito en Español.' },
    { 'id' => '3', 'text' => '这是一个用中文写的文件' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Odpowiedź wykrywania języka**

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analiza tonacji

Interfejs API analizy tonacji wykrywa tonację zestawu rekordów tekstowych przy użyciu [metody Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). W poniższym przykładzie oceniane są dwa dokumenty, jeden w języku angielskim, a drugi w hiszpańskim.

1. Utwórz zmienne `TEXT_ANALYTICS_SUBSCRIPTION_KEY` środowiskowe `TEXT_ANALYTICS_ENDPOINT` i klucz subskrypcji dla Twojego zasobu. Jeśli ta zmienna środowiskowa zostanie utworzona po rozpoczęciu edytowania aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę używaną w celu uzyskania dostępu do zmiennych.
1. Utwórz nowy projekt Ruby w ulubionym środowisku IDE.
1. Dodaj kod przedstawiony poniżej.
1. Uruchom program.

```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if (!ENV[key_var])
    raise "Please set/export the following environment variable: " + key_var
else
    subscription_key = ENV[key_var]
end

endpoint_var = "TEXT_ANALYTICS_ENDPOINT"
if (!ENV[endpoint_var])
    raise "Please set/export the following environment variable: " + endpoint_var
else
    endpoint = ENV[endpoint_var]
end

path = '/text/analytics/v2.1/sentiment'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Odpowiedź analizy tonacji**

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Wyodrębnij frazy kluczowe

Interfejs API wyodrębniania kluczowych fraz wyodrębnia kluczowe frazy w dokumencie tekstowym przy użyciu [metody Key Phrases](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). W poniższym przykładzie wyodrębniono frazy kluczowe dla dokumentów w języku angielskim i hiszpańskim.

1. Utwórz zmienne `TEXT_ANALYTICS_SUBSCRIPTION_KEY` środowiskowe `TEXT_ANALYTICS_ENDPOINT` i klucz subskrypcji dla Twojego zasobu. Jeśli ta zmienna środowiskowa zostanie utworzona po rozpoczęciu edytowania aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę używaną w celu uzyskania dostępu do zmiennych.
1. Utwórz nowy projekt Ruby w ulubionym środowisku IDE.
1. Dodaj kod przedstawiony poniżej.
1. Uruchom program.


```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if (!ENV[key_var])
    raise "Please set/export the following environment variable: " + key_var
else
    subscription_key = ENV[key_var]
end

endpoint_var = "TEXT_ANALYTICS_ENDPOINT"
if (!ENV[endpoint_var])
    raise "Please set/export the following environment variable: " + endpoint_var
else
    endpoint = ENV[endpoint_var]
end

path = '/text/analytics/v2.1/keyPhrases'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
    { 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' },
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Odpowiedź wyodrębniania kluczowych fraz**

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```
<a name="Entities"></a>

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Interfejs API jednostek wyodrębnia jednostki w dokumencie tekstowym przy użyciu [metody Entities](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634). W poniższym przykładzie zidentyfikowano jednostki dla dokumentów w języku angielskim.

1. Utwórz zmienne `TEXT_ANALYTICS_SUBSCRIPTION_KEY` środowiskowe `TEXT_ANALYTICS_ENDPOINT` i klucz subskrypcji dla Twojego zasobu. Jeśli ta zmienna środowiskowa zostanie utworzona po rozpoczęciu edytowania aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę używaną w celu uzyskania dostępu do zmiennych.
1. Utwórz nowy projekt Ruby w ulubionym środowisku IDE.
1. Dodaj kod przedstawiony poniżej.
1. Uruchom program.

```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if (!ENV[key_var])
    raise "Please set/export the following environment variable: " + key_var
else
    subscription_key = ENV[key_var]
end

endpoint_var = "TEXT_ANALYTICS_ENDPOINT"
if (!ENV[endpoint_var])
    raise "Please set/export the following environment variable: " + endpoint_var
else
    endpoint = ENV[endpoint_var]
end

path = '/text/analytics/v2.1/entities'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'Microsoft is an It company.' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Odpowiedź wyodrębniania jednostek**

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także 

 [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)

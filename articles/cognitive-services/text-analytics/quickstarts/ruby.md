---
title: Ruby Przewodnik Szybki start dotyczący usługi Azure Cognitive Services, interfejs API analizy tekstu | Dokumentacja firmy Microsoft
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API analizy tekstu usług Microsoft Cognitive Services na platformie Azure.
services: cognitive-services
documentationcenter: ''
author: ashmaka
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 08/30/2018
ms.author: ashmaka
ms.openlocfilehash: 75c6476e86ee4a742e32ae0e7ffd27842f591843
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841766"
---
# <a name="quickstart-for-text-analytics-api-with-ruby"></a>Przewodnik Szybki start dotyczący interfejsu API za pomocą języka Ruby analizy tekstu 
<a name="HOLTop"></a>

W tym artykule przedstawiono, jak do [Wykryj język](#Detect), [analizowanie tonacji](#SentimentAnalysis), [wyodrębnianie kluczowych fraz](#KeyPhraseExtraction), i [zidentyfikować połączonych jednostek](#Entities) przy użyciu [interfejsów API analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) za pomocą języka Ruby.

Zapoznaj się [definicji interfejsu API](//go.microsoft.com/fwlink/?LinkID=759346) dokumentacja techniczna w przypadku interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsu API analizy tekstu**. Możesz użyć **5000 transakcji miesięcznie w warstwie bezpłatna** aby ukończyć ten przewodnik Szybki Start.

Musisz również posiadać [punktu końcowego i klucza dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany dla Ciebie podczas logowania się. 

<a name="Detect"></a>

## <a name="detect-language"></a>Wykrywanie języka

Interfejs API wykrywania języka wykrywa język tekstu dokumentu, za pomocą [metody wykrywania języka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

1. Utwórz nowy projekt języka Ruby w Twoim ulubionym środowisku IDE.
2. Dodaj kod, przedstawione poniżej.
3. Zastąp `accessKey` wartością prawidłowy klucz dostępu dla Twojej subskrypcji.
4. Zastąp lokalizację w `uri` (obecnie `westus`) do regionu, możesz w konkursie.
5. Uruchom program.

```ruby
require 'net/https'
require 'uri'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the accessKey string value with your valid access key.
accessKey = 'ENTER KEY HERE'

# Replace or verify the region.
#
# You must use the same region in your REST API call as you used to obtain your access keys.
# For example, if you obtained your access keys from the westus region, replace 
# "westcentralus" in the URI below with "westus".
#
# NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
# a free trial access key, you should not need to change this region.
uri = 'https://westus.api.cognitive.microsoft.com'
path = '/text/analytics/v2.0/'

uri = URI(uri + path + 'languages')

documents = { 'documents': [
    { 'id' => '1', 'text' => 'This is a document written in English.' },
    { 'id' => '2', 'text' => 'Este es un document escrito en Español.' },
    { 'id' => '3', 'text' => '这是一个用中文写的文件' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Odpowiedź wykrywanie języka**

Pomyślnej odpowiedzi jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

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

Interfejs API analizy tonacji detexts tonacji zestaw rekordów tekstowych, za pomocą [metoda tonacji](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). Poniższy przykład ocenia dwa dokumenty, jeden w języku angielskim, a drugi w języku hiszpańskim.

Dodaj następujący kod do kodu ze [poprzedniej sekcji](#Detect).

```ruby
uri = URI(uri + path + 'sentiment')

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Odpowiedź analizy tonacji**

Pomyślnej odpowiedzi jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

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

## <a name="extract-key-phrases"></a>Wyodrębnianie kluczowych fraz

Klucz frazy wyodrębniania wyodrębnia kluczowych fraz z tekstu dokumentu, za pomocą [metoda kluczowych fraz](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Poniższy przykład wyodrębnia frazy kluczowe dla dokumentów w języku angielskim i hiszpańskim.

Dodaj następujący kod do kodu ze [poprzedniej sekcji](#SentimentAnalysis).

```ruby
uri = URI(uri + path + 'keyPhrases')

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
    { 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' },
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Odpowiedź wyodrębnianie kluczowych fraz**

Pomyślnej odpowiedzi jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

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

## <a name="identify-linked-entities"></a>Identyfikowanie połączonych jednostek

Interfejs API Entity Linking identyfikuje dobrze znanych jednostek w tekście dokumentu, za pomocą [łączenie podmiotów metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). Poniższy przykład określa jednostki dla dokumentów w języku angielskim.

Dodaj następujący kod do kodu ze [poprzedniej sekcji](#KeyPhraseExtraction).

```ruby
uri = URI(uri + path + 'entities')

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'en', 'text' => 'The Seattle Seahawks won the Super Bowl in 2014.' },
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Odpowiedź łączenia jednostek**

Pomyślnej odpowiedzi jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także 

 [Omówienie analizy tekstu](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)

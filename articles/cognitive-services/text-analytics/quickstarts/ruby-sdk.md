---
title: 'Szybki start: Wywoływanie usługi Cognitive Analiza tekstu przy użyciu zestawu SDK języka Ruby'
titleSuffix: Azure Cognitive Services
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API analizy tekstu w usługach Azure Cognitive Services.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 688887826fa803b616ca737bc8558aa17ed80e37
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297773"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>Szybki start: Wywoływanie usługi Analiza tekstu przy użyciu zestawu SDK języka Ruby

<a name="HOLTop"></a>


Użyj tego przewodnika Szybki Start, aby rozpocząć analizowanie języka przy użyciu zestawu SDK analizy tekstu dla języka Ruby. Gdy [analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) interfejsu API REST jest zgodny z większość języków programowania, zestaw SDK udostępnia łatwy sposób zintegrować usługę ze swoimi aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb).

Zapoznaj się z tematem [API definitions (Definicje interfejsu API)](//go.microsoft.com/fwlink/?LinkID=759346), zawierającym dokumentację techniczną interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

* [Ruby 2.5.5 lub nowszy](https://www.ruby-lang.org/)
* Analiza tekstu [zestawu SDK dla języka Ruby](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
 
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Musisz również mieć [punkt końcowy i klucz dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany podczas tworzenia konta. 

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Utwórz projekt języka Ruby i zainstaluj zestaw SDK

1. Utwórz nowy projekt języka ruby i Dodaj nowy plik o nazwie `Gemfile`.
2. Dodaj zestaw SDK analizy tekstu w projekcie przez dodanie poniższego kodu, aby `Gemfile`.

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>Tworzenie klienta analizy tekstu

1. Utwórz nowy plik o nazwie `TextAnalyticsExamples.rb` w ulubionym edytorze lub w środowisku IDE. Importowanie usługi Text Analytics SDK.

2. Obiekt poświadczeń będzie używany przez klienta analizy tekstu. Utwórz ją za pomocą `CognitiveServicesCredentials.new()` i przekazując klucz subskrypcji.

3. Tworzenie klienta z punktem końcowym usługi poprawna Analiza tekstu.

    ```ruby
    require 'azure_cognitiveservices_textanalytics'
    
    include Azure::CognitiveServices::TextAnalytics::V2_1::Models
    
    credentials =
        MsRestAzure::CognitiveServicesCredentials.new("enter key here")
    # Replace 'westus' with the correct region for your Text Analytics subscription
    endpoint = String.new("https://westus.api.cognitive.microsoft.com/")
    
    textAnalyticsClient =
        Azure::TextAnalytics::Profiles::Latest::Client.new({
            credentials: credentials
        })
    textAnalyticsClient.endpoint = endpoint
    ```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Analiza tonacji

Za pomocą Text Analytics SDK lub interfejsu API, można wykonywać analizę tonacji na zestawie rekordów tekstowych. Poniższy przykład wyświetla wyniki tonacji dla kilku dokumentów.

1. Utwórz nową funkcję o nazwie `SentimentAnalysisExample()` używającą klienta analizy tekstu, które są utworzone powyżej jako parametr.

2. Definiowanie zestawu `MultiLanguageInput` obiekty do analizy. Dodaj język i tekst dla każdego obiektu. Identyfikator może być dowolna wartość.

    ```ruby
    def SentimentAnalysisExample(client)
      # The documents to be analyzed. Add the language of the document. The ID can be any value.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'en'
      input_1.text = 'I had the best day of my life.'
    
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'en'
      input_2.text = 'This was a waste of my time. The speaker put me to sleep.'
    
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'es'
      input_3.text = 'No tengo dinero ni nada que dar...'
    
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'it'
      input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    ```

3. W ramach tej samej funkcji należy połączyć dokumenty w postaci listy. Dodaj go do `documents` pole `MultiLanguageBatchInput` obiektu. 

4. Wywołanie klienta `sentiment()` funkcją `MultiLanguageBatchInput` obiektu jako parametr do wysłania do dokumentów. Jeśli nie zostały zwrócone wszystkie wyniki, wydrukuj te instrukcje.
    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.sentiment(
          multi_language_batch_input: input_documents
      )
      
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
          puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
      end
    end
    ```

5. Wywołaj funkcję `SentimentAnalysisExample()`.

    ```ruby
    SentimentAnalysisExample(textAnalyticsClient)
    ```

### <a name="output"></a>Dane wyjściowe

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Wykrywanie języka

Usługi analizy tekstu można wykryć język dokument tekstowy w wielu języków i ustawień regionalnych. Poniższy przykład wyświetla kilka dokumentów zostały napisane w języku.

1. Utwórz nową funkcję o nazwie `DetectLanguageExample()` przyjmującej klienta analizy tekstu, które są utworzone powyżej jako parametr. 

2. Definiowanie zestawu `LanguageInput` obiekty do analizy. Dodaj język i tekst dla każdego obiektu. Identyfikator może być dowolna wartość.

    ```ruby
    def DetectLanguageExample(client)
       # The documents to be analyzed.
       language_input_1 = LanguageInput.new
       language_input_1.id = '1'
       language_input_1.text = 'This is a document written in English.'
    
       language_input_2 = LanguageInput.new
       language_input_2.id = '2'
       language_input_2.text = 'Este es un document escrito en Español..'
    
       language_input_3 = LanguageInput.new
       language_input_3.id = '3'
       language_input_3.text = '这是一个用中文写的文件'
    ```

3. W ramach tej samej funkcji należy połączyć dokumenty w postaci listy. Dodaj go do `documents` pole `LanguageBatchInput` obiektu. 

4. Wywołanie klienta `detect_language()` funkcją `LanguageBatchInput` obiektu jako parametr do wysłania do dokumentów. Jeśli nie zostały zwrócone wszystkie wyniki, wydrukuj te instrukcje.
    ```ruby
       input_documents = LanguageBatchInput.new
       input_documents.documents = [language_input_1, language_input_2, language_input_3]
    
    
       result = client.detect_language(
           language_batch_input: input_documents
       )
    
       if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
         puts '===== LANGUAGE DETECTION ====='
         result.documents.each do |document|
           puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
         end
       else
         puts 'No results data..'
       end
     end
    ```

5. Wywołanie funkcji `DetectLanguageExample`

    ```ruby
    DetectLanguageExample(textAnalyticsClient)
    ```

### <a name="output"></a>Dane wyjściowe

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Usługi analizy tekstu można odróżnić i Wyodrębnij różnymi jednostkami dokumenty tekstowe (osoby, miejsca i rzeczy). Poniższy przykład wyświetla jednostki w kilku dokumentów w przykładzie.

1. Utwórz nową funkcję o nazwie `Recognize_Entities()` używającą klienta analizy tekstu, które są utworzone powyżej jako parametr.

2. Definiowanie zestawu `MultiLanguageInput` obiekty do analizy. Dodaj język i tekst dla każdego obiektu. Identyfikator może być dowolna wartość.

    ```ruby
      def RecognizeEntitiesExample(client)
        # The documents to be analyzed.
        input_1 = MultiLanguageInput.new
        input_1.id = '1'
        input_1.language = 'en'
        input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'
    
        input_2 = MultiLanguageInput.new
        input_2.id = '2'
        input_2.language = 'es'
        input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. W ramach tej samej funkcji należy połączyć dokumenty w postaci listy. Dodaj go do `documents` pole `MultiLanguageBatchInput` obiektu. 

4. Wywołanie klienta `entities()` funkcją `MultiLanguageBatchInput` obiektu jako parametr do wysłania do dokumentów. Jeśli nie zostały zwrócone wszystkie wyniki, wydrukuj te instrukcje.

    ```ruby
        input_documents =  MultiLanguageBatchInput.new
        input_documents.documents = [input_1, input_2]
    
        result = client.entities(
            multi_language_batch_input: input_documents
        )
    
        if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
          puts '===== ENTITY RECOGNITION ====='
          result.documents.each do |document|
            puts "Document ID: #{document.id}"
              document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                  puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
              end
          end
        else
          puts 'No results data..'
        end
      end
    ```

5. Wywołanie funkcji `RecognizeEntitiesExample`
    ```ruby
    RecognizeEntitiesExample(textAnalyticsClient)
    ```

### <a name="output"></a>Dane wyjściowe

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Usługi analizy tekstu można wyodrębnić klucza wyrażenia w zdaniach. Poniższy przykład wyświetla obiekty w kilku dokumentów na przykład w wielu językach.

1. Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()` używającą klienta analizy tekstu, które są utworzone powyżej jako parametr.

2. Definiowanie zestawu `MultiLanguageInput` obiekty do analizy. Dodaj język i tekst dla każdego obiektu. Identyfikator może być dowolna wartość.

    ```ruby
    def KeyPhraseExtractionExample(client)
      # The documents to be analyzed.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'ja'
      input_1.text = '猫は幸せ'
  
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'de'
      input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'
  
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'en'
      input_3.text = 'My cat is stiff as a rock.'
  
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'es'
      input_4.text = 'A mi me encanta el fútbol!'
      ```

3. W ramach tej samej funkcji należy połączyć dokumenty w postaci listy. Dodaj go do `documents` pole `MultiLanguageBatchInput` obiektu. 

4. Wywołanie klienta `key_phrases()` funkcją `MultiLanguageBatchInput` obiektu jako parametr do wysłania do dokumentów. Jeśli nie zostały zwrócone wszystkie wyniki, wydrukuj te instrukcje.

    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.key_phrases(
          multi_language_batch_input: input_documents
      )
    
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
          puts "Document Id: #{document.id}"
          puts '  Key Phrases'
          document.key_phrases.each do |key_phrase|
            puts "    #{key_phrase}"
          end
        end
      else
        puts 'No results data..'
      end
    end
    ```

5. Wywołanie funkcji `KeyPhraseExtractionExample`

    ```ruby
    KeyPhraseExtractionExample(textAnalyticsClient)
    ```

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także

 [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)

---
title: 'Szybki start: Wywoływanie usługi analiza tekstu poznawczej przy użyciu zestawu Ruby SDK'
titleSuffix: Azure Cognitive Services
description: Uzyskaj informacje i przykłady kodu, aby szybko rozpocząć korzystanie z interfejs API analizy tekstu na platformie Azure Cognitive Services.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 3f18b77fe436328e79df351b9c5edcf6dc289ad7
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697267"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>Szybki start: Wywoływanie usługi analiza tekstu przy użyciu zestawu Ruby SDK

<a name="HOLTop"></a>


Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć analizowanie języka z zestawem SDK analiza tekstu for Ruby. Chociaż interfejs API REST [Analiza tekstu](//go.microsoft.com/fwlink/?LinkID=759711) jest zgodny z większością języków programowania, zestaw SDK zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb).

Zapoznaj się z tematem [API definitions (Definicje interfejsu API)](//go.microsoft.com/fwlink/?LinkID=759346), zawierającym dokumentację techniczną interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

* [2.5.5 Ruby lub nowszy](https://www.ruby-lang.org/)
* [Zestaw SDK](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) analizy tekstu dla języka Ruby
 
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Utwórz projekt Ruby i Zainstaluj zestaw SDK

1. Utwórz nowy projekt Ruby i Dodaj nowy plik o nazwie `Gemfile`.
2. Dodaj analiza tekstu SDK do projektu, dodając Poniższy kod do `Gemfile`.

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>Tworzenie klienta analizy tekstu

1. Utwórz nowy plik o nazwie `TextAnalyticsExamples.rb` w ulubionym edytorze lub w środowisku IDE. Zaimportuj zestaw SDK analiza tekstu.

2. Obiekt poświadczeń zostanie użyty przez klienta analiza tekstu. Utwórz go przy `CognitiveServicesCredentials.new()` użyciu i przekazanie klucza subskrypcji.

3. Utwórz klienta z prawidłowym punktem końcowym analiza tekstu.

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

Za pomocą zestawu SDK analiza tekstu lub interfejsu API można wykonać analizę tonacji na zestawie rekordów tekstowych. Poniższy przykład wyświetla wyniki tonacji dla kilku dokumentów.

1. Utwórz nową funkcję o nazwie `SentimentAnalysisExample()` , która powoduje, że klient analizy tekstu utworzony powyżej jako parametr.

2. Zdefiniuj zestaw `MultiLanguageInput` obiektów do przeanalizowania. Dodaj język i tekst dla każdego obiektu. Identyfikator może być dowolną wartością.

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

3. W tej samej funkcji Połącz dokumenty z listą. Dodaj go do `documents` pola `MultiLanguageBatchInput` obiektu. 

4. Wywołaj `sentiment()` funkcję klienta `MultiLanguageBatchInput` z obiektem jako parametrem do wysłania dokumentów. Jeśli zostaną zwrócone jakieś wyniki, wydrukuj je.
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

### <a name="output"></a>Output

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Wykrywanie języka

Usługa analiza tekstu może wykryć język dokumentu tekstowego w wielu językach i ustawieniach regionalnych. Poniższy przykład wyświetla język, w którym zapisano kilka dokumentów.

1. Utwórz nową funkcję o nazwie `DetectLanguageExample()` , która pobiera klienta analizy tekstu powyżej jako parametr. 

2. Zdefiniuj zestaw `LanguageInput` obiektów do przeanalizowania. Dodaj język i tekst dla każdego obiektu. Identyfikator może być dowolną wartością.

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

3. W tej samej funkcji Połącz dokumenty z listą. Dodaj go do `documents` pola `LanguageBatchInput` obiektu. 

4. Wywołaj `detect_language()` funkcję klienta `LanguageBatchInput` z obiektem jako parametrem do wysłania dokumentów. Jeśli zostaną zwrócone jakieś wyniki, wydrukuj je.
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

5. Wywoływanie funkcji`DetectLanguageExample`

    ```ruby
    DetectLanguageExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Usługa analiza tekstu umożliwia odróżnienie i wyodrębnienie różnych jednostek (osób, miejsc i rzeczy) w dokumentach tekstowych. Poniższy przykład wyświetla jednostki Znalezione w kilku przykładowych dokumentach.

1. Utwórz nową funkcję o nazwie `Recognize_Entities()` , która powoduje, że klient analizy tekstu utworzony powyżej jako parametr.

2. Zdefiniuj zestaw `MultiLanguageInput` obiektów do przeanalizowania. Dodaj język i tekst dla każdego obiektu. Identyfikator może być dowolną wartością.

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

3. W tej samej funkcji Połącz dokumenty z listą. Dodaj go do `documents` pola `MultiLanguageBatchInput` obiektu. 

4. Wywołaj `entities()` funkcję klienta `MultiLanguageBatchInput` z obiektem jako parametrem do wysłania dokumentów. Jeśli zostaną zwrócone jakieś wyniki, wydrukuj je.

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

5. Wywoływanie funkcji`RecognizeEntitiesExample`
    ```ruby
    RecognizeEntitiesExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

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

Usługa analiza tekstu może wyodrębnić frazy kluczowe w zdaniach. Poniższy przykład wyświetla jednostki Znalezione w kilku przykładowych dokumentach w wielu językach.

1. Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()` , która powoduje, że klient analizy tekstu utworzony powyżej jako parametr.

2. Zdefiniuj zestaw `MultiLanguageInput` obiektów do przeanalizowania. Dodaj język i tekst dla każdego obiektu. Identyfikator może być dowolną wartością.

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

3. W tej samej funkcji Połącz dokumenty z listą. Dodaj go do `documents` pola `MultiLanguageBatchInput` obiektu. 

4. Wywołaj `key_phrases()` funkcję klienta `MultiLanguageBatchInput` z obiektem jako parametrem do wysłania dokumentów. Jeśli zostaną zwrócone jakieś wyniki, wydrukuj je.

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

5. Wywoływanie funkcji`KeyPhraseExtractionExample`

    ```ruby
    KeyPhraseExtractionExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

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

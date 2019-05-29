---
title: 'Szybki start: Wywoływanie usługi Analiza tekstu przy użyciu zestaw SDK języka Go'
titleSuffix: Azure Cognitive Services
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API analizy tekstu usług Microsoft Cognitive Services.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/23/2019
ms.author: aahi
ms.openlocfilehash: 44def29292bc882fdaa08ff76667742756f178b8
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299611"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-go-sdk"></a>Szybki start: Wywoływanie usługi Analiza tekstu przy użyciu zestaw SDK języka Go 
<a name="HOLTop"></a>

Użyj tego przewodnika Szybki Start, aby rozpocząć analizowanie języka przy użyciu zestawu SDK analizy tekstu dla języka Go. Ten artykuł pokazuje, jak wykrywanie języka, analizowanie tonacji, wyodrębnianie kluczowych fraz i zidentyfikować połączonych jednostek. Gdy interfejs API REST jest zgodny z większość języków programowania, zestaw SDK udostępnia łatwy sposób zintegrować usługę ze swoimi aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices).

## <a name="prerequisites"></a>Wymagania wstępne

* Analiza tekstu [zestawu SDK dla języka Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Musisz również mieć [punkt końcowy i klucz dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany dla Ciebie podczas tworzenia konta.

## <a name="set-up-a-new-project"></a>Konfigurowanie nowego projektu

Utwórz nowy projekt z rzeczywistym użyciem w IDE lub wybrany edytor kodu. Następnie dodaj następującą instrukcję import do pliku z rzeczywistym użyciem.

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
)
```

Dodaj następujące funkcje do projektu, ponieważ większość parametry i właściwości dla tego przewodnika Szybki Start oczekiwane wskaźniki ciągu i wartość logiczna.

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

## <a name="create-text-analytics-client-and-authenticate-credentials"></a>Tworzenie klienta analizy tekstu i uwierzytelnić poświadczeń

W funkcji main w projekcie, Utwórz nową `TextAnalytics` obiektu. Użyj poprawny region platformy Azure dla Twojej subskrypcji analizy tekstu. Na przykład: `https://eastus.api.cognitive.microsoft.com`. Jeśli używasz klucza wersji próbnej, nie trzeba zaktualizować lokalizację.

```golang
//Replace 'eastus' with the correct region for your Text Analytics subscription
textAnalyticsClient := textanalytics.New("https://eastus.api.cognitive.microsoft.com")
```

Utwórz zmienną dla klucza i przekaż go do funkcji `autorest.NewCognitiveServicesAuthorizer` który następnie zostanie przekazany do klienta `authorizer` właściwości.

```golang
subscriptionKey := "<<subscriptionKey>>"
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>Analiza tonacji

Utwórz nową funkcję o nazwie `SentimentAnalysis()` przyjmującej klienta utworzone wcześniej. Utwórz listę `MultiLanguageInput` obiektów, zawierającego dokumenty przeznaczone do przeanalizowania. Każdy obiekt będzie zawierać `id`, `Language` i `text` atrybutu. `text` Atrybut przechowuje tekst, który ma być analizowane `language` jest język dokumentu, a `id` może być dowolna wartość. 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("1"),
            Text:StringPointer("This was a waste of my time. The speaker put me to sleep."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("2"),
            Text:StringPointer("No tengo dinero ni nada que dar..."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("it"),
            ID:StringPointer("3"),
            Text:StringPointer("L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

W tej samej funkcji, należy wywołać `textAnalyticsclient.Sentiment()` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i Drukuj identyfikator każdego dokumentu i ocenę tonacji. Podczas oceny bliższa 1 oznacza pozytywną tonację, wyniku przybliżania 0 wskazuje negatywną tonację.

```golang
result, _ := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

W funkcji main w projekcie, wywołaj `SentimentAnalysis()`.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz nową funkcję o nazwie `LanguageDetection()` przyjmującej klienta utworzone wcześniej. Utwórz listę `LanguageInput` obiektów, zawierającego dokumenty przeznaczone do przeanalizowania. Każdy obiekt będzie zawierać `id` i `text` atrybutu. `text` Atrybut przechowuje tekst, który ma być analizowane i `id` może być dowolna wartość. 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
        textanalytics.LanguageInput {
            ID:StringPointer("1"),
            Text:StringPointer("Este es un document escrito en Español."),
        },
        textanalytics.LanguageInput {
            ID:StringPointer("2"),
            Text:StringPointer("这是一个用中文写的文件"),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

W tej samej funkcji, należy wywołać `textAnalyticsclient.DetectLanguage()` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i Drukuj identyfikator każdego dokumentu i wykryty język.

```golang
result, _ := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

W funkcji main w projekcie, wywołaj `LanguageDetection()`.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0 Detected Languages with Score: English 1.000000,
Document ID: 1 Detected Languages with Score: Spanish 1.000000,
Document ID: 2 Detected Languages with Score: Chinese_Simplified 1.000000,
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Utwórz nową funkcję o nazwie `ExtractEntities()` przyjmującej klienta utworzone wcześniej. Utwórz listę `MultiLanguageInput` obiektów, zawierającego dokumenty przeznaczone do przeanalizowania. Każdy obiekt będzie zawierać `id`, `language`, a `text` atrybutu. `text` Atrybut przechowuje tekst, który ma być analizowane `language` jest język dokumentu, a `id` może być dowolna wartość. 

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("1"),
            Text:StringPointer("La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

W tej samej funkcji `call textAnalyticsclient.Entities()` i uzyskać wynik. Następnie iterację wyniki i drukowania, każdy dokument identyfikator użytkownika i wyodrębnić czy ocena jednostek.

```golang
    result, _ := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

W funkcji main w projekcie, wywołaj `ExtractEntities()`.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000

Document ID: 1
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 21  Length: 9   Score: 0.999756
        Name: Redmond (Washington)  Type: Location
            Offset: 60  Length: 7   Score: 0.991128
        Name: 21 kilómetros Type: Quantity  Sub-Type: Dimension

            Offset: 71  Length: 13  Score: 0.800000
        Name: Seattle   Type: Location
            Offset: 88  Length: 7   Score: 0.999878
```

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz nową funkcję o nazwie `ExtractKeyPhrases()` przyjmującej klienta utworzone wcześniej. Utwórz listę `MultiLanguageInput` obiektów, zawierającego dokumenty przeznaczone do przeanalizowania. Każdy obiekt będzie zawierać `id`, `language`, a `text` atrybutu. `text` Atrybut przechowuje tekst, który ma być analizowane `language` jest język dokumentu, a `id` może być dowolna wartość.

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("ja"),
            ID:StringPointer("0"),
            Text:StringPointer("猫は幸せ"),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("de"),
            ID:StringPointer("1"),
            Text:StringPointer("Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("2"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("3"),
            Text:StringPointer("A mi me encanta el fútbol!"),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

W tej samej funkcji należy wywołać textAnalyticsclient.KeyPhrases() i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i identyfikator każdego dokumentu i frazy kluczowe wyodrębnione wydruku.

```golang
    result, _ := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

W funkcji main w projekcie, wywołaj `ExtractKeyPhrases()`.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
    Extracted Key Phrases:
        幸せ

Document ID: 1
    Extracted Key Phrases:
        Stuttgart
        Hotel
        Fahrt
        Fu

Document ID: 2
    Extracted Key Phrases:
        cat
        veterinarian

Document ID: 3
    Extracted Key Phrases:
        fútbol
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także

 [Omówienie analizy tekstu](../overview.md) [Frequently asked questions (FAQ) (Często zadawane pytania (FAQ))](../text-analytics-resource-faq.md)

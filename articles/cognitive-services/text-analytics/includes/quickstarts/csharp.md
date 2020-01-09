---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: ea526648b1b37919eb41953937d3afa72f7f39e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446270"
---
<a name="HOLTop"></a>

[Dokumentacja referencyjna](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [pakietu (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [próbka](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE]
> Kod w tym artykule używa metod synchronicznych analiza tekstu .NET SDK dla uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Na przykład wywoływanie [SentimentBatchAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) zamiast [tonacji ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-text-analytics-azure-resource"></a>Tworzenie zasobu analiza tekstu platformy Azure

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Tworzenie nowej aplikacji platformy .NET Core

Za pomocą środowiska IDE programu Visual Studio Utwórz nową aplikację konsolową platformy .NET Core. Spowoduje to utworzenie prostego projektu "Hello world" z pojedynczym C# plikiem źródłowym: *program.cs*.

Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który zostanie otwarty, wybierz pozycję **Przeglądaj** i Wyszukaj `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Kliknij go, a następnie **Zainstaluj**. Można również użyć [konsoli Menedżera pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Otwórz plik *program.cs* i Dodaj następujące dyrektywy `using`:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

W klasie `Program` aplikacji Utwórz zmienne dla klucza i punktu końcowego zasobu. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Zastąp metodę `Main` aplikacji. Metody wywoływane tutaj zostaną zdefiniowane później.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

## <a name="object-model"></a>Model obiektów

Klient analiza tekstu jest obiektem [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) , który jest uwierzytelniany na platformie Azure przy użyciu klucza, i udostępnia funkcje do akceptowania tekstu jako pojedyncze ciągi lub jako partia. Możesz wysyłać tekst do interfejsu API synchronicznie lub asynchronicznie. Obiekt Response będzie zawierać informacje o analizie dla każdego wysyłanego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek](#entity-recognition)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz nową klasę `ApiKeyServiceClientCredentials` do przechowywania poświadczeń i Dodaj je do żądań klienta. W tym celu należy utworzyć przesłonięcie dla `ProcessHttpRequestAsync()`, które dodaje klucz do nagłówka `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Utwórz metodę, aby utworzyć wystąpienie obiektu [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) z punktem końcowym i obiektem `ApiKeyServiceClientCredentials` zawierającym klucz.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

W metodzie `main()` programu Wywołaj metodę uwierzytelniania w celu utworzenia wystąpienia klienta.

## <a name="sentiment-analysis"></a>Analiza opinii

Utwórz nową funkcję o nazwie `SentimentAnalysisExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję [tonacji ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) . Zwrócony obiekt [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) będzie zawierać tonacji `Score` w przypadku powodzenia i `errorMessage`, jeśli nie. 

Wynik zbliżony do 0 wskazuje negatywną tonacji, podczas gdy wynik zbliżony do 1 wskazuje pozytywną tonacji.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

### <a name="output"></a>Dane wyjściowe

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz nową funkcję o nazwie `languageDetectionExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Zwrócony obiekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) będzie zawierać listę wykrytych języków w `DetectedLanguages`, jeśli zakończy się pomyślnie, a `errorMessage`, jeśli nie.  Drukowanie pierwszego zwracanego języka.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Można użyć parametru `countryHint`, aby określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Dane wyjściowe

```console
Language: English
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Utwórz nową funkcję o nazwie `RecognizeEntitiesExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję [Entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Wykonaj iterację w wynikach. Zwrócony obiekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) będzie zawierać listę wykrytych jednostek w `Entities`, jeśli zakończy się pomyślnie, a `errorMessage`, jeśli nie. Dla każdej wykrytej jednostki Wydrukuj jej typ, podtyp, nazwę witryny Wikipedia (jeśli istnieją), a także lokalizacje w oryginalnym tekście.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]


### <a name="output"></a>Dane wyjściowe

```console
Entities:
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0,      Length: 9,      Score: 1.000
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25,     Length: 10,     Score: 1.000
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40,     Length: 10,     Score: 0.999
    Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54,     Length: 7,      Score: 0.800
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54,     Length: 13,     Score: 0.800
    Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89,     Length: 5,      Score: 0.800
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()`, która powoduje, że klient został utworzony wcześniej i wywołaj funkcję [frazy kluczowej ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Wynik będzie zawierać listę wykrytych fraz kluczy w `KeyPhrases`, jeśli się powiedzie, i `errorMessage`, jeśli nie. Drukuj wszystkie wykryte frazy kluczy.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Dane wyjściowe

```console
Key phrases:
    cat
    veterinarian
```

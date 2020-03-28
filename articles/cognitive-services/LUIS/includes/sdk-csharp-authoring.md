---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 53e6382cf8d046b2c9818b906890bc64642fd2ed
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372054"
---
Użyj biblioteki klienta tworzenia języka (LUIS) dla platformy .NET, aby:

* Tworzenie aplikacji
* Dodawanie intencji, encji i wypowiedzi przykładowych
* Dodawanie funkcji, takich jak lista fraz
* Trenuj i publikuj aplikację

[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [dokumentacja biblioteki kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [źródłowy pakiet tworzenia (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [C# Przykłady](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto portalu language understanding (LUIS) — [utwórz je bezpłatnie](https://www.luis.ai)
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).


## <a name="setting-up"></a>Konfigurowanie

### <a name="get-your-language-understanding-luis-starter-key"></a>Pobierz klucz startowy rozumienia języka (LUIS)

Pobierz [klucz startowy,](../luis-how-to-azure-subscription.md#starter-key) tworząc zasób tworzenia usługi LUIS. Zachowaj klucz i region klucza do następnego kroku.

### <a name="create-an-environment-variable"></a>Tworzenie zmiennej środowiskowej

Korzystając z klucza i regionu klucza, utwórz dwie zmienne środowiskowe do uwierzytelniania:

* `COGNITIVESERVICE_AUTHORING_KEY`- Klucz zasobu do uwierzytelniania żądań.
* `COGNITIVESERVICE_REGION`- Region skojarzony z kluczem. Na przykład: `westus`.

Użyj instrukcji dla systemu operacyjnego.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

Po dodaniu zmiennej środowiskowej uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[Macos](#tab/unix)

Edytuj `.bash_profile`swój program i dodaj zmienną środowiskową:

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji języka C#

Utwórz nową aplikację .NET Core w preferowanym edytorze lub ide.

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia `new` dotnet, `language-understanding-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" C# `Program.cs`z jednym plikiem źródłowym: .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Zmień katalog na nowo utworzony folder aplikacji.

1. Aplikację można utworzyć za pomocą:

    ```dotnetcli
    dotnet build
    ```

    Dane wyjściowe kompilacji nie powinny zawierać żadnych ostrzeżeń ani błędów.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```


### <a name="install-the-sdk"></a>Instalacja zestawu SDK

W katalogu aplikacji zainstaluj bibliotekę klienta tworzenia języka (LUIS) dla platformy .NET za pomocą następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, biblioteka klienta jest dostępna jako pakiet NuGet do pobrania.


## <a name="object-model"></a>Model obiektu

Klient tworzenia języka (LUIS) jest obiektem [LUISAuthoringClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) który uwierzytelnia się na platformie Azure, który zawiera klucz autora.

Po utworzeniu klienta użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* Aplikacje - [tworzenie,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) [usuwanie,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) [publikowanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Przykładowe wypowiedzi — [dodawanie przez partię](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [usuwanie według identyfikatora](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Funkcje — zarządzanie [listami fraz](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* Model — zarządzanie [intencjami](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) i encjami
* Wzorzec - zarządzanie [wzorami](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Pociąg - [trenuj](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) aplikację i ankietę na [temat statusu szkolenia](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Wersje](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) — zarządzanie za pomocą klonowania, eksportowania i usuwania


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta tworzenia języka (LUIS) dla platformy .NET:

* [Tworzenie aplikacji](#create-a-luis-app)
* [Dodawanie jednostek](#create-entities-for-the-app)
* [Dodawanie intencji](#create-intent-for-the-app)
* [Dodawanie przykładowych wypowiedzi](#add-example-utterance-to-intent)
* [Trenuj aplikację](#train-the-app)
* [Publikowanie aplikacji](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu otwórz plik *Program.cs* w preferowanym edytorze lub w programie IDE. Zastąp `using` istniejący `using` kod następującymi dyrektywami:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

1. Utwórz zmienną do zarządzania kluczem autora `COGNITIVESERVICES_AUTHORING_KEY`pobranym ze zmiennej środowiskowej o nazwie . Jeśli utworzono zmienną środowiskową po uruchomieniu aplikacji, edytor, IDE lub powłoki uruchomionej będzie musiał zostać zamknięty i ponownie załadowany, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone później.

1. Tworzenie zmiennych do przechowywania regionu tworzenia i punktu końcowego. Region klucza tworzenia zależy od tego, gdzie jest tworzenie. Trzy [regiony tworzenia](../luis-reference-regions.md) to:

    * Australia -`australiaeast`
    * Europa -`westeurope`
    * Stany Zjednoczone i inne regiony - `westus` (Domyślnie)

    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Utwórz [obiekt ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) za pomocą klucza i użyj go z punktem końcowym do utworzenia obiektu [LUISAuthoringClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet)

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Tworzenie aplikacji usługi LUIS

1. Utwórz aplikację usługi LUIS, która będzie zawierała model przetwarzania języka naturalnego (NLP), zawierający intencje, jednostki i wypowiedzi przykładowe.

1. Utwórz [przykład tworzenia aplikacji](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). Nazwa i kultura języka są wymagane właściwości.

1. Wywołanie [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) metody. Odpowiedzią jest identyfikator aplikacji.

    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Tworzenie intencji dla aplikacji
Obiekt podstawowy w modelu aplikacji usługi LUIS jest intencją. Intencji jest wyrównane z grupowania _intencji_wypowiedzi użytkownika . Użytkownik może zadać pytanie lub złożyć oświadczenie w poszukiwaniu konkretnej _zamierzonej_ odpowiedzi od bota (lub innej aplikacji klienckiej). Przykładami intencji są rezerwacja lotu, pytanie o pogodę w mieście docelowym i pytanie o informacje kontaktowe dotyczące obsługi klienta.

Utwórz [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) o nazwie unikatowej intencji, a następnie przekaż identyfikator aplikacji, identyfikator wersji i ModelCreateObject do [metody Model.AddIntentAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) Odpowiedź jest identyfikatorem intencji.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Tworzenie encji dla aplikacji

Chociaż jednostki nie są wymagane, znajdują się one w większości aplikacji. Jednostka wyodrębnia informacje z wypowiedzi użytkownika, niezbędne do fullfil intencji użytkownika. Istnieje kilka typów [wstępnie utworzonych](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) i niestandardowych jednostek, z których każdy ma własne modele obiektu transformacji danych (DTO).  Typowe wstępnie utworzone encje do dodania do aplikacji obejmują [numer](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [liczba porządkowa](../luis-reference-prebuilt-ordinal.md).

Ta metoda **AddEntities** stworzyła prostą `Location` `Class` jednostkę z `Flight` dwiema rolami, prostą jednostką, jednostką złożoną i dodaje kilka wstępnie utworzonych jednostek.

Ważne jest, aby wiedzieć, że jednostki nie są oznaczone z zamiarem. Mogą i zwykle mają zastosowanie do wielu intencji. Tylko przykład wypowiedzi użytkownika są oznaczone dla określonego, pojedynczego zamiaru.

Metody tworzenia dla jednostek są częścią [Model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) klasy. Każdy typ jednostki ma swój własny model obiektu przekształcania `model` danych (DTO), zwykle zawierający wyraz w obszarze nazw [Modele.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet)

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Dodawanie przykładu wypowiedź do intencji

W celu określenia intencji wypowiedź i wyodrębnić jednostki, aplikacja potrzebuje przykładów wypowiedzi. Przykłady należy kierować określonego, pojedynczy zamiar i należy oznaczyć wszystkie encje niestandardowe. Wstępnie utworzone jednostki nie muszą być oznaczane.

Dodaj wypowiedzi przykład, tworząc listę [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) obiektów, jeden obiekt dla każdego wypowiedź przykład. Każdy przykład należy oznaczyć wszystkie jednostki słownikiem pary nazwy/wartości nazwy jednostki i wartości jednostki. Wartość jednostki powinna być dokładnie tak, jak pojawia się w tekście wypowiedź przykład.

Wywołanie [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) z identyfikatorem aplikacji, identyfikatorem wersji i listą przykładów. Połączenie odpowiada z listą wyników. Należy sprawdzić wynik każdego przykładu, aby upewnić się, że został pomyślnie dodany do modelu.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]

**Metody CreateUtterance** i **CreateLabel** są metodami narzędziowymi ułatwiające tworzenie obiektów.

## <a name="train-the-app"></a>Uczenie aplikacji

Po utworzeniu modelu aplikacja usługi LUIS musi zostać przeszkolona dla tej wersji modelu. Przeszkolony model może być używany w [kontenerze](../luis-container-howto.md)lub [opublikowany](../luis-how-to-publish-app.md) w szczelinach przejściowych lub produktowych.

[Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) Metoda wymaga identyfikatora aplikacji i identyfikator wersji.

Bardzo mały model, taki jak ten szybki start, będzie trenował bardzo szybko. W przypadku aplikacji na poziomie produkcji szkolenia aplikacji powinny zawierać wywołanie sondowania [do GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) metody, aby określić, kiedy lub jeśli szkolenie zakończyło się pomyślnie. Odpowiedź jest lista [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) obiektów o osobnym stanie dla każdego obiektu. Wszystkie obiekty muszą być skuteczne, aby szkolenie było uważane za kompletne.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Publikowanie aplikacji do rozumienia języka

Opublikuj aplikację usługi LUIS przy użyciu [metody PublishAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) Spowoduje to opublikowanie bieżącej uczonej wersji do określonego gniazda w punkcie końcowym. Aplikacja kliencka używa tego punktu końcowego do wysyłania wypowiedzi użytkownika do przewidywania intencji i wyodrębniania jednostek.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `dotnet run` pomocą polecenia z katalogu aplikacji.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz oczyścić, możesz usunąć aplikację usługi LUIS. Usuwanie aplikacji odbywa się za pomocą [metody Apps.DeleteAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) Aplikację można również usunąć z [portalu usługi LUIS](https://www.luis.ai).
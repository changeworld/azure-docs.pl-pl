---
title: 'Szybki start: Biblioteka klienta aparatu rozpoznawania formularzy dla platformy .NET | Microsoft Docs'
description: Wprowadzenie do biblioteki klienta aparatu rozpoznawania formularzy dla platformy .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: pafarley
ms.openlocfilehash: ce1cdadcdc69fb5539394aa9bf402aa9463311e9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057657"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Szybki start: Biblioteka klienta aparatu rozpoznawania formularzy dla platformy .NET

Wprowadzenie do biblioteki klienta aparatu rozpoznawania formularzy dla platformy .NET. Aparat rozpoznawania formularzy jest usługą poznawczej, która korzysta z technologii uczenia maszynowego do identyfikowania i wyodrębniania par klucz/wartość oraz danych tabeli z dokumentów formularzy. Następnie wyświetla ustrukturyzowane dane, które obejmują relacje w oryginalnym pliku. Wykonaj następujące kroki, aby zainstalować pakiet SDK i wypróbować przykładowy kod dla podstawowych zadań.

Biblioteka klienta aparatu rozpoznawania w programie .NET umożliwia:

* [Uczenie niestandardowego modelu aparatu rozpoznawania formularzy](#train-a-custom-model)
* [Pobierz listę wyodrębnionych kluczy](#get-a-list-of-extracted-keys)
* [Analizowanie formularzy przy użyciu modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Pobieranie listy modeli niestandardowych](#get-a-list-of-custom-models)
* [Usuwanie modelu niestandardowego](#delete-a-custom-model)

[](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | Pakiet[kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer)[](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/) źródłowego biblioteki dokumentacji referencyjnej (NuGet) | 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Obiekt BLOB usługi Azure Storage zawierający zestaw danych szkoleniowych. Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../build-training-data-set.md) w celu uzyskania wskazówek i opcji tworzenia danych szkoleniowych. 

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-form-recognizer-azure-resource"></a>Tworzenie usługi Azure Resource dla aparatu rozpoznawania

[!INCLUDE [create resource](../includes/create-resource.md)]

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [Utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `FORM_RECOGNIZER_KEY`.

### <a name="create-a-new-c-application"></a>Utwórz nową C# aplikację

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie. `formrecognizer-quickstart` To polecenie tworzy prosty projekt "Hello world" C# z pojedynczym plikiem źródłowym: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Następnie skompiluj aplikację przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu projektu Otwórz plik _program.cs_ w preferowanym edytorze lub w środowisku IDE. Dodaj następujące instrukcje `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Następnie Dodaj następujący kod w metodzie **głównej** aplikacji. To zadanie asynchroniczne zostanie zdefiniowane później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

W katalogu aplikacji zainstaluj bibliotekę klienta aparatu rozpoznawania dla platformy .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują główne funkcje zestawu SDK aparatu rozpoznawania formularzy.

|Name|Opis|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Ta klasa jest wymagana dla wszystkich funkcji aparatu rozpoznawania formularzy. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Ta klasa jest używana do uczenia niestandardowego modelu aparatu rozpoznawania formularzy przy użyciu własnych danych wejściowych szkoleniowych. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Ta klasa dostarcza wyniki operacji nauczenia modelu niestandardowego, łącznie z IDENTYFIKATORem modelu, którego można następnie użyć do analizowania formularzy. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Ta klasa dostarcza wyniki operacji analizy modelu niestandardowego. Zawiera listę wystąpień **ExtractedPage** . |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Ta klasa reprezentuje wszystkie dane wyodrębnione z jednego dokumentu formularza.|

## <a name="code-examples"></a>Przykłady kodu

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienckiej aparatu rozpoznawania w programie .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Uczenie niestandardowego modelu aparatu rozpoznawania formularzy](#train-a-custom-model)
* [Pobierz listę wyodrębnionych kluczy](#get-a-list-of-extracted-keys)
* [Analizowanie formularzy przy użyciu modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Pobieranie listy modeli niestandardowych](#get-a-list-of-custom-models)
* [Usuwanie modelu niestandardowego](#delete-a-custom-model)

## <a name="define-variables"></a>Definiowanie zmiennych

Przed zdefiniowaniem jakichkolwiek metod Dodaj następujące definicje zmiennych na początku klasy **programu** . Należy samodzielnie wypełnić niektóre zmienne. 

* Wartość punktu końcowego usługi można znaleźć w sekcji **Przegląd** w Azure Portal. 
* Aby pobrać adres URL sygnatury dostępu współdzielonego dla danych szkoleniowych, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener, a następnie wybierz pozycję **Pobierz sygnaturę dostęp współdzielony**. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Poniżej metody Zdefiniuj zadanie, `Main`do którego odwołuje się. `Main` W tym miejscu można uwierzytelnić obiekt klienta przy użyciu zdefiniowanych powyżej zmiennych subskrypcji. Pozostałe metody zostaną zdefiniowane później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

W poniższej metodzie obiekt klienta aparatu rozpoznawania formularzy jest używany do uczenia nowego modelu rozpoznawania w dokumentach przechowywanych w kontenerze obiektów blob platformy Azure. Używa metody pomocnika, aby wyświetlić informacje o nowo przeszkolonym modelu (reprezentowane przez obiekt [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) ) i zwraca identyfikator modelu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

W poniższej metodzie pomocnika są wyświetlane informacje o modelu aparatu rozpoznawania formularzy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Pobierz listę wyodrębnionych kluczy

Po zakończeniu szkolenia model niestandardowy będzie przechowywać listę kluczy wyodrębnionych z dokumentów szkoleniowych. Oczekuje się, że przyszłe dokumenty formularza zawierają te klucze i wyodrębni odpowiednie wartości w operacji analizy. Użyj następującej metody, aby pobrać listę wyodrębnionych kluczy i wydrukować ją w konsoli programu. Jest to dobry sposób, aby sprawdzić, czy proces uczenia był skuteczny.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy przy użyciu modelu niestandardowego

Ta metoda używa klienta aparatu rozpoznawania formularzy i identyfikatora modelu do analizy dokumentu PDF i wyodrębnienia danych klucza/wartości. Używa metody pomocnika, aby wyświetlić wyniki (reprezentowane przez obiekt [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) ).

> [!NOTE]
> Poniższa metoda analizuje formularz PDF. Podobne metody służące do analizowania formularzy JPEG i PNG można znaleźć w pełnym przykładowym kodzie w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

W poniższej metodzie pomocnika są wyświetlane informacje o operacji analizy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Pobieranie listy modeli niestandardowych

Można zwrócić listę wszystkich przeszkolonych modeli należących do Twojego konta i można pobrać informacje o tym, kiedy zostały utworzone. Lista modeli jest reprezentowana przez obiekt [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Usuwanie modelu niestandardowego

Jeśli chcesz usunąć niestandardowy model z konta, użyj następującej metody:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację, wywołując `dotnet run` polecenie z katalogu aplikacji.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ponadto, jeśli przeszkolony model niestandardowy, który ma zostać usunięty z konta, należy uruchomić metodę w obszarze [usuwanie modelu niestandardowego](#delete-a-custom-model).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto biblioteki klienta .NET rozpoznawania formularzy do uczenia modelu niestandardowego i analizowania formularzy. Następnie zapoznaj się z poradami, aby utworzyć lepszy zestaw danych szkoleniowych i uzyskać bardziej dokładne modele.

> [!div class="nextstepaction"]
>[Tworzenie zestawu danych szkoleniowych](../build-training-data-set.md)

* [Co to jest aparat rozpoznawania formularzy?](../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

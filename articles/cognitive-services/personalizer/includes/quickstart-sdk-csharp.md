---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7fd63841cabd91d46dd311f571fd100bbcfdd0fe
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122887"
---
[Przykłady](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | referencyjnej[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Samples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Korzystanie z tego przewodnika Szybki start

Istnieje kilka kroków, aby użyć tego przewodnika Szybki start:

* W witrynie Azure portal utwórz zasób Personalizer
* W witrynie Azure portal dla zasobu Personalizer na stronie **Konfiguracja** zmień częstotliwość aktualizacji modelu na bardzo krótki interwał
* W edytorze kodu utwórz plik kodu i edytuj plik kodu
* W wierszu polecenia lub terminalu zainstaluj pakiet SDK z wiersza polecenia
* W wierszu polecenia lub terminalu uruchom plik kodu

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji języka C#

Utwórz nową aplikację .NET Core w preferowanym edytorze lub ide.

W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia `new` dotnet, `personalizer-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" C# `Program.cs`z jednym plikiem źródłowym: .

```console
dotnet new console -n personalizer-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

```console
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

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

W katalogu aplikacji zainstaluj bibliotekę klienta personalizatora dla platformy .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Jeśli używasz środowiska IDE programu Visual Studio, biblioteka klienta jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektu

Klient Personalizer jest [personalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) obiektu, który uwierzytelnia się na platformie Azure przy użyciu microsoft.rest.ServiceClientCredentials, który zawiera klucz.

Aby poprosić o jeden najlepszy element zawartości, utwórz [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview), a następnie przekaż go [do klienta. Metoda rangi.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) Rank Metoda zwraca RankResponse.

Aby wysłać wynik nagrody do Personalizer, utwórz [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview), a następnie przekazać go do [klienta. Metoda nagradzania.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview)

Określenie wyniku nagrody w tym przewodniku Szybki start jest banalne. W systemie produkcyjnym określenie, co wpływa na [wynik nagrody](../concept-rewards.md) i o ile może być złożonym procesem, możesz zdecydować się na zmianę w czasie. Ta decyzja projektowa powinna być jedną z podstawowych decyzji w architekturze Personalizer.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta personalizatora dla platformy .NET:

* [Tworzenie klienta personalizatora](#create-a-personalizer-client)
* [Interfejs API rangi](#request-the-best-action)
* [Api nagród](#send-a-reward)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu otwórz plik **Program.cs** w preferowanym edytorze lub w programie IDE. Zastąp `using` istniejący `using` kod następującymi dyrektywami:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Dodawanie informacji o zasobie Personalizer

W **klasie Program** utwórz zmienne dla klucza platformy Azure i punktu końcowego `PERSONALIZER_RESOURCE_KEY` `PERSONALIZER_RESOURCE_ENDPOINT`zasobu pobrane ze zmiennych środowiskowych o nazwie i . Jeśli utworzono zmienne środowiskowe po uruchomieniu aplikacji, edytor, IDE lub powłoki uruchomionej będzie musiał zostać zamknięty i ponownie załadowany, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone w dalszej części tego przewodnika Szybki start.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Tworzenie klienta personalizatora

Następnie utwórz metodę zwracania klienta Personalizer. Parametrem metody jest `PERSONALIZER_RESOURCE_ENDPOINT` apiKey jest `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Uzyskaj produkty spożywcze jako akcje rangowalne

Akcje reprezentują wybory zawartości, z których chcesz Personalizer wybrać najlepszy element zawartości. Dodaj następujące metody do Klasy Program do reprezentowania zestawu akcji i ich funkcji.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Uzyskaj preferencje użytkownika dla kontekstu

Dodaj następujące metody do klasy Program, aby uzyskać dane wejściowe użytkownika z wiersza polecenia dla porę dnia i bieżące preferencje dotyczące żywności. Będą one używane jako funkcje kontekstu.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Obie metody `GetKey` używają tej metody do odczytu wyboru użytkownika z wiersza polecenia.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Tworzenie pętli uczenia się

Pętla uczenia się Personalizer to cykl połączeń [rangi](#request-the-best-action) i [nagród.](#send-a-reward) W tym przewodniku Szybki start każde wywołanie rangi, aby spersonalizować zawartość, następuje wywołanie nagrodą, aby poinformować Personalizatora, jak dobrze działała usługa.

Poniższy kod pętli przez cykl z prośbą użytkownika ich preferencje w wierszu polecenia, wysyłanie tych informacji do Personalizer, aby wybrać najlepszą akcję, przedstawiając wybór do wyboru z listy, a następnie wysyłanie wynik nagrody do Personalizer sygnalizując, jak dobrze usługa zrobiła w swoim wyborze.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Przed uruchomieniem pliku kodu dodaj następujące metody, które [utrzymuje wybór zawartości:](#get-food-items-as-rankable-actions)

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Poproś o najlepszą akcję

Aby ukończyć żądanie rangi, program prosi preferencje `currentContent` użytkownika o utworzenie opcji zawartości. Proces może tworzyć zawartość, aby wykluczyć `excludeActions`z akcji, pokazane jako . Żądanie rangi wymaga akcji i ich funkcji, currentContext funkcje, excludeActions i unikatowy identyfikator zdarzenia, aby otrzymać odpowiedź.

Ten szybki start ma proste funkcje kontekstowe pory dnia i preferencji żywieniowych użytkowników. W systemach produkcyjnych określanie i [ocena](../concept-feature-evaluation.md) [działań i funkcji](../concepts-features.md) może być kwestią nietrywialną.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Wyślij nagrodę

Aby uzyskać wynik nagrody do wysłania w żądaniu nagrody, program pobiera wybór użytkownika z wiersza polecenia, przypisuje wartość liczbową do zaznaczenia, a następnie wysyła unikatowy identyfikator zdarzenia i wynik nagrody jako wartość liczbową do interfejsu API nagrody.

Ten szybki start przypisuje prostą liczbę jako wynik nagrody, zero lub 1. W systemach produkcyjnych określenie, kiedy i co wysłać na [zaproszenie](../concept-rewards.md) do nagrody, może być nietrywialne, w zależności od konkretnych potrzeb.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom aplikację za pomocą `run` polecenia dotnet z katalogu aplikacji.

```console
dotnet run
```

![Program szybki start zadaje kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, a następnie zapewnia najwyższą akcję.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Kod źródłowy tego przewodnika Szybki start](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) jest dostępny w przykładowym repozytorium Usługi GitHub.

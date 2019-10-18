---
title: 'Szybki Start: Biblioteka klienta personalizacji dla platformy .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z biblioteką klienta narzędzia Personalizacja dla platformy .NET za pomocą pętli szkoleniowej.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: 4308ed6d00bd3900986f08a93a686f0d7d00bcfb
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515599"
---
# <a name="quickstart-personalizer-client-library-for-net"></a>Szybki Start: Biblioteka klienta personalizacji dla platformy .NET

Wyświetl spersonalizowaną zawartość w C# tym przewodniku szybki start za pomocą usługi personalizacji.

Wprowadzenie do biblioteki klienta programu Personalizacja dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

 * Ustalanie rangi listy akcji do personalizacji.
 * Ocenę nagrody raportu wskazującej na powodzenie najwyższej funkcjonalnej akcji.

[Dokumentacja referencyjna](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview)  |   |  pakietu[kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) [ | ](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Korzystanie z tego przewodnika Szybki Start

Aby skorzystać z tego przewodnika Szybki Start, należy wykonać kilka czynności:

* W Azure Portal Utwórz zasób personalizacji
* W Azure Portal dla zasobu Personalizacja na stronie **Ustawienia** Zmień częstotliwość aktualizacji modelu
* W edytorze kodu Utwórz plik kodu i edytuj plik kodu
* W wierszu polecenia lub terminalu Zainstaluj zestaw SDK z wiersza polecenia
* W wierszu polecenia lub terminalu uruchom plik kodu

## <a name="create-a-personalizer-azure-resource"></a>Tworzenie zasobu platformy Azure dla programu personalizacji

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla narzędzia Personalizacja przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services) ważny przez 7 dni bezpłatnie. Po zarejestrowaniu program będzie dostępny w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/).

<!-- rename TBD_KEY to something meaningful for your service, like TEXT_ANALYTICS_KEY -->
Po otrzymaniu klucza z subskrypcji próbnej lub zasobu Utwórz dwie [zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY` klucza zasobu.
* `PERSONALIZER_RESOURCE_ENDPOINT` dla punktu końcowego zasobu.

W Azure Portal wartości klucza i punktu końcowego są dostępne na stronie **Szybki Start** .

## <a name="change-the-model-update-frequency"></a>Zmień częstotliwość aktualizacji modelu

W Azure Portal na stronie **Ustawienia** w zasobów personalizacji Zmień **częstotliwość aktualizacji modelu** na 10 sekund. Dzięki temu będzie można szybko przeszkolić usługę, co pozwoli zobaczyć, jak Górna akcja zmienia się dla każdej iteracji.

![Zmień częstotliwość aktualizacji modelu](./media/settings/configure-model-update-frequency-settings.png)

W przypadku pierwszego wystąpienia pętli programu personalizacji nie istnieje model, ponieważ nie ma żadnych wywołań interfejsu API do uczenia się. Wywołania rangi będą zwracać równe prawdopodobieństwa dla każdego elementu. Aplikacja powinna nadal zawsze klasyfikować zawartość przy użyciu danych wyjściowych RewardActionId.

## <a name="create-a-new-c-application"></a>Utwórz nową C# aplikację

Utwórz nową aplikację platformy .NET Core w preferowanym edytorze lub środowisku IDE. 

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj polecenia dotnet `new`, aby utworzyć nową aplikację konsolową o nazwie `personalizer-quickstart`. To polecenie tworzy prosty projekt "Hello world" C# z pojedynczym plikiem źródłowym: `Program.cs`. 

```console
dotnet new console -n personalizer-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

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

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

W katalogu aplikacji zainstaluj bibliotekę klienta programu Personalizacja dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektów

Klient narzędzia personalizacji jest obiektem [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) , który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz.

Aby poprosił o rangę zawartości, Utwórz element [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview), a następnie Przekaż go do [klienta. Ranga](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) metody. Metoda rangi zwraca RankResponse, zawierający sklasyfikowaną zawartość. 

Aby wysłać wynagrodzenie do programu Personalizacja, Utwórz element [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview), a następnie Przekaż go do [klienta. Metoda nagradzania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) . 

Ustalenie nagrody w tym przewodniku Szybki Start jest proste. W systemie produkcyjnym określenie, co ma wpływ na [wynik nagrody](concept-rewards.md) i według ile może być złożonym procesem, można zmienić z upływem czasu. Powinna to być jedna z podstawowych decyzji projektowych w architekturze personalizacji. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta programu Personalizacja dla platformy .NET:

* [Tworzenie klienta programu Personalizacja](#create-a-personalizer-client)
* [Żądaj rangi](#request-a-rank)
* [Wyślij wynagrodzenie](#send-a-reward)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu Otwórz plik **program.cs** w preferowanym edytorze lub w środowisku IDE. Zastąp istniejący kod `using` następującymi dyrektywami `using`:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Dodawanie informacji o zasobach personalizacji

W klasie **program** Utwórz zmienne dla klucza i punktu końcowego usługi Azure Resource pobrane ze zmiennych środowiskowych o nazwie `PERSONALIZER_RESOURCE_KEY` i `PERSONALIZER_RESOURCE_ENDPOINT`. Jeśli po uruchomieniu aplikacji zostały utworzone zmienne środowiskowe, Edytor, środowisko IDE lub powłoka, na których działa, będzie musiał zostać zamknięte i ponownie załadowane w celu uzyskania dostępu do zmiennej. Metody zostaną utworzone w dalszej części tego przewodnika Szybki Start.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Tworzenie klienta programu Personalizacja

Następnie Utwórz metodę zwracającą klienta programu Personalizacja. Parametr do metody jest `PERSONALIZER_RESOURCE_ENDPOINT`, a ApiKey jest `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-content-choices-represented-as-actions"></a>Pobierz opcje zawartości reprezentowane jako akcje

Akcje reprezentują opcje zawartości, które chcesz spersonalizować. Dodaj następujące metody do klasy program, aby uzyskać dane wejściowe użytkownika z wiersza polecenia dla pory dnia i bieżącego preferencji żywności.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Obie metody wykorzystują metodę `GetKey`, aby odczytać wybór użytkownika z wiersza polecenia. 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Tworzenie pętli uczenia

Pętla szkoleniowa personalizacji jest cyklem wywołań rangi i nagrody. W tym przewodniku szybki start każdy wywołania rangi, aby spersonalizować zawartość, nastąpi wywołanie zarobkowe, aby poinformować program Personalizuj, jak dobrze zaklasyfikował zawartość usługi. 

Poniższy kod w metodzie `main` programu pętle przez cykl monitowania użytkownika o ich preferencje w wierszu polecenia, wysyłając te informacje do narzędzia Personalizacja, aby określić rangę i przedstawiony wybór do klienta w celu wybrania spośród listy , a następnie wysyłając do personalizacji wynagrodzenie sygnalizujące, jak dobrze ta usługa przeszedł w wyborze.

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Initialize Personalizer client.
    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        // <rank>
        // Get context information from the user.
        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        // Create current context from user specified data.
        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        // Exclude an action for personalizer ranking. This action will be held at its current position.
        // This simulates a business rule to force the action "juice" to be ignored in the ranking.
        // As juice is excluded, the return of the API will always be with a probability of 0.
        IList<string> excludeActions = new List<string> { "juice" };

        // Generate an ID to associate with the request.
        string eventId = Guid.NewGuid().ToString();

        // Rank the actions
        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);
        // </rank>

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        // <reward>
        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        // Send the reward for the action based on user response.
        client.Reward(response.EventId, new RewardRequest(reward));
        // </reward>

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

Dodaj następujące metody, które [pobierają Opcje zawartości](#get-content-choices-represented-as-actions), przed uruchomieniem pliku kodu:

* GetUsersTimeOfDay
* GetUsersTastePreference
* GetKey

## <a name="request-a-rank"></a>Żądaj rangi

Aby ukończyć żądanie rangi, program prosi o preferencje użytkownika w celu utworzenia `currentContent` opcji zawartości. Proces może utworzyć zawartość, która ma zostać wykluczona z rangi, pokazana jako `excludeActions`. Żądanie rangi wymaga akcji, currentContext, excludeActions i unikatowego identyfikatora zdarzenia rangi (jako identyfikatora GUID), aby otrzymać żądaną odpowiedź. 

Ten przewodnik Szybki Start zawiera proste funkcje kontekstu o porze dnia i preferencjach żywności dla użytkowników. W systemach produkcyjnych określenie i [Ocena](concept-feature-evaluation.md) [działań i funkcji](concepts-features.md) może być nieuproszczona.  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Wyślij wynagrodzenie

Aby zakończyć żądanie pozyskania, program pobiera wybór użytkownika z wiersza polecenia, przypisuje wartość liczbową do każdego zaznaczenia, a następnie wysyła unikatowy identyfikator zdarzenia rangi i wartość liczbową do metody nagrody.

Ten przewodnik Szybki Start przypisuje prostą liczbę jako wynagrodzenie, zero lub 1. W systemach produkcyjnych określenie, kiedy i co mają być wysyłane do [płatnego wywołania,](concept-rewards.md) może być nieuproszczone, w zależności od konkretnych potrzeb. 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom aplikację z poleceniem dotnet `run` z katalogu aplikacji.

```console
dotnet run
```

![Program szybkiego startu prosi o kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, a następnie zapewnia najwyższą akcję.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Kod źródłowy dla tego przewodnika Szybki Start](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) jest dostępny w repozytorium.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Jak działa Personalizacja](how-personalizer-works.md)

* [Co to jest Personalizacja?](what-is-personalizer.md)
* [Gdzie można używać personalizacji?](where-can-you-use-personalizer.md)
* [Rozwiązywanie problemów](troubleshooting.md)


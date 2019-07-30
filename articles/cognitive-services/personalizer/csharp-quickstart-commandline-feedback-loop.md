---
title: 'Szybki start: Tworzenie pętli opinii — Personalizacja'
titleSuffix: Azure Cognitive Services
description: Spersonalizuj zawartość w tym C# przewodniku szybki start za pomocą usługi personalizacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: diberry
ms.openlocfilehash: 54aa23071fef09058a1702218d6b7fc920363518
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662797"
---
# <a name="quickstart-personalize-content-using-c"></a>Szybki start: Personalizowanie zawartości przy użyciuC# 

Wyświetl spersonalizowaną zawartość w C# tym przewodniku szybki start za pomocą usługi personalizacji.

W tym przykładzie pokazano, jak za pomocą biblioteki C# klienta programu personalizacji wykonać następujące czynności: 

 * Ustalanie rangi listy akcji do personalizacji.
 * Zgłoś wynagrodzenie w celu przydzielenia do najwyższej funkcjonalnej akcji na podstawie wyboru użytkownika dla określonego zdarzenia.

Wprowadzenie do personalizacji obejmuje następujące kroki:

1. Odwoływanie się do zestawu SDK 
1. Pisanie kodu w celu określenia działań, które mają być wyświetlane użytkownikom.
1. Pisanie kodu w celu wysłania nagrody do uczenia pętli.

## <a name="prerequisites"></a>Wymagania wstępne

* Potrzebujesz [usługi personalizacji](how-to-settings.md) , aby uzyskać klucz subskrypcji i adres URL usługi punktu końcowego. 
* [Program Visual Studio 2015 lub 2017](https://visualstudio.microsoft.com/downloads/).
* Pakiet NuGet zestawu SDK [Microsoft. Azure. CognitiveServices. personalizowania](https://go.microsoft.com/fwlink/?linkid=2092272) . Instrukcje instalacji znajdują się poniżej.

## <a name="change-the-model-update-frequency"></a>Zmień częstotliwość aktualizacji modelu

W zasobie Personalizuj w Azure Portal Zmień **częstotliwość aktualizacji modelu** na 10 sekund. Dzięki temu będzie można szybko przeszkolić usługę, co pozwoli zobaczyć, jak Górna akcja zmienia się dla każdej iteracji.

W przypadku pierwszego wystąpienia pętli programu personalizacji nie istnieje model, ponieważ nie ma żadnych wywołań interfejsu API do uczenia się. Wywołania rangi będą zwracać równe prawdopodobieństwa dla każdego elementu. Aplikacja powinna nadal zawsze klasyfikować zawartość przy użyciu danych wyjściowych RewardActionId.

![Zmień częstotliwość aktualizacji modelu](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Tworzenie nowej aplikacji konsolowej i odwoływanie się do zestawu SDK programu personalizacji 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Utwórz nową aplikację konsolową języka Visual C# w programie Visual Studio.
1. Zainstaluj pakiet NuGet biblioteki klienta narzędzia Personalizacja. W menu wybierz kolejno opcje **Narzędzia**i **Menedżer pakietów NuGet**, a następnie **Zarządzaj pakietami NuGet dla rozwiązania**.
1. Zaznacz pole wyboru **Uwzględnij wersję wstępną**.
1. Wybierz kartę **Przeglądaj** , a następnie w polu **wyszukiwania** wpisz `Microsoft.Azure.CognitiveServices.Personalizer`.
1. Podczas wyświetlania wybierz pozycję **Microsoft. Azure. CognitiveServices. Personalizowanie** .
1. Zaznacz pole wyboru obok nazwy projektu, a następnie wybierz pozycję **Zainstaluj**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Dodaj kod i umieść go w usłudze Personalizacja i w usłudze Azure Keys

1. Zastąp plik Program.cs poniższym kodem. 
1. Zastąp `serviceKey` wartość prawidłowym kluczem subskrypcji personalizacji.
1. Zamień `serviceEndpoint` na punkt końcowy usługi. Może to być na przykład `https://westus2.api.cognitive.microsoft.com/`.
1. Uruchom program.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Dodaj kod, aby klasyfikować akcje, które mają być wyświetlane użytkownikom

Poniższy C# kod stanowi kompletną listę, która umożliwia przekazywanie informacji o użytkowniku, _features oraz informacji o zawartości, _działaniach_i personalizacji przy użyciu zestawu SDK. Personalizacja Zwraca górną rangę akcji, aby wyświetlić użytkownika.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;

namespace PersonalizerExample
{
    class Program
    {
        // The key specific to your personalizer service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string ApiKey = "";

        // The endpoint specific to your personalizer service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string ServiceEndpoint = "";

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

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalizer ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

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

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalizer client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalizer client instance</returns>
        static PersonalizerClient InitializePersonalizerClient(string url)
        {
            PersonalizerClient client = new PersonalizerClient(
                new ApiKeyServiceClientCredentials(ApiKey)) {Endpoint = url};

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
        static string GetUsersTimeOfDay()
        {
            string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

            Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
            if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
                timeIndex = 1;
            }

            return timeOfDayFeatures[timeIndex - 1];
        }

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
        static string GetUsersTastePreference()
        {
            string[] tasteFeatures = new string[] { "salty", "sweet" };

            Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
            if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
                tasteIndex = 1;
            }

            return tasteFeatures[tasteIndex - 1];
        }

        /// <summary>
        /// Creates personalizer actions feature list.
        /// </summary>
        /// <returns>List of actions for personalizer.</returns>
        static IList<RankableAction> GetActions()
        {
            IList<RankableAction> actions = new List<RankableAction>
            {
                new RankableAction
                {
                    Id = "pasta",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
                },

                new RankableAction
                {
                    Id = "ice cream",
                    Features =
                    new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
                },

                new RankableAction
                {
                    Id = "juice",
                    Features =
                    new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
                },

                new RankableAction
                {
                    Id = "salad",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
                }
            };

            return actions;
        }

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>Uruchamianie programu

Skompiluj i uruchom program. Program szybkiego startu prosi o kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, a następnie zapewnia najwyższą akcję.

![Program szybkiego startu prosi o kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, a następnie zapewnia najwyższą akcję.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po ukończeniu przewodnika Szybki start usuń wszystkie pliki utworzone w tym przewodniku Szybki start. 

## <a name="next-steps"></a>Następne kroki

[Jak działa Personalizacja](how-personalizer-works.md)



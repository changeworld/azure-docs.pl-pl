---
title: Sprzężenia zwrotnego - Personalizer
titleSuffix: Azure Cognitive Services
description: Dostosowanie zawartości w tym C# Szybki Start z usługą Personalizer.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/08/2019
ms.author: edjez
ms.openlocfilehash: b0dc8fbbb80a4d03b2cb64d09ffe9a36883c5bf9
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521370"
---
# <a name="quickstart-personalize-content-using-c"></a>Szybki start: Personalizowanie zawartości przy użyciuC# 

Wyświetlanie personalizowania zawartości w tym C# Szybki Start z usługą Personalizer.

W tym przykładzie pokazano, jak używać biblioteki klienckiej Personalizer dla C# do wykonywania następujących czynności: 

 * Rank listy akcji na potrzeby personalizacji.
 * Należy sporządzić raport za wynagrodzeniem można przydzielić do góry randze spośród wszystkich akcji w oparciu o wybór użytkownika dla określonego zdarzenia.

Wprowadzenie do Personalizer obejmuje następujące czynności:

1. Odwołanie do zestawu SDK 
1. Pisanie kodu pod rank akcje, które powinny być widoczne dla użytkowników,
1. Pisanie kodu w celu wysyłania rewards to w opracowywaniu pętli.

## <a name="prerequisites"></a>Wymagania wstępne

* Potrzebujesz [usługi Personalizer](how-to-settings.md) można pobrać subskrypcji klucz i punkt końcowy adres url Twojej usługi. 
* [Visual Studio 2015 lub 2017](https://visualstudio.microsoft.com/downloads/).
* [Microsoft.Azure.CognitiveServices.Personalizer](https://go.microsoft.com/fwlink/?linkid=2092272) pakietu NuGet zestawu SDK. Instrukcje instalacji znajdują się poniżej.

## <a name="change-the-model-update-frequency"></a>Zmiana częstotliwości aktualizacji modelu

W zasobie Personalizer w witrynie Azure portal, należy zmienić **częstotliwości aktualizacji modelu** to 10 sekund. To spowoduje nauczenia usługi szybko, dzięki czemu możesz widzieć, jak zmienia się akcja najważniejsze dla każdej iteracji

![Zmiana częstotliwości aktualizacji modelu](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Tworzenie nowej aplikacji konsoli i odwołanie do zestawu SDK Personalizer 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Utwórz nową aplikację konsolową języka Visual C# w programie Visual Studio.
1. Zainstaluj pakiet NuGet biblioteki klienta Personalizer. W menu, wybierz **narzędzia**, wybierz opcję **pakietu Nuget Manager**, następnie **Zarządzaj pakietami NuGet dla rozwiązania**.
1. Wybierz **Przeglądaj** kartę, a następnie w **wyszukiwania** wpisz `Microsoft.Azure.CognitiveServices.Personalizer`.
1. Wybierz **Microsoft.Azure.CognitiveServices.Personalizer** kiedy zostanie wyświetlony.
1. Zaznacz pole wyboru obok nazwy swojego projektu, a następnie wybierz pozycję **zainstalować**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Dodaj kod i umieścić w klucze Personalizer i na platformie Azure

1. Zastąp plik Program.cs poniższym kodem. 
1. Zastąp `serviceKey` wartością prawidłowy klucz subskrypcji Personalizer.
1. Zastąp `serviceEndpoint` przy użyciu punktu końcowego usługi. Może to być na przykład `https://westus2.api.cognitive.microsoft.com/`.
1. Uruchom program.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Dodaj kod, aby rank akcje, które mają być wyświetlane użytkownikom

Następujące C# kod jest uzyskać pełną listę, aby przekazać informacje o użytkowniku, _features i informacje o zawartości, _akcje_, aby Personalizer przy użyciu zestawu SDK. Personalizer zwraca górnej pozycjonowane akcję, aby wyświetlić użytkowników.  

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

Skompiluj i uruchom program. Przewodnik Szybki Start program zadaje kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, następnie zawiera najważniejsze akcji.

![Przewodnik Szybki Start program zadaje kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, następnie zawiera najważniejsze akcji.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po ukończeniu przewodnika Szybki start usuń wszystkie pliki utworzone w tym przewodniku Szybki start. 

## <a name="next-steps"></a>Kolejne kroki

[Jak działa Personalizer](how-personalizer-works.md)



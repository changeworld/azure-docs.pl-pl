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
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: c566d1fd4b151efc0d28b7059504e60a1451c034
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027145"
---
# <a name="quickstart-personalize-content-using-c"></a>Szybki start: Personalizowanie zawartości przy użyciuC# 

Wyświetlanie personalizowania zawartości w tym C# Szybki Start z usługą Personalizer.

W tym przykładzie pokazano, jak używać biblioteki klienckiej personalizacji dla C# do wykonywania następujących czynności: 

 * Rank listy akcji na potrzeby personalizacji.
 * Należy sporządzić raport za wynagrodzeniem można przydzielić do góry randze spośród wszystkich akcji w oparciu o wybór użytkownika dla określonego zdarzenia.

Wprowadzenie do personalizacji obejmuje następujące czynności:

1. Odwołanie do zestawu SDK 
1. Pisanie kodu pod rank akcje, które powinny być widoczne dla użytkowników,
1. Pisanie kodu w celu wysyłania rewards to w opracowywaniu pętli.

## <a name="prerequisites"></a>Wymagania wstępne

* Wymagany klucz subskrypcji oraz adres url usługi wystawianie tokenu.
* [Visual Studio 2015 lub 2017](https://visualstudio.microsoft.com/downloads/).
* Pakiet NuGet zestawu SDK Microsoft.Azure.CognitiveServices.Personalization. Instrukcje instalacji znajdują się poniżej.

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Tworzenie nowej aplikacji konsoli i odwołanie do zestawu SDK Personalizer 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Utwórz nową aplikację konsolową języka Visual C# w programie Visual Studio.
1. Zainstaluj pakiet NuGet biblioteki klienta personalizacji. W menu, wybierz **narzędzia**, wybierz opcję **pakietu Nuget Manager**, następnie **Zarządzaj pakietami NuGet dla rozwiązania**.
1. Wybierz **Przeglądaj** kartę, a następnie w **wyszukiwania** wpisz `Microsoft.Azure.CognitiveServices.Personalization`.
1. Wybierz **Microsoft.Azure.CognitiveServices.Personalization** kiedy zostanie wyświetlony.
1. Zaznacz pole wyboru obok nazwy swojego projektu, a następnie wybierz pozycję **zainstalować**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Dodaj kod i umieścić w klucze Personalizer i na platformie Azure

1. Zastąp plik Program.cs poniższym kodem. 
1. Zastąp `serviceKey` wartością prawidłowy klucz subskrypcji Personalizer.
1. Zastąp `serviceEndpoint` przy użyciu punktu końcowego usługi. Może to być na przykład `https://westus2.api.cognitive.microsoft.com/`.
1. Uruchom program.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Dodaj kod, aby rank akcje, które mają być wyświetlane użytkownikom

Następujące C# kod jest uzyskać pełną listę, aby przekazać informacje o użytkowniku, _features i informacje o zawartości, _akcje_, aby Personalizer przy użyciu zestawu SDK. Personalizer zwraca górnej pozycjonowane akcję, aby wyświetlić użytkowników.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalization;
using Microsoft.Azure.CognitiveServices.Personalization.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PersonalizationExample
{
    class Program
    {
        // The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string serviceKey = "";

        // The endpoint specific to your personalization service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string serviceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            Uri url = new Uri(serviceEndpoint);

            // Get the actions list to choose from personalization with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalization client.
            PersonalizationClient client = InitializePersonalizationClient(url);

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

                // Exclude an action for personalization ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalization service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

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

                Console.WriteLine("\nPersonalization service ranked the actions with the probabilities as below:");
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
        /// Initializes the personalization client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalization client instance</returns>
        static PersonalizationClient InitializePersonalizationClient(Uri url)
        {
            PersonalizationClient client = new PersonalizationClient(url,
            new ApiKeyServiceClientCredentials(serviceKey),
            new DelegatingHandler[] { });

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
        /// Creates personalization actions feature list.
        /// </summary>
        /// <returns>List of actions for personalization.</returns>
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



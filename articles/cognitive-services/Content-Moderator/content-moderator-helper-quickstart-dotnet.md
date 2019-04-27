---
title: 'Szybki start: Tworzenie klienta moderacji dla platformy .NET — Content Moderator'
titlesuffix: Azure Cognitive Services
description: Sposób zwracania klienta usług Content Moderator przy użyciu zestawu SDK Azure Content Moderator dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 94a16d03e47a9bec29e5e1c4326beab376dd33dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699107"
---
# <a name="quickstart-helper-code-to-return-a-content-moderator-client"></a>Szybki start: kod pomocnika zwracający klienta usług Content Moderator

Ten artykuł zawiera informacje i przykłady kodu, które pomogą Ci rozpocząć korzystanie z zestawu SDK Content Moderator dla platformy .NET w celu utworzenia klienta usług Content Moderator dla subskrypcji.

Biblioteka jest używana przez inne przewodniki Szybki start w tej sekcji.

W tym artykule założono, że znasz już program Visual Studio i język C#.

> [!IMPORTANT]
> Ta biblioteka klas zawiera kod przeznaczony wyłącznie w celach demonstracyjnych.
> W przypadku adaptacji tego kodu do użycia w środowisku produkcyjnym użyj bezpiecznej metody przechowywania i używania klucza subskrypcji Content Moderator.

## <a name="sign-up-for-content-moderator-services"></a>Zarejestruj się w usługach Content Moderator

Zanim użyjesz usług Content Moderator za pomocą interfejsu API REST lub zestawu SDK, potrzebujesz klucza subskrypcji.
Zapoznaj się z przewodnikiem Szybki start [Wypróbowywanie usług Content Moderator w Internecie](quick-start.md), aby dowiedzieć się, jak można uzyskać klucz.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Utwórz nowy projekt **Biblioteka klas (.NET Framework)**.

   W przykładowym kodzie projekt został nazwany **ModeratorHelper**.

1. Dodaj odwołanie do zestawu struktury **System.Configuration** Framework.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Tworzenie klienta usługi Content Moderator

Zastąp zawartość pliku ModeratorHelper.cs następującym kodem:

    using Microsoft.CognitiveServices.ContentModerator;

    namespace ModeratorHelper
    {
    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "myRegion";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "myKey";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }
    }


> [!IMPORTANT]
> Zaktualizuj pola **AzureRegion** i **CMSubscriptionKey** wartościami identyfikatora regionu i klucza subskrypcji.

Oto szybki sposób utworzenia klienta usług Content Moderator dla subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

[Pobierz rozwiązanie programu Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego i innych przewodników Szybki start usług Content Moderator dla platformy .NET i rozpocznij pracę nad integracją.

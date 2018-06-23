---
title: Azure zawartości SDK moderatora dla metody pomocnika .NET | Dokumentacja firmy Microsoft
description: Sposób zwracania klienta moderatora zawartości przy użyciu zestawu SDK moderatora zawartości platformy Azure dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 36f2124708731f78f34849d8210ed39ea8f59140
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346929"
---
# <a name="helper-code-to-return-a-content-moderator-client"></a>Kod pomocniczy do zwrócenia klientowi moderatora zawartości

Ten artykuł zawiera informacje i przykłady ułatwiające rozpoczęcie pracy z zawartości moderatora zestawu SDK dla platformy .NET można utworzyć klienta moderatora zawartości dla Twojej subskrypcji.

Biblioteka jest używany przez inne elementy szybkiego startu w tej sekcji.

W tym artykule przyjęto założenie, że znasz już program Visual Studio i C#.

> [!IMPORTANT]
> Ta biblioteka klas zawiera kod przeznaczony wyłącznie w celach demonstracyjnych.
> Jeśli dostosować ten kod do użycia w środowisku produkcyjnym, należy użyć metody bezpiecznego przechowywania i przy użyciu klucza zawartości moderatora subskrypcji.

## <a name="sign-up-for-content-moderator-services"></a>Załóż moderatora zawartości usług

Zanim użyjesz usługi moderatora zawartości za pośrednictwem interfejsu API REST lub zestawu SDK, należy klucza subskrypcji.
Zapoznaj się [szybkiego startu](quick-start.md) Aby dowiedzieć się, jak można uzyskać klucz.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Utwórz nową **biblioteki klas (.NET Framework)** projektu.

   W przykładowym kodzie I o nazwie projektu **ModeratorHelper**.

1. Dodaj odwołanie do **System.Configuration** zestawu struktury.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Tworzenie klienta moderatora zawartości

Zastąp zawartość pliku ModeratorHelper.cs następujący kod:

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
> Aktualizacja **AzureRegion** i **CMSubscriptionKey** pola z wartościami region klucza identyfikator i subskrypcji.

Masz teraz szybko utworzyć klienta moderatora zawartości dla Twojej subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

[Pobierz rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) to i inne elementy zawartości moderatora szybkiego startu dla platformy .NET i rozpocząć pracę na integracją.

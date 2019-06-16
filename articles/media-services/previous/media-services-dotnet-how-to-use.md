---
title: Jak skonfigurować komputer dla Programowanie przy użyciu platformy .NET usługi Media Services
description: Dowiedz się więcej o wymaganiach wstępnych usługi Media Services za pomocą zestawu Media Services SDK dla platformy .NET. Poznasz również sposób tworzenia aplikacji programu Visual Studio.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 51fffbd170daecfec6fcea95caa0526e6d881407
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724119"
---
# <a name="media-services-development-with-net"></a>Projektowanie usługi Media Services przy użyciu platformy .NET 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

W tym artykule omówiono sposób rozpoczęcia tworzenia aplikacji usługi Media Services przy użyciu platformy .NET.

**Usługi Azure Media Services .NET SDK** biblioteki pozwala programować przy użyciu usługi Media Services przy użyciu platformy .NET. Aby jeszcze bardziej ułatwiał tworzyć aplikacje za pomocą platformy .NET, **rozszerzenia SDK .NET usługi Azure Media Services** podano biblioteki. Ta biblioteka zawiera zestaw metod rozszerzeń i funkcji pomocnika, które upraszczają kod .NET. Zarówno biblioteki są dostępne za pośrednictwem **NuGet** i **GitHub**.

## <a name="prerequisites"></a>Wymagania wstępne
* Konto usługi Media Services w nowej lub istniejącej subskrypcji platformy Azure. Zapoznaj się z artykułem [sposób tworzenia konta usługi Media Services](media-services-portal-create-account.md).
* Operating Systems (Systemy operacyjne): System Windows 10, Windows 7, Windows 2008 R2 lub Windows 8.
* .NET framework 4.5 lub nowszej.
* Program Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio
W tej sekcji pokazano, jak utworzyć projekt w programie Visual Studio i skonfigurować go do tworzenia usługi Media Services.  W tym przypadku projekt jest C# Windows aplikacji konsoli, ale te same kroki instalacji, pokazano poniżej dotyczą innych rodzajów projektów można utworzyć aplikacji usługi Media Services (na przykład aplikacji Windows Forms lub aplikacji internetowej ASP.NET).

W tej sekcji pokazano, jak używać **NuGet** można dodać rozszerzenia zestawu SDK .NET usługi Media Services i inne zależne biblioteki.

Alternatywnie, można uzyskać najnowsze elementy Media Services .NET SDK z usługi GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) lub [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), a następnie skompilować rozwiązanie i dodaj odwołania do projektu klienta. Wszystkie niezbędne zależności Pobierz pobrane i wyodrębnione automatycznie.

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio. Wprowadź **nazwa**, **lokalizacji**, i **Nazwa rozwiązania**, a następnie kliknij przycisk OK.
2. Skompiluj rozwiązanie.
3. Użyj **NuGet** na zainstalowanie i dodanie **rozszerzenia SDK .NET usługi Azure Media Services** (**windowsazure.mediaservices.extensions**). Podczas instalacji tego pakietu instalowany jest również zestaw **.NET SDK usługi Media Services** oraz dodawane są wszystkie inne wymagane zależności.
   
    Upewnij się, że masz najnowszą wersję zainstalowanego Menedżera NuGet. Aby uzyskać więcej informacji i instalacji instrukcji, zobacz [NuGet](https://nuget.codeplex.com/).

    1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz **Zarządzaj pakietami NuGet**.

    2. Zostanie wyświetlone okno dialogowe Zarządzanie pakietami NuGet.

    3. W galerii w trybie Online, wyszukiwanie rozszerzeń MediaServices platformy Azure, wybierz **rozszerzenia SDK .NET usługi Azure Media Services** (**windowsazure.mediaservices.extensions**), a następnie kliknij przycisk  **Zainstaluj** przycisku.
   
    4. Projekt jest zmodyfikowany i odwołania do rozszerzenia SDK .NET usługi Media Services, Media Services .NET SDK i inne zależne zestawy są dodawane.
4. Aby wspierać bardziej przejrzyste środowisko programistyczne, należy rozważyć włączenie, przywracanie pakietów NuGet. Aby uzyskać więcej informacji, zobacz [Przywracanie pakietów NuGet "](https://docs.nuget.org/consume/package-restore).
5. Dodaj odwołanie do **System.Configuration** zestawu. Ten zestaw zawiera System.Configuration. **ConfigurationManager** klasę, która umożliwia dostęp do plików konfiguracji (np. App.config).
   
    1. Aby dodać odwołania za pomocą okna dialogowego Zarządzanie odwołania, kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań. Następnie kliknij przycisk **Dodaj**, następnie kliknij przycisk **odwołanie...** .
   
    2. Pojawi się okno dialogowe Zarządzanie odwołania.
    3. W obszarze zestawy .NET framework, Znajdź i zaznacz zestawu System.Configuration, a następnie naciśnij klawisz **OK**.
6. Otwórz plik App.config i Dodaj **appSettings** sekcji w pliku. Ustaw wartości, które są wymagane do połączenia interfejsu API usług Media Services. Aby uzyskać więcej informacji, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Ustaw wartości, które są wymagane do połączenia za pomocą **nazwy głównej usługi** metodę uwierzytelniania.

        ```csharp
                <configuration>
                ...
                    <appSettings>
                        <add key="AMSAADTenantDomain" value="tenant"/>
                        <add key="AMSRESTAPIEndpoint" value="endpoint"/>
                        <add key="AMSClientId" value="id"/>
                        <add key="AMSClientSecret" value="secret"/>
                    </appSettings>
                </configuration>
        ```

7. Dodaj **System.Configuration** odwołanie do projektu.
8. Zastąp istniejące **przy użyciu** instrukcji na początku pliku Program.cs następującym kodem:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    W tym momencie możesz przystąpić do rozpoczęcia tworzenia aplikacji usługi Media Services.    

## <a name="example"></a>Przykład

Poniżej przedstawiono krótki przykład, który nawiązuje połączenie z interfejsem API usługi AMS i wymieniono wszystkie dostępne procesory multimediów.

```csharp
        class Program
        {
            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];
        
            private static CloudMediaContext _context = null;
            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
                // List all available Media Processors
                foreach (var mp in _context.MediaProcessors)
                {
                    Console.WriteLine(mp.Name);
                }
        
            }
 ```

## <a name="next-steps"></a>Kolejne kroki

Teraz [można połączyć z interfejsem API usługi AMS](media-services-use-aad-auth-to-access-ams-api.md) i Rozpocznij [tworzenia](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


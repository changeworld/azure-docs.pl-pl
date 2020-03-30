---
title: Jak skonfigurować komputer do tworzenia usług multimedialnych za pomocą platformy .NET
description: Dowiedz się więcej o wymaganiach wstępnych dla usługi Media Services przy użyciu sdk usług multimedialnych dla platformy .NET. Dowiedz się również, jak utworzyć aplikację programu Visual Studio.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64724119"
---
# <a name="media-services-development-with-net"></a>Program rozwoju usługi Media Services z usługą .NET 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

W tym artykule omówiono sposób uruchamiania tworzenia aplikacji usługi Media Services przy użyciu platformy .NET.

Biblioteka **zestawów SDK usługi Azure Media Services .NET** umożliwia program przeciwko usługie Media Services przy użyciu platformy .NET. Aby jeszcze bardziej ułatwić tworzenie za pomocą platformy .NET, dostępna jest biblioteka **rozszerzeń SDK usługi Azure Media Services .NET.** Ta biblioteka zawiera zestaw metod rozszerzenia i funkcje pomocnicze, które upraszczają kod .NET. Obie biblioteki są dostępne za pośrednictwem **NuGet** i **GitHub.**

## <a name="prerequisites"></a>Wymagania wstępne
* Konto usługi Media Services w nowej lub istniejącej subskrypcji platformy Azure. Zobacz artykuł [Jak utworzyć konto usługi Media Services](media-services-portal-create-account.md).
* Systemy operacyjne: Windows 10, Windows 7, Windows 2008 R2 lub Windows 8.
* .NET Framework 4.5 lub nowszych.
* Program Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio
W tej sekcji pokazano, jak utworzyć projekt w programie Visual Studio i skonfigurować go do programowania usługi Media Services.  W takim przypadku projekt jest aplikacją konsoli systemu Windows w języku C#, ale te same kroki konfiguracji przedstawione w tym miejscu dotyczą innych typów projektów, które można utworzyć dla aplikacji usługi Media Services (na przykład aplikacji Windows Forms lub ASP.NET aplikacji sieci Web).

W tej sekcji pokazano, jak używać **nuget** do dodawania rozszerzeń SDK usługi Media Services .NET i innych bibliotek zależnych.

Alternatywnie można uzyskać najnowsze bity SDK usługi Media Services .NET z usługi GitHub[(github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) lub [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), utworzyć rozwiązanie i dodać odwołania do projektu klienta. Wszystkie niezbędne zależności są pobierane i wyodrębniane automatycznie.

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio. Wprowadź **nazwę,** **lokalizację**i **nazwę rozwiązania**, a następnie kliknij przycisk OK.
2. Skompiluj rozwiązanie.
3. Użyj **nuGet,** aby zainstalować i dodać **rozszerzenia SDK usługi Azure Media Services .NET** **(windowsazure.mediaservices.extensions).** Podczas instalacji tego pakietu instalowany jest również zestaw **.NET SDK usługi Media Services** oraz dodawane są wszystkie inne wymagane zależności.
   
    Upewnij się, że masz zainstalowaną najnowszą wersję programu NuGet. Aby uzyskać więcej informacji i instrukcje instalacji, zobacz [NuGet](https://nuget.codeplex.com/).

    1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy nazwę projektu i wybierz polecenie **Zarządzaj pakietami NuGet**.

    2. Zostanie wyświetlone okno dialogowe Zarządzanie pakietami NuGet.

    3. W galerii Online wyszukaj rozszerzenia usługi Azure MediaServices, wybierz pozycję **Rozszerzenia SDK usługi Azure Media Services** **(windowsazure.mediaservices.extensions),** a następnie kliknij przycisk **Zainstaluj.**
   
    4. Projekt jest modyfikowany i dodawane są odwołania do rozszerzeń SDK usługi Media Services .NET, programu Media Services .NET SDK i innych zestawów zależnych.
4. Aby promować środowisko programistyczne czystsze, należy rozważyć włączenie przywracania pakietu NuGet. Aby uzyskać więcej informacji, zobacz [NuGet Package Restore"](https://docs.nuget.org/consume/package-restore).
5. Dodaj odwołanie do **zestawu System.Configuration.** Ten zestaw zawiera system.Configuration. **ConfigurationManager** klasa, która jest używana do uzyskiwania dostępu do plików konfiguracyjnych (na przykład App.config).
   
    1. Aby dodać odwołania za pomocą okna dialogowego Zarządzaj odwołaniami, kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań. Następnie kliknij przycisk **Dodaj**, a następnie kliknij pozycję **Odwołanie...**.
   
    2. Zostanie wyświetlone okno dialogowe Zarządzaj odwołaniami.
    3. W obszarze Zestawy .NET framework znajdź i wybierz zestaw System.Configuration i naciśnij **przycisk OK**.
6. Otwórz plik App.config i dodaj do pliku sekcję **appSettings.** Ustaw wartości potrzebne do nawiązania połączenia z interfejsem API usługi Media Services. Aby uzyskać więcej informacji, zobacz [Dostęp do interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Ustaw wartości, które są potrzebne do nawiązania połączenia przy użyciu metody uwierzytelniania **głównego usługi.**

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

7. Dodaj odwołanie **system.configuration** do projektu.
8. Zastąp istniejące **instrukcje przy użyciu** na początku pliku Program.cs następującym kodem:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    W tym momencie można przystąpić do rozpoczęcia tworzenia aplikacji usługi Media Services.    

## <a name="example"></a>Przykład

Oto mały przykład, który łączy się z interfejsem API usługi AMS i wyświetla listę wszystkich dostępnych procesorów multimediów.

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

## <a name="next-steps"></a>Następne kroki

Teraz [możesz połączyć się z interfejsem API AMS](media-services-use-aad-auth-to-access-ams-api.md) i rozpocząć [tworzenie](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


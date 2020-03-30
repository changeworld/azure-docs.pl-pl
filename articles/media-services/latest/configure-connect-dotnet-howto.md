---
title: Łączenie się z interfejsem API usługi Azure Media Services w wersji 3 — .NET
description: W tym artykule pokazano, jak połączyć się z interfejsem API usługi Media Services w wersji 3 za pomocą platformy .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: b8f4de1a5b9d8216ae2442631f5f9135c3c72d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269811"
---
# <a name="connect-to-media-services-v3-api---net"></a>Łączenie się z interfejsem API usługi Media Services w wersji 3 — .NET

W tym artykule pokazano, jak połączyć się z zestawem SDK usługi Azure Media Services w wersji 3 .NET przy użyciu metody logowania jednostki usługi.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta usługi Media Services
- Zainstaluj narzędzie, którego chcesz użyć do tworzenia platformy .NET. W tym artykule pokazano, jak korzystać z [programu Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Można użyć programu Visual Studio Code, zobacz [Praca z C#](https://code.visualstudio.com/docs/languages/csharp). Można też użyć innego edytora kodu.

> [!IMPORTANT]
> Przejrzyj [konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

1. Uruchom program Visual Studio. 
1. W menu **Plik** kliknij polecenie **Nowy** > **projekt**. 
1. Utwórz aplikację konsoli **.NET Core.**

Przykładowa aplikacja w tym `netcoreapp2.0`temacie, cele . Kod używa "asynchronii main", który jest dostępny począwszy od C# 7.1. Zobacz ten [blog,](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) aby uzyskać więcej informacji.

## <a name="add-required-nuget-packages"></a>Dodawanie wymaganych pakietów NuGet

1. W programie Visual Studio wybierz pozycję **Narzędzia** > **NuGet Package Manager** > **Konsola.**
2. W oknie Konsoli Menedżera `Install-Package` **pakietów** użyj polecenia, aby dodać następujące pakiety NuGet. Na przykład `Install-Package Microsoft.Azure.Management.Media`.

|Pakiet|Opis|
|---|---|
|`Microsoft.Azure.Management.Media`|Zestaw SDK usług multimediów platformy Azure. <br/>Aby upewnić się, że używasz najnowszego pakietu usługi Azure Media Services, sprawdź [microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Biblioteka uwierzytelniania ADAL dla usługi Azure SDK dla sieci NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Odczytywanie wartości konfiguracyjnych ze zmiennych środowiskowych i lokalnych plików JSON|
|`Microsoft.Extensions.Configuration.Json`|Odczytywanie wartości konfiguracyjnych ze zmiennych środowiskowych i lokalnych plików JSON
|`WindowsAzure.Storage`|SDK magazynu|

## <a name="create-and-configure-the-app-settings-file"></a>Tworzenie i konfigurowanie pliku ustawień aplikacji

### <a name="create-appsettingsjson"></a>Tworzenie pliku appsettings.json

1. Przejdź plik**tekstu** **ogólnego** > .
1. Nazwij go "appsettings.json".
1. Ustaw właściwość "Kopiuj do katalogu wyjściowego" pliku .json na "Kopiuj, jeśli nowsza" (tak, aby aplikacja mogła uzyskać do niego dostęp po opublikowaniu).

### <a name="set-values-in-appsettingsjson"></a>Ustawianie wartości w pliku appsettings.json

Uruchom `az ams account sp create` polecenie zgodnie z opisem w [interfejsach API dostępu](access-api-cli-how-to.md). Polecenie zwraca json, który należy skopiować do "appsettings.json".
 
## <a name="add-configuration-file"></a>Dodawanie pliku konfiguracji

Dla wygody dodaj plik konfiguracyjny, który jest odpowiedzialny za odczyt wartości z "appsettings.json".

1. Dodaj nową klasę cs do projektu. Nadaj jej nazwę `ConfigWrapper`. 
1. Wklej następujący kod w tym pliku (w tym przykładzie założono, że masz obszar nazw jest `ConsoleApp1`).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Łączenie się z klientem platformy .NET

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W poniższym kodzie Funkcja GetCredentialsAsync tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracyjnym.

1. Otwórz plik `Program.cs`.
1. Wklej następujący kod:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Przesyłanie, kodowanie i przesyłanie strumieniowe filmów - .NET](stream-files-tutorial-with-api.md) 
- [Samouczek: Przesyłanie strumieniowe na żywo z usługami Media Services w wersji 3 - .NET](stream-live-tutorial-with-api.md)
- [Samouczek: Analizowanie klipów wideo za pomocą usługi Media Services w wersji 3 - .NET](analyze-videos-tutorial-with-api.md)
- [Tworzenie danych wejściowych zadania z pliku lokalnego — .NET](job-input-from-local-file-how-to.md)
- [Tworzenie danych wejściowych zadania na podstawie adresu URL protokołu HTTPS — .NET](job-input-from-http-how-to.md)
- [Kodowanie za pomocą transformacji niestandardowej — .NET](customize-encoder-presets-how-to.md)
- [Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy — .NET](protect-with-aes128.md)
- [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM — .NET](protect-with-drm.md)
- [Pobieranie klucza podpisywania z istniejących zasad — .NET](get-content-key-policy-dotnet-howto.md)
- [Tworzenie filtrów za pomocą usługi Media Services — .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Zaawansowane przykłady wideo na żądanie usługi Azure Functions v2 z usługą Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Zobacz też

* [Dokumentacja platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* Aby uzyskać więcej przykładów kodu, zobacz [.NET SDK przykłady repozytorium.](https://github.com/Azure-Samples/media-services-v3-dotnet)

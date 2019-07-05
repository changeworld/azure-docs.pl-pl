---
title: Łączenie usługi Azure Media Services v3 API — .NET
description: Dowiedz się, jak nawiązać połączenie usługi Media Services v3 z interfejsem API przy użyciu platformy .NET.
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
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: a256eb787d7e3dbd800ec2e630cac591b07ca0fc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444165"
---
# <a name="connect-to-media-services-v3-api---net"></a>Łączenie z Media Services v3 API — .NET

W tym artykule pokazano, jak połączyć się z zestawu .NET SDK usługi Azure Media Services v3 przy użyciu metody głównej nazwy logowania usługi.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Upewnij się, że do zapamiętania nazwę grupy zasobów i nazwę konta usługi Media Services
- Zainstaluj narzędzie, którego chcesz użyć podczas tworzenia aplikacji .NET. Kroki opisane w tym artykule pokazano, jak używać [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Możesz używać programu Visual Studio Code, zobacz [Praca z C# ](https://code.visualstudio.com/docs/languages/csharp). Alternatywnie można użyć edytora inny kod.

## <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

1. Uruchom program Visual Studio. 
1. Z **pliku** menu, kliknij przycisk **New** > **projektu**. 
1. Tworzenie **platformy .NET Core** aplikacji konsoli.

Przykładowa aplikacja w tym temacie jest przeznaczony dla `netcoreapp2.0`. Kod używa "async main", która jest dostępna, począwszy od C# 7.1. Zobacz ten [blogu](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) Aby uzyskać więcej informacji.

## <a name="add-required-nuget-packages"></a>Dodawanie wymaganych pakietów NuGet

1. W programie Visual Studio, wybierz **narzędzia** > **Menedżera pakietów NuGet** > **konsoli Menedżera NuGet**.
2. W **Konsola Menedżera pakietów** oknie, użyj `Install-Package` polecenie, aby dodać następujące pakiety NuGet. Na przykład `Install-Package Microsoft.Azure.Management.Media`.

|Pakiet|Opis|
|---|---|
|`Microsoft.Azure.Management.Media`|Usługi Azure Media Services SDK. <br/>Aby upewnić się, w przypadku korzystania z najnowszego pakietu usługi Azure Media Services, zapoznaj się z [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Biblioteki uwierzytelniania ADAL dla zestawu Azure SDK dla NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Odczytywanie wartości konfiguracji zmiennych środowiskowych i lokalne pliki JSON|
|`Microsoft.Extensions.Configuration.Json`|Odczytywanie wartości konfiguracji zmiennych środowiskowych i lokalne pliki JSON
|`WindowsAzure.Storage`|Zestaw SDK usługi Storage|

## <a name="create-and-configure-the-app-settings-file"></a>Tworzenie i konfigurowanie pliku ustawień aplikacji

### <a name="create-appsettingsjson"></a>Tworzenie pliku appsettings.json

1. Przejdź z rzeczywistym użyciem **ogólne** > **plik tekstowy**.
1. Name it "appsettings.json".
1. Ustaw właściwość "Kopiuj do katalogu wyjściowego" plik JSON na wartość "Kopiuj Jeśli nowszy" (tak, aby aplikacja jest w stanie dostępu do niego po opublikowaniu).

### <a name="set-values-in-appsettingsjson"></a>Ustawianie wartości w pliku appsettings.json

Uruchom `az ams account sp create` polecenia zgodnie z opisem w [dostęp do interfejsów API](access-api-cli-how-to.md). Polecenie zwraca json, którego należy kopiować do pliku "appsettings.json".
 
## <a name="add-configuration-file"></a>Dodawanie pliku konfiguracji

Dla wygody Dodaj plik konfiguracji, który jest odpowiedzialny za odczytywanie wartości z "appsettings.json".

1. Dodaj nową klasę .cs do projektu. Nadaj jej nazwę `ConfigWrapper`. 
1. Wklej następujący kod w tym pliku (w tym przykładzie przyjęto założenie, ma przestrzeń nazw jest `ConsoleApp1`).

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

## <a name="connect-to-the-net-client"></a>Nawiązywać połączenia z klientem platformy .NET

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W poniższym kodzie funkcja GetCredentialsAsync tworzy obiekt ServiceClientCredentials oparte na poświadczeniach dostarczona w pliku konfiguracji lokalnej.

1. Otwórz `Program.cs`.
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

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Przekazywanie, kodowanie i przesyłanie strumieniowe klipów wideo — .NET](stream-files-tutorial-with-api.md) 
- [Samouczek: Transmisja strumieniowa na żywo z usługi Media Services 3 — .NET](stream-live-tutorial-with-api.md)
- [Samouczek: Analizowanie wideo za pomocą usługi Media Services 3 — .NET](analyze-videos-tutorial-with-api.md)
- [Tworzenie danych wejściowych zadania z pliku lokalnego — .NET](job-input-from-local-file-how-to.md)
- [Tworzenie danych wejściowych zadania na podstawie adresu URL protokołu HTTPS — .NET](job-input-from-http-how-to.md)
- [Kodowanie za pomocą transformacji niestandardowej — .NET](customize-encoder-presets-how-to.md)
- [Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy — .NET](protect-with-aes128.md)
- [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM — .NET](protect-with-drm.md)
- [Pobieranie klucza podpisywania z istniejących zasad — .NET](get-content-key-policy-dotnet-howto.md)
- [Tworzenie filtrów za pomocą usługi Media Services — .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Zaawansowane przykłady wideo na żądanie usługi Azure Functions v2 z usługą Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Zobacz także

[Dokumentacja platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)

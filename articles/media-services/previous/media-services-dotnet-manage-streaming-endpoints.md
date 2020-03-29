---
title: Zarządzanie punktami końcowymi przesyłania strumieniowego za pomocą sdk .NET. | Microsoft Docs
description: W tym artykule pokazano, jak zarządzać punktami końcowymi przesyłania strumieniowego za pomocą witryny Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b6c6fccd473ae57139c0b46bf32dc9468a4ba1a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901277"
---
# <a name="manage-streaming-endpoints-with-net-sdk"></a>Zarządzanie punktami końcowymi przesyłania strumieniowego za pomocą sdk .NET  

>[!NOTE]
>Zapoznaj się z [omówieniem](media-services-streaming-endpoints-overview.md) artykułu. Ponadto, przegląd [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

Kod w tym artykule pokazuje, jak wykonać następujące zadania przy użyciu narzędzia Azure Media Services .NET SDK:

- Sprawdź domyślny punkt końcowy przesyłania strumieniowego.
- Utwórz/dodaj nowy punkt końcowy przesyłania strumieniowego.

    Możesz mieć wiele punktów końcowych przesyłania strumieniowego, jeśli planujesz mieć różne sieci CDN lub sieć CDN i bezpośredni dostęp.

    > [!NOTE]
    > Naliczane są tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomionym.
    
- Zaktualizuj punkt końcowy przesyłania strumieniowego.
    
    Pamiętaj, aby wywołać funkcję Update().

- Usuń punkt końcowy przesyłania strumieniowego.

    >[!NOTE]
    >Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego.

Aby uzyskać informacje dotyczące skalowania punktu końcowego przesyłania strumieniowego, zobacz [ten](media-services-portal-scale-streaming-endpoints.md) artykuł.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

## <a name="add-code-that-manages-streaming-endpoints"></a>Dodawanie kodu zarządzającego punktami końcowymi przesyłania strumieniowego
    
Zastąp kod w Program.cs następującym kodem:

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.Live;

namespace AMSStreamingEndpoint
{
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

            var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s => s.Name.Contains("default")).FirstOrDefault();
            ExamineStreamingEndpoint(defaultStreamingEndpoint);

            IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
            UpdateStreamingEndpoint(newStreamingEndpoint);
            DeleteStreamingEndpoint(newStreamingEndpoint);
        }

        static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            Console.WriteLine(streamingEndpoint.Name);
            Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
            Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
            Console.WriteLine(streamingEndpoint.ScaleUnits);
            Console.WriteLine(streamingEndpoint.CdnProvider);
            Console.WriteLine(streamingEndpoint.CdnProfile);
            Console.WriteLine(streamingEndpoint.CdnEnabled);
        }

        static public IStreamingEndpoint AddStreamingEndpoint()
        {
            var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
            var option = new StreamingEndpointCreationOptions(name, 1)
            {
                StreamingEndpointVersion = new Version("2.0"),
                CdnEnabled = true,
                CdnProfile = "CdnProfile",
                CdnProvider = CdnProviderType.PremiumVerizon
            };

            var streamingEndpoint = _context.StreamingEndpoints.Create(option);

            return streamingEndpoint;
        }

        static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            if (streamingEndpoint.StreamingEndpointVersion == "1.0")
                streamingEndpoint.StreamingEndpointVersion = "2.0";

            streamingEndpoint.CdnEnabled = false;
            streamingEndpoint.Update();
        }

        static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            streamingEndpoint.Delete();
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


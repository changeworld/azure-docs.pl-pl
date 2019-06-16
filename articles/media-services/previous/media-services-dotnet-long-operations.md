---
title: Sondowanie operacji długotrwałych | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak sondowanie długotrwałych operacji.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 752c502268ef53d3c0575d92e75ce6a965fccd9f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61464984"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Dostarczanie transmisja strumieniowa na żywo za pomocą usługi Azure Media Services

## <a name="overview"></a>Omówienie

Microsoft Azure Media Services oferuje interfejsy API, które wysyłają żądania do usługi Media Services można uruchomić operacji (na przykład: tworzenie, uruchamianie, zatrzymywanie lub usuwanie kanału). Te operacje są długo działającymi.

Zestawu SDK .NET usługi Media Services udostępnia interfejsy API, który wysłać żądanie i poczekaj na zakończenie operacji (wewnętrznie, interfejsy API są sondowanie postęp operacji niektóre odstępach). Na przykład gdy wywołujesz kanału. Start(), metoda zwraca po uruchomieniu kanału. Można również użyć asynchroniczna wersja: await kanału. StartAsync() (Aby uzyskać informacji na temat wzorca asynchronicznego opartego na zadaniach, zobacz [NACIŚNIJ](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). Interfejsy API, Wyślij żądanie operacji, a następnie wykonać sondowanie stanu, aż do zakończenia operacji, które są nazywane "sondowania metody". Te metody (szczególnie wersję Async) są zalecane w przypadku zaawansowanych aplikacji klienckich i/lub usług stanowych.

Istnieją scenariusze, w których aplikacja nie może czekać na długo działające żądanie http i chce, aby sondować postęp operacji ręcznie. Typowym przykładem może być interakcji z bezstanowej usługi internetowej przeglądarki: gdy przeglądarka żąda próba utworzenia kanału, usługę sieci web inicjuje długotrwałej operacji i zwraca identyfikator operacji w przeglądarce. Przeglądarka może następnie poproś usługę sieci web, aby pobrać stan operacji na podstawie identyfikatora. Zestawu SDK .NET usługi Media Services udostępnia interfejsy API, które są przydatne w przypadku tego scenariusza. Te interfejsy API są nazywane "bez sondowania metody".
"Metody bez sondowania" mają następującym wzorcem nazewnictwa: Wyślij*OperationName*operacji (na przykład SendCreateOperation). Wyślij*OperationName*operacji metody zwracają **IOperation** obiekt; zwrócony obiekt zawiera informacje, które mogą być używane do śledzenia operacji. Wyślij*OperationName*OperationAsync metody zwracają **zadań\<IOperation >** .

Obecnie następujące klasy obsługuje metod innych niż sondowania:  **Kanał**, **StreamingEndpoint**, i **Program**.

Aby wykonać sondowanie dotyczące stanu operacji, użyj **GetOperation** metody **OperationBaseCollection** klasy. Użyj następujących przedziałów czasu, aby sprawdzić stan operacji: dla **kanału** i **StreamingEndpoint** operacje, używają 30 sekund; **Program** operacje, używają 10 sekund.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Przykład

W poniższym przykładzie zdefiniowano klasę o nazwie **ChannelOperations**. Tę definicję klasy może być punkt początkowy dla swojej definicji klasy usługi sieci web. Dla uproszczenia w poniższych przykładach używane wersji bez asynchronicznych metod.

W przykładzie pokazano również, jak klient może używać tej klasy.

### <a name="channeloperations-class-definition"></a>Definicja klasy ChannelOperations

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
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

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>Kod klienta

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


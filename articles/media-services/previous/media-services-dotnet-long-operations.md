---
title: Sondowanie długotrwałych operacji | Dokumenty firmy Microsoft
description: Usługa Azure Media Services oferuje interfejsy API, które wysyłają żądania do usługi Media Services w celu rozpoczęcia operacji (na przykład tworzenie, uruchamianie, zatrzymywania lub usuwanie kanału), te operacje są długotrwałe. W tym temacie pokazano, jak sondować długotrwałe operacje.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 43d9a6adc935010eab6e5e52d73f2019c8afcf5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887162"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Dostarczanie przesyłania strumieniowego na żywo za pomocą usługi Azure Media Services

## <a name="overview"></a>Omówienie

Usługa Microsoft Azure Media Services oferuje interfejsy API, które wysyłają żądania do usługi Media Services w celu rozpoczęcia operacji (na przykład: tworzenie, uruchamianie, zatrzymywania lub usuwanie kanału). Te operacje są długotrwałe.

Media Services .NET SDK udostępnia interfejsy API, które wysyłają żądanie i czekać na zakończenie operacji (wewnętrznie interfejsy API są sondowania dla postępu operacji w niektórych odstępach czasu). Na przykład podczas wywoływania kanału. Start(), metoda zwraca po uruchomieniu kanału. Można również użyć wersji asynchroniiowej: await channel. StartAsync() (aby uzyskać informacje na temat wzorca asynchronicznego opartego na zadaniach, zobacz [TAP](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). Interfejsy API, które wysyłają żądanie operacji, a następnie sondują stan, dopóki operacja nie zostanie ukończona, są nazywane "metodami sondowania". Te metody (zwłaszcza wersja Async) są zalecane dla bogatych aplikacji klienckich i/lub usług stanowych.

Istnieją scenariusze, w których aplikacja nie może czekać na długo działające żądanie http i chce ręcznie sondować postęp operacji. Typowym przykładem może być przeglądarka w interakcji z bezstanową usługą sieci web: gdy przeglądarka żąda utworzenia kanału, usługa sieci web inicjuje długotrwałą operację i zwraca identyfikator operacji do przeglądarki. Przeglądarka może następnie poprosić usługę sieci web o uzyskanie stanu operacji na podstawie identyfikatora. Media Services .NET SDK udostępnia interfejsy API, które są przydatne w tym scenariuszu. Te interfejsy API są nazywane "metody nie sondowania".
"Metody nie sondowania" mają następujący wzorzec nazewnictwa: Wyślij*OperationName*Operation (na przykład SendCreateOperation). Wyślij*OperationName*Metody operacji zwracają **IOperation** obiektu; zwrócony obiekt zawiera informacje, które mogą służyć do śledzenia operacji. Metody Send*OperationName*OperationAsync zwracają **>zadania\<IOperation **.

Obecnie następujące klasy obsługują metody niesadowywania: **Kanał**, **StreamingEndpoint**i **Program**.

Aby sondować stan operacji, należy użyć **GetOperation** metody **w OperationBaseCollection** klasy. Użyj następujących interwałów, aby sprawdzić stan operacji: dla operacji **Channel** i **StreamingEndpoint** użyj 30 sekund; w przypadku operacji **programu** użyj 10 sekund.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Przykład

Poniższy przykład definiuje klasę o nazwie **ChannelOperations**. Ta definicja klasy może być punktem wyjścia dla definicji klasy usługi sieci web. Dla uproszczenia poniższe przykłady używają nieaskronikowych wersji metod.

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


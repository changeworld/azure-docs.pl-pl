---
title: Sondowanie długotrwałe operacje | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób sondowania długotrwałej operacji.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: fa456a2d0bb427af80d67a0b971fe8bd41bb7868
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788424"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Dostarczanie transmisję strumieniową na żywo za pomocą usługi Azure Media Services

## <a name="overview"></a>Przegląd

Microsoft Azure Media Services udostępnia interfejsy API, które wysyłają żądania do usługi Media Services można uruchomić operacji (na przykład: Utwórz, uruchom, Zatrzymaj lub usuwanie kanału). Operacje te są długotrwałe.

SDK .NET usługi Media Services udostępnia interfejsy API, która wysłać żądania i poczekaj na ukończenie tej operacji (wewnętrznie, interfejsy API są sondowanie postęp operacji niektórych odstępach). Na przykład w przypadku wywołania kanału. Funkcji Start(), metoda zwraca po rozpoczęciu kanału. Można również użyć to wersja asynchroniczna: oczekiwania kanału. StartAsync() (Aby uzyskać informacje dotyczące wzorca asynchronicznego opartego na zadaniach, zobacz [wybierz](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). Interfejsy API, które Wyślij żądanie operacji, a następnie wykonać sondowanie stanu, aż do zakończenia operacji są nazywane "sondowania metody". Te metody (szczególnie wersję Async) są zalecane w przypadku zaawansowanych aplikacji klienckich i/lub usług stanowych.

Brak scenariuszy, w którym aplikacja nie może czekać na długo działające żądanie http i chce ręcznie sondować postęp operacji. Typowym przykładem jest przeglądarką interakcji z usługą sieci web bezstanowe: gdy przeglądarka żąda próba utworzenia kanału, usługę sieci web inicjuje operacją wymagającą dużo czasu i zwraca identyfikator operacji w przeglądarce. Przeglądarka może następnie poprosić usługi sieci web można uzyskać stanu działania na podstawie identyfikatora. SDK .NET usługi Media Services udostępnia interfejsy API, które są przydatne w przypadku tego scenariusza. Te interfejsy API są nazywane "metod innych niż sondowania".
"Metody z systemem innym niż sondowania" mają następujący wzór nazewnictwa: wysyłanie*OperationName*operacji (na przykład SendCreateOperation). Wyślij*OperationName*operacji metody zwracają **IOperation** obiekt; zwrócona obiektu zawiera informacje, które mogą służyć do śledzenia wykonać operację. Wyślij*OperationName*OperationAsync metody zwracają **zadań<IOperation>**.

Obecnie następujące klasy obsługuje metod innych niż sondowania: **kanału**, **StreamingEndpoint**, i **Program**.

Aby wykonać sondowanie stanu operacji, należy użyć **GetOperation** metoda **OperationBaseCollection** klasy. Użyj następujących przedziałów czasu, aby sprawdzić stan operacji: dla **kanału** i **StreamingEndpoint** operacji, użyj 30 sekund; dla **Program** operacji, użyj 10 sekund.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Przykład

W poniższym przykładzie zdefiniowano klasę o nazwie **ChannelOperations**. Ta definicja klasy może być punkt początkowy dla definicji klasy usługi sieci web. Dla uproszczenia przedstawiono przykłady Użyj wersji z systemem innym niż asynchronicznych metod.

W przykładzie przedstawiono również sposób klient może używać tej klasy.

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


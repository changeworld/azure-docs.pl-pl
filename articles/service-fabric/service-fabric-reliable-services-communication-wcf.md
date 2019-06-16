---
title: Niezawodne stos komunikacji WCF usług | Dokumentacja firmy Microsoft
description: Wbudowane stos komunikacji WCF w usłudze Service Fabric zapewnia komunikację WCF Usługa klienta dla usług Reliable Services.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: ae8a0ab0382083ebfca0834d2238403668efa71d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725605"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Stos komunikacji WCF usług Reliable Services
W ramach usług Reliable Services umożliwia autorom usługi wybierz stos komunikacji, który ma być używane dla swojej usługi. Można dodać stos komunikacji przy użyciu wybranych przez nich **ICommunicationListener** zwróciło [CreateServiceReplicaListeners lub CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) metody. Struktura zawiera implementacja stosu komunikacji, oparty na Windows Communication Foundation (WCF) dla usługi autorów, którzy chcą używać komunikacji WCF.

## <a name="wcf-communication-listener"></a>Odbiornik komunikacji WCF
Implementacja specyficzne dla usługi WCF **ICommunicationListener** są dostarczane przez **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** klasy.

Co najmniej Załóżmy, że mamy typ kontraktu usługi `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Możemy utworzyć odbiornik komunikacji WCF w usłudze w następujący sposób.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Zapisywanie klientów stos komunikacji WCF
Do pisania klientom komunikowanie się z usługami przy użyciu usługi WCF, udostępnia platformę **WcfClientCommunicationFactory**, który jest implementacją specyficzne dla usługi WCF [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Kanał komunikacyjny WCF jest możliwy z **WcfCommunicationClient** utworzone przez **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Kod klienta może użyć **WcfCommunicationClientFactory** wraz z **WcfCommunicationClient** który implementuje **ServicePartitionClient** ustalenie punkt końcowy usługi i komunikacji z usługą.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> Domyślne ServicePartitionResolver przyjęto założenie, że klient jest uruchomiony w tym samym klastrze usługi. Jeśli to znaczy nie tak, Utwórz obiekt ServicePartitionResolver i przekazać punkty końcowe połączenia klastra.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Zdalne wywołanie procedury z wywołaniem funkcji zdalnych usług Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Internetowy interfejs API z oprogramowaniem OWIN usług Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Zabezpieczenia komunikacji w przypadku usług Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)


---
title: Niezawodne stosu komunikacji usług WCF | Dokumentacja firmy Microsoft
description: Wbudowane stosu komunikacji usługi WCF w sieci szkieletowej usług zawiera Usługa klienta WCF komunikacji niezawodnej usług.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: f5ca579b446e5d3608d53cea73fa9392cd00db06
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Komunikacja oparta na WCF stos niezawodne usługi
Niezawodne usługi framework umożliwia autorom usługi wybierz stosu komunikacji, który ma być używany dla ich usługi. Można podłączyć stosu komunikacji wybranych przez nich za pośrednictwem **ICommunicationListener** zwrócony z [CreateServiceReplicaListeners lub CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) metody. Struktury dostarcza implementację tego stosu komunikacji oparte na systemie Windows Communication Foundation (WCF) dla autorów usługi, które mają być używane do komunikacji usługi WCF.

## <a name="wcf-communication-listener"></a>Odbiornik komunikacji usługi WCF
Implementacja specyficzne dla usługi WCF **ICommunicationListener** są dostarczane przez **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** klasy.

Co najmniej powiedz mamy typu kontraktu usługi `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Można utworzyć odbiornik komunikacji usługi WCF w usłudze w następujący sposób.

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Pisanie klientów dla stosu komunikacji usługi WCF
Do pisania klientom komunikowanie się z usługami przy użyciu usługi WCF, zapewnia platformę **WcfClientCommunicationFactory**, która jest implementacją specyficzne dla usługi WCF [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Kanał komunikacyjny WCF można uzyskać z **WcfCommunicationClient** utworzone przez **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Kod klienta można użyć **WcfCommunicationClientFactory** wraz z **WcfCommunicationClient** z zaimplementowanym **ServicePartitionClient** do określenia punktu końcowego usługi i komunikować się z usługą.

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
> Domyślnie ServicePartitionResolver przyjęto założenie, czy klient jest uruchomiony w tym samym klastrze jako usługa. Jeśli to znaczy inaczej, Utwórz obiekt ServicePartitionResolver i podaj punkty końcowe połączenia klastra.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Wywołanie procedury zdalnej komunikacji zdalnej niezawodne usługi](service-fabric-reliable-services-communication-remoting.md)
* [Interfejs API OWIN w niezawodnej usługi sieci Web](service-fabric-reliable-services-communication-webapi.md)
* [Zabezpieczenia komunikacji niezawodnej usług](service-fabric-reliable-services-secure-communication.md)


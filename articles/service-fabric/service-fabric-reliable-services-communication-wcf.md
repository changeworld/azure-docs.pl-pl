---
title: Stos komunikacji WCF niezawodnych usług
description: Wbudowany stos komunikacji WCF w sieci szkieletowej usług zapewnia komunikację WCF usługi klienta dla niezawodnych usług.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: 7f3b3974893316a488270f755b8f8822080658d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433859"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Stos komunikacji oparty na WCF dla niezawodnych usług
Struktura niezawodnych usług umożliwia autorom usług wybranie stosu komunikacji, którego chcą używać do obsługi. Mogą podłączyć stos komunikacji ich wyboru za pośrednictwem **ICommunicationListener** zwrócił z [CreateServiceReplicaListeners lub CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) metody. Struktura zapewnia implementację stosu komunikacji na podstawie programu Windows Communication Foundation (WCF) dla autorów usług, którzy chcą używać komunikacji opartej na WCF.

## <a name="wcf-communication-listener"></a>Odbiornik komunikacji WCF
Implementacja **ICommunicationListener** specyficzne dla WCF jest dostarczana przez **microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** klasy.

Żeby nie powiedzieć, że mamy umowę serwisową typu`ICalculator`

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Zapisywanie klientów dla stosu komunikacji WCF
Do pisania klientów do komunikowania się z usługami przy użyciu WCF, framework zapewnia **WcfClientCommunicationFactory**, który jest WCF specyficzne implementacji [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Dostęp do kanału komunikacyjnego WCF można uzyskać z **obiektu WcfCommunicationClient** utworzonego przez **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Kod klienta można użyć **WcfCommunicationClientFactory** wraz z **WcfCommunicationClient,** który implementuje **ServicePartitionClient** do określenia punktu końcowego usługi i komunikować się z usługą.

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
> Domyślny ServicePartitionResolver zakłada, że klient jest uruchomiony w tym samym klastrze co usługa. Jeśli tak nie jest, utwórz ServicePartitionResolver obiektu i przekazać w punktach końcowych połączenia klastra.
> 
> 

## <a name="next-steps"></a>Następne kroki
* [Zdalne wywołanie procedury z obsługą komunikacji zdalnej z niezawodnymi usługami](service-fabric-reliable-services-communication-remoting.md)
* [Internetowy interfejs API z owin w niezawodnych usługach](service-fabric-reliable-services-communication-webapi.md)
* [Zabezpieczanie komunikacji dla niezawodnych usług](service-fabric-reliable-services-secure-communication-wcf.md)


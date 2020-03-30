---
title: Bezpieczna komunikacja serwisowa oparta na WCF
description: Dowiedz się, jak zabezpieczyć komunikację opartą na WCF dla niezawodnych usług, które są uruchomione w klastrze sieci szkieletowej usług Azure.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ca5eafa4612503a13f80b7f238e4827979c0358b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614166"
---
# <a name="secure-wcf-based-communications-for-a-service"></a>Bezpieczna komunikacja oparta na WCF dla usługi
Bezpieczeństwo jest jednym z najważniejszych aspektów komunikacji. Struktura aplikacji niezawodne usługi zawiera kilka wstępnie utworzonych stosów komunikacji i narzędzi, których można użyć w celu zwiększenia bezpieczeństwa. W tym artykule o tym, jak poprawić bezpieczeństwo podczas korzystania z komunikacji zdalnej usługi.

Używamy istniejącego [przykładu,](service-fabric-reliable-services-communication-wcf.md) który wyjaśnia, jak skonfigurować stos komunikacji oparty na WCF dla niezawodnych usług. Aby zabezpieczyć usługę podczas korzystania z stosu komunikacji opartego na WCF, wykonaj następujące kroki:

1. Dla usługi należy pomóc zabezpieczyć odbiornik komunikacji WCF`WcfCommunicationListener`( ) który tworzysz. Aby to zrobić, `CreateServiceReplicaListeners` zmodyfikuj metodę.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. W kliencie `WcfCommunicationClient` klasa, która została utworzona w poprzednim [przykładzie](service-fabric-reliable-services-communication-wcf.md) pozostaje niezmieniona. Ale musisz zastąpić `CreateClientAsync` `WcfCommunicationClientFactory`metodę:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Służy `SecureWcfCommunicationClientFactory` do tworzenia klienta komunikacji`WcfCommunicationClient`WCF ( ). Użyj klienta do wywoływania metod usługi.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

W następnym kroku przeczytaj [interfejs API sieci Web z owin w reliable services](service-fabric-reliable-services-communication-webapi.md).

---
title: Bezpieczna komunikacja remoting service przy użyciu C# w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć na podstawie komunikacji zdalnej komunikacji usług C# usług reliable services, które są uruchomione w klastrze usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: f247142f26490e1899256917b64fbec7308fb281
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107674"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Bezpieczna komunikacja usług zdalnych usług w C# usługi
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java w systemie Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Zabezpieczeń jest jednym z najważniejszych aspektów komunikacji. Struktura aplikacji usług Reliable Services zawiera kilka wbudowanych komunikacji stosy i narzędzia, których można użyć w celu zwiększenia bezpieczeństwa. W tym artykule omówiono sposób poprawiania zabezpieczeń podczas korzystania z zdalna komunikacja usług w C# usługi. Opiera się na istniejącą [przykład](service-fabric-reliable-services-communication-remoting.md) , wyjaśnia, jak skonfigurować komunikację zdalną dla usług reliable services w C#. 

Aby zabezpieczyć usługi podczas korzystania z zdalna komunikacja usług przy użyciu C# usług, wykonaj następujące kroki:

1. Utwórz interfejs `IHelloWorldStateful`, który definiuje metody, które będą dostępne dla zdalnego wywołania procedury w usłudze. Usługa będzie używać `FabricTransportServiceRemotingListener`, która jest zadeklarowana w `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` przestrzeni nazw. Jest to `ICommunicationListener` implementację, która oferuje możliwości komunikacji zdalnej.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Dodaj ustawienia odbiornika i poświadczenia zabezpieczeń.

    Upewnij się, że zainstalowano certyfikat, którego chcesz użyć do zabezpieczania komunikacji usługi we wszystkich węzłach w klastrze. 
    
    > [!NOTE]
    > W węzłach systemu Linux, certyfikat musi być obecne jako pliki w formacie PEM w */var/lib/sfcerts* katalogu. Aby dowiedzieć się więcej, zobacz [lokalizacji i format certyfikatów X.509 w węzłach systemu Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Istnieją dwa sposoby, które można udostępniać ustawienia odbiornika i poświadczenia zabezpieczeń:

   1. Podaj je bezpośrednio w kodzie usługi:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Podaj je za pomocą [pakiet konfiguracji](service-fabric-application-and-service-manifests.md):

       Dodaj nazwane `TransportSettings` sekcji w pliku settings.xml.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       W tym przypadku `CreateServiceReplicaListeners` metoda będzie wyglądać następująco:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Jeśli dodasz `TransportSettings` sekcji w pliku settings.xml `FabricTransportRemotingListenerSettings` zostanie załadowany w tej sekcji wszystkie ustawienia domyślne.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        W tym przypadku `CreateServiceReplicaListeners` metoda będzie wyglądać następująco:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Gdy wywołujesz metody zabezpieczonej usługi przy użyciu komunikacji zdalnej stosu, zamiast `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klasy, aby utworzyć usługę serwera proxy, należy użyć `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Przekaż `FabricTransportRemotingSettings`, który zawiera `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Jeśli kod klienta jest uruchomiony jako część usługi, możesz załadować `FabricTransportRemotingSettings` z pliku settings.xml. Utwórz sekcję HelloWorldClientTransportSettings, która jest podobna do kodu usługi, jak pokazano wcześniej. Dla kodu klienta, należy wprowadzić następujące zmiany:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Jeśli klient nie jest uruchomiony jako część usługi, można utworzyć plik client_name.settings.xml w tej samej lokalizacji, gdzie jest client_name.exe. Następnie utwórz sekcję TransportSettings, w tym pliku.

    Podobnie jak usługa, w przypadku dodania `TransportSettings` sekcji settings.xml/client_name.settings.xml klienta `FabricTransportRemotingSettings` ładuje wszystkie ustawienia w tej sekcji ma domyślnie.

    W takiej sytuacji jest jeszcze bardziej uproszczone wcześniej kod:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Kolejnym krokiem odczytu [internetowego interfejsu API z OWIN usług Reliable Services](service-fabric-reliable-services-communication-webapi.md).

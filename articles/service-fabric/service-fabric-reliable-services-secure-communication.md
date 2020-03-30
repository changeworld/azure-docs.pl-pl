---
title: 'Bezpieczna komunikacja zdalna usługi z C #'
description: Dowiedz się, jak zabezpieczyć komunikację zdalną usługi dla niezawodnych usług języka C#, które są uruchomione w klastrze sieci szkieletowej usług Azure.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ee2f1d70f4094ccc7d80edbfaf16509b5124f607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609625"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Bezpieczna komunikacja zdalna usługi w usłudze Języka C#
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java w systemie Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Bezpieczeństwo jest jednym z najważniejszych aspektów komunikacji. Struktura aplikacji niezawodne usługi zawiera kilka wstępnie utworzonych stosów komunikacji i narzędzi, których można użyć w celu zwiększenia bezpieczeństwa. W tym artykule omówiono sposób poprawy zabezpieczeń podczas korzystania z komunikacji zdalnej usługi w usłudze Języka C#. Opiera się na istniejącym [przykładzie,](service-fabric-reliable-services-communication-remoting.md) który wyjaśnia, jak skonfigurować komunikacji zdalnej dla niezawodnych usług napisanych w języku C#. 

Aby zabezpieczyć usługę podczas korzystania z komunikacji zdalnej usługi za pomocą usług Języka C#, wykonaj następujące kroki:

1. Utwórz interfejs, który definiuje metody, `IHelloWorldStateful`które będą dostępne dla zdalnego wywołania procedury w usłudze. Usługa będzie `FabricTransportServiceRemotingListener`używać , który `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` jest zadeklarowany w obszarze nazw. Jest to `ICommunicationListener` implementacja, która zapewnia możliwości komunikacji zdalnej.

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

    Upewnij się, że certyfikat, którego chcesz użyć, aby ułatwić zabezpieczenie komunikacji usługi, jest zainstalowany we wszystkich węzłach w klastrze. 
    
    > [!NOTE]
    > W węzłach systemu Linux certyfikat musi być obecny jako pliki w formacie PEM w katalogu */var/lib/sfcerts.* Aby dowiedzieć się więcej, zobacz [Lokalizacja i format certyfikatów X.509 w węzłach systemu Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Istnieją dwa sposoby dostarczania ustawień odbiornika i poświadczeń zabezpieczeń:

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
   2. Podaj je za pomocą [pakietu konfiguracyjnego:](service-fabric-application-and-service-manifests.md)

       Dodaj nazwaną `TransportSettings` sekcję w pliku settings.xml.

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

       W takim przypadku `CreateServiceReplicaListeners` metoda będzie wyglądać następująco:

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

        Jeśli dodasz `TransportSettings` sekcję w pliku settings.xml, `FabricTransportRemotingListenerSettings` domyślnie wczytuje wszystkie ustawienia z tej sekcji.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        W takim przypadku `CreateServiceReplicaListeners` metoda będzie wyglądać następująco:

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
3. Po wywołaniu metod w zabezpieczonej usłudze przy użyciu stosu `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` komunikacji zdalnej, zamiast `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`używać klasy do tworzenia serwera proxy usługi, należy użyć . Przemiń `FabricTransportRemotingSettings`w `SecurityCredentials`, który zawiera .

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

    Jeśli kod klienta jest uruchomiony jako część usługi, można załadować `FabricTransportRemotingSettings` z pliku settings.xml. Utwórz HelloWorldClientTransportSettings sekcji, która jest podobna do kodu usługi, jak pokazano wcześniej. Wprowadzać następujące zmiany w kodzie klienta:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Jeśli klient nie jest uruchomiony jako część usługi, można utworzyć plik client_name.settings.xml w tej samej lokalizacji, w której znajduje się client_name.exe. Następnie utwórz sekcję TransportSettings w tym pliku.

    Podobnie jak w przypadku usługi, jeśli dodasz sekcję `TransportSettings` w pliku client settings.xml/client_name.settings.xml, `FabricTransportRemotingSettings` domyślnie ładuje wszystkie ustawienia z tej sekcji.

    W takim przypadku wcześniejszy kod jest jeszcze bardziej uproszczony:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


W następnym kroku przeczytaj [interfejs API sieci Web z owin w reliable services](service-fabric-reliable-services-communication-webapi.md).

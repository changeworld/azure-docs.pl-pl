---
title: Pomoc bezpiecznej komunikacji dla usług w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Przegląd sposobów zapewnienia bezpiecznej komunikacji dla niezawodnych usług, które są uruchomione w klastrze usługi sieć szkieletowa usług Azure.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 624d9d358145fb8b41013d686821cb157693d3c6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Pomoc w bezpiecznej komunikacji dla usług w sieci szkieletowej usług Azure
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java w systemie Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Zabezpieczanie usługi podczas korzystania z komunikacji zdalnej usługi
Będziemy używać istniejącego [przykład](service-fabric-reliable-services-communication-remoting-java.md) który wyjaśnia, jak skonfigurować komunikację zdalną dla niezawodne usługi. Aby ułatwić zabezpieczanie usługi podczas korzystania z usługi komunikacji zdalnej, wykonaj następujące kroki:

1. Tworzenie interfejsu `HelloWorldStateless`, który definiuje metody, które będą dostępne dla zdalnego wywołania procedury w usłudze. Usługa będzie używać `FabricTransportServiceRemotingListener`, która jest zadeklarowana w `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` pakietu. Jest to `CommunicationListener` implementację, która zapewnia możliwości komunikacji zdalnej.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Dodaj ustawienia odbiornika i poświadczeń zabezpieczeń.

    Upewnij się, że certyfikat, który ma być używany do zabezpieczania komunikacji usługi jest zainstalowany na wszystkich węzłach w klastrze. Istnieją dwa sposoby, które można udostępniać ustawienia odbiornika i poświadczenia zabezpieczeń:

   1. Podaj je za pomocą [pakietu konfiguracji](service-fabric-application-and-service-manifests.md):

       Dodaj `TransportSettings` sekcji w pliku settings.xml.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       W takim przypadku `createServiceInstanceListeners` metoda będzie wyglądać następująco:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Jeśli dodasz `TransportSettings` sekcji w pliku settings.xml bez żadnych prefiksów `FabricTransportListenerSettings` załaduje wszystkie ustawienia w tej sekcji ma domyślnie.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        W takim przypadku `CreateServiceInstanceListeners` metoda będzie wyglądać następująco:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Gdy wywoływać metod w usług zabezpieczonych przy użyciu stosu usług zdalnych, zamiast `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` klasę, aby utworzyć serwer proxy usługi, użyj `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Jeśli kod klienta działa w ramach usługi, można załadować `FabricTransportSettings` z pliku settings.xml. Utwórz sekcję TransportSettings, która jest podobna do kodu usługi, jak pokazano wcześniej. Wprowadź następujące zmiany w kodzie klienta:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```

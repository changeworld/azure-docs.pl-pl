---
title: Bezpieczna komunikacja usług zdalnych usług za pomocą języka Java w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć na podstawie komunikacji zdalnej komunikacji usług Java usług reliable services, które są uruchomione w klastrze usługi Azure Service Fabric.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: b465ab602a14285f8cf40b24ce1dfa9c763fecb8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667008"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Bezpieczna komunikacja usług zdalnych usług usługi Java
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java w systemie Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Zabezpieczeń jest jednym z najważniejszych aspektów komunikacji. Struktura aplikacji usług Reliable Services zawiera kilka wbudowanych komunikacji stosy i narzędzia, których można użyć w celu zwiększenia bezpieczeństwa. W tym artykule omówiono sposób poprawiania zabezpieczeń podczas korzystania z zdalna komunikacja usług usługi Java. Opiera się na istniejącą [przykład](service-fabric-reliable-services-communication-remoting-java.md) , wyjaśnia, jak skonfigurować komunikację zdalną dla usług reliable services napisaną w języku Java. 

Aby zabezpieczyć usługi podczas korzystania z zdalna komunikacja usług przy użyciu usługi Java, wykonaj następujące kroki:

1. Utwórz interfejs `HelloWorldStateless`, który definiuje metody, które będą dostępne dla zdalnego wywołania procedury w usłudze. Usługa będzie używać `FabricTransportServiceRemotingListener`, która jest zadeklarowana w `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` pakietu. Jest to `CommunicationListener` implementację, która oferuje możliwości komunikacji zdalnej.

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
2. Dodaj ustawienia odbiornika i poświadczenia zabezpieczeń.

    Upewnij się, że zainstalowano certyfikat, którego chcesz użyć do zabezpieczania komunikacji usługi we wszystkich węzłach w klastrze. Dla usług działających w systemie Linux certyfikat musi być dostępna jako plik PEM formmatted; albo `.pem` plik zawierający certyfikat i klucz prywatny lub `.crt` pliku, który zawiera certyfikat oraz `.key` pliku, który zawiera klucz prywatny. Aby dowiedzieć się więcej, zobacz [lokalizacji i format certyfikatów X.509 w węzłach systemu Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Istnieją dwa sposoby, które można udostępniać ustawienia odbiornika i poświadczenia zabezpieczeń:

   1. Podaj je za pomocą [pakiet konfiguracji](service-fabric-application-and-service-manifests.md):

       Dodaj nazwane `TransportSettings` sekcji w pliku settings.xml.

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

       W tym przypadku `createServiceInstanceListeners` metoda będzie wyglądać następująco:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Jeśli dodasz `TransportSettings` sekcji w pliku settings.xml bez dowolnego prefiksu, `FabricTransportListenerSettings` zostanie załadowany w tej sekcji wszystkie ustawienia domyślne.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        W tym przypadku `CreateServiceInstanceListeners` metoda będzie wyglądać następująco:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Gdy wywołujesz metody zabezpieczonej usługi przy użyciu komunikacji zdalnej stosu, zamiast `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` klasy, aby utworzyć usługę serwera proxy, należy użyć `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Jeśli kod klienta jest uruchomiony jako część usługi, możesz załadować `FabricTransportSettings` z pliku settings.xml. Utwórz sekcję TransportSettings, która jest podobna do kodu usługi, jak pokazano wcześniej. Dla kodu klienta, należy wprowadzić następujące zmiany:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```

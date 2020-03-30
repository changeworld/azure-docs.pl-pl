---
title: Bezpieczna komunikacja zdalna usługi z oprogramowaniem Java
description: Dowiedz się, jak zabezpieczyć komunikację zdalną usługi dla niezawodnych usług Java, które są uruchomione w klastrze sieci szkieletowej usług Azure.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: adefeadf939d398268624343d82c18cbf5ec87cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609642"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Bezpieczna komunikacja zdalna usługi w usłudze Java
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java w systemie Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Bezpieczeństwo jest jednym z najważniejszych aspektów komunikacji. Struktura aplikacji niezawodne usługi zawiera kilka wstępnie utworzonych stosów komunikacji i narzędzi, których można użyć w celu zwiększenia bezpieczeństwa. W tym artykule omówiono sposób poprawy zabezpieczeń podczas korzystania z komunikacji zdalnej usługi w usłudze Java. Opiera się na istniejącym [przykładzie,](service-fabric-reliable-services-communication-remoting-java.md) który wyjaśnia, jak skonfigurować komunikacji zdalnej dla niezawodnych usług napisanych w języku Java. 

Aby zabezpieczyć usługę podczas korzystania z komunikacji zdalnej usługi za pomocą usług Java, wykonaj następujące kroki:

1. Utwórz interfejs, który definiuje metody, `HelloWorldStateless`które będą dostępne dla zdalnego wywołania procedury w usłudze. Usługa będzie `FabricTransportServiceRemotingListener`używać , który `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` jest zadeklarowany w pakiecie. Jest to `CommunicationListener` implementacja, która zapewnia możliwości komunikacji zdalnej.

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

    Upewnij się, że certyfikat, którego chcesz użyć, aby ułatwić zabezpieczenie komunikacji usługi, jest zainstalowany we wszystkich węzłach w klastrze. W przypadku usług uruchomionych w systemie Linux certyfikat musi być dostępny jako plik z formą PEM; plik zawierający `.pem` certyfikat i klucz prywatny `.crt` lub plik zawierający `.key` certyfikat i plik zawierający klucz prywatny. Aby dowiedzieć się więcej, zobacz [Lokalizacja i format certyfikatów X.509 w węzłach systemu Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Istnieją dwa sposoby dostarczania ustawień odbiornika i poświadczeń zabezpieczeń:

   1. Podaj je za pomocą [pakietu konfiguracyjnego:](service-fabric-application-and-service-manifests.md)

       Dodaj nazwaną `TransportSettings` sekcję w pliku settings.xml.

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

        Jeśli dodasz `TransportSettings` sekcję w pliku settings.xml `FabricTransportListenerSettings` bez żadnego prefiksu, domyślnie załaduje wszystkie ustawienia z tej sekcji.

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
3. Po wywołaniu metod w zabezpieczonej usłudze przy użyciu stosu `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` komunikacji zdalnej, zamiast `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`używać klasy do tworzenia serwera proxy usługi, należy użyć .

    Jeśli kod klienta jest uruchomiony jako część usługi, można załadować `FabricTransportSettings` z pliku settings.xml. Utwórz TransportSettings sekcji, która jest podobna do kodu usługi, jak pokazano wcześniej. Wprowadzać następujące zmiany w kodzie klienta:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```

---
title: Interfejsy API klienta java usługi azure service fabric
description: Generowanie i używanie interfejsów API klienta java sieci szkieletowej usług przy użyciu specyfikacji interfejsu API REST klienta sieci szkieletowej usług
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 0a243c1cd0ab0dcb93a1cc6169c89ba18606f346
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451680"
---
# <a name="azure-service-fabric-java-client-apis"></a>Interfejsy API klienta java usługi azure service fabric

Interfejsy API klienta sieci szkieletowej usług umożliwiają wdrażanie aplikacji i kontenerów opartych na mikrousługach i zarządzanie nimi w klastrze sieci szkieletowej usług na platformie Azure, lokalnie, na komputerze deweloperskim lokalnym lub w innej chmurze. W tym artykule opisano sposób generowania i używania interfejsów API klienta java sieci szkieletowej sieci szkieletowej na interfejsach API REST klienta sieci szkieletowej usług

## <a name="generate-the-client-code-using-autorest"></a>Generowanie kodu klienta przy użyciu autoochłoka

[AutoRest](https://github.com/Azure/autorest) to narzędzie, które generuje biblioteki klienckie w celu uzyskania dostępu do usług internetowych RESTful. Wejście do autoognienia jest specyfikacją, która opisuje interfejs API REST przy użyciu formatu specyfikacji OpenAPI. [Interfejsy API REST klienta sieci szkieletowej usług](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) są zgodne z tą specyfikacją .

Wykonaj kroki wymienione poniżej, aby wygenerować kod klienta Java usługi przy użyciu narzędzia Autorest.

1. Zainstaluj środowisko NodeJs i menedżera NPM na swojej maszynie

    Jeśli używasz Linuksa, a następnie:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Jeśli używasz systemu Mac OS X, możesz:
    ```bash
    brew install node
    ```

2. Zainstaluj AutoRest przy użyciu NPM.
    ```bash
    npm install -g autorest
    ```

3. Rozwidli i [sklonować azure-rest-api-specs](https://github.com/Azure/azure-rest-api-specs) repozytorium w komputerze lokalnym i przejdź do sklonowanej lokalizacji z terminalu komputera.


4. Przejdź do lokalizacji wymienionej poniżej w sklonowanym repozytorium.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Jeśli wersja klastra nie jest 6.0.* następnie przejdź do odpowiedniego katalogu w folderze stabilnym.
    >   

5. Uruchom następujące polecenie autorest, aby wygenerować kod klienta java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Poniżej znajduje się przykład pokazujący użycie autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Następujące polecenie ``servicefabric.json`` przyjmuje plik specyfikacji jako dane ``java-rest-api-     code`` wejściowe i generuje ``servicefabricrest`` kod klienta java w folderze i zawiera kod w obszarze nazw. Po tym kroku można znaleźć ``models`` ``implementation`` dwa foldery ``package-info.java`` i ``java-rest-api-code`` dwa pliki ``ServiceFabricClientAPIs.java`` i generowane w folderze.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Uwzględnianie wygenerowanego klienta w projekcie i używanie go

1. Dodaj wygenerowany kod odpowiednio do projektu. Zaleca się utworzenie biblioteki przy użyciu wygenerowanego kodu i dołączenie tej biblioteki do projektu.
2. Jeśli tworzysz bibliotekę, dołącz następującą zależność do projektu biblioteki. Jeśli są następujące różne podejście następnie uwzględnić zależność odpowiednio.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Na przykład, jeśli używasz systemu kompilacji Maven, dołącz do ``pom.xml`` pliku następujące elementy:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Utwórz RestClient przy użyciu następującego kodu:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Użyj obiektu klienta i wykonać odpowiednie wywołania zgodnie z wymaganiami. Oto kilka przykładów, które pokazują użycie obiektu klienta. Zakładamy, że pakiet aplikacji jest zbudowany i przekazany do magazynu obrazów przed użyciem poniższego interfejsu API.
    * Udostępnienie wniosku
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Tworzenie aplikacji

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Opis wygenerowanego kodu
Dla każdego interfejsu API znajdziesz cztery przeciążenia implementacji. Jeśli istnieją parametry opcjonalne, można znaleźć cztery inne odmiany, w tym te parametry opcjonalne. Rozważmy na ``removeReplica``przykład interfejs API .
 1. **public void removeReplica(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * Jest to synchroniczne wariant removeReplica wywołanie interfejsu API
 2. **public ServiceFuture\<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout, final ServiceCallback\<Void> serviceCallback)**
    * Ten wariant wywołania interfejsu API może być używany, jeśli chcesz używać przyszłego programowania asynchronitycznego i używać wywołań zwrotnych
 3. **publiczne obserwowalne\<void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId)**
    * Ten wariant wywołania interfejsu API może być używany, jeśli chcesz użyć reaktywnego programowania asynchronitycznego
 4. **public Observable\<ServiceResponse\<Void>> removeReplicaWithServiceResponseAsync(String nodeName, UUID partitionId, String replicaId)**
    * Ten wariant wywołania INTERFEJSU API może być używany, jeśli chcesz użyć reaktywnego programowania asynchronitycznego i radzić sobie z odpowiedzią na odpoczynek RAW

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [interfejsach API REST sieci szkieletowej usług](https://docs.microsoft.com/rest/api/servicefabric/)


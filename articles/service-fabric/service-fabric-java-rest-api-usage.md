---
title: Interfejsów API usługi Azure Service Fabric Java klienta | Dokumentacja firmy Microsoft
description: Generowanie i używanie klienta usługi sieci szkieletowej Java interfejsów API przy użyciu specyfikacji interfejsu API REST klienta sieci szkieletowej usług
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 987959742335940dca8eb57c54d593aea90dec15
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111188"
---
# <a name="azure-service-fabric-java-client-apis"></a>Interfejsów API usługi Azure Service Fabric Java klienta

Klient sieci szkieletowej usług API umożliwia wdrażanie i zarządzanie mikrousług aplikacji i kontenery w klastrze usługi sieć szkieletowa usług na podstawie Azure, w infrastrukturze lokalnej, na komputerze deweloperskim lokalnej lub w chmurze innych. W tym artykule opisano sposób generowania i użytkowania interfejsów API klienta usługi sieci szkieletowej Java na klienta usługi sieć szkieletowa interfejsów API REST

## <a name="generate-the-client-code-using-autorest"></a>Generowanie kodu klienta przy użyciu AutoRest

[AutoRest](https://github.com/Azure/autorest) to narzędzie, które generuje bibliotek klienta do uzyskiwania dostępu do usług sieci web RESTful. Dane wejściowe AutoRest jest specyfikacją opisujący w formacie OpenAPI specyfikacja interfejsu API REST. [Klient usługi sieć szkieletowa interfejsów API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) wykonaj tej specyfikacji.

Wykonaj kroki wymienione poniżej do generowania kodu klienta usługi sieci szkieletowej w języku Java za pomocą narzędzia AutoRest.

1. Zainstaluj środowisko NodeJs i menedżera NPM na swojej maszynie

    Jeśli używasz systemu Linux następnie:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Jeśli używasz systemu Mac OS X następnie:
    ```bash
    brew install node
    ```

2. Zainstaluj AutoRest za pomocą programu NPM.
    ```bash
    npm install -g autorest
    ```

3. Rozwidlenia i klonowania [azure-rest-api specyfikacji](https://github.com/Azure/azure-rest-api-specs) repozytorium w komputerze lokalnym i przejdź do lokalizacji sklonowany z terminalu na komputerze.


4. Przejdź do lokalizacji wymienione poniżej w Twojej sklonowanego repozytorium.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Jeśli nie jest klastra w wersji 6.0. * następnie przejdź do odpowiedniego katalogu w folderze stabilna.
    >   

5. Uruchom następujące polecenie autorest do generowania kodu klienta java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Poniżej przedstawiono przykład prezentacja użycie autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Następujące polecenie przyjmuje ``servicefabric.json`` specyfikacji pliku jako dane wejściowe i generuje kod klienta java w ``java-rest-api-     code`` folderu i umieszcza kod w ``servicefabricrest`` przestrzeni nazw. Po wykonaniu tego kroku znajdował dwa foldery ``models``, ``implemenation`` i dwa pliki ``ServiceFabricClientAPIs.java`` i ``package-info.java`` wygenerowanych w ``java-rest-api-code`` folderu.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Obejmują i używania wygenerowanego klienta w projekcie

1. Dodaj wygenerowany kod odpowiednio do projektu. Firma Microsoft zaleca utworzenie bibliotekę przy użyciu wygenerowanego kodu i umieścić tę bibliotekę w swoim projekcie.
2. Jeśli tworzysz biblioteki obejmują następujące zależności w projekcie biblioteki. Jeśli wykonujesz różne podejścia następnie obejmują zależności odpowiednio.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Na przykład, jeśli używasz Maven system kompilacji są następujące w Twojej ``pom.xml`` pliku:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Utwórz RestClient, używając następującego kodu:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Użyć obiektu klienta i odpowiednie wywołania zgodnie z potrzebami. Oto kilka przykładów, które wykazują użycie obiektu klienta. Przyjęto założenie, że pakiet aplikacji jest wbudowane i przekazać do magazynu obrazów przed rozpoczęciem korzystania z poniższych interfejsów API.
    * Udostępnianie aplikacji
    
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
Dla każdego interfejsu API można znaleźć cztery przeciążenia implementacji. Istnieją następujące parametry opcjonalne może znaleźć cztery więcej zmian, łącznie z tych parametrów opcjonalnych. Na przykład należy wziąć pod uwagę interfejsu API ``removeReplica``.
 1. **publiczny removeReplica void (nodeName ciągu, UUID partitionId, replicaId ciągu, logiczna forceRemove, długi limitu czasu)**
    * Jest to wariant synchroniczne wywołanie removeReplica interfejsu API
 2. **publiczny ServiceFuture<Void> removeReplicaAsync (String nodeName, partitionId identyfikator UUID, replicaId ciągu, logiczna forceRemove, długi limitu czasu końcowego ServiceCallback<Void> serviceCallback)**
    * Ten wariant wywołanie interfejsu API mogą być używane, gdy chcesz używać przyszłych oparte na programowanie asynchroniczne oraz wywołań zwrotnych
 3. **publiczny według<Void> removeReplicaAsync (nodeName ciąg, identyfikator UUID partitionId replicaId ciąg)**
    * Ten wariant wywołanie interfejsu API mogą być używane, gdy chcesz użyć reaktywne programowanie asynchroniczne
 4. **publiczny według < ServiceResponse<Void>> removeReplicaWithServiceResponseAsync (nodeName ciąg, identyfikator UUID partitionId replicaId ciąg)**
    * Ten wariant wywołanie interfejsu API mogą być używane, gdy chcesz użyć reaktywne programowanie asynchroniczne i postępowania w przypadku reakcji NIEPRZETWORZONA rest

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [usługi sieć szkieletowa interfejsów API REST](https://docs.microsoft.com/rest/api/servicefabric/)


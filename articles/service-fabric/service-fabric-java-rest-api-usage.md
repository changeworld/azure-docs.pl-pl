---
title: Usługa Azure Service Fabric Java klienta API | Dokumentacja firmy Microsoft
description: Generowanie i używanie interfejsów API klienta Java usługi Service Fabric przy użyciu specyfikacji interfejsu API REST klienta usługi Service Fabric
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 97bba87331965b0f7ce20ec2ee089e0e18f72457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720284"
---
# <a name="azure-service-fabric-java-client-apis"></a>Usługa Azure Service Fabric Java klienta API

Interfejsów API klienta usługi Service Fabric umożliwia wdrażanie i zarządzanie nimi mikrousług na podstawie aplikacji i kontenerów w klastrze usługi Service Fabric na platformie Azure, lokalnie na lokalnej maszynie do programowania lub inne chmury. W tym artykule opisano sposób generowania i używania interfejsów API klienta Java usługi Service Fabric na podstawie klienta interfejsów API REST usługi Service Fabric

## <a name="generate-the-client-code-using-autorest"></a>Generowanie kodu klienta, za pomocą AutoRest

[AutoRest](https://github.com/Azure/autorest) to narzędzie, które generuje biblioteki klienckie do uzyskiwania dostępu do usług sieci web typu RESTful. Dane wejściowe AutoRest jest specyfikacja, która opisuje interfejs API REST w formacie specyfikacji interfejsu OpenAPI. [Klient usługi Service Fabric interfejsów API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) postępuj zgodnie z tą specyfikacją.

Wykonaj kroki wymienione poniżej, aby wygenerować kod klienta Java usługi Service Fabric przy użyciu narzędzia AutoRest.

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

2. Zainstaluj AutoRest za pomocą Menedżera NPM.
    ```bash
    npm install -g autorest
    ```

3. Rozwidlenia i klonowania [azure-rest-api specyfikacje](https://github.com/Azure/azure-rest-api-specs) repozytorium na komputerze lokalnym i przejdź do lokalizacji sklonowany z poziomu terminalu maszyny.


4. Przejdź do lokalizacji wymienionych poniżej w sklonowanym repozytorium.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Jeśli używana wersja klastra nie jest w wersji 6.0. * Przejdź do odpowiedniego katalogu w folderze stabilne.
    >   

5. Uruchom następujące polecenie autorest do generowania kodu klienta java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Poniżej przedstawiono przykład prezentacja użycie autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Następujące polecenie pobiera ``servicefabric.json`` specyfikacji pliku jako dane wejściowe i generuje kod klienta java w ``java-rest-api-     code`` folder i umieszcza kod w ``servicefabricrest`` przestrzeni nazw. Po wykonaniu tego kroku można znaleźć dwa foldery ``models``, ``implementation`` i dwa pliki ``ServiceFabricClientAPIs.java`` i ``package-info.java`` wygenerowane w ``java-rest-api-code`` folderu.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Uwzględnienia, a następnie użyj wygenerowanego klienta w projekcie

1. Dodaj wygenerowany kod odpowiednio do projektu. Firma Microsoft zaleca tworzenie biblioteki za pomocą wygenerowanego kodu, a następnie dołączyć tej biblioteki do projektu.
2. Jeśli tworzysz bibliotekę, obejmują następujące zależności w projekcie biblioteki. Jeśli korzystasz z innego podejścia następnie uwzględnić zależności odpowiednio.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Na przykład, jeśli używasz narzędzia Maven, system kompilacji obejmują następujące elementy w swojej ``pom.xml`` pliku:

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
4. Użyj obiektu klienta i wykonywanie odpowiednich wywołań zgodnie z potrzebami. Poniżej przedstawiono kilka przykładów, które pokazują użycie obiektu klienta. Przyjęto założenie, że pakiet aplikacji jest utworzone i przekazane do magazynu obrazów przed rozpoczęciem korzystania z poniższych interfejsów API.
    * Aprowizacja aplikacji
    
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
Dla każdego interfejsu API zawiera cztery przeciążenia implementacji. Jeśli istnieją następujące parametry opcjonalne czy możesz znaleźć cztery więcej zmian, łącznie z tych parametrów opcjonalnych. Na przykład rozważmy interfejsu API ``removeReplica``.
 1. **publiczne removeReplica void (nodeName ciąg identyfikatora UUID partycji, replicaId ciągu, logiczna forceRemove, długi limit czasu)**
    * Jest to wariant synchroniczne wywołanie removeReplica interfejsu API
 2. **publiczne ServiceFuture\<Void > removeReplicaAsync (ciąg nodeName, identyfikatora UUID partycji, replicaId ciągu, logiczna forceRemove, długi limit czasu, ostatnim ServiceCallback\<Void > serviceCallback)**
    * Ten wariant wywołania interfejsu API może być używany, jeśli chcesz używać przyszłości na podstawie programowania asynchronicznego i wywołania zwrotne
 3. **publiczne Obserwowalnymi\<Void > removeReplicaAsync (nodeName ciągu, identyfikatora UUID partycji, replicaId ciąg)**
    * Ten wariant wywołania interfejsu API może być używany, jeśli chcesz użyć programowania asynchronicznego
 4. **publiczne Obserwowalnymi\<ServiceResponse\<Void >> removeReplicaWithServiceResponseAsync (nodeName ciągu, identyfikatora UUID partycji, replicaId ciąg)**
    * Ten wariant wywołania interfejsu API może być używany, jeśli chcesz użyć programowania asynchronicznego i przeciwdziałania odpowiedź rest NIEPRZETWORZONE

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [usługi Service Fabric interfejsów API REST](https://docs.microsoft.com/rest/api/servicefabric/)


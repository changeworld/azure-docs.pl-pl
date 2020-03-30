---
title: Zintegruj istniejącą aplikację MongoDB z interfejsem API usługi Azure Cosmos DB dla mongodb i open service broker for Azure (OSBA)
description: W tym artykule dowiesz się, jak zintegrować istniejącą aplikację Java i MongoDB z interfejsem API usługi Azure Cosmos DB dla mongodb przy użyciu open service broker dla platformy Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker for Azure
ms.openlocfilehash: ddaa3b9aa198bc142e1bcbcab6b7b1e028eff2aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247917"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Zintegruj istniejącą aplikację MongoDB z interfejsem API usługi Azure Cosmos DB dla mongodb i open service broker for Azure (OSBA)

Azure Cosmos DB to usługa globalnie dystrybuowanej, wielomodelowej bazy danych. Zapewnia również zgodność protokołu przewodowego z kilkoma interfejsami API NoSQL, w tym dla MongoDB. Interfejs API usługi Cosmos DB dla mongodb umożliwia korzystanie z usługi Cosmos DB z istniejącą aplikacją MongoDB bez konieczności zmiany sterowników bazy danych aplikacji lub implementacji. Można również aprowizować usługę Usługi Usługi DB usługi przy użyciu Open Service Broker for Azure.

W tym artykule należy wziąć istniejącą aplikację Java, która używa bazy danych MongoDB i zaktualizować go do korzystania z bazy danych usługi Cosmos DB przy użyciu Open Service Broker dla platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim przejdziesz dalej, musisz mieć:
    
* [Utworzono klaster usługi Azure Kubernetes.](kubernetes-walkthrough.md)
* [Zainstaluj i skonfiguruj open service brokera dla platformy Azure w klastrze AKS](integrate-azure.md). 
* Zainstaluj i skonfigurowano [wiersz konfiguracji wiersza polecenia wykazu usług](https://svc-cat.io/docs/install/) do uruchamiania `svcat` poleceń.
* Mieć istniejącą bazę danych [MongoDB.](https://www.mongodb.com/) Na przykład może mieć MongoDB uruchomiony na [komputerze deweloperskim](https://docs.mongodb.com/manual/administration/install-community/) lub na [maszynie wirtualnej platformy Azure.](../virtual-machines/linux/install-mongodb.md)
* Mieć sposób łączenia się i wykonywania zapytań z bazy danych MongoDB, takich jak [powłoka mongo](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Pobieranie kodu aplikacji
    
W tym artykule użyjesz [aplikacji próbki muzyki wiosennej z Cloud Foundry,](https://github.com/cloudfoundry-samples/spring-music) aby zademonstrować aplikację, która używa bazy danych MongoDB.
    
Klonowanie aplikacji z usługi GitHub i przechodzenie do jej katalogu:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Przygotowanie aplikacji do korzystania z bazy danych MongoDB

Aplikacja próbki muzyki wiosennej udostępnia wiele opcji dla źródeł danych. W tym artykule można skonfigurować go do korzystania z istniejącej bazy danych MongoDB. 

Dodaj YAML po na końcu *src/main/resources/application.yml*. Ten dodatek tworzy profil o nazwie *mongodb* i konfiguruje identyfikator URI i nazwę bazy danych. Zastąp identyfikator URI informacjami o połączeniu z istniejącą bazą danych MongoDB. Dodanie identyfikatora URI, który zawiera nazwę użytkownika i hasło, bezpośrednio do tego pliku jest tylko do **użytku dewelopera** i **nigdy nie powinny być dodawane do kontroli wersji**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Po uruchomieniu aplikacji i powiedz jej, aby korzystać z profilu *mongodb,* łączy się z bazy danych MongoDB i używać go do przechowywania danych aplikacji.

Aby utworzyć aplikację:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Uruchom aplikację i powiedz jej, aby używała profilu *mongodb:*

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Przejdź `http://localhost:8080` do przeglądarki.

![Aplikacja Spring Music z danymi domyślnymi](media/music-app.png)

Zwróć uwagę, że aplikacja została wypełniona niektórymi [danymi domyślnymi](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Wchodź z nim, usuwając kilka istniejących albumów i tworząc kilka nowych.

Możesz sprawdzić, czy aplikacja korzysta z bazy danych MongoDB, łącząc się z nią i odwołując się do bazy danych *musicdb:*

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

W poprzednim przykładzie użyto [powłoki mongo](https://docs.mongodb.com/manual/mongo/) do łączenia się z bazą danych MongoDB i wykonywania jej kwerend. Można również sprawdzić, czy zmiany są zachowywane przez zatrzymanie aplikacji, ponowne uruchomienie jej i nawigowanie z powrotem do niej w przeglądarce. Zwróć uwagę, że wprowadzone zmiany nadal istnieją.


## <a name="create-a-cosmos-db-database"></a>Tworzenie bazy danych usługi Cosmos DB

Aby utworzyć bazę danych usługi Cosmos DB `svcat provision` na platformie Azure przy użyciu open service broker, należy użyć polecenia:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Poprzednie polecenie apreguje bazę danych usługi Cosmos DB na platformie Azure w grupie zasobów *MyResourceGroup* w regionie *eastus.* Więcej informacji na temat *resourceGroup*, *location*i innych parametrów JSON specyficznych dla platformy Azure jest dostępnych w [dokumentacji referencyjnej modułu usługi Cosmos DB](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Aby sprawdzić, czy baza danych ukończyła aprowizację, użyj polecenia `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Baza danych jest gotowa, gdy zobacz *Gotowe w* obszarze *STAN*.

Po zakończeniu inicjowania obsługi administracyjnej bazy danych należy powiązać jej metadane z [kluczem tajnym kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Inne aplikacje mogą następnie uzyskać dostęp do tych danych po tym, jak zostały powiązane z kluczem tajnym. Aby powiązać metadane bazy danych z `svcat bind` kluczem tajnym, użyj polecenia:

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>Korzystanie z bazy danych Usługi Cosmos DB z aplikacją

Aby użyć bazy danych usługi Cosmos DB z aplikacją, musisz znać identyfikator URI, aby się z nią połączyć. Aby uzyskać te informacje, użyj `kubectl get secret` polecenia:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Poprzednie polecenie pobiera klucz tajny *w pliku musicdb* i wyświetla tylko identyfikator URI. Wpisy tajne są przechowywane w formacie base64, więc poprzednie polecenie również dekoduje go.

Za pomocą identyfikatora URI bazy danych usługi Cosmos DB, należy zaktualizować *src/main/resources/application.yml,* aby go użyć:

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

Aktualizacja identyfikatora URI, który zawiera nazwę użytkownika i hasło, bezpośrednio do tego pliku jest tylko do **użytku dewelopera** i **nigdy nie powinny być dodawane do kontroli wersji**.

Odbuduj i uruchom aplikację, aby rozpocząć korzystanie z bazy danych usługi Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Zwróć uwagę, że aplikacja nadal używa profilu *mongodb* i identyfikatora URI, który rozpoczyna się od *mongodb://,* aby połączyć się z bazą danych usługi Cosmos DB. Interfejs [API bazy danych usługi Azure Cosmos db dla usługi MongoDB](../cosmos-db/mongodb-introduction.md) zapewnia tę zgodność. Umożliwia aplikacji nadal działać tak, jakby jest przy użyciu bazy danych MongoDB, ale faktycznie jest przy użyciu usługi Cosmos DB.

Przejdź `http://localhost:8080` do przeglądarki. Zwróć uwagę, że dane domyślne zostały przywrócone. Wchodź z nim, usuwając kilka istniejących albumów i tworząc kilka nowych. Można sprawdzić, czy zmiany są zachowywane przez zatrzymanie aplikacji, ponowne uruchomienie jej i nawigowanie z powrotem do niej w przeglądarce. Zwróć uwagę, że wprowadzone zmiany nadal istnieją. Zmiany są utrwalone w usłudze Cosmos DB utworzonej przy użyciu open service broker dla platformy Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Uruchamianie aplikacji w klastrze AKS

Za pomocą [usługi Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) można wdrożyć aplikację w klastrze AKS. Usługa Azure Dev Spaces ułatwia generowanie artefaktów, takich jak dockerfiles i wykresy helm, a także wdrażanie i uruchamianie aplikacji w usłudze AKS.

Aby włączyć usługi Azure Dev Spaces w klastrze usługi AKS:

```azurecli
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Użyj narzędzia Azure Dev Spaces, aby przygotować aplikację do uruchomienia w usłudze AKS:

```cmd
azds prep --public
```

To polecenie generuje kilka artefaktów, w tym *wykresy /* folder, który jest wykres Helm, w katalogu głównym projektu. To polecenie nie może wygenerować *pliku Docker dla* tego konkretnego projektu, więc trzeba go utworzyć.

Utwórz plik w katalogu głównym projektu o nazwie *Dockerfile* z tą zawartością:

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Ponadto należy zaktualizować *właściwość configurations.develop.build* w *pliku azds.yaml* do *false:*
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Należy również zaktualizować atrybut *containerPort* do *8080* w *charts/spring-music/templates/deployment.yaml:*

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

Aby wdrożyć aplikację w u usługi AKS:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Przejdź do adresu URL wyświetlanego w dziennikach. W poprzednim przykładzie należy *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* użyć programu . 

Sprawdź, czy aplikacja jest widoczna wraz ze zmianami.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób aktualizowania istniejącej aplikacji z używania mongodb do korzystania z interfejsu API bazy danych Usługi Cosmos dla mongodb. W tym artykule opisano również sposób aprowizowania usługi Usługi Cosmos DB przy użyciu open service broker dla platformy Azure i wdrażania tej aplikacji do usługi AKS za pomocą usługi Azure Dev Spaces.

Aby uzyskać więcej informacji na temat usługi Cosmos DB, Open Service Broker for Azure i Azure Dev Spaces, zobacz:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker for Azure](https://osba.sh)
* [Programowanie za pomocą usługi Dev Spaces](../dev-spaces/azure-dev-spaces.md)

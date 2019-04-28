---
title: Integrowanie istniejącej aplikacji MongoDB przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB i usługi Open Service Broker for Azure (OSBA)
description: W tym artykule dowiesz się, jak zintegrować istniejącej aplikacji języka Java i bazy danych MongoDB przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB przy użyciu usługi Open Service Broker for Azure (OSBA).
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Usługa cosmos DB, otwórz Service Broker, usługi Open Service Broker for Azure
ms.openlocfilehash: 46fa5564e5dd3429f812b263295044d867a8511c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028424"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrowanie istniejącej aplikacji MongoDB przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB i usługi Open Service Broker for Azure (OSBA)

Azure Cosmos DB to usługa globalnie dystrybuowanej, wielomodelowej bazy danych. Zapewnia także zgodność z protokołem o komunikacji sieciowej przy użyciu kilku interfejsów API NoSQL, łącznie z bazy danych mongodb. Interfejs API Cosmos DB dla bazy danych MongoDB umożliwia za pomocą usługi Cosmos DB istniejącej aplikacji MongoDB bez konieczności zmiany sterowników bazy danych aplikacji lub implementacji. Można również udostępniać usługi Cosmos DB, za pomocą usługi Open Service Broker for Azure.

W tym artykule możesz wykonać istniejącej aplikacji Java, która korzysta z bazy danych MongoDB i zaktualizować go do korzystania z bazy danych Cosmos DB za pomocą usługi Open Service Broker for Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim przejdziesz dalej, musisz mieć:
    
* Masz [klastra Azure Kubernetes Service](kubernetes-walkthrough.md) utworzone.
* Masz [Open Service Broker for Azure zainstalowane i skonfigurowane w klastrze AKS](integrate-azure.md). 
* Masz [interfejsu wiersza polecenia usługi katalogu](https://svc-cat.io/docs/install/) zainstalowany i skonfigurowany do uruchamiania `svcat` poleceń.
* Istniejący [bazy danych MongoDB](https://www.mongodb.com/) bazy danych. Na przykład można mieć bazą danych MongoDB uruchomioną Twoje [komputera deweloperskiego](https://docs.mongodb.com/manual/administration/install-community/) lub [maszyny Wirtualnej platformy Azure](../virtual-machines/linux/install-mongodb.md).
* Możliwości nawiązywania połączenia i wykonywania zapytań względem bazy danych MongoDB, takich jak [powłoki mongo](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Pobieranie kodu aplikacji
    
W tym artykule używasz [spring utworów muzycznych przykładowej aplikacji z usługi Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) aby zademonstrować aplikację, która korzysta z bazy danych MongoDB.
    
Klonowanie aplikacji z usługi GitHub i przechodzenie do jej katalogu:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Przygotowanie aplikacji do korzystania z bazy danych MongoDB

Przykładowa aplikacja muzyka spring udostępnia wiele opcji dla źródeł danych. W tym artykule możesz skonfigurować go do korzystania z istniejącej bazy danych MongoDB. 

Dodaj następującą YAML na końcu *src/main/resources/application.yml*. To dodawanie tworzy profil o nazwie *bazy danych mongodb* i konfiguruje identyfikator URI i nazwę bazy danych. Zastąp identyfikator URI informacje o połączeniu z istniejącej bazy danych MongoDB. Dodawanie identyfikatora URI, który zawiera nazwę użytkownika i hasło, bezpośrednio do tego pliku jest dla **tylko do użytku rozwoju** i **nigdy nie powinny zostać dodane do kontroli wersji**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Kiedy uruchomić aplikację i przekaż go do korzystania ze *bazy danych mongodb* profilu go nawiązuje połączenie z bazą danych MongoDB i używać go do przechowywania danych aplikacji.

Aby skompilować aplikację:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Uruchomić aplikację, a następnie przekaż go do korzystania ze *bazy danych mongodb* profilu:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Przejdź do `http://localhost:8080` w przeglądarce.

![Aplikacja Spring Music z danymi domyślnymi](media/music-app.png)

Zwróć uwagę, aplikacji zostały wypełnione z niektórymi [danych domyślnych](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Korzystać z niego, usuwając kilka istniejących ze zdjęciami i tworzyć kilka nowych.

Możesz sprawdzić aplikacji używa bazy danych MongoDB nawiązania z nim i wykonywania zapytań względem *musicdb* bazy danych:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

W poprzednim przykładzie użyto [powłoki mongo](https://docs.mongodb.com/manual/mongo/) do łączenia z bazą danych MongoDB i wykonuje zapytania. Możesz również sprawdzić, czy zmiany są zachowywane przez zatrzymanie aplikacji, jeszcze raz uruchomić go i przejdź wstecz do niego w przeglądarce. Zwróć uwagę, że zmiany wprowadzone przez użytkownika nadal istnieją.


## <a name="create-a-cosmos-db-database"></a>Tworzenie bazy danych Cosmos DB

Aby utworzyć bazę danych Cosmos DB na platformie Azure przy użyciu usługi Open Service Broker, użyj `svcat provision` polecenia:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Poprzednie polecenie obsługuje bazy danych Cosmos DB na platformie Azure w grupie zasobów *MyResourceGroup* w *eastus* regionu. Więcej informacji na temat *resourceGroup*, *lokalizacji*, a inne parametry JSON specyficzne dla platformy Azure jest dostępna w [dokumentację referencyjną usługi Cosmos DB modułu](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Aby sprawdzić, czy baza danych ukończyła aprowizację, użyj polecenia `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Bazy danych jest gotowy w występuje *gotowe* w obszarze *stan*.

Po bazy danych zostało ukończone, inicjowanie obsługi administracyjnej, należy powiązać metadane w celu [wpisie tajnym rozwiązania Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Inne aplikacje mogą następnie uzyskać dostępu do tych danych, po powiązano wpisu tajnego. Aby powiązać metadanych bazy danych z klucz tajny, należy użyć `svcat bind` polecenia:

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


## <a name="use-the-cosmos-db-database-with-your-application"></a>Użyj bazy danych Cosmos DB z aplikacją

Aby użyć bazy danych Cosmos DB z aplikacją, musisz wiedzieć identyfikatora URI się z nim łączyć. Aby uzyskać te informacje, należy użyć `kubectl get secret` polecenia:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Poprzednie polecenie pobiera *musicdb* wpisu tajnego i wyświetla tylko identyfikator URI. Klucze tajne są przechowywane w formacie base64, więc również dekoduje w poprzednim poleceniu.

Korzystając z identyfikatora URI bazy danych Cosmos DB, zaktualizuj *src/main/resources/application.yml* z niego korzystać:

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

Aktualizowanie identyfikatora URI, który zawiera nazwę użytkownika i hasło, bezpośrednio do tego pliku jest dla **tylko do użytku rozwoju** i **nigdy nie powinny zostać dodane do kontroli wersji**.

Ponownie skompilować i uruchomić aplikację, aby rozpocząć korzystanie z bazy danych Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Zwróć uwagę, aplikacja nadal używa *bazy danych mongodb* profilu i identyfikator URI, który rozpoczyna się od *bazy danych mongodb: / /* nawiązać połączenia z bazą danych Cosmos DB. [Usługi Azure Cosmos DB interfejs API systemu MongoDB](../cosmos-db/mongodb-introduction.md) zapewnia zgodność z tym. Umożliwia aplikacji dalsze działanie tak, jakby korzysta ona z bazy danych MongoDB, ale faktycznie jest za pomocą usługi Cosmos DB.

Przejdź do `http://localhost:8080` w przeglądarce. Zwróć uwagę, że Przywrócono wartości domyślne. Korzystać z niego, usuwając kilka istniejących ze zdjęciami i tworzyć kilka nowych. Możesz sprawdzić, czy zmiany są zachowywane przez zatrzymanie aplikacji, jeszcze raz uruchomić go i przejdź wstecz do niego w przeglądarce. Zwróć uwagę, że zmiany wprowadzone przez użytkownika nadal istnieją. Zmiany są zachowywane w usługą Cosmos DB zostały utworzone za pomocą usługi Open Service Broker for Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Uruchom aplikację w klastrze usługi AKS

Możesz użyć [miejsca do magazynowania Azure Dev](../dev-spaces/azure-dev-spaces.md) Aby wdrożyć aplikację w klastrze AKS. Usługa Azure Dev spacje ułatwia Generowanie artefaktów, takich jak wykresy plików Dockerfile i Helm, wdrożyć i uruchomić aplikację w usłudze AKS.

Aby włączyć usługi Azure Dev miejsca do magazynowania w klastrze AKS:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Użyj narzędzi usługi Azure Dev miejsca do magazynowania, aby przygotować aplikację do uruchamiania w usłudze AKS:

```cmd
azds prep --public
```

To polecenie spowoduje wygenerowanie kilku artefaktów, w tym *wykresy /* folder, który jest wykresu Helm, w katalogu głównym projektu. To polecenie nie może wygenerować *pliku Dockerfile* dla tego określonego projektu, więc musisz ją utworzyć.

Utwórz plik w katalogu głównym projektu o nazwie *pliku Dockerfile* z tą zawartością:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Ponadto należy zaktualizować *configurations.develop.build* właściwość *azds.yaml* do *false*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Należy również zaktualizować *containerPort* atrybutu *8080* w *charts/spring-music/templates/deployment.yaml*:

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

Aby wdrożyć aplikację w usłudze AKS:

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

Przejdź do adresu URL wyświetlany w dziennikach. W poprzednim przykładzie, należy użyć *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/*. 

Sprawdź, czy aplikacja zostanie wyświetlony wraz z zmiany.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano, jak zaktualizować istniejącą aplikację z przy użyciu bazy danych MongoDB za pomocą interfejsu API usługi Cosmos DB dla bazy danych MongoDB. W tym artykule opisano również sposób aprowizacji usługi Cosmos DB, za pomocą usługi Open Service Broker for Azure i wdrażanie tej aplikacji w usłudze AKS za pomocą usługi Azure Dev miejsca do magazynowania.

Aby uzyskać więcej informacji na temat usługi Cosmos DB, Open Service Broker for Azure i usługi Azure Dev miejsca do magazynowania, zobacz:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Usługi Open Service Broker for Azure](https://osba.sh)
* [Programowanie przy użyciu standardowego miejsca do magazynowania](../dev-spaces/azure-dev-spaces.md)

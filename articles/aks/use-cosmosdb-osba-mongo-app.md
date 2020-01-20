---
title: Integruj istniejącą aplikację MongoDB z interfejsem API Azure Cosmos DB dla MongoDB i Otwórz Service Broker dla platformy Azure (OSBA)
description: W tym artykule dowiesz się, jak zintegrować istniejącą aplikację Java i MongoDB z interfejsem API Azure Cosmos DB dla MongoDB przy użyciu otwartych Service Broker dla platformy Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB Otwórz Service Broker, Otwórz Service Broker dla platformy Azure
ms.openlocfilehash: 3d0ab0b27d77e45d779227d30c5a8e4f824ba62a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277704"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integruj istniejącą aplikację MongoDB z interfejsem API Azure Cosmos DB dla MongoDB i Otwórz Service Broker dla platformy Azure (OSBA)

Azure Cosmos DB to usługa globalnie dystrybuowanej, wielomodelowej bazy danych. Zapewnia również zgodność protokołu przewodowego z kilkoma interfejsami API NoSQL, takimi jak MongoDB. Cosmos DB API for MongoDB umożliwia korzystanie z Cosmos DB z istniejącą aplikacją MongoDB bez konieczności zmiany sterowników lub implementacji bazy danych aplikacji. Usługę Cosmos DB można również zainicjować za pomocą usługi Open Service Broker dla systemu Azure.

W tym artykule opisano tworzenie istniejącej aplikacji Java korzystającej z bazy danych MongoDB i aktualizowanie jej do używania bazy danych Cosmos DB przy użyciu otwartych Service Broker dla platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim przejdziesz dalej, musisz mieć:
    
* Utworzono [klaster usługi Azure Kubernetes](kubernetes-walkthrough.md) .
* Masz [otwarte Service Broker dla platformy Azure zainstalowane i skonfigurowane w KLASTRZE AKS](integrate-azure.md). 
* Należy zainstalować [interfejs wiersza polecenia katalogu usług](https://svc-cat.io/docs/install/) i skonfigurować go do uruchamiania poleceń `svcat`.
* Mieć istniejącą bazę danych [MongoDB](https://www.mongodb.com/) . Na przykład możesz mieć MongoDB uruchomione na [komputerze deweloperskim](https://docs.mongodb.com/manual/administration/install-community/) lub na maszynie [wirtualnej platformy Azure](../virtual-machines/linux/install-mongodb.md).
* Możliwość nawiązywania połączeń z bazą danych MongoDB i wykonywania w niej zapytań, takich jak [powłoka Mongo](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Pobieranie kodu aplikacji
    
W tym artykule użyto [przykładowej aplikacji muzycznej Music z Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) , aby przedstawić aplikację, która korzysta z bazy danych MongoDB.
    
Klonowanie aplikacji z usługi GitHub i przechodzenie do jej katalogu:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Przygotowywanie aplikacji do korzystania z bazy danych MongoDB

Przykładowa aplikacja ze sprężyną muzyczną oferuje wiele opcji dla źródeł danych. W tym artykule opisano konfigurowanie do korzystania z istniejącej bazy danych MongoDB. 

Dodaj YAML na końcu elementu *src/Main/Resources/Application. yml*. To dodanie spowoduje utworzenie profilu o nazwie *MongoDB* i skonfigurowanie identyfikatora URI i nazwy bazy danych. Zastąp identyfikator URI informacjami o połączeniu z istniejącą bazą danych MongoDB. Dodanie identyfikatora URI, który zawiera nazwę użytkownika i hasło, bezpośrednio do tego pliku służy tylko do celów **deweloperskich** i **nigdy nie powinien być dodawany do kontroli wersji**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Po uruchomieniu aplikacji i poinformowaniu go o użyciu profilu *MongoDB* nawiązuje połączenie z bazą danych MongoDB i używa jej do przechowywania danych aplikacji.

Aby skompilować aplikację:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Uruchom aplikację i poproś o użycie profilu *MongoDB* :

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Przejdź do `http://localhost:8080` w przeglądarce.

![Aplikacja Spring Music z danymi domyślnymi](media/music-app.png)

Zauważ, że aplikacja została wypełniona z niektórymi [danymi domyślnymi](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Z niej korzystać, usuwając kilka istniejących albumów i tworząc kilka nowych.

Możesz sprawdzić, czy aplikacja korzysta z bazy danych MongoDB, łącząc się z nią i wykonując zapytania o bazę danych *musicdb* :

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

Poprzedni przykład używa [powłoki Mongo](https://docs.mongodb.com/manual/mongo/) w celu nawiązania połączenia z bazą danych MongoDB i wykonywania w niej zapytania. Możesz również sprawdzić, czy zmiany są utrwalane, zatrzymując aplikację, ponownie uruchamiając ją i przechodząc do niej z powrotem w przeglądarce. Zauważ, że wprowadzone zmiany są tam nadal dostępne.


## <a name="create-a-cosmos-db-database"></a>Tworzenie bazy danych usługi Cosmos DB

Aby utworzyć bazę danych Cosmos DB na platformie Azure przy użyciu programu Open Service Broker, użyj polecenia `svcat provision`:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Powyższe polecenie Inicjuje obsługę Cosmos DB bazy danych na platformie Azure *w grupie zasobów Grupa zasobu w* regionie *wschodnim* . Więcej informacji na temat *zasobów*, *lokalizacji*i innych parametrów JSON specyficznych dla platformy Azure jest dostępnych w [dokumentacji dotyczącej modułu Cosmos DB](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Aby sprawdzić, czy baza danych ukończyła aprowizację, użyj polecenia `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Twoja baza danych jest gotowa, gdy zobaczysz *stan* *gotowe* .

Po zakończeniu aprowizacji bazy danych należy powiązać jej metadane z [wpisem tajnym Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Inne aplikacje mogą następnie uzyskiwać dostęp do tych danych po ich powiązaniu z wpisem tajnym. Aby powiązać metadane bazy danych z wpisem tajnym, użyj polecenia `svcat bind`:

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


## <a name="use-the-cosmos-db-database-with-your-application"></a>Używanie bazy danych Cosmos DB z aplikacją

Aby użyć bazy danych Cosmos DB w aplikacji, musisz znać identyfikator URI, aby nawiązać z nim połączenie. Aby uzyskać te informacje, użyj `kubectl get secret` polecenia:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Poprzednie polecenie pobiera wpis tajny *musicdb* i wyświetla tylko identyfikator URI. Wpisy tajne są przechowywane w formacie Base64, dlatego poprzednie polecenie dekoduje je.

Używając identyfikatora URI bazy danych Cosmos DB, zaktualizuj plik *src/Main/sources/Application. yml* , aby go użyć:

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

Zaktualizowanie identyfikatora URI, który zawiera nazwę użytkownika i hasło, bezpośrednio do tego pliku służy **tylko do celów deweloperskich** i **nigdy nie powinien być dodawany do kontroli wersji**.

Skompiluj ponownie i uruchom aplikację, aby rozpocząć korzystanie z bazy danych Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Zwróć uwagę, że aplikacja nadal używa profilu *MongoDB* oraz identyfikatora URI rozpoczynającego się od *MongoDB://* w celu nawiązania połączenia z bazą danych Cosmos DB. [Azure Cosmos DB API for MongoDB](../cosmos-db/mongodb-introduction.md) zapewnia tę zgodność. Dzięki temu aplikacja może nadal działać tak, jakby była używana baza danych MongoDB, ale w rzeczywistości używa Cosmos DB.

Przejdź do `http://localhost:8080` w przeglądarce. Zauważ, że domyślne dane zostały przywrócone. Z niej korzystać, usuwając kilka istniejących albumów i tworząc kilka nowych. Możesz sprawdzić, czy zmiany są utrwalane, zatrzymując aplikację, ponownie uruchamiając ją i przechodząc do niej z powrotem w przeglądarce. Zauważ, że wprowadzone zmiany są tam nadal dostępne. Zmiany są utrwalane w Cosmos DB utworzonych za pomocą otwartych Service Broker dla platformy Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Uruchamianie aplikacji w klastrze AKS

Za pomocą [Azure dev Spaces](../dev-spaces/azure-dev-spaces.md) można wdrożyć aplikację w klastrze AKS. Azure Dev Spaces pomaga generować artefakty, takie jak wykresy wieloetapowe dockerfile i Helm, a także wdrażać i uruchamiać aplikacje w AKS.

Aby włączyć Azure Dev Spaces w klastrze AKS:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Użyj narzędzi Azure Dev Spaces, aby przygotować aplikację do działania w programie AKS:

```cmd
azds prep --public
```

To polecenie generuje kilka artefaktów, w tym *wykresów/* folderów, które są wykresem Helm w katalogu głównym projektu. To polecenie nie może wygenerować *pliku dockerfile* dla tego konkretnego projektu, więc trzeba go utworzyć.

Utwórz plik w katalogu głównym projektu o nazwie *pliku dockerfile* z tą zawartością:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Ponadto należy zaktualizować właściwości *Configurations. opracowywać. Build* w *azds. YAML* do *wartości false*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Należy również zaktualizować atrybut *containerPort* do *8080* na *wykresach/Spring-Music/templates/Deployment. YAML*:

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

Przejdź do adresu URL wyświetlanego w dziennikach. W poprzednim przykładzie użyto *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* . 

Sprawdź, czy aplikacja zostanie wyświetlona wraz ze zmianami.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób aktualizowania istniejącej aplikacji z używania programu MongoDB do korzystania z interfejsu API Cosmos DB dla MongoDB. W tym artykule opisano również sposób aprowizacji usługi Cosmos DB przy użyciu otwartych Service Broker dla platformy Azure i wdrażania tej aplikacji w celu AKS z Azure Dev Spaces.

Aby uzyskać więcej informacji na temat Cosmos DB, Otwórz Service Broker dla systemu Azure i Azure Dev Spaces Zobacz:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Otwórz Service Broker dla platformy Azure](https://osba.sh)
* [Programowanie przy użyciu funkcji miejsca do magazynowania](../dev-spaces/azure-dev-spaces.md)

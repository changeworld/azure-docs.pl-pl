---
title: Używanie bazy danych usługi Open Service Broker for Azure z aplikacją w rozwiązaniu Pivotal Cloud Foundry
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: Opis sposobu konfigurowania aplikacji Pivotal Cloud Foundry w celu używania bazy danych usługi Open Service Broker for Azure
keywords: Pivotal Cloud Foundry, Cloud Foundry, Open Service Broker, Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197219"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>Samouczek: Używanie bazy danych usługi Open Service Broker for Azure z aplikacją w rozwiązaniu Pivotal Cloud Foundry

Korzystanie z usługi Open Service Broker for Azure z wystąpieniem rozwiązania Pivotal Cloud Foundry umożliwia aprowizowanie usług, takich jak bazy danych, na platformie Azure bezpośrednio z interfejsu wiersza polecenia platformy Cloud Foundry i wystąpienia rozwiązania Pivotal Cloud Foundry. Możesz również powiązać te usługi z aplikacją uruchomioną w wystąpieniu rozwiązania Pivotal Cloud Foundry. Gdy w ten sposób tworzysz powiązanie aplikacji z usługą, nie musisz aktualizować żadnego kodu ani konfiguracji w aplikacji. W tym artykule wyjaśniono, jak używać usługi Open Service Broker for Azure dla bazy danych z aplikacją w rozwiązaniu Pivotal Cloud Foundry.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowanie obszaru aplikacji w rozwiązaniu Pivotal Cloud Foundry.
> * Klonowanie źródła przykładowej aplikacji z usługi GitHub.
> * Przygotowywanie aplikacji do wdrożenia.
> * Wdrażanie aplikacji.
> * Tworzenie bazy danych przy użyciu usługi Open Service Broker for Azure.
> * Powiązanie bazy danych z aplikacją.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim przejdziesz dalej, musisz mieć:

* [zainstalowane i skonfigurowane rozwiązanie Pivotal Cloud Foundry](create-cloud-foundry-on-azure.md)
* [zainstalowaną i skonfigurowaną usługę Open Service Broker for Azure](https://network.pivotal.io/products/azure-open-service-broker-pcf) z wystąpieniem platformy Cloud Foundry

Oto przykład ekranu usługi Pivotal Cloud Foundry Ops Manager z zainstalowanym i skonfigurowanym kafelkiem usługi Open Service Broker for Azure:

![Rozwiązanie Pivotal Cloud Foundry z zainstalowaną usługą Open Service Broker for Azure](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>Przygotowanie obszaru aplikacji w rozwiązaniu Pivotal Cloud Foundry

Aby wdrożyć aplikację w wystąpieniu rozwiązania Pivotal Cloud Foundry, konieczne będzie zalogowanie się przy użyciu narzędzia wiersza polecenia `cf`. Musisz mieć również wskazaną żądaną docelową organizację i obszar.

Aby sprawdzić, czy użytkownik jest zalogowany i wyświetla wybrany obszar docelowy, użyj polecenia `cf target`. Poniższy przykład przedstawia użytkownika już zalogowanego jako *administrator*, używającego organizacji *myorg* i wskazującego jako docelowy obszar *dev*:

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

Aby się zalogować, użyj polecenia `cf login`:

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Aby utworzyć nową organizację i obszar, użyj poleceń `cf create-org` i `cf create-space`:

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

Aby wskazać obszar jako docelowy, użyj polecenia `cf target`:

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>Pobieranie kodu aplikacji

Aby aplikacja mogła korzystać z usługi Open Service Broker for Azure, musi używać źródła danych, takiego jak baza danych. W tym artykule użyjemy [przykładowej aplikacji Spring Music z usługi Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music), aby zademonstrować aplikację korzystającą ze źródła danych.

Klonowanie aplikacji z usługi GitHub i przechodzenie do jej katalogu:

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> Aby wyświetlić źródła danych dla tej aplikacji, otwórz plik [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml).


## <a name="prepare-the-application-for-deployment"></a>Przygotowanie aplikacji do wdrożenia

Przed wdrożeniem aplikacji w wystąpieniu rozwiązania Pivotal Cloud Foundry należy ją skompilować. W celach demonstracyjnych włączamy także niektóre funkcje rejestrowania *DEBUG*, które będzie rejestrować informacje o połączeniu ze źródłem danych.

Aby włączyć rejestrowanie *DEBUG* dla szczegółów połączenia z bazą danych, dodaj poniższą właściwość yaml na końcu pliku *application.yml*:

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

Przykładowa aplikacja używa narzędzia Gradle w celu skompilowania aplikacji jako pliku JAR możliwego do uruchomienia na platformie Spring Boot. Możliwy do uruchomienia plik JAR zostanie wdrożony w Twoim wystąpieniu rozwiązania Pivotal Cloud Foundry. Aby skompilować aplikację:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>Wdrażanie aplikacji

Użyj polecenia `cf push`, aby wdrożyć aplikację w wystąpieniu rozwiązania Pivotal Cloud Foundry:

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

Skopiuj wartość z *tras* wyświetlanych w wyjściu z polecenia `cf push`. Trasa jest adresem URL, którego użyjesz w celu uzyskania dostępu do uruchomionej aplikacji. Na przykład:

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


Po wdrożeniu aplikacji możesz wyświetlić dzienniki aplikacji, aby zobaczyć adres URL połączenia używany przez aplikację. Poniższe polecenie wyświetla dzienniki aplikacji i używa elementu `grep` do wyszukania konfiguracji *jdbcUrl*.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

Zwróć uwagę, że aplikacja używa bazy *h2:mem:testdb*, która jest bazą danych w pamięci. Aplikacja platformy Spring jest automatycznie konfigurowana do użycia bazy danych w pamięci, gdy zależność bazy danych w pamięci znajduje się w ścieżce klasy i [automatyczna konfiguracja](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html) jest włączona. Przykładowa aplikacja ma [skonfigurowaną zależność bazy danych w pamięci h2](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49) i włączoną automatyczną konfigurację w pliku [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8).

Użyj trasy aplikacji, aby przejść do niej w przeglądarce. Trasa, czyli adres URL, jest wyświetlany w danych wyjściowych z polecenia `cf push`.

> [!TIP]
> Adres URL aplikacji można także wyświetlić, uruchamiając polecenie `cf apps`.

Po przejściu do aplikacji przy użyciu przeglądarki wejdź z nią w interakcję, usuwając kilka istniejących albumów i tworząc kilka nowych. Przykładowa aplikacja używa bazy danych w pamięci do zapisywania zmian. Zauważysz również, że aplikacja została wypełniona [danymi domyślnymi](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). 

![Aplikacja Spring Music z danymi domyślnymi](media/music-app.png)

Ponieważ aplikacja korzysta z bazy danych w pamięci, zmiany nie zostaną utrwalone, jeśli aplikacja zostanie ponownie uruchomiona lub ponownie wdrożona. Aby pokazać, że zmiany nie są utrwalane, po dokonaniu paru zmian ponownie przygotuj swoją aplikację, używając polecenia `cf restage`:

```cmd
cf restage spring-music
```

Po ponownym przygotowaniu aplikacji przejdź do niej w przeglądarce, używając tego samego adresu URL. Zwróć uwagę, że wprowadzone zmiany zostały usunięte i wyświetlane są dane domyślne.

![Aplikacja Spring Music z danymi domyślnymi](media/music-app.png)

## <a name="create-a-database"></a>Tworzenie bazy danych

Aby utworzyć trwałą bazę danych na platformie Azure przy użyciu usługi Open Service Broker, należy użyć polecenia `cf create-service`. Poniższe polecenie obsługuje bazę danych PostgreSQL na platformie Azure w grupie zasobów *MyResourceGroup* w regionie *eastus*. Więcej informacji na temat parametrów *resourceGroup*, *location* i innych parametrów JSON specyficznych dla platformy Azure jest dostępnych w [dokumentacji referencyjnej modułu PostgreSQL](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision):

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

Baza danych jest uwidaczniana w wystąpieniu rozwiązania Pivotal Cloud Foundry jako usługa o nazwie *mypgsql*. Po ukończeniu aprowizacji bazy danych, co powinno zająć kilka minut, możesz powiązać z nią aplikację. Aby sprawdzić, czy baza danych ukończyła aprowizację, użyj polecenia `cf services`:

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

Wartością parametru *last operation* (ostatnia operacja) będzie *create succeeded* (pomyślnie utworzono), jeśli aprowizacja została ukończona.

## <a name="bind-the-database-to-your-application"></a>Powiązanie bazy danych z aplikacją

Użyj polecenia `bind-service`, aby powiązać usługę z aplikacją.

```cmd
cf bind-service spring-music mypgsql
```

Po powiązaniu usługi z aplikacją musisz ponownie przygotować aplikację, aby zmiany zaczęły obowiązywać.

```cmd
cf restage spring-music
```

Aplikacja jest [skonfigurowana do używania środowiska Spring Cloud Connectors](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46), co umożliwia wystąpieniu rozwiązania Pivotal Cloud Foundry użycie [automatycznej ponownej konfiguracji](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto) w źródle danych aplikacji. W tym przypadku wystąpienie rozwiązania Pivotal Cloud Foundry automatycznie ponownie skonfiguruje aplikację do używania usługi, z którą jest powiązana, dla źródła danych po ponownym przygotowaniu aplikacji.

Gdy aplikacja zostanie ponownie przygotowana, będzie do przechowywania danych używać bazy danych *mypgsql* zamiast bazy danych w pamięci.

Wszelkie wprowadzone zmiany będą teraz utrwalane i zachowywane po ponownych uruchomieniach i ponownych wdrożeniach. Możesz również zobaczyć adres URL połączenia używany przez aplikację, ponownie wyświetlając dzienniki aplikacji.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

Zwróć uwagę, że są wyświetlane dwa wpisy:

* Oryginalna wartość *h2:mem:testdb*.
* Zaktualizowana wartość dla bazy danych programu PostgreSQL po ponownym przygotowaniu aplikacji.

Aby sprawdzić, czy dane są utrwalane w bazie danych PostgreSQL, przejdź do aplikacji w przeglądarce, wprowadź pewne zmiany, a następnie ponownie przygotuj aplikację:

```cmd
cf restage spring-music
```

Po ponownym przygotowaniu aplikacji przejdź do niej w przeglądarce, używając tego samego adresu URL. Zwróć uwagę, że wprowadzone zmiany są nadal dostępne.

## <a name="cleanup"></a>Czyszczenie

Jeśli chcesz odłączyć aplikację od bazy danych, możesz usunąć jej powiązanie za pomocą polecenia `cf unbind-service`.

```cmd
cf unbind-service spring-music mypgsql
```

Podobnie jak w przypadku tworzenia powiązania aplikacji z usługą, należy ponownie przygotować aplikację, aby te zmiany zaczęły obowiązywać. Ta akcja pozostawi *mypgsql* i aplikację bez zmian, ale aplikacja zacznie używać bazy danych w pamięci zamiast *mypgsql*.

Aby usunąć bazę danych, możesz użyć polecenia `cf delete-service`. *Tej akcji nie można cofnąć, więc przed kontynuowaniem upewnij się, że chcesz usunąć bazę danych.*

```cmd
cf delete-service mypgsql
```

Aby usunąć aplikację z wystąpienia rozwiązania Pivotal Cloud Foundry:
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku opisano wdrażanie aplikacji w rozwiązaniu Pivotal Cloud Foundry, a także tworzenie bazy danych za pomocą usługi Open Service Broker for Azure. Omówiono również tworzenie powiązania bazy danych z aplikacją w wystąpieniu rozwiązania Pivotal Cloud Foundry. Aby uzyskać więcej informacji na temat wdrażania aplikacji w rozwiązaniu Cloud Foundry na platformie Azure, zobacz:

* [Rozwiązanie Cloud Foundry na platformie Azure](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Wdrażanie pierwszej aplikacji w rozwiązaniu Cloud Foundry na platformie Microsoft Azure](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)
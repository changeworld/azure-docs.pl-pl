---
title: Tworzenie aplikacji internetowej języka Java Enterprise w systemie Linux — Azure App Service | Microsoft Docs
description: Dowiedz się, jak uruchomić aplikację języka Java Enterprise na serwerze Wildfly w usłudze Azure App Service w systemie Linux.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 3a668783e8257ef9074d12b30ff0afc3a40325f4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539727"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Samouczek: Tworzenie aplikacji internetowej języka Java EE korzystającej z bazy danych Postgres na platformie Azure

W tym samouczku przedstawiono tworzenie aplikacji internetowej języka Java Enterprise Edition (EE) w usłudze Azure App Service i łączenie jej z bazą danych Postgres. Po ukończeniu tego samouczka będziesz mieć działającą w usłudze [Azure App Service dla systemu Linux](app-service-linux-intro.md) aplikację [WildFly](https://www.wildfly.org/about/), która umożliwia przechowywanie danych w usłudze [Azure Database for Postgres](https://azure.microsoft.com/services/postgresql/).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wdrażanie aplikacji języka Java EE na platformie Azure przy użyciu narzędzia Maven
> * Tworzenie bazy danych Postgres na platformie Azure
> * Konfigurowanie serwera WildFly pod kątem korzystania z bazy danych Postgres
> * Aktualizowanie i ponowne wdrażanie aplikacji
> * Uruchamianie testów jednostkowych na serwerze WildFly

## <a name="prerequisites"></a>Wymagania wstępne

1. [Pobierz i zainstaluj narzędzie Git](https://git-scm.com/)
2. [Pobierz i zainstaluj pakiet Maven 3](https://maven.apache.org/install.html)
3. [Pobierz i zainstaluj interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Klonowanie i edytowanie aplikacji przykładowej

W tym kroku sklonujemy aplikację przykładową i skonfigurujemy wdrożenie modelu Project Object Model (pliku POM lub pom.xml) narzędzia Maven.

### <a name="clone-the-sample"></a>Klonowanie przykładu

W oknie terminala przejdź do katalogu roboczego i sklonuj [przykładowe repozytorium](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Aktualizowanie modelu POM narzędzia Maven

Zaktualizuj model POM narzędzia Maven, podając odpowiednią nazwę i grupę zasobów usługi App Service. Wartości te zostaną wprowadzone do wtyczki platformy Azure, która znajduje się w dalszej części pliku _pom.xml_. Nie trzeba wcześniej tworzyć planu ani wystąpienia usługi App Service. Wtyczka Maven utworzy grupę zasobów i wystąpienie usługi App Service, jeśli jeszcze go nie ma.

Jeśli chcesz sprawdzić wtyczkę platformy Azure, przewiń zawartość pliku _pom.xml_ do sekcji `<plugins>`. Sekcja konfiguracji `<plugin>` w pliku _pom.xml_ dla elementu azure-webapp-maven-plugin powinna obejmować następującą konfigurację:

```xml
      <!--*************************************************-->
      <!-- Deploy to WildFly in App Service Linux           -->
      <!--*************************************************-->
 
      <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.0</version>
        <configuration>
 
          <!-- Web App information -->
          <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
          <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
          <appName>${WEBAPP_NAME}</appName>
          <region>${REGION}</region>
 
          <!-- Java Runtime Stack for Web App on Linux-->
          <linuxRuntime>wildfly 14-jre8</linuxRuntime>
 
        </configuration>
      </plugin>
```

Zamień symbole zastępcze na odpowiednie nazwy zasobów:
```xml
<azure.plugin.appname>YOUR_APP_NAME</azure.plugin.appname>
<azure.plugin.resourcegroup>YOUR_RESOURCE_GROUP</azure.plugin.resourcegroup>
```


## <a name="build-and-deploy-the-application"></a>Kompilowanie i wdrażanie aplikacji

Teraz skompilujemy naszą aplikację i wdrożymy ją w usłudze App Service za pomocą narzędzia Maven.

### <a name="build-the-war-file"></a>Tworzenie pliku war

Konfiguracja modelu POM w tym projekcie obejmuje utworzenie pakietu aplikacji w pliku archiwum internetowego (WAR). Kompilowanie aplikacji przy użyciu narzędzia Maven:

```bash
mvn clean install -DskipTests
```

Przypadki testowe w tej aplikacji są przeznaczone do uruchamiania po wdrożeniu aplikacji na serwerze WildFly. Pominiemy lokalne tworzenie testów. Zostaną one uruchomione po wdrożeniu aplikacji w usłudze App Service.

### <a name="deploy-to-app-service"></a>Wdrażanie do usługi App Service

Gdy już mamy plik WAR, możemy wdrożyć aplikację w usłudze App Service za pomocą wtyczki platformy Azure:

```bash
mvn azure-webapp:deploy
```

Po zakończeniu wdrażania przejdź do następnego kroku.

### <a name="create-a-record"></a>Tworzenie rekordu

Otwórz przeglądarkę i przejdź pod adres `https://<your_app_name>.azurewebsites.net/`. Gratulacje! Udało Ci się wdrożyć aplikację języka Java EE w usłudze Azure App Service.

Aktualnie aplikacja korzysta z bazy danych H2 w pamięci. Aby utworzyć nową kategorię, kliknij pozycję „admin” (Administrator) na pasku nawigacyjnym. Ponowne uruchomienie wystąpienia usługi App Service spowoduje utratę rekordu w bazie danych w pamięci. W poniższych krokach rozwiążemy ten problem, aprowizując bazę danych Postgres na platformie Azure i konfigurując serwer WildFly pod kątem używania tej bazy danych.

![Lokalizacja przycisku administratora](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Aprowizowanie bazy danych Postgres

Aby zaaprowizować serwer bazy danych Postgres, otwórz terminal i uruchom następujące polecenie, podając odpowiednie wartości: nazwę serwera, nazwę użytkownika, hasło i lokalizację. Użyj grupy zasobów, w której znajduje się wystąpienie usługi App Service. Zachowaj hasło — będzie potrzebne później.

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

Przejdź do portalu i poszukaj bazy danych Postgres. Gdy pojawi się blok, skopiuj wartości „Nazwa serwera” i „Nazwa logowania administratora serwera”. Zostaną one użyte później.

### <a name="allow-access-to-azure-services"></a>Zezwalanie na dostęp do usług platformy Azure

W panelu **Zabezpieczenia połączeń** bloku Baza danych platformy Azure ustaw przełącznik „Zezwalaj na dostęp do usług platformy Azure” w pozycji **WŁ**.

![Zezwalanie na dostęp do usług platformy Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Zaktualizuj aplikację języka Java dla bazy danych Postgres

W aplikacji języka Java wprowadzimy pewne zmiany, które umożliwią używanie naszej bazy danych Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Dodawanie poświadczeń bazy danych Postgres do modelu POM

W pliku _pom.xml_ zamień symbole zastępcze na wartości nazwy serwera bazy danych Postgres, nazwy logowania administratora i hasła. Wartości te zostaną wprowadzone jako zmienne środowiskowe w wystąpieniu usługi App Service podczas ponownego wdrażania aplikacji.

```xml
<azure.plugin.postgres-server-name>SERVER_NAME</azure.plugin.postgres-server-name>
<azure.plugin.postgres-username>USERNAME@FIRST_PART_OF_SERVER_NAME</azure.plugin.postgres-username>
<azure.plugin.postgres-password>PASSWORD</azure.plugin.postgres-password>
```

### <a name="update-the-java-transaction-api"></a>Aktualizowanie interfejsu API transakcji języka Java

Następny krok obejmuje modyfikację konfiguracji interfejsu API transakcji języka Java (JPA). Chcemy, aby nasza aplikacja języka Java komunikowała się z bazą danych Postgres, a nie używaną wcześniej bazą danych H2 w pamięci. W edytorze otwórz plik _src/main/resources/META-INF/persistence.xml_. Zastąp wartość elementu `<jta-data-source>` cięgiem `java:jboss/datasources/postgresDS`. Dane XML JTA powinny teraz mieć następujące ustawienie:

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>Konfigurowanie serwera aplikacji WildFly

Przed wdrożeniem ponownie skonfigurowanej aplikacji musimy zaktualizować serwer aplikacji WildFly o moduł Postgres i jego zależności. Do skonfigurowania serwera potrzebne są cztery pliki z katalogu `wildfly_config/`:

- **postgresql-42.2.5.jar**: ten plik JAR jest sterownikiem JDBC dla bazy danych Postgres. Więcej informacji można znaleźć na [oficjalnej witrynie](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml**: ten plik XML służy do deklarowania nazwy modułu Postgres (org.postgres). Umożliwia on również wskazanie zasobów i zależności niezbędnych do korzystania z tego modułu.
- **jboss_cli_commands.cl**: ten plik zawiera polecenia konfiguracji, które zostaną wykonane w interfejsie wiersza polecenia oprogramowania JBoss. Polecenia te pozwalają na przykład dodać moduł Postgres do serwera aplikacji WildFly, podać poświadczenia, zadeklarować nazwę JNDI i ustawić próg limitu czasu. Jeśli nie znasz dobrze interfejsu wiersza polecenia oprogramowania JBoss, zapoznaj się z [oficjalną dokumentacją](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh**: ten skrypt powłoki jest wykonywany przy każdym uruchomieniu wystąpienia usługi App Service. Jego jedyna funkcja polega na przekazywaniu w potoku poleceń zawartych w pliku `jboss_cli_commands.cli` do interfejsu wiersza polecenia oprogramowania JBoss.

Zdecydowanie zalecamy zapoznanie się z zawartością tych plików, zwłaszcza pliku _jboss_cli_commands.cli_.

### <a name="ftp-the-configuration-files"></a>Przesyłanie plików konfiguracji za pośrednictwem połączenia FTP

Za pośrednictwem połączenia FTP musimy przesłać zawartość katalogu `wildfly_config/` do wystąpienia usługi App Service. Aby uzyskać poświadczenia protokołu FTP, kliknij przycisk **Pobierz profil publikowania** widoczny w bloku usługi App Service w witrynie Azure Portal. Nazwa użytkownika i hasło protokołu FTP znajdują się w pobranym dokumencie XML. Więcej informacji na temat profilu publikowania można znaleźć w [tym dokumencie](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Za pomocą dowolnego narzędzia FTP prześlij cztery pliki z katalogu `wildfly_config/` do lokalizacji `/home/site/deployments/tools/`. Pamiętaj o tym, że należy przesłać same pliki, a nie katalog.


### <a name="finalize-app-service"></a>Finalizowanie usługi App Service

W bloku usługi App Service przejdź do panelu „Ustawienia aplikacji”. W obszarze „Środowisko uruchomieniowe” ustaw pole „Plik startowy” na wartość `/home/site/deployments/tools/startup_script.sh`. Gwarantuje to uruchomienie skryptu powłoki po utworzeniu wystąpienia usługi App Service, ale przed uruchomieniem serwera WildFly.

Na koniec uruchom ponownie usługę App Service. Przycisk znajduje się w panelu „Przegląd”.

## <a name="redeploy-the-application"></a>Ponowne wdrażanie aplikacji

W oknie terminalu ponownie skompiluj i wdróż aplikację.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Gratulacje! Twoja aplikacja używa teraz bazy danych Postgres. Wszystkie rekordy utworzone w aplikacji będą przechowywane w bazie danych Postgres, a nie w poprzednio używanej bazie danych H3 w pamięci. Aby to sprawdzić, możesz utworzyć rekord i ponownie uruchomić usługę App Service. Rekordy nadal będą dostępne po ponownym uruchomieniu aplikacji.

## <a name="clean-up"></a>Czyszczenie

Jeśli nie potrzebujesz tych zasobów w innym samouczku (zobacz Następne kroki), możesz je usunąć, uruchamiając następujące polecenie:

```bash
az group delete --name <your_resource_group> 
```

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu aplikacji języka Java EE w usłudze App Service możesz zapoznać się z [przewodnikiem dla deweloperów języka Java Enterprise](https://aka.ms/wildfly-quickstart), który zawiera więcej informacji na temat konfigurowania usług, rozwiązywania problemów i skalowania aplikacji.
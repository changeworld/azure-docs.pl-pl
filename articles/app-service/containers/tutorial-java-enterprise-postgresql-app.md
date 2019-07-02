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
ms.openlocfilehash: dcd1ef5c54885b758ac9a301616d79a163999bc9
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509638"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Samouczek: Tworzenie aplikacji internetowej języka Java EE korzystającej z bazy danych Postgres na platformie Azure

Ten samouczek pokazuje, jak utworzyć aplikację internetową Java Enterprise Edition (EE) w usłudze Azure App Service i połączyć ją z bazą danych Postgres. Po ukończeniu tego samouczka będziesz mieć działającą w usłudze [Azure App Service dla systemu Linux](app-service-linux-intro.md) aplikację [WildFly](https://www.wildfly.org/about/), która umożliwia przechowywanie danych w usłudze [Azure Database for Postgres](https://azure.microsoft.com/services/postgresql/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
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

W tym kroku zostanie sklonuj przykładową aplikację i skonfigurować Model obiektu projektu Maven (POM lub *pom.xml*) do wdrożenia.

### <a name="clone-the-sample"></a>Klonowanie przykładu

W oknie terminala przejdź do katalogu roboczego i sklonuj [przykładowe repozytorium](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Aktualizowanie modelu POM narzędzia Maven

Zaktualizuj wtyczkę Maven platformy Azure, podając odpowiednią nazwę i grupę zasobów usługi App Service. Nie trzeba wcześniej tworzyć planu ani wystąpienia usługi App Service. Wtyczka Maven utworzy grupę zasobów i wystąpienie usługi App Service, jeśli jeszcze go nie ma.

Aby wprowadzić zmiany, przewiń zawartość pliku *pom.xml* do sekcji `<plugins>` (wiersz 200).

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

Zamień wartości `YOUR_APP_NAME` i `YOUR_RESOURCE_GROUP` na nazwy używanej usługi App Service i grupy zasobów.

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

Otwórz przeglądarkę i przejdź do `https://<your_app_name>.azurewebsites.net/`. Gratulacje! Udało Ci się wdrożyć aplikację języka Java EE w usłudze Azure App Service.

Aktualnie aplikacja korzysta z bazy danych H2 w pamięci. Aby utworzyć nową kategorię, kliknij pozycję „admin” (Administrator) na pasku nawigacyjnym. Ponowne uruchomienie wystąpienia usługi App Service spowoduje utratę rekordu w bazie danych w pamięci. W poniższych krokach rozwiążemy ten problem, aprowizując bazę danych Postgres na platformie Azure i konfigurując serwer WildFly pod kątem używania tej bazy danych.

![Lokalizacja przycisku administratora](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Aprowizowanie bazy danych Postgres

Aby zainicjować obsługę serwera bazy danych Postgres, otwórz terminal i użyj [az postgres server utworzyć](https://docs.microsoft.com/cli/azure/postgres/server) polecenia, jak pokazano w poniższym przykładzie. Zastąp symbole zastępcze (włącznie z nawiasami) wybranej wartości, przy użyciu tego samego zasobu grupy, które wcześniej podane dla swojego wystąpienia usługi App Service. Poświadczenia administratora, podaj będzie włączyć dostęp w przyszłości, więc należy koniecznie Zapisz je w celu późniejszego użycia.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

Po uruchomieniu tego polecenia, przejdź do witryny Azure portal, a następnie przejdź do bazy danych Postgres. Gdy pojawi się blok, skopiuj wartości „Nazwa serwera” i „Nazwa logowania administratora serwera”. Zostaną one użyte później.

### <a name="allow-access-to-azure-services"></a>Zezwalanie na dostęp do usług platformy Azure

W panelu **Zabezpieczenia połączeń** bloku Baza danych platformy Azure ustaw przełącznik „Zezwalaj na dostęp do usług platformy Azure” w pozycji **WŁ**.

![Zezwalanie na dostęp do usług platformy Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Zaktualizuj aplikację języka Java dla bazy danych Postgres

W aplikacji języka Java wprowadzimy pewne zmiany, które umożliwią używanie naszej bazy danych Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Dodawanie poświadczeń bazy danych Postgres do modelu POM

W pliku *pom.xml* zamień napisane wielkimi literami symbole zastępcze na wartości nazwy serwera bazy danych Postgres, nazwy logowania administratora i hasła. Te pola znajdują się we wtyczce Maven platformy Azure. (Uważaj, aby zastąpić wartości `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME` i `YOUR_PG_PASSWORD` w tagach `<value>`, a nie w tagach `<name>`).

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Aktualizowanie interfejsu API transakcji języka Java

Następny krok obejmuje modyfikację konfiguracji interfejsu API transakcji języka Java (JPA). Chcemy, aby nasza aplikacja języka Java komunikowała się z bazą danych Postgres, a nie używaną wcześniej bazą danych H2 w pamięci. W edytorze otwórz plik *src/main/resources/META-INF/persistence.xml*. Zastąp wartość elementu `<jta-data-source>` cięgiem `java:jboss/datasources/postgresDS`. Dane XML JTA powinny teraz mieć następujące ustawienie:

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>Konfigurowanie serwera aplikacji WildFly

Przed wdrożeniem ponownie skonfigurowanej aplikacji musimy zaktualizować serwer aplikacji WildFly o moduł Postgres i jego zależności. Więcej informacji o konfiguracji znajduje się w temacie [server skonfigurować WildFly](configure-language-java.md#configure-java-ee-wildfly).

Aby skonfigurować serwer, musimy cztery pliki w *wildfly_config /* katalogu:

- **postgresql-42.2.5.jar**: ten plik JAR jest sterownikiem JDBC dla bazy danych Postgres. Więcej informacji można znaleźć na [oficjalnej witrynie](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml**: ten plik XML służy do deklarowania nazwy modułu Postgres (org.postgres). Umożliwia on również wskazanie zasobów i zależności niezbędnych do korzystania z tego modułu.
- **jboss_cli_commands.cl**: ten plik zawiera polecenia konfiguracji, które zostaną wykonane w interfejsie wiersza polecenia oprogramowania JBoss. Polecenia te pozwalają na przykład dodać moduł Postgres do serwera aplikacji WildFly, podać poświadczenia, zadeklarować nazwę JNDI i ustawić próg limitu czasu. Jeśli nie znasz dobrze interfejsu wiersza polecenia oprogramowania JBoss, zapoznaj się z [oficjalną dokumentacją](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh**: ten skrypt powłoki jest wykonywany przy każdym uruchomieniu wystąpienia usługi App Service. Skrypt wykonuje tylko jedną funkcję: przekazanie w potoku poleceń w *jboss_cli_commands.cli* JBoss interfejsu wiersza polecenia.

Zdecydowanie zalecamy zapoznanie się z zawartością tych plików, zwłaszcza pliku *jboss_cli_commands.cli*.

### <a name="ftp-the-configuration-files"></a>Przesyłanie plików konfiguracji za pośrednictwem połączenia FTP

Firma Microsoft będzie należy FTP zawartość *wildfly_config /* do naszych wystąpienia usługi App Service. Aby uzyskać poświadczenia protokołu FTP, kliknij przycisk **Pobierz profil publikowania** widoczny w bloku usługi App Service w witrynie Azure Portal. Nazwa użytkownika i hasło protokołu FTP znajdują się w pobranym dokumencie XML. Więcej informacji na temat profilu publikowania można znaleźć w [tym dokumencie](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Za pomocą narzędzia FTP, co pozwala przenieść cztery pliki w *wildfly_config /* do */home/site/wdrożeń/tools/* . Pamiętaj o tym, że należy przesłać same pliki, a nie katalog.

### <a name="finalize-app-service"></a>Finalizowanie usługi App Service

W bloku usługi App Service przejdź do panelu „Ustawienia aplikacji”. W obszarze "Środowiska uruchomieniowego" Ustaw pole "Pliku startowego" */home/site/deployments/tools/startup_script.sh*. Gwarantuje to uruchomienie skryptu powłoki po utworzeniu wystąpienia usługi App Service, ale przed uruchomieniem serwera WildFly.

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
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie aplikacji języka Java EE na platformie Azure przy użyciu narzędzia Maven
> * Tworzenie bazy danych Postgres na platformie Azure
> * Konfigurowanie serwera WildFly pod kątem korzystania z bazy danych Postgres
> * Aktualizowanie i ponowne wdrażanie aplikacji
> * Uruchamianie testów jednostkowych na serwerze WildFly

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: Mapowanie niestandardowej nazwy DNS na aplikację](../app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Java](configure-language-java.md)

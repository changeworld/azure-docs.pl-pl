---
title: Przewodnik dla deweloperów języka Java dla usługi App Service w systemie Linux — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować aplikacje w języku Java uruchomiona w usłudze Azure App Service w systemie Linux.
keywords: Usługa Azure app service, aplikacji sieci web, linux, oss, języka java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: bab6510af98b153ecb61db8fc49b5124aae04598
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500468"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Przewodnik dla deweloperów języka Java dla usługi App Service w systemie Linux

Usługa Azure App Service w systemie Linux umożliwia deweloperom szybkie tworzenie, wdrażanie i skalowanie ich Tomcat języka Java lub Java Standard Edition (SE) w pakiecie aplikacji sieci web w pełni zarządzanej usługi opartej na systemie Linux. Wdrażanie aplikacji przy użyciu wtyczki usługi Maven w wierszu polecenia lub w edytorach, takich jak IntelliJ, Eclipse lub Visual Studio Code.

Ten przewodnik zawiera podstawowe pojęcia i instrukcje dla deweloperów języka Java przy użyciu w usłudze App Service dla systemu Linux. Jeśli nie znasz usługi Azure App Service dla systemu Linux, należy przeczytać za pośrednictwem [Szybki Start Java](quickstart-java.md) pierwszy. W odpowiedzi są ogólne pytania dotyczące korzystania z usługi App Service dla systemu Linux, które nie są specyficzne dla programowania Java [App Service Linux — często zadawane pytania](app-service-linux-faq.md).

## <a name="deploying-your-app"></a>Wdrażanie aplikacji

Aby wdrożyć pliki JAR i WAR, można użyć wtyczki Maven. Zobacz [tej dokumentacji](https://docs.microsoft.com/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme?view=azure-java-stable) więcej informacji na temat wtyczki Maven.

Jeśli nie używasz narzędzia Maven, metody wdrażania będzie zależeć od typu archiwum:

- Aby wdrożyć pliki WAR Tomcat, użyj `/api/wardeploy/` punktu końcowego można OPUBLIKOWAĆ pliku archiwum. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tej dokumentacji](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Aby wdrożyć pliki JAR w obrazach Java SE, użyj `/api/zipdeploy/` witryny Kudu punktu końcowego. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tej dokumentacji](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Nie należy wdrażać swoje WAR lub JAR przy użyciu protokołu FTP. Narzędzie FTP jest przeznaczony do przekazania skrypty uruchamiania, zależności lub innych plików środowiska uruchomieniowego. Nie jest optymalnym wyborem do wdrażania aplikacji sieci web.

## <a name="logging-and-debugging-apps"></a>Rejestrowanie i debugowanie aplikacji

Raporty dotyczące wydajności, wizualizacji ruchu i kontrole kondycji są dostępne dla każdej aplikacji za pośrednictwem witryny Azure portal. Zobacz [Omówienie diagnostyki usługi Azure App Service](/azure/app-service/overview-diagnostics) Aby uzyskać więcej informacji na temat uzyskać dostęp do tych narzędzi diagnostycznych.

## <a name="application-performance-monitoring"></a>Monitorowanie wydajności aplikacji

Zobacz [narzędzia z aplikacjami Java w usłudze Azure App Service w systemie Linux do monitorowania wydajności aplikacji](how-to-java-apm-monitoring.md) porad instrukcje dotyczące konfigurowania usługi New Relic i AppDynamics przy użyciu aplikacji języka Java uruchomiona w usłudze App Service w systemie Linux.

### <a name="ssh-console-access"></a>Dostęp do konsoli SSH

Połączenie SSH do środowiska systemu Linux, z tą aplikacją, jest dostępna. Zobacz [Obsługa protokołu SSH dla usługi Azure App Service w systemie Linux](/azure/app-service/containers/app-service-linux-ssh-support) pełne instrukcje połączyć się z systemem Linux przez przeglądarkę sieci web lub terminalu lokalnym.

### <a name="streaming-logs"></a>Przesyłanie strumieniowe dzienników

Szybkie debugowania i rozwiązywania problemów, można przesyłać strumieniowo dzienniki do konsoli przy użyciu wiersza polecenia platformy Azure. Konfigurowanie interfejsu wiersza polecenia przy użyciu `az webapp log config` włączania rejestrowania:

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Następnie przesyłanie strumieniowe dzienników do konsoli przy użyciu `az webapp log tail`:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Aby uzyskać więcej informacji, zobacz [przesyłania strumieniowego dzienników za pomocą wiersza polecenia platformy Azure](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Rejestrowanie aplikacji

Włącz [rejestrowanie aplikacji](/azure/app-service/troubleshoot-diagnostic-logs#enablediag) za pośrednictwem witryny Azure portal lub [wiersza polecenia platformy Azure](/cli/azure/webapp/log#az-webapp-log-config) do skonfigurowania usługi App Service do zapisu wyjście konsoli standardowej i strumieni błąd konsoli standardowej aplikacji lokalnej System plików lub usługi Azure Blob Storage. Rejestrowanie w lokalnym systemie plików usługi App Service wystąpienie jest wyłączone na 12 godzin, po skonfigurowaniu go. Dłuższy okres przechowywania, należy skonfigurować aplikację, aby zapisywać dane wyjściowe do kontenera magazynu obiektów Blob. Można znaleźć w dziennikach aplikacji Java i Tomcat `/home/LogFiles/Application/` katalogu.

Jeśli aplikacja używa [Logback](https://logback.qos.ch/) lub [Log4j](https://logging.apache.org/log4j) śledzenia, możesz przekazywać te dane śledzenia do przeglądu w usłudze Azure Application Insights zgodnie z instrukcjami konfiguracji struktury rejestrowania [Dzienniki śledzenia zapoznaj się z języka Java w usłudze Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Narzędzia do rozwiązywania problemów

Wbudowane obrazy Java opierają się na [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) systemu operacyjnego. Użyj `apk` package manager w celu zainstalowania dowolnej Rozwiązywanie problemów z narzędzia i polecenia.

## <a name="customization-and-tuning"></a>Dostosowywanie i dostrajanie

Usługa Azure App Service dla systemu Linux obsługuje poza pole dostrajanie i dostosowywanie przy użyciu witryny Azure Portal oraz interfejsu wiersza polecenia. Sprawdź następujące artykuły dotyczące konfiguracji aplikacji sieci web-Java:

- [Konfigurowanie ustawień usługi App Service](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurowanie domeny niestandardowej](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Włącz protokół SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Dodawanie usługi CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurowanie witryny Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Ustaw opcje środowiska uruchomieniowego języka Java

Aby ustawić ilość przydzielonej pamięci lub inne opcje środowiska uruchomieniowego maszyny JVM w środowiskach Java SE i Tomcat, utworzyć [ustawienie aplikacji](/azure/app-service/web-sites-configure#app-settings) o nazwie `JAVA_OPTS` z opcjami. Usługa App Service w systemie Linux przekazuje to ustawienie jako zmienną środowiskową środowisko wykonawcze języka Java podczas jego uruchamiania.

W witrynie Azure portal w obszarze **ustawienia aplikacji** dla aplikacji sieci web, należy utworzyć nowe ustawienie aplikacji o nazwie `JAVA_OPTS` zawierającej dodatkowe ustawienia, takie jak `-Xms512m -Xmx1204m`.

Aby skonfigurować ustawienia aplikacji z wtyczki Maven, Dodaj ustawienia i wartości tagów w sekcji wtyczka platformy Azure. W poniższym przykładzie ustawiono określonej minimalnej i maksymalnej heapsize Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Deweloperzy uruchomił jedną aplikację z gniazdem jedno wdrożenie w planu usługi App Service można użyć następujących opcji:

- Wystąpienia B1 i S1: `-Xms1024m -Xmx1024m`
- Wystąpienia B2 i S2: `-Xms3072m -Xmx3072m`
- Wystąpienia B3 i S3: `-Xms6144m -Xmx6144m`

Podczas dostosowywania ustawienia sterty aplikacji, przejrzyj szczegóły planu usługi App Service i wziąć pod uwagę wiele aplikacji i miejsca wdrożenia musi znaleźć optymalne alokacji pamięci.

Jeśli wdrażasz aplikację JAR, powinien zostać nazwany `app.jar` tak, aby wbudowanym obrazem można poprawnie identyfikacji danej aplikacji. (Wtyczki Maven nie, ta zmiana nazw automatycznie.) Jeśli nie chcesz zmienić nazwę Twojego pliku JAR do `app.jar`, możesz przesłać skrypt powłoki, za pomocą polecenia do uruchomienia usługi JAR. Następnie wklej pełną ścieżkę do tego skryptu w [plik startowy](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-faq#startup-file) pole tekstowe w sekcji konfiguracji portalu.

### <a name="turn-on-web-sockets"></a>Włącz gniazda sieci web

Włączanie obsługi gniazda sieci web w witrynie Azure portal w **ustawienia aplikacji** dla aplikacji. Należy ponownie uruchomić aplikację, aby ustawienia zaczęły obowiązywać.

Włączenie obsługi gniazda sieci web przy użyciu wiersza polecenia platformy Azure za pomocą następującego polecenia:

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true
```

Następnie ponownie uruchom aplikację:

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
```

### <a name="set-default-character-encoding"></a>Ustaw domyślne kodowanie znaków

W witrynie Azure portal w obszarze **ustawienia aplikacji** dla aplikacji sieci web, należy utworzyć nowe ustawienie aplikacji o nazwie `JAVA_OPTS` wartością `-Dfile.encoding=UTF-8`.

Alternatywnie można skonfigurować ustawienia aplikacji przy użyciu wtyczki Maven usługi aplikacji. Dodaj tagi nazwę i wartość ustawienia konfiguracji wtyczki:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Dostosuj limit czasu uruchamiania

Jeśli aplikacja języka Java jest szczególnie duże, należy zwiększyć limit czasu uruchamiania. Aby to zrobić, należy utworzyć ustawienie aplikacji, `WEBSITES_CONTAINER_START_TIME_LIMIT` i ustaw ją na liczbę sekund oczekiwania przed przekroczeniem limitu czasu usługi App Service. Wartość maksymalna to `1800` sekund.

## <a name="secure-applications"></a>Zabezpiecz aplikacje

Aplikacje Java uruchomiona w usłudze App Service dla systemu Linux ma ten sam zestaw [najlepszych rozwiązań dotyczących zabezpieczeń](/azure/security/security-paas-applications-using-app-services) jako inne aplikacje.

### <a name="authenticate-users"></a>Uwierzytelnianie użytkowników

Konfigurowanie uwierzytelniania aplikacji w witrynie Azure Portal za pomocą **uwierzytelnianie i autoryzacja** opcji. Z tego miejsca można włączyć uwierzytelnianie przy użyciu usługi Azure Active Directory lub społecznościowych nazw logowania, takich jak Facebook, Google lub GitHub. Konfiguracja portalu Azure działa tylko podczas konfigurowania dostawcy jednorazowego uwierzytelniania.  Aby uzyskać więcej informacji, zobacz [skonfiguruj aplikację usługi App Service, aby używała logowania do usługi Azure Active Directory](/azure/app-service/configure-authentication-provider-aad) pokrewne artykuły dotyczące innych dostawców tożsamości.

Jeśli musisz włączyć wielu dostawców logowania, postępuj zgodnie z instrukcjami [Dostosowywanie uwierzytelniania usługi App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) artykułu.

Spring rozruchu deweloperzy mogą używać [usługi Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) do zabezpieczania aplikacji za pomocą dobrze znanych adnotacje zabezpieczeń Spring i interfejsów API. Pamiętaj zwiększyć rozmiar maksymalny nagłówka w swojej `application.properties` pliku. Zalecamy, aby wartość `16384`.

### <a name="configure-tlsssl"></a>Konfigurowanie protokołów TLS/SSL

Postępuj zgodnie z instrukcjami w [wiązanie istniejącego niestandardowego certyfikatu SSL](/azure/app-service/app-service-web-tutorial-custom-ssl) Prześlij istniejący certyfikat protokołu SSL i powiązać nazwy domeny Twojej aplikacji. Domyślnie aplikacja będzie nadal zezwalania HTTP połączeń — wykonaj konkretne kroki w tym samouczku, aby wymusić SSL i TLS.

### <a name="use-keyvault-references"></a>Używać odwołań do magazynu kluczy

[Usługa Azure KeyVault](../../key-vault/key-vault-overview.md) umożliwia scentralizowane zarządzanie wpisu tajnego z historią zasad i inspekcja dostępu. Można przechowywać klucze tajne (takie jak hasła lub parametry połączenia) w magazynie KeyVault i uzyskiwanie dostępu do tych kluczy tajnych w aplikacji za pomocą zmiennych środowiskowych.

Po pierwsze, postępuj zgodnie z instrukcjami dotyczącymi [przyznania aplikacji dostępu do usługi Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) i [odwołanie magazynu kluczy do klucz tajny w ustawieniu aplikacji](../app-service-key-vault-references.md#reference-syntax). Aby zweryfikować, czy odwołanie jest rozpoznawana jako klucz tajny, drukowanie zmiennej środowiskowej, uzyskując dostęp do zdalnego terminalu usługi App Service.

Aby wstawić tych kluczy tajnych w pliku konfiguracji platformy Spring lub Tomcat, należy użyć składni iniekcji zmiennej środowiska (`${MY_ENV_VAR}`). Pliki konfiguracji środowiska Spring, można znaleźć tej dokumentacji na [zewnętrznych konfiguracje](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="data-sources"></a>Źródła danych

### <a name="tomcat"></a>Tomcat

Te instrukcje mają zastosowanie do wszystkich połączeń z bazą danych. Należy wypełnić symbole zastępcze nazwą klasy sterownik wybranej bazy danych i pliku JAR. Podany jest tabelą z nazwy klas i sterowniki do pobrania dla wspólnej bazy danych.

| Database (Baza danych)   | Nazwa klasy sterownika                             | Sterownik JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Do pobrania](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Pobierz](https://dev.mysql.com/downloads/connector/j/) (Wybierz "Niezależne od platformy") |
| Oprogramowanie SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Do pobrania](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Aby skonfigurować Tomcat, aby użyć połączenia bazy danych języka Java (JDBC) lub interfejsu API trwałości Java rozwiązaniami (JPA), należy najpierw dostosować `CATALINA_OPTS` zmiennej środowiskowej, który jest wczytywany w Tomcat na początku się. Ustaw te wartości za pomocą ustawienia aplikacji w [wtyczki Maven usługi aplikacji](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>  
    <property>  
        <name>CATALINA_OPTS</name>  
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>  
    </property>  
</appSettings>  
```

Lub ustawić zmienne środowiskowe w bloku "Ustawienia aplikacji" w witrynie Azure portal.

Następnie należy określić, czy źródło danych powinna być dostępna, do jednej aplikacji lub wszystkie aplikacje uruchomione na serwerze Tomcat serwletu.

#### <a name="application-level-data-sources"></a>Źródła danych na poziomie aplikacji

1. Tworzenie `context.xml` w pliku `META-INF/` katalogu projektu. Utwórz `META-INF/` katalogu, jeśli nie istnieje.

2. W `context.xml`, Dodaj `Context` element połączenia źródła danych na adres JNDI. Zastąp `driverClassName` zastępczego dla nazwy klasy w sterowniku z powyższej tabeli.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Aktualizowanie aplikacji `web.xml` używać źródła danych w aplikacji.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Udostępnione zasoby na poziomie serwera

1. Skopiuj zawartość `/usr/local/tomcat/conf` do `/home/tomcat/conf` na usługi App Service Linux wystąpienia przy użyciu protokołu SSH, jeśli jeszcze nie masz konfiguracji istnieje.

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Dodaj element kontekstu w swojej `server.xml` w ramach `<Server>` elementu.

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. Aktualizowanie aplikacji `web.xml` używać źródła danych w aplikacji.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>Na koniec umieść plikach JAR sterownika w ścieżce klasy Tomcat i uruchom ponownie usługi App Service

1. Upewnij się, że pliki sterownika JDBC są dostępne dla Tomcat classloader, umieszczając je w `/home/tomcat/lib` katalogu. (Utwórz ten katalog, jeśli jeszcze nie istnieje). Aby przekazać te pliki do swojego wystąpienia usługi App Service, wykonaj następujące czynności:  
   1. Zainstaluj rozszerzenie webpp w usłudze Azure App Service:

      ```azurecli-interactive
      az extension add –name webapp
      ```

   1. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do usługi App Service:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

   1. Podłącz do lokalnego portu tunelowania przy użyciu klienta protokołu SFTP oraz przekazywanie plików do `/home/tomcat/lib` folderu.

      Alternatywnie można użyć klienta FTP do przekazania sterownik JDBC. Postępuj zgodnie z tymi [instrukcje dotyczące pobierania poświadczeń protokołu FTP](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

2. Jeśli utworzono źródła danych na poziomie serwera, uruchom ponownie aplikację App Service dla systemu Linux. Spowoduje to zresetowanie Tomcat `CATALINA_HOME` do `/home/tomcat/conf` i użyć zaktualizowanej konfiguracji.

### <a name="spring-boot"></a>Spring Boot

Aby połączyć się ze źródłami danych w aplikacji platformy Spring Boot, zalecamy tworzenie parametrów połączenia i wprowadzanie ich na swojej `application.properties` pliku.

1. W sekcji "Ustawienia aplikacji" w bloku usługi App Service Ustaw nazwę ciągu, Wklej parametry połączenia sterownika JDBC w polu wartość i ustawienie typu na "Niestandardowe". Opcjonalnie możesz ustawić te parametry połączenia jako ustawienie miejsca.

    ![Tworzenie parametrów połączenia w portalu.][1]

    Ten ciąg połączenia jest dostępny do naszej aplikacji jako zmienną środowiskową o nazwie `CUSTOMCONNSTR_<your-string-name>`. Na przykład, będzie miała parametry połączenia, utworzone powyżej `CUSTOMCONNSTR_exampledb`.

2. W swojej `application.properties` plików, odwołać ten ciąg ustawienia z nazwa zmiennej środowiskowej. W tym przykładzie należy użyć następujących czynności.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Zobacz [dokumentacji platformy Spring Boot na dostęp do danych](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html
) i [zewnętrznych konfiguracje](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) Aby uzyskać więcej informacji na ten temat.

## <a name="docker-containers"></a>Kontenerów Docker

Aby użyć zestawu JDK Zulu obsługiwanej przez platformę Azure w swoje kontenery, upewnij się, że ściągać i używać wstępnie utworzone obrazy, zgodnie z opisem z [obsługiwane Azul Zulu Enterprise dla strony pobierania Azure](https://www.azul.com/downloads/azure-only/zulu/) lub użyj `Dockerfile` przykłady z [Repozytorium usługi GitHub firmy Microsoft Java](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Dostępność w czasie wykonywania i oświadczenie zespołu pomocy technicznej

App Service dla systemu Linux obsługuje dwa środowiska uruchomieniowe zarządzany hosting aplikacji sieci web Java:

- [Kontener serwletów Tomcat](https://tomcat.apache.org/) do uruchamiania aplikacji w pakiecie, tak jak w sieci web plików archiwów (WAR). Obsługiwane wersje to 8.5 i 9.0.
- Środowisko uruchomieniowe Java SE do uruchamiania aplikacji w pakiecie jako archiwum Java plików (plik JAR). Jedyna obsługiwana wersja główne to Java 8.

## <a name="java-runtime-statement-of-support"></a>Java runtime oświadczenie zespołu pomocy technicznej

### <a name="jdk-versions-and-maintenance"></a>Wersje zestawu JDK i konserwacji

Interfejsów platformy Azure obsługiwane Java Development Kit (JDK) jest [Zulu](https://www.azul.com/downloads/azure-only/zulu/) dostępne za pośrednictwem [firmy Azul Systems](https://www.azul.com/).

Aktualizacji wersji głównych będzie świadczona poprzez nowe opcje środowiska uruchomieniowego usługi Azure App Service dla systemu Linux. Klienci aktualizacji tych nowszych wersji języka Java, konfigurując ich wdrożenie usługi App Service i są odpowiedzialne za przeprowadzanie testów i zapewnienie ważna aktualizacja odpowiadają ich potrzebom.

Obsługiwane JDK są automatycznie poprawić kwartalnie w styczniu, kwietniu, lipcu i październiku każdego roku.

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Jak najszybciej po ich udostępnieniu firmy Azul Systems zostaną wydane poprawki i poprawki dla luki w zabezpieczeniach głównych. "Główne" luk w zabezpieczeniach jest zdefiniowanym przez wynik podstawowy 9.0 lub nowszej na [NIST typowe luki w zabezpieczeniach oceniania systemu, wersja 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Ogłoszone jako przestarzałe i wycofania

Jeśli zostanie wycofana obsługiwane środowisko wykonawcze języka Java, deweloperów platformy Azure przy użyciu których to dotyczy środowiska uruchomieniowego otrzymają powiadomienie o wycofaniu co najmniej sześć miesięcy przed środowiska uruchomieniowego została wycofana.

### <a name="local-development"></a>Lokalne programowanie

Deweloperzy mogli przeprowadzić pobieranie produkcji wersji o Azul Zulu Enterprise zestaw JDK potrzeby lokalnego programowania z [witryny pobierania firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Obsługa projektowania

Pomoc techniczna dla [obsługiwanej przez platformę Azure rozwiązanie Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) jest dostępna za pośrednictwem podczas tworzenia na platformie Azure lub [usługi Azure Stack](https://azure.microsoft.com/overview/azure-stack/) z [kwalifikowana planu pomocy technicznej Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Obsługa środowiska uruchomieniowego

Deweloperzy mogą [Otwórz problem](/azure/azure-supportability/how-to-create-azure-support-request) z JDK oprogramowanie Zulu firmy Azul za pośrednictwem pomocy technicznej platformy Azure, jeśli mają one [plan pomocy technicznej kwalifikowaną](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę [platformy Azure dla deweloperów języka Java](/java/azure/) Centrum, aby znaleźć Azure przewodników Szybki Start, samouczki i dokumentacja języka Java.

<!--Image references-->
[1]: ./media/app-service-linux-java/connection-string.png

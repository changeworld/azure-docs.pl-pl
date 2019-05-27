---
title: Konfigurowanie aplikacji Java w systemie Linux — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować aplikacje w języku Java uruchomiona w usłudze Azure App Service w systemie Linux.
keywords: Usługa Azure app service, aplikacji sieci web, systemu linux, oss, java, java ee, jee, javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 3361013d8421cd859c834c07018356318d5e2989
ms.sourcegitcommit: f4469b7bb1f380bf9dddaf14763b24b1b508d57c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66179817"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurowanie aplikacji w języku Java w systemie Linux dla usługi Azure App Service

Usługa Azure App Service w systemie Linux umożliwia deweloperom szybkie tworzenie, wdrażanie i skalowanie ich Tomcat języka Java lub Java Standard Edition (SE) w pakiecie aplikacji sieci web w pełni zarządzanej usługi opartej na systemie Linux. Wdrażanie aplikacji przy użyciu wtyczki usługi Maven w wierszu polecenia lub w edytorach, takich jak IntelliJ, Eclipse lub Visual Studio Code.

Ten przewodnik zawiera podstawowe pojęcia i instrukcje dla deweloperów języka Java, którzy korzystają z wbudowanych kontenera systemu Linux w usłudze App Service. Jeśli nie znasz usługi Azure App Service, postępuj zgodnie z [Szybki Start Java](quickstart-java.md) i [Java z samouczkiem PostgreSQL](tutorial-java-enterprise-postgresql-app.md) pierwszy.

## <a name="deploying-your-app"></a>Wdrażanie aplikacji

Możesz użyć [wtyczka Maven Plugin for Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) wdrożyć pliki JAR i WAR. Wdrożenia z popularnymi środowiskami IDE, jest również obsługiwana za pomocą [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) lub [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

W przeciwnym razie metody wdrażania będzie zależeć od typu archiwum:

- Aby wdrożyć pliki WAR Tomcat, użyj `/api/wardeploy/` punktu końcowego można OPUBLIKOWAĆ pliku archiwum. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tej dokumentacji](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Aby wdrożyć pliki JAR w obrazach Java SE, użyj `/api/zipdeploy/` witryny Kudu punktu końcowego. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tej dokumentacji](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Nie należy wdrażać swoje WAR lub JAR przy użyciu protokołu FTP. Narzędzie FTP jest przeznaczony do przekazania skrypty uruchamiania, zależności lub innych plików środowiska uruchomieniowego. Nie jest optymalnym wyborem do wdrażania aplikacji sieci web.

## <a name="logging-and-debugging-apps"></a>Rejestrowanie i debugowanie aplikacji

Raporty dotyczące wydajności, wizualizacji ruchu i kontrole kondycji są dostępne dla każdej aplikacji za pośrednictwem witryny Azure portal. Aby uzyskać więcej informacji, zobacz [Omówienie diagnostyki usługi Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Dostęp do konsoli SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Aby uzyskać więcej informacji, zobacz [przesyłania strumieniowego dzienników za pomocą wiersza polecenia platformy Azure](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Rejestrowanie aplikacji

Włącz [rejestrowanie aplikacji](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) za pośrednictwem witryny Azure portal lub [wiersza polecenia platformy Azure](/cli/azure/webapp/log#az-webapp-log-config) do skonfigurowania usługi App Service do zapisu wyjście konsoli standardowej i strumieni błąd konsoli standardowej aplikacji lokalnej System plików lub usługi Azure Blob Storage. Rejestrowanie w lokalnym systemie plików usługi App Service wystąpienie jest wyłączone na 12 godzin, po skonfigurowaniu go. Dłuższy okres przechowywania, należy skonfigurować aplikację, aby zapisywać dane wyjściowe do kontenera magazynu obiektów Blob. Można znaleźć w dziennikach aplikacji Java i Tomcat `/home/LogFiles/Application/` katalogu.

Jeśli aplikacja używa [Logback](https://logback.qos.ch/) lub [Log4j](https://logging.apache.org/log4j) śledzenia, możesz przekazywać te dane śledzenia do przeglądu w usłudze Azure Application Insights zgodnie z instrukcjami konfiguracji struktury rejestrowania [Dzienniki śledzenia zapoznaj się z języka Java w usłudze Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Narzędzia do rozwiązywania problemów

Wbudowane obrazy Java opierają się na [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) systemu operacyjnego. Użyj `apk` package manager w celu zainstalowania dowolnej Rozwiązywanie problemów z narzędzia i polecenia.

## <a name="customization-and-tuning"></a>Dostosowywanie i dostrajanie

Usługa Azure App Service dla systemu Linux obsługuje poza pole dostrajanie i dostosowywanie przy użyciu witryny Azure portal i interfejs wiersza polecenia. Sprawdź następujące artykuły dotyczące konfiguracji aplikacji sieci web bez określonego języka Java:

- [Konfigurowanie ustawień aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Konfigurowanie domeny niestandardowej](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Włącz protokół SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Dodawanie usługi CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurowanie witryny Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Ustaw opcje środowiska uruchomieniowego języka Java

Aby ustawić ilość przydzielonej pamięci lub inne opcje środowiska uruchomieniowego maszyny JVM w środowiskach Java SE i Tomcat, utworzyć [ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) o nazwie `JAVA_OPTS` z opcjami. Usługa App Service w systemie Linux przekazuje to ustawienie jako zmienną środowiskową środowisko wykonawcze języka Java podczas jego uruchamiania.

W witrynie Azure portal w obszarze **ustawienia aplikacji** dla aplikacji sieci web, należy utworzyć nowe ustawienie aplikacji o nazwie `JAVA_OPTS` zawierającej dodatkowe ustawienia, takie jak `-Xms512m -Xmx1204m`.

Aby skonfigurować ustawienia aplikacji z wtyczki Maven, Dodaj ustawienia i wartości tagów w sekcji wtyczka platformy Azure. W poniższym przykładzie ustawiono określonych minimalny i maksymalny rozmiar sterty środowiska Java:

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

Jeśli wdrażasz aplikację JAR, powinien zostać nazwany `app.jar` tak, aby wbudowanym obrazem można poprawnie identyfikacji danej aplikacji. (Wtyczki Maven nie, ta zmiana nazw automatycznie.) Jeśli nie chcesz zmienić nazwę Twojego pliku JAR do `app.jar`, możesz przesłać skrypt powłoki, za pomocą polecenia do uruchomienia usługi JAR. Następnie wklej pełną ścieżkę do tego skryptu w [plik startowy](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#startup-file) pole tekstowe w sekcji konfiguracji portalu.

### <a name="turn-on-web-sockets"></a>Włącz gniazda sieci web

Włączanie obsługi gniazda sieci web w witrynie Azure portal w **ustawienia aplikacji** dla aplikacji. Należy ponownie uruchomić aplikację, aby ustawienia zaczęły obowiązywać.

Włączenie obsługi gniazda sieci web przy użyciu wiersza polecenia platformy Azure za pomocą następującego polecenia:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Następnie ponownie uruchom aplikację:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
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

Konfigurowanie uwierzytelniania aplikacji w witrynie Azure portal, za pomocą **uwierzytelnianie i autoryzacja** opcji. Z tego miejsca można włączyć uwierzytelnianie przy użyciu usługi Azure Active Directory lub społecznościowych nazw logowania, takich jak Facebook, Google lub GitHub. Konfiguracja portalu Azure działa tylko podczas konfigurowania dostawcy jednorazowego uwierzytelniania. Aby uzyskać więcej informacji, zobacz [skonfiguruj aplikację usługi App Service, aby używała logowania do usługi Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) pokrewne artykuły dotyczące innych dostawców tożsamości. Jeśli musisz włączyć wielu dostawców logowania, postępuj zgodnie z instrukcjami [Dostosowywanie uwierzytelniania usługi App Service](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) artykułu.

#### <a name="tomcat"></a>Tomcat

Dostęp użytkownika Twojej aplikacji Tomcat oświadczeń bezpośrednio z serwlet Tomcat przez rzutowanie podmiot zabezpieczeń do obiektu obiektu mapy. Każdy typ oświadczenia będzie mapowany zbiór oświadczeń dla tego typu obiektu mapy. W poniższym kodzie `request` jest wystąpieniem `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Teraz możesz sprawdzić `Map` obiektu dla dowolnego z określonych oświadczenia. Na przykład poniższy fragment kodu iteruje przez wszystkie typy oświadczeń i drukuje zawartość każdej kolekcji.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Wyloguj użytkowników i wykonywać inne czynności, można znaleźć w dokumentacji na [użycie aplikacji usług uwierzytelniania i autoryzacji](https://docs.microsoft.com/en-us/azure/app-service/app-service-authentication-how-to). Istnieje również oficjalnej dokumentacji na serwerze Tomcat [interfejsu HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) i jego metod. Następujące serwletów, które metody są również uwodniony zgodnie z konfiguracją usługi App Service:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Aby wyłączyć tę funkcję, należy utworzyć ustawienie aplikacji o nazwie `WEBSITE_AUTH_SKIP_PRINCIPAL` o wartości `1`. Aby wyłączyć wszystkie filtry serwlet dodany przez usługę App Service, Utwórz ustawienie o nazwie `WEBSITE_SKIP_FILTERS` o wartości `1`.

#### <a name="spring-boot"></a>Spring Boot

Spring rozruchu deweloperzy mogą używać [usługi Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) do zabezpieczania aplikacji za pomocą dobrze znanych adnotacje zabezpieczeń Spring i interfejsów API. Pamiętaj zwiększyć rozmiar maksymalny nagłówka w swojej `application.properties` pliku. Zalecamy, aby wartość `16384`.

### <a name="configure-tlsssl"></a>Konfigurowanie protokołów TLS/SSL

Postępuj zgodnie z instrukcjami w [wiązanie istniejącego niestandardowego certyfikatu SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) Prześlij istniejący certyfikat protokołu SSL i powiązać nazwy domeny Twojej aplikacji. Domyślnie aplikacja będzie nadal zezwalania HTTP połączeń — wykonaj konkretne kroki w tym samouczku, aby wymusić SSL i TLS.

### <a name="use-keyvault-references"></a>Używać odwołań do magazynu kluczy

[Usługa Azure KeyVault](../../key-vault/key-vault-overview.md) umożliwia scentralizowane zarządzanie wpisu tajnego z historią zasad i inspekcja dostępu. Można przechowywać klucze tajne (takie jak hasła lub parametry połączenia) w magazynie KeyVault i uzyskiwanie dostępu do tych kluczy tajnych w aplikacji za pomocą zmiennych środowiskowych.

Po pierwsze, postępuj zgodnie z instrukcjami dotyczącymi [przyznania aplikacji dostępu do usługi Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) i [odwołanie magazynu kluczy do klucz tajny w ustawieniu aplikacji](../app-service-key-vault-references.md#reference-syntax). Aby zweryfikować, czy odwołanie jest rozpoznawana jako klucz tajny, drukowanie zmiennej środowiskowej, uzyskując dostęp do zdalnego terminalu usługi App Service.

Aby wstawić tych kluczy tajnych w pliku konfiguracji platformy Spring lub Tomcat, należy użyć składni iniekcji zmiennej środowiska (`${MY_ENV_VAR}`). Pliki konfiguracji środowiska Spring, można znaleźć tej dokumentacji na [zewnętrznych konfiguracje](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="configure-apm-platforms"></a>Konfiguruj platformy APM

W tej sekcji pokazano sposób łączenia aplikacji w języku Java wdrożone w usłudze Azure App Service w systemie Linux przy użyciu usługi NewRelic i AppDynamics application performance monitoring (APM) platformy.

[Konfigurowanie usługi New Relic](#configure-new-relic)
[skonfigurować AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Konfigurowanie usługi New Relic

1. Tworzenie konta usługi NewRelic na [NewRelic.com](https://newrelic.com/signup)
2. Pobierz agenta Java z usługi NewRelic, będzie mieć nazwę pliku, podobnie jak `newrelic-java-x.x.x.zip`.
3. Skopiuj klucz licencji, będzie potrzebny później skonfigurować agenta.
4. [SSH do Twojego wystąpienia usługi App Service](app-service-linux-ssh-support.md) i Utwórz nowy katalog `/home/site/wwwroot/apm`.
5. Przekaż nierozpakowane plików agenta NewRelic Java do katalogu, w obszarze `/home/site/wwwroot/apm`. Pliki agenta musi należeć do `/home/site/wwwroot/apm/newrelic`.
6. Zmodyfikuj plik YAML na `/home/site/wwwroot/apm/newrelic/newrelic.yml` i zastąp wartość symbolu zastępczego licencji klucz licencji.
7. W witrynie Azure portal przejdź do aplikacji w usłudze App Service, a następnie utwórz nowe ustawienie aplikacji.
    - Jeśli aplikacja używa **Java SE**, Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Jeśli używasz **Tomcat**, Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Jeśli używasz **WildFly**, znajdują się w dokumentacji usługi New Relic [tutaj](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) wskazówki na temat instalowania agenta Java i JBoss konfiguracji.
    - Jeśli masz już zmienną środowiskową dla `JAVA_OPTS` lub `CATALINA_OPTS`, dołącz `javaagent` opcji-to-end bieżącą wartość.

### <a name="configure-appdynamics"></a>Konfigurowanie AppDynamics

1. Utwórz konto AppDynamics podczas [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Pobierz agenta Java z oprogramowaniem AppDynamics witryny sieci Web, nazwa pliku będą wyglądać mniej więcej `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH do Twojego wystąpienia usługi App Service](app-service-linux-ssh-support.md) i Utwórz nowy katalog `/home/site/wwwroot/apm`.
1. Przekazywanie plików agenta Java na katalog, w obszarze `/home/site/wwwroot/apm`. Pliki agenta musi należeć do `/home/site/wwwroot/apm/appdynamics`.
1. W witrynie Azure portal przejdź do aplikacji w usłudze App Service, a następnie utwórz nowe ustawienie aplikacji.
    - Jeśli używasz **Java SE**, Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` wartością `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` gdzie `<app-name>` to nazwa usługi App Service.
    - Jeśli używasz **Tomcat**, Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` wartością `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` gdzie `<app-name>` to nazwa usługi App Service.
    - Jeśli używasz **WildFly**, zobacz dokumentację AppDynamics [tutaj](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) wskazówki na temat instalowania agenta Java i JBoss konfiguracji.
    
## <a name="configure-jar-applications"></a>Konfigurowanie aplikacji JAR

### <a name="starting-jar-apps"></a>Uruchamianie aplikacji JAR

Domyślnie usługa App Service oczekuje, że aplikacja JAR miała nazwę `app.jar`. Jeśli ma ona tę nazwę, zostanie automatycznie uruchomiony. Dla użytkowników narzędzia Maven, można ustawić nazwę pliku JAR, umieszczając `<finalName>app</finalName>` w `<build>` części Twojej `pom.xml`. [Dzieje się tak samo w Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) , ustawiając `archiveFileName` właściwości.

Jeśli chcesz użyć innej nazwy dla Twojego pliku JAR, musisz także podać [polecenie uruchamiania](app-service-linux-faq.md#built-in-images) , który jest wykonywany plik JAR. Na przykład `java -jar my-jar-app.jar`. Wartość można ustawić dla polecenia uruchamiania w portalu w obszarze Konfiguracja > Ustawienia ogólne lub ustawienie aplikacji o nazwie `STARTUP_COMMAND`.

### <a name="server-port"></a>Port serwera

App Service dla systemu Linux kieruje żądania przychodzące do portu 80, dzięki czemu aplikacja powinna nasłuchiwania na porcie 80 także. Można to zrobić w konfiguracji aplikacji (np. przez Spring `application.properties` pliku), lub polecenia uruchomienia (na przykład `java -jar spring-app.jar --server.port=80`). Można znaleźć w poniższej dokumentacji, common platform Java:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Odtwórz Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Źródła danych

### <a name="tomcat"></a>Tomcat

Te instrukcje mają zastosowanie do wszystkich połączeń z bazą danych. Należy wypełnić symbole zastępcze nazwą klasy sterownik wybranej bazy danych i pliku JAR. Podany jest tabelą z nazwy klas i sterowniki do pobrania dla wspólnej bazy danych.

| Database (Baza danych)   | Nazwa klasy sterownika                             | Sterownik JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Pobieranie](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Pobierz](https://dev.mysql.com/downloads/connector/j/) (Wybierz "Niezależne od platformy") |
| Oprogramowanie SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Pobieranie](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Aby skonfigurować Tomcat, aby użyć połączenia bazy danych języka Java (JDBC) lub interfejsu API trwałości Java rozwiązaniami (JPA), należy najpierw dostosować `CATALINA_OPTS` zmiennej środowiskowej, który jest wczytywany w Tomcat przy rozruchu. Ustaw te wartości za pomocą ustawienia aplikacji w [wtyczki Maven usługi aplikacji](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Lub ustawić zmienne środowiskowe w **konfiguracji** > **ustawienia aplikacji** strony w witrynie Azure portal.

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
    ```
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

#### <a name="finalize-configuration"></a>Finalizowanie konfiguracji

Na koniec umieść plikach JAR sterownika w ścieżce klasy Tomcat i uruchom ponownie usługi App Service.

1. Upewnij się, że pliki sterownika JDBC są dostępne dla Tomcat classloader, umieszczając je w `/home/tomcat/lib` katalogu. (Utwórz ten katalog, jeśli jeszcze nie istnieje). Aby przekazać te pliki do swojego wystąpienia usługi App Service, wykonaj następujące czynności:
    1. W [Cloud Shell](https://shell.azure.com), instalowanie rozszerzenia aplikacji sieci Web:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do usługi App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Podłącz do lokalnego portu tunelowania przy użyciu klienta protokołu SFTP oraz przekazywanie plików do `/home/tomcat/lib` folderu.

    Alternatywnie można użyć klienta FTP do przekazania sterownik JDBC. Postępuj zgodnie z tymi [instrukcje dotyczące pobierania poświadczeń protokołu FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Jeśli utworzono źródła danych na poziomie serwera, uruchom ponownie aplikację App Service dla systemu Linux. Spowoduje to zresetowanie Tomcat `CATALINA_HOME` do `/home/tomcat/conf` i użyć zaktualizowanej konfiguracji.

### <a name="spring-boot"></a>Spring Boot

Aby połączyć się ze źródłami danych w aplikacji platformy Spring Boot, zalecamy tworzenie parametrów połączenia i wprowadzanie ich na swojej `application.properties` pliku.

1. W sekcji "Konfiguracja" Strona usługi App Service Ustaw nazwę ciągu, Wklej parametry połączenia sterownika JDBC w polu wartość i ustawienie typu na "Niestandardowe". Opcjonalnie możesz ustawić te parametry połączenia jako ustawienie miejsca.

    Ten ciąg połączenia jest dostępny do naszej aplikacji jako zmienną środowiskową o nazwie `CUSTOMCONNSTR_<your-string-name>`. Na przykład, będzie miała parametry połączenia, utworzone powyżej `CUSTOMCONNSTR_exampledb`.

2. W swojej `application.properties` plików, odwoływać się do tych parametrów połączenia przy użyciu nazwa zmiennej środowiskowej. W tym przykładzie należy użyć następujących czynności.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Zobacz [dokumentacji platformy Spring Boot na dostęp do danych](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) i [zewnętrznych konfiguracje](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) Aby uzyskać więcej informacji na ten temat.

## <a name="configure-java-ee-wildfly"></a>Konfigurowanie środowiska Java EE (WildFly)

> [!NOTE]
> Oprogramowania Java Enterprise Edition w systemie Linux usługi aplikacji jest obecnie w wersji zapoznawczej. Ten stos jest **nie** zalecane w przypadku pracy przeznaczonych dla produkcji. informacje na temat naszych stosów języka Java SE i Tomcat.

Usługa Azure App Service w systemie Linux umożliwia deweloperom języka Java, tworzenie, wdrażanie i skalowanie aplikacji Java przedsiębiorstwa (Java EE) na w pełni zarządzanych usług opartych na systemie Linux.  Podstawowe środowisko uruchomieniowe Java Enterprise jest typu open-source [Wildfly](https://wildfly.org/) serwera aplikacji.

[Skalowanie przy użyciu usługi App Service](#scale-with-app-service)
[Konfiguracja serwera aplikacji Dostosuj](#customize-application-server-configuration)
[modułów i zależności](#modules-and-dependencies)
[danych źródła](#data-sources)
[Włączanie obsługi komunikatów dostawców](#enable-messaging-providers)
[skonfigurować buforowanie zarządzania sesji](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Skalowanie przy użyciu usługi App Service

Serwer aplikacji WildFly działającej w usłudze App Service w systemie Linux jest uruchamiany w trybie autonomicznym, nie znajduje się w konfiguracji domeny. Skalowanie w poziomie Plan usługi App Service, każde wystąpienie WildFly jest skonfigurowany jako serwer autonomiczny.

 Skalowanie aplikacji w pionie lub w poziomie za pomocą [skalowanie reguły](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) i [zwiększyć swoje liczba wystąpień](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Dostosuj konfigurację serwera aplikacji

Wystąpienia aplikacji sieci Web są bezstanowe, więc każde wystąpienie nowej pracy musi być skonfigurowany przy uruchamianiu w celu zapewnienia obsługi konfiguracji Wildfly, wymagane przez aplikację.
Uruchamianie skryptu powłoki systemowej w celu wywołania WildFly interfejs wiersza polecenia i można napisać:

- Konfigurowanie źródła danych
- Konfigurowanie dostawców obsługi komunikatów
- Do konfiguracji serwera Wildfly, należy dodać inne moduły i zależności.

 Skrypt jest uruchamiany, gdy Wildfly jest uruchomiona, ale przed uruchomieniem aplikacji. Należy użyć skryptu [JBOSS interfejsu wiersza polecenia](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) wywoływane z `/opt/jboss/wildfly/bin/jboss-cli.sh` skonfigurować serwer aplikacji z konfiguracji lub zmian po uruchomieniu serwera.

Nie należy używać tryb interaktywny interfejsu wiersza polecenia do konfigurowania Wildfly. Zamiast tego można udostępnić skrypt poleceń interfejsu wiersza polecenia JBoss przy użyciu `--file` polecenia, na przykład:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Przekaż skrypt uruchamiania `/home/site/deployments/tools` w wystąpieniu usługi App Service. Zobacz [w tym dokumencie](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope) instrukcje dotyczące pobierania poświadczeń protokołu FTP.

Ustaw **skryptu uruchamiania** pola w witrynie Azure portal do lokalizacji uruchamiania skryptu powłoki, na przykład `/home/site/deployments/tools/your-startup-script.sh`.

Podaj [ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) w konfiguracji aplikacji, aby przekazać zmienne środowiskowe do wykorzystania w skrypcie. Ustawienia aplikacji przechowywać parametry połączenia i innych wpisów tajnych, wymagane do skonfigurowania aplikacji z systemu kontroli wersji.

### <a name="modules-and-dependencies"></a>Moduły i zależności

Aby zainstalować moduły oraz ich zależności w ścieżce Wildfly, za pomocą interfejsu wiersza polecenia JBoss, należy utworzyć następujące pliki w ich własnych katalogu. Niektóre moduły i zależności może wymagać dodatkowej konfiguracji, takich jak JNDI nazewnictwa lub innych konfiguracji specyficznej dla interfejsu API, więc ta lista jest minimalny zestaw co jest potrzebne skonfigurować zależności w większości przypadków.

- [Deskryptora modułu XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Ten plik XML definiuje nazwę, atrybuty i zależności modułu. To [przykładowy plik module.xml](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definiuje moduł Postgres, jego zależność od sterownika JDBC pliku JAR i inne zależności moduł wymagany.
- Wszelkie niezbędne JAR pliku zależności dla modułu.
- Skrypt za pomocą poleceń interfejsu wiersza polecenia JBoss konfigurowania nowego modułu. Ten plik będzie zawierać Twoich poleceń do wykonania przez JBoss interfejs wiersza polecenia i skonfigurować serwer do korzystania z zależności. Aby uzyskać dokumentację na temat poleceń, aby dodać moduły, źródła danych i dostawców obsługi komunikatów, zobacz [w tym dokumencie](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Skrypt uruchamiania powłoki Bash wywołać JBoss interfejsu wiersza polecenia i uruchom skrypt w poprzednim kroku. Ten plik zostanie wykonany po ponownym uruchomieniu wystąpienia usługi App Service, lub gdy nowe wystąpienia są aprowizowane podczas skalowania w poziomie. Ten skrypt uruchamiania to, gdzie można wykonać inne konfiguracje dla aplikacji, JBoss polecenia są przekazywane do JBoss interfejsu wiersza polecenia. Co najmniej ten plik może być jednego polecenia do przekazania skryptu polecenia interfejsu wiersza polecenia JBoss JBoss interfejsu wiersza polecenia:

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

Po utworzeniu plików i zawartości dla modułu, wykonaj poniższe kroki, aby dodać moduł do serwera aplikacji Wildfly.

1. FTP pliki `/home/site/deployments/tools` w wystąpieniu usługi App Service. Zobacz ten dokument, aby uzyskać instrukcje na temat pobierania poświadczeń protokołu FTP.
2. W **konfiguracji** > **ustawienia ogólne** strony Azure portal, ustaw "skrypt uruchamiania" pole Lokalizacja uruchamiania skryptu powłoki, na przykład `/home/site/deployments/tools/your-startup-script.sh` .
3. Ponownie uruchom wystąpienie usługi App Service, naciskając **ponowne uruchomienie** znajdujący się w **Przegląd** części portalu lub przy użyciu wiersza polecenia platformy Azure.

### <a name="configure-data-source-connections"></a>Konfigurowanie połączeń ze źródłami danych

Aby skonfigurować Wildfly do połączenia ze źródłem danych, postępuj zgodnie z tym samym procesie opisanych powyżej w części instalowanie modułów i zależności. Aby wykonać te same kroki dla dowolnej usługi Azure Database.

1. Pobierz sterownik JDBC dla użytkownika wersja bazy danych. Dla wygody w tym miejscu są sterowniki [Postgres](https://jdbc.postgresql.org/download.html) i [MySQL](https://dev.mysql.com/downloads/connector/j/). Rozpakuj pobierania można pobrać pliku JAR.
2. Postępuj zgodnie z konspektu kroki w "Modułów i zależności" Aby utworzyć i przekazać deskryptora modułu XML, skrypt interfejsu wiersza polecenia JBoss, skrypt uruchamiania i zależności JAR JDBC.

Więcej informacji na temat konfigurowania Wildfly z [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) , [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource), i [bazy danych SQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) jest dostępna. Można dodać definicji źródła danych na serwerze, można użyć tych dostosowanych instrukcji wraz z uogólnionego podejście powyżej.

### <a name="enable-messaging-providers"></a>Włączanie obsługi komunikatów dostawców

Aby włączyć fasoli opartych na komunikat przy użyciu usługi Service Bus jako mechanizm obsługi komunikatów:

1. Użyj [biblioteki obsługi wiadomości Apache QPId JMS](https://qpid.apache.org/proton/index.html). Obejmują tę zależność w swojej pom.xml (lub inny plik kompilacji) dla aplikacji.

2. Tworzenie [zasobów usługi Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Tworzenie przestrzeni nazw usługi Azure Service Bus i kolejki, w ramach tej przestrzeni nazw i zasady dostępu współużytkowanego przy użyciu wysyłania i odbierania możliwości.

3. Przekaż klucz zasad dostępu współdzielonego w kodzie, przez kodowanie adresu URL klucza podstawowego zasad lub [używania zestawu SDK usługi Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Wykonaj czynności opisane w sekcji instalowanie modułów i zależności za pomocą modułu XML deskryptora, JAR zależności, JBoss polecenia interfejsu wiersza polecenia i skryptu uruchamiania dla dostawcy JMS. Oprócz cztery pliki należy również utworzyć plik XML, który definiuje nazwę JNDI JMS kolejki i tematu. Zobacz [to repozytorium](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) dla plików konfiguracji odniesienia.

### <a name="configure-session-management-caching"></a>Skonfigurować buforowanie zarządzania sesji

Domyślnie usługi App Service w systemie Linux użyje plików cookie koligacji sesji, aby upewnić się, że są kierowane żądania klientów przy użyciu istniejącej sesji to samo wystąpienie elementu aplikacji. To zachowanie domyślne nie wymaga konfiguracji, ale ma pewne ograniczenia:

- Jeśli wystąpienie aplikacji zostanie ponownie uruchomiony lub skalowane w dół, stan sesji użytkownika na serwerze aplikacji zostaną utracone.
- Jeśli ustawienia limitu czasu sesji długi lub stała liczba użytkowników aplikacji, może upłynąć trochę czasu, przez funkcję nowe wystąpienia mogą odbierać obciążenia, ponieważ tylko nowe sesje będą kierowane do nowo rozpoczęte wystąpień.

Można skonfigurować Wildfly, aby korzystać z magazynu zewnętrznego sesji, takie jak [pamięci podręcznej Redis Azure](/azure/azure-cache-for-redis/). Konieczne będzie [wyłączanie istniejących koligacja ARR w wystąpieniu](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurację, aby wyłączyć sesji na podstawie plików cookie routingu i umożliwić skonfigurowanego magazynu sesji Wildfly działanie bez zakłóceń.

## <a name="docker-containers"></a>Kontenerów Docker

Aby użyć zestawu JDK Zulu obsługiwanej przez platformę Azure w swoje kontenery, upewnij się, że ściągać i używać wstępnie utworzone obrazy, zgodnie z opisem z [obsługiwane Azul Zulu Enterprise dla strony pobierania Azure](https://www.azul.com/downloads/azure-only/zulu/) lub użyj `Dockerfile` przykłady z [Repozytorium usługi GitHub firmy Microsoft Java](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Oświadczenie zespołu pomocy technicznej

### <a name="runtime-availability"></a>Dostępność w czasie wykonywania

App Service dla systemu Linux obsługuje dwa środowiska uruchomieniowe zarządzany hosting aplikacji sieci web Java:

- [Kontener serwletów Tomcat](https://tomcat.apache.org/) do uruchamiania aplikacji w pakiecie, tak jak w sieci web plików archiwów (WAR). Obsługiwane wersje to 8.5 i 9.0.
- Środowisko uruchomieniowe Java SE do uruchamiania aplikacji w pakiecie jako archiwum Java plików (plik JAR). Obsługiwane wersje to Java 8 i 11.

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

W odpowiedzi są ogólne pytania dotyczące korzystania z usługi App Service dla systemu Linux, które nie są specyficzne dla programowania Java [App Service Linux — często zadawane pytania](app-service-linux-faq.md).


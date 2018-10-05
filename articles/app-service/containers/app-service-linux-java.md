---
title: Obsługa języka Java dla usługi Azure App Service w systemie Linux | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera dotyczący wdrażania aplikacji Java w usłudze Azure App Service w systemie Linux.
keywords: Usługa Azure app service, aplikacji sieci web, linux, oss, języka java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.openlocfilehash: 48d6836a2f1c7eb53fd6d7a08ee5a049cd9503f6
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803039"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Przewodnik dla deweloperów języka Java dla usługi App Service w systemie Linux

Usługa Azure App Service w systemie Linux umożliwia deweloperom szybkie tworzenie, wdrażanie i skalowanie ich Tomcat języka Java lub Java Standard Edition (SE) w pakiecie aplikacji sieci web w pełni zarządzanej usługi opartej na systemie Linux. Wdrażanie aplikacji przy użyciu wtyczki usługi Maven w wierszu polecenia lub w edytorach, takich jak IntelliJ, Eclipse lub Visual Studio Code.

Ten przewodnik zawiera podstawowe pojęcia i instrukcje dla deweloperów języka Java przy użyciu w usłudze App Service dla systemu Linux. Jeśli nie znasz usługi Azure App Service dla systemu Linux, należy przeczytać za pośrednictwem [Szybki Start Java](quickstart-java.md) pierwszy. W odpowiedzi są ogólne pytania dotyczące korzystania z usługi App Service dla systemu Linux, które nie są specyficzne dla programowania Java [App Service Linux — często zadawane pytania](app-service-linux-faq.md).

## <a name="logging-and-debugging-apps"></a>Rejestrowanie i debugowanie aplikacji

Raporty dotyczące wydajności, wizualizacji ruchu i kontrole kondycji są dostępne dla aplikacji eeach za pośrednictwem witryny Azure portal. Zobacz [Omówienie diagnostyki usługi Azure App Service](/azure/app-service/app-service-diagnostics) Aby uzyskać więcej informacji na temat uzyskać dostęp do tych narzędzi diagnostycznych.

### <a name="ssh-console-access"></a>Dostęp do konsoli SSH 

Połączenia SSH do środowiska systemu Linux, z tą aplikacją są dostępne. Zobacz [Obsługa protokołu SSH dla usługi Azure App Service w systemie Linux](/azure/app-service/containers/app-service-linux-ssh-support) pełne instrukcje połączyć się z systemem Linux przez przeglądarkę sieci web lub terminalu lokalnym.

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

Aby uzyskać więcej informacji, zobacz [przesyłania strumieniowego dzienników za pomocą wiersza polecenia platformy Azure](/azure/app-service/web-sites-enable-diagnostic-log#streaming-with-azure-command-line-interface).

### <a name="app-logging"></a>Rejestrowanie aplikacji

Włącz [rejestrowanie aplikacji](/azure/app-service/web-sites-enable-diagnostic-log#enablediag) za pośrednictwem witryny Azure portal lub [wiersza polecenia platformy Azure](/cli/azure/webapp/log#az-webapp-log-config) do skonfigurowania usługi App Service do zapisu wyjście konsoli standardowej i strumieni błąd konsoli standardowej aplikacji lokalnej System plików lub usługi Azure Blob Storage. Rejestrowanie w lokalnym systemie plików usługi App Service wystąpienie jest wyłączone na 12 godzin, po skonfigurowaniu go. Dłuższy okres przechowywania, należy skonfigurować aplikację, aby zapisywać dane wyjściowe do kontenera magazynu obiektów Blob.

Jeśli aplikacja używa [Logback](https://logback.qos.ch/) lub [Log4j](https://logging.apache.org/log4j) śledzenia, możesz przekazywać te dane śledzenia do przeglądu w usłudze Azure Application Insights zgodnie z instrukcjami konfiguracji struktury rejestrowania [Dzienniki śledzenia zapoznaj się z języka Java w usłudze Application Insights](/azure/application-insights/app-insights-java-trace-logs). 

## <a name="customization-and-tuning"></a>Dostosowywanie i dostrajanie

Usługa Azure App Service dla systemu Linux obsługuje poza pole dostrajanie i dostosowywanie przy użyciu witryny Azure Portal oraz interfejsu wiersza polecenia. Sprawdź następujące artykuły dotyczące konfiguracji aplikacji sieci web-Java:

- [Konfigurowanie ustawień usługi App Service](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurowanie domeny niestandardowej](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Włącz protokół SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Dodawanie usługi CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Ustaw opcje środowiska uruchomieniowego języka Java

Ilość przydzielonej pamięci lub inne opcje środowiska uruchomieniowego maszyny JVM w środowiskach Java SE i Tomcat ustawia JAVA_OPTS, jak pokazano poniżej jako [ustawienie aplikacji](/azure/app-service/web-sites-configure#app-settings). Usługa App Service w systemie Linux przekazuje to ustawienie jako zmienną środowiskową środowisko wykonawcze języka Java podczas jego uruchamiania.

W witrynie Azure portal w obszarze **ustawienia aplikacji** dla aplikacji sieci web, należy utworzyć nowe ustawienie aplikacji o nazwie `JAVA_OPTS` zawierającej dodatkowe ustawienia, takie jak `$JAVA_OPTS -Xms512m -Xmx1204m`.

Aby skonfigurować ustawienia aplikacji z wtyczki Azure App Service Linux Maven, Dodaj ustawienia i wartości tagów w sekcji wtyczka platformy Azure. W poniższym przykładzie ustawiono określonej minimalnej i maksymalnej heapsize Java:

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value> 
    </property> 
</appSettings> 
```

Deweloperzy uruchomił jedną aplikację z gniazdem jedno wdrożenie w planu usługi App Service można użyć następujących opcji:

- Wystąpienia B1 i S1:-Xms1024m-Xmx1024m
- Wystąpienia B2 i S2:-Xms3072m-Xmx3072m
- Wystąpień w wersji B3 i S3:-Xms6144m-Xmx6144m


Podczas dostosowywania ustawienia sterty aplikacji, przejrzyj szczegóły planu usługi App Service i wziąć pod uwagę wiele aplikacji i miejsca wdrożenia musi znaleźć optymalne alokacji pamięci.

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

W witrynie Azure portal w obszarze **ustawienia aplikacji** dla aplikacji sieci web, należy utworzyć nowe ustawienie aplikacji o nazwie `JAVA_OPTS` wartością `$JAVA_OPTS -Dfile.encoding=UTF-8`.

Alternatywnie można skonfigurować ustawienia aplikacji przy użyciu wtyczki Maven usługi aplikacji. Dodaj tagi nazwę i wartość ustawienia konfiguracji wtyczki: 

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value> 
    </property> 
</appSettings> 
```

## <a name="secure-application"></a>Zabezpieczanie aplikacji

Aplikacje Java uruchomiona w usłudze App Service dla systemu Linux ma ten sam zestaw [najlepszych rozwiązań dotyczących zabezpieczeń](/azure/security/security-paas-applications-using-app-services) jako inne aplikacje. 

### <a name="authenticate-users"></a>Uwierzytelnianie użytkowników

Konfigurowanie uwierzytelniania aplikacji w witrynie Azure Portal za pomocą **uwierzytelnianie i autoryzacja** opcji. Z tego miejsca można włączyć uwierzytelnianie przy użyciu usługi Azure Active Directory lub społecznościowych nazw logowania, takich jak Facebook, Google lub GitHub. Konfiguracja portalu Azure działa tylko podczas konfigurowania dostawcy jednorazowego uwierzytelniania.  Aby uzyskać więcej informacji, zobacz [skonfiguruj aplikację usługi App Service, aby używała logowania do usługi Azure Active Directory](/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) pokrewne artykuły dotyczące innych dostawców tożsamości.

Jeśli musisz włączyć wielu dostawców logowania, postępuj zgodnie z instrukcjami [Dostosowywanie uwierzytelniania usługi App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) artykułu.

 Spring rozruchu deweloperzy mogą używać [usługi Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) do zabezpieczania aplikacji za pomocą dobrze znanych adnotacje zabezpieczeń Spring i interfejsów API.

### <a name="configure-tlsssl"></a>Konfigurowanie protokołów TLS/SSL

Postępuj zgodnie z instrukcjami w [wiązanie istniejącego niestandardowego certyfikatu SSL](/azure/app-service/app-service-web-tutorial-custom-ssl) Prześlij istniejący certyfikat protokołu SSL i powiązać nazwy domeny Twojej aplikacji. Domyślnie aplikacja będzie nadal zezwalania HTTP połączeń — wykonaj konkretne kroki w tym samouczku, aby wymusić SSL i TLS.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>Łączenie ze źródłami danych

>[!NOTE]
> Jeśli aplikacja używa Spring Framework lub Spring Boot, można ustawić informacji połączenia bazy danych dla źródła danych z rozwiązaniami JPA jako zmienne środowiskowe [w pliku właściwości aplikacji]. Następnie użyj [ustawienia aplikacji](/azure/app-service/web-sites-configure#app-settings) do definiowania te wartości dla swojej aplikacji w witrynie Azure portal lub interfejsu wiersza polecenia.

Aby skonfigurować Tomcat używać zarządzanego połączenia z bazami danych przy użyciu języka Java połączenia bazy danych (JDBC) lub interfejsu API trwałości Java rozwiązaniami (JPA), należy najpierw dostosować zmienną środowiskową CATALINA_OPTS przeczytać, Tomcat podczas uruchamiania. Ustaw te wartości za pomocą ustawienia aplikacji we wtyczce aplikacji usługi Maven:

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Dmysqluser=${mysqluser} -Dmysqlpass=${mysqlpass} -DmysqlURL=${mysqlURL}"</value> 
    </property> 
</appSettings> 
```

Lub równoważne usługi App Service, ustawiając w witrynie Azure portal.

Następnie należy określić, czy źródło danych ma udostępnione tylko do jednej aplikacji lub dla wszystkich aplikacji uruchomionych na plan usługi App Service.

W przypadku źródeł danych na poziomie aplikacji: 

1. Dodaj `context.xml` plik, jeśli nie istnieje, do aplikacji sieci web i dodaj go `META-INF` katalogu pliku WAR podczas kompilowania projektu.

2. W tym pliku Dodaj `Context` wpis ścieżki połączenia źródła danych na adres JNDI. W

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Aktualizowanie aplikacji `web.xml` używać źródła danych w aplikacji.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

Za udostępnione zasoby na poziomie serwera:

1. Skopiuj zawartość `/usr/local/tomcat/conf` do `/home/tomcat` na usługi App Service Linux wystąpienia przy użyciu protokołu SSH, jeśli jeszcze nie masz konfiguracji istnieje.

2. Dodawanie kontekstu do usługi `server.xml`

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Aktualizowanie aplikacji `web.xml` używać źródła danych w aplikacji.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

4. Upewnij się, że pliki sterownika JDBC są dostępne dla Tomcat classloader, umieszczając je w `/home/tomcat/lib` katalogu. Aby przekazać te pliki do swojego wystąpienia usługi App Service, wykonaj następujące czynności:  
    1. Zainstaluj rozszerzenie webpp w usłudze Azure App Service:
      ```azurecli-interactive
      az extension add –name webapp
      ```
    2. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do usługi App Service:
      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```
    3. Podłącz do lokalnego portu tunelowania przy użyciu klienta protokołu SFTP oraz przekazywanie plików do `/home/tomcat/lib`.

5. Uruchom ponownie aplikację App Service dla systemu Linux. Spowoduje to zresetowanie Tomcat `CATALINA_HOME` do `/home/tomcat` i korzystać z zaktualizowaną konfiguracją i klas.

## <a name="docker-containers"></a>Kontenerów Docker

Aby użyć zestawu JDK Zulu obsługiwanej przez platformę Azure w swoje kontenery, upewnij się, że ściągać i używać wstępnie utworzone obrazy na [strony pobierania firmy Azul](https://www.azul.com/downloads/azure-only/zulu/#docker) lub użyj `Dockerfile` przykłady z [repozytorium usługi GitHub firmy Microsoft Java](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Dostępność w czasie wykonywania i oświadczenie zespołu pomocy technicznej

App Service dla systemu Linux obsługuje dwa środowiska uruchomieniowe zarządzany hosting aplikacji sieci web Java:

- [Kontener serwletów Tomcat](http://tomcat.apache.org/) do uruchamiania aplikacji w pakiecie, tak jak w sieci web plików archiwów (WAR). Obsługiwane wersje to 8.5 i 9.0.
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

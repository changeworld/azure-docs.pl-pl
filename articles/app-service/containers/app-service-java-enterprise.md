---
title: Pomoc techniczną dla przedsiębiorstw Java w systemie Linux — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera dotyczący wdrażania aplikacji Java Enterprise Wildfly przy użyciu usługi Azure App Service w systemie Linux.
keywords: Usługa Azure app service, aplikacji sieci web, linux, oss, java, wildfly, enterprise, java ee, jee, javaee
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
ms.custom: seodec18
ms.openlocfilehash: 8db65fd9a1f271aea4ceb345f4d9dfbb6b9ff8a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852230"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>Przewodnik dotyczący Java Enterprise dla usługi App Service w systemie Linux

> [!NOTE] 
> Oprogramowania Java Enterprise Edition w systemie Linux usługi aplikacji jest obecnie w wersji zapoznawczej. Ten stos jest **nie** zalecane w przypadku pracy przeznaczonych dla produkcji. Zobacz [Java developer's guide](app-service-linux-java.md) uzyskać informacji na temat naszych stosów języka Java SE i Tomcat.

Usługa Azure App Service w systemie Linux umożliwia deweloperom języka Java, tworzenie, wdrażanie i skalowanie aplikacji Java przedsiębiorstwa (Java EE) na w pełni zarządzanych usług opartych na systemie Linux.  Podstawowe środowisko uruchomieniowe Java Enterprise jest typu open-source [Wildfly](https://wildfly.org/) serwera aplikacji.

Ten przewodnik zawiera podstawowe pojęcia i instrukcje dla deweloperów w przedsiębiorstwach Java przy użyciu w usłudze App Service dla systemu Linux. Jeśli nigdy nie udało Ci się wdrożyć aplikacji Java w usłudze Azure App Service dla systemu Linux, należy wykonać [Szybki Start Java](quickstart-java.md) pierwszy. W odpowiedzi na pytania dotyczące usługi App Service dla systemu Linux, które nie są specyficzne dla języka Java Enterprise [Java developer's guide](app-service-linux-java.md) i [App Service Linux — często zadawane pytania](app-service-linux-faq.md).

## <a name="scale-with-app-service"></a>Skalowanie przy użyciu usługi App Service 

Serwer aplikacji WildFly działającej w usłudze App Service w systemie Linux jest uruchamiany w trybie autonomicznym, nie znajduje się w konfiguracji domeny. Skalowanie w poziomie Plan usługi App Service, każde wystąpienie WildFly jest skonfigurowany jako serwer autonomiczny.

 Skalowanie aplikacji w pionie lub w poziomie za pomocą [skalowanie reguły](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json) i [zwiększyć swoje liczba wystąpień](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). 

## <a name="customize-application-server-configuration"></a>Dostosuj konfigurację serwera aplikacji

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

Przekaż skrypt uruchamiania `/home/site/deployments/tools` w wystąpieniu usługi App Service. Zobacz [w tym dokumencie](/azure/app-service/deploy-configure-credentials#userscope) instrukcje dotyczące pobierania poświadczeń protokołu FTP. 

Ustaw **skryptu uruchamiania** pola w witrynie Azure portal do lokalizacji uruchamiania skryptu powłoki, na przykład `/home/site/deployments/tools/your-startup-script.sh`.

Podaj [ustawienia aplikacji](/azure/app-service/web-sites-configure#application-settings) w konfiguracji aplikacji, aby przekazać zmienne środowiskowe do wykorzystania w skrypcie. Ustawienia aplikacji przechowywać parametry połączenia i innych wpisów tajnych, wymagane do skonfigurowania aplikacji z systemu kontroli wersji.

## <a name="modules-and-dependencies"></a>Moduły i zależności

Aby zainstalować moduły oraz ich zależności w ścieżce Wildfly, za pomocą interfejsu wiersza polecenia JBoss, należy utworzyć następujące pliki w ich własnych katalogu.  Niektóre moduły i zależności może wymagać dodatkowej konfiguracji, takich jak JNDI nazewnictwa lub innych konfiguracji specyficznej dla interfejsu API, więc ta lista jest minimalny zestaw co jest potrzebne skonfigurować zależności w większości przypadków.

- [Deskryptora modułu XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Ten plik XML definiuje nazwę, atrybuty i zależności modułu. To [przykładowy plik module.xml](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definiuje moduł Postgres, jego zależność od sterownika JDBC pliku JAR i inne zależności moduł wymagany.
- Wszelkie niezbędne JAR pliku zależności dla modułu.
- Skrypt za pomocą poleceń interfejsu wiersza polecenia JBoss konfigurowania nowego modułu. Ten plik będzie zawierać Twoich poleceń do wykonania przez JBoss interfejs wiersza polecenia i skonfigurować serwer do korzystania z zależności. Aby uzyskać dokumentację na temat poleceń, aby dodać moduły, źródła danych i dostawców obsługi komunikatów, zobacz [w tym dokumencie](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
-  Skrypt uruchamiania powłoki Bash wywołać JBoss interfejsu wiersza polecenia i uruchom skrypt w poprzednim kroku. Ten plik zostanie wykonany po ponownym uruchomieniu wystąpienia usługi App Service, lub gdy nowe wystąpienia są aprowizowane podczas skalowania w poziomie.  Ten skrypt uruchamiania to, gdzie można wykonać inne konfiguracje dla aplikacji, JBoss polecenia są przekazywane do JBoss interfejsu wiersza polecenia. Co najmniej ten plik może być jednego polecenia do przekazania skryptu polecenia interfejsu wiersza polecenia JBoss JBoss interfejsu wiersza polecenia: 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

Po utworzeniu plików i zawartości dla modułu, wykonaj poniższe kroki, aby dodać moduł do serwera aplikacji Wildfly. 

1. FTP pliki `/home/site/deployments/tools` w wystąpieniu usługi App Service. Zobacz ten dokument, aby uzyskać instrukcje na temat pobierania poświadczeń protokołu FTP. 
2. W bloku ustawień aplikacji w witrynie Azure Portal, ustaw pole "Skryptu uruchomieniowego" do lokalizacji uruchamiania skryptu powłoki, na przykład `/home/site/deployments/tools/your-startup-script.sh` .
3. Ponownie uruchom wystąpienie usługi App Service, naciskając **ponowne uruchomienie** znajdujący się w **Przegląd** części portalu lub przy użyciu wiersza polecenia platformy Azure.

## <a name="data-sources"></a>Źródła danych

Aby skonfigurować Wildfly do połączenia ze źródłem danych, postępuj zgodnie z tym samym procesie opisanych powyżej w części instalowanie modułów i zależności. Aby wykonać te same kroki dla dowolnej usługi Azure Database.

1. Pobierz sterownik JDBC dla użytkownika wersja bazy danych. Dla wygody w tym miejscu są sterowniki [Postgres](https://jdbc.postgresql.org/download.html) i [MySQL](https://dev.mysql.com/downloads/connector/j/). Rozpakuj pobierania można pobrać pliku JAR.
2. Postępuj zgodnie z konspektu kroki w "Modułów i zależności" Aby utworzyć i przekazać deskryptora modułu XML, skrypt interfejsu wiersza polecenia JBoss, skrypt uruchamiania i zależności JAR JDBC.


Więcej informacji na temat konfigurowania Wildfly z [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) , [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource), i [bazy danych SQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) jest dostępna. Można dodać definicji źródła danych na serwerze, można użyć tych dostosowanych instrukcji wraz z uogólnionego podejście powyżej.

## <a name="messaging-providers"></a>Dostawców obsługi komunikatów

Aby włączyć fasoli opartych na komunikat przy użyciu usługi Service Bus jako mechanizm obsługi komunikatów:

1. Użyj [biblioteki obsługi wiadomości Apache QPId JMS](https://qpid.apache.org/proton/index.html). Obejmują tę zależność w swojej pom.xml (lub inny plik kompilacji) dla aplikacji.

2.  Tworzenie [zasobów usługi Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Tworzenie przestrzeni nazw usługi Azure Service Bus i kolejki, w ramach tej przestrzeni nazw i zasady dostępu współużytkowanego przy użyciu wysyłania i odbierania możliwości.

3. Przekaż klucz zasad dostępu współdzielonego w kodzie, przez kodowanie adresu URL klucza podstawowego zasad lub [używania zestawu SDK usługi Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Wykonaj czynności opisane w sekcji instalowanie modułów i zależności za pomocą modułu XML deskryptora, JAR zależności, JBoss polecenia interfejsu wiersza polecenia i skryptu uruchamiania dla dostawcy JMS. Oprócz cztery pliki należy również utworzyć plik XML, który definiuje nazwę JNDI JMS kolejki i tematu. Zobacz [to repozytorium](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) dla plików konfiguracji odniesienia.


## <a name="configure-session-management-caching"></a>Skonfigurować buforowanie zarządzania sesji

Domyślnie usługi App Service w systemie Linux użyje plików cookie koligacji sesji, aby upewnić się, że są kierowane żądania klientów przy użyciu istniejącej sesji to samo wystąpienie elementu aplikacji. To zachowanie domyślne nie wymaga konfiguracji, ale ma pewne ograniczenia:

- Jeśli wystąpienie aplikacji zostanie ponownie uruchomiony lub skalowane w dół, stan sesji użytkownika na serwerze aplikacji zostaną utracone.
- Jeśli ustawienia limitu czasu sesji długi lub stała liczba użytkowników aplikacji, może upłynąć trochę czasu, przez funkcję nowe wystąpienia mogą odbierać obciążenia, ponieważ tylko nowe sesje będą kierowane do nowo rozpoczęte wystąpień.

Można skonfigurować Wildfly, aby korzystać z magazynu zewnętrznego sesji, takie jak [pamięci podręcznej Redis Azure](/azure/azure-cache-for-redis/). Konieczne będzie [wyłączanie istniejących koligacja ARR w wystąpieniu](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurację, aby wyłączyć sesji na podstawie plików cookie routingu i umożliwić skonfigurowanego magazynu sesji Wildfly działanie bez zakłóceń.

## <a name="enable-web-sockets"></a>Włącz gniazda sieci web

Domyślnie gniazda sieci web są włączane w usłudze App Service. Aby rozpocząć korzystanie z funkcji WebSockets w aplikacji, zapoznaj się [ten przewodnik Szybki Start](https://github.com/wildfly/quickstart/tree/master/websocket-hello).

## <a name="logs-and-troubleshooting"></a>Dzienniki i rozwiązywania problemów

Usługa App Service udostępnia narzędzia do rozwiązywania problemów z aplikacją.

-   Włącz rejestrowanie, klikając **dzienniki diagnostyczne** w okienku nawigacji po lewej stronie. Kliknij przycisk **System plików** Ustaw okres limitu przydziału i okres przechowywania magazynu i Zapisz zmiany. Możesz znaleźć te dzienniki w obszarze `/home/LogFiles/`.
-   [Za pomocą protokołu SSH Połącz się z wystąpieniem aplikacji](app-service-linux-ssh-support.md) Aby wyświetlić dzienniki do uruchamiania aplikacji.
-   Dzienniki diagnostyczne wyboru **dzienniki diagnostyczne** panelu portalu lub przy użyciu polecenia interfejsu wiersza polecenia platformy Azure: `az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group>`

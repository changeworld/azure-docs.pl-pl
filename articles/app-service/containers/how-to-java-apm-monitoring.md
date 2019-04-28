---
title: Konfigurowanie środowiska Java APM narzędzia i monitorowania w systemie Linux — usłudze Azure App Service
description: Dowiedz się, jak wysyłać dzienniki i metryki dla aplikacji języka Java uruchomionego na App Service dla systemu Linux do dostawców usługi NewRelic i aplikacji Dynamics APM
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 03/21/2019
ms.author: astay;routlaw
ms.custom: seodec18
ms.openlocfilehash: e6a22258266bda18c9ff79590d88e70d512f6c77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849969"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Porady: Narzędzia, za pomocą aplikacji Java w usłudze Azure App Service w systemie Linux do monitorowania wydajności aplikacji

W tym artykule pokazano sposób łączenia aplikacji w języku Java wdrożone w usłudze Azure App Service w systemie Linux przy użyciu usługi NewRelic i AppDynamics application performance monitoring (APM) platformy.

## <a name="configure-new-relic"></a>Konfigurowanie usługi New Relic

1. Tworzenie konta usługi NewRelic na [NewRelic.com](https://newrelic.com/signup)
2. Pobierz agenta Java z usługi NewRelic, będzie mieć nazwę pliku, podobnie jak `newrelic-java-x.x.x.zip`.
3. Skopiuj klucz licencji, będzie potrzebny później skonfigurować agenta.
4. [SSH do Twojego wystąpienia usługi App Service](/azure/app-service/containers/app-service-linux-ssh-support) i Utwórz nowy katalog `/home/site/wwwroot/apm`.
5. Przekaż nierozpakowane plików agenta NewRelic Java do katalogu, w obszarze `/home/site/wwwroot/apm`. Pliki agenta musi należeć do `/home/site/wwwroot/apm/newrelic`.
6. Zmodyfikuj plik YAML na `/home/site/wwwroot/apm/newrelic/newrelic.yml` i zastąp wartość symbolu zastępczego licencji klucz licencji.
7. W witrynie Azure portal przejdź do aplikacji w usłudze App Service, a następnie utwórz nowe ustawienie aplikacji.
    - Jeśli aplikacja używa **Java SE**, Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Jeśli używasz **Tomcat**, Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Jeśli używasz **WildFly**, znajdują się w dokumentacji usługi New Relic [tutaj](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) wskazówki na temat instalowania agenta Java i JBoss konfiguracji.
    - Jeśli masz już zmienną środowiskową dla `JAVA_OPTS` lub `CATALINA_OPTS`, dołącz `javaagent` opcji-to-end bieżącą wartość.

## <a name="configure-appdynamics"></a>Konfigurowanie AppDynamics

1. Utwórz konto AppDynamics podczas [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Pobierz agenta Java z oprogramowaniem AppDynamics witryny sieci Web, nazwa pliku będą wyglądać mniej więcej `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH do Twojego wystąpienia usługi App Service](/azure/app-service/containers/app-service-linux-ssh-support) i Utwórz nowy katalog `/home/site/wwwroot/apm`.
1. Przekazywanie plików agenta Java na katalog, w obszarze `/home/site/wwwroot/apm`. Pliki agenta musi należeć do `/home/site/wwwroot/apm/appdynamics`.
1. W witrynie Azure portal przejdź do aplikacji w usłudze App Service, a następnie utwórz nowe ustawienie aplikacji.
    - Jeśli używasz **Java SE**, Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` wartością `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` gdzie `<YOUR_SITE_NAME>` to nazwa usługi App Service.
    - Jeśli używasz **Tomcat**, Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` wartością `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` gdzie `<YOUR_SITE_NAME>` to nazwa usługi App Service.
    - Jeśli używasz **WildFly**, zobacz dokumentację AppDynamics [tutaj](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) wskazówki na temat instalowania agenta Java i JBoss konfiguracji.

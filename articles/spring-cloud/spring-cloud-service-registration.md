---
title: Automatyzowanie rejestru i odnajdywania usługi
description: Dowiedz się, jak zautomatyzować odnajdywanie i rejestrację usług przy użyciu rejestru wiosennej usługi w chmurze
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: jeconnoc
ms.openlocfilehash: 72327e116e498ce0f6881a5c585a08e56c8bf8c2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038745"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Odnajdywanie i rejestrowanie usług w chmurze wiosny

Odnajdowanie usług to kluczowy wymóg dla architektury opartej na mikrousługach.  Skonfigurowanie każdego klienta ręcznie zabiera czas i stanowi zagrożenie dla człowieka.  Rejestr usługi w chmurze Azure wiosennej rozwiązuje ten problem.  Po skonfigurowaniu serwer rejestru usługi będzie kontrolować rejestrację i odnajdywanie usługi dla mikrousług aplikacji. Serwer rejestru usługi przechowuje rejestr wdrożonych mikrousług, umożliwia Równoważenie obciążenia po stronie klienta i oddziela dostawców usług od klientów bez konieczności korzystania z systemu DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Rejestrowanie aplikacji przy użyciu rejestru wiosennej usługi w chmurze

Aby aplikacja mogła zarządzać rejestracją i odnajdywaniem usługi przy użyciu rejestru wiosennej usługi w chmurze, w pliku *pliku pom. XML* aplikacji musi być uwzględnionych kilka zależności.

Aby rozpocząć, Dodaj repozytorium migawek do sekcji *repozytorium* *pliku pliku pom. XML*

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
```

## <a name="include-dependencies"></a>Uwzględnij zależności

Następnie dołączymy zależności dla sieci *wiosny-Cloud-Starter-Netflix-Eureka-Client* i *sprężyny-Cloud-Starter-Azure-sprężyny-Cloud-Client* z *pliku pom. XML*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Aktualizowanie klasy najwyższego poziomu

Na koniec dodajemy adnotację do klasy najwyższego poziomu aplikacji

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Punkt końcowy serwera rejestru usługi w chmurze wiosny zostanie dodany jako zmienna środowiskowa w aplikacji.  Mikrousługi będą teraz mogły zarejestrować się na serwerze rejestru usługi i wykryć inne zależne mikrousługi.

Należy pamiętać, że może upłynąć kilka minut, zanim zmiany są propagowane z serwera do wszystkich mikrousług.

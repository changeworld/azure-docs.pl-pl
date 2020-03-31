---
title: Automatyzacja rejestru i odnajdowania usług
description: Dowiedz się, jak zautomatyzować odnajdowanie i rejestrację usługi przy użyciu rejestru usługi Spring Cloud Service Registry
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278856"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Odkryj i zarejestruj swoje usługi Spring Cloud

Odnajdowanie usługi jest kluczowym wymaganiem dla architektury opartej na mikrousługach.  Ręczne konfigurowanie każdego klienta wymaga czasu i wprowadza możliwość wystąpienia błędu ludzkiego.  Usługa Azure Spring Cloud Service Registry rozwiązuje ten problem.  Po skonfigurowaniu serwer rejestru usług będzie kontrolować rejestrację i odnajdowanie usługi dla mikrousług aplikacji. Serwer rejestru usług prowadzi rejestr wdrożonych mikrousług, umożliwia równoważenie obciążenia po stronie klienta i oddziela dostawców usług od klientów bez polegania na systemie DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Rejestrowanie aplikacji przy użyciu rejestru usługi Spring Cloud Service Registry

Aby aplikacja mogła zarządzać rejestracją i odnajdowaniem usług przy użyciu rejestru usługi w chmurze Spring, w pliku *pom.xml* aplikacji musi zostać uwzględnionych kilka zależności.
Uwzględnij zależności dla *klienta wiosna-cloud-starter-netflix-eureka-client* i *spring-cloud-starter-azure-spring-cloud-client* do *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Aktualizowanie klasy najwyższego poziomu

Na koniec dodajemy adnotację do najwyższej klasy aplikacji

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

Punkt końcowy serwera usługi Spring Cloud Service Registry zostanie wstrzyknięty jako zmienna środowiskowa w aplikacji.  Mikrousługi będą teraz mogli zarejestrować się na serwerze rejestru usług i odnajdywać inne mikrousług zależne.

Należy zauważyć, że może upłynąć kilka minut dla zmian propagacji z serwera do wszystkich mikrousług.

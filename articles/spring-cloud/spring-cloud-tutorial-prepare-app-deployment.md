---
title: Samouczek — przygotowanie aplikacji ze sprężyną Java do wdrożenia w chmurze Azure wiosennej
description: W tym samouczku przygotowasz aplikację ze sprężyną Java do wdrożenia w chmurze z systemem Azure wiosną.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 1af4bdcfc0fea319b9e5c9f39a1ade3f4492404f
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137631"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Przygotowywanie aplikacji ze sprężyną Java do wdrożenia w chmurze Azure wiosennej

Ten przewodnik Szybki Start przedstawia sposób przygotowania istniejącej aplikacji ze sprężyną Java do wdrożenia w chmurze z usługą Azure wiosną. W przypadku poprawnego skonfigurowania chmurowa usługa Azure wiosenna zapewnia niezawodne usługi do monitorowania, skalowania i aktualizowania aplikacji w chmurze ze sprężyną Java.

## <a name="java-runtime-version"></a>Wersja środowiska uruchomieniowego języka Java

Tylko aplikacje sprężynowe/Java mogą działać w chmurze Azure wiosennej.

Chmura sprężynowa platformy Azure obsługuje języki Java 8 i Java 11. Środowisko hostingu zawiera najnowszą wersję programu Azul Zulu OpenJDK for Azure. Aby uzyskać więcej informacji na temat Azul Zulu OpenJDK for Azure, zobacz [Instalowanie programu JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Sprężynowe wersje chmurowe i sprężynowe

Chmura sprężynowa platformy Azure obsługuje tylko aplikacje do rozruchu sprężynowego. Obsługuje zarówno rozruchy sprężynowe w wersji 2,1, jak i w wersji 2,2. W poniższej tabeli wymieniono obsługiwane kombinacje chmurowych rozruchowych i sprężynowych:

Wersja sprężyny rozruchowej | Wersja chmury wiosennej
---|---
2.1 | Greenwich. RELEASE
2.2 | Hoxton. RELEASE

Sprawdź, czy plik pliku pom. XML ma poprawne zależności chmurowego rozruchowego i sprężynowego na podstawie wersji rozruchowej sprężyny.

### <a name="dependencies-for-spring-boot-version-21"></a>Zależności w przypadku rozruchu sprężynowego w wersji 2,1

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Zależności w przypadku rozruchu sprężynowego w wersji 2,2

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Zależność klienta chmury ze sprężyną Azure

Usługa Azure wiosny Cloud hostuje i zarządza składnikami chmury Wiosnowej. Takie składniki obejmują rejestr usługi w chmurze i serwer konfiguracji chmury sprężynowej. Uwzględnij w zależnościach bibliotekę kliencką chmury platformy Azure, aby umożliwić komunikację z wystąpieniem usługi w chmurze ze sprężyną Azure.

W poniższej tabeli wymieniono prawidłowe wersje chmurowe platformy Azure dla aplikacji, które korzystają z chmury rozruchowej i sprężyny.

Wersja sprężyny rozruchowej | Wersja chmury wiosennej | Wersja chmury wiosennej platformy Azure
---|---|---
2.1 | Greenwich. RELEASE | 2.1
2.2 | Hoxton. RELEASE | 2.2

Uwzględnij jedną z następujących zależności w pliku pliku pom. XML. Wybierz zależność, której wersja chmury sieci Azure ze sprężyną jest zgodna z własnymi.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Zależność dla chmury wiosennej platformy Azure w wersji 2,1

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Zależność dla chmury wiosennej platformy Azure w wersji 2,2

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Inne wymagane zależności

Aby włączyć wbudowane funkcje chmury Azure wiosennej, aplikacja musi zawierać następujące zależności. To włączenie zapewnia, że aplikacja poprawnie skonfiguruje poszczególne składniki.  

### <a name="service-registry-dependency"></a>Zależność rejestru usługi

Aby można było korzystać z usługi Azure Managed Registry, należy uwzględnić zależność `spring-cloud-starter-netflix-eureka-client` w pliku pliku pom. XML, jak pokazano poniżej:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Punkt końcowy serwera rejestru usługi jest automatycznie wprowadzany jako zmienne środowiskowe w aplikacji. Aplikacje mogą zarejestrować się na serwerze rejestru usługi i odnajdywać inne zależne mikrousługi.

### <a name="distributed-configuration-dependency"></a>Zależność konfiguracji rozproszonej

Aby włączyć konfigurację rozproszoną, w sekcji zależności pliku pliku pom. XML uwzględnij następujące zależności `spring-cloud-config-client`:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Nie określaj `spring.cloud.config.enabled=false` w konfiguracji ładowania początkowego. W przeciwnym razie aplikacja przestanie działać z serwerem konfiguracji.

### <a name="metrics-dependency"></a>Zależność metryk

Uwzględnij zależność `spring-boot-starter-actuator` w sekcji zależności pliku pliku pom. XML, jak pokazano poniżej:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metryki są okresowo ściągane z punktów końcowych JMX. Możesz wizualizować metryki przy użyciu Azure Portal.

### <a name="distributed-tracing-dependency"></a>Zależność śledzenia rozproszonego

Dołącz następujące `spring-cloud-starter-sleuth` i `spring-cloud-starter-zipkin` zależności w sekcji zależności pliku pliku pom. XML:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 Musisz również włączyć wystąpienie usługi Azure Application Insights, aby współpracowało z wystąpieniem usług w chmurze sieci Azure ze sprężyną. Zapoznaj się z [samouczkiem dotyczącym śledzenia rozproszonego](spring-cloud-tutorial-distributed-tracing.md) , aby dowiedzieć się, jak używać Application Insights z chmurą Azure wiosną.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób konfigurowania aplikacji ze sprężyną Java na potrzeby wdrażania w chmurze Azure wiosennej. Aby dowiedzieć się, jak skonfigurować wystąpienie serwera konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak skonfigurować wystąpienie serwera konfiguracji](spring-cloud-tutorial-config-server.md)

Więcej przykładów można znaleźć w witrynie GitHub: [przykłady chmur usługi Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

---
title: Przygotowywanie aplikacji sprężynowej do wdrożenia w chmurze Azure wiosennej | Microsoft Docs
description: W tym przewodniku szybki start przygotowano aplikację ze sprężyną Java do wdrożenia.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 6c3048fd27c3d3d4fdba0841fb4ab21bd10e15fe
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721254"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Samouczek: przygotowanie aplikacji ze sprężyną Java do wdrożenia w chmurze Azure wiosennej

Ten przewodnik Szybki Start przedstawia sposób przygotowania istniejącej aplikacji w chmurze ze sprężyną Java na potrzeby wdrożenia w chmurze z możliwością korzystania z platformy Azure.  Prawidłowo skonfigurowano chmurę z usługą Azure wiosną, która zapewnia niezawodne usługi do monitorowania, skalowania i aktualizowania aplikacji w chmurze wiosny. 

## <a name="java-runtime-version"></a>Wersja środowiska uruchomieniowego języka Java

Tylko aplikacje sprężynowe/Java mogą działać w chmurze Azure wiosennej.

Obsługiwane są zarówno Java 8, jak i Java 11. Środowisko hostingu zawiera najnowszą Azul Zulu OpenJDK dla platformy Azure. Zapoznaj się z [tym artykułem](https://docs.microsoft.com/azure/java/jdk/java-jdk-install) , aby uzyskać więcej informacji na temat Azul Zulu OpenJDK for Azure. 

## <a name="spring-boot-and-spring-cloud-versions"></a>Sprężynowe wersje chmurowe i sprężynowe

W chmurze Azure wiosennej są obsługiwane tylko aplikacje do rozruchu sprężynowego. Obsługiwane są oba rozruchy sprężynowe 2,0 i 2,1. W poniższej tabeli wymieniono obsługiwane kombinacje chmurowych rozruchowych i sprężynowych.

Wersja sprężyny rozruchowej | Wersja chmury wiosennej
---|---
2.0. x | Finchley. RELEASE
2.1. x | Greenwich. RELEASE

Sprawdź, czy plik `pom.xml` ma zależności chmurowego rozruchowego i sprężynowego zależnie od używanej wersji.

### <a name="version-20"></a>Wersja 2,0:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>Wersja 2,1:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Zależność klienta chmury ze sprężyną Azure

Usługa Azure wiosny Cloud hostuje i zarządza składnikami chmury wiosennej, takimi jak rejestr usługi w chmurze wiosny oraz serwer konfiguracji chmury wiosennej. Uwzględnij bibliotekę kliencką chmury Azure wiosny w zależnościach, aby umożliwić komunikację z wystąpieniem usługi w chmurze ze sprężyną na platformie Azure.

W poniższej tabeli przedstawiono prawidłowe wersje aplikacji w chmurze z rozruchem/sprężyną.

Wersja sprężyny rozruchowej | Wersja chmury wiosennej | Wersja chmury wiosennej platformy Azure
---|---|---
2.0. x | Finchley. RELEASE | 2.0. x
2.1. x | Greenwich. RELEASE | 2.1. x

Dołącz jeden z fragmentów kodu poniżej w `pom.xml`.  Wybierz fragment kodu, którego wersja pasuje do własnych.

### <a name="version-20x"></a>Wersja 2.0. x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>Wersja 2.1. x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Inne wymagane zależności

Aby włączyć wbudowane funkcje chmury Azure wiosennej, aplikacja musi zawierać następujące zależności. Zapewni to, że aplikacja poprawnie skonfiguruje poszczególne składniki.  

### <a name="service-registry"></a>Rejestr usługi

Aby można było korzystać z usługi Azure Managed Registry, należy uwzględnić `spring-cloud-starter-netflix-eureka-client` w `POM.xml`, jak pokazano poniżej.

Punkt końcowy serwera rejestru usługi zostanie automatycznie dodany jako zmienne środowiskowe w aplikacji. Aplikacje będą mogły zarejestrować się na serwerze rejestru usługi i wykryć inne zależne mikrousługi.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Konfiguracja rozproszona

Aby włączyć konfigurację rozproszoną, Uwzględnij `spring-cloud-config-client` w sekcji zależności `pom.xml`.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Nie określaj `spring.cloud.config.enabled=false` w konfiguracji ładowania początkowego, ponieważ spowoduje to zatrzymanie działania aplikacji z serwerem konfiguracji.

### <a name="metrics"></a>Metryki

Uwzględnij `spring-boot-starter-actuator` w sekcji zależności pliku pliku pom. XML. Metryki będą okresowo ściągane z punktów końcowych JMX i mogą być wizualizowane przy użyciu Azure Portal.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Śledzenie rozproszone

Dołącz `spring-cloud-starter-sleuth` i `spring-cloud-starter-zipkin` w sekcji zależności pliku pliku pom. XML jak poniżej. Ponadto musisz włączyć wystąpienie usługi Azure App Insights, aby współpracowało z wystąpieniem usług w chmurze sieci Azure ze sprężyną. Przeczytaj więcej na temat sposobu włączania usługi App Insights w usłudze Azure wiosny Cloud [tutaj](spring-cloud-tutorial-distributed-tracing.md)

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

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób konfigurowania aplikacji ze sprężyną Java na potrzeby wdrażania w chmurze Azure wiosennej.  Aby dowiedzieć się, jak włączyć serwer konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak skonfigurować serwer konfiguracji](spring-cloud-tutorial-config-server.md).


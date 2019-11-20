---
title: Przewodnik Szybki start prezentujący, jak używać usługi Azure App Configuration | Microsoft Docs
description: Przewodnik Szybki start dotyczący korzystania z usługi Azure App Configuration z aplikacjami Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: e8f6f9ca610c515deca6ed1bdbee54f40cacf427
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184936"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Szybki Start: Tworzenie aplikacji ze sprężyną Java przy użyciu konfiguracji aplikacji platformy Azure

W tym przewodniku szybki start dołączysz konfigurację aplikacji platformy Azure do aplikacji ze sprężyną Java w celu scentralizowanego przechowywania i zarządzania ustawieniami aplikacji oddzielonymi od kodu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- Obsługiwany [zestaw Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) w wersji 8.
- System [Apache Maven](https://maven.apache.org/download.cgi) w wersji 3,0 lub nowszej.

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję **Eksplorator konfiguracji** >  **+ Utwórz** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | /application/config.message | Hello |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

## <a name="create-a-spring-boot-app"></a>Tworzenie aplikacji Spring Boot

Używasz [sprężyny Initializr](https://start.spring.io/) do tworzenia nowego projektu sprężyny rozruchowej.

1. Przejdź do <https://start.spring.io/>.

2. Określ następujące opcje:

   * Wygeneruj projekt **Maven** z użyciem języka **Java**.
   * Określ wersję **rozruchu sprężynowego** , która jest równa lub większa niż 2,0.
   * Określ nazwy **Grupa** i **Artefakt** dla swojej aplikacji.
   * Dodaj zależność **Internet**.

3. Po określeniu poprzednich opcji wybierz pozycję **Generuj projekt**. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Po wyodrębnieniu plików w systemie lokalnym, prosta aplikacja rozruchowa sprężyny jest gotowa do edycji. Znajdź plik *pom.xml* w katalogu głównym swojej aplikacji.

2. Otwórz plik *pom.xml* w edytorze tekstu, a następnie dodaj starter Spring Cloud Azure Config do listy `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M5</version>
    </dependency>
    ```

3. Utwórz nowy plik Java o nazwie *MessageProperties.java* w katalogu pakietów swojej aplikacji. Dodaj następujące wiersze:

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. Utwórz nowy plik Java o nazwie *HelloController.java* w katalogu pakietów swojej aplikacji. Dodaj następujące wiersze:

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. Otwórz główny plik Java aplikacji i dodaj element `@EnableConfigurationProperties`, aby włączyć tę funkcję.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Utwórz nowy plik o nazwie `bootstrap.properties` w katalogu zasobów aplikacji, a następnie Dodaj do pliku następujące wiersze. Zastąp przykładowe wartości odpowiednimi właściwościami magazynu konfiguracji aplikacji.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację do rozruchu ze sprężyną przy użyciu Maven i uruchom ją, na przykład:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Po uruchomieniu aplikacji należy użyć *zazwinięcie* do przetestowania aplikacji, na przykład:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Zobaczysz komunikat wprowadzony w magazynie konfiguracji aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji i użył go w aplikacji ze sprężyną Java. Aby uzyskać więcej informacji, zobacz [Sprężyna na platformie Azure](https://docs.microsoft.com/java/azure/spring-framework/). Aby dowiedzieć się, jak za pomocą tożsamości zarządzanej platformy Azure usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)

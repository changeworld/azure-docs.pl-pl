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
ms.openlocfilehash: 9137262ffc7c172e6a99920a7abb31459a8703fe
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408574"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Szybki start: Tworzenie aplikacji Java Spring korzystającej z usługi App Configuration

Usługa Azure App Configuration to zarządzana usługa konfiguracji na platformie Azure. Umożliwia on łatwe przechowywać i zarządzać wszystkie ustawienia aplikacji w jednym miejscu, który jest oddzielony od kodu. W tym przewodniku Szybki start pokazano, jak zintegrować usługę z aplikacją Java Spring.

Wykonaj kroki w tym przewodniku Szybki Start, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałą opcją dostępne w Windows, macOS i platformy Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start, instaluje obsługiwanej [Java Development Kit (JDK)](https://aka.ms/azure-jdks) z wersji 8 i [narzędzia Apache Maven](https://maven.apache.org/) w wersji 3.0 lub nowszej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz **Eksplorator konfiguracji** > **+ Utwórz** można dodać następujące pary klucz wartość:

    | Klucz | Wartość |
    |---|---|
    | /application/config.message | Witaj |

    Pozostaw **etykiety** i **typu zawartości** puste, aby teraz.

## <a name="create-a-spring-boot-app"></a>Tworzenie aplikacji Spring Boot

Możesz użyć [Spring Initializr](https://start.spring.io/) Aby utworzyć nowy projekt aplikacji Spring Boot.

1. Przejdź do <https://start.spring.io/>.

2. Określ następujące opcje:

   * Wygeneruj projekt **Maven** z użyciem języka **Java**.
   * Określ **Spring Boot** wersji, która jest równa lub większa niż 2.0.
   * Określ nazwy **Grupa** i **Artefakt** dla swojej aplikacji.
   * Dodaj zależność **Internet**.

3. Po określeniu poprzednich opcji, wybierz **Generowanie projektu**. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

## <a name="connect-to-an-app-configuration-store"></a>Łączenie do sklepu z aplikacjami konfiguracji

1. Po wyodrębnieniu plików w systemie lokalnym prostą aplikację Spring Boot jest gotowy do edycji. Znajdź plik *pom.xml* w katalogu głównym swojej aplikacji.

2. Otwórz plik *pom.xml* w edytorze tekstu, a następnie dodaj starter Spring Cloud Azure Config do listy `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M3</version>
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

6. Utwórz nowy plik o nazwie `bootstrap.properties` zasobów w katalogu aplikacji i dodaj następujące wiersze do pliku. Zastąp przykładowe wartości odpowiednie właściwości dla magazynu konfiguracji aplikacji.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Tworzenie aplikacji platformy Spring Boot przy użyciu narzędzia Maven i uruchom go, na przykład:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Po uruchomieniu aplikacji użyj *curl* do testowania aplikacji, na przykład:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Zobaczysz komunikat, który został wprowadzony w magazynie konfiguracji aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start został utworzony nowy magazyn konfiguracji aplikacji i jej za pomocą aplikacji Java Spring. Aby uzyskać więcej informacji, zobacz [Spring na platformie Azure](https://docs.microsoft.com/java/azure/spring-framework/).

Aby dowiedzieć się więcej o tym, jak używać konfiguracji aplikacji, przejdź do następnego samouczka, który demonstruje uwierzytelniania.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanych](./howto-integrate-azure-managed-service-identity.md)

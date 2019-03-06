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
ms.openlocfilehash: d607d6cd813b23051e1676153cbb134261bcf5bc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960626"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Szybki start: Tworzenie aplikacji Java Spring korzystającej z usługi App Configuration

Usługa Azure App Configuration to zarządzana usługa konfiguracji na platformie Azure. Dzięki niej możesz łatwo przechowywać wszystkie ustawienia aplikacji w jednym miejscu oddzielonym od kodu i zarządzać tymi ustawieniami. W tym przewodniku Szybki start pokazano, jak zintegrować usługę z aplikacją Java Spring.

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Doskonałym wyborem jest program [Visual Studio Code](https://code.visualstudio.com/), dostępny na platformach Windows, macOS i Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, zainstaluj obsługiwany zestaw [Java Development Kit (JDK)](https://aka.ms/azure-jdks) w wersji 8 i oprogramowanie [Apache Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

1. Aby utworzyć nowy magazyn konfiguracji aplikacji, najpierw zaloguj się do witryny [Azure Portal](https://aka.ms/azconfig/portal). W lewej górnej części strony kliknij polecenie **+ Utwórz zasób**. W polu tekstowym **Wyszukaj w witrynie Marketplace** wpisz ciąg **App Configuration** i naciśnij klawisz **Enter**.

    ![Wyszukiwanie usługi App Configuration](./media/quickstarts/azure-app-configuration-new.png)

2. Kliknij pozycję **App Configuration** w wynikach wyszukiwania, a następnie kliknij pozycję **Utwórz**.

3. Na stronie **App Configuration** > **Utwórz** wprowadź następujące ustawienia:

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa zasobu** | Nazwa unikatowa w skali globalnej | Wprowadź unikatową nazwę zasobu do użycia dla zasobu magazynu konfiguracji aplikacji. Nazwa musi być ciągiem od 1 do 63 znaków i może zawierać tylko cyfry, litery i znak `-`. Na początku ani na końcu nazwy nie może występować znak `-`, a następujące po sobie znaki `-` nie są prawidłowe.  |
    | **Subskrypcja** | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi App Configuration. Jeśli konto ma tylko jedną subskrypcję, zostanie ona automatycznie wybrana i lista rozwijana **Subskrypcja** nie będzie wyświetlana. |
    | **Grupa zasobów** | *AppConfigTestResources* | Wybierz lub utwórz grupę zasobów dla zasobu magazynu konfiguracji aplikacji. Ta grupa jest przydatna do organizowania wielu zasobów, umożliwiając ich jednoczesne usunięcie przez usunięcie grupy zasobów. Więcej informacji można znaleźć w temacie [Używanie grup zasobów do zarządzania zasobami platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
    | **Lokalizacja** | *Środkowe stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany zasób usługi SignalR. Aby uzyskać najlepszą wydajność, zalecamy utworzenie zasobu w tym samym regionie, co inne składniki aplikacji. |

    ![Tworzenie magazynu konfiguracji aplikacji](./media/quickstarts/azure-app-configuration-create.png)

4. Kliknij pozycję **Utwórz**. Wdrożenie może potrwać kilka minut.

5. Po zakończeniu wdrażania kliknij pozycje **Ustawienia** > **Klucze dostępu**. Zanotuj parametry połączenia dla podstawowego klucza tylko do odczytu lub podstawowego klucza do odczytu i zapisu. Użyjesz ich później do skonfigurowania swojej aplikacji pod kątem komunikowania się z nowo utworzonym magazynem konfiguracji aplikacji. Parametry połączenia mają następującą postać:

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    W Twojej aplikacji będzie konieczne użycie pełnych parametrów.

6. Kliknij pozycje **Eksplorator kluczy/wartości** i **+ Utwórz**, aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | /application/config.message | Hello |

    Pola **Etykieta** i **Typ zawartości** póki co pozostaną puste.

## <a name="create-a-spring-boot-app"></a>Tworzenie aplikacji Spring Boot

Użyjesz programu [Spring Initializr](https://start.spring.io/) do utworzenia nowego projektu Spring Boot.

1. Przejdź do <https://start.spring.io/>.

2. Określ następujące opcje:

   * Wygeneruj projekt **Maven** z użyciem języka **Java**.
   * Określ wersję narzędzia **Spring Boot** równą lub większą niż 2.0.
   * Określ nazwy **Grupa** i **Artefakt** dla swojej aplikacji.
   * Dodaj zależność **Internet**.

3. Po określeniu opcji wymienionych powyżej kliknij pozycję **Generuj projekt**. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

## <a name="connect-to-app-configuration-store"></a>Łączenie z magazynem konfiguracji aplikacji

1. Po wyodrębnieniu plików w systemie lokalnym Twoja prosta aplikacja Spring Boot będzie gotowa do edycji. Znajdź plik *pom.xml* w katalogu głównym swojej aplikacji.

2. Otwórz plik *pom.xml* w edytorze tekstu, a następnie dodaj starter Spring Cloud Azure Config do listy `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.RC2</version>
    </dependency>
    ```

3. Utwórz nowy plik Java o nazwie *MessageProperties.java* w katalogu pakietów swojej aplikacji. Dodaj następujące wiersze.

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

4. Utwórz nowy plik Java o nazwie *HelloController.java* w katalogu pakietów swojej aplikacji. Dodaj następujące wiersze.

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

6. Utwórz nowy plik o nazwie `bootstrap.yaml` w katalogu zasobów Twojej aplikacji, dodaj następujące wiersze do pliku i zastąp przykładowe wartości odpowiednimi właściwościami dla magazynu konfiguracji Twojej aplikacji.

    ```yaml
    spring:
        cloud:
            azure:
                config:
                    stores:
                        - connection-string: [your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Skompiluj swoją aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją, na przykład:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Gdy aplikacja jest uruchomiona, możesz użyć narzędzia *curl* w celu przetestowania swojej aplikacji, na przykład:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Powinien zostać wyświetlony komunikat wprowadzony w magazynie konfiguracji aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start został utworzony nowy magazyn konfiguracji aplikacji i użyto go z aplikacją Java Spring. Odwiedź [stronę główną platformy Spring na platformie Azure](https://docs.microsoft.com/java/azure/spring-framework/), aby uzyskać więcej informacji.

Aby dowiedzieć się więcej o korzystaniu z usługi App Configuration, przejdź do następnego samouczka, w którym zaprezentowano uwierzytelnianie.

> [!div class="nextstepaction"]
> [Tożsamości zarządzane na potrzeby integracji zasobów platformy Azure](./integrate-azure-managed-service-identity.md)
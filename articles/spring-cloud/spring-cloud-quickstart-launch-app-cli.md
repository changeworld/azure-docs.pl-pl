---
title: 'Szybki start: uruchamianie aplikacji Java Spring przy użyciu interfejsu wiersza polecenia platformy Azure'
description: W tym przewodniku Szybki start można wdrożyć przykładową aplikację do usługi Azure Spring Cloud w interfejsie wiersza polecenia platformy Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: c05e53bd8ad8ade8c1e42729f46c99a0059c4dce
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470864"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Szybki start: uruchamianie aplikacji Java Spring przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure Spring Cloud umożliwia łatwe uruchamianie aplikacji opartych na mikrousługach opartych na wiosennym rozruchu na platformie Azure.

Ten przewodnik Szybki start pokazuje, jak wdrożyć istniejącą aplikację Java Spring Cloud na platformie Azure. Po zakończeniu możesz nadal zarządzać aplikacją za pośrednictwem interfejsu wiersza polecenia platformy Azure lub przełączyć się do korzystania z witryny Azure portal.

Po tym przewodniku Szybki start dowiesz się, jak:

> [!div class="checklist"]
> * Uarowuj instancję usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Tworzenie aplikacji mikrousług lokalnie
> * Wdrażanie każdej mikrousług
> * Przypisywanie publicznego punktu końcowego dla aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

>[!Note]
> Usługa Azure Spring Cloud jest obecnie oferowana jako publiczna wersja zapoznawcza. Publiczne oferty w wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną premierą.  Publiczne funkcje i usługi w wersji zapoznawczej nie są przeznaczone do użytku w wersji produkcyjnej.  Aby uzyskać więcej informacji na temat pomocy technicznej podczas wersji zapoznawców, zapoznaj się z [często zadawanymi pytaniami](https://azure.microsoft.com/support/faq/) lub złóż [wniosek o pomoc techniczną,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) aby dowiedzieć się więcej.

>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule.  Ma wstępnie zainstalowane typowe narzędzia platformy Azure, w tym najnowsze wersje git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli jesteś zalogowany do subskrypcji platformy Azure, uruchom usługę [Azure Cloud Shell](https://shell.azure.com) z shell.azure.com.  Więcej informacji o usłudze Azure Cloud Shell można [znaleźć, czytając naszą dokumentację](../cloud-shell/overview.md)

Aby ukończyć ten przewodnik Szybki start:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
2. [Instalacja JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Instalowanie maven 3.0 lub wyższej](https://maven.apache.org/download.cgi)
4. [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie usługi Azure Spring Cloud dla interfejsu wiersza polecenia platformy Azure za pomocą następującego polecenia

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Aprowizuj wystąpienie usługi w łańce rządowej platformy Azure

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure i wybierz aktywną subskrypcję. Pamiętaj, aby wybrać aktywną subskrypcję, która jest na białej liście dla usługi Azure Spring Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription <Name or ID of subscription from the last step>
    ```

2. Przygotuj nazwę usługi Azure Spring Cloud.  Nazwa musi zawierać od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.

3. Utwórz grupę zasobów zawierającą usługę Azure Spring Cloud.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```

    Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).

4. Otwórz okno interfejsu wiersza polecenia platformy Azure i uruchom następujące polecenia, aby aprowizować wystąpienie usługi Azure Spring Cloud.

    ```azurecli
        az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Wystąpienie usługi zajmie około pięciu minut, aby wdrożyć.

5. Ustaw domyślną nazwę grupy zasobów i nazwę klastra za pomocą następujących poleceń:

    ```azurecli
        az configure --defaults group=<resource group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=provision)

## <a name="setup-your-configuration-server"></a>Konfigurowanie serwera konfiguracji

Zaktualizuj swój config-server z lokalizacją repozytorium git dla naszego projektu:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=config-server)

## <a name="build-the-microservices-applications-locally"></a>Tworzenie aplikacji mikrousług lokalnie

1. Utwórz nowy folder i sklonuj przykładowe repozytorium aplikacji do konta usługi Azure Cloud.  

    ```console
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Zmień katalog i skompiluj projekt.

    ```console
        cd piggymetrics
        mvn clean package -D skipTests
    ```

Kompilacja projektu trwa około 5 minut.  Po zakończeniu należy mieć indywidualne pliki JAR dla każdej usługi w ich odpowiednich folderach.

## <a name="create-the-microservices"></a>Tworzenie mikrousług

Tworzenie mikrousług Spring Cloud przy użyciu plików JAR wbudowanych w poprzednim kroku. Utworzysz trzy mikrousługi: **brama,** **auth-service**i **account-service**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Wdrażanie aplikacji i ustawianie zmiennych środowiskowych

Musimy faktycznie wdrożyć nasze aplikacje na platformie Azure. Użyj następujących poleceń, aby wdrożyć wszystkie trzy aplikacje:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=deploy)

## <a name="assign-public-endpoint-to-gateway"></a>Przypisywanie publicznego punktu końcowego do bramy

Potrzebujemy sposobu na dostęp do aplikacji za pośrednictwem przeglądarki internetowej. Nasza aplikacja bramy potrzebuje publicznego punktu końcowego.

1. Przypisz punkt końcowy za pomocą następującego polecenia:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

2. Kwerenda aplikacji **bramy** dla jej publicznego adresu IP, dzięki czemu można sprawdzić, czy aplikacja jest uruchomiona:

W systemie Linux:

```azurecli
az spring-cloud app show --name gateway | grep url
```

W systemie Windows:

```azurecli
az spring-cloud app show -s <service name> -g <resource group> -n gateway -o table
```

3. Przejdź do adresu URL podanego przez poprzednie polecenie, aby uruchomić aplikację PiggyMetrics.
    ![Zrzut ekranu przedstawiający działa piggymetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Można również przejść do witryny Azure portal, aby znaleźć adres URL. 
1. Przejdź do usługi
2. Wybierz **aplikacje**
3. Wybierz **bramę**

    ![Zrzut ekranu przedstawiający działa piggymetrics](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Znajdź adres URL **gateway Overview** na ![stronie Przegląd bramy Zrzut ekranu z piggymetrics uruchomiony](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono aplikację Spring Cloud z interfejsu wiersza polecenia platformy Azure.  Aby dowiedzieć się więcej o usłudze Azure Spring Cloud, przejdź do samouczka na temat przygotowywania aplikacji do wdrożenia.

> [!div class="nextstepaction"]
> [Przygotowanie aplikacji usługi Azure Spring Cloud do wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)

Więcej przykładów jest dostępnych w usłudze GitHub: [Samples w chmurze w chmurze Azure Spring.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)

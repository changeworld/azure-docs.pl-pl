---
title: Uruchamianie aplikacji w chmurze ze sprężyną Azure przy użyciu Azure Portal
description: Wdróż przykładową aplikację w chmurze Azure wiosennej przy użyciu Azure Portal.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: e6a1dba603cbeea276dc6153f82ae599e3d29b3d
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73101354"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Szybki Start: uruchamianie aplikacji w chmurze ze sprężyną na platformie Azure przy użyciu Azure Portal

Chmura sprężynowa platformy Azure umożliwia łatwe uruchamianie aplikacji mikrousług opartych na chmurze na platformie Azure.

W tym przewodniku szybki start przedstawiono sposób wdrażania istniejącej aplikacji ze sprężyną w chmurze na platformie Azure. [Poniżej znajduje się link](https://github.com/Azure-Samples/PiggyMetrics) do kodu przykładowej aplikacji użytej w tym samouczku. Po zakończeniu udostępniona Przykładowa aplikacja będzie dostępna w trybie online i będzie gotowa do zarządzania za pośrednictwem Azure Portal.

Postępując zgodnie z tym przewodnikiem Szybki Start, dowiesz się, jak:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Lokalne Tworzenie aplikacji mikrousług
> * Wdróż każdą mikrousługę
> * Przypisywanie publicznego punktu końcowego dla aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

>[!Note]
> Przed rozpoczęciem pracy z tym przewodnikiem Szybki Start upewnij się, że Twoja subskrypcja platformy Azure ma dostęp do chmury wiosennej platformy Azure.  Jako usługa w wersji zapoznawczej poprosimy klientów o skontaktowanie się z nami, aby można było dodać subskrypcję do listy dozwolonych.  Jeśli chcesz zapoznać się z możliwościami chmury wiosennej platformy Azure, skontaktuj się z nami za pośrednictwem poczty e-mail: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule.  Zawiera ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

Aby ukończyć ten przewodnik Szybki Start:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
2. [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Zainstaluj Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi)
4. [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi na Azure Portal

1. W przeglądarce sieci Web Otwórz [ten link do chmury wiosennej platformy Azure w Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=AppPlatformExtension#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/Azure%20Spring%20Cloud).

    ![Zrzut ekranu przedstawiający Portal ASC](media/spring-cloud-quickstart-launch-app-portal/goto-portal.png)

1. Wybierz pozycję **chmura ze sprężyną Azure** , aby przejść do strony przegląd. Następnie wybierz przycisk **Utwórz** , aby rozpocząć.

1. Wypełnij formularz, biorąc pod uwagę następujące wytyczne:
    - Nazwa usługi: Określ nazwę wystąpienia usługi.  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.
    - Subskrypcja: wybierz subskrypcję, dla której chcesz naliczać opłaty za ten zasób.  Upewnij się, że ta subskrypcja została dodana do naszej listy dozwolonych chmur Azure wiosennej.
    - Grupa zasobów: najlepszym rozwiązaniem jest utworzenie nowych grup zasobów dla nowych zasobów.
    - Lokalizacja: Wybierz lokalizację wystąpienia usługi. Obecnie obsługiwane lokalizacje to Wschodnie stany USA, zachodnie stany USA 2, Europa Zachodnia i Azja Południowo-Wschodnia.
    
Wdrożenie usługi wymaga około 5 minut.  Po jego wdrożeniu zostanie wyświetlona strona **przeglądu** wystąpienia usługi.

## <a name="set-up-your-configuration-server"></a>Konfigurowanie serwera konfiguracji

1. Przejdź do strony **Przegląd** usługi i wybierz pozycję **serwer konfiguracji**.

1. W sekcji **repozytorium domyślne** ustaw wartość **Identyfikator URI** na "https\://GitHub.com/Azure-Samples/piggymetrics", ustaw wartość **etykieta** na "config", a następnie wybierz pozycję **Zastosuj** , aby zapisać zmiany.

    ![Zrzut ekranu przedstawiający Portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

## <a name="build-and-deploy-microservice-applications"></a>Kompilowanie i wdrażanie aplikacji mikrousług

1. Otwórz okno polecenia i uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na maszynę lokalną.

    ```cli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Skompiluj projekt, uruchamiając poniższe polecenie.

    ```cli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure i ustaw aktywną subskrypcję.

    ```cli
    # Login to Azure CLI
    az login

    # List all subscriptions
    az account list -o table

    # Set active subscription
    az account set --subscription <target subscription ID>
    ```

1. Przypisywanie nazw do grupy zasobów i usługi. Pamiętaj, aby zastąpić symbole zastępcze poniżej nazwą grupy zasobów i nazwą usługi, która została zainicjowana wcześniej w tym samouczku.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Utwórz aplikację `gateway` i Wdróż plik JAR.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. Postępując zgodnie z tym samym wzorcem, Utwórz aplikacje `account-service` i `auth-service` i Wdróż ich pliki JAR.

    ```cli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Wdrożenie aplikacji może zająć kilka minut. Aby upewnić się, że zostały wdrożone, przejdź do bloku **aplikacje** w Azure Portal. Powinna zostać wyświetlona linia każdej z trzech aplikacji.

## <a name="assign-a-public-endpoint-to-gateway"></a>Przypisywanie publicznego punktu końcowego do bramy

1. Otwórz kartę **aplikacje** w menu po lewej stronie.
2. Wybierz aplikację `gateway`, aby wyświetlić stronę **Przegląd** .
3. Wybierz pozycję **Przypisz domenę** , aby przypisać publiczny punkt końcowy do bramy. Może to potrwać kilka minut.

    ![Zrzut ekranu przedstawiający Portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

1. Wprowadź przypisany publiczny punkt końcowy (oznaczony jako **adres URL**) do przeglądarki, aby wyświetlić uruchomioną aplikację.

    ![Zrzut ekranu przedstawiający Portal ASC](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Lokalne Tworzenie aplikacji mikrousług
> * Wdróż każdą mikrousługę
> * Przypisywanie publicznego punktu końcowego dla bramy aplikacji

> [!div class="nextstepaction"]
> [Przygotowywanie aplikacji w chmurze platformy Azure pod kątem wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)

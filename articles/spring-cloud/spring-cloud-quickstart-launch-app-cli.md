---
title: Uruchamianie aplikacji ze sprężyną Java przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku szybki start wdrożono przykładową aplikację w chmurze Azure wiosennej w interfejsie wiersza polecenia platformy Azure.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 2bb8932ec60c614870f669b4ee98f3800abfe202
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163650"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Szybki Start: uruchamianie aplikacji ze sprężyną Java przy użyciu interfejsu wiersza polecenia platformy Azure

Chmura sprężynowa platformy Azure umożliwia łatwe uruchamianie aplikacji mikrousług opartych na rozruchu wiosny na platformie Azure.

W tym przewodniku szybki start przedstawiono sposób wdrażania istniejącej aplikacji w chmurze ze sprężyną Java na platformie Azure. Po zakończeniu można nadal zarządzać aplikacją za pośrednictwem interfejsu wiersza polecenia platformy Azure lub przełączać się do korzystania z Azure Portal.

Postępując zgodnie z tym przewodnikiem Szybki Start, dowiesz się, jak:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Lokalne Tworzenie aplikacji mikrousług
> * Wdróż każdą mikrousługę
> * Przypisywanie publicznego punktu końcowego dla aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

>[!Note]
> Przed rozpoczęciem pracy z tym przewodnikiem Szybki Start upewnij się, że Twoja subskrypcja platformy Azure ma dostęp do chmury wiosennej platformy Azure.  Jako usługa w wersji zapoznawczej prosimy o skontaktowanie się z nami, aby można było dodać subskrypcję do listy dozwolonych.  Jeśli chcesz poznać możliwości chmury Azure wiosennej, [Wypełnij ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u).  Chociaż chmura Wiosenna platformy Azure jest dostępna w wersji zapoznawczej, firma Microsoft oferuje ograniczoną pomoc techniczną bez umowy SLA.  Aby uzyskać więcej informacji na temat pomocy technicznej w przypadku wersji zapoznawczych, zapoznaj się z tematem [często zadawanych pytań](https://azure.microsoft.com/support/faq/).

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

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi w interfejsie wiersza polecenia platformy Azure

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure i wybierz aktywną subskrypcję. Pamiętaj, aby wybrać aktywną subskrypcję, która jest listy dozwolonych dla chmury wiosennej platformy Azure

    ```azurecli
        az login
        az account list -o table
        az account set --subscription
    ```

2. Przygotuj nazwę usługi w chmurze Azure wiosennej.  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.

3. Utwórz grupę zasobów, która będzie zawierać swoją usługę w chmurze platformy Azure.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```
    Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).

4. Otwórz okno interfejsu wiersza polecenia platformy Azure i uruchom następujące polecenia, aby zainicjować obsługę chmury wiosennej platformy Azure.

    ```azurecli
        az spring-cloud create -n <service name> -g <resource group name>
    ```

    Wdrożenie wystąpienia usługi potrwa około 5 minut.

5. Ustaw domyślną nazwę grupy zasobów i nazwę klastra przy użyciu następujących poleceń:

    ```azurecli
        az configure --defaults group=<service group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

## <a name="setup-your-configuration-server"></a>Skonfiguruj serwer konfiguracji

Zaktualizuj konfigurację serwera, używając lokalizacji repozytorium git dla naszego projektu:

```git
az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/Azure-Samples/piggymetrics --label config
```

## <a name="build-the-microservices-applications-locally"></a>Lokalne Tworzenie aplikacji mikrousług

1. Utwórz nowy folder i Sklonuj repozytorium przykładowej aplikacji na koncie w chmurze platformy Azure.  

    ```azurecli
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Zmień katalog i skompiluj projekt.

    ```azurecli
        cd PiggyMetrics
        mvn clean package -D skipTests
    ```

Kompilowanie projektu trwa około 5 minut.  Po ukończeniu należy mieć pojedyncze pliki JAR dla każdej usługi w odpowiednich folderach.

## <a name="create-the-microservices"></a>Tworzenie mikrousług

Twórz mikrousługi w chmurze wiosny przy użyciu plików JAR utworzonych w poprzednim kroku. Utworzysz trzy mikrousługi: **brama**, **uwierzytelnianie-usługa**i **konto usługi**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Wdrażanie aplikacji i Ustawianie zmiennych środowiskowych

Musimy faktycznie wdrożyć nasze aplikacje na platformie Azure. Użyj następujących poleceń, aby wdrożyć wszystkie trzy aplikacje:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

## <a name="assign-public-endpoint-to-gateway"></a>Przypisz publiczny punkt końcowy do bramy

Potrzebujemy sposobu na dostęp do aplikacji za pośrednictwem przeglądarki sieci Web. Nasza aplikacja bramy wymaga publicznego punktu końcowego, który można przypisać za pomocą następującego polecenia:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

Na koniec Zbadaj swój publiczny adres IP aplikacji **bramy** , aby można było sprawdzić, czy aplikacja jest uruchomiona:

```azurecli
az spring-cloud app show --name gateway | grep url
```

Przejdź do adresu URL podanego przez poprzednie polecenie, aby wyświetlić uruchomioną aplikację PiggyMetrics.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono aplikację w chmurze ze sprężyną z poziomu interfejsu wiersza polecenia platformy Azure.  Aby dowiedzieć się więcej na temat chmury Azure wiosennej, przejdź do samouczka dotyczącego przygotowywania aplikacji do wdrożenia.

> [!div class="nextstepaction"]
> [Przygotowywanie aplikacji w chmurze platformy Azure pod kątem wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)

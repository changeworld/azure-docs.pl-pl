---
title: 'Szybki Start: uruchamianie aplikacji w chmurze wiosny z poziomu kodu źródłowego'
description: Dowiedz się, jak uruchomić aplikację w chmurze ze sprężyną Azure bezpośrednio z poziomu kodu źródłowego
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: jeconnoc
ms.openlocfilehash: 573baa242c06868326568a82bc358e136f1ece2c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177958"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>Uruchamianie aplikacji w chmurze wiosny z poziomu kodu źródłowego

Chmura sprężynowa platformy Azure umożliwia łatwe uruchamianie aplikacji mikrousług opartych na chmurze na platformie Azure.

Chmura sprężynowa platformy Azure umożliwia uruchamianie aplikacji bezpośrednio z kodu źródłowego Java lub z wstępnie skompilowanego systemu JAR. Ten artykuł przeprowadzi Cię przez wymagane kroki.

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

Przed rozpoczęciem upewnij się, że Twoja subskrypcja platformy Azure ma wymagane zależności:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
2. [Zainstaluj program JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Zainstaluj Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi)
4. [Zainstalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule.  Zawiera ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi za pomocą interfejsu wiersza polecenia platformy Azure

Zaloguj się do interfejsu wiersza polecenia platformy Azure i wybierz aktywną subskrypcję. Pamiętaj, aby wybrać aktywną subskrypcję, która jest listy dozwolonych dla chmury wiosennej platformy Azure

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Otwórz okno interfejsu wiersza polecenia platformy Azure i uruchom następujące polecenia, aby zainicjować obsługę chmury wiosennej platformy Azure. Zwróć uwagę na to, że firma Microsoft informuje również chmurę Azure o konieczności przypisania domeny publicznej.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

Wdrożenie wystąpienia usługi zajmie około pięciu minut.

Ustaw domyślną nazwę grupy zasobów i nazwę klastra przy użyciu następujących poleceń:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>Tworzenie aplikacji w chmurze wiosny

Następujące polecenie tworzy aplikację w chmurze wiosny w Twojej subskrypcji.  Spowoduje to utworzenie pustej usługi w chmurze wiosny, do której możemy przekazać aplikację.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Wdróż aplikację w chmurze wiosny

Aplikację można wdrożyć z poziomu wstępnie skompilowanego JAR lub z repozytorium Gradle lub Maven.  Znajdź instrukcje dla każdego przypadku poniżej.

### <a name="deploy-a-built-jar"></a>Wdrażanie wbudowanego JAR

Aby wdrożyć aplikację z poziomu systemu JAR skompilowanego na komputerze lokalnym, upewnij się, że Twoja kompilacja produkuje plik [jar](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Aby wdrożyć plik FAT-JAR w aktywnym wdrożeniu

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Aby wdrożyć plik FAT-JAR w określonym wdrożeniu

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Wdróż z kodu źródłowego

Chmura sprężynowa platformy Azure używa [kpack](https://github.com/pivotal/kpack) do kompilowania projektu.  Możesz użyć interfejsu wiersza polecenia platformy Azure, aby przekazać kod źródłowy, skompilować projekt przy użyciu kpack i wdrożyć go w aplikacji docelowej.

> [!WARNING]
> Projekt musi generować tylko jeden plik JAR z wpisem `main-class` w `MANIFEST.MF` w `target` (dla wdrożeń Maven lub `build/libs` (dla wdrożeń Gradle).  Wiele plików JAR z wpisami `main-class` spowoduje niepowodzenie wdrożenia.

W przypadku projektów Maven/Gradle o pojedynczym module:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

W przypadku projektów Maven/Gradle z wieloma modułami Powtórz te czynności dla każdego modułu:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Pokaż dzienniki wdrożenia

Przejrzyj dzienniki kompilacji kpack za pomocą następującego polecenia:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> W dziennikach kpack będzie wyświetlane tylko najnowsze wdrożenie, jeśli wdrożenie zostało skompilowane ze źródła przy użyciu kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Przypisywanie publicznego punktu końcowego do bramy

1. Otwórz stronę **pulpitu nawigacyjnego aplikacji** .
2. Wybierz aplikację `gateway`, aby wyświetlić stronę **Szczegóły aplikacji** .
3. Wybierz pozycję **Przypisz domenę** , aby przypisać publiczny punkt końcowy do bramy. Może to potrwać kilka minut. 
4. Wprowadź przypisany publiczny adres IP do przeglądarki, aby wyświetlić uruchomioną aplikację.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Lokalne Tworzenie aplikacji mikrousług
> * Wdróż każdą mikrousługę
> * Edytuj zmienne środowiskowe dla aplikacji
> * Przypisywanie publicznego adresu IP do bramy aplikacji

> [!div class="nextstepaction"]
> [Przygotowywanie aplikacji w chmurze platformy Azure pod kątem wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)

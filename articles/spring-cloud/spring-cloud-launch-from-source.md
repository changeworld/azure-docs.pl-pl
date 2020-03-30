---
title: Szybki start — uruchom aplikację Spring Cloud z kodu źródłowego
description: W tym przewodniku Szybki start dowiedz się, jak uruchomić aplikację usługi Azure Spring Cloud bezpośrednio z kodu źródłowego
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: brendm
ms.openlocfilehash: 3ab4b1729ea380671b72a9bb01740930a186d5c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470798"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Szybki start: Uruchom aplikację Spring Cloud z kodu źródłowego

Usługa Azure Spring Cloud umożliwia łatwe uruchamianie aplikacji opartych na chmurze Spring Cloud na platformie Azure.

Usługa Azure Spring Cloud umożliwia uruchamianie aplikacji bezpośrednio z kodu źródłowego środowiska Java lub z wstępnie utworzonego pliku JAR. W tym artykule otrzymasz od ciebie wymagane kroki.

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

Przed rozpoczęciem upewnij się, że subskrypcja platformy Azure ma wymagane zależności:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
2. [Instalacja JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Instalowanie maven 3.0 lub wyższej](https://maven.apache.org/download.cgi)
4. [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule.  Ma wstępnie zainstalowane typowe narzędzia platformy Azure, w tym najnowsze wersje git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli jesteś zalogowany do subskrypcji platformy Azure, uruchom usługę [Azure Cloud Shell](https://shell.azure.com) z shell.azure.com.  Więcej informacji o usłudze Azure Cloud Shell można [znaleźć, czytając naszą dokumentację](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie usługi Azure Spring Cloud dla interfejsu wiersza polecenia platformy Azure za pomocą następującego polecenia

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Aprowizuj wystąpienie usługi przy użyciu interfejsu wiersza polecenia platformy Azure

Zaloguj się do interfejsu wiersza polecenia platformy Azure i wybierz aktywną subskrypcję. Pamiętaj, aby wybrać aktywną subskrypcję, która jest na białej liście dla usługi Azure Spring Cloud

```azurecli
az login
az account list -o table
az account set --subscription
```

Utwórz grupę zasobów zawierającą usługę Azure Spring Cloud. Więcej informacji o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Uruchom następujące polecenia, aby aprowizować wystąpienie usługi Azure Spring Cloud. Przygotuj nazwę usługi Azure Spring Cloud. Nazwa musi zawierać od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

Wystąpienie usługi zajmie około pięciu minut, aby wdrożyć.

Ustaw domyślną nazwę grupy zasobów i nazwę klastra za pomocą następujących poleceń:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=provision)

## <a name="create-the-spring-cloud-application"></a>Tworzenie aplikacji Spring Cloud

Następujące polecenie tworzy aplikację Spring Cloud w ramach subskrypcji.  Spowoduje to utworzenie pustej usługi Spring Cloud, do której możemy przesłać naszą aplikację.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Wdrażanie aplikacji Spring Cloud

Aplikację można wdrożyć z wbudowanego jar lub z repozytorium Gradle lub Maven.  Instrukcje dotyczące każdego przypadku znajdziesz poniżej.

### <a name="deploy-a-built-jar"></a>Wdrażanie wbudowanego JAR

Aby wdrożyć z JAR zbudowany na komputerze lokalnym, upewnij się, że kompilacja produkuje [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Aby wdrożyć fat-JAR do aktywnego wdrożenia

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Aby wdrożyć fat-JAR do określonego wdrożenia

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Wdrażanie z kodu źródłowego

Usługa Azure Spring Cloud używa [kpack](https://github.com/pivotal/kpack) do tworzenia projektu.  Za pomocą interfejsu wiersza polecenia platformy Azure można przekazać kod źródłowy, utworzyć projekt przy użyciu kpack i wdrożyć go w aplikacji docelowej.

> [!WARNING]
> Projekt musi utworzyć tylko jeden `main-class` plik JAR `MANIFEST.MF` `target` z wpisem w `build/libs` wejściu (dla wdrożeń Maven lub (dla wdrożeń Gradle).  Wiele plików `main-class` JAR z wpisami spowoduje niepowodzenie wdrożenia.

Dla pojedynczych modułów Maven / Gradle projektów:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

W przypadku projektów Maven / Gradle z wieloma modułami powtórz dla każdego modułu:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Pokaż dzienniki wdrażania

Przejrzyj dzienniki kompilacji kpack za pomocą następującego polecenia:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Dzienniki kpack pokaże najnowsze wdrożenie tylko wtedy, gdy to wdrożenie zostało zbudowane ze źródła przy użyciu kpack.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Przypisywanie publicznego punktu końcowego do bramy

1. Otwórz stronę **Pulpit nawigacyjny aplikacji.**
2. Wybierz `gateway` aplikację, aby wyświetlić stronę **Szczegóły aplikacji.**
3. Wybierz **pozycję Przypisz domenę,** aby przypisać publiczny punkt końcowy do bramy. Może to kilka minut. 
4. Wprowadź przypisany publiczny adres IP w przeglądarce, aby wyświetlić uruchomieniowe aplikacje.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uarowuj instancję usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Tworzenie aplikacji mikrousług lokalnie
> * Wdrażanie każdej mikrousług
> * Edytowanie zmiennych środowiskowych dla aplikacji
> * Przypisywanie publicznego adresu IP dla bramy aplikacji

> [!div class="nextstepaction"]
> [Przygotowanie aplikacji usługi Azure Spring Cloud do wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)

Więcej przykładów jest dostępnych w usłudze GitHub: [Samples w chmurze w chmurze Azure Spring.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)

---
title: 'Szybki Start: uruchamianie aplikacji przy użyciu chmury wiosennej platformy Azure Maven'
description: Uruchamianie przykładowej aplikacji przy użyciu Maven
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: d2b9592656d00b38c18b8c9e07540ccb5913766c
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039056"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Szybki Start: uruchamianie aplikacji w chmurze ze sprężyną na platformie Azure przy użyciu wtyczki Maven

Wtyczka Azure wiosny Cloud Maven umożliwia łatwe tworzenie i aktualizowanie aplikacji usługi w chmurze Azure wiosennej. Wstępnie definiując konfigurację, można wdrażać aplikacje w istniejącej usłudze w chmurze platformy Azure. W tym artykule używamy przykładowej aplikacji o nazwie PiggyMetrics do zademonstrowania tej funkcji.

>[!Note]
> Przed rozpoczęciem pracy z tym przewodnikiem Szybki Start upewnij się, że Twoja subskrypcja platformy Azure ma dostęp do chmury wiosennej platformy Azure.  Jako usługa w wersji zapoznawczej poprosimy klientów o skontaktowanie się z nami, aby można było dodać subskrypcję do listy dozwolonych.  Jeśli chcesz zapoznać się z możliwościami chmury wiosennej platformy Azure, skontaktuj się z nami za pośrednictwem poczty e-mail: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule.  Zawiera ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

Aby ukończyć ten przewodnik Szybki Start:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
2. [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Zainstaluj Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi)
4. [Zainstalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi na Azure Portal

1. W przeglądarce sieci Web Otwórz [Azure Portal](https://portal.azure.com)i zaloguj się na swoim koncie.

1. Wyszukaj **chmurę ze sprężyną Azure** i wybierz ją, aby przejść do strony przegląd. Wybierz przycisk **Utwórz** , aby rozpocząć.

1. Wypełnij formularz, biorąc pod uwagę następujące wytyczne:
    - Nazwa usługi: Określ nazwę wystąpienia usługi.  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.
    - Subskrypcja: wybierz subskrypcję, dla której chcesz naliczać opłaty za ten zasób.  Upewnij się, że ta subskrypcja została dodana do naszej listy dozwolonych chmur Azure wiosennej.
    - Grupa zasobów: najlepszym rozwiązaniem jest utworzenie nowych grup zasobów dla nowych zasobów.
    - Lokalizacja: Wybierz lokalizację wystąpienia usługi. Obecnie obsługiwane lokalizacje to Wschodnie stany USA, zachodnie stany USA 2, Europa Zachodnia i Azja Południowo-Wschodnia.
    
Wdrożenie usługi wymaga około 5 minut.  Po jego wdrożeniu zostanie wyświetlona strona **przeglądu** wystąpienia usługi.

## <a name="set-up-your-configuration-server"></a>Konfigurowanie serwera konfiguracji

1. Przejdź do strony **Przegląd** usługi i wybierz pozycję **serwer konfiguracji**.
1. W sekcji **repozytorium domyślne** ustaw wartość **Identyfikator URI** na "https://github.com/Azure-Samples/piggymetrics", ustaw **etykietę** na "config", a następnie wybierz pozycję **Zastosuj** , aby zapisać zmiany.

## <a name="clone-and-build-the-sample-application-repository"></a>Klonowanie i kompilowanie przykładowego repozytorium aplikacji

1. Klonowanie repozytorium git przez uruchomienie następującego polecenia.

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Zmień katalog i skompiluj projekt, uruchamiając następujące polecenie.

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Generowanie i wdrażanie konfiguracji chmury wiosennej na platformie Azure

1. Dodaj następujące elementy do `pom.xml` lub `settings.xml`, aby umożliwić Maven pracy z chmurą wiosenną platformy Azure.

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. Wygeneruj konfigurację, uruchamiając następujące polecenie.

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. Wybierz moduły `gateway`, `auth-service` i `account-service`.

    1. Wybierz subskrypcję i klaster usługi w chmurze Azure wiosennej.

    1. Z dostarczonej listy projektów wprowadź numer, który odpowiada `gateway`, aby przyznać mu publiczny dostęp.
    
    1. Potwierdź konfigurację.

1. Wdróż aplikacje za pomocą następującego polecenia:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Dostęp do usługi PiggyMetrics można uzyskać przy użyciu adresu URL podanego w danych wyjściowych poprzedniego polecenia.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono aplikację w chmurze ze sprężyną z repozytorium Maven.  Aby dowiedzieć się więcej na temat chmury Azure wiosennej, przejdź do samouczka dotyczącego przygotowywania aplikacji do wdrożenia.

> [!div class="nextstepaction"]
> [Przygotowywanie aplikacji w chmurze platformy Azure pod kątem wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)

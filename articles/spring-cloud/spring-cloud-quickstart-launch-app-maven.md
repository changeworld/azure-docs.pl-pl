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
ms.openlocfilehash: ce07d43a289cf527664b120dd832cf832fb2b05e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161401"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>Szybki Start: uruchamianie aplikacji w chmurze ze sprężyną na platformie Azure przy użyciu wtyczki Maven

Dzięki usłudze Azure wiosny Cloud Maven można łatwo tworzyć i aktualizować aplikacje usług w chmurze platformy Azure. Dzięki wstępnemu definiowaniu konfiguracji można wdrażać aplikacje w istniejącej usłudze w chmurze platformy Azure. W tym artykule przedstawiono tę funkcję za pomocą przykładowej aplikacji o nazwie PiggyMetrics.

>[!Note]
> Przed rozpoczęciem pracy z tym przewodnikiem Szybki Start upewnij się, że Twoja subskrypcja platformy Azure ma dostęp do chmury wiosennej platformy Azure. Jako usługa w wersji zapoznawczej zachęcamy Cię do skontaktowania się z nami, aby można było dodać subskrypcję do listy dozwolonych. Jeśli chcesz poznać możliwości chmury Azure wiosny, Wypełnij i prześlij [chmurę Azure wiosennej (prywatna wersja zapoznawcza) — forma zainteresowania](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u). Chociaż chmura Wiosenna platformy Azure jest dostępna w wersji zapoznawczej, firma Microsoft oferuje ograniczoną pomoc techniczną bez umowy SLA.  Aby uzyskać więcej informacji na temat pomocy technicznej w przypadku wersji zapoznawczych, zapoznaj się z tematem [często zadawanych pytań](https://azure.microsoft.com/support/faq/).

>[!TIP]
> Azure Cloud Shell to bezpłatna interaktywna powłoka, której można użyć do uruchomienia poleceń z tego artykułu. Ma ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje narzędzia Git, zestawu Java Development Kit (JDK), Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com). Aby uzyskać więcej informacji, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md).

Aby ukończyć ten przewodnik Szybki Start:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/).
2. [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Zainstaluj program Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi).
4. [Zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
5. [Zarejestruj się, aby skorzystać z bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi na Azure Portal

1. W przeglądarce sieci Web Otwórz [Azure Portal](https://portal.azure.com)i zaloguj się na swoim koncie.

1. Wyszukaj, a następnie wybierz pozycję **chmura sprężynowa platformy Azure**. 
1. Na stronie Przegląd wybierz pozycję **Utwórz**, a następnie wykonaj następujące czynności:  

    a. W polu **nazwa usługi** Określ nazwę wystąpienia usługi. Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.  

    b. Z listy rozwijanej **subskrypcja** wybierz subskrypcję, dla której chcesz naliczać opłaty za ten zasób. Upewnij się, że ta subskrypcja została dodana do listy dozwolonych w chmurze Azure wiosennej.  

    d. W polu **Grupa zasobów** Utwórz nową grupę zasobów. Najlepszym rozwiązaniem jest utworzenie grup zasobów dla nowych zasobów.  

    d. Z listy rozwijanej **Lokalizacja** wybierz lokalizację wystąpienia usługi. Obecnie obsługiwane lokalizacje to Wschodnie stany USA, zachodnie stany USA 2, Europa Zachodnia i Azja Południowo-Wschodnia.
    
Wdrożenie usługi wymaga około 5 minut. Po wdrożeniu usługi zostanie wyświetlona strona **przeglądu** wystąpienia usługi.

## <a name="set-up-your-configuration-server"></a>Konfigurowanie serwera konfiguracji

1. Na stronie **Przegląd** usługi wybierz opcję **serwer konfiguracji**.
1. W sekcji **repozytorium domyślne** Ustaw **identyfikator URI** na **https://github.com/Azure-Samples/piggymetrics** , ustaw wartość **etykieta** na **config**, a następnie wybierz pozycję **Zastosuj** , aby zapisać zmiany.

## <a name="clone-and-build-the-sample-application-repository"></a>Klonowanie i kompilowanie przykładowego repozytorium aplikacji

1. Sklonuj repozytorium git, uruchamiając następujące polecenie:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Zmień katalog i skompiluj projekt, uruchamiając następujące polecenie:

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Generowanie i wdrażanie konfiguracji chmury wiosennej na platformie Azure

1. Aby umożliwić Maven współpracują z chmurą Azure wiosną, Dodaj następujący kod do pliku *pliku pom. XML* lub *Settings. XML* .

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

1. Wygeneruj konfigurację, uruchamiając następujące polecenie:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. Wybierz moduły `gateway`, `auth-service` i `account-service`.

    b. Wybierz subskrypcję i klaster usługi w chmurze Azure wiosennej.

    d. Na liście podanych projektów wprowadź numer, który odpowiada `gateway`, aby przyznać mu publiczny dostęp.
    
    d. Potwierdź konfigurację.

1. Wdróż aplikacje za pomocą następującego polecenia:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Dostęp do usługi PiggyMetrics można uzyskać przy użyciu adresu URL podanego w danych wyjściowych poprzedniego polecenia.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono aplikację w chmurze ze sprężyną z repozytorium Maven. Aby dowiedzieć się więcej na temat chmury Azure wiosennej, przejdź do samouczka dotyczącego przygotowywania aplikacji do wdrożenia.

> [!div class="nextstepaction"]
> [Przygotowywanie aplikacji w chmurze platformy Azure pod kątem wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)

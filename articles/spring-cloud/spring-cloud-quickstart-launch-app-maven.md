---
title: Szybki Start — uruchamianie aplikacji przy użyciu usługi Maven z chmurą Azure wiosną
description: W tym przewodniku Szybki Start Uruchom przykładową aplikację przy użyciu Maven
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 26b53e897d70e596bcd770e99d2d29ac16c52755
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75707082"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Szybki Start: uruchamianie aplikacji w chmurze ze sprężyną na platformie Azure przy użyciu wtyczki Maven

Korzystając z wtyczki usługi Azure wiosennej w chmurze Maven, możesz łatwo tworzyć i aktualizować aplikacje w chmurze platformy Azure. Dzięki wstępnemu definiowaniu konfiguracji można wdrażać aplikacje w istniejącej usłudze w chmurze platformy Azure. W tym artykule przedstawiono tę funkcję za pomocą przykładowej aplikacji o nazwie PiggyMetrics.

Postępując zgodnie z tym przewodnikiem Szybki Start, dowiesz się, jak:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Konfigurowanie serwera konfiguracji dla wystąpienia
> * Lokalne klonowanie i kompilowanie aplikacji mikrousług
> * Wdróż każdą mikrousługę
> * Przypisywanie publicznego punktu końcowego aplikacji

>[!Note]
> Chmura Wiosenna platformy Azure jest obecnie oferowana jako publiczna wersja zapoznawcza. Oferty publicznej wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną wersją.  Funkcje i usługi publicznej wersji zapoznawczej nie są przeznaczone do użytku produkcyjnego.  Aby uzyskać więcej informacji na temat pomocy technicznej w przypadku wersji zapoznawczych, zapoznaj się z naszymi [często zadawanymi pytaniami](https://azure.microsoft.com/support/faq/) lub zapoznaj się z [support requestą](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)


>[!TIP]
> Azure Cloud Shell to bezpłatna interaktywna powłoka, której można użyć do uruchomienia poleceń z tego artykułu. Ma ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje narzędzia Git, zestawu Java Development Kit (JDK), Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com). Aby uzyskać więcej informacji, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md).

Aby ukończyć ten przewodnik Szybki start:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/).
2. [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Zainstaluj program Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi).
4. [Zarejestruj się, aby skorzystać z bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/).

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi na Azure Portal

1. W przeglądarce sieci Web Otwórz [ten link do chmury Azure wiosny w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)i zaloguj się na swoim koncie.

1. Podaj **szczegóły projektu** dla przykładowej aplikacji w następujący sposób:

    1. Wybierz **subskrypcję** , z którą aplikacja zostanie skojarzona.
    1. Wybierz lub Utwórz grupę zasobów dla aplikacji. Zalecamy utworzenie nowej grupy zasobów.  W poniższym przykładzie pokazano nową grupę zasobów o nazwie `myspringservice`.
    1. Podaj nazwę nowej usługi w chmurze Azure wiosennej.  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.  Usługa w poniższym przykładzie ma nazwę `contosospringcloud`.
    1. Wybierz lokalizację aplikacji z dostępnych opcji.  W tym przykładzie wybieramy `East US`.
    1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć Podsumowanie nowej usługi.  Jeśli wszystko wygląda poprawnie, wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![wybierz pozycję Recenzja + Utwórz](media/maven-qs-review-create.jpg)

Wdrożenie usługi wymaga około 5 minut. Po wdrożeniu usługi wybierz pozycję **Przejdź do zasobu** , a zostanie wyświetlona strona **Omówienie** wystąpienia usługi.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Konfigurowanie serwera konfiguracji

1. Na stronie **Przegląd** usługi wybierz opcję **serwer konfiguracji**.
1. W sekcji **repozytorium domyślne** Ustaw **identyfikator URI** na **https://github.com/Azure-Samples/piggymetrics** , ustaw wartość **etykieta** na **config**, a następnie wybierz pozycję **Zastosuj** , aby zapisać zmiany.

    > [!div class="mx-imgBorder"]
    > ![zdefiniować i zastosować ustawienia konfiguracji](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>Klonowanie i kompilowanie przykładowego repozytorium aplikacji

1. Uruchom [Azure Cloud Shell](https://shell.azure.com).

1. Sklonuj repozytorium git, uruchamiając następujące polecenie:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Zmień katalog i skompiluj projekt, uruchamiając następujące polecenie:

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generuj konfiguracje i wdrażaj je w chmurze Azure wiosennej

1. Wygeneruj konfiguracje, uruchamiając następujące polecenie w folderze głównym PiggyMetrics zawierającym pliku pom nadrzędny:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.0.0:config
    ```

    a. Wybierz moduły `gateway`,`auth-service`i `account-service`.

    b. Wybierz subskrypcję i klaster usługi w chmurze Azure wiosennej.

    d. Na liście podanych projektów wprowadź numer, który odpowiada `gateway`, aby przyznać mu publiczny dostęp.
    
    d. Potwierdź konfigurację.

1. PLIKU pom zawiera teraz zależności i konfiguracje wtyczki. Wdróż aplikacje za pomocą następującego polecenia:

   ```azurecli
   mvn azure-spring-cloud:deploy
   ```

1. Po zakończeniu wdrażania możesz uzyskać dostęp do PiggyMetrics przy użyciu adresu URL podanego w danych wyjściowych z poprzedniego polecenia.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono aplikację w chmurze ze sprężyną z repozytorium Maven. Aby dowiedzieć się więcej na temat chmury Azure wiosennej, przejdź do samouczka dotyczącego przygotowywania aplikacji do wdrożenia.

> [!div class="nextstepaction"]
> [Przygotuj aplikację w chmurze platformy Azure do wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)
> [Dowiedz się więcej o wtyczkach Maven dla platformy Azure](https://github.com/microsoft/azure-maven-plugin)

Więcej przykładów można znaleźć w witrynie GitHub: [przykłady chmur usługi Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

---
title: Szybki start — uruchamianie istniejącej aplikacji usługi Azure Spring Cloud przy użyciu portalu Azure
description: W tym przewodniku Szybki start wdrożyć aplikację Spring Cloud w chmurze Azure Spring Cloud przy użyciu witryny Azure portal.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: 9cd59fdf81e9b5d56872d20c76e8ea177b3c8577
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470898"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Szybki start: uruchamianie istniejącej aplikacji usługi Azure Spring Cloud przy użyciu portalu Azure

Ten przewodnik Szybki start pokazuje, jak wdrożyć istniejącą aplikację Spring Cloud na platformie Azure. Usługa Azure Spring Cloud umożliwia łatwe uruchamianie aplikacji opartych na chmurze Spring Cloud na platformie Azure. 

Przykładowy kod aplikacji użyty w tym samouczku można znaleźć w naszym [repozytorium próbek GitHub.](https://github.com/Azure-Samples/PiggyMetrics) Po zakończeniu podana przykładowa aplikacja będzie dostępna w trybie online i będzie gotowa do zarządzania za pośrednictwem witryny Azure portal.

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

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Aprowizuj wystąpienie usługi w witrynie Azure portal

1. W nowej karcie otwórz [witrynę Azure portal](https://ms.portal.azure.com/). 

2. W górnym polu wyszukiwania wyszukaj **usługę Azure Spring Cloud**.

3. Wybierz **platformę Azure Spring Cloud** z wyników.

 ![Ikona ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na stronie Usługi Azure Spring Cloud kliknij pozycję **+ Dodaj**.

 ![Ikona ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Wypełnij formularz na stronie Azure Spring Cloud **Create.**  Należy wziąć pod uwagę następujące wskazówki:
    - **Subskrypcja:** Wybierz subskrypcję, która ma być naliczona za ten zasób.  Upewnij się, że ta subskrypcja została dodana do naszej listy dozwolonych dla usługi Azure Spring Cloud.
    - **Grupa zasobów**: Tworzenie nowych grup zasobów dla nowych zasobów jest najlepszym rozwiązaniem.
    - **Szczegóły usługi/nazwa:** Określ nazwę wystąpienia usługi.  Nazwa musi zawierać od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.
    - **Lokalizacja:** Wybierz lokalizację wystąpienia usługi. Obecnie obsługiwane lokalizacje obejmują wschodnie stany USA, zachodnie stany USA 2, Europę Zachodnią i Azję Południowo-Wschodnią.

    ![Uruchomienie portalu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Kliknij kartę **Ustawienia diagnostyczne,** aby otworzyć następujące okno dialogowe.

7. Możesz ustawić **Włącz dzienniki** na *tak* lub *nie* zgodnie z wymaganiami.

    ![Włączanie dzienników](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Kliknij kartę **Śledzenie.**

9. Zgodnie z wymaganiami można ustawić **opcję Włącz śledzenie** na *tak* lub *nie.*  Jeśli ustawisz **Włącz śledzenie** na tak, wybierz również istniejący wgląd w aplikację lub utwórz nowy. Bez specyfikacji **usługi Application Insights** wystąpi błąd sprawdzania poprawności.


    ![Śledzenie](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Kliknij pozycję **Przejrzyj i utwórz**.

11. Sprawdź swoje specyfikacje i kliknij przycisk **Utwórz**.

Trwa około 5 minut dla usługi do wdrożenia.  Po wdrożeniu zostanie wyświetlona strona **Przegląd** wystąpienia usługi.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Konfigurowanie serwera konfiguracji

1. Przejdź do strony **Przegląd** usługi i wybierz pozycję **Config Server**.

2. W sekcji **Domyślne repozytorium** ustawhttps://github.com/Azure-Samples/piggymetrics-configidentyfikator **URI** na " ".

3. Wybierz pozycję **Zastosuj**, aby zapisać zmiany.

    ![Zrzut ekranu przedstawiający portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Tworzenie i wdrażanie aplikacji mikrousług

1. Otwórz [usługę Azure Cloud Shell](https://shell.azure.com) i sklonuj przykładowe repozytorium aplikacji na komputerze lokalnym.  W tym miejscu najpierw tworzymy `source-code` katalog tymczasowy wywoływany przed sklonowaniem aplikacji.

    ```console
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Tworzenie sklonowanego pakietu.

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

3. Przypisz nazwy do grupy zasobów i usługi. Pamiętaj, aby zastąpić symbole zastępcze poniżej nazwą grupy zasobów i nazwą usługi, które zostały zainicjowane wcześniej w tym samouczku.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

4. Utwórz `gateway` aplikację i wdrożyć plik JAR.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

5. Zgodnie z tym samym `account-service` `auth-service` wzorcem, należy utworzyć i aplikacje i wdrożyć ich pliki JAR.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

6. Trwa kilka minut, aby zakończyć wdrażanie aplikacji. Aby potwierdzić, że zostały wdrożone, przejdź do **aplikacji** bloku w witrynie Azure portal. Powinien zostać wyświetlony wiersz każdej z trzech aplikacji.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Przypisywanie publicznego punktu końcowego do bramy

1. Otwórz kartę **Aplikacje** w menu po lewej stronie.

2. Wybierz `gateway` aplikację, aby wyświetlić stronę **Przegląd.**

3. Wybierz **pozycję Przypisz punkt końcowy,** aby przypisać publiczny punkt końcowy do bramy. Może to potrwać kilka minut.

    ![Zrzut ekranu przedstawiający portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Wprowadź przypisany publiczny punkt końcowy (oznaczony **adres URL)** w przeglądarce, aby wyświetlić uruchomianą aplikację.

    ![Zrzut ekranu przedstawiający portal ASC](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uarowuj instancję usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Tworzenie aplikacji mikrousług lokalnie
> * Wdrażanie każdej mikrousług
> * Przypisywanie publicznego punktu końcowego dla bramy aplikacji

> [!div class="nextstepaction"]
> [Przygotowanie aplikacji usługi Azure Spring Cloud do wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)

Więcej przykładów jest dostępnych w usłudze GitHub: [Samples w chmurze w chmurze Azure Spring.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)

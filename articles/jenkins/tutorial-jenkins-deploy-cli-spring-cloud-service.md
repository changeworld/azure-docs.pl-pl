---
title: Wdrażanie aplikacji w usłudze Azure Spring Cloud przy użyciu narzędzia Jenkins i interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure w potoku ciągłej integracji i wdrażania w celu wdrażania mikrousług w usłudze Azure Spring Cloud
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75734978"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Samouczek: Wdrażanie aplikacji w chmurze Azure Spring Cloud przy użyciu usługi Jenkins i interfejsu wiersza polecenia platformy Azure

[Usługa Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) to w pełni zarządzany program rozwoju mikrousług z wbudowanym odnajdywania usług i zarządzania konfiguracją. Usługa ułatwia wdrażanie aplikacji opartych na wiosennym rozruchu mikrousług na platformie Azure. W tym samouczku pokazano, jak można używać interfejsu wiersza polecenia platformy Azure w usłudze Jenkins do automatyzacji ciągłej integracji i dostarczania (CI/CD) dla usługi Azure Spring Cloud.

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Aprowizuj wystąpienie usługi i uruchom aplikację Java Spring
> * Przygotowanie serwera jenkinsa
> * Użyj interfejsu wiersza polecenia platformy Azure w potoku usługi Jenkins do tworzenia i wdrażania aplikacji mikrousług 

W tym samouczku przyjęto założenie, że wiedza pośrednia na temat podstawowych usług platformy Azure, usługi Azure Spring Cloud, [potoków](https://jenkins.io/doc/book/pipeline/) i wtyczek usługi Jenkins oraz usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

>[!Note]
> Usługa Azure Spring Cloud jest obecnie oferowana jako publiczna wersja zapoznawcza. Publiczne oferty w wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną premierą.  Publiczne funkcje i usługi w wersji zapoznawczej nie są przeznaczone do użytku w wersji produkcyjnej.  Aby uzyskać więcej informacji na temat pomocy technicznej podczas wersji zapoznawców, zapoznaj się z [często zadawanymi pytaniami](https://azure.microsoft.com/support/faq/) lub złóż [wniosek o pomoc techniczną,](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) aby dowiedzieć się więcej.

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* Konto usługi GitHub. Jeśli nie masz konta GitHub, utwórz [bezpłatne konto](https://github.com/) przed rozpoczęciem.

* Serwer główny Jenkins. Jeśli nie masz jeszcze wzorca usługi Jenkins, wdrożyć [usługę Jenkins](https://aka.ms/jenkins-on-azure) na platformie Azure, wykonując kroki opisane w tym [przewodniku Szybki start.](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template) Następujące są wymagane w węźle Jenkins/agent (na przykład. serwer kompilacji):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 lub wyższy](https://maven.apache.org/download.cgi)
    * [Zainstalowano narzędzie CLI platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)w wersji 2.0.67 lub nowszej

    >[!TIP]
    > Narzędzia takie jak Git, JDK, Az CLI i Azure plug-ius są domyślnie zawarte w szablonie rozwiązania Microsoft [Jenkins](https://aka.ms/jenkins-on-azure) w portalu Azure Marketplace.
    
* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Aprowizuj wystąpienie usługi i uruchom aplikację Java Spring

Używamy [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) jako przykładowej aplikacji usługi firmy Microsoft i wykonaj te same kroki w [przewodniku Szybki start: Uruchom aplikację Java Spring przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) do inicjowania obsługi administracyjnej wystąpienia usługi i konfigurowania aplikacji. Jeśli przeszedłeś już ten sam proces, możesz przejść do następnej sekcji. W przeciwnym razie uwzględnione w następujących są polecenia interfejsu wiersza polecenia platformy Azure. Zapoznaj się z [przewodnikiem Szybki start: Uruchom aplikację Java Spring przy użyciu interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) aby uzyskać dodatkowe informacje ogólne.

Komputer lokalny musi spełniać te same wymagania wstępne co serwer kompilacji usługi Jenkins. Upewnij się, że są zainstalowane następujące do tworzenia i wdrażania aplikacji mikrousług:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 lub wyższy](https://maven.apache.org/download.cgi)
    * [Zainstalowano narzędzie CLI platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)w wersji 2.0.67 lub nowszej

1. Zainstaluj rozszerzenie usługi Azure Spring Cloud:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Utwórz grupę zasobów zawierającą usługę Azure Spring Cloud:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Aprowizuj wystąpienie usługi Azure Spring Cloud:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Rozwidli repozytorium [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) na własne konto GitHub. Na komputerze lokalnym sklonuj repozytorium `source-code`w katalogu o nazwie:

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Skonfiguruj serwer konfiguracji. Upewnij się, &lt;że twój identyfikator&gt; GitHub jest prawidłowy.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Zbuduj projekt:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Utwórz trzy mikrousługi: **brama,** **usługa eru**i **usługa konta:**

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Wdrażanie aplikacji: 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Przypisywanie publicznego punktu końcowego do bramy:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Kwerenda aplikacji bramy, aby uzyskać adres URL, dzięki czemu można sprawdzić, czy aplikacja jest uruchomiona.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Przejdź do adresu URL podanego przez poprzednie polecenie, aby uruchomić aplikację PiggyMetrics. 

## <a name="prepare-jenkins-server"></a>Przygotowanie serwera Jenkins

W tej sekcji należy przygotować serwer jenkins do uruchomienia kompilacji, co jest w porządku do testowania. Jednak ze względu na wpływ na bezpieczeństwo należy użyć [agenta maszyny Wirtualnej platformy Azure](https://plugins.jenkins.io/azure-vm-agents) lub [agenta kontenera Platformy Azure](https://plugins.jenkins.io/azure-container-agents) do uruchomienia agenta na platformie Azure w celu uruchomienia kompilacji. Aby uzyskać więcej informacji, zobacz artykuł w witrynie Jenkins dotyczący [zagadnień bezpieczeństwa kompilowania w węźle głównym](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Instalowanie wtyczek

1. Zaloguj się na serwerze jenkinsa. Wybierz **pozycję Zarządzaj > Zarządzaj wtyczkami w ujamią**w u portalu Jenkins .
2. Na karcie **Dostępne** wybierz następujące wtyczki:
    * [Integracja z gitHubem](https://plugins.jenkins.io/github-pullrequest)
    * [Poświadczenia platformy Azure](https://plugins.jenkins.io/azure-credentials)

    Jeśli te wtyczki nie są wyświetlane na liście, sprawdź kartę **Zainstalowane,** aby sprawdzić, czy są już zainstalowane.

3. Aby zainstalować wtyczki, wybierz pozycję **Pobierz teraz i zainstaluj po ponownym uruchomieniu**.

4. Uruchom ponownie serwer usługi Jenkins, aby zakończyć instalację.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Dodawanie poświadczeń jednostki usługi Azure w magazynie poświadczeń usługi Jenkins

1. Musisz jednostki usługi Azure do wdrożenia na platformie Azure. Aby uzyskać więcej informacji, zobacz sekcję [Tworzenie jednostki](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) usługi w samouczku Wdrażanie usługi Azure App Service. Dane wyjściowe z `az ad sp create-for-rbac` wygląda mniej więcej tak:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Na pulpicie nawigacyjnym usługi Jenkins wybierz pozycję**System** **poświadczeń** > . Następnie wybierz pozycję **Global credentials (unrestricted)** (Poświadczenia globalne [bez ograniczeń]).

3. Wybierz **pozycję Dodaj poświadczenia**. 

4. Wybierz **jednostkę usługi Microsoft Azure** jako rodzaj.

5. Wartości dostawy dla: * Identyfikator subskrypcji: użyj identyfikatora subskrypcji `appId` platformy Azure * `password` Identyfikator klienta: `tenant` użyj * Klucz tajny klienta: użyj * Identyfikator dzierżawy: użyj * Środowisko platformy Azure: wybierz wstępnie ustawioną wartość. Na przykład użyj **platformy Azure** for Azure Global * ID: ustaw **jako azure_service_principal**. Używamy tego identyfikatora w późniejszym kroku w tym artykule * Opis: jest polem opcjonalnym. Zalecamy zapewnienie znaczącej wartości tutaj.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Zainstaluj rozszerzenie maven i Az CLI sprężyny chmury

Potok próbki używa Maven do kompilacji i Az CLI do wdrożenia w wystąpieniu usługi. Po zainstalowaniu usługi Jenkins tworzy konto administratora o nazwie *jenkins*. Upewnij się, że *jenkins* użytkownika ma uprawnienia do uruchamiania rozszerzenia chmury wiosna.

1. Połącz się z wzorcem jenkinsa za pośrednictwem protokołu SSH. 

2. Zainstaluj Maven

    ```bash
        sudo apt-get install maven 
    ```

3. Zainstaluj interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Narzędzie CLI platformy Azure jest instalowane domyślnie, jeśli używasz [usługi Jenkins Master na platformie Azure](https://aka.ms/jenkins-on-azure).

4. Przełącz `jenkins` się do użytkownika:

    ```bash
        sudo su jenkins
    ```

5. Dodaj rozszerzenie **chmury sprężyny:**

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Tworzenie pliku Jenkinsa
1. W swoim własnymhttps://github.com/&ltrepozytorium (&gt;;your GitHub id /piggymetrics) utwórz **plik Jenkins w** katalogu głównym.

2. Zaktualizuj plik w następujący sposób. Upewnij się, że zastąpisz wartości ** \<nazwy grupy zasobów>** i ** \<nazwy usługi>**. Zastąp **azure_service_principal** odpowiednim identyfikatorem, jeśli używasz innej wartości podczas dodawania poświadczeń w uliczce w uliczce w uliczce. 

```groovy
    node {
      stage('init') {
        checkout scm
      }
      stage('build') {
        sh 'mvn clean package'
      }
      stage('deploy') {
        withCredentials([azureServicePrincipal('azure_service_principal')]) {
          // login to Azure
          sh '''
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            az account set -s $AZURE_SUBSCRIPTION_ID
          '''  
          // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
          sh 'az configure --defaults group=<resource group name>'
          sh 'az configure --defaults spring-cloud=<service name>'
          // Deploy applications
          sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
          sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
          sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
          sh 'az logout'
        }
      }
    }
```

3. Zapisz i zaobję zmianę.

## <a name="create-the-job"></a>Tworzenie zadania

1. Na pulpicie nawigacyjnym usługi Jenkins kliknij pozycję **Nowy element**.

2. Podaj nazwę, *Deploy-PiggyMetrics* dla zadania i wybierz **potok**. Kliknij przycisk OK.

3. Następnie kliknij kartę **Pipeline** (Potok).

4. W polu **Definition** (Definicja) wybierz wartość **Pipeline script from SCM** (Skrypt potoku z menedżera SCM).

5. W polu **SCM** wybierz wartość **Git**.

6. Wprowadź adres URL GitHub dla rozwidłego repozytorium: ** https://github.com/&lt;twój identyfikator GitHub&gt;/piggymetrics.git**

7. Upewnij **się, że specyfikator gałęzi (czarny dla "any")** to ***/Azure**

8. Zachowaj **ścieżkę skryptu** jako **plik Jenkinsa**

7. Kliknij **przycisk Zapisz**

## <a name="validate-and-run-the-job"></a>Sprawdzanie poprawności i uruchamianie zadania

Przed uruchomieniem zadania zaktualizujmy tekst w polu wprowadzania danych logowania, aby **wprowadzić identyfikator logowania**.

1. We własnym repozytorium otwórz `index.html` w **/gateway/src/main/resources/static/**

2. Wyszukaj "wprowadź swój login" i zaktualizuj do "wprowadź identyfikator logowania"

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. Zatwierdzanie zmian

4. Uruchom zadanie w u jenkins ręcznie. Na pulpicie nawigacyjnym usługi Jenkins kliknij zadanie *Deploy-PiggyMetrics,* a następnie **stwórz teraz**.

Po zakończeniu zadania przejdź do publicznego adresu IP aplikacji **bramy** i sprawdź, czy aplikacja została zaktualizowana. 

![Zaktualizowano piggy metryki](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne, usuń zasoby utworzone w tym artykule:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure w usłudze Jenkins do automatyzacji ciągłej integracji i dostarczania (CI/CD) dla usługi Azure Spring Cloud.

Aby dowiedzieć się więcej o dostawcy usługi Azure Jenkins, zobacz witrynę usługi Jenkins w witrynie platformy Azure.

> [!div class="nextstepaction"]
> [Usługa Jenkins na platformie Azure](/azure/jenkins/)

---
title: Wdrażanie aplikacji w chmurze Azure sprężynowej przy użyciu Jenkins i interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak korzystać z interfejsu wiersza polecenia platformy Azure w ciągłej integracji i potoku wdrażania, aby wdrożyć mikrousługi w usłudze w chmurze Azure wiosny
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75734978"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Samouczek: wdrażanie aplikacji w chmurze Azure sprężynowej przy użyciu Jenkins i interfejsu wiersza polecenia platformy Azure

[Chmura sprężynowa platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) to w pełni zarządzane programowanie mikrousług za pomocą wbudowanego zarządzania odnajdywaniem i konfiguracją usług. Usługa ułatwia wdrażanie aplikacji mikrousług opartych na rozruchu sprężyny na platformie Azure. W tym samouczku pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure w Jenkins zautomatyzować ciągłą integrację i dostarczanie (CI/CD) dla chmury wiosennej platformy Azure.

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi i uruchamianie aplikacji ze sprężyną Java
> * Przygotowywanie serwera Jenkins
> * Korzystanie z interfejsu wiersza polecenia platformy Azure w potoku Jenkins, aby kompilować i wdrażać aplikacje mikrousług 

W tym samouczku przyjęto założenie pośredniej wiedzy na temat podstawowych usług platformy Azure, usługi Azure wiosny, [potoków](https://jenkins.io/doc/book/pipeline/) Jenkins oraz dodatków plug-in i GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

>[!Note]
> Chmura Wiosenna platformy Azure jest obecnie oferowana jako publiczna wersja zapoznawcza. Oferty publicznej wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną wersją.  Funkcje i usługi publicznej wersji zapoznawczej nie są przeznaczone do użytku produkcyjnego.  Aby uzyskać więcej informacji na temat pomocy technicznej w przypadku wersji zapoznawczych, zapoznaj się z naszymi [często zadawanymi pytaniami](https://azure.microsoft.com/support/faq/) lub zapoznaj się z [support requestą](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* Konto usługi GitHub. Jeśli nie masz konta w usłudze GitHub, przed rozpoczęciem Utwórz [bezpłatne konto](https://github.com/) .

* Serwer główny Jenkins. Jeśli nie masz jeszcze wzorca Jenkins, wdróż [Jenkins](https://aka.ms/jenkins-on-azure) na platformie Azure, wykonując kroki opisane w tym [przewodniku szybki start](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). Na węźle/agencie Jenkins (na przykład) wymagane są następujące elementy. serwer kompilacji):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi)
    * [Interfejs wiersza polecenia platformy Azure został zainstalowany](/cli/azure/install-azure-cli?view=azure-cli-latest), w wersji 2.0.67 lub nowszej

    >[!TIP]
    > Narzędzia takie jak Git, JDK, AZ CLI i Azure plug-ius są uwzględniane domyślnie w szablonie rozwiązania [Microsoft Jenkins](https://aka.ms/jenkins-on-azure) w portalu Azure Marketplace.
    
* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi i uruchamianie aplikacji ze sprężyną Java

Używamy [metryk Piggy](https://github.com/Azure-Samples/piggymetrics) jako przykładowej aplikacji usługi firmy Microsoft i postępuj zgodnie z tymi samymi krokami w [przewodniku szybki start: uruchamianie aplikacji w języku Java za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) , aby zainicjować obsługę administracyjną wystąpienia usługi i skonfigurować aplikacje. Jeśli ten sam proces został już usunięty, można przejść do następnej sekcji. W przeciwnym razie zawarte w poniższych tematach są poleceniami interfejsu CLI platformy Azure. Zapoznaj się z [przewodnikiem Szybki Start: uruchamianie aplikacji w języku Java za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) w celu uzyskania dodatkowych informacji w tle.

Komputer lokalny musi spełniać te same wymagania wstępne co serwer kompilacji Jenkins. Upewnij się, że zainstalowano następujące elementy, aby skompilować i wdrożyć aplikacje mikrousług:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi)
    * [Interfejs wiersza polecenia platformy Azure został zainstalowany](/cli/azure/install-azure-cli?view=azure-cli-latest), w wersji 2.0.67 lub nowszej

1. Zainstaluj rozszerzenie chmury wiosennej platformy Azure:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Utwórz grupę zasobów zawierającą usługę Azure wiosny w chmurze:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Inicjowanie obsługi administracyjnej chmury wiosennej platformy Azure:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Rozwidlenie repozytorium [metryk Piggy](https://github.com/Azure-Samples/piggymetrics) na własne konto w usłudze GitHub. Na komputerze lokalnym Sklonuj repozytorium w katalogu o nazwie `source-code`:

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Skonfiguruj serwer konfiguracji. Upewnij się, że &lt;identyfikator usługi GitHub&gt; z poprawną wartością.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Kompiluj projekt:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Tworzenie trzech mikrousług: **bramy**, **uwierzytelniania**i usługi **konta**:

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Wdróż aplikacje: 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Przypisz publiczny punkt końcowy do bramy:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Wykonaj zapytanie dotyczące aplikacji bramy, aby uzyskać adres URL, aby można było sprawdzić, czy aplikacja jest uruchomiona.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Przejdź do adresu URL podanego przez poprzednie polecenie, aby uruchomić aplikację PiggyMetrics. 

## <a name="prepare-jenkins-server"></a>Przygotowanie serwera Jenkins

W tej sekcji przygotowano serwer Jenkins do uruchamiania kompilacji, co jest przeznaczone do testowania. Jednak ze względu na bezpieczeństwo należy użyć [agenta maszyny wirtualnej platformy Azure](https://plugins.jenkins.io/azure-vm-agents) lub [agenta kontenera platformy Azure](https://plugins.jenkins.io/azure-container-agents) do uruchomienia agenta na platformie Azure, aby uruchomić kompilacje. Aby uzyskać więcej informacji, zobacz artykuł w witrynie Jenkins dotyczący [zagadnień bezpieczeństwa kompilowania w węźle głównym](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Zainstaluj wtyczki

1. Zaloguj się do serwera Jenkins. Wybierz pozycję **Zarządzaj Jenkins > zarządzanie wtyczkami**.
2. Na karcie **dostępne** wybierz następujące wtyczki:
    * [Integracja z usługą GitHub](https://plugins.jenkins.io/github-pullrequest)
    * [Poświadczenia platformy Azure](https://plugins.jenkins.io/azure-credentials)

    Jeśli te wtyczki nie są wyświetlane na liście, sprawdź **zainstalowaną** kartę, aby sprawdzić, czy są już zainstalowane.

3. Aby zainstalować wtyczki, wybierz pozycję **Pobierz teraz i zainstaluj po ponownym uruchomieniu**.

4. Uruchom ponownie serwer Jenkins, aby zakończyć instalację.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Dodawanie poświadczeń jednostki usługi platformy Azure w magazynie poświadczeń Jenkins

1. Do wdrożenia na platformie Azure potrzebna jest jednostka usługi platformy Azure. Aby uzyskać więcej informacji, zobacz sekcję [Tworzenie nazwy głównej usługi](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) w samouczku wdrażanie do Azure App Service. Dane wyjściowe `az ad sp create-for-rbac` wyglądają następująco:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Na pulpicie nawigacyjnym serwera Jenkins wybierz pozycję **Credentials** > **System** (Poświadczenia > System). Następnie wybierz pozycję **Global credentials (unrestricted)** (Poświadczenia globalne [bez ograniczeń]).

3. Wybierz pozycję **Dodaj poświadczenia**. 

4. Wybierz **Microsoft Azure nazwę główną usługi** jako typ.

5. Podaj wartości dla: * Identyfikator subskrypcji: Użyj identyfikatora subskrypcji platformy Azure * identyfikator klienta: Użyj `appId` * klucz tajny klienta: Użyj `password` * identyfikator dzierżawy: Użyj `tenant` * środowiska Azure: Wybierz wstępnie ustawioną wartość. Na przykład użyj **platformy Azure** dla usługi Azure Global * ID: ustaw jako **azure_service_principal**. Ten identyfikator jest używany w kolejnym kroku w tym artykule * Description: to pole opcjonalne. Zalecamy podanie tutaj wartości znaczącej.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Zainstaluj rozszerzenie Maven i AZ CLI sprężyn-Cloud Extension

Potok przykładowy używa Maven do kompilowania i AZ CLI do wdrożenia w wystąpieniu usługi. Po zainstalowaniu Jenkins tworzy konto administratora o nazwie *Jenkins*. Upewnij się, że użytkownik *Jenkins* ma uprawnienia do uruchamiania rozszerzenia z chmurą.

1. Połącz się z serwerem głównym Jenkins za pośrednictwem protokołu SSH. 

2. Zainstaluj Maven

    ```bash
        sudo apt-get install maven 
    ```

3. Zainstaluj interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Interfejs wiersza polecenia platformy Azure jest instalowany domyślnie, jeśli używasz [wzorca Jenkins na platformie Azure](https://aka.ms/jenkins-on-azure).

4. Przełącz do `jenkins` użytkownika:

    ```bash
        sudo su jenkins
    ```

5. Dodaj rozszerzenie usługi **wiosna Cloud** :

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Utwórz fragmenty
1. W swoim repozytorium (https://github.com/&lt ; identyfikator GitHub&gt; /piggymetrics) Utwórz **fragmenty** w katalogu głównym.

2. Zaktualizuj plik w następujący sposób. Upewnij się, że zastąpisz wartości **\<nazw grup zasobów >** i **\<> nazwy usługi**. Zamień **azure_service_principal** z PRAWIDŁOWYm identyfikatorem, jeśli używasz innej wartości podczas dodawania poświadczeń w Jenkins. 

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

3. Zapisz i zatwierdź zmianę.

## <a name="create-the-job"></a>Tworzenie zadania

1. Na pulpicie nawigacyjnym Jenkins kliknij pozycję **nowy element**.

2. Podaj nazwę, *Wdróż-PiggyMetrics* dla zadania i wybierz **potok**. Kliknij przycisk OK.

3. Następnie kliknij kartę **Pipeline** (Potok).

4. W polu **Definition** (Definicja) wybierz wartość **Pipeline script from SCM** (Skrypt potoku z menedżera SCM).

5. W polu **SCM** wybierz wartość **Git**.

6. Wprowadź adres URL usługi GitHub dla repozytorium z rozwidleniem: **https://github.com/&lt ; identyfikator GitHub&gt; /piggymetrics.git**

7. Upewnij się, że **specyfikator gałęzi (czerń dla elementu "any")** jest * **/Azure**

8. Zachowaj **ścieżkę skryptu** jako **fragmenty**

7. Kliknij pozycję **Zapisz**

## <a name="validate-and-run-the-job"></a>Weryfikowanie i uruchamianie zadania

Przed uruchomieniem zadania zaktualizujmy tekst w polu wejściowym logowania, aby **wprowadzić identyfikator logowania**.

1. W swoim repozytorium Otwórz `index.html` w **/Gateway/src/Main/Resources/static/**

2. Wyszukaj ciąg "Wprowadź nazwę logowania" i zaktualizuj go do "Wprowadź identyfikator logowania"

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. Zatwierdź zmiany

4. Uruchom zadanie w Jenkins ręcznie. Na pulpicie nawigacyjnym Jenkins kliknij zadanie *Deploy-PiggyMetrics* , a następnie **Skompiluj teraz**.

Po zakończeniu zadania przejdź do publicznego adresu IP aplikacji **bramy** i sprawdź, czy aplikacja została zaktualizowana. 

![Zaktualizowane metryki Piggy](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania z interfejsu wiersza polecenia platformy Azure w programie Jenkins w celu zautomatyzowania ciągłej integracji i dostarczania (CI/CD) dla chmury wiosennej platformy Azure.

Aby dowiedzieć się więcej na temat dostawcy usługi Azure Jenkins, zobacz Jenkins w witrynie platformy Azure.

> [!div class="nextstepaction"]
> [Usługa Jenkins na platformie Azure](/azure/jenkins/)

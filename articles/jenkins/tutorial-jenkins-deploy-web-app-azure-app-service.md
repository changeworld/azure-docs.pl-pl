---
title: 'Samouczek: Wdrażanie z usługi GitHub w Azure App Service z usługą Jenkins'
description: Konfigurowanie narzędzia Jenkins pod kątem ciągłej integracji z usługi GitHub i ciągłego wdrażania w usłudze Azure App Service dla aplikacji internetowych w języku Java
services: jenkins
ms.service: jenkins
author: tomarchermsft
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.custom: seo-java-august2019
ms.openlocfilehash: 955ce9724d576e56766ab3d87a374a65e4ca5c0e
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967119"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Samouczek: Wdrażanie z usługi GitHub do usługi Azure App Service przy użyciu funkcji ciągłej integracji i ciągłego wdrażania narzędzia Jenkins

W tym samouczku wdrożysz przykładową internetową aplikację Java z usługi GitHub do [usługi Azure App Service w systemie Linux](/azure/app-service/containers/app-service-linux-intro), konfigurując ciągłą integrację i ciągłe wdrażanie w narzędziu Jenkins. Po zaktualizowaniu aplikacji przez wypchnięcie zatwierdzeń do usługi GitHub narzędzie Jenkins automatycznie skompiluje i opublikuje ponownie aplikację w usłudze Azure App Service. Przykładowa aplikacja w tym samouczku została opracowana przy użyciu struktury [Spring Boot](https://projects.spring.io/spring-boot/). 

![Omówienie](media/tutorial-jenkins-deploy-web-app-azure-app-service/overview.png)

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Instalowanie wtyczek narzędzia Jenkins umożliwiających kompilowanie z usługi GitHub, wdrażanie w usłudze Azure App Service i wykonywanie innych powiązanych zadań.
> * Tworzenie rozwidlenia przykładowego repozytorium GitHub w celu utworzenia kopii roboczej.
> * Łączenie narzędzia Jenkins z usługą GitHub.
> * Tworzenie jednostki usługi platformy Azure w celu umożliwienia narzędziu Jenkins dostępu do platformy Azure bez użycia poświadczeń.
> * Dodawanie jednostki usługi do narzędzia Jenkins.
> * Tworzenie potoku narzędzia Jenkins, który kompiluje i wdraża przykładową aplikację po każdej aktualizacji aplikacji w usłudze GitHub.
> * Tworzenie plików kompilacji i wdrażania dla potoku narzędzia Jenkins.
> * Określenie dla potoku narzędzia Jenkins skryptu kompilacji i wdrażania.
> * Wdrażanie przykładowej aplikacji na platformie Azure przez ręczne uruchomienie kompilacji.
> * Wypchnięcie aktualizacji aplikacji w usłudze GitHub powodujące wyzwolenie w narzędziu Jenkins kompilacji i ponownego wdrażania na platformie Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są potrzebne następujące elementy:

* Serwer [Jenkins](https://jenkins.io/) z zestawem Java Development Kit (JDK) i narzędziami Maven zainstalowanymi na maszynie wirtualnej systemu Linux na platformie Azure

  Jeśli nie masz serwera Jenkins, wykonaj teraz następujące kroki w witrynie Azure Portal: [Tworzenie serwera Jenkins na maszynie wirtualnej systemu Linux na platformie Azure](/azure/jenkins/install-jenkins-solution-template)

* Konto usługi [GitHub](https://github.com) umożliwiające uzyskanie kopii roboczej (tworzenie rozwidlenia) przykładowej internetowej aplikacji Java. 

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), który można uruchomić z poziomu lokalnego wiersza polecenia lub z [usługi Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Instalowanie wtyczek narzędzia Jenkins

1. Zaloguj się do konsoli internetowej narzędzia Jenkins w następującej lokalizacji:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Na stronie głównej narzędzia Jenkins wybierz pozycję **Manage Jenkins** > **Manage Plugins** (Zarządzaj narzędziem Jenkins > Zarządzaj wtyczkami).

   ![Zarządzanie wtyczkami narzędzia Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Na karcie **Available** (Dostępne) wybierz następujące wtyczki:

   - [Usługa Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub Branch Source](https://plugins.jenkins.io/github-branch-source)
   - [Environment Injector Plugin](https://plugins.jenkins.io/envinject) dla narzędzia Jenkins
   - [Azure Credentials](https://plugins.jenkins.io/azure-credentials)

   Jeśli te wtyczki nie są wyświetlane, upewnij się, że nie są już zainstalowane, sprawdzając kartę **Installed** (Zainstalowane).

1. Aby zainstalować wybrane wtyczki, wybierz pozycję **Pobierz teraz i zainstaluj po ponownym uruchomieniu**.

1. Po wykonaniu tych działań wybierz w menu narzędzia Jenkins pozycję **Manage Jenkins**  (Zarządzaj narzędziem Jenkins), aby wrócić na stronę zarządzania narzędziem Jenkins w celu wykonania kolejnych kroków.

## <a name="fork-sample-github-repo"></a>Utworzenie rozwidlenia przykładowego repozytorium GitHub

1. [Zaloguj się do repozytorium GitHub dla przykładowej aplikacji Spring Boot](https://github.com/spring-guides/gs-spring-boot). 

1. W prawym górnym rogu w usłudze GitHub wybierz opcję **rozwidlenie**.

   ![Utworzenie rozwidlenia przykładowego repozytorium z usługi GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Postępuj zgodnie z monitami, aby wybrać konto usługi GitHub i zakończyć tworzenie rozwidlenia.

Następnie skonfiguruj narzędzie Jenkins przy użyciu poświadczeń usługi GitHub.

## <a name="connect-jenkins-to-github"></a>Łączenie narzędzia Jenkins z usługą GitHub

Aby narzędzie Jenkins monitorowało usługę GitHub i odpowiadało po wypchnięciu nowych zatwierdzeń do aplikacji internetowej w Twoim rozwidleniu usługi GitHub, włącz [elementy webhook usługi GitHub](https://developer.github.com/webhooks/) w narzędziu Jenkins.

> [!NOTE]
> 
> Te kroki umożliwiają utworzenie poświadczeń osobistego tokenu dostępu dla narzędzia Jenkins na potrzeby współpracy z usługą GitHub za pomocą nazwy użytkownika i hasła usługi GitHub. 
> Jeśli jednak konto usługi GitHub korzysta z uwierzytelniania dwuskładnikowego, zamiast tego utwórz token w usłudze GitHub i skonfiguruj narzędzie Jenkins pod kątem użycia tego tokenu. 
> Aby uzyskać więcej informacji, zobacz dokumentację [wtyczki usługi GitHub dla narzędzia Jenkins](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin).

1. Na stronie **Manage Jenkins** (Zarządzanie narzędziem Jenkins) wybierz pozycję **Configure System** (Konfiguruj system). 

   ![Konfigurowanie systemu](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. W sekcji **GitHub** podaj szczegóły serwera GitHub. Z listy **Add GitHub Server** (Dodaj serwer GitHub) wybierz pozycję **GitHub Server** (Serwer GitHub). 

   ![Dodawanie serwera GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Jeśli właściwość **Manage hooks** (Zarządzaj wpięciami) nie jest wybrana, wybierz ją. Wybierz pozycję **Zaawansowane** , aby określić inne ustawienia. 

   ![Wybieranie pozycji „Advanced” (Zaawansowane) w celu określenia kolejnych ustawień](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. Z listy **Manage additional GitHub actions** (Zarządzaj dodatkowymi akcjami usługi GitHub) wybierz pozycję **Convert login and password to token** (Konwertuj nazwę logowania i hasło na token).

   ![Wybieranie pozycji „Manage additional GitHub actions” (Zarządzaj dodatkowymi akcjami usługi GitHub)](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Wybierz pozycję **From login and password** (Z nazwy logowania i hasła), aby można było podać nazwę użytkownika i hasło usługi GitHub. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz poświadczenia tokenu**, co spowoduje utworzenie [osobistego tokenu dostępu usługi GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Tworzenie osobistego tokenu dostępu usługi GitHub na podstawie nazwy logowania i hasła](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. W sekcji **GitHub Server** (Serwer usługi GitHub) wybierz nowy token z listy **Credentials** (Poświadczenia). Sprawdź, czy uwierzytelnianie działa, wybierając polecenie **Test connection** (Testuj połączenie).

   ![Sprawdzanie połączenia z serwerem GitHub za pomocą nowego osobistego tokenu dostępu](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Następnie utwórz jednostkę usługi platformy Azure, której narzędzie Jenkins będzie używać do uwierzytelniania i na potrzeby uzyskiwania dostępu do zasobów platformy Azure.

## <a name="create-service-principal"></a>Tworzenie jednostki usługi

W dalszej sekcji utworzysz zadanie potoku narzędzia Jenkins, które kompiluje aplikację z usługi GitHub i wdraża ją w usłudze Azure App Service. Aby narzędzie Jenkins miało dostęp do platformy Azure bez konieczności wprowadzania poświadczeń, utwórz [jednostkę usługi](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) w usłudze Azure Active Directory dla narzędzia Jenkins. Jednostka usługi to osobna tożsamość, której narzędzie Jenkins może używać na potrzeby uwierzytelniania dostępu do zasobów platformy Azure. Aby utworzyć tę jednostkę usługi, uruchom polecenie interfejsu wiersza polecenia platformy Azure [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) z poziomu lokalnego wiersza polecenia lub usługi Azure Cloud Shell, na przykład: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Upewnij się, że nazwa jednostki usługi jest podana w cudzysłowie. Utwórz także silne hasło zgodne z [regułami i ograniczeniami usługi Azure Active Directory dotyczącymi haseł](/azure/active-directory/active-directory-passwords-policy). Jeśli nie podasz hasła, interfejs wiersza polecenia platformy Azure utworzy hasło. 

Oto dane wyjściowe wygenerowane przez polecenie **`create-for-rbac`** : 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Jeśli masz już jednostkę usługi, możesz jej użyć zamiast tworzyć nową.
> Do określenia wartości jednostki usługi na potrzeby uwierzytelniania użyj wartości właściwości `appId`, `password` i `tenant`. 
> Do wyszukiwania istniejącej jednostki usługi użyj wartości właściwości `displayName`.

## <a name="add-service-principal-to-jenkins"></a>Dodawanie jednostki usługi do narzędzia Jenkins

1. Na stronie głównej narzędzia Jenkins wybierz pozycję **Credentials** > **System** (Poświadczenia > System). 

1. Na stronie **System** w obszarze **Domain** (Domena) wybierz pozycję **Global credentials (unrestricted)** (Poświadczenia globalne — bez ograniczeń).

1. Z menu po lewej stronie wybierz pozycję **Add Credentials** (Dodaj poświadczenia).

1. Z listy **Kind** (Rodzaj) wybierz pozycję **Azure Service Principal** (Jednostka usługi platformy Azure).

1. Podaj informacje dotyczące jednostki usługi i subskrypcji platformy Azure za pomocą właściwości opisanych w tabeli znajdującej się w tym kroku:

   ![Dodawanie poświadczeń jednostki usługi platformy Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Właściwość | Value | Opis | 
   |----------|-------|-------------| 
   | **Subscription ID (Identyfikator subskrypcji)** | <*yourAzureSubscription-ID (Identyfikator Twojej subskrypcji platformy Azure)* > | Wartość identyfikatora GUID dla subskrypcji platformy Azure <p>**Porada**: jeśli nie znasz identyfikatora subskrypcji platformy Azure, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure z poziomu wiersza polecenia lub usługi Cloud Shell, a następnie użyj wartości identyfikatora GUID `id`: <p>`az account list` | 
   | **Identyfikator klienta** | <*yourAzureServicePrincipal-ID (Identyfikator jednostki usługi platformy Azure)* > | Wartość identyfikatora GUID `appId` wygenerowanego wcześniej dla jednostki usługi platformy Azure | 
   | **Client Secret (Wpis tajny klienta)** | <*yourSecurePassword (Hasło)* > | Wartość `password` lub „wpis tajny” określony dla jednostki usługi platformy Azure | 
   | **Tenant ID (Identyfikator dzierżawy)** | <*yourAzureActiveDirectoryTenant-ID (Identyfikator dzierżawy usługi Azure Active Directory)* > | Wartość identyfikatora GUID `tenant` dla dzierżawy usługi Azure Active Directory | 
   | **Identyfikator** | <*yourAzureServicePrincipalName (Nazwa jednostki usługi platformy Azure)* > | Wartość `displayName` jednostki usługi platformy Azure | 

1. Aby potwierdzić, że nazwa główna usługi działa, wybierz pozycję **weryfikuj nazwę główną usługi**. Gdy skończysz, wybierz opcję **OK**.

Następnie utwórz potok narzędzia Jenkins, który kompiluje i wdraża aplikację.

## <a name="create-jenkins-pipeline"></a>Tworzenie potoku Jenkins

W narzędziu Jenkins utwórz zadanie potoku na potrzeby kompilowania i wdrażania aplikacji.

1. Wróć do strony głównej narzędzia Jenkins i wybierz pozycję **New Item** (Nowy element). 

   ![Wybieranie pozycji „New Item” (Nowy element)](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Podaj nazwę dla zadania potoku, na przykład, „Moja internetowa aplikacja Java”, i wybierz pozycję **Pipeline** (Potok). Kliknij **przycisk OK**w dolnej części.  

   ![Wybieranie pozycji „Pipeline” (Potok)](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Skonfiguruj narzędzie Jenkins przy użyciu jednostki usługi, co umożliwi narzędziu Jenkins wdrażanie na platformie Azure bez konieczności używania poświadczeń.

   1. Na karcie **General** (Ogólne) wybierz pozycję **Prepare an environment for the run** (Przygotuj środowisko dla uruchomienia). 

   1. W wyświetlonym polu **Properties Content** (Treść właściwości) dodaj następujące zmienne środowiskowe i ich wartości. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Wybieranie pozycji „Prepare an environment for the run” (Przygotuj środowisko dla uruchomienia) i ustawianie zmiennych środowiskowych](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-run.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

Następnie utwórz skrypty kompilacji i wdrażania dla narzędzia Jenkins.

## <a name="create-build-and-deployment-files"></a>Tworzenie plików kompilacji i wdrażania

Teraz możesz utworzyć pliki używane przez narzędzie Jenkins do kompilowania i wdrażania aplikacji.

1. W folderze `src/main/resources/` Twojego rozwidlenia usługi GitHub utwórz plik konfiguracji aplikacji o nazwie `web.config` zawierający następujący kod XML, lecz z ciągiem `$(JAR_FILE_NAME)` zastąpionym wartością `gs-spring-boot-0.1.0.jar`:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. W folderze głównym rozwidlenia usługi GitHub utwórz skrypt kompilacji i wdrażania o nazwie `Jenkinsfile` zawierający następujący tekst ([źródło w usłudze GitHub znajduje się tutaj](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Zatwierdź pliki `web.config` i `Jenkinsfile` w rozwidleniu usługi GitHub, a następnie wypchnij zmiany.

## <a name="point-pipeline-at-script"></a>Wskazanie skryptu dla potoku

Teraz określ skrypt kompilowania i wdrażania, którego ma używać narzędzie Jenkins.

1. W narzędziu Jenkins wybierz uprzednio utworzone zadanie potoku. 

   ![Wybieranie zadania potoku dla aplikacji internetowej](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. W menu po lewej stronie wybierz pozycję **Configure** (Konfiguruj).

1. Na karcie **Pipeline** (Potok) wybierz z listy **Definition** (Definicja) pozycję **Pipeline script from SCM** (Skrypt potoku z narzędzia SCM).

   1. W wyświetlonym polu **SCM** (Narzędzie SCM) wybierz pozycję **Git** dla kontroli źródła. 

   1. W sekcji **Repositories** (Repozytoria) w polu **Repository URL** (Adres URL repozytorium) podaj adres URL Twojego rozwidlenia usługi GitHub, na przykład: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. W polu **Credentials** (Poświadczenia) wybierz uprzednio utworzony osobisty token dostępu usługi GitHub.

   1. W polu **Script Path** (Ścieżka skryptu) dodaj ścieżkę do skryptu „Jenkinsfile”.

   Gdy wszystko będzie gotowe, definicja potoku będzie wyglądać podobnie do następującego przykładu: 

   ![Wskazanie skryptu dla potoku](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

Następnie skompiluj i wdróż aplikację w usłudze Azure App Service. 

## <a name="build-and-deploy-to-azure"></a>Kompilowanie i wdrażanie na platformie Azure

1. Za pomocą interfejsu wiersza polecenia platformy Azure z poziomu wiersza polecenia lub usługi Azure Cloud Shell utwórz [aplikację internetową usługi Azure App Service w systemie Linux](/azure/app-service/containers/app-service-linux-intro), przy czym narzędzie Jenkins wdroży aplikację internetową po zakończeniu kompilowania. Upewnij się, że Twoja aplikacja internetowa ma unikatową nazwę.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Aby uzyskać więcej informacji na temat tych poleceń interfejsu wiersza polecenia platformy Azure, zobacz następujące strony:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. W narzędziu Jenkins wybierz zadanie potoku, a następnie wybierz polecenie **Build Now** (Kompiluj teraz).

   Po zakończeniu kompilacji narzędzie Jenkins wdroży aplikację, która będzie dostępna na platformie Azure pod adresem URL publikacji, na przykład: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Wyświetlanie wdrożonej aplikacji na platformie Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Wypychanie zmian i ponowne wdrażanie

1. W przeglądarce internetowej przejdź do następującej lokalizacji w Twoim rozwidleniu usługi GitHub dla aplikacji internetowej:

   `complete/src/main/java/Hello/Application.java`
   
1. W prawym górnym rogu w usłudze GitHub wybierz pozycję **Edytuj ten plik**.

1. Wprowadź następującą zmianę w metodzie `commandLineRunner()` i zatwierdź zmianę gałęzi `master` repozytorium. To zatwierdzenie w gałęzi `master` uruchamia kompilację w narzędziu Jenkins. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Po zakończeniu kompilacji i ponownym wdrożeniu przez narzędzie Jenkins na platformie Azure odśwież aplikację, która teraz pokaże aktualizację.

   ![Wyświetlanie wdrożonej aplikacji na platformie Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Use Azure VMs as build agents (Używanie maszyn wirtualnych platformy Azure)](/azure/jenkins/jenkins-azure-vm-agents)

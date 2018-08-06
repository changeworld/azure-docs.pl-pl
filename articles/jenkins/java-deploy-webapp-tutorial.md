---
title: Używanie serwera Jenkins do wdrażania aplikacji internetowych na platformie Azure
description: Konfiguruj ciągłą integrację z usługi GitHub do usługi Azure App Service dla aplikacji internetowych Java za pomocą serwera Jenkins i platformy Docker.
ms.topic: tutorial
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/31/2018
ms.openlocfilehash: e880d84c3ae0fd23c11bb9b30733544bd5f28872
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389946"
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Konfigurowanie ciągłej integracji i wdrażania w usłudze Azure App Service za pomocą serwera Jenkins

W tym samouczku przedstawiono sposób konfigurowania ciągłej integracji i wdrażania (CI/CD) przykładowej aplikacji internetowej języka Java opracowanej przy użyciu platformy [Spring Boot](http://projects.spring.io/spring-boot/) do [aplikacji internetowej usługi Azure App Service w systemie Linux](/azure/app-service/containers/app-service-linux-intro) za pomocą serwera Jenkins.

Podczas pracy z samouczkiem wykonasz następujące zadania:

> [!div class="checklist"]
> * Instalowanie wtyczek serwera Jenkins wymaganych do wdrożenia w usłudze Azure App Service.
> * Definiowanie zadań serwera Jenkins w celu skompilowania obrazów platformy Docker z repozytorium GitHub po wypchnięciu nowego zatwierdzenia.
> * Definiowanie nowej aplikacji internetowej platformy Azure dla systemu Linux oraz konfigurowanie jej do wdrażania obrazów platformy Docker wypchniętych do rejestru Azure Container Registry.
> * Konfigurowanie wtyczki Jenkins dla usługi Azure App Service.
> * Wdrażanie przykładowej aplikacji w usłudze Azure App Service przy użyciu kompilacji ręcznej.
> * Wyzwalanie kompilacji narzędzia serwera Jenkins i aktualizowanie aplikacji internetowej przez wypchnięcie zmian do serwisu GitHub.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Serwer [Jenkins](https://jenkins.io/) ze skonfigurowanymi narzędziami zestawu JDK i Maven. Jeśli nie masz systemu Jenkins, utwórz go teraz na platformie Azure z poziomu [szablonu rozwiązania Jenkins](/azure/jenkins/install-jenkins-solution-template).
* Konto usługi [GitHub](https://github.com).
* [Interfejs wiersza polecenia platformy Azure w wersji 2.0](/cli/azure) z lokalnego wiersza polecenia lub w usłudze [Azure Cloud Shell](/azure/cloud-shell/overview)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Instalowanie wtyczek narzędzia Jenkins

1. Otwórz konsolę internetową serwera Jenkins w przeglądarce internetowej, a następnie wybierz pozycję **Manage Jenkins** (Zarządzaj serwerem Jenkins) z menu po lewej stronie, następnie wybierz pozycję **Manage Plugins** (Zarządzaj wtyczkami).
2. Wybierz kartę **Available** (Dostępne).
3. Wyszukaj następujące wtyczki i zaznacz pole wyboru obok nich:   

    - [Azure App Service Plug-in](https://plugins.jenkins.io/azure-app-service) (Wtyczka usługi Azure App Service)
    - [GitHub Branch Source Plug-in](https://plugins.jenkins.io/github-branch-source) (Wtyczka gałęzi źródłowej usługi GitHub)

    Jeśli wtyczki nie są wyświetlane, upewnij się, że nie zostały już zainstalowane, sprawdzając kartę **Installed** (Zainstalowane).

1. Wybierz pozycję **Download now and install after restart** (Pobierz teraz i zainstaluj po ponownym uruchomieniu), aby włączyć wtyczki w konfiguracji serwera Jenkins.

## <a name="configure-github-and-jenkins"></a>Konfigurowanie usługi GitHub i serwera Jenkins

Skonfiguruj serwer Jenkins, aby otrzymywać [elementy webhook usługi GitHub](https://developer.github.com/webhooks/), gdy nowe zatwierdzenia będą przekazywane do repozytorium na Twoim koncie.

1. Wybierz pozycję **Manage Jenkins** (Zarządzaj serwerem Jenkins), a następnie pozycję **Configure System** (Skonfiguruj system). W sekcji **GitHub** sprawdź, czy zaznaczono pole **Manage hooks** (Zarządzaj punktami zaczepienia), a następnie wybierz pozycję **Manage additional GitHub actions** (Zarządzaj dodatkowymi akcjami usługi GitHub) i wybierz pozycję **Convert login and password to token** (Konwertuj dane logowania i hasło na token).
2. Zaznacz przycisk radiowy **From login and password** (Na podstawie danych logowania i hasła) i wprowadź nazwę użytkownika i hasło usługi GitHub. Wybierz pozycję **Create token credentials** (Utwórz poświadczenia tokenu), aby utworzyć nowy [osobisty token dostępu usługi GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![Tworzenie osobistego tokenu dostępu usługi GitHub na podstawie danych logowania i hasła](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Wybierz nowo utworzony token z listy rozwijanej **Credentials** (Poświadczenia) w konfiguracji serwerów usługi GitHub. Wybierz pozycję **Test connection** (Testuj połączenie), aby sprawdzić, czy uwierzytelnianie działa.   
   ![Weryfikowanie połączenia z usługą GitHub po skonfigurowaniu osobistego tokenu dostępu](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Jeśli Twoje konto usługi GitHub ma włączone uwierzytelnianie dwuskładnikowe, utwórz token w serwisie GitHub i skonfiguruj serwer Jenkins tak, aby z niego korzystał. Pełne szczegółowe informacje można znaleźć w dokumentacji dotyczącej [wtyczki serwisu GitHub dla serwera Jenkins](https://wiki.jenkins.io/display/JENKINS/Github+Plugin).

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Tworzenie rozwidlenia przykładowego repozytorium i tworzenia zadania serwera Jenkins 

1. Otwórz [repozytorium przykładowej aplikacji platformy Spring Boot](https://github.com/spring-guides/gs-spring-boot-docker) i utwórz rozwidlenie na własnym koncie usługi GitHub, wybierając pozycję **Fork** (Utwórz rozwidlenie) w prawym górnym rogu.   
    ![Tworzenie rozwidlenia z usługi GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. W internetowej konsoli serwerze Jenkins wybierz pozycję **New Item** (Nowy element), nadaj elementowi nazwę **MyJavaApp**, wybierz pozycję **Freestyle project** (Projekt Freestyle), a następnie wybierz przycisk **OK**.   
    ![Nowy projekt Freestyle serwera Jenkins](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. W sekcji **General** (Ogólne) wybierz projekt **GitHub** i wprowadź adres URL rozwidlonego repozytorium, taki jak https://github.com/raisa/gs-spring-boot-docker
3. W sekcji **Source code management** (Zarządzanie kodem źródłowym) wybierz pozycję **Git** i wprowadź adres URL rozwidlonego repozytorium `.git`, taki jak https://github.com/raisa/gs-spring-boot-docker.git
4. W sekcji **Kompilowanie wyzwalaczy** wybierz pozycję **Wyzwalacz punktu zaczepienia GitHub na potrzeby sondowania GITscm**.
5. W sekcji **Build** (Kompilacja) wybierz pozycję **Add build step** (Dodaj krok kompilacji), a następnie pozycję **Invoke top-level Maven targets** (Wywołaj elementy docelowe najwyższego poziomu narzędzia Maven). Wprowadź `package` w polu **Goals** (Cele).
6. Wybierz pozycję **Zapisz**. Możesz przetestować zadanie, wybierając pozycję **Build Now** (Kompiluj teraz) na stronie projektu.

## <a name="configure-azure-app-service"></a>Konfigurowanie usługi App Service 

1. Przy użyciu interfejsu wiersza polecenia platformy Azure lub usługi [Cloud Shell](/azure/cloud-shell/overview) utwórz nową [aplikację internetową w systemie Linux](/azure/app-service/containers/app-service-linux-intro). Nazwa aplikacji internetowej w tym samouczku to `myJavaApp`, ale własnej aplikacji musisz nadać unikatową nazwę.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Utwórz [rejestr Azure Container Registry](/azure/container-registry/container-registry-intro) do przechowywania obrazów platformy Docker kompilowanych przez serwer Jenkins. Nazwa rejestru kontenerów używana w tym samouczku to `jenkinsregistry`, ale własnemu rejestrowi kontenerów musisz nadać unikatową nazwę. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Skonfiguruj aplikację internetową do uruchamiania obrazów platformy Docker wypchniętych do rejestru kontenerów, a następnie określ, czy aplikacja działająca w kontenerze nasłuchuje żądań na porcie 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Konfigurowanie wtyczki Jenkins dla usługi Azure App Service

1. W konsoli internetowej serwera Jenkins wybierz utworzone zadanie **MyJavaApp**, a następnie wybierz pozycję **Configure** (Konfiguruj) w lewej części strony.
2. Przewiń w dół do pozycji **Post-build Actions** (Akcje wykonywane po kompilacji), a następnie wybierz kolejno pozycje **Add post-build action** (Dodaj akcję wykonywaną po kompilacji) i **Publish an Azure Web App** (Publikuj aplikację internetową platformy Azure).
3. W obszarze **Azure Profile Configuration** (Konfiguracja profilu platformy Azure) wybierz pozycję **Add** (Dodaj) obok pozycji **Azure Credentials** (Poświadczenia platformy Azure) i wybierz pozycję **Jenkins**.
4. W oknie dialogowym **Add Credentials** (Dodawanie poświadczeń) wybierz pozycję **Microsoft Azure Service Principal** (Jednostka usługi Microsoft Azure) z listy rozwijanej **Kind** (Rodzaj).
5. Utwórz jednostkę usługi Active Directory za pomocą wiersza polecenia platformy Azure lub usługi [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Wprowadź poświadczenia jednostki usługi w oknie dialogowym **Add credentials** (Dodawanie poświadczeń). Jeśli nie znasz identyfikatora subskrypcji platformy Azure, możesz go uzyskać za pomocą zapytania interfejsu wiersza polecenia:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Konfigurowanie jednostki usługi platformy Azure](media/jenkins-java-quickstart/azure_service_principal.png)
6. Sprawdź, czy jednostka usługi jest uwierzytelniana na platformie Azure, wybierając pozycję **Verify Service Principal** (Zweryfikuj jednostkę usługi). 
7. Wybierz pozycję **Add** (Dodaj), aby zapisać poświadczenia.
8. Wybierz poświadczenie jednostki usługi, które zostało właśnie dodane z listy rozwijanej **Azure Credentials** (Poświadczenia platformy Azure), po powrocie do konfiguracji **Publish an Azure Web App** (Publikuj aplikację internetową platformy Azure).
9. W obszarze **App Configuration** (Konfiguracja aplikacji) wybierz grupę zasobów i nazwę aplikacji internetowej z listy rozwijanej.
10. Wybierz przycisk radiowy **Publish via Docker** (Publikuj za pośrednictwem platformy Docker).
11. Wprowadź `complete/Dockerfile` jako **ścieżkę do pliku Dockerfile**.
12. Wprowadź `https://jenkinsregistry.azurecr.io` w polu **Docker registry URL** (Adres URL rejestru platformy Docker).
13. Wybierz pozycję **Add** (Dodaj) obok pozycji **Registry Credentials** (Poświadczenia rejestru). 
14. Wprowadź nazwę użytkownika administratora utworzonego rejestru Azure Container Registry w obszarze **Username** (Nazwa użytkownika).
15. Wprowadź hasło rejestru Azure Container Registry w polu **Password** (Hasło). Nazwę użytkownika i hasło można pobrać z witryny Azure Portal lub za pośrednictwem następującego polecenia interfejsu wiersza polecenia:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Dodawanie poświadczeń rejestru kontenerów](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Wybierz pozycję **Add** (Dodaj), aby zapisać poświadczenie.
16. Wybierz nowo utworzone poświadczenie z listy rozwijanej **Registry credentials** (Poświadczenia rejestru) w panelu **App Configuration** (Konfiguracja aplikacji) dla pozycji **Publish an Azure Web App** (Publikuj aplikację internetową platformy Azure). Gotowa akcja wykonywana po kompilacji powinna wyglądać podobnie do następującego obrazu:   
    ![Konfiguracja akcji wykonywanej po kompilacji na potrzeby wdrażania usługi Azure App Service](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Wybierz pozycję **Save** (Zapisz), aby zapisać konfigurację zadania.

## <a name="deploy-the-app-from-github"></a>Wdrażanie aplikacji z usługi GitHub

1. W projekcie serwera Jenkins wybierz pozycję **Build Now** (Kompiluj teraz), aby wdrożyć przykładową aplikację na platformie Azure.
2. Po zakończeniu kompilacji aplikacja działa na platformie Azure pod adresem URL publikowania, na przykład http://myjavaapp.azurewebsites.net.   
   ![Wyświetlanie wdrożonej aplikacji na platformie Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Wypychanie zmian i ponowne wdrażanie

1. Z poziomu rozwidlenia w serwisie Github przejdź w Internecie do adresu `complete/src/main/java/Hello/Application.java`. Wybierz link **Edit this file** (Edytuj ten plik) po prawej stronie interfejsu serwisu GitHub.
2. Wprowadź następującą zmianę metody `home()` i zatwierdź zmianę głównej gałęzi repozytorium.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. Na serwerze Jenkins rozpoczyna się nowa konfiguracja wyzwolona przez nowe zatwierdzenie w obrębie gałęzi `master` repozytorium. Po zakończeniu załaduj ponownie aplikację na platformie Azure.     
      ![Wyświetlanie wdrożonej aplikacji na platformie Azure](media/jenkins-java-quickstart/hello_docker_world.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Use Azure VMs as build agents (Używanie maszyn wirtualnych platformy Azure)](/azure/jenkins/jenkins-azure-vm-agents)
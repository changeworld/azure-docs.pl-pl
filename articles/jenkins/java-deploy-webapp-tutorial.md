---
title: Wdrażanie aplikacji sieci web na platformie Azure przy użyciu Wpięć | Dokumentacja firmy Microsoft
description: Konfigurowanie ciągłej integracji z serwisu GitHub do usługi Azure App Service dla aplikacji sieci web Java za pomocą Wpięć i Docker.
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: ''
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: b2606acba341d4cfbc16314048e134fa30ff8606
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29853003"
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Konfigurowanie ciągłej integracji i wdrażania w usłudze Azure App Service z Wpięć

W tym samouczku konfiguruje ciągłej integracji i wdrażania (CI/CD) przykładową aplikację sieci web Java opracowanych za pomocą [rozruchu Spring](http://projects.spring.io/spring-boot/) platformę, by [aplikacji sieci Web usługi aplikacji Azure w systemie Linux](/azure/app-service/containers/app-service-linux-intro) przy użyciu Wpięć.

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Instalowanie wtyczki Wpięć potrzebne do wdrożenia w usłudze Azure App Service.
> * Zdefiniuj zadanie Wpięć, aby tworzyć obrazy Docker z repozytorium GitHub, gdy spoczywa nowe zatwierdzenia.
> * Zdefiniuj nowej aplikacji sieci Web platformy Azure dla systemu Linux i skonfigurować go do wdrażania obrazów Docker do rejestru kontenera platformy Azure.
> * Skonfiguruj wtyczkę Wpięć usługi aplikacji Azure.
> * Wdrażanie przykładowej aplikacji do usługi Azure App Service z kompilacją ręczne.
> * Wyzwolić kompilację Wpięć i zaktualizować aplikacji sieci web, wypychając zmiany do usługi GitHub.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Wpięć](https://jenkins.io/) narzędzia JDK i Maven, które są skonfigurowane. Jeśli nie masz system Wpięć, utwórz ją teraz na platformie Azure z [szablon rozwiązania Wpięć](/azure/jenkins/install-jenkins-solution-template).
* Konto usługi [GitHub](https://github.com).
* [Azure CLI 2.0](/cli/azure), z sieci lokalnej wiersza polecenia lub w [powłoki chmury Azure](/azure/cloud-shell/overview)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Instalowanie wtyczki Wpięć

1. Otwórz przeglądarkę sieci web do konsoli sieci web Wpięć i wybierz **Zarządzanie Wpięć** z menu po lewej stronie, następnie wybierz **Zarządzanie wtyczkami**.
2. Wybierz **dostępne** kartę.
3. Wyszukaj, a następnie zaznacz pole wyboru obok następujące wtyczki:   

    - [Usługa aplikacji Azure wtyczki](https://plugins.jenkins.io/azure-app-service)
    - [GitHub Branch Source Plug-in](https://plugins.jenkins.io/github-branch-source)

    Jeśli dodatki nie są wyświetlane, upewnij się, nie są zainstalowane sprawdzając **zainstalowana** kartę.

1. Wybierz **teraz pobrać i zainstalować po ponownym uruchomieniu** umożliwiające wtyczek w konfiguracji Wpięć.

## <a name="configure-github-and-jenkins"></a>Konfigurowanie usługi GitHub i Wpięć

Konfiguracja Wpięć do odbierania [elementów webhook GitHub](https://developer.github.com/webhooks/) po nowe zatwierdzenia są przenoszone do repozytorium na Twoim koncie.

1. Wybierz **Zarządzanie Wpięć**, następnie **skonfigurować System**. W **GitHub** sekcji, upewnij się, że **Zarządzanie punkty zaczepienia** jest zaznaczone, a następnie wybierz **Zarządzanie dodatkowe akcje GitHub** i wybierz polecenie **przekonwertować logowania i hasło do tokenu**.
2. Wybierz **z identyfikator logowania i hasło** przycisk radiowy, a następnie wprowadź nazwę użytkownika usługi GitHub i hasło. Wybierz **Utwórz token poświadczenia** do tworzenia nowego [GitHub osobisty Token dostępu](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![Utwórz GitHub PAT z identyfikator logowania i hasło](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Wybierz nowo utworzony token z **poświadczenia** listy rozwijanej w konfiguracji serwerów usługi GitHub. Wybierz **połączenie testowe** Aby sprawdzić, czy działa uwierzytelniania.   
   ![Sprawdź połączenie z usługi GitHub po skonfigurowaniu Marta](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Jeśli konto GitHub ma włączone uwierzytelnianie dwuskładnikowe, utworzenia tokenu w usłudze GitHub i skonfigurować Wpięć z niego korzystać. Przegląd [GitHub Wpięć wtyczki](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) dokumentacji, aby uzyskać szczegółowe informacje.

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Rozwidlania repozytorium przykładowej, a następnie utworzyć zadanie Wpięć 

1. Otwórz [repozytorium aplikacji przykładowej rozruchu Spring](https://github.com/spring-guides/gs-spring-boot-docker) i rozwidlania go z kontem usługi GitHub, wybierając **rozwidlenia** w prawym górnym rogu.   
    ![Rozwidlenia z usługi GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. W konsoli sieci web Wpięć wybierz **nowy element**, nadaj mu nazwę **MyJavaApp**, wybierz pozycję **stylu projektu**, a następnie wybierz pozycję **OK**.   
    ![Nowy projekt dowolne Wpięć](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. W obszarze **ogólne** zaznacz **GitHub** projektu, a następnie wprowadź adres URL repozytorium rozwidlonych, takich jak https://github.com/raisa/gs-spring-boot-docker
3. W obszarze **źródła zarządzania kodem** zaznacz **Git**, wprowadź Twojego repozytorium rozwidlonych `.git` adres URL, takie jak https://github.com/raisa/gs-spring-boot-docker.git
4. W sekcji **Kompilowanie wyzwalaczy** wybierz pozycję **Wyzwalacz punktu zaczepienia GitHub na potrzeby sondowania GITscm**.
5. W obszarze **kompilacji** zaznacz **kroku kompilacji Dodaj** i wybierz polecenie **wywołania najwyższego poziomu celów Maven**. Wprowadź `package` w **cele** pola.
6. Wybierz pozycję **Zapisz**. Zadania można przetestować, wybierając **kompilacji teraz** ze strony projektu.

## <a name="configure-azure-app-service"></a>Konfigurowanie usługi aplikacji Azure 

1. Przy użyciu wiersza polecenia platformy Azure lub [powłoki chmury](/azure/cloud-shell/overview), Utwórz nową [aplikacji sieci Web w systemie Linux](/azure/app-service/containers/app-service-linux-intro). Nazwa aplikacji sieci web, w tym samouczku jest `myJavaApp`, ale musisz użyć unikatową nazwę aplikacji.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Utwórz [rejestru kontenera Azure](/azure/container-registry/container-registry-intro) do przechowywania obrazów Docker utworzony przez Wpięć. Nazwa rejestru kontenera używane w tym samouczku jest `jenkinsregistry`, ale musisz użyć unikatową nazwę rejestru kontenera. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Konfigurowanie aplikacji sieci web do uruchamiania do rejestru kontenera obrazy usługi Docker i określić, że aplikacja działa w kontenerze nasłuchuje żądań na porcie 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Skonfiguruj wtyczkę Wpięć usługi aplikacji Azure

1. W konsoli sieci web Wpięć wybierz **MyJavaApp** utworzonego zadania, a następnie wybierz **Konfiguruj** w lewej części strony.
2. Przewiń w dół do **akcje postkompilacyjnego**, a następnie wybierz pozycję **dodać akcję postkompilacyjnego** i wybierz polecenie **publikowania aplikacji sieci Web platformy Azure**.
3. W obszarze **konfiguracji profilu Azure**, wybierz pozycję **Dodaj** obok pozycji **poświadczenia Azure** i wybierz polecenie **Wpięć**.
4. W **dodać poświadczenia** okno dialogowe, wybierz opcję **główną usługi Microsoft Azure** z **rodzaj** listy rozwijanej.
5. Utworzyć podmiot zabezpieczeń usługi Active Directory z wiersza polecenia platformy Azure lub [powłoki chmury](/azure/cloud-shell/overview).
    
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
6. Wprowadź poświadczenia z usługą główną w **Dodawanie poświadczeń** okna dialogowego. Jeśli nie znasz identyfikator subskrypcji platformy Azure, można wykonać zapytanie, z poziomu interfejsu wiersza polecenia:
     
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

    ![Konfigurowanie nazwy głównej usługi Azure](media/jenkins-java-quickstart/azure_service_principal.png)
6. Sprawdź nazwy głównej usługi jest uwierzytelniany w usłudze Azure, wybierając **Sprawdź nazwy głównej usługi**. 
7. Wybierz **Dodaj** można zapisać poświadczenia.
8. Wybierz poświadczenia główne usługi dodane z **poświadczenia Azure** listy rozwijanej podczas pracy do **publikowania aplikacji sieci Web platformy Azure** konfiguracji.
9. W **Konfiguracja aplikacji**, wybierz grupy zasobów, a nazwa aplikacji z listy rozwijanej sieci web.
10. Wybierz **publikowania za pomocą Docker** przycisk radiowy.
11. Wprowadź `complete/Dockerfile` dla **ścieżki plik Dockerfile**.
12. Wprowadź `https://jenkinsregistry.azurecr.io` w **Docker rejestru URL** pola.
13. Wybierz **Dodaj** obok **poświadczenia rejestru**. 
14. Wprowadź nazwę użytkownika administratora rejestru kontenera Azure utworzoną w ramach **Username**.
15. Wprowadź hasło dla klucza rejestru kontenera Azure **hasło** pola. W portalu Azure lub za pomocą następującego polecenia interfejsu wiersza polecenia można uzyskać nazwy użytkownika i hasła:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Dodaj swoje poświadczenia rejestru kontenera](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Wybierz **Dodaj** można zapisać poświadczenia.
16. Wybierz nowo utworzonego poświadczenia z **poświadczenia rejestru** listy rozwijanej w **Konfiguracja aplikacji** panelu dla **publikowania aplikacji sieci Web platformy Azure**. Zakończono działanie mające miejsce po kompilacji powinien wyglądać poniższej ilustracji:   
    ![Konfiguracja akcji kompilacji POST wdrażania usługi aplikacji Azure](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Wybierz **zapisać** można zapisać konfiguracji zadania.

## <a name="deploy-the-app-from-github"></a>Wdrażanie aplikacji z usługi GitHub

1. Z Wpięć projektu, zaznacz **kompilacji teraz** wdrażania przykładowej aplikacji na platformie Azure.
2. Po zakończeniu kompilacji, Twoja aplikacja działa na platformie Azure pod jego adres URL publikowania, na przykład http://myjavaapp.azurewebsites.net.   
   ![Wyświetlanie wdrożonej aplikacji na platformie Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Wypchnij zmiany i ponownie wdrożyć

1. Z rozwidlenia Github, przejdź w sieci web, `complete/src/main/java/Hello/Application.java`. Wybierz **edytować ten plik** łącza z prawej strony interfejsu GitHub.
2. Wprowadź następujące zmiany do `home()` metody oraz zatwierdzić zmiany do głównej gałęzi w repozytorium.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. Uruchamia nową kompilację w Wpięć wyzwalane przez nowe zatwierdzenia na `master` gałęzi repozytorium. Po zakończeniu instalacji należy ponownie załadować aplikacji na platformie Azure.     
      ![Wyświetlanie wdrożonej aplikacji na platformie Azure](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>Kolejne kroki

- [Użyj maszyn wirtualnych platformy Azure zgodnie z agentów kompilacji](/azure/jenkins/jenkins-azure-vm-agents)
- [Zarządzanie zasobami w zadań i potoków z wiersza polecenia platformy Azure](/azure/jenkins/execute-cli-jenkins-pipeline)
 

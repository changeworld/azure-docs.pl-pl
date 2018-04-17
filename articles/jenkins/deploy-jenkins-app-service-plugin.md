---
title: Wdrażanie w usłudze Azure App Service za pomocą wtyczki Wpięć | Dokumentacja firmy Microsoft
description: Dowiedz się, jak umożliwia wdrażanie aplikacji sieci web Java na platformie Azure w Wpięć wtyczki Wpięć usługi aplikacji Azure
services: app-service\web
documentationcenter: ''
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 0128ad37e3ba66710279de42cf4eae0ce5431b5b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Wdrażanie w usłudze Azure App Service za pomocą wtyczki Wpięć 

Aby wdrożyć aplikację sieci web Java na platformie Azure, można użyć wiersza polecenia platformy Azure w [potoku Wpięć](/azure/jenkins/execute-cli-jenkins-pipeline) lub użyć [wtyczki Wpięć usługi aplikacji Azure](https://plugins.jenkins.io/azure-app-service). Wpięć wtyczki w wersji 1.0 obsługuje ciągłe wdrażanie przy użyciu funkcji aplikacji sieci Web w usłudze Azure App Service za pośrednictwem:
* Git i FTP.
* Docker dla aplikacji sieci Web w systemie Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj Wpięć do wdrażania aplikacji sieci Web za pomocą narzędzia Git i FTP.
> * Skonfiguruj Wpięć do wdrożenia aplikacji sieci Web dla kontenerów.

## <a name="create-and-configure-a-jenkins-instance"></a>Tworzenie i konfigurowanie wystąpienia Wpięć

Jeśli nie masz jeszcze wzorca Wpięć, rozpoczynać [szablon rozwiązania](install-jenkins-solution-template.md), która obejmuje Java Development Kit (JDK) 8 w wersji i następujących wymaganych Wpięć wtyczek:

* [Dodatek klienta Git Wpięć](https://plugins.jenkins.io/git-client) wersji 2.4.6 w brzmieniu 
* [Dodatek docker Commons](https://plugins.jenkins.io/docker-commons) wersji 1.4.0
* [Poświadczenia Azure](https://plugins.jenkins.io/azure-credentials) w wersji 1.2
* [Usługa aplikacji Azure](https://plugins.jenkins.io/azure-app-service) wersji 0,1

Dodatek Wpięć umożliwia wdrażanie aplikacji sieci web, w dowolnym języku, który jest obsługiwany przez aplikacje sieci Web, takich jak C#, PHP, Java i Node.js. W tym samouczku używamy [prostej aplikacji sieci web Java na platformie Azure](https://github.com/azure-devops/javawebappsample). Do rozwidlania repozytorium na koncie usługi GitHub, wybierz **rozwidlenia** przycisk w prawym górnym rogu interfejsu GitHub.  
> [!NOTE]
> Java JDK i Maven są wymagane do utworzenia projektu języka Java. Zainstalować te składniki na wzorcu Wpięć lub agenta maszyny Wirtualnej Użyj agenta dla ciągłej integracji. 

Aby zainstalować składniki, zaloguj się do wystąpienia Wpięć przy użyciu protokołu SSH i uruchom następujące polecenia:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Aby wdrożyć aplikację sieci Web dla kontenerów, należy zainstalować Docker na wzorcu Wpięć lub agenta maszyny Wirtualnej, który jest używany dla kompilacji. Aby uzyskać instrukcje, zobacz [zainstalować Docker na Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a> Dodaj nazwę główną usługi Azure poświadczenia Wpięć

Należy nazwy głównej usługi Azure, aby wdrożyć na platformie Azure. 


1. Aby utworzyć nazwy głównej usługi platformy Azure, użyj [interfejsu wiersza polecenia Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) lub [portalu Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. Na pulpicie nawigacyjnym Wpięć wybierz **poświadczenia** > **systemu**. Następnie wybierz opcję **credentials(unrestricted) globalne**.
3. Aby dodać nazwy głównej usługi Microsoft Azure, wybierz **dodać poświadczeń**. Podaj wartości dla **identyfikator subskrypcji**, **identyfikator klienta**, **klucz tajny klienta**, i **końcowym tokenów OAuth 2.0** pola. Ustaw **identyfikator** do **mySp**. Firma Microsoft za pomocą tego Identyfikatora w kolejnych krokach, w tym artykule.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Skonfiguruj Wpięć do wdrażania aplikacji sieci Web przez przekazywanie plików

Na potrzeby wdrażania projektu aplikacji sieci Web, możesz przekazać Twojej artefaktów kompilacji (na przykład plik WAR w języku Java) przy użyciu narzędzia Git i FTP.

Przed skonfigurowaniem zadania w Wpięć należy plan usługi aplikacji Azure i aplikacji sieci web, aby uruchomić aplikację Java.


1. Tworzenie planu usługi aplikacji Azure z **wolne** warstwy cenowej przy użyciu `az appservice plan create` [polecenia interfejsu wiersza polecenia Azure](/cli/azure/appservice/plan#az_appservice_plan_create). Plan usługi aplikacji określa zasoby fizyczne, które są używane do hostowania aplikacji. Wszystkie aplikacje, które są przypisane do planu usługi App Service udostępniania tych zasobów. Udostępnione zasoby ułatwiają zapisać kosztów odnośnie do hostowania wielu aplikacji.
2. Utwórz aplikację sieci Web. Można użyć [portalu Azure](/azure/app-service-web/web-sites-configure) lub następujące `az` polecenia wiersza polecenia platformy Azure:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Ustawianie konfiguracji środowiska uruchomieniowego języka Java, które Twoja aplikacja powinna. Aplikacja sieci web do uruchamiania na ostatnie 8 JDK konfiguruje następujące polecenie z wiersza polecenia platformy Azure i [Apache Tomcat](http://tomcat.apache.org/) wersji 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Konfigurowanie zadania Wpięć

1. Utwórz nową **dowolne** projektu na pulpicie nawigacyjnym Wpięć.
2. Skonfiguruj **zarządzania kodem źródłowym** pola do użycia lokalnego rozwidlenia z [prostej aplikacji sieci web Java na platformie Azure](https://github.com/azure-devops/javawebappsample). Podaj **adres URL repozytorium** wartość. Na przykład: http://github.com/ &lt;your_ID > / javawebappsample.
3. Dodaj krok, aby skompilować projekt za pomocą narzędzia Maven, dodając **wykonywania powłoki** polecenia. W tym przykładzie potrzebne dodatkowe polecenia, aby zmienić nazwę \*.war plik w folderze docelowym, aby **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Dodaj akcję po kompilacji, wybierając **publikowania aplikacji sieci Web platformy Azure**.
5. Podaj **mySp** jako podmiot zabezpieczeń usługi Azure. Tego podmiotu zabezpieczeń są przechowywane jako [poświadczenia Azure](#service-principal) w poprzednim kroku.
6. W **Konfiguracja aplikacji** wybierz aplikację sieci web i grupy zasobów w ramach subskrypcji. Dodatek Wpięć automatycznie wykrywa, czy aplikacja sieci web jest oparta na systemie Windows lub Linux. W przypadku aplikacji sieci web systemu Windows **publikowania plików** przedstawiono opcji.
7. Wypełnij pliki, które mają zostać wdrożone. Określ pakiet, WAR na przykład, jeśli używasz języka Java. Użyj opcjonalnego **katalog źródłowy** i **katalog docelowy** parametrów, aby określić foldery źródłowy i docelowy, na potrzeby przekazywania plików. Aplikacja sieci web Java na platformie Azure jest uruchamiane na serwerze Tomcat. Dlatego dla języka Java, możesz przekazać pakietu WAR do folderu webapps. Na przykład ustawić **katalog źródłowy** do wartości **docelowej** i **katalog docelowy** do wartości **webapps**.
8. Jeśli chcesz wdrożyć w miejscu innym niż produkcji, można również ustawić **miejsca** nazwy.
9. Zapisz projekt i skompiluj go. Aplikacja sieci web jest wdrażany na platformie Azure po zakończeniu kompilacji.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Wdrażanie aplikacji sieci Web przez przekazywanie plików za pomocą potoku Wpięć

Wtyczka Wpięć usługi aplikacji Azure jest gotowe do potoku. Może się odwoływać do poniższego przykładu w repozytorium GitHub.

1. Otwórz w interfejsie GitHub **Jenkinsfile_ftp_plugin** pliku. Aby edytować plik, wybierz ikonę ołówka. Aktualizacja **resourceGroup** i **webAppName** definicje dla aplikacji sieci web na wierszach 11 i 12:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Ustaw **withCredentials** definicję w wierszu 14 do Identyfikatora poświadczeń w wystąpieniu Wpięć:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Tworzenie potoku Wpięć

1. Otwórz Wpięć w przeglądarce sieci web. Wybierz **nowy element**.
2. Podaj nazwę zadania i wybierz **potoku**. Kliknij przycisk **OK**.
3. Wybierz **potoku** kartę.
4. Dla **definicji** wartości, wybierz opcję **potoku skryptu z SCM**.
5. Aby uzyskać **SCM** wartości, wybierz opcję **Git**. Wprowadź adres URL usługi GitHub dla programu rozwidlonych repozytorium. Na przykład: https://&lt;your_forked_repo > .git.
6. Aktualizacja **ścieżka skryptu** do wartości **Jenkinsfile_ftp_plugin**.
7. Wybierz **zapisać** i uruchom zadanie.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Skonfiguruj Wpięć do wdrożenia aplikacji sieci Web dla kontenerów

Aplikacje w systemie Linux w sieci Web obsługuje wdrażanie przy użyciu rozwiązania Docker. Aby wdrożyć aplikację sieci web przy użyciu rozwiązania Docker, należy dostarczyć plik Dockerfile, które pakiety aplikacji sieci web o środowisku uruchomieniowym usługi do obrazu Docker. Następnie wtyczki Wpięć tworzy obraz, wypchnięcia jej do rejestru Docker i wdraża obraz do aplikacji sieci web.

Aplikacje w systemie Linux w sieci Web obsługuje również metody tradycyjnego wdrażania, takie jak usługi Git i FTP, ale tylko dla wbudowanych języków (.NET Core, Node.js, PHP i Ruby). W przypadku języków musisz pakietu aplikacji runtime kod i usługi ze sobą w obrazie Docker i wdrażanie przy użyciu rozwiązania Docker.

Przed skonfigurowaniem zadania w Wpięć, należy w aplikacji sieci web w systemie Linux. Należy również rejestru kontener do przechowywania i zarządzania nimi prywatnej obrazy usługi Docker kontenera. DockerHub służy do tworzenia rejestru kontenera. W tym przykładzie używamy rejestru kontenera platformy Azure.

* [Tworzenie aplikacji sieci web w systemie Linux](../app-service/containers/quickstart-nodejs.md).
* Rejestru kontenera platformy Azure jest zarządzanego [rejestru Docker](https://docs.docker.com/registry/) usługi, która jest oparta na typu open source rejestru Docker w wersji 2.0. [Tworzenie kontenera platformy Azure rejestru](/azure/container-registry/container-registry-get-started-azure-cli). Można również użyć DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Konfigurowanie zadania Wpięć for Docker

1. Utwórz nową **dowolne** projektu na pulpicie nawigacyjnym Wpięć.
2. Skonfiguruj **zarządzania kodem źródłowym** pola do użycia lokalnego rozwidlenia z [prostej aplikacji sieci web Java na platformie Azure](https://github.com/azure-devops/javawebappsample). Podaj **adres URL repozytorium** wartość. Na przykład: http://github.com/ &lt;your_ID > / javawebappsample.
3. Dodaj krok, aby skompilować projekt za pomocą narzędzia Maven, dodając **wykonywania powłoki** polecenia. Dołącz następujący wiersz polecenia:
    ```bash
    mvn clean package
    ```

4. Dodaj akcję po kompilacji, wybierając **publikowania aplikacji sieci Web platformy Azure**.
5. Podaj **mySp** jako podmiot zabezpieczeń usługi Azure. Tego podmiotu zabezpieczeń są przechowywane jako [poświadczenia Azure](#service-principal) w poprzednim kroku.
6. W **Konfiguracja aplikacji** wybierz grupę zasobów i aplikacji sieci web systemu Linux w Twojej subskrypcji.
7. Wybierz **publikowanie za pośrednictwem Docker**.
8. Wypełnij **plik Dockerfile** wartość ścieżki. Można zachować /Dockerfile wartość domyślną.
Dla **URL rejestru Docker** wartość, podaj adres URL przy użyciu formatu https://&lt;yourRegistry >. azurecr.io, korzystając z rejestru kontenera platformy Azure. Jeśli używasz DockerHub, pozostaw pustą wartość.
9. Dla **poświadczenia rejestru** wartość, Dodaj poświadczenie rejestru kontenera. Identyfikator użytkownika i hasło można uzyskać, uruchamiając następujące polecenia w wiersza polecenia platformy Azure. Pierwsze polecenie umożliwia konta administratora:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Docker nazwy i tagu wartość obrazu w **zaawansowane** kartę są opcjonalne. Domyślnie wartość nazwy obrazów są uzyskiwane z nazwa obrazu, skonfigurowanego w portalu Azure w **kontenera Docker** ustawienie. Tag jest generowany na podstawie $BUILD_NUMBER.
    > [!NOTE]
    > Należy określić nazwę obrazu w portalu Azure lub podać **obrazu Docker** wartość w **zaawansowane** kartę. Na przykład ustawić **obrazu Docker** do wartości &lt;your_Registry >.azurecr.io/calculator i pozostaw **Tag obrazu Docker** wartość pustą.

11. Wdróż kończy się niepowodzeniem, jeśli używasz wbudowanych ustawienie obrazu Docker. Zmień konfigurację Docker, aby użyć niestandardowego obrazu w **kontenera Docker** ustawienie w portalu Azure. Wbudowane obrazu należy użyć metody przekazywania plików do wdrożenia.
12. Podobnie jak metoda przekazywania plików, można wybrać inną **miejsca** inne niż nazwa **produkcji**.
13. Zapisz i skompilować projekt. Obraz kontener jest przypisany do rejestru i wdrożeniu aplikacji sieci web.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Wdrażanie aplikacji sieci Web dla kontenerów za pomocą potoku Wpięć

1. Otwórz w interfejsie GitHub **Jenkinsfile_container_plugin** pliku. Aby edytować plik, wybierz ikonę ołówka. Aktualizacja **resourceGroup** i **webAppName** definicje dla aplikacji sieci web na wierszach 11 i 12:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Zmień wiersz 13 serwera rejestru kontenera:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Zmień wiersz 16, aby użyć wystąpienia Wpięć identyfikator poświadczeń:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Tworzenie potoku Wpięć    

1. Otwórz Wpięć w przeglądarce sieci web. Wybierz **nowy element**.
2. Podaj nazwę zadania i wybierz **potoku**. Kliknij przycisk **OK**.
3. Wybierz **potoku** kartę.
4. Dla **definicji** wartości, wybierz opcję **potoku skryptu z SCM**.
5. Aby uzyskać **SCM** wartości, wybierz opcję **Git**. Wprowadź adres URL usługi GitHub dla programu rozwidlonych repozytorium. Na przykład: https://&lt;your_forked_repo > .git.
7. Aktualizacja **ścieżka skryptu** do wartości **Jenkinsfile_container_plugin**.
8. Wybierz **zapisać** i uruchom zadanie.

## <a name="verify-your-web-app"></a>Sprawdź aplikację sieci web

1. Aby sprawdzić, czy plik WAR został wdrożony pomyślnie na aplikację sieci web, otwórz przeglądarkę sieci web.
2. Przejdź do http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Zastąp &lt;your_app_name > nazwą swojej aplikacji sieci web. Zostanie wyświetlony komunikat:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Przejdź do http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Zastąp &lt;x > i &lt;y > z dowolnej liczby można pobrać sumy x + y. Kalkulator zawiera sumę: ![Kalkulator: Dodaj](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Dla usługi Azure App Service w systemie Linux

1. Aby zweryfikować aplikacji sieci web, uruchom następujące polecenie w wiersza polecenia platformy Azure:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Zostanie wyświetlony następujący komunikat:
    ```CLI
    ["calculator"]
    ```
    
2. Przejdź do http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Zastąp &lt;your_app_name > nazwą swojej aplikacji sieci web. Zostanie wyświetlony komunikat: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Przejdź do http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Zastąp &lt;x > i &lt;y > z dowolnej liczby można pobrać sumy x + y.
    
## <a name="next-steps"></a>Kolejne kroki

W tym samouczku użyto wtyczki Wpięć usługi aplikacji Azure do wdrożenia na platformie Azure.

W tym samouczku omówiono:

> [!div class="checklist"]
> * Skonfiguruj Wpięć do wdrożenia usługi Azure App Service za pośrednictwem FTP 
> * Skonfiguruj Wpięć do wdrożenia aplikacji sieci Web dla kontenerów 

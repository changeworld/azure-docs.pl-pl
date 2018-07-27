---
title: Wdrażanie w usłudze Azure App Service przy użyciu wtyczki narzędzia Jenkins
description: Dowiedz się, jak dodatek Jenkins usługi aplikacji Azure umożliwia wdrażanie aplikacji sieci web Java na platformie Azure w usłudze Jenkins
ms.topic: article
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/25/2018
ms.openlocfilehash: 407ec2bbb145e73b1a903886204b660aadc9a65f
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284418"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Wdrażanie w usłudze Azure App Service przy użyciu wtyczki narzędzia Jenkins 

Aby wdrażanie aplikacji sieci web Java na platformie Azure, można użyć wiersza polecenia platformy Azure w [potoku Jenkins](/azure/jenkins/execute-cli-jenkins-pipeline) lub użyć [dodatek Jenkins usługi aplikacji Azure](https://plugins.jenkins.io/azure-app-service). Jenkins wtyczki w wersji 1.0 obsługuje ciągłe wdrażanie za pomocą funkcji Web Apps w usłudze Azure App Service za pomocą:
* Git i FTP.
* Platformy docker dla aplikacji sieci Web w systemie Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie usługi Jenkins, jak wdrażać aplikacje sieci Web za pomocą narzędzia Git i FTP.
> * Konfigurowanie usługi Jenkins, aby wdrożyć Web App for Containers.

## <a name="create-and-configure-a-jenkins-instance"></a>Utwórz i skonfiguruj wystąpienie narzędzia Jenkins

Jeśli nie masz jeszcze wzorca usługi Jenkins, skorzystaj z [szablon rozwiązania](install-jenkins-solution-template.md)następujących wymaganych wtyczek Jenkins i w tym Java Development Kit (JDK) w wersji 8:

* [Dodatek klienta usługi Jenkins Git](https://plugins.jenkins.io/git-client) wersji 2.4.6 w brzmieniu 
* [Wtyczka platformy docker Commons](https://plugins.jenkins.io/docker-commons) wersji 1.4.0
* [Poświadczenia platformy Azure](https://plugins.jenkins.io/azure-credentials) w wersji 1.2
* [Usługa Azure App Service](https://plugins.jenkins.io/azure-app-service) wersja 0.1

Dodatek Jenkins umożliwia wdrażanie aplikacji sieci web w taki sposób, w dowolnym języku, który jest obsługiwany przez aplikacje sieci Web, takie jak językach C#, PHP, Java i Node.js. W tym samouczku używamy [prostej aplikacji sieci web Java na platformie Azure](https://github.com/azure-devops/javawebappsample). Aby utworzyć rozwidlenie repozytorium na koncie usługi GitHub, wybierz pozycję **rozwidlenia** przycisk w prawym górnym rogu interfejsu usługi GitHub.  
> [!NOTE]
> Zestaw JDK języka Java i Maven, są wymagane do utworzenia projektu języka Java. Zainstalować te składniki na wzorca usługi Jenkins, lub agenta maszyny Wirtualnej Użyj agenta w celu zapewnienia ciągłej integracji. 

Aby zainstalować składniki, zaloguj się do wystąpienia narzędzia Jenkins przy użyciu protokołu SSH i uruchom następujące polecenia:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Aby wdrożyć w sieci Web App for Containers, należy zainstalować platformę Docker na wzorca usługi Jenkins lub agenta maszyny Wirtualnej, który jest używany dla kompilacji. Aby uzyskać instrukcje, zobacz [instalowanie platformy Docker w systemie Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a> Dodawanie jednostki usługi Azure poświadczenia usługi Jenkins

Należy jednostka usługi platformy Azure, aby wdrożyć na platformie Azure. 


1. Aby Tworzenie jednostki usługi platformy Azure, należy użyć [wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) lub [witryny Azure portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. Na pulpicie nawigacyjnym usługi Jenkins wybierz **poświadczenia** > **systemu**. Następnie wybierz **globalnego credentials(unrestricted)**.
3. Aby dodać nazwy głównej usługi Microsoft Azure, wybierz **Dodaj poświadczenia**. Podaj wartości dla **identyfikator subskrypcji**, **identyfikator klienta**, **klucz tajny klienta**, i **punkt końcowy protokołu OAuth 2.0 Token** pola. Ustaw **identyfikator** pole **mySp**. Używamy tego Identyfikatora w kolejnych krokach w tym artykule.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Konfigurowanie usługi Jenkins wdrażania aplikacji sieci Web za przekazywanie plików

Aby wdrożyć projekt aplikacji sieci Web, możesz przekazać swoje artefakty kompilacji (na przykład plik WAR w języku Java) przy użyciu narzędzia Git i FTP.

Przed skonfigurowaniem zadania w usłudze Jenkins należy planu usługi Azure App Service i aplikację internetową, aby uruchomić aplikację języka Java.


1. Utwórz plan usługi Azure App Service za pomocą **bezpłatna** warstwę cenową za pomocą `az appservice plan create` [polecenia wiersza polecenia platformy Azure](/cli/azure/appservice/plan#az_appservice_plan_create). Plan usługi App Service definiuje zasoby fizyczne, które są używane do hostowania aplikacji. Wszystkie aplikacje, które są przypisane do planu usługi App Service współdzielą te zasoby. Udostępnione zasoby pomagają zmniejszyć koszty hostowania wielu aplikacji.
2. Utwórz aplikację sieci Web. Możesz użyć [witryny Azure portal](/azure/app-service-web/web-sites-configure) lub następujące znaki `az` polecenia wiersza polecenia platformy Azure:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Ustaw konfigurację środowiska uruchomieniowego języka Java wymaganą przez aplikację. Następujące polecenie z wiersza polecenia platformy Azure umożliwia skonfigurowanie aplikacji sieci web do uruchamiania na ostatnie 8 JDK i [Apache Tomcat](http://tomcat.apache.org/) wersji 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Konfigurowanie zadania narzędzia Jenkins

1. Utwórz nową **dowolne** projektu na pulpicie nawigacyjnym usługi Jenkins.
2. Konfigurowanie **zarządzania kodem źródłowym** pole do użycia w Twoim rozwidleniu lokalnym [prostej aplikacji sieci web Java na platformie Azure](https://github.com/azure-devops/javawebappsample). Podaj **adres URL repozytorium** wartość. Na przykład: http://github.com/&lt; your_ID > / javawebappsample.
3. Dodaj krok, aby skompilować projekt przy użyciu narzędzia Maven, dodając **wykonaj powłokę** polecenia. W tym przykładzie potrzebujemy dodatkowych polecenie, aby zmienić \*plik WAR w folderze docelowym, aby **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Dodawanie akcji wykonywanych po kompilacji, wybierając **opublikować aplikację internetową platformy Azure**.
5. Podaj **mySp** jako jednostka usługi platformy Azure. Ta jednostka została zapisana jako [Azure Credentials](#service-principal) w poprzednim kroku.
6. W **konfiguracji aplikacji** sekcji, wybierz aplikację sieci web i grupy zasobów w ramach subskrypcji. Dodatek Jenkins automatycznie wykrywa, czy aplikacja sieci web jest oparty na Windows lub Linux. W przypadku aplikacji sieci web Windows **publikowania plików** opcję jest przedstawiany.
7. Wypełnij pliki, które mają zostać wdrożone. Na przykład należy określić pakiet WAR, jeśli używasz języka Java. Użyj opcjonalnego **katalog źródłowy** i **katalog docelowy** w celu określenia folderu źródłowego i docelowego na potrzeby przekazywania plików. Aplikacji sieci web Java na platformie Azure jest uruchamiane na serwerze Tomcat. Dlatego dla języka Java, możesz przekazać pakiet WAR w folderze aplikacji internetowych. W tym przykładzie należy ustawić **katalog źródłowy** wartość **docelowej** i **katalog docelowy** wartość, która **webapps**.
8. Jeśli chcesz wdrożyć w miejscu innym niż produkcyjne, możesz również ustawić **miejsca** nazwy.
9. Zapisz projekt i skompiluj je. Twoja aplikacja sieci web jest wdrażane na platformie Azure po zakończeniu kompilacji.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Wdrażanie aplikacji sieci Web, przekazując pliki przy użyciu narzędzia Jenkins potoku

Dodatek Jenkins usługi aplikacji Azure jest gotowa do użycia w potoku. Można odwołać się do poniższego przykładu w repozytorium GitHub.

1. W interfejsie usługi GitHub, otwórz **Jenkinsfile_ftp_plugin** pliku. Aby edytować plik, wybierz ikonę ołówka. Aktualizacja **resourceGroup** i **webAppName** definicje dla aplikacji sieci web na wierszach 11 i 12:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Ustaw **withCredentials** definicję w wierszu 14 z Identyfikatorem wystąpienia usługi Jenkins na poświadczeń:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Tworzenie potoku usługi Jenkins

1. Otwórz narzędzia Jenkins w przeglądarce sieci web. Wybierz pozycję **New Item** (Nowy element).
2. Podaj nazwę zadania, a następnie wybierz pozycję **potoku**. Kliknij przycisk **OK**.
3. Wybierz **potoku** kartę.
4. Dla **definicji** wartości, wybierz opcję **potoku skrypt z SCM**.
5. Aby uzyskać **SCM** wartości, wybierz opcję **Git**. Wprowadź adres URL usługi GitHub do rozwidlonego repozytorium. Na przykład: https://&lt;your_forked_repo > .git.
6. Aktualizacja **ścieżka skryptu** wartość **Jenkinsfile_ftp_plugin**.
7. Wybierz **Zapisz** i uruchom zadanie.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Konfigurowanie usługi Jenkins, aby wdrożyć Web App for Containers

Usługa Web Apps w systemie Linux obsługuje wdrażanie przy użyciu platformy Docker. Aby wdrożyć aplikację sieci web za pomocą platformy Docker, należy podać plik Dockerfile, które pakiety aplikacji sieci web za pomocą środowiska uruchomieniowego usługi do obrazu platformy Docker. Następnie dodatek Jenkins tworzy obraz, wypychany do rejestru platformy Docker i wdraża obraz do aplikacji sieci web.

Usługa Web Apps w systemie Linux obsługuje również tradycyjne metody wdrażania, takich jak usługi Git i FTP, ale tylko dla wbudowanych języków (.NET Core, Node.js, PHP i Ruby). W przypadku innych języków należy spakować Twojego kodu i usługa środowisko uruchomieniowe aplikacji razem w obrazu platformy Docker i wdrażanie przy użyciu platformy Docker.

Przed skonfigurowaniem zadania w usłudze Jenkins, potrzebujesz aplikacji sieci web w systemie Linux. Należy również rejestru kontenerów, przechowywać i zarządzać nimi prywatnych obrazów kontenerów Docker. Za pomocą witryny DockerHub do utworzenia kontenera rejestru. W tym przykładzie używamy usługi Azure Container Registry.

* [Tworzenie aplikacji sieci web w systemie Linux](../app-service/containers/quickstart-nodejs.md).
* Usługa Azure Container Registry to zarządzana [rejestru platformy Docker](https://docs.docker.com/registry/) usługa, która opiera się na "open source" rejestru platformy Docker w wersji 2.0. [Utwórz rejestr Azure container registry](/azure/container-registry/container-registry-get-started-azure-cli). Można również użyć witryny DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Konfigurowanie zadania narzędzia Jenkins dla platformy Docker

1. Utwórz nową **dowolne** projektu na pulpicie nawigacyjnym usługi Jenkins.
2. Konfigurowanie **zarządzania kodem źródłowym** pole do użycia w Twoim rozwidleniu lokalnym [prostej aplikacji sieci web Java na platformie Azure](https://github.com/azure-devops/javawebappsample). Podaj **adres URL repozytorium** wartość. Na przykład: http://github.com/&lt; your_ID > / javawebappsample.
3. Dodaj krok, aby skompilować projekt przy użyciu narzędzia Maven, dodając **wykonaj powłokę** polecenia. W poleceniu, należy umieścić następujący wiersz:
    ```bash
    mvn clean package
    ```

4. Dodawanie akcji wykonywanych po kompilacji, wybierając **opublikować aplikację internetową platformy Azure**.
5. Podaj **mySp** jako jednostka usługi platformy Azure. Ta jednostka została zapisana jako [Azure Credentials](#service-principal) w poprzednim kroku.
6. W **konfiguracji aplikacji** sekcji, wybierz grupę zasobów i aplikację internetową systemu Linux w ramach subskrypcji.
7. Wybierz **publikowanie za pośrednictwem Docker**.
8. Wypełnij **pliku Dockerfile** wartość ścieżki. Możesz zachować /Dockerfile wartość domyślną.
Aby uzyskać **adres URL rejestru platformy Docker** wartość, należy podać adres URL przy użyciu format https://&lt;yourRegistry >. azurecr.io, jeśli używasz usługi Azure Container Registry. Jeśli używasz witryny DockerHub, pozostaw pustą wartość.
9. Aby uzyskać **poświadczeń rejestru** wartość, należy dodać poświadczenia dla rejestru kontenerów. Identyfikator użytkownika i hasło można uzyskać, uruchamiając następujące polecenia w interfejsie wiersza polecenia platformy Azure. Pierwsze polecenie włącza konta administratora:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Docker image nazwę oraz tag wartość w **zaawansowane** karty są opcjonalne. Domyślnie wartość dla nazwy obrazów są uzyskiwane z nazwę obrazu, który został skonfigurowany w witrynie Azure portal w **kontener platformy Docker** ustawienie. Tag jest generowany na podstawie $BUILD_NUMBER.
    > [!NOTE]
    > Pamiętaj określić nazwę obrazu w witrynie Azure portal lub podać **obrazu platformy Docker** wartość w **zaawansowane** kartę. W tym przykładzie należy ustawić **obrazu platformy Docker** wartość &lt;your_Registry >.azurecr.io/calculator pracy i opuszczania **Tag obrazu platformy Docker** wartość pustą.

11. Wdrażanie nie powiedzie się, korzystając z wbudowanego ustawienia obrazu platformy Docker. Zmień konfigurację platformy Docker, aby użyć obrazu niestandardowego w **kontener platformy Docker** ustawienia w witrynie Azure portal. Wbudowanego obrazu należy użyć metody przekazywania plików do wdrożenia.
12. Podobnie jak metoda przekazywania pliku, możesz wybrać inną **miejsce** inne niż nazwa **produkcji**.
13. Zapisz i skompiluj projekt. Obraz kontenera jest wypchnięte do rejestru i wdrażanie aplikacji sieci web.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Wdrażanie Web App for Containers przy użyciu narzędzia Jenkins potoku

1. W interfejsie usługi GitHub, otwórz **Jenkinsfile_container_plugin** pliku. Aby edytować plik, wybierz ikonę ołówka. Aktualizacja **resourceGroup** i **webAppName** definicje dla aplikacji sieci web na wierszach 11 i 12:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Zmień wiersz 13 do serwera rejestru kontenerów:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Zmień wiersz 16 do użycia identyfikator poświadczeń do wystąpienia usługi Jenkins:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Tworzenie potoku usługi Jenkins    

1. Otwórz narzędzia Jenkins w przeglądarce sieci web. Wybierz pozycję **New Item** (Nowy element).
2. Podaj nazwę zadania, a następnie wybierz pozycję **potoku**. Kliknij przycisk **OK**.
3. Wybierz **potoku** kartę.
4. Dla **definicji** wartości, wybierz opcję **potoku skrypt z SCM**.
5. Aby uzyskać **SCM** wartości, wybierz opcję **Git**. Wprowadź adres URL usługi GitHub do rozwidlonego repozytorium. Na przykład: https://&lt;your_forked_repo > .git.
7. Aktualizacja **ścieżka skryptu** wartość **Jenkinsfile_container_plugin**.
8. Wybierz **Zapisz** i uruchom zadanie.

## <a name="verify-your-web-app"></a>Sprawdź aplikację sieci web

1. Aby sprawdzić, czy plik WAR jest pomyślnie wdrożone w aplikacji sieci web, otwórz przeglądarkę sieci web.
2. Przejdź do http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Zastąp &lt;your_app_name > nazwą aplikacji sieci web. Zostanie wyświetlony komunikat:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Przejdź do http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Zastąp &lt;x > i &lt;y > za pomocą dowolne liczby w celu uzyskania sumy x + y. Kalkulator zawiera sumę: ![Kalkulatora: Dodaj](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Azure App Service w systemie Linux

1. Aby sprawdzić, czy Twoja aplikacja sieci web, uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Zostanie wyświetlony następujący komunikat:
    ```CLI
    ["calculator"]
    ```
    
2. Przejdź do http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Zastąp &lt;your_app_name > nazwą aplikacji sieci web. Zostanie wyświetlony komunikat: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Przejdź do http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Zastąp &lt;x > i &lt;y > za pomocą dowolne liczby w celu uzyskania sumy x + y.
    
## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli występują jakiekolwiek z wtyczek Jenkins, Prześlij zgłoszenie do [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku dodatek Jenkins usługi aplikacji Azure jest używana do wdrażania na platformie Azure.

W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie usługi Jenkins do wdrożenia usługi Azure App Service za pośrednictwem protokołu FTP 
> * Konfigurowanie usługi Jenkins, aby wdrożyć w usłudze Web App for Containers 

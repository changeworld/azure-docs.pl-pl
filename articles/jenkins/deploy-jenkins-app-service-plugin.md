---
title: Wdrażanie w usłudze Azure App Service przy użyciu wtyczki Jenkins
description: Dowiedz się, jak przy użyciu wtyczki Jenkins usługi Azure App Service wdrożyć aplikację internetową Java na platformie Azure na serwerze Jenkins
keywords: jenkins, azure, devops, usługa app service
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: fcaf45003e865cc5aac3f6bd4580479a27d38b50
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251456"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Wdrażanie w usłudze Azure App Service przy użyciu wtyczki Jenkins 

Aby wdrożyć aplikację internetową Java na platformie Azure, można użyć interfejsu wiersza polecenia platformy Azure w [potoku Jenkins](/azure/jenkins/execute-cli-jenkins-pipeline) lub [wtyczki Jenkins usługi Azure App Service](https://plugins.jenkins.io/azure-app-service). Wtyczka Jenkins w wersji 1.0 obsługuje ciągłe wdrażanie za pomocą funkcji Web Apps usługi Azure App Service za pośrednictwem:
* przekazywania pliku.
* platformy Docker dla usługi Web Apps on Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie serwera Jenkins w celu wdrożenia funkcji Web Apps za pośrednictwem przekazywania plików.
> * Konfigurowanie serwera Jenkins w celu wdrożenia funkcji Web App for Containers.

## <a name="create-and-configure-a-jenkins-instance"></a>Tworzenie i konfigurowanie wystąpienia serwera Jenkins

Jeśli nie masz jeszcze głównego serwera Jenkins, zacznij od tego [szablonu rozwiązania](install-jenkins-solution-template.md), który zawiera zestaw Java Development Kit (JDK) w wersji 8 oraz następujące wymagane wtyczki Jenkins:

* [Wtyczka Jenkins dla klienta Git](https://plugins.jenkins.io/git-client) w wersji 2.4.6 
* [Wtyczka Docker Commons](https://plugins.jenkins.io/docker-commons) w wersji 1.4.0
* [Poświadczenia platformy Azure](https://plugins.jenkins.io/azure-credentials) w wersji 1.2
* [Usługa Azure App Service](https://plugins.jenkins.io/azure-app-service) w wersji 0.1

Wtyczki Jenkins można użyć do wdrożenia aplikacji sieci Web w dowolnym języku obsługiwanym przez Web Apps, takich jak C#php, Python, Java i Node. js. W tym samouczku użyjemy [prostej aplikacji internetowej Java dla platformy Azure](https://github.com/azure-devops/javawebappsample). Aby utworzyć rozwidlenie repozytorium na swoim koncie usługi GitHub, wybierz przycisk **Fork** (Rozwidlenie) w prawym górnym rogu interfejsu usługi GitHub.  

> [!NOTE]
> Do utworzenia projektu języka Java wymagany jest zestaw Java JDK i narzędzie Maven. Zainstaluj te składniki na głównym serwerze Jenkins lub na agencie maszyny wirtualnej, jeśli używasz agenta w celu zapewnienia ciągłej integracji. Jeśli wdrażasz aplikację Java SE, na serwerze kompilacji jest również wymagany plik ZIP.
>

Aby je zainstalować, zaloguj się na wystąpieniu serwera Jenkins za pomocą protokołu SSH i uruchom następujące polecenia:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Aby przeprowadzić wdrożenie dla funkcji Web App for Containers, zainstaluj platformę Docker na głównym serwerze Jenkins lub na agencie maszyny wirtualnej używanym na potrzeby tej kompilacji. Aby uzyskać instrukcje, zobacz [Zainstaluj platformę Docker w systemie Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

## <a name="service-principal"></a> Dodawanie jednostki usługi platformy Azure do poświadczeń serwera Jenkins

W celu wdrażania na platformie Azure potrzebna jest jednostka usługi platformy Azure. 


1. Aby utworzyć jednostkę usługi platformy Azure, użyj [interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) lub [witryny Azure Portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. Na pulpicie nawigacyjnym serwera Jenkins wybierz pozycję **Credentials** > **System** (Poświadczenia > System). Następnie wybierz pozycję **Global credentials (unrestricted)** (Poświadczenia globalne [bez ograniczeń]).
3. Aby dodać jednostkę usługi platformy Microsoft Azure, wybierz pozycję **Add Credentials** (Dodaj poświadczenia). Podaj wartości w polach **Subscription ID** (Identyfikator subskrypcji), **Client ID** (Identyfikator klienta), **Client Secret** (Wpis tajny klienta) i **OAuth 2.0 Token Endpoint** (Punkt końcowy tokenu OAuth 2.0). W polu **ID** ustaw wartość **mySp**. Będziemy używać tego identyfikatora w kolejnych krokach w tym artykule.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Konfigurowanie serwera Jenkins w celu wdrożenia funkcji Web Apps przez przekazanie plików

Aby wdrożyć projekt w funkcji Web Apps, możesz przekazać artefakty kompilacji za pośrednictwem przekazywania pliku. Usługa Azure App Service obsługuje wiele opcji wdrażania. Dodatek Jenkins dla usługi Azure App Service upraszcza wdrażanie i oferuje opcję wdrażania w zależności od typu pliku. 

* W przypadku aplikacji Java EE jest używane [wdrożenie WAR](/azure/app-service/deploy-zip#deploy-war-file).
* W przypadku aplikacji Java SE jest używane [wdrożenie ZIP](/azure/app-service/deploy-zip#deploy-zip-file).
* W przypadku innych języków jest używane [wdrożenie Git](/azure/app-service/deploy-local-git).

Zanim skonfigurujesz zadanie na serwerze Jenkins, potrzebujesz planu usługi Azure App Service i aplikacji internetowej do uruchomienia aplikacji Java.


1. Utwórz plan Azure App Service za pomocą warstwy cenowej **bezpłatna** przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/appservice/plan#az-appservice-plan-create)`az appservice plan create`. Plan usługi App Service definiuje zasoby fizyczne używane do hostowania aplikacji. Wszystkie aplikacje przypisane do planu usługi App Service współdzielą te zasoby. Zasoby współdzielone pomagają zmniejszyć koszty w przypadku hostowania wielu aplikacji.
2. Utwórz aplikację internetową. Możesz użyć [witryny Azure Portal](/azure/app-service/configure-common) lub następującego `az` polecenia interfejsu wiersza polecenia platformy Azure:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Ustaw konfigurację środowiska uruchomieniowego języka Java wymaganą przez aplikację. Następujące polecenie interfejsu wiersza polecenia platformy Azure konfiguruje aplikację internetową do uruchamiania razem z zestawem JDK 8 i środowiskiem [Apache Tomcat](https://tomcat.apache.org/) w wersji 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Konfigurowanie zadania serwera Jenkins

1. Utwórz nowy **dowolny** projekt na pulpicie nawigacyjnym serwera Jenkins.
2. Skonfiguruj pole **Source Code Management** (Zarządzanie kodem źródłowym) w celu użycia Twojego rozwidlenia lokalnego [prostej aplikacji internetowej Java dla platformy Azure](https://github.com/azure-devops/javawebappsample). Podaj wartość **Repository URL** (Adres URL repozytorium). Na przykład: http:\//github.com/&lt;your_ID >/javawebappsample.
3. Dodaj krok kompilowania projektu przy użyciu narzędzia Maven, dodając polecenie **Execute shell** (Wykonaj powłokę). W tym przykładzie potrzebujemy dodatkowego polecenia, aby zmienić nazwę pliku \*.war w folderze docelowym na **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Dodaj akcję wykonywaną po kompilacji, wybierając pozycję **Publish an Azure Web App** (Opublikuj aplikację internetową platformy Azure).
5. Podaj identyfikator **mySp** jako jednostkę usługi platformy Azure. Ta jednostka została zapisana jako [poświadczenia platformy Azure](#service-principal) w poprzednim kroku.
6. W sekcji **App Configuration** (Konfiguracja aplikacji) wybierz grupę zasobów i aplikację internetową w swojej subskrypcji. Wtyczka Jenkins automatycznie wykrywa, czy aplikacja internetowa jest oparta na systemie Windows, czy Linux. W przypadku aplikacji internetowej systemu Windows zostanie wyświetlona opcja **Publish Files** (Opublikuj pliki).
7. Podaj pliki, które chcesz wdrożyć. Jeśli na przykład używasz języka Java, określ pakiet WAR. Przy użyciu opcjonalnych parametrów **Source Directory** (Katalog źródłowy) i **Target Directory** (Katalog docelowy) określ folder źródłowy i docelowy na potrzeby przekazywania plików. Aplikacja internetowa Java na platformie Azure jest uruchamiana na serwerze Tomcat. Dlatego dla języka Java należy przekazać pakiet WAR do folderu aplikacji internetowych. W tym przykładzie w polu **Source Directory** (Katalog źródłowy) ustaw wartość **target**, a w polu **Target Directory** (Katalog docelowy) ustaw wartość **webapps**.
8. Jeśli chcesz wdrożyć w miejscu innym niż produkcyjne, możesz również ustawić inną nazwę w polu **Slot** (Miejsce).
9. Zapisz projekt i skompiluj go. Twoja aplikacja internetowa zostanie wdrożona na platformie Azure po zakończeniu kompilacji.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Wdrażanie funkcji Web Apps przez przekazanie plików przy użyciu potoku Jenkins

Wtyczka Jenkins dla usługi Azure App Service obsługuje potok. Możesz zapoznać się z poniższym przykładem w repozytorium GitHub.

1. W interfejsie usługi GitHub otwórz plik **Jenkinsfile_ftp_plugin**. Aby edytować plik, wybierz ikonę ołówka. Zaktualizuj definicje **resourceGroup** i **webAppName** w wierszach 11 i 12 o wartości dla swojej aplikacji internetowej:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Dla definicji **withCredentials** w wierszu 14 ustaw wartość identyfikatora poświadczeń w swoim wystąpieniu serwera Jenkins:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Tworzenie potoku serwera Jenkins

1. Otwórz stronę serwera Jenkins w przeglądarce internetowej. Wybierz pozycję **New Item** (Nowy element).
2. Podaj nazwę zadania i wybierz pozycję **Pipeline** (Potok). Kliknij przycisk **OK**.
3. Wybierz kartę **Pipeline** (Potok).
4. W polu **Definition** (Definicja) wybierz wartość **Pipeline script from SCM** (Skrypt potoku z menedżera SCM).
5. W polu **SCM** wybierz wartość **Git**. Wprowadź adres URL usługi GitHub dla swojego rozwidlonego repozytorium. Na przykład: https://&lt;Twoje_rozwidlone_repozytorium>.git.
6. W polu **Script Path** (Ścieżka skryptu) zaktualizuj wartość na **Jenkinsfile_ftp_plugin**.
7. Wybierz pozycję **Save** (Zapisz) i uruchom zadanie.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Konfigurowanie serwera Jenkins w celu wdrożenia funkcji Web App for Containers

Funkcja Web Apps w systemie Linux obsługuje wdrażanie przy użyciu platformy Docker. Aby wdrożyć aplikację internetową za pomocą platformy Docker, należy podać plik Dockerfile, który pakuje aplikację internetową wraz ze środowiskiem uruchomieniowym usługi do obrazu platformy Docker. Następnie wtyczka Jenkins kompiluje ten obraz, wypycha go do rejestru platformy Docker i wdraża obraz do aplikacji internetowej.

Usługa Web App on Linux obsługuje również tradycyjne metody wdrażania, takie jak usługa Git i przekazywanie plików, ale tylko dla wbudowanych języków (.NET Core, Node.js, PHP i Ruby). W przypadku innych języków należy spakować swój kod aplikacji i środowisko uruchomieniowe usługi razem w obraz platformy Docker i użyć platformy Docker w celu wdrożenia.

Przed skonfigurowaniem zadania na serwerze Jenkins potrzebujesz aplikacji internetowej systemu Linux. Potrzebujesz również rejestru kontenerów w celu przechowywania prywatnych obrazów kontenerów platformy Docker i zarządzania nimi. Aby utworzyć rejestr kontenerów, możesz użyć usługi DockerHub. W tym przykładzie użyjemy usługi Azure Container Registry.

* [Utwórz aplikację internetową w systemie Linux](../app-service/containers/quickstart-nodejs.md).
* Usługa Azure Container Registry to zarządzana usługa [rejestru platformy Docker](https://docs.docker.com/registry/) oparta na oprogramowaniu typu open-source Docker Registry w wersji 2.0. [Utwórz rejestr kontenerów platformy Azure](/azure/container-registry/container-registry-get-started-azure-cli). Możesz również użyć usługi DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Konfigurowanie zadania serwera Jenkins dla platformy Docker

1. Utwórz nowy **dowolny** projekt na pulpicie nawigacyjnym serwera Jenkins.
2. Skonfiguruj pole **Source Code Management** (Zarządzanie kodem źródłowym) w celu użycia Twojego rozwidlenia lokalnego [prostej aplikacji internetowej Java dla platformy Azure](https://github.com/azure-devops/javawebappsample). Podaj wartość **Repository URL** (Adres URL repozytorium). Na przykład: http:\//github.com/&lt;your_ID >/javawebappsample.
3. Dodaj krok kompilowania projektu przy użyciu narzędzia Maven, dodając polecenie **Execute shell** (Wykonaj powłokę). W poleceniu dołącz następujący wiersz:
    ```bash
    mvn clean package
    ```

4. Dodaj akcję wykonywaną po kompilacji, wybierając pozycję **Publish an Azure Web App** (Opublikuj aplikację internetową platformy Azure).
5. Podaj identyfikator **mySp** jako jednostkę usługi platformy Azure. Ta jednostka została zapisana jako [poświadczenia platformy Azure](#service-principal) w poprzednim kroku.
6. W sekcji **App Configuration** (Konfiguracja aplikacji) wybierz grupę zasobów i aplikację internetową systemu Linux w swojej subskrypcji.
7. Wybierz pozycję **Publish via Docker** (Opublikuj za pośrednictwem platformy Docker).
8. Wypełnij ścieżkę pliku **Dockerfile**. Możesz zachować wartość domyślną /Dockerfile.
W polu **Docker registry URL** (Adres URL rejestru platformy Docker) podaj adres URL przy użyciu formatu https://&lt;Twój_rejestr>.azurecr.io, jeśli używasz usługi Azure Container Registry. Jeśli używasz witryny DockerHub, pozostaw to pole puste.
9. W polu **Registry credentials** (Poświadczenia rejestru) dodaj poświadczenia dla rejestru kontenerów. Aby uzyskać identyfikator użytkownika i hasło, uruchom następujące polecenia w interfejsie wiersza polecenia platformy Azure. Pierwsze polecenie włącza konto administratora:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Nazwa obrazu platformy Docker oraz wartość tagu na karcie **Advanced** (Zaawansowane) to wartości opcjonalne. Domyślnie wartość nazwy obrazu jest uzyskiwana z nazwy obrazu, która została skonfigurowana w witrynie Azure Portal w ustawieniu **Kontener platformy Docker**. Tag jest generowany na podstawie numeru kompilacji $BUILD_NUMBER.
    > [!NOTE]
    > Upewnij się, że nazwa obrazu jest określona w Azure Portal lub podaj wartość **obrazu platformy Docker** na karcie **Zaawansowane** . Na potrzeby tego przykładu ustaw wartość **obrazu platformy Docker** na &lt;your_Registry >. azurecr. IO/Kalkulator i pozostaw pustą wartość **znacznika obrazu platformy Docker** .

11. Wdrażanie nie powiedzie się, jeśli skorzystasz z ustawienia wbudowanego obrazu platformy Docker. W ustawieniu **Kontener platformy Docker** w witrynie Azure Portal zmień konfigurację platformy Docker, aby użyć obrazu niestandardowego. W celu wdrożenia z użyciem wbudowanego obrazu skorzystaj z metody przekazywania plików.
12. Podobnie jak w przypadku metody przekazywania plików, możesz wybrać inną nazwę **miejsca** niż **produkcyjne**.
13. Zapisz i skompiluj projekt. Obraz kontenera zostanie wypchnięty do rejestru i aplikacja internetowa zostanie wdrożona.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Wdrażanie funkcji Web App for Containers przy użyciu potoku Jenkins

1. W interfejsie usługi GitHub otwórz plik **Jenkinsfile_container_plugin**. Aby edytować plik, wybierz ikonę ołówka. Zaktualizuj definicje **resourceGroup** i **webAppName** w wierszach 11 i 12 o wartości dla swojej aplikacji internetowej:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Zmień wiersz 13, aby zawierał serwer rejestru kontenerów:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Zmień wiersz 16, aby użyć identyfikatora poświadczeń w Twoim wystąpieniu serwera Jenkins:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Tworzenie potoku serwera Jenkins    

1. Otwórz stronę serwera Jenkins w przeglądarce internetowej. Wybierz pozycję **New Item** (Nowy element).
2. Podaj nazwę zadania i wybierz pozycję **Pipeline** (Potok). Kliknij przycisk **OK**.
3. Wybierz kartę **Pipeline** (Potok).
4. W polu **Definition** (Definicja) wybierz wartość **Pipeline script from SCM** (Skrypt potoku z menedżera SCM).
5. W polu **SCM** wybierz wartość **Git**. Wprowadź adres URL usługi GitHub dla swojego rozwidlonego repozytorium. Na przykład: https://&lt;Twoje_rozwidlone_repozytorium>.git.
7. W polu **Script Path** (Ścieżka skryptu) zaktualizuj wartość na **Jenkinsfile_container_plugin**.
8. Wybierz pozycję **Save** (Zapisz) i uruchom zadanie.

## <a name="verify-your-web-app"></a>Weryfikowanie aplikacji internetowej

1. Aby sprawdzić, czy plik WAR został pomyślnie wdrożony w aplikacji internetowej, otwórz przeglądarkę internetową.
2. Przejdź do adresu http://&lt;nazwa_Twojej_aplikacji>.azurewebsites.net/api/calculator/ping. Zastąp fragment &lt;nazwa_Twojej_aplikacji> nazwą Twojej aplikacji internetowej. Zostanie wyświetlony komunikat:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Przejdź do adresu http://&lt;nazwa_Twojej_aplikacji>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Zastąp fragmenty &lt;x> i &lt;y> dowolnymi liczbami, aby uzyskać sumę x+y. Kalkulator pokaże sumę: ![Calculator: add](./media/execute-cli-jenkins-pipeline/calculator-add.png) (Kalkulatora: dodawanie)

### <a name="for-azure-app-service-on-linux"></a>Dla usługi Azure App Service w systemie Linux

1. Aby zweryfikować swoją aplikację internetową, uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure:
    
    ```azurecli
    az acr repository list -n <myRegistry> -o json
    ```
    
    Zostanie wyświetlony następujący komunikat:
    
    ```CLI
    ["calculator"]
    ```
    
2. Przejdź do adresu http://&lt;nazwa_Twojej_aplikacji>.azurewebsites.net/api/calculator/ping. Zastąp fragment &lt;nazwa_Twojej_aplikacji> nazwą Twojej aplikacji internetowej. Zostanie wyświetlony komunikat: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Przejdź do adresu http://&lt;nazwa_Twojej_aplikacji>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Zastąp fragmenty &lt;x> i &lt;y> dowolnymi liczbami, aby uzyskać sumę x+y.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeprowadziliśmy wdrożenie na platformie Azure za pomocą wtyczki Jenkins dla usługi Azure App Service.

W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie serwera Jenkins w celu wdrożenia usługi Azure App Service za pośrednictwem przekazywania plików 
> * Konfigurowanie serwera Jenkins w celu wdrożenia dla funkcji Web App for Containers 

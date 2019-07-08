---
title: Przy użyciu wtyczki miejsca do magazynowania deweloperów platformy Azure dla usługi Jenkins z usługą Kubenetes platformy Azure
description: Dowiedz się, jak używać wtyczki Azure Dev miejsca do magazynowania w potoku ciągłej integracji.
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/18/2019
ms.openlocfilehash: 8c47b8caf2d289ed17647b8003cc702156f3cddb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592074"
---
<!-- GMinchAQ, 06/18/19 -->

# <a name="tutorial-using-the-azure-dev-spaces-plugin-for-jenkins-with-azure-kubenetes-service"></a>Samouczek: Przy użyciu wtyczki miejsca do magazynowania deweloperów platformy Azure dla usługi Jenkins z usługą Kubenetes platformy Azure 

Usługa Azure Dev spacje umożliwia testowanie i programowanie iteracyjne aplikacji mikrousług, uruchomionej w usłudze Azure Kubernetes Service (AKS) bez konieczności replikacji lub testowanie zależności. Wtyczka usługi Azure Dev miejsca do magazynowania dla serwera Jenkins ułatwia korzystanie z Dev miejsca do magazynowania w ciągłej integracji i potoku dostarczania (CI/CD).

W tym samouczku również używa usługi Azure Container Registry (ACR). Rejestru Azure container Registry przechowywane obrazy i zadania usługi ACR kompilacji platformy Docker i Helm artefaktów. Przy użyciu usługi ACR i zadanie usługi ACR do generowania artefaktu eliminuje potrzebę instalowania dodatkowego oprogramowania, takich jak platforma Docker, na serwerze Jenkins. 

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie klastra AKS włączone usługi Azure Dev miejsca do magazynowania
> * Wdrażanie wielu usług aplikacji w usłudze AKS
> * Przygotowywanie serwera Jenkins
> * Podgląd zmian kodu przed scaleniem ich do projektu przy użyciu plugin Azure Dev miejsca do magazynowania w potoku usługi Jenkins

W tym samouczku założono pośrednich wiedzę na temat podstawowych usług platformy Azure, usługi AKS, rejestru Azure container Registry, Azure Dev miejsca do magazynowania, Jenkins [potoki](https://jenkins.io/doc/book/pipeline/) i dodatków plug-in i GitHub. Podstawowa wiedza o obsłudze narzędzi, takich jak narzędzia kubectl i Helm jest pomocne.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto usługi GitHub. Jeśli nie masz konta usługi GitHub, utworzyć [bezpłatne konto](https://github.com/) przed przystąpieniem do wykonywania.

* [Visual Studio Code](https://code.visualstudio.com/download) z [miejsca do magazynowania Azure Dev](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) zainstalowane rozszerzenie.

* [Wiersza polecenia platformy Azure zainstalowany](/cli/azure/install-azure-cli?view=azure-cli-latest), wersja 2.0.43 lub nowszej.

* Serwer główny Jenkins. Jeśli nie masz jeszcze wzorca usługi Jenkins, wdrożyć [Jenkins](https://aka.ms/jenkins-on-azure) na platformie Azure, wykonując czynności opisane w tym [Szybki Start](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Serwer Jenkins musi mieć zarówno narzędzia Helm, jak i narzędzia kubectl, zainstalowanych i dostępnych do konta usługi Jenkins, zgodnie z opisem w dalszej części tego samouczka.

* Program VS Code, program VS Code Terminal lub WSL i powłoki Bash. 


## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

W tej sekcji opisano tworzenie zasobów platformy Azure:

* Grupa zasobów zawiera wszystkie zasoby Azure na potrzeby tego samouczka.
* [Usługi Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) klastra (AKS).
* [Usługi Azure container registry](https://docs.microsoft.com/azure/container-registry/) (ACR) kompilacji (przy użyciu zadania rejestru Azure container Registry) do przechowywania obrazów platformy Docker.

1. Utwórz grupę zasobów.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Tworzenie klastra AKS. Tworzenie klastra AKS w [region, który obsługuje spacje Dev](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Konfigurowanie usługi AKS na spacje deweloperów.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Ta czynność powoduje zainstalowanie `azds` rozszerzenie interfejsu wiersza polecenia.

4. Tworzenie rejestru kontenerów.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Wdrażanie przykładowej aplikacji do klastra usługi AKS

W tej sekcji służy do ustawiania miejsca deweloperów i wdrożysz przykładową aplikację w klastrze AKS, utworzony w ostatniej sekcji. Aplikacja składa się z dwóch części *webfrontend* i *mywebapi*. Oba te składniki są wdrażane w miejscu do deweloperów. W dalszej części tego samouczka będziesz przesłać żądanie ściągnięcia względem mywebapi wyzwalanie potoku ciągłej integracji w usłudze Jenkins.

Aby uzyskać więcej informacji na temat korzystania z usługi Azure Dev miejsca do magazynowania i rozwoju wielu usług za pomocą usługi Azure Dev miejsca do magazynowania, zobacz [wprowadzenie do usługi Azure Dev miejsca do magazynowania przy użyciu języka Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java), i [rozwoju wielu usług za pomocą usługi Azure Dev miejsca do magazynowania](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Te samouczki zawierają dodatkowe informacje ogólne nie są uwzględnione w tym miejscu.

1. Pobierz https://github.com/Azure/dev-spaces repozytorium z usługi GitHub.

2. Otwórz `samples/java/getting-started/webfrontend` folderu w programie VS Code. (Możesz zignorować wszystkie domyślne monity o dodanie zasobów debugowania lub przywrócenie projektu).

3. Aktualizacja `/src/main/java/com/ms/sample/webfrontend/Application.java` do wyglądać podobnie do następującego:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Kliknij przycisk **widoku** następnie **terminalu** aby otworzyć zintegrowany Terminal programu VS Code.

5. Uruchom `azds prep` polecenie, aby przygotować aplikację do uruchamiania w miejscu do deweloperów. To polecenie należy uruchomić z `dev-spaces/samples/java/getting-started/webfrontend` poprawnie przygotowanie aplikacji:

    ```bash
    azds prep --public
    ```

    CLI miejsca do magazynowania Dev `azds prep` polecenie generuje zasoby platformy Docker i Kubernetes przy użyciu ustawień domyślnych. Te pliki są przechowywane trwania projektu, a następnie można dostosować:

    * `./Dockerfile` i `./Dockerfile.develop` opisu aplikacji obraz kontenera i jak kod źródłowy jest wbudowana i jest uruchamiany w kontenerze.
    * [Pakiet Helm](https://helm.sh/docs/developing_charts/) w folderze `./charts/webfrontend` opisuje, jak wdrożyć kontener na platformie Kubernetes.
    * `./azds.yaml` jest to plik konfiguracji usługi Azure Dev miejsca do magazynowania.

    Aby uzyskać więcej informacji, zobacz [jak Azure Dev miejsca do magazynowania działa i jest skonfigurowany](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Kompilowanie i uruchamianie aplikacji w usłudze AKS za pomocą `azds up` polecenia:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Dane wyjściowe konsoli, aby uzyskać informacje o adresie URL, który został utworzony przez skanowanie `up` polecenia. Będzie on mieć postać:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Otwórz ten adres URL w przeglądarce i powinna zostać wyświetlona aplikacja sieci web. Podczas wykonywania kontenera dane wyjściowe `stdout` i `stderr` są przesyłane strumieniowo do okna terminalu.

8. Następnie skonfiguruj i wdróż *mywebapi*:

    1. Zmień katalog na `dev-spaces/samples/java/getting-started/mywebapi`

    2. Uruchom polecenie

        ```bash
        azds prep
        ```

    3. Uruchom polecenie

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Przygotowanie serwera Jenkins

W tej sekcji służy do przygotowania serwera Jenkins, aby uruchomić przykładowy potok ciągłej integracji.

* Instalowanie wtyczki
* Zainstaluj narzędzia Helm i interfejsu wiersza polecenia Kubernetes
* Dodaj poświadczenia

### <a name="install-plugins"></a>Instalowanie wtyczki

1. Zaloguj się do serwera Jenkins. Wybierz **Zarządzanie Jenkins > Zarządzaj wtyczkami**.
2. Na **dostępne** , a następnie wybierz następujące wtyczki:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Usługa Azure Container Registry zadania](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Iniektor środowiska](https://plugins.jenkins.io/envinject)
    * [Integracja z usługą GitHub](https://plugins.jenkins.io/github-pullrequest)

    Jeśli te dodatki nie są wyświetlane na liście, sprawdź **zainstalowane** kartę, aby zobaczyć, jeśli są one już zainstalowane.

3. Aby zainstalować wtyczki, wybierz **Pobierz i zainstaluj po ponownym uruchomieniu**.

4. Uruchom ponownie serwer Jenkins do ukończenia instalacji.

### <a name="install-helm-and-kubectl"></a>Zainstaluj narzędzia Helm i narzędzia kubectl

Przykładowy potok do wdrażania do obszaru dev korzysta Helm i narzędzia kubectl. Po zainstalowaniu narzędzia Jenkins tworzy konto administratora o nazwie *jenkins*. Narzędzia Helm i kubectl muszą być dostępne dla użytkownika usługi jenkins.

1. Utwórz połączenie SSH do wzorca usługi Jenkins. 

2. Przełącz się do `jenkins` użytkownika:
    ```bash
    sudo su jenkins
    ```

3. Zainstaluj narzędzie Helm interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [instalowanie narzędzia Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Instalowanie narzędzia kubectl. Aby uzyskać więcej informacji, zobacz [ **az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Dodawanie poświadczeń do serwera Jenkins

1. Jenkins wymaga jednostki usługi Azure do uwierzytelniania i uzyskiwania dostępu do zasobów platformy Azure. Aby utworzyć jednostkę usługi, zapoznaj się [Tworzenie jednostki usługi](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) sekcji Wdróż do samouczka dotyczącego usługi Azure App Service. Pamiętaj zapisać kopię danych wyjściowych `create-for-rbac` ponieważ potrzebujesz tych informacji do wykonania następnego kroku. Dane wyjściowe będą wyglądać mniej więcej tak:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Dodaj *Microsoft Azure Service Principal* poświadczeń typu w usłudze Jenkins przy użyciu informacji o jednostce usługi z poprzedniego kroku. Nazwy na poniższym zrzucie ekranu odpowiadają dane wyjściowe z `create-for-rbac`.

    **Identyfikator** pola jest nazwą serwera Jenkins poświadczeń dla jednostki usługi. W przykładzie użyto wartości `displayName` (w tym wypadku `xxxxxxxjenkinssp`), ale można użyć dowolnego tekst, który ma. Ta nazwa poświadczenia jest wartość dla zmiennej środowiskowej AZURE_CRED_ID w następnej sekcji.

    ![Dodawanie poświadczeń jednostki usługi do systemu Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    **Opis** jest opcjonalne. Aby uzyskać szczegółowe instrukcje, zobacz [Dodaj jednostki usługi do systemu Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) sekcji Wdróż do samouczka dotyczącego usługi Azure App Service. 



3. Aby wyświetlić poświadczenia rejestru Azure container Registry, uruchom następujące polecenie:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Utwórz kopię danych wyjściowych JSON, która powinna wyglądać mniej więcej tak:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Dodaj *nazwy użytkownika z hasłem* typ w usłudze Jenkins poświadczeń. **Username** jest nazwą użytkownika w ostatnim kroku, w tym przykładzie `acr01`. **Hasło** jest wartością dla pierwszej hasła, w tym przykładzie `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. **Identyfikator** tych poświadczeń jest wartością ACR_CRED_ID.

5. Konfigurowanie poświadczeń usługi AKS. Dodaj *konfigurację Kubernetes (plik kubeconfig)* poświadczeń typu w usłudze Jenkins (Użyj opcji "Enter bezpośrednio"). Aby uzyskać poświadczenia dostępu do klastra usługi AKS, uruchom następujące polecenie:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   **Identyfikator** to poświadczenie jest wartością KUBE_CONFIG_ID w następnej sekcji.

## <a name="create-a-pipeline"></a>Tworzenie potoku

Scenariusz, wybrany dla potoku przykład opiera się na wzorzec rzeczywistych. Żądania ściągnięcia, że wyzwalacze CI potoku, który tworzy, a następnie wdraża proponowanych zmian do obszaru deweloperów platformy Azure do testowania i przeglądu. W zależności od wyniku przeglądu zmiany są scalane i wdrożona w usłudze AKS albo odrzucone. Na koniec miejsca dev zostaną usunięte.

Konfiguracja potoku Jenkins i pliku Jenkins definiować etapy w potoku ciągłej integracji. Tym schemacie blokowym pokazano etapy potoku i decyzje, zdefiniowane przez pliku Jenkins:

![Przepływ procesu usługi Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Pobierz zmodyfikowanej wersji *mywebapi* projekt z https://github.com/azure-devops/mywebapi. Ten projekt zawiera kilka plików, potrzebne do utworzenia potoku, w tym *pliku Jenkins*, *plików Dockerfile*i narzędzia Helm.

2. Zaloguj się do serwera Jenkins. W menu po lewej stronie wybierz **elementu Dodawanie**.

3. Wybierz **potoku**, a następnie wprowadź nazwę w **wprowadź nazwę elementu** pole. Wybierz **OK**, a następnie automatycznie zostanie otwarty ekran konfiguracji potoku.

4. Na **ogólne** karcie wyboru **przygotowywanie środowiska na potrzeby uruchomienia**. 

5. Sprawdź **zachować zmienne środowiskowe systemu Jenkins** i **zachować zmienne kompilację narzędzia Jenkins**.

6. W **właściwości zawartości** wprowadź następujące zmienne środowiskowe:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Korzystając z przykładowych wartości podanych w poprzednich sekcjach, listę zmiennych środowiskowych powinien wyglądać mniej więcej tak:

    ![Zmienne środowiskowe w potoku usługi Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Wybierz **potoku skrypt z SCM** w **potoku > definicji**.
8. W **SCM**, wybierz **Git** , a następnie wprowadź adres URL repozytorium.
9. W **specyfikator gałęzi**, wprowadź `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Wypełnij SCM repozytorium adresu URL i skrypt ścieżka "pliku Jenkins".
11. **Uproszczone wyewidencjonowania** powinny być sprawdzane.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Utwórz żądanie ściągnięcia w celu wyzwolenia potoku

Wykonaj krok 3 w tej sekcji musisz dodać komentarz część pliku Jenkins, w przeciwnym razie podczas próby wyświetlenia nowych i starych wersji obok siebie zostanie wyświetlony błąd 404. Domyślnie w przypadku scalania żądania Ściągnięcia, udostępniony poprzednią wersję mywebapi będzie można usunięta i zastąpiona przez nową wersję. Wprowadź następującą zmianę w pliku Jenkins, przed wykonaniem kroku 1:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Wprowadź zmiany do `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`, a następnie utwórz żądanie ściągnięcia. Na przykład:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Zaloguj się do serwera Jenkins i wybierz nazwę potoku, a następnie wybierz **Kompiluj teraz**. 

    Możesz również skonfigurować *elementu webhook* automatyczne wyzwalanie potoku usługi Jenkins. Po wprowadzeniu żądania ściągnięcia GitHub wystawia WPIS Jenkins, wyzwalanie potoku. Aby uzyskać więcej informacji na temat konfigurowania elementów webhook, zobacz [Connect z narzędzia Jenkins do usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Porównaj zmiany do bieżącej wersji udostępnionych:

    1. Otwórz przeglądarkę i przejdź do udostępnionej wersja `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT zawiera adres URL.

    2. Otwórz innej karty, a następnie wprowadź adres URL żądania Ściągnięcia dev miejsca. Będą wyglądać mniej więcej `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Znajdziesz link w **historii kompilacji >< kompilacji # >> dane wyjściowe konsoli** dla zadania narzędzia Jenkins. Strona dla wyszukiwania `aksapp`, aby wyświetlić tylko prefiksy, wyszukaj `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Konstruowanie adres URL do miejsca dev podrzędne

Podczas zachowywania żądania ściągnięcia narzędzia Jenkins Tworzy spację dev podrzędnych zespołu deweloperów udostępnione miejsca w oparciu i uruchamia kod z żądania ściągnięcia w ten podrzędny dev miejsca. Adres URL do miejsca dev podrzędnych ma postać `http://$env.azdsprefix.<test_endpoint>`. 

**$env.azdsprefix** została ustawiona podczas wykonywania potoku przez wtyczki Azure Dev miejsca do magazynowania według **devSpacesCreate**:

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

`test_endpoint` Jest adres URL do aplikacji webfrontend wcześniej wdrożona za pomocą `azds up`w [wdrażanie przykładowych aplikacji w klastrze AKS, kroku 7](#test_endpoint). Wartość `$env.TEST_ENDPOINT` jest ustawiona w konfiguracji procesu. 

Poniższy fragment kodu przedstawia, jak podrzędnych dev miejsca na adres URL jest używany w `smoketest` etapu. Kod sprawdza, czy miejsce dev podrzędnych TEST_ENDPOINT jest dostępny, a jeśli tak, pobiera tekst powitania do strumienia wyjściowego stdout:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu za pomocą aplikacji przykładowej, czyszczenie zasobów platformy Azure, usuwając grupę zasobów:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób używania wtyczki Azure Dev miejsca do magazynowania dla serwera Jenkins i wtyczki usługi Azure Container Registry do tworzenia kodu i wdrażania do obszaru deweloperów.

Poniższa lista zasobów zawiera więcej informacji na Azure Dev miejsca do magazynowania, zadania usługi ACR i ciągłej integracji/ciągłego wdrażania przy użyciu narzędzia Jenkins.

Usługa Azure Dev miejsca do magazynowania:
* [Jak działają obszary Azure Dev Spaces i jak są skonfigurowane](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

Zadania usługi ACR:
* [Automatyzacja systemu operacyjnego i poprawianie struktury przy użyciu usługi ACR Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Automatyczna kompilacja przy zatwierdzeniu kodu](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

Ciągła Integracja/ciągłe dostarczanie przy użyciu usługi Jenkins na platformie Azure:
* [Ciągłe wdrażanie usługi Jenkins](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)

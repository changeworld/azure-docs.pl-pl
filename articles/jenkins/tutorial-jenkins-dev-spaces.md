---
title: Korzystanie z wtyczki Azure Dev Spaces dla Jenkins za pomocą usługi Azure Kubernetes Service
description: Dowiedz się, jak używać wtyczki Azure Dev Spaces w potoku ciągłej integracji.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 42d732cda26f0c34f0a54fffc0b1b9c54def94ad
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158731"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Samouczek: korzystanie z wtyczki Azure Dev Spaces dla Jenkins za pomocą usługi Azure Kubernetes Service 

Azure Dev Spaces umożliwia testowanie i iteracyjne opracowywanie aplikacji mikrousług działającej w usłudze Azure Kubernetes Service (AKS) bez konieczności replikowania lub makiety zależności. Wtyczka Azure Dev Spaces dla Jenkins ułatwia korzystanie z funkcji miejsca deweloperskiego w potoku ciągłej integracji i dostarczania (CI/CD).

Ten samouczek używa również Azure Container Registry (ACR). ACR przechowuje obrazy, a zadanie ACR kompiluje artefakty Docker i Helm. Korzystanie z ACR i ACR zadania do generowania artefaktów eliminuje konieczność zainstalowania dodatkowego oprogramowania, takiego jak Docker, na serwerze Jenkins. 

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie klastra AKS Azure Dev Spaces z włączonymi
> * Wdrażanie aplikacji wielousługowej w AKS
> * Przygotowywanie serwera Jenkins
> * Użyj wtyczki Azure Dev Spaces w potoku Jenkins, aby wyświetlić podgląd zmian w kodzie przed scaleniem ich do projektu

W tym samouczku przyjęto założenie pośredniej wiedzy na temat podstawowych usług platformy Azure, AKS, ACR, Azure Dev Spaces i [potoków](https://jenkins.io/doc/book/pipeline/) Jenkins oraz dodatków plug-in i GitHub. Przydatna jest znajomość podstawowej znajomości narzędzi pomocniczych, takich jak polecenia kubectl i Helm.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto usługi GitHub. Jeśli nie masz konta w usłudze GitHub, przed rozpoczęciem Utwórz [bezpłatne konto](https://github.com/) .

* [Visual Studio Code](https://code.visualstudio.com/download) z zainstalowaną rozszerzeniem [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) .

* [Zainstalowano interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)w wersji 2.0.43 lub nowszej.

* Serwer główny Jenkins. Jeśli nie masz jeszcze wzorca Jenkins, wdróż [Jenkins](https://aka.ms/jenkins-on-azure) na platformie Azure, wykonując kroki opisane w tym [przewodniku szybki start](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Na serwerze Jenkins musi być zainstalowany program Helm i polecenia kubectl, który jest dostępny dla konta Jenkins, jak wyjaśniono w dalszej części tego samouczka.

* VS Code, Terminal VS Code lub WSL oraz bash. 


## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

W tej sekcji utworzysz zasoby platformy Azure:

* Grupa zasobów zawierająca wszystkie zasoby platformy Azure dla tego samouczka.
* Klaster [usługi Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) (AKS).
* [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) (ACR) do kompilowania (przy użyciu zadań ACR) i przechowywania obrazów platformy Docker.

1. Utwórz grupę zasobów.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Utwórz klaster AKS. Utwórz klaster AKS w [regionie, który obsługuje funkcję Spaces dev](../dev-spaces/about.md#supported-regions-and-configurations).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Skonfiguruj AKS do używania funkcji Spaces dev.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Ten krok powoduje zainstalowanie rozszerzenia interfejsu wiersza polecenia `azds`.

4. Utwórz rejestr kontenerów.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Wdrażanie przykładowych aplikacji w klastrze AKS

W tej sekcji skonfigurujesz miejsce do tworzenia i wdrożono przykładową aplikację w klastrze AKS utworzonym w ostatniej sekcji. Aplikacja składa się z dwóch części, *webfrontonu* i *mywebapi*. Oba składniki są wdrażane w obszarze dev. W dalszej części tego samouczka zostanie przesłane żądanie ściągnięcia do mywebapi, aby wyzwolić potok CI w Jenkins.

Aby uzyskać więcej informacji na temat korzystania z Azure Dev Spaces i tworzenia wielu usług za pomocą Azure Dev Spaces, zobacz artykuł Rozpoczynanie [pracy w systemie Azure dev Spaces przy użyciu języka Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java)i [Tworzenie wielu usług za pomocą Azure dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Te samouczki zawierają dodatkowe informacje w tle, które nie zostały uwzględnione w tym miejscu.

1. Pobierz repozytorium https://github.com/Azure/dev-spaces z usługi GitHub.

2. Otwórz folder `samples/java/getting-started/webfrontend` w VS Code. (Możesz zignorować wszystkie domyślne monity o dodanie zasobów debugowania lub przywrócenie projektu).

3. Zaktualizuj `/src/main/java/com/ms/sample/webfrontend/Application.java`, aby wyglądać następująco:

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

4. Kliknij przycisk **Wyświetl** , a następnie **Terminal** , aby otworzyć zintegrowany terminal w vs Code.

5. Uruchom `azds prep` polecenie, aby przygotować aplikację do uruchamiania w obszarze dev. To polecenie musi zostać uruchomione z `dev-spaces/samples/java/getting-started/webfrontend` w celu poprawnego przygotowania aplikacji:

    ```bash
    azds prep --public
    ```

    Polecenie `azds prep` interfejsu wiersza polecenia dev Spaces generuje zasoby Docker i Kubernetes z ustawieniami domyślnymi. Te pliki są utrwalane przez okres istnienia projektu i można je dostosować:

    * `./Dockerfile` i `./Dockerfile.develop` opisują obraz kontenera aplikacji oraz sposób kompilowania i uruchamiania kodu źródłowego w ramach kontenera.
    * [Pakiet Helm](https://helm.sh/docs/developing_charts/) w folderze `./charts/webfrontend` opisuje, jak wdrożyć kontener na platformie Kubernetes.
    * `./azds.yaml` to plik konfiguracji Azure Dev Spaces.

    Aby uzyskać więcej informacji, zobacz [jak działa Azure dev Spaces i jest skonfigurowany](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Kompiluj i uruchamiaj aplikację w AKS przy użyciu polecenia `azds up`:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Zeskanuj dane wyjściowe konsoli, aby uzyskać informacje o adresie URL, który został utworzony przez polecenie `up`. Będzie on mieć postać:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Otwórz ten adres URL w oknie przeglądarki i zobaczysz aplikację sieci Web. Podczas wykonywania kontenera dane wyjściowe `stdout` i `stderr` są przesyłane strumieniowo do okna terminalu.

8. Następnie skonfiguruj i Wdróż *mywebapi*:

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

W tej sekcji przygotowano serwer Jenkins do uruchamiania przykładowego potoku CI.

* Zainstaluj wtyczki
* Instalowanie interfejsu wiersza polecenia Helm i Kubernetes
* Dodawanie poświadczeń

### <a name="install-plug-ins"></a>Zainstaluj wtyczki

1. Zaloguj się do serwera Jenkins. Wybierz pozycję **Zarządzaj Jenkins > zarządzanie wtyczkami**.
2. Na karcie **dostępne** wybierz następujące wtyczki:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry zadania](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Wtryskiwacz środowiska](https://plugins.jenkins.io/envinject)
    * [Integracja z usługą GitHub](https://plugins.jenkins.io/github-pullrequest)

    Jeśli te wtyczki nie są wyświetlane na liście, sprawdź **zainstalowaną** kartę, aby sprawdzić, czy są już zainstalowane.

3. Aby zainstalować wtyczki, wybierz pozycję **Pobierz teraz i zainstaluj po ponownym uruchomieniu**.

4. Uruchom ponownie serwer Jenkins, aby zakończyć instalację.

### <a name="install-helm-and-kubectl"></a>Zainstaluj Helm i polecenia kubectl

Przykładowy potok używa Helm i polecenia kubectl do wdrożenia w obszarze dev. Po zainstalowaniu Jenkins tworzy konto administratora o nazwie *Jenkins*. Zarówno Helm, jak i polecenia kubectl muszą być dostępne dla użytkownika Jenkins.

1. Utwórz połączenie SSH z serwerem głównym Jenkins. 

2. Przełącz do `jenkins` użytkownika:
    ```bash
    sudo su jenkins
    ```

3. Zainstaluj interfejs wiersza polecenia Helm. Aby uzyskać więcej informacji, zobacz [Installing Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Zainstaluj polecenia kubectl. Aby uzyskać więcej informacji, zobacz [**AZ ACS Kubernetes Install-CLI**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Dodawanie poświadczeń do Jenkins

1. Jenkins potrzebuje jednostki usługi platformy Azure na potrzeby uwierzytelniania i uzyskiwania dostępu do zasobów platformy Azure. Aby utworzyć nazwę główną usługi, zapoznaj się z sekcją [tworzenie głównej usługi](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) w samouczku wdrażanie do Azure App Service. Pamiętaj, aby zapisać kopię danych wyjściowych z `create-for-rbac`, ponieważ potrzebujesz tych informacji, aby wykonać następny krok. Dane wyjściowe będą wyglądać następująco:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Dodaj typ poświadczeń jednostki *usługi Microsoft Azure* w Jenkins, używając informacji o jednostce usługi z poprzedniego kroku. Nazwy na poniższym zrzucie ekranu odpowiadają dane wyjściowe z `create-for-rbac`.

    Pole **ID** jest nazwą poświadczenia Jenkins dla nazwy głównej usługi. W przykładzie użyto wartości `displayName` (w tym przypadku `xxxxxxxjenkinssp`), ale można użyć dowolnego tekstu. Ta nazwa poświadczenia jest wartością zmiennej środowiskowej AZURE_CRED_ID w następnej sekcji.

    ![Dodaj poświadczenia nazwy głównej usługi do Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    **Opis** jest opcjonalny. Aby uzyskać bardziej szczegółowe instrukcje, zobacz sekcję [Dodawanie nazwy głównej usługi do Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) w samouczku wdrażanie do Azure App Service. 



3. Aby wyświetlić poświadczenia ACR, uruchom następujące polecenie:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Utwórz kopię danych wyjściowych JSON, która powinna wyglądać następująco:

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

4. Dodaj *nazwę użytkownika z* poświadczeniami typu w Jenkins. **Nazwa użytkownika** jest nazwą użytkownika z ostatniego kroku, w tym przykładzie `acr01`. **Hasło** jest wartością pierwszego hasła, w tym przykładzie `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. **Identyfikator** tego poświadczenia jest wartością ACR_CRED_ID.

5. Skonfiguruj poświadczenia AKS. Dodaj typ poświadczeń *Kubernetes Configuration (kubeconfig)* w Jenkins (Użyj opcji "wpisz bezpośrednio"). Aby uzyskać poświadczenia dostępu do klastra AKS, uruchom następujące polecenie:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   **Identyfikator** to poświadczenie jest wartością KUBE_CONFIG_ID w następnej sekcji.

## <a name="create-a-pipeline"></a>Tworzenie potoku

Scenariusz wybrany dla przykładowego potoku jest oparty na wzorcu rzeczywistym: żądanie ściągnięcia wyzwala potok elementu konfiguracji, który kompiluje, a następnie wdraża proponowane zmiany w obszarze dev platformy Azure na potrzeby testowania i przeglądu. W zależności od wyniku przeglądu zmiany są scalane i wdrażane w AKS lub odrzucane. Na koniec miejsce dev jest usuwana.

Konfiguracja potoku Jenkins i fragmenty definiują etapy w potoku CI. Ten schemat blokowy przedstawia etapy potoku i punkty decyzyjne zdefiniowane przez fragmenty:

![Przepływ potoku Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Pobierz zmodyfikowaną wersję projektu *mywebapi* z https://github.com/azure-devops/mywebapi. Ten projekt zawiera kilka plików wymaganych do utworzenia potoku, w tym wykres *fragmenty*, *wieloetapowe dockerfile*i Helm.

2. Zaloguj się do Jenkins. Z menu po lewej stronie wybierz pozycję **Dodaj element**.

3. Wybierz pozycję **potok**, a następnie wprowadź nazwę w polu **Wprowadź nazwę elementu** . Wybierz przycisk **OK**, a następnie ekranu konfiguracja potoku zostanie automatycznie otwarta.

4. Na karcie **Ogólne** zaznacz opcję **Przygotuj środowisko do uruchomienia**. 

5. Zaznacz opcję **Zachowaj zmienne środowiskowe Jenkins** i **Zachowaj zmienne kompilacji Jenkins**.

6. W polu **zawartość właściwości** wprowadź następujące zmienne środowiskowe:

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

    Korzystając z przykładowych wartości podanej w poprzednich sekcjach, lista zmiennych środowiskowych powinna wyglądać następująco:

    ![Zmienne środowiskowe potoku Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Wybierz pozycję **skrypt potoku z usługi SCM** w **definicji > potoku**.
8. W obszarze **SCM**wybierz pozycję **git** , a następnie wprowadź adres URL repozytorium.
9. W polu **specyfikator gałęzi**wprowadź `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Wprowadź adres URL repozytorium SCM i ścieżkę skryptu "fragmenty".
11. Należy sprawdzić **uproszczone wyewidencjonowanie** .

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Utwórz żądanie ściągnięcia, aby wyzwolić potok

Aby wykonać krok 3 w tej sekcji, należy dodać komentarz do części fragmenty. w przeciwnym razie podczas próby wyświetlenia nowych i starych wersji obok siebie zostanie wyświetlony błąd 404. Domyślnie po wybraniu opcji scalenia żądania ściągnięcia Poprzednia wersja udostępniona mywebapi zostanie usunięta i zastąpiona przez nową wersję. Wprowadź następujące zmiany do fragmenty przed ukończeniem kroku 1:

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

1. Wprowadź zmianę w `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`a następnie Utwórz żądanie ściągnięcia. Na przykład:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Zaloguj się do Jenkins i wybierz nazwę potoku, a następnie wybierz opcję **Kompiluj teraz**. 

    Możesz również skonfigurować *element webhook* , aby automatycznie wyzwalał potok Jenkins. Po wprowadzeniu żądania ściągnięcia GitHub wysyła wpis do Jenkins, wyzwalając potoku. Aby uzyskać więcej informacji o konfigurowaniu elementu webhook, zobacz [Connect Jenkins to GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Porównaj zmiany w bieżącej udostępnionej wersji:

    1. Otwórz przeglądarkę i przejdź do udostępnionej wersji `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT zawiera adres URL.

    2. Otwórz kolejną kartę, a następnie wprowadź adres URL przestrzeni dev dla zakupu. Będzie wyglądać podobnie do `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Link zostanie znaleziony w **historii kompilacji > < kompilacja # > > dane wyjściowe konsoli** dla zadania Jenkins. Przeszukaj stronę `aksapp`lub aby wyświetlić tylko prefiks, wyszukaj ciąg `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Konstruowanie adresu URL do podrzędnego miejsca deweloperskiego

Gdy tworzysz plik żądania ściągnięcia, Jenkins tworzy potomne miejsce dev na podstawie udostępnionego obszaru dev zespołu i uruchamia kod z żądania ściągnięcia w tym podrzędnym obszarze dev. Adres URL do podrzędnego miejsca deweloperskiego przyjmuje postać `http://$env.azdsprefix.<test_endpoint>`. 

**$ENV. azdsprefix** jest ustawiany podczas wykonywania potoku przez wtyczkę Azure dev Spaces przez **devSpacesCreate**:

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

`test_endpoint` to adres URL aplikacji webfrontonu, która została wcześniej wdrożona przy użyciu `azds up`[wdrażania przykładowych aplikacji do klastra AKS, krok 7](#test_endpoint). Wartość `$env.TEST_ENDPOINT` jest ustawiona w konfiguracji potoku. 

Poniższy fragment kodu przedstawia sposób używania adresu URL podrzędnego miejsca deweloperskiego na etapie `smoketest`. Kod sprawdza, czy TEST_ENDPOINT podrzędne miejsce dev jest dostępne, a jeśli tak, pobiera tekst pozdrowienia do stdout:

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

Po zakończeniu korzystania z przykładowej aplikacji Wyczyść zasoby platformy Azure, usuwając grupę zasobów:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania z wtyczki Azure Dev Spaces dla Jenkins i wtyczki Azure Container Registry do kompilowania kodu i wdrażania go w obszarze dev.

Poniższa lista zasobów zawiera więcej informacji na temat Azure Dev Spaces, ACR zadań i ciągłej integracji/ciągłego wdrażania z Jenkins.

Azure Dev Spaces:
* [Jak działają obszary Azure Dev Spaces i jak są skonfigurowane](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

ACR zadania:
* [Automatyzacja systemu operacyjnego i poprawianie struktury przy użyciu usługi ACR Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Automatyczne Kompilowanie przy zatwierdzaniu kodu](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

Ciągłej integracji/ciągłego wdrażania za pomocą Jenkins na platformie Azure:
* [Jenkins ciągłe wdrażanie](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)

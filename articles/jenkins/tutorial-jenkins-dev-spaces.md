---
title: Korzystanie z wtyczki Azure Dev Spaces dla usługi Jenkins za pomocą usługi Azure Kubernetes
description: Dowiedz się, jak korzystać z wtyczki Azure Dev Spaces w potoku ciągłej integracji.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 281565cec5ee947781ab8ee9f62a00e01f9ababb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037036"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Samouczek: Korzystanie z wtyczki Azure Dev Spaces dla usługi Jenkins za pomocą usługi Azure Kubernetes 

Usługa Azure Dev Spaces umożliwia testowanie i iteratively tworzenie aplikacji mikrousługi uruchomionych w usłudze Azure Kubernetes Service (AKS) bez konieczności replikowania lub makiety zależności. Wtyczka Azure Dev Spaces dla usługi Jenkins ułatwia korzystanie z funkcji Dev Spaces w potoku ciągłej integracji i dostarczania (CI/CD).

W tym samouczku użyto również usługi Azure Container Registry (ACR). Funkcja ACR przechowuje obrazy, a zadanie ACR tworzy artefakty platformy Docker i Helm. Użycie funkcji ACR i ACR Task do generowania artefaktów eliminuje konieczność instalowania dodatkowego oprogramowania, takiego jak docker, na serwerze usługi Jenkins. 

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie klastra AKS z obsługą usługi Azure Dev Spaces
> * Wdrażanie aplikacji z wieloma usługami w usłudze AKS
> * Przygotowanie serwera jenkinsa
> * Użyj wtyczki Azure Dev Spaces w potoku usługi Jenkins, aby wyświetlić podgląd zmian kodu przed scaleniem ich z projektem

W tym samouczku przyjęto założenie, że wiedza pośrednia na temat podstawowych usług platformy Azure, usług AKS, ACR, azure dev spaces, [potoków](https://jenkins.io/doc/book/pipeline/) i wtyczek usługi Jenkins oraz usługi GitHub. Podstawowa znajomość narzędzi pomocniczych, takich jak kubectl i Helm, jest pomocna.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

* Konto usługi GitHub. Jeśli nie masz konta GitHub, utwórz [bezpłatne konto](https://github.com/) przed rozpoczęciem.

* [Visual Studio Code](https://code.visualstudio.com/download) z zainstalowanym rozszerzeniem [azure dev spaces.](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)

* [Zainstalowano narzędzie cli platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)w wersji 2.0.43 lub nowszej.

* Serwer główny Jenkins. Jeśli nie masz jeszcze wzorca usługi Jenkins, wdrożyć [usługę Jenkins](https://aka.ms/jenkins-on-azure) na platformie Azure, wykonując kroki opisane w tym [przewodniku Szybki start.](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template) 

* Serwer usługi Jenkins musi mieć zainstalowany zarówno Helm, jak i kubectl i dostęp do konta usługi Jenkins, jak wyjaśniono w dalszej części tego samouczka.

* VS, terminalu kodu VS lub WSL i Bash. 


## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

W tej sekcji utworzysz zasoby platformy Azure:

* Grupa zasobów zawierająca wszystkie zasoby platformy Azure dla tego samouczka.
* Klaster [usługi Kubernetes platformy Azure](https://docs.microsoft.com/azure/aks/) (AKS).
* [Rejestr kontenerów platformy Azure](https://docs.microsoft.com/azure/container-registry/) (ACR) do tworzenia (przy użyciu zadań ACR) i przechowywania obrazów platformy Docker.

1. Utwórz grupę zasobów.

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. Tworzenie klastra AKS. Utwórz klaster AKS w [regionie obsługującym obszary deweloperskie](../dev-spaces/about.md#supported-regions-and-configurations).

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Skonfiguruj plik AKS do używania przestrzeni deweloperskich.

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Ten krok instaluje rozszerzenie interfejsu wiersza `azds` polecenia.

4. Tworzenie rejestru kontenerów.

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Wdrażanie przykładowych aplikacji w klastrze AKS

W tej sekcji należy skonfigurować przestrzeń deweloperów i wdrożyć przykładową aplikację do klastra AKS utworzonego w ostatniej sekcji. Aplikacja składa się z dwóch części, *webfrontend* i *mywebapi*. Oba składniki są wdrażane w przestrzeni deweloperów. W dalszej części tego samouczka prześlisz żądanie ściągnięcia przeciwko mywebapi, aby wyzwolić potok ciągłej integracji w u portalu Jenkins.

Aby uzyskać więcej informacji na temat korzystania z usługi Azure Dev Spaces i tworzenia wielu usług za pomocą usługi Azure Dev Spaces, zobacz [Wprowadzenie do usługi Azure Dev Spaces w języku Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java)i tworzenie wielu usług za pomocą usługi Azure Dev [Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Te samouczki zawierają dodatkowe informacje podstawowe nie uwzględnione w tym miejscu.

1. Pobierz https://github.com/Azure/dev-spaces repozytorium z GitHub.

2. Otwórz `samples/java/getting-started/webfrontend` folder w programie VS Code. (Możesz zignorować wszystkie domyślne monity o dodanie zasobów debugowania lub przywrócenie projektu).

3. Aktualizuj, `/src/main/java/com/ms/sample/webfrontend/Application.java` aby wyglądać następująco:

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

4. Kliknij **przycisk Wyświetl,** a następnie **terminal,** aby otworzyć zintegrowany terminal w programie VS Code.

5. Uruchom `azds prep` polecenie, aby przygotować aplikację do uruchomienia w przestrzeni deweloperskiej. To polecenie musi `dev-spaces/samples/java/getting-started/webfrontend` być uruchomione z aby poprawnie przygotować aplikację:

    ```bash
    azds prep --public
    ```

    Polecenie Interfejsu wiersza `azds prep` polecenia Dev Spaces generuje zasoby platformy Docker i Kubernetes z ustawieniami domyślnymi. Te pliki utrzymują się przez cały okres istnienia projektu i można je dostosować:

    * `./Dockerfile`i `./Dockerfile.develop` opisz obraz kontenera aplikacji i jak kod źródłowy jest zbudowany i działa w kontenerze.
    * [Pakiet Helm](https://helm.sh/docs/topics/charts/) w folderze `./charts/webfrontend` opisuje, jak wdrożyć kontener na platformie Kubernetes.
    * `./azds.yaml`to plik konfiguracyjny usługi Azure Dev Spaces.

    Aby uzyskać więcej informacji, zobacz [Jak działa usługa Azure Dev Spaces i jest skonfigurowana](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Skompiluj i uruchom aplikację `azds up` w uzywniu AKS za pomocą polecenia:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Zeskanuj dane wyjściowe konsoli w `up` poszukiwaniu informacji o adresie URL utworzonym przez polecenie. Będzie on mieć postać:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Otwórz ten adres URL w oknie przeglądarki, a aplikacja internetowa powinna zostać wyświetlena. Podczas wykonywania kontenera dane wyjściowe `stdout` i `stderr` są przesyłane strumieniowo do okna terminalu.

8. Następnie skonfiguruj i rozmieścij *mywebapi*:

    1. Zmienianie katalogu na`dev-spaces/samples/java/getting-started/mywebapi`

    2. Run

        ```bash
        azds prep
        ```

    3. Run

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Przygotowanie serwera Jenkins

W tej sekcji należy przygotować serwer jenkins do uruchomienia przykładowego potoku ciągłej integracji.

* Instalowanie wtyczek
* Instalowanie interfejsu wiersza polecenia Helm i Kubernetes
* Dodawanie poświadczeń

### <a name="install-plug-ins"></a>Instalowanie wtyczek

1. Zaloguj się na serwerze jenkinsa. Wybierz **pozycję Zarządzaj > Zarządzaj wtyczkami w ujamią**w u portalu Jenkins .
2. Na karcie **Dostępne** wybierz następujące wtyczki:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Usługa Azure Container Registry Tasks](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Wtryskiwacz środowiska](https://plugins.jenkins.io/envinject)
    * [Integracja z gitHubem](https://plugins.jenkins.io/github-pullrequest)

    Jeśli te wtyczki nie są wyświetlane na liście, sprawdź kartę **Zainstalowane,** aby sprawdzić, czy są już zainstalowane.

3. Aby zainstalować wtyczki, wybierz pozycję **Pobierz teraz i zainstaluj po ponownym uruchomieniu**.

4. Uruchom ponownie serwer usługi Jenkins, aby zakończyć instalację.

### <a name="install-helm-and-kubectl"></a>Zainstaluj Helm i kubectl

Potok przykładu używa Helm i kubectl do wdrożenia w przestrzeni deweloperów. Po zainstalowaniu usługi Jenkins tworzy konto administratora o nazwie *jenkins*. Helm i kubectl muszą być dostępne dla użytkownika usługi Jenkins.

1. Nawiązuj połączenie SSH z wzorcem usługi Jenkins. 

2. Przełącz `jenkins` się do użytkownika:
    ```bash
    sudo su jenkins
    ```

3. Zainstaluj interfejsu wiersza polecenia helm. Aby uzyskać więcej informacji, zobacz [Instalowanie helmu](https://helm.sh/docs/using_helm/#installing-helm).

4. Zainstaluj narzędzie kubectl. Aby uzyskać więcej informacji, zobacz [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Dodawanie poświadczeń do usługi Jenkins

1. Jenkins potrzebuje jednostki usługi platformy Azure do uwierzytelniania i uzyskiwania dostępu do zasobów platformy Azure. Aby utworzyć jednostkę usługi, zapoznaj się z sekcją [Utwórz jednostkę](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) usługi w samouczku Wdrażanie w usłudze Azure App Service. Pamiętaj, aby zapisać kopię danych `create-for-rbac` wyjściowych, ponieważ potrzebujesz tych informacji, aby wykonać następny krok. Dane wyjściowe będą przypominać następujące:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Dodaj typ poświadczeń *jednostki usługi Microsoft Azure* w usłudze Jenkins przy użyciu informacji o jednostki usługi z poprzedniego kroku. Nazwy na poniższym zrzucie `create-for-rbac`ekranu odpowiadają wyjściu z .

    Pole **identyfikatora** jest nazwą poświadczeń usługi Jenkins dla jednostki usługi. W przykładzie użyto `displayName` wartości (w `xxxxxxxjenkinssp`tym przypadku), ale można użyć dowolnego tekstu, który chcesz. Ta nazwa poświadczeń jest wartością zmiennej środowiskowej AZURE_CRED_ID w następnej sekcji.

    ![Dodawanie poświadczeń jednostki usługi do usługi Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    **Opis** jest opcjonalny. Aby uzyskać bardziej szczegółowe instrukcje, zobacz [Dodawanie jednostki usługi do usługi Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) sekcji w wdrażaniu w usłudze Azure App Service samouczka. 



3. Aby wyświetlić poświadczenia usługi ACR, uruchom to polecenie:

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    Zrób kopię wyjścia JSON, która powinna wyglądać mniej więcej tak:

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

4. Dodaj *nazwę użytkownika z typem* poświadczeń hasła w uliczce w uliczce w uliczce. Nazwa **użytkownika** to nazwa użytkownika z ostatniego kroku, w tym przykładzie `acr01`. **Hasło** jest wartością pierwszego hasła, w `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`tym przykładzie . **Identyfikator** tego poświadczenia jest wartością ACR_CRED_ID.

5. Konfigurowanie poświadczeń AKS. Dodaj typ poświadczeń *konfiguracji Kubernetes (kubeconfig)* w uliczce Jenkins (użyj opcji "Wprowadź bezpośrednio"). Aby uzyskać poświadczenia dostępu do klastra usługi AKS, uruchom następujące polecenie:

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   **Identyfikator** tego poświadczenia jest wartością KUBE_CONFIG_ID w następnej sekcji.

## <a name="create-a-pipeline"></a>Tworzenie potoku

Scenariusz wybrany dla przykładowego potoku jest oparty na wzorzec rzeczywistych: żądanie ściągnięcia wyzwala potok ciągłej integracji, który tworzy, a następnie wdraża proponowane zmiany w przestrzeni deweloperskiej platformy Azure do testowania i przeglądu. W zależności od wyniku przeglądu zmiany są scalane i wdrażane w udręki AKS lub odrzucane. Na koniec przestrzeń dewelopera jest usuwana.

Konfiguracja potoku jenkins i Jenkinsfile definiują etapy w potoku ciągłej integracji. Ten schemat blokowy pokazuje etapy potoku i punkty decyzyjne zdefiniowane przez plik Jenkinsa:

![Przepływ potoku Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Pobierz zmodyfikowaną wersję projektu *mywebapi* z [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi). Ten projekt zawiera kilka plików potrzebnych do utworzenia potoku, w tym *Jenkinsfile*, *Dockerfiles*i Helm chart.

2. Zaloguj się do usługi Jenkins. Z menu po lewej stronie wybierz pozycję **Dodaj element**.

3. Wybierz **pozycję Potok**, a następnie wprowadź nazwę w polu Wprowadź nazwę **elementu.** Wybierz **przycisk OK**, a następnie ekran konfiguracji potoku zostanie automatycznie otwarty.

4. Na karcie **Ogólne** zaznacz pole **wyboru Przygotowywanie środowiska do uruchomienia**. 

5. Sprawdź **zachowaj zmienne środowiskowe usługi Jenkins** i **zachowaj zmienne kompilacji usługi Jenkins**.

6. W polu **Zawartość właściwości** wprowadź następujące zmienne środowiskowe:

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

    Korzystając z przykładowych wartości podanych w poprzednich sekcjach, lista zmiennych środowiskowych powinna wyglądać mniej więcej tak:

    ![Zmienne środowiskowe potoku usługi Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Wybierz **skrypt potoku z SCM** w **definicji > potoku**.
8. W **SCM**wybierz **git,** a następnie wprowadź adres URL repozytorium.
9. W **pliku Gałązka**wprowadź . `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`
10. Wpisz adres URL repozytorium SCM i ścieżkę skryptu "Jenkinsfile".
11. Należy sprawdzić **lekkie kasy.**

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Tworzenie żądania ściągnięcia w celu wyzwolenia potoku

Aby wykonać krok 3 w tej sekcji, należy skomentować część Jenkinsfile, w przeciwnym razie otrzymasz błąd 404 podczas próby wyświetlenia nowych i starych wersji obok siebie. Domyślnie po wybraniu scalić PR, poprzednia udostępniona wersja mywebapi zostanie usunięta i zastąpiona nową wersją. Przed wykonaniem kroku 1 należy wprowadzić następujące zmiany w pliku Jenkins:

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

1. W celu `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`dokonania zmiany, a następnie utworzenia żądania ściągnięcia. Przykład:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Zaloguj się do usługi Jenkins i wybierz nazwę potoku, a następnie wybierz pozycję **Zbuduj teraz**. 

    Można również skonfigurować element *webhook,* aby automatycznie wyzwolić potoku usługi Jenkins. Po wprowadzeniu żądania ściągnięcia GitHub wystawia post do usługi Jenkins, wyzwalając potok. Aby uzyskać więcej informacji na temat konfigurowania elementu webhook, zobacz [Łączenie usługi Jenkins z usługą GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Porównaj zmiany z bieżącą wersją udostępnioną:

    1. Otwórz przeglądarkę i przejdź `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`do wersji udostępnionej . TEST_ENDPOINT zawiera adres URL.

    2. Otwórz inną kartę, a następnie wprowadź adres URL obszaru deweloperów. Będzie podobny do `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Znajdziesz łącze w **historii kompilacji > <kompilacji#> > dane wyjściowe konsoli** dla zadania usługi Jenkins. Wyszukaj `aksapp`stronę w poszukiwaniu lub aby `azdsprefix`wyświetlić tylko prefiks, wyszukaj .

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Konstruowanie adresu URL obszaru deweloperów podrzędnych

Podczas składania żądania ściągnięcia Jenkins tworzy miejsce dewelopera podrzędnego na podstawie udostępnionego miejsca dewelopera zespołu i uruchamia kod z żądania ściągnięcia w tym podrzędnym miejscu deweloperskim. Adres URL do przestrzeni deweloperów `http://$env.azdsprefix.<test_endpoint>`podrzędnych ma formę . 

**$env.azdsprefix** jest ustawiany podczas wykonywania potoku przez wtyczkę Azure Dev Spaces przez **devSpacesCreate:**

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

Jest `test_endpoint` to adres URL do aplikacji webfrontend, `azds up`którą wcześniej wdrożono w [przypadku wdrażania przykładowych aplikacji w klastrze AKS, krok 7](#test_endpoint). Wartość jest `$env.TEST_ENDPOINT` ustawiona w konfiguracji potoku. 

Poniższy fragment kodu pokazuje, jak adres URL obszaru `smoketest` dewelopera podrzędnego jest używany na stole montażowym. Kod sprawdza, czy miejsce dewelopera podrzędnego TEST_ENDPOINT jest dostępne, a jeśli tak, pobiera tekst powitania do stdout:

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

Po zakończeniu korzystania z przykładowej aplikacji wyczyść zasoby platformy Azure, usuwając grupę zasobów:

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ciągła integracja/dysk CD z usługą Jenkins na platformie Azure](jenkins-continuous-deployment.md)

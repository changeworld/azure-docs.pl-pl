---
title: Wdrażanie z usługi GitHub w usłudze AKS w usłudze Jenkins
titleSuffix: Azure Kubernetes Service
description: Konfigurowanie usługi Jenkins dla ciągłej integracji (CI) z usługi GitHub i ciągłego wdrażania (CD) do usługi Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: a93cfc77178ff7638217663c2ceda500aae4cfd7
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668634"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Samouczek: Wdrażanie z usługi GitHub w usłudze Azure Kubernetes (AKS) dzięki ciągłej integracji i wdrażaniu usługi Jenkins

W tym samouczku wdraża przykładową aplikację z usługi GitHub do klastra [usługi Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) przez skonfigurowanie ciągłej integracji (CI) i ciągłego wdrażania (CD) w usłudze Jenkins. W ten sposób po zaktualizowaniu aplikacji przez wypychanie zatwierdzeń do usługi GitHub usługa Jenkins automatycznie uruchamia nową kompilację kontenera, wypycha obrazy kontenerów do rejestru kontenerów platformy Azure (ACR), a następnie uruchamia aplikację w usłudze AKS. 

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Wdrażanie przykładowej aplikacji do głosowania platformy Azure w klastrze AKS.
> * Tworzenie podstawowego projektu jenkinsa.
> * Skonfiguruj poświadczenia dla usługi Jenkins do interakcji z acr.
> * Tworzenie zadania kompilacji usługi Jenkins i elementu webhook Usługi GitHub dla kompilacji automatycznych.
> * Przetestuj potok ciągłej integracji/ciągłego wdrażania, aby zaktualizować aplikację w usłudze AKS na podstawie zatwierdzania kodu Usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są potrzebne następujące elementy:

- Podstawowa wiedza o obrazach kubernetes, Git, CI/CD i kontenerowych

- [Klaster AKS](../aks/kubernetes-walkthrough.md) i `kubectl` skonfigurowany z [poświadczeniami klastra AKS](/cli/azure/aks#az-aks-get-credentials).

- [Rejestr usługi Azure Container Registry (ACR),](../container-registry/container-registry-get-started-azure-cli.md)nazwa serwera logowania ACR i klaster AKS skonfigurowany do [uwierzytelniania przy użyciu rejestru ACR](../aks/cluster-container-registry-integration.md).

- Zainstalowana i skonfigurowana i skonfigurowana i skonfigurowana usługa Azure CLI w wersji 2.0.46 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

- [Docker zainstalowany](https://docs.docker.com/install/) w systemie dewelopera

- Konto GitHub, [token dostępu osobistego GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)i klient Git zainstalowany w systemie deweloperskim

- Jeśli podasz własne wystąpienie usługi Jenkins, a nie ten przykładowy sposób wdrażania usługi Jenkins, wystąpienie usługi Jenkins wymaga [zainstalowania i skonfigurowania](https://docs.docker.com/install/) platformy Docker oraz [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## <a name="prepare-your-app"></a>Przygotowanie aplikacji

W tym artykule należy użyć przykładowej aplikacji głosowania platformy Azure, która zawiera interfejs sieci web hostowany w co najmniej jednym zasobniku i drugiego zasobnika obsługującego usługę Redis do tymczasowego przechowywania danych. Przed zintegrowaniem usługi Jenkins i usługi AKS w celu wdrożenia automatycznego należy najpierw ręcznie przygotować i wdrożyć aplikację głosowania platformy Azure w klastrze usługi AKS. To wdrożenie ręczne jest wersją jednej z aplikacji i umożliwia wyświetlenie aplikacji w akcji.

> [!NOTE]
> Przykładowa aplikacja do głosowania platformy Azure używa zasobnika systemu Linux, który jest zaplanowany do uruchomienia w węźle systemu Linux. Przepływ opisany w tym artykule działa również dla zasobnika systemu Windows Server zaplanowanego w węźle systemu Windows Server.

Rozwidli następujące repozytorium GitHub dla [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis)przykładowej aplikacji - . Aby utworzyć rozwidlenie repozytorium na koncie usługi GitHub, wybierz przycisk **Rozwidlenie** w prawym górnym rogu.

Sklonuj widelec do systemu programistycznego. Upewnij się, że używasz adresu URL widelca podczas klonowania tego repozytorium:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Zmień katalog sklonowanego widelca:

```console
cd azure-voting-app-redis
```

Aby utworzyć obrazy kontenerów potrzebne dla przykładowej aplikacji, użyj pliku *docker-compose.yaml* z: `docker-compose`

```console
docker-compose up -d
```

Wymagane obrazy podstawowe są pobierane i kontenery aplikacji zbudowany. Następnie można użyć polecenia [obrazy platformy docker,](https://docs.docker.com/engine/reference/commandline/images/) aby wyświetlić utworzony obraz. Zostały pobrane lub utworzone trzy obrazy. Obraz `azure-vote-front` zawiera aplikację i używa obrazu `nginx-flask` jako podstawy. Obraz `redis` jest używany do uruchamiania wystąpienia Redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Aby można było wypchnąć obraz kontenera *z przodu głosowania azure* do programu ACR, pobierz serwer logowania ACR za pomocą polecenia [az acr list.](/cli/azure/acr#az-acr-list) W poniższym przykładzie zostanie odnajduje się adres serwera logowania usługi ACR dla rejestru w grupie zasobów o nazwie *myResourceGroup:*

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Użyj polecenia [tagu docker,](https://docs.docker.com/engine/reference/commandline/tag/) aby oznaczyć obraz nazwą serwera `v1`logowania ACR i numerem wersji programu . Podaj `<acrLoginServer>` własne imię i nazwisko uzyskane w poprzednim kroku:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Na koniec wypchnij obraz *frontowy azure-vote* do rejestru ACR. Ponownie zastąp `<acrLoginServer>` nazwą serwera logowania własnego rejestru `myacrregistry.azurecr.io`ACR, takiego jak:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Wdrażanie przykładowej aplikacji w udręki AKS

Aby wdrożyć przykładową aplikację do klastra AKS, można użyć pliku manifestu Kubernetes w katalogu głównym repozytorium głosowania platformy Azure. Otwórz plik manifestu *azure-vote-all-in-one-redis.yaml* za `vi`pomocą edytora, takiego jak . Zastąp wartość `microsoft` nazwą serwera logowania usługi ACR. Ta wartość znajduje się w wierszu **47** pliku manifestu:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Następnie użyj polecenia [zastosuj kubectl,](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) aby wdrożyć aplikację w klastrze usługi AKS:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Usługa modułu równoważenia obciążenia Kubernetes jest tworzona w celu udostępnienia aplikacji do Internetu. Ten proces może potrwać kilka minut. Aby monitorować postęp wdrożenia modułu równoważenia obciążenia, użyj `--watch` polecenia [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) z argumentem. Po zmianie adresu *IP z* *oczekującego na* adres *IP*należy użyć `Control + C` do zatrzymania procesu obserwowania kubectl.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Aby wyświetlić aplikację w akcji, otwórz przeglądarkę internetową na zewnętrzny adres IP usługi. Aplikacja głosowania platformy Azure jest wyświetlana, jak pokazano w poniższym przykładzie:

![Przykładowa aplikacja do głosowania platformy Azure uruchomiona w usłudze AKS](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Wdrażanie usługi Jenkins na maszynie wirtualnej platformy Azure

Aby szybko wdrożyć usługę Jenkins do użycia w tym artykule, można użyć następującego skryptu do wdrożenia maszyny wirtualnej platformy Azure, skonfigurowania dostępu do sieci i ukończenia podstawowej instalacji usługi Jenkins. W przypadku uwierzytelniania między usługą Jenkins a klastrem AKS skrypt kopiuje plik konfiguracyjny usługi Kubernetes z systemu deweloperskiego do systemu Jenkins.

> [!WARNING]
> Ten przykładowy skrypt służy do celów demonstracyjnych, aby szybko aprowizować środowisko usługi Jenkins, które działa na maszynie Wirtualnej platformy Azure. Używa rozszerzenia skryptu niestandardowego platformy Azure do skonfigurowania maszyny Wirtualnej, a następnie wyświetlenia wymaganych poświadczeń. Urządzenie *~/.kube/config* jest kopiowane do maszyny Wirtualnej usługi Jenkins.

Uruchom następujące polecenia, aby pobrać i uruchomić skrypt. Przed uruchomieniem skryptu należy przejrzeć [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)zawartość dowolnego skryptu - .

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Utworzenie maszyny Wirtualnej i wdrożenie wymaganych składników dla platformy Docker i Jenkins zajmuje kilka minut. Po zakończeniu skryptu wyprowadza adres serwera jenkins i klucz do odblokowania pulpitu nawigacyjnego, jak pokazano w poniższym przykładzie danych wyjściowych:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Otwórz przeglądarkę internetową na wyświetlony adres URL i wprowadź klucz odblokowania. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby ukończyć konfigurację usługi Jenkins:

- Wybierz **pozycję Zainstaluj sugerowane wtyczki**
- Utwórz pierwszego użytkownika administratora. Wprowadź nazwę użytkownika, taką jak *azureuser,* a następnie podaj własne bezpieczne hasło. Na koniec wpisz pełną nazwę i adres e-mail.
- Wybierz pozycję **Zapisz i zakończ**.
- Gdy narzędzie Jenkins będzie gotowe, wybierz pozycję **Rozpocznij korzystanie z narzędzia Jenkins**.
    - Jeśli po rozpoczęciu korzystania z narzędzia Jenkins w przeglądarce internetowej zostanie wyświetlona pusta strona, uruchom ponownie usługę Jenkins. Aby ponownie uruchomić usługę, SSH do publicznego adresu `sudo service jenkins restart`IP wystąpienia usługi Jenkins i wpisz . Po ponownym uruchomieniu usługi odśwież przeglądarkę internetową.
- Zaloguj się do usługi Jenkins przy użyciu nazwy użytkownika i hasła utworzonego w procesie instalacji.

## <a name="create-a-jenkins-environment-variable"></a>Tworzenie zmiennej środowiskowej usługi Jenkins

Zmienna środowiskowa usługi Jenkins jest używana do przechowywania nazwy serwera logowania usługi ACR. Do tej zmiennej odwołuje się podczas zadania kompilacji jenkins. Aby utworzyć tę zmienną środowiskową, wykonaj następujące czynności:

- Po lewej stronie portalu usługi Jenkins wybierz pozycję **Zarządzaj** > **systemem konfigurowania** usługi Jenkins
- W obszarze **Właściwości globalne**wybierz pozycję **Zmienne środowiskowe**. Dodaj zmienną o `ACR_LOGINSERVER` nazwie i wartości serwera logowania ACR.

    ![Zmienne środowiskowe usługi Jenkins](media/jenkins-continuous-deployment/env-variables.png)

- Po zakończeniu kliknij przycisk **Zapisz** u dołu strony konfiguracji usługi Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Tworzenie poświadczeń usługi Jenkins dla usługi ACR

Aby zezwolić jenkins do tworzenia, a następnie wypychania zaktualizowanych obrazów kontenerów do usługi ACR, należy określić poświadczenia dla usługi ACR. To uwierzytelnianie może używać podmiotów usługi Azure Active Directory. W wymaganiach wstępnych skonfigurowano jednostkę usługi dla klastra AKS z uprawnieniami *czytnika* do rejestru usługi ACR. Uprawnienia te umożliwiają klastrowi AKS *ściąganie* obrazów z rejestru ACR. Podczas procesu ciągłej integracji/ciągłego wdrażania jenkins tworzy nowe obrazy kontenerów na podstawie aktualizacji aplikacji, a następnie *wypycha* te obrazy do rejestru usługi ACR. W przypadku rozdzielania ról i uprawnień należy teraz skonfigurować jednostkę usługi dla usługi Jenkins z uprawnieniami *współautora* do rejestru usługi ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Tworzenie jednostki usługi dla usługi Jenkins do używania usługi ACR

Najpierw utwórz jednostkę usługi za pomocą polecenia [az ad sp create-for-rbac:](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Zanotuj *appId* i *hasło* wyświetlane w danych wyjściowych. Te wartości są używane w następujących krokach, aby skonfigurować zasób poświadczeń w u jenkins.

Pobierz identyfikator zasobu rejestru ACR za pomocą polecenia [az acr show](/cli/azure/acr#az-acr-show) i przechowuj go jako zmienną. Podaj nazwę grupy zasobów i nazwę ACR:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Teraz utwórz przypisanie roli, aby przypisać prawa *współautora* usługi do rejestru usługi ACR. W poniższym przykładzie podaj własny *appId* wyświetlane w danych wyjściowych poprzednie polecenie, aby utworzyć jednostkę usługi:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Tworzenie zasobu poświadczeń w usłudze Jenkins dla jednostki usługi ACR

Po utworzeniu przypisania roli na platformie Azure należy teraz przechowywać poświadczenia usługi ACR w obiekcie poświadczeń usługi Jenkins. Te poświadczenia są odwoływane podczas zadania kompilacji jenkins.

Po powrocie po lewej stronie portalu jenkins kliknij pozycję Poświadczenia**globalne poświadczeń** >  **Credentials** > **usługi Jenkins** > (nieograniczone)**Dodawanie poświadczeń**

Upewnij się, że rodzaj poświadczeń to **Nazwa użytkownika z hasłem** i wprowadź następujące elementy:

- **Nazwa użytkownika** — *appId* jednostki usługi utworzonej do uwierzytelniania za pomocą rejestru ACR.
- **Hasło** — *hasło* jednostki usługi utworzonej do uwierzytelniania za pomocą rejestru ACR.
- **Identyfikator** — identyfikator poświadczeń, taki jak *poświadczenia acr*

Po zakończeniu formularz poświadczeń wygląda następująco:

![Tworzenie obiektu poświadczeń usługi Jenkins z informacjami o jednostki usługi](media/jenkins-continuous-deployment/acr-credentials.png)

Kliknij **przycisk OK** i wróć do portalu jenkins.

## <a name="create-a-jenkins-project"></a>Tworzenie projektu usługi Jenkins

Na stronie głównej portalu usługi Jenkins wybierz pozycję **Nowy element** po lewej stronie:

1. Wprowadź *azure-vote* jako nazwę zadania. Wybierz **projekt Freestyle**, a następnie wybierz przycisk **OK**
1. W sekcji **Ogólne** wybierz **projekt GitHub** i wprowadź rozwidlony adres URL repozytorium, na przykład *\/https: /github.com/\<your-github-account\>/azure-voting-app-redis*
1. W sekcji **Zarządzanie kodem źródłowym** wybierz **git**, wprowadź rozwidlony adres URL repozytorium *.git,* na przykład *\/https: /github.com/\<your-github-account\>/azure-voting-app-redis.git*

1. W sekcji **Wyzwalacze kompilacji** wybierz **wyzwalacz haka GitHub dla sondowania GITCM**
1. W obszarze **Środowisko kompilacji**wybierz pozycję **Użyj tajnych tekstów lub plików**
1. W obszarze **Powiązania**wybierz **pozycję Dodaj** > **nazwę użytkownika i hasło (oddzielone)**
   - Wprowadź `ACR_ID` **zmienną nazwy** `ACR_PASSWORD` użytkownika i **zmienną hasła**

     ![Powiązania jenkins](media/jenkins-continuous-deployment/bindings.png)

1. Wybierz, aby dodać **krok kompilacji** typu **Wykonaj powłokę** i użyj następującego tekstu. Ten skrypt tworzy nowy obraz kontenera i wypycha go do rejestru usługi ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Dodaj kolejny **krok kompilacji** typu **Wykonaj powłokę** i użyj następującego tekstu. Ten skrypt aktualizuje wdrożenie aplikacji w aks z nowym obrazem kontenera z usługi ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Po zakończeniu kliknij przycisk **Zapisz**.

## <a name="test-the-jenkins-build"></a>Testowanie kompilacji jenkinsa

Przed zautomatyzowaniem zadania na podstawie zatwierdzania GitHub najpierw ręcznie przetestować kompilację usługi Jenkins. Ta kompilacja ręczna sprawdza, czy zadanie zostało poprawnie skonfigurowane, odpowiedni plik uwierzytelniania Kubernetes jest na miejscu i czy uwierzytelnianie za pomocą usługi ACR działa.

W menu po lewej stronie projektu wybierz pozycję **Buduj teraz**.

![Kompilacja testna jenkinsa](media/jenkins-continuous-deployment/test-build.png)

Pierwsza kompilacja trwa minutę lub dwie, jak warstwy obrazu platformy Docker są ściągane w dół do serwera jenkins. Kolejne kompilacje można użyć warstw obrazu w pamięci podręcznej, aby poprawić czas kompilacji.

Podczas procesu kompilacji repozytorium GitHub jest klonowane do serwera kompilacji jenkins. Nowy obraz kontenera jest zbudowany i wypychany do rejestru ACR. Na koniec aplikacja głosowania platformy Azure uruchomiona w klastrze AKS jest aktualizowana w celu użycia nowego obrazu. Ponieważ nie wprowadzono żadnych zmian w kodzie aplikacji, aplikacja nie zostanie zmieniona, jeśli wyświetlisz przykładową aplikację w przeglądarce sieci Web.

Po zakończeniu zadania kompilacji kliknij na **kompilacji #1** w historii kompilacji. Wybierz **wyjście konsoli** i wyświetl dane wyjściowe z procesu kompilacji. Końcowy wiersz powinien wskazywać pomyślną kompilację.

## <a name="create-a-github-webhook"></a>Tworzenie elementu webhook usługi GitHub

Po pomyślnym zakończeniu ręcznej kompilacji teraz zintegruj gitHub z kompilacją usługi Jenkins. Element webhook może służyć do uruchamiania zadania kompilacji usługi Jenkins za każdym razem, gdy zatwierdzenie kodu jest w gitHub. Aby utworzyć element webhook Usługi GitHub, wykonaj następujące kroki:

1. Przejdź do rozwidlonego repozytorium GitHub w przeglądarce internetowej.
1. Wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Elementy webhook** po lewej stronie.
1. Wybierz opcję **Dodaj element webhook**. W przypadku *adresu URL ładunku*wprowadź `http://<publicIp:8080>/github-webhook/`, gdzie `<publicIp>` znajduje się adres IP serwera Jenkins. Upewnij się, że na końcu /. Pozostaw inne ustawienia domyślne dla typu zawartości i wyzwolić na zdarzenia *wypychania.*
1. Wybierz **pozycję Dodaj element webhook**.

    ![Tworzenie elementu webhook usługi GitHub dla usługi Jenkins](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testowanie kompletnego potoku ciągłej integracji/ciągłego wdrażania

Teraz możesz przetestować cały potok ciągłej integracji/ciągłego wdrażania. Po wypchnięciu zatwierdzenia kodu do gitHub, następujące kroki się zdarzyć:

1. Element webhook GitHub dociera do usługi Jenkins.
1. Jenkins uruchamia zadanie kompilacji i pobiera najnowsze zatwierdzenie kodu z usługi GitHub.
1. Kompilacja platformy Docker jest uruchamiana przy użyciu zaktualizowanego kodu, a nowy obraz kontenera jest oznaczony najnowszym numerem kompilacji.
1. Ten nowy obraz kontenera jest wypychany do rejestru kontenerów platformy Azure.
1. Aplikacja wdrożona w usłudze Azure Kubernetes service jest aktualizowana przy zastosowaniu najnowszego obrazu kontenera z rejestru kontenerów platformy Azure.

Na komputerze deweloperskim otwórz sklonowaną aplikację za pomocą edytora kodu. W katalogu */azure-vote/azure-vote* otwórz plik o nazwie **config_file.cfg**. Zaktualizuj wartości głosowania w tym pliku do czegoś innego niż koty i psy, jak pokazano w poniższym przykładzie:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Po zaktualizowaniu zapisz plik, zaobję zmiany i wypchnij je do rozwidlonego repozytorium GitHub. Element webhook GitHub wyzwala nowe zadanie kompilacji w usłudze Jenkins. Na pulpicie nawigacyjnym sieci Web usługi Jenkins monitoruj proces kompilacji. Trwa kilka sekund, aby wyciągnąć najnowszy kod, utworzyć i wypchnąć zaktualizowany obraz i wdrożyć zaktualizowaną aplikację w uzywu usługi AKS.

Po zakończeniu kompilacji odśwież przeglądarkę sieci Web przykładowej aplikacji do głosowania platformy Azure. Zmiany są wyświetlane, jak pokazano w poniższym przykładzie:

![Przykładowe głosowanie na platformie Azure w usłudze AKS zaktualizowane przez zadanie kompilacji usługi Jenkins](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Usługa Jenkins na platformie Azure](/azure/jenkins)
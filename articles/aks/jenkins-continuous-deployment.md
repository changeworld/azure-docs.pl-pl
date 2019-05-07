---
title: Samouczek — wdrażanie z repozytorium GitHub do usługi Azure Kubernetes Service (AKS) przy użyciu narzędzia Jenkins
description: Konfigurowanie narzędzia Jenkins dla ciągłej integracji (CI) z serwisu GitHub i ciągłe wdrażanie (CD) do usługi Azure Kubernetes Service (AKS)
services: container-service
ms.service: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: 703aa081c8acf41f9206e2b0ccff45571367d2e8
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073786"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Samouczek: Wdrażanie z repozytorium GitHub do usługi Azure Kubernetes Service (AKS) przy użyciu narzędzia Jenkins ciągłej integracji i ciągłego wdrażania

W tym samouczku wdraża przykładową aplikację z witryny GitHub i [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) klastra przez skonfigurowanie ciągłej integracji (CI) i ciągłe wdrażanie (CD) w usłudze Jenkins. Dzięki temu po zaktualizowaniu aplikacji przez wypychanie zatwierdzeń do repozytorium GitHub, Jenkins automatycznie uruchamia nową kompilację kontenera, wypychanie obrazów kontenera do usługi Azure Container Registry (ACR), a następnie uruchamia aplikację w usłudze AKS. 

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Wdróż przykładową aplikację głosowania na platformie Azure z klastrem usługi AKS.
> * Tworzenie podstawowego projektu usługi Jenkins.
> * Skonfiguruj poświadczenia dla serwera Jenkins na interakcję z rejestru Azure container Registry.
> * Utwórz element webhook usługi GitHub dla zautomatyzowanych kompilacji i zadania kompilacji usługi Jenkins.
> * Testowanie potoku ciągłej integracji/ciągłego wdrażania, aby zaktualizować aplikację w usłudze AKS w oparciu o zatwierdzenia kodu GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są potrzebne następujące elementy:

- Podstawową wiedzę na temat usługi Kubernetes, Git, ciągłej integracji/ciągłego Dostarczania i kontenera obrazów

- [Klastra AKS] [ aks-quickstart] i `kubectl` skonfigurowano [poświadczeń klastra usługi AKS][aks-credentials]

- [Rejestru Azure Container Registry (ACR)][acr-quickstart], nazwę serwera logowania usługi ACR i klastrem AKS skonfigurowany tak, aby [uwierzytelnianie za pomocą rejestru ACR][acr-authentication]

- Wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej zainstalowany i skonfigurowany. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

- [Zainstalowana platforma docker] [ docker-install] w systemie deweloperskim

- Konto usługi GitHub [osobisty token dostępu GitHub][git-access-token]i zainstalowanego w systemie deweloperskim klienta usługi Git

- Jeśli podasz własne narzędzia Jenkins wystąpienia, a nie w tym przykładzie inicjowanych przez skrypty sposób wdrażania narzędzia Jenkins, usługi Jenkins wystąpienia potrzeb [platformy Docker zainstalowany i skonfigurowany] [ docker-install] i [kubectl][kubectl-install].

## <a name="prepare-your-app"></a>Przygotowywanie aplikacji

W tym artykule używamy przykładową aplikację głosowania na platformie Azure, który zawiera interfejs sieci web hostowanych w zasobników przynajmniej jednego i drugiego zasobnik obsługującego magazyn Redis do przechowywania danych tymczasowych. Zanim włączysz Jenkins i usługi AKS w przypadku zautomatyzowanych wdrożeń, najpierw ręcznie przygotować i wdrożyć aplikację głosowania na platformie Azure w klastrze AKS. To ręczne wdrożenie jest wersja 1 aplikacji i pozwala zobaczyć aplikację w akcji.

Utworzenie rozwidlenia repozytorium GitHub poniższej przykładowej aplikacji — [ https://github.com/Azure-Samples/azure-voting-app-redis ](https://github.com/Azure-Samples/azure-voting-app-redis). Aby utworzyć rozwidlenie repozytorium na koncie usługi GitHub, wybierz przycisk **Rozwidlenie** w prawym górnym rogu.

Sklonuj rozwidlenie również na systemie deweloperskim. Upewnij się, że używasz adresu URL Twojego rozwidlenia tego repozytorium klonowanie:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Przejdź do katalogu rozwidlenia sklonowany:

```console
cd azure-voting-app-redis
```

Aby utworzyć obrazy kontenerów, służące do przykładowej aplikacji, użyj *docker compose.yaml* plik z `docker-compose`:

```console
docker-compose up -d
```

Wymagane obrazy podstawowe są pobierane i wbudowanych kontenerów aplikacji. Następnie można użyć [obrazów platformy docker] [ docker-images] polecenie, aby wyświetlić utworzony obraz. Zostały pobrane lub utworzone trzy obrazy. Obraz `azure-vote-front` zawiera aplikację i używa obrazu `nginx-flask` jako podstawy. `redis` Obrazu jest używany do uruchomienia wystąpienia pamięci podręcznej Redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Zanim będzie można wypchnąć *azure-vote-front* obraz kontenera do rejestru Azure container Registry, Pobierz serwera logowania usługi ACR za pomocą [az acr list] [ az-acr-list] polecenia. Poniższy przykład pobiera adres serwera logowania usługi ACR dla rejestru w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Użyj [docker tag] [ docker-tag] polecenie, aby utworzyć tag obrazu z nazwę serwera logowania usługi ACR i numer wersji `v1`. Podaj własny `<acrLoginServer>` nazwa uzyskanego w poprzednim kroku:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Na koniec wypychania *azure-vote-front* obrazu do rejestru usługi ACR. Ponownie `<acrLoginServer>` nazwą serwera logowania własnego rejestru ACR, takich jak `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Wdrażanie przykładowej aplikacji w usłudze AKS

Aby wdrożyć przykładową aplikację do klastra usługi AKS, można użyć plik manifestu rozwiązania Kubernetes w katalogu głównym repozytorium repozytorium głosowania na platformie Azure. Otwórz *azure-vote-all-in-one-redis.yaml* manifest pliku za pomocą edytora, takich jak `vi`. Zastąp wartość `microsoft` nazwą serwera logowania usługi ACR. Ta wartość zostanie znaleziona w wierszu **47** pliku manifestu:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Następnie użyj [zastosować kubectl] [ kubectl-apply] polecenie, aby wdrożyć aplikację w klastrze AKS:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Usługa równoważenia obciążenia rozwiązania Kubernetes jest tworzony do udostępnienia aplikacji w Internecie. Ten proces może potrwać kilka minut. Aby monitorować postęp wdrażania modułu równoważenia obciążenia, użyj [kubectl get-service] [ kubectl-get] polecenia `--watch` argumentu. Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na *adres IP*, zatrzymaj proces śledzenia narzędzia kubectl za pomocą polecenia `Control + C`.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Aby wyświetlić aplikację w akcji, otwórz przeglądarkę sieci web w taki sposób, aby zewnętrzny adres IP usługi. Aplikacja do głosowania platformy Azure jest wyświetlana, jak pokazano w poniższym przykładzie:

![Aplikacja do głosowania przykładowy Azure uruchomione w usłudze AKS](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Wdrażanie usługi Jenkins na maszynie Wirtualnej platformy Azure

Szybko wdrożyć usługę Jenkins do użycia w tym artykule, poniższy skrypt służy do wdrażania maszyn wirtualnych, konfigurowanie dostępu do sieci, a następnie ukończ podstawową instalację usługi jenkins. Do uwierzytelniania między Jenkins i klastra AKS skrypt kopiuje plik konfiguracji Kubernetes w systemie deweloperskim do systemu Jenkins.

> [!WARNING]
> Ten przykładowy skrypt jest dla celów demonstracyjnych szybko aprowizować środowiska narzędzia Jenkins, które jest uruchamiane na Maszynie wirtualnej platformy Azure. Aby skonfigurować Maszynę wirtualną, a następnie Wyświetl wymagane poświadczenia używa rozszerzenia niestandardowego skryptu platformy Azure. Twoje *~/.kube/config* jest kopiowana do maszyny Wirtualnej usługi Jenkins.

Uruchom następujące polecenia, aby pobrać i uruchomić skrypt. Należy przejrzeć zawartość dowolnego skryptu przed uruchomieniem jej- [ https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh ](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Trwa kilka minut utworzyć maszynę Wirtualną i wdrożyć składniki wymagane dla platformy Docker i Jenkins. Po ukończeniu działania skryptu Wyświetla adres dla serwera Jenkins oraz klucza do Odblokuj pulpit nawigacyjny, jak pokazano w następujących przykładowych danych wyjściowych:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Otwórz przeglądarkę sieci web w taki sposób, aby adres URL wyświetlany, a następnie wprowadź klucz unlock. Postępuj zgodnie z monitami wyświetlanymi na ekranie w celu ukończenia konfiguracji usługi Jenkins:

- Wybierz **instalowanie sugerowanych wtyczek**
- Utwórz pierwszego użytkownika administratora. Wprowadź nazwę użytkownika, taką jak *azureuser*, następnie podaj bezpieczne hasło. Na koniec wpisz pełną nazwę i adres e-mail.
- Wybierz pozycję **Zapisz i zakończ**.
- Gdy narzędzie Jenkins będzie gotowe, wybierz pozycję **Rozpocznij korzystanie z narzędzia Jenkins**.
    - Jeśli po rozpoczęciu korzystania z narzędzia Jenkins w przeglądarce internetowej zostanie wyświetlona pusta strona, uruchom ponownie usługę Jenkins. Aby ponownie uruchomić usługę, SSH publiczny adres IP wystąpienie narzędzia Jenkins i typ `sudo service jenkins restart`. Po ponownym uruchomieniu usługi, możesz odświeżyć przeglądarkę sieci web.
- Zaloguj się do systemu Jenkins przy użyciu nazwy użytkownika i hasło utworzone w procesie instalacji.

## <a name="create-a-jenkins-environment-variable"></a>Utwórz zmienną środowiskową systemu Jenkins

Zmienna środowiskowa Jenkins jest używane do przechowywania nazwę serwera logowania usługi ACR. Ta zmienna odwołuje się podczas wykonywania zadania kompilacji usługi Jenkins. Aby utworzyć tej zmiennej środowiskowej, wykonaj następujące czynności:

- Po lewej stronie portalu narzędzia Jenkins, wybierz **Zarządzaj serwerem Jenkins** > **Konfigurowanie systemu**
- W obszarze **globalne właściwości**, wybierz opcję **zmienne środowiskowe**. Dodaj zmienną o nazwie `ACR_LOGINSERVER` i wartość serwera logowania usługi ACR.

    ![Zmienne środowiskowe systemu Jenkins](media/aks-jenkins/env-variables.png)

- Po zakończeniu kliknij przycisk **Zapisz** w dolnej części strony konfiguracji usługi Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Tworzenie serwera Jenkins poświadczeń dla rejestru Azure container Registry

Aby zezwolić na narzędzie Jenkins do kompilacji, a następnie Wypchnij obrazy zaktualizowanego kontenera do rejestru Azure container Registry, należy określić poświadczenia dla rejestru Azure container Registry. To uwierzytelnianie, można użyć jednostki usługi Azure Active Directory. W wymagania wstępne, skonfigurowano nazwy głównej usługi dla klastra usługi AKS przy użyciu *czytnika* uprawnienia do rejestru usługi ACR. Te uprawnienia zezwalają na klaster AKS *ściągnięcia* obrazów z rejestru ACR. W procesie ciągłej integracji/ciągłego wdrażania narzędzia Jenkins tworzy nowe obrazy kontenera, na podstawie aktualizacji aplikacji i należy następnie *wypychania* tych obrazów do rejestru ACR. Separacja ról i uprawnień, teraz skonfigurować nazwy głównej usługi dla serwera Jenkins przy użyciu *Współautor* uprawnienia do rejestru usługi ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Tworzenie jednostki usługi dla serwera Jenkins przy użyciu usługi ACR

Najpierw należy utworzyć jednostki usługi za pomocą [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] polecenia:

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

Zwróć uwagę na *appId* i *hasło* wyświetlany w danych wyjściowych. Te wartości są używane zawarte w poniższych krokach skonfigurowania zasobu poświadczeń w usłudze Jenkins.

Pobierz identyfikator zasobu usługi ACR rejestru przy użyciu [az acr show] [ az-acr-show] poleceń i zapisz go jako zmienną. Podaj nazwę grupy zasobów i nazwa ACR:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Teraz Utwórz przypisanie roli, aby przypisać nazwę główną usługi *Współautor* praw do rejestru ACR. W poniższym przykładzie podać własne *appId* wyświetlany w danych wyjściowych poprzedniego polecenia, aby utworzyć nazwę główną usługi:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Tworzenie zasobu poświadczeń w usłudze Jenkins dla usługi ACR jednostki

Przy użyciu przypisania roli, utworzony na platformie Azure należy teraz zapisać poświadczenia rejestru Azure container Registry w obiekt poświadczeń usługi Jenkins. Te poświadczenia są przywoływane podczas zadania kompilacji usługi Jenkins.

Na lewym rogu portalu narzędzia Jenkins, kliknij **poświadczenia** > **Jenkins** > **poświadczeń globalnych (nieograniczony)**  >  **Dodaj poświadczenia**

Upewnij się, że typ poświadczeń **nazwy użytkownika z hasłem** i wprowadź następujące elementy:

- **Nazwa użytkownika** — *appId* jednostki usługi, utworzony do uwierzytelniania przy użyciu rejestru usługi ACR.
- **Hasło** — *hasło* jednostki usługi, utworzony do uwierzytelniania przy użyciu rejestru usługi ACR.
- **Identyfikator** — takich jak poświadczenia identyfikatora *poświadczenia rejestru Azure container Registry*

Po zakończeniu formularza poświadczenia wygląda następująco:

![Utwórz obiekt poświadczeń usługi Jenkins za pomocą informacji o jednostce usługi](media/aks-jenkins/acr-credentials.png)

Kliknij przycisk **OK** i powróć do portalu narzędzia Jenkins.

## <a name="create-a-jenkins-project"></a>Utwórz projekt narzędzia Jenkins

Na stronie głównej portalu narzędzia Jenkins wybierz **nowy element** po lewej stronie:

1. Wprowadź *azure-vote* jako nazwę zadania. Wybierz **projekt Freestyle**, a następnie wybierz **OK**
1. W obszarze **ogólne** zaznacz **projektu GitHub** i wprowadź adres URL rozwidlonego repozytorium, taki jak *https:\//github.com/\<-github konta\>/azure-voting-app-redis*
1. W obszarze **zarządzanie kodem źródłowym** zaznacz **Git**, wprowadź rozwidlonego repozytorium *.git* adresu URL, takich jak *https:\//github.com/\<konta usługi github\>/azure-voting-app-redis.git*

1. W obszarze **kompilowanie wyzwalaczy** zaznacz **wyzwalacz punktu zaczepienia GitHub na potrzeby sondowania GITscm**
1. W obszarze **środowisko budowania środowiska**, wybierz opcję **za pomocą klucza tajnego teksty lub plików**
1. W obszarze **powiązania**, wybierz opcję **Dodaj** > **nazwy użytkownika i hasło (oddzielone)**
   - Wprowadź `ACR_ID` dla **zmienna nazwy użytkownika**, i `ACR_PASSWORD` dla **zmienna hasła**

     ![Powiązania usługi Jenkins](media/aks-jenkins/bindings.png)

1. Wybierz dodać **krok kompilacji** typu **wykonaj powłokę** i skorzystaj z poniższego tekstu. Ten skrypt tworzy nowy obraz kontenera i wypychany do rejestru usługi ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Dodaj kolejną **krok kompilacji** typu **wykonaj powłokę** i skorzystaj z poniższego tekstu. Ten skrypt aktualizacji wdrożenia aplikacji w usłudze AKS za pomocą nowy obraz kontenera z rejestru Azure container Registry.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Po zakończeniu kliknij przycisk **Zapisz**.

## <a name="test-the-jenkins-build"></a>Testowanie kompilacji usługi Jenkins

Przed rozpoczęciem automatyzowania zadań, w oparciu o zatwierdzenia GitHub, najpierw ręcznie przetestuj kompilację narzędzia Jenkins. Ręczna kompilacja weryfikuje, że zadania zostały prawidłowo skonfigurowane, prawidłowego pliku uwierzytelniania Kubernetes znajduje się w miejscu i czy uwierzytelnianie za pomocą rejestru Azure container Registry działa.

W menu po lewej stronie projektu, wybierz **Kompiluj teraz**.

![Jenkins testów kompilacji](media/aks-jenkins/test-build.png)

Pierwsza Kompilacja trwa około minutę lub dwie warstwy obrazu platformy Docker są pobierane w dół do serwera Jenkins. Kolejnych kompilacjach umożliwia warstwy pamięci podręcznej obrazu: skracanie czasu kompilacji.

Podczas procesu kompilacji w repozytorium GitHub został sklonowany serwer kompilacji usługi Jenkins. Nowy obraz kontenera jest utworzone i wypchnięte do rejestru usługi ACR. Na koniec uruchomionego w klastrze AKS aplikacja do głosowania platformy Azure jest aktualizowana, aby korzystała z nowego obrazu. Ponieważ wprowadzono żadnych zmian kodu aplikacji, aplikacja nie jest zmieniany, czy wyświetlać przykładową aplikację w przeglądarce sieci web.

Po zakończeniu zadań kompilacji kliknij **kompilacji #1** w obszarze Historia kompilacji. Wybierz **dane wyjściowe konsoli** i wyświetlić dane wyjściowe z procesu kompilacji. Ostatni wiersz powinien wskazywać pomyślnej kompilacji.

## <a name="create-a-github-webhook"></a>Utwórz element webhook usługi GitHub

Dzięki pomyślne zakończenie kompilacji ręcznych teraz zintegrować GitHub kompilację narzędzia Jenkins. Element webhook może służyć do uruchamiania zadania kompilacji usługi Jenkins za każdym razem, gdy przeprowadzane jest zatwierdzenie kodu w serwisie GitHub. Aby utworzyć element webhook usługi GitHub, wykonaj następujące czynności:

1. Przejdź do rozwidlonego repozytorium GitHub w przeglądarce sieci web.
1. Wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Elementy webhook** po lewej stronie.
1. Możliwość **Dodaj element webhook**. Aby uzyskać *adres URL ładunku*, wprowadź `http://<publicIp:8080>/github-webhook/`, gdzie `<publicIp>` jest adres IP serwera Jenkins. Upewnij się, że wpisano /. Pozostaw inne wartości domyślne dla typu zawartości i wyzwalacza w *wypychania* zdarzenia.
1. Wybierz **Dodaj element webhook**.

    ![Utwórz element webhook usługi GitHub dla usługi Jenkins](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testowanie pełny potok CI/CD

Teraz można przetestować cały potok ciągłej integracji/ciągłego wdrażania. Po wypchnięciu zatwierdzenia kodu do serwisu GitHub, się zdarzyć następujące czynności:

1. Element webhook usługi GitHub łączy się z usługą Jenkins.
1. Jenkins uruchamia zadania kompilacji i ściąga najnowsze zatwierdzenie kodu z repozytorium GitHub.
1. Kompilacji platformy Docker jest uruchomiona, po zaktualizowaniu kodu, a nowy obraz kontenera jest oznaczane numer najnowszej kompilacji.
1. Ten nowy obraz kontenera zostanie przypisany do usługi Azure Container Registry.
1. Aplikacja wdrożona na aktualizacje usługi Azure Kubernetes Service przy użyciu najnowszego obrazu kontenera z rejestru Azure Container Registry.

Na komputerze deweloperskim otwierają sklonowanej aplikacji za pomocą edytora kodu. W obszarze */azure-vote/azure-vote* katalog, otwórz plik o nazwie **config_file.cfg**. Zaktualizuj wartości głosu w tym pliku na coś innego niż koty i psy, jak pokazano w poniższym przykładzie:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Po zaktualizowaniu, Zapisz plik, Zatwierdź zmiany i Wypchnij je do swojego rozwidlenia repozytorium GitHub. Nowe zadanie kompilacji w usłudze Jenkins jest wyzwalana przez element webhook usługi GitHub. W sieci web pulpitu nawigacyjnego usługi Jenkins monitorowanie procesu kompilacji. Zajmuje kilka sekund, aby ściągnąć najnowszy kod, Utwórz i wypychanie zaktualizowanego obrazu i wdróż zaktualizowaną aplikację w usłudze AKS.

Po zakończeniu kompilacji, Odśwież przeglądarkę sieci web przykładowej aplikacji Azure vote. Dokonane zmiany są widoczne, jak pokazano w poniższym przykładzie:

![Azure próbki głosu w usłudze AKS aktualizowane przez zadania kompilacji usługi Jenkins](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób użycia narzędzia Jenkins w ramach rozwiązania ciągłej integracji/ciągłego wdrażania. AKS można zintegrować z innych rozwiązań do ciągłej integracji/ciągłego wdrażania i narzędzia automatyzacji, takie jak [projektu DevOps platformy Azure] [ azure-devops] lub [tworzenia klastra usługi AKS za pomocą rozwiązania Ansible] [ aks-ansible].

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[docker-install]: https://docs.docker.com/install/
[kubectl-install]: https://kubernetes.io/docs/tasks/tools/install-kubectl/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md#grant-aks-access-to-acr
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md

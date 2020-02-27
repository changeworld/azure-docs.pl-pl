---
title: Samouczek — wdrażanie z usługi GitHub w usłudze Azure Kubernetes Service (AKS) przy użyciu programu Jenkins
description: Konfigurowanie Jenkins na potrzeby ciągłej integracji (CI) z usługi GitHub i ciągłego wdrażania (CD) w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: eb48a8558aab6c7a832efe45650686d9df0d7426
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624750"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Samouczek: wdrażanie z usługi GitHub w usłudze Azure Kubernetes Service (AKS) przy użyciu ciągłej integracji i wdrażania programu Jenkins

W tym samouczku wdrożono przykładową aplikację z witryny GitHub do klastra [usługi Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) przez skonfigurowanie ciągłej integracji (ci) i ciągłego wdrażania (CD) w systemie Jenkins. Dzięki temu w przypadku aktualizowania aplikacji przez wypychanie zatwierdzeń do usługi GitHub usługa Jenkins automatycznie uruchamia nową kompilację kontenera, wypchnij obrazy kontenerów do Azure Container Registry (ACR), a następnie uruchomi aplikację w AKS. 

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Wdróż przykładową aplikację do głosowania na platformie Azure w klastrze AKS.
> * Utwórz podstawowy projekt Jenkins.
> * Skonfiguruj poświadczenia dla usługi Jenkins, aby móc korzystać z ACR.
> * Utwórz zadanie kompilacji Jenkins i element webhook usługi GitHub dla zautomatyzowanych kompilacji.
> * Przetestuj potok ciągłej integracji/ciągłego wdrażania, aby zaktualizować aplikację w programie AKS na podstawie zatwierdzeń kodu usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są potrzebne następujące elementy:

- Podstawowe informacje na temat Kubernetes, Git, CI/CD i obrazów kontenerów

- [Klaster AKS](../aks/kubernetes-walkthrough.md) i `kubectl` skonfigurowany przy użyciu [poświadczeń klastra AKS](/cli/azure/aks#az-aks-get-credentials).

- [Rejestr Azure Container Registry (ACR)](../container-registry/container-registry-get-started-azure-cli.md), nazwa serwera logowania ACR i klaster AKS skonfigurowany do [uwierzytelniania za pomocą rejestru ACR](../aks/cluster-container-registry-integration.md).

- Interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej został zainstalowany i skonfigurowany. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

- Platforma [Docker zainstalowana](https://docs.docker.com/install/) w systemie programistycznym

- Konto usługi GitHub, [osobisty token dostępu usługi GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)i klient usługi git zainstalowany w systemie programistycznym

- Jeśli postanowisz własne wystąpienie Jenkins zamiast tego przykładowego skryptu do wdrożenia Jenkins, wystąpienie Jenkins będzie wymagało [zainstalowania i skonfigurowania platformy Docker](https://docs.docker.com/install/) oraz [polecenia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## <a name="prepare-your-app"></a>Przygotowywanie aplikacji

W tym artykule użyto przykładowej aplikacji do głosowania platformy Azure, która zawiera interfejs sieci Web hostowany w jednym lub więcej zasobników, a drugi pod Redis hostingu na potrzeby tymczasowego przechowywania danych. Przed integracją usług Jenkins i AKS dla zautomatyzowanych wdrożeń najpierw należy ręcznie przygotować i wdrożyć aplikację do głosowania platformy Azure w klastrze AKS. To wdrożenie ręczne ma wersję jedną z aplikacji i pozwala zobaczyć aplikację w działaniu.

> [!NOTE]
> Przykładowa aplikacja do głosowania platformy Azure używa systemu Linux, który jest zaplanowany do uruchomienia w węźle systemu Linux. Przepływ przedstawiony w tym artykule działa również w przypadku systemu Windows Server pod kątem harmonogramu w węźle systemu Windows Server.

Utwórz rozwidlenie następującego repozytorium GitHub dla przykładowej aplikacji — [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Aby utworzyć rozwidlenie repozytorium na koncie usługi GitHub, wybierz przycisk **Rozwidlenie** w prawym górnym rogu.

Sklonuj rozwidlenie do systemu deweloperskiego. Upewnij się, że używasz adresu URL rozwidlenia podczas klonowania tego repozytorium:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Przejdź do katalogu sklonowanego rozwidlenia:

```console
cd azure-voting-app-redis
```

Aby utworzyć obrazy kontenerów, które są konieczne dla przykładowej aplikacji, użyj pliku *Docker-Zredaguj. YAML* z `docker-compose`:

```console
docker-compose up -d
```

Wymagane są ściągane obrazy podstawowe oraz skompilowane kontenery aplikacji. Następnie można użyć polecenia [Docker images](https://docs.docker.com/engine/reference/commandline/images/) , aby wyświetlić utworzony obraz. Zostały pobrane lub utworzone trzy obrazy. Obraz `azure-vote-front` zawiera aplikację i używa obrazu `nginx-flask` jako podstawy. Obraz `redis` jest używany do uruchamiania wystąpienia Redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Zanim będzie można wypchnąć obraz kontenera z systemem *Azure do głosu* z ACR, Pobierz serwer logowania ACR za pomocą polecenia [AZ ACR list](/cli/azure/acr#az-acr-list) . Poniższy przykład pobiera adres serwera logowania ACR dla rejestru w grupie zasobów o nazwie Moja *resourceName*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Użyj polecenia [Docker tag](https://docs.docker.com/engine/reference/commandline/tag/) , aby oznaczyć obraz nazwą serwera logowania ACR i numerem wersji `v1`. Podaj własną nazwę `<acrLoginServer>` uzyskaną w poprzednim kroku:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Na koniec wypchnij obraz z *przodu platformy Azure* do rejestru ACR. Ponownie Zastąp `<acrLoginServer>` nazwą serwera logowania własnego rejestru ACR, na przykład `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Wdróż przykładową aplikację w usłudze AKS

Aby wdrożyć przykładową aplikację w klastrze AKS, można użyć pliku manifestu Kubernetes w katalogu głównym repozytorium repozytorium głosowania platformy Azure. Otwórz plik manifestu *Azure-głosowa-All-in-in-in-one-Redis. YAML* z edytorem, takim jak `vi`. Zastąp wartość `microsoft` nazwą serwera logowania usługi ACR. Ta wartość jest dostępna w wierszu **47** pliku manifestu:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Następnie użyj polecenia [polecenia kubectl Apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) , aby wdrożyć aplikację w klastrze AKS:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Zostanie utworzona usługa równoważenia obciążenia Kubernetes w celu udostępnienia aplikacji w Internecie. Ten proces może potrwać kilka minut. Aby monitorować postęp wdrożenia modułu równoważenia obciążenia, użyj polecenia [polecenia kubectl Get Service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) z argumentem `--watch`. Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na *adres IP*, zatrzymaj proces śledzenia narzędzia kubectl za pomocą polecenia `Control + C`.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Aby wyświetlić aplikację w działaniu, Otwórz przeglądarkę internetową na zewnętrzny adres IP usługi. Zostanie wyświetlona aplikacja do głosowania platformy Azure, jak pokazano w następującym przykładzie:

![Przykładowa aplikacja do głosowania na platformie Azure działająca w AKS](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Wdrażanie Jenkins na maszynie wirtualnej platformy Azure

Aby szybko wdrożyć Jenkins do użycia w tym artykule, można użyć następującego skryptu do wdrożenia maszyny wirtualnej platformy Azure, skonfigurowania dostępu do sieci i wykonania podstawowej instalacji usługi Jenkins. W przypadku uwierzytelniania między Jenkins i klastrem AKS skrypt kopiuje plik konfiguracji Kubernetes z systemu deweloperskiego do systemu Jenkins.

> [!WARNING]
> Ten przykładowy skrypt służy do pokazania, aby szybko zainicjować środowisko Jenkins, które działa na maszynie wirtualnej platformy Azure. Używa rozszerzenia niestandardowego skryptu platformy Azure, aby skonfigurować maszynę wirtualną, a następnie wyświetlić wymagane poświadczenia. Wartość *~/.Kube/config* jest kopiowana do maszyny wirtualnej Jenkins.

Uruchom następujące polecenia, aby pobrać i uruchomić skrypt. Przed uruchomieniem [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)należy przejrzeć jego zawartość.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Utworzenie maszyny wirtualnej i wdrożenie wymaganych składników dla platformy Docker i Jenkins może potrwać kilka minut. Po zakończeniu skryptu generuje on adres serwera Jenkins i klucz do odblokowania pulpitu nawigacyjnego, jak pokazano w następujących przykładowych danych wyjściowych:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Otwórz przeglądarkę internetową, aby wyświetlić adres URL, a następnie wprowadź klucz odblokowywania. Postępuj zgodnie z monitami wyświetlanymi na ekranie, aby ukończyć konfigurację Jenkins:

- Wybierz pozycję **Zainstaluj sugerowane wtyczki**
- Utwórz pierwszego użytkownika administratora. Wprowadź nazwę użytkownika, na przykład *azureuser*, a następnie podaj swoje bezpieczne hasło. Na koniec wpisz pełną nazwę i adres e-mail.
- Wybierz pozycję **Zapisz i zakończ**.
- Gdy narzędzie Jenkins będzie gotowe, wybierz pozycję **Rozpocznij korzystanie z narzędzia Jenkins**.
    - Jeśli po rozpoczęciu korzystania z narzędzia Jenkins w przeglądarce internetowej zostanie wyświetlona pusta strona, uruchom ponownie usługę Jenkins. Aby ponownie uruchomić usługę, SSH z publicznym adresem IP wystąpienia usługi Jenkins i wpisz `sudo service jenkins restart`. Po ponownym uruchomieniu usługi Odśwież przeglądarkę internetową.
- Zaloguj się do Jenkins przy użyciu nazwy użytkownika i hasła utworzonego w procesie instalacji.

## <a name="create-a-jenkins-environment-variable"></a>Utwórz zmienną środowiskową Jenkins

Zmienna środowiskowa Jenkins jest używana do przechowywania nazwy serwera logowania ACR. Ta zmienna jest przywoływana podczas zadania kompilacji Jenkins. Aby utworzyć tę zmienną środowiskową, wykonaj następujące czynności:

- Po lewej stronie portalu Jenkins wybierz pozycję **Zarządzaj Jenkins** > **Skonfiguruj system**
- W obszarze **Właściwości globalne**wybierz pozycję **zmienne środowiskowe**. Dodaj zmienną o nazwie `ACR_LOGINSERVER` i wartość serwera logowania ACR.

    ![Zmienne środowiskowe Jenkins](media/jenkins-continuous-deployment/env-variables.png)

- Po zakończeniu kliknij pozycję **Zapisz** w dolnej części strony konfiguracji Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Utwórz poświadczenie Jenkins dla ACR

Aby umożliwić programowi Jenkins kompilację i wypychanie zaktualizowanych obrazów kontenerów do ACR, należy określić poświadczenia dla ACR. Za pomocą tego uwierzytelniania można Azure Active Directory jednostki usługi. W wymaganiach wstępnych należy skonfigurować jednostkę usługi dla klastra AKS z uprawnieniami *czytelnika* do rejestru ACR. Te uprawnienia umożliwiają klastrowi AKS *ściąganie* obrazów z rejestru ACR. W procesie ciągłej integracji/ciągłego wdrażania Jenkins kompiluje nowe obrazy kontenerów na podstawie aktualizacji aplikacji i muszą następnie *wypchnąć* te obrazy do rejestru ACR. W przypadku separacji ról i uprawnień skonfiguruj nazwę główną usługi dla Jenkins z uprawnieniami *współautora* do rejestru ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Utwórz nazwę główną usługi dla Jenkins, aby użyć ACR

Najpierw Utwórz nazwę główną usługi przy użyciu polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) :

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

Zanotuj *identyfikator appid* i *hasło* podane w danych wyjściowych. Te wartości są używane w poniższych krokach w celu skonfigurowania zasobu Credential w Jenkins.

Pobierz identyfikator zasobu rejestru ACR za pomocą polecenia [AZ ACR show](/cli/azure/acr#az-acr-show) i Zapisz go jako zmienną. Podaj nazwę grupy zasobów i nazwę ACR:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Teraz Utwórz przypisanie roli, aby przypisać uprawnienia *współautora* jednostki usługi do rejestru ACR. W poniższym przykładzie Podaj swój własny *identyfikator appid* przedstawiony w danych wyjściowych poprzedniego polecenia, aby utworzyć jednostkę usługi:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Utwórz zasób poświadczeń w usłudze Jenkins dla jednostki usługi ACR

Przy przypisaniu roli utworzonego na platformie Azure, teraz przechowuj poświadczenia usługi ACR w obiekcie poświadczeń Jenkins. Te poświadczenia są przywoływane podczas zadania kompilacji Jenkins.

Po lewej stronie portalu Jenkins kliknij pozycję **poświadczenia** > **Jenkins** > **poświadczenia globalne (bez ograniczeń)**  > **Dodaj poświadczenia**

Upewnij się, że typ poświadczeń to **username z hasłem** i wprowadź następujące elementy:

- **Username** — *Identyfikator aplikacji* jednostki usługi utworzony na potrzeby uwierzytelniania przy użyciu rejestru ACR.
- **Password** — *hasło* nazwy głównej usługi utworzonej na potrzeby uwierzytelniania przy użyciu rejestru ACR.
- **Identyfikator** — identyfikator poświadczeń, taki jak *ACR — poświadczenia*

Po zakończeniu formularz poświadczeń wygląda podobnie do poniższego przykładu:

![Utwórz obiekt poświadczeń Jenkins z informacjami o jednostce usługi](media/jenkins-continuous-deployment/acr-credentials.png)

Kliknij przycisk **OK** i wróć do portalu Jenkins.

## <a name="create-a-jenkins-project"></a>Tworzenie projektu Jenkins

Na stronie głównej portalu Jenkins wybierz pozycję **nowy element** po lewej stronie:

1. Wprowadź *zagłosuj na platformę Azure* jako nazwę zadania. Wybierz **projekt Freestyle**, a następnie wybierz przycisk **OK** .
1. W sekcji **Ogólne** wybierz **projekt GitHub** i wprowadź adres URL repozytorium rozwidlenia, taki jak *https:\//GitHub.com/\<Twoje konto w serwisie GitHub\>/Azure-Voting-App-Redis*
1. W sekcji **Zarządzanie kodem źródłowym** wybierz pozycję **git**i wprowadź adres URL repozytorium *. git* , na przykład *https:\//GitHub.com/\<Twoje konto w serwisie GitHub\>/Azure-Voting-App-Redis.git*

1. W sekcji **Kompiluj wyzwalacze** wybierz pozycję **wyzwalacz punktu zaczepienia GitHub na potrzeby sondowania GITscm**
1. W obszarze **środowisko kompilacji**wybierz pozycję **Użyj wpisów lub plików tajnych** .
1. W obszarze **powiązania**wybierz pozycję **Dodaj** > **Nazwa użytkownika i hasło (oddzielone)**
   - Wprowadź `ACR_ID` dla **zmiennej username**i `ACR_PASSWORD` dla **zmiennej Password**

     ![Powiązania Jenkins](media/jenkins-continuous-deployment/bindings.png)

1. Wybierz, aby dodać **krok kompilacji** typu **Wykonaj powłokę** i użyj poniższego tekstu. Ten skrypt kompiluje nowy obraz kontenera i wypchnij go do rejestru ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Dodaj kolejny **krok kompilacji** typu **Wykonaj powłokę** i użyj poniższego tekstu. Ten skrypt aktualizuje wdrożenie aplikacji w AKS z nowym obrazem kontenera z ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Po zakończeniu kliknij przycisk **Zapisz**.

## <a name="test-the-jenkins-build"></a>Testowanie kompilacji Jenkins

Przed automatyzacją zadania na podstawie zatwierdzeń usługi GitHub należy najpierw ręcznie przetestować kompilację Jenkins. Ta ręczna kompilacja sprawdza, czy zadanie zostało prawidłowo skonfigurowane, prawidłowy plik uwierzytelniania Kubernetes oraz że uwierzytelnianie przy użyciu ACR działa.

W menu po lewej stronie projektu wybierz opcję **Kompiluj teraz**.

![Kompilacja testu Jenkins](media/jenkins-continuous-deployment/test-build.png)

Pierwsza kompilacja trwa minutę lub dwie, ponieważ warstwy obrazu platformy Docker są ściągane do serwera Jenkins. Kolejne kompilacje mogą używać warstw obrazu w pamięci podręcznej w celu skrócenia czasu kompilacji.

Podczas procesu kompilacji repozytorium GitHub jest klonowane do serwera kompilacji Jenkins. Nowy obraz kontenera jest zbudowany i wypychany do rejestru ACR. Na koniec aplikacja do głosowania platformy Azure uruchomiona w klastrze AKS zostaje zaktualizowana tak, aby korzystała z nowego obrazu. Ponieważ nie wprowadzono żadnych zmian w kodzie aplikacji, aplikacja nie zostanie zmieniona w przypadku wyświetlenia przykładowej aplikacji w przeglądarce sieci Web.

Po zakończeniu zadania kompilacji kliknij pozycję **kompilacja #1** w obszarze Historia kompilacji. Wybierz pozycję **dane wyjściowe konsoli** i Wyświetl dane wyjściowe procesu kompilacji. Ostatni wiersz powinien wskazywać pomyślne skompilowanie.

## <a name="create-a-github-webhook"></a>Tworzenie elementu webhook usługi GitHub

Po pomyślnym ukończeniu ręcznego kompilowania można teraz zintegrować witrynę GitHub z kompilacją Jenkins. Element webhook może służyć do uruchamiania zadania kompilacji Jenkins za każdym razem, gdy w serwisie GitHub zostanie wykonane zatwierdzenie kodu. Aby utworzyć element webhook usługi GitHub, wykonaj następujące czynności:

1. Przejdź do repozytorium GitHub z rozwidleniem w przeglądarce internetowej.
1. Wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Elementy webhook** po lewej stronie.
1. Wybierz, aby **dodać element webhook**. W polu *adres URL ładunku*wprowadź `http://<publicIp:8080>/github-webhook/`, gdzie `<publicIp>` jest adresem IP serwera Jenkins. Pamiętaj, aby uwzględnić końcowe/. Pozostaw inne wartości domyślne dla typu zawartości i Wyzwalaj zdarzenia *wypychania* .
1. Wybierz pozycję **Dodaj element webhook**.

    ![Tworzenie elementu webhook usługi GitHub dla usługi Jenkins](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Przetestuj kompletny potok ciągłej integracji/ciągłego dostarczania

Teraz można testować cały potok ciągłej integracji/ciągłego wdrażania. Po wypchnięciu zatwierdzenia kodu do usługi GitHub wykonywane są następujące czynności:

1. Element webhook usługi GitHub osiągnie wartość Jenkins.
1. Jenkins uruchamia zadanie kompilacji i pobiera najnowsze zatwierdzenie kodu z usługi GitHub.
1. Kompilacja platformy Docker została uruchomiona przy użyciu zaktualizowanego kodu, a nowy obraz kontenera jest oznaczony najnowszym numerem kompilacji.
1. Ten nowy obraz kontenera jest wypychany do Azure Container Registry.
1. Twoja aplikacja została wdrożona w usłudze Azure Kubernetes Update przy użyciu najnowszego obrazu kontenera z rejestru Azure Container Registry.

Na komputerze deweloperskim Otwórz sklonowaną aplikację z edytorem kodu. W katalogu */Azure-vote/Azure-vote* Otwórz plik o nazwie **config_file. cfg**. Zaktualizuj wartości głosu w tym pliku do czegoś innego niż koty i psy, jak pokazano w następującym przykładzie:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Po zaktualizowaniu Zapisz plik, Zatwierdź zmiany i wypchnij je do rozwidlenia repozytorium GitHub. Element webhook usługi GitHub wyzwala nowe zadanie kompilacji w programie Jenkins. Na pulpicie nawigacyjnym sieci Web Jenkins Monitoruj proces kompilacji. Ściąganie najnowszego kodu trwa kilka sekund, Utwórz i wypchnij zaktualizowany obraz i Wdróż zaktualizowaną aplikację w AKS.

Po zakończeniu kompilacji Odśwież przeglądarkę sieci Web przykładowej aplikacji do głosowania platformy Azure. Zostaną wyświetlone zmiany, jak pokazano w następującym przykładzie:

![Przykładowy głos na platformie Azure w AKS zaktualizowany przez zadanie kompilacji Jenkins](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Usługa Jenkins na platformie Azure](/azure/jenkins)
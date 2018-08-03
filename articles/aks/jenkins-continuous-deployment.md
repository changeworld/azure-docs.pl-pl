---
title: Ciągłe wdrażanie usługi Jenkins za pomocą rozwiązania Kubernetes w usłudze Azure Kubernetes Service
description: Jak zautomatyzować proces ciągłego wdrażania przy użyciu narzędzia Jenkins, wdrażanie i uaktualnianie konteneryzowanych aplikacji w rozwiązaniu Kubernetes w usłudze Azure Kubernetes Service
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a1a6799bc049fea829f8e32d12705e26e3a41dc0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425762"
---
# <a name="continuous-deployment-with-jenkins-and-azure-kubernetes-service"></a>Ciągłe wdrażanie za pomocą systemu Jenkins i usługi Azure Kubernetes Service

W tym dokumencie przedstawiono sposób konfigurowania podstawowych ciągłe wdrażanie przepływu pracy, między Jenkins oraz klastra Azure Kubernetes Service (AKS).

Przykładowy przepływ pracy obejmuje następujące kroki:

> [!div class="checklist"]
> * Wdróż aplikację głosowania na platformie Azure w klastrze Kubernetes.
> * Zaktualizuj kod aplikacji głosowania na platformie Azure i wypychania do repozytorium GitHub, który uruchamia proces ciągłego wdrażania.
> * Jenkins klonuje repozytorium i tworzy nowy obraz kontenera ze zaktualizowanym kodem.
> * Ten obraz zostanie przypisany do usługi Azure Container Registry (ACR).
> * Nowy obraz kontenera jest aktualizowana aplikacji uruchomionych w klastrze AKS.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy jest niezbędna, aby wykonać kroki opisane w tym artykule.

- Podstawową wiedzę na temat usługi Kubernetes, Git, ciągłej integracji/ciągłego Dostarczania i Azure Container Registry (ACR).
- [Klastra Azure Kubernetes Service (AKS)] [ aks-quickstart] i [skonfigurowane poświadczenia usługi AKS] [ aks-credentials] w systemie deweloperskim.
- [Rejestru Azure Container Registry (ACR)][acr-quickstart], nazwę serwera logowania usługi ACR i [poświadczenia rejestru Azure container Registry] [ acr-authentication] z dostępem do ściągania i wypychania.
- Wiersza polecenia platformy Azure zainstalowany w systemie deweloperskim.
- Platformy docker zainstalowany w systemie deweloperskim.
- Konto usługi GitHub [osobisty token dostępu GitHub][git-access-token]i zainstalowanego w systemie deweloperskim klienta Git.

## <a name="prepare-application"></a>Przygotowywanie aplikacji

Aplikacja do głosowania platformy Azure w całym dokumencie zawiera interfejs sieci web hostowanych w zasobników przynajmniej jednego i drugiego zasobnik obsługującego magazyn Redis do przechowywania danych tymczasowych.

Przed zbudowaniem narzędzia Jenkins / Integracja usługi AKS, przygotowywania i wdrażania aplikacji Azure vote do klastra usługi AKS. Myśl o tym, jak wersja 1 aplikacji.

Rozwidlenia następujące repozytorium GitHub.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

Po utworzeniu rozwidlenia, sklonować ten projekt systemu dla deweloperów. Upewnij się, że używasz adresu URL Twojego rozwidlenia podczas klonowania tego repozytorium.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Zmień katalogi, aby pracować w sklonowanym katalogu.

```bash
cd azure-voting-app-redis
```

Uruchom `docker-compose.yaml` pliku w celu utworzenia `azure-vote-front` obrazu kontenera i uruchamiania aplikacji.

```bash
docker-compose up -d
```

Po zakończeniu użyj [obrazów platformy docker] [ docker-images] polecenie, aby wyświetlić utworzony obraz.

Zwróć uwagę, że zostały pobrane lub utworzone trzy obrazy. Obraz `azure-vote-front` zawiera aplikację i używa obrazu `nginx-flask` jako podstawy. Obraz `redis` jest używany do uruchomienia wystąpienia usługi Redis.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Uzyskaj adres serwera logowania usługi ACR za pomocą [az acr list] [ az-acr-list] polecenia. Upewnij się, można zaktualizować nazwy grupy zasobów z grupą zasobów hostingu rejestru usługi ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Użyj [docker tag] [ docker-tag] polecenia w celu otagowania obrazu z nazwą serwera logowania i numer wersji `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Zaktualizuj wartość serwera logowania usługi ACR za pomocą nazwy serwera logowania rejestru Azure container Registry i wypychania `azure-vote-front` obrazu do rejestru.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Wdrażanie aplikacji w usłudze Kubernetes

Może to być plik manifestu rozwiązania Kubernetes znalezione w folderze głównym repozytorium głosowania na platformie Azure i może służyć do wdrożenia aplikacji na klastrze platformy Kubernetes.

Najpierw należy zaktualizować **azure-vote-all-in-one-redis.yaml** pliku manifestu z lokalizacji rejestru usługi ACR. Otwórz plik za pomocą dowolnego edytora tekstów, a następnie zastąp `microsoft` nazwą serwera logowania usługi ACR. Tę wartość można znaleźć w wierszu **47** pliku manifestu.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Następnie użyj [zastosować kubectl] [ kubectl-apply] polecenie, aby uruchomić aplikację. To polecenie analizuje plik manifestu i tworzy zdefiniowane obiekty usługi Kubernetes.

```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A [usługi Kubernetes] [ kubernetes-service] utworzono w celu udostępnienia aplikacji w Internecie. Ten proces może potrwać kilka minut.

Aby monitorować postęp, użyj polecenia [kubectl get-service][kubectl-get] z argumentem `--watch`.

```bash
kubectl get service azure-vote-front --watch
```

Początkowo adres *EXTERNAL-IP* dla usługi *azure-vote-front* jest wyświetlany jako *oczekujący*.

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Po zmianie adresu *EXTERNAL-IP* z *oczekującego* na *adres IP*, zatrzymaj proces śledzenia narzędzia kubectl za pomocą polecenia `control+c`.

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

Aby wyświetlić aplikację, przejdź do zewnętrznego adresu IP.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>Wdrażanie usługi Jenkins na maszynie Wirtualnej

Skrypt został wstępnie utworzony, aby wdrożyć maszynę wirtualną, konfigurowanie dostępu do sieci i ukończyć podstawową instalację usługi jenkins. Ponadto skrypt kopiuje plik konfiguracji Kubernetes w systemie deweloperskim do systemu Jenkins. Ten plik jest używany do uwierzytelniania między Jenkins i klastra AKS.

Uruchom następujące polecenia, aby pobrać i uruchomić skrypt. Poniższy adres URL może również służyć do Przejrzyj zawartość skryptu.

> [!WARNING]
> Ten przykładowy skrypt jest dla celów demonstracyjnych szybko aprowizować środowiska narzędzia Jenkins, które jest uruchamiane na Maszynie wirtualnej platformy Azure. Aby skonfigurować Maszynę wirtualną, a następnie Wyświetl wymagane poświadczenia używa rozszerzenia niestandardowego skryptu platformy Azure. Twoje *~/.kube/config* jest kopiowana do maszyny Wirtualnej usługi Jenkins.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Po zakończeniu skrypt generuje adres dla serwera Jenkins jako również klucz odblokowywanie usługi Jenkins. Przejdź do adresu URL, wprowadź klucz i zgodnie z monitami wyświetlanymi na ekranie w celu ukończenia konfiguracji usługi Jenkins.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Zmienne środowiskowe systemu Jenkins

Zmienna środowiskowa Jenkins jest używane do przechowywania nazwy serwera logowania usługi Azure Container Registry (ACR). Ta zmienna odwołuje się podczas wykonywania zadania ciągłego wdrażania narzędzia Jenkins.

Znajduje się w portalu administracyjnym usługi Jenkins kliknij **Zarządzaj serwerem Jenkins** > **Konfiguruj System**.

W obszarze **globalne właściwości**, wybierz opcję **zmienne środowiskowe**i Dodaj zmienną o nazwie `ACR_LOGINSERVER` i wartość serwera logowania usługi ACR.

![Zmienne środowiskowe systemu Jenkins](media/aks-jenkins/env-variables.png)

Po zakończeniu kliknij przycisk **Zapisz** na stronie Konfiguracja serwera Jenkins.

## <a name="jenkins-credentials"></a>Poświadczenia usługi Jenkins

Teraz można zapisać poświadczenia rejestru Azure container Registry w obiekt poświadczeń usługi Jenkins. Te poświadczenia są przywoływane podczas zadania kompilacji usługi Jenkins.

Wstecz w portalu administracyjnym usługi Jenkins kliknij **poświadczenia** > **Jenkins** > **poświadczeń globalnych (nieograniczony)**  >   **Dodaj poświadczenia**.

Upewnij się, że typ poświadczeń **nazwy użytkownika z hasłem** i wprowadź następujące elementy:

- **Nazwa użytkownika** — identyfikator podmiotu zabezpieczeń użytkowania usługi uwierzytelniania za pomocą rejestru usługi ACR.
- **Hasło** — wpis tajny klienta jednostki usługi użytek uwierzytelniania za pomocą rejestru usługi ACR.
- **Identyfikator** — takich jak poświadczenia identyfikatora `acr-credentials`.

Po zakończeniu formularza poświadczenia powinna wyglądać następująco:

![Poświadczenia rejestru Azure container Registry](media/aks-jenkins/acr-credentials.png)

Kliknij przycisk **OK** i powróć do portalu administratora usługi Jenkins.

## <a name="create-jenkins-project"></a>Utwórz projekt narzędzia Jenkins

W portalu administracyjnym usługi Jenkins kliknij **nowy element**.

Nazwij projekt, na przykład `azure-vote`, wybierz opcję **projektu dowolne**i kliknij przycisk **OK**.

![Projekt Jenkins](media/aks-jenkins/jenkins-project.png)

W obszarze **ogólne**, wybierz opcję **projektu GitHub** i wprowadź adres URL Twojego rozwidlenia projektu GitHub głosowania na platformie Azure.

![Projektu GitHub](media/aks-jenkins/github-project.png)

W obszarze **zarządzania kodem źródłowym**, wybierz opcję **Git**, wprowadź adres URL Twojego rozwidlenia repozytorium Azure Vote w witrynie GitHub.

Za pomocą poświadczeń kliknij i **Dodaj** > **Jenkins**. W obszarze **rodzaj**, wybierz opcję **tekst wpisu tajnego** i wprowadź swoje [osobisty token dostępu GitHub] [ git-access-token] jako klucz tajny.

Wybierz **Dodaj** po zakończeniu.

![Poświadczenia usługi GitHub](media/aks-jenkins/github-creds.png)

W obszarze **kompilowanie wyzwalaczy**, wybierz opcję **wyzwalacz punktu zaczepienia GitHub na potrzeby sondowania GITScm**.

![Wyzwalacze kompilacji usługi Jenkins](media/aks-jenkins/build-triggers.png)

W obszarze **środowisko budowania środowiska**, wybierz opcję **za pomocą klucza tajnego teksty lub pliki**.

![Środowiska narzędzia Jenkins](media/aks-jenkins/build-environment.png)

W obszarze **powiązania**, wybierz opcję **Dodaj** > **nazwy użytkownika i hasło (oddzielone)**.

Wprowadź `ACR_ID` dla **zmienna nazwy użytkownika**, i `ACR_PASSWORD` dla **zmienna hasła**.

![Powiązania usługi Jenkins](media/aks-jenkins/bindings.png)

Dodaj **krok kompilacji** typu **wykonaj powłokę** i skorzystaj z poniższego tekstu. Ten skrypt tworzy nowy obraz kontenera i wypychany do rejestru usługi ACR.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Dodaj kolejną **krok kompilacji** typu **wykonaj powłokę** i skorzystaj z poniższego tekstu. Ten skrypt aktualizacji wdrożenia rozwiązania Kubernetes.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

Po zakończeniu kliknij przycisk **Zapisz**.

## <a name="test-the-jenkins-build"></a>Testowanie kompilacji usługi Jenkins

Zanim przejdziesz dalej, przetestuj kompilację narzędzia Jenkins. Sprawdza to, że zadanie kompilacji został prawidłowo skonfigurowany, prawidłowego pliku uwierzytelniania Kubernetes znajduje się w miejscu, a podano prawidłowe poświadczenia rejestru Azure container Registry.

Kliknij przycisk **Kompiluj teraz** w menu po lewej stronie projektu.

![Jenkins testów kompilacji](media/aks-jenkins/test-build.png)

W trakcie tego procesu repozytorium GitHub został sklonowany serwer kompilacji usługi Jenkins. Nowy obraz kontenera jest utworzone i wypchnięte do rejestru usługi ACR. Na koniec uruchomionego w klastrze AKS aplikacja do głosowania platformy Azure jest aktualizowana, aby korzystała z nowego obrazu. Ponieważ wprowadzono żadnych zmian kodu aplikacji, aplikacja nie jest zmieniany.

Po zakończeniu procesu kliknij **kompilacji #1** w obszarze Historia kompilacji i wybierz **dane wyjściowe konsoli** aby zobaczyć wszystkie dane wyjściowe z procesu kompilacji. Ostatni wiersz powinien wskazywać pomyślnej kompilacji.

## <a name="create-github-webhook"></a>Tworzenie elementu webhook GitHub

Następnie należy dołączyć repozytorium aplikacji na serwerze kompilacji usługi Jenkins, dzięki czemu na każdego zatwierdzenia nowych kompilacja zostaje wyzwolona.

1. Przejdź do rozwidlonego repozytorium GitHub.
2. Wybierz **ustawienia**, a następnie wybierz **elementów Webhook** po lewej stronie.
3. Możliwość **Dodaj element webhook**. Aby uzyskać *adres URL ładunku*, wprowadź `http://<publicIp:8080>/github-webhook/` gdzie `publicIp` jest adres IP serwera Jenkins. Upewnij się, że wpisano /. Pozostaw inne wartości domyślne dla typu zawartości i wyzwalacza w *wypychania* zdarzenia.
4. Wybierz **Dodaj element webhook**.

    ![Element webhook GitHub](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Testowanie procesu ciągłej integracji/ciągłego Dostarczania typu end to end

Na komputerze deweloperskim otwierają sklonowanej aplikacji za pomocą edytora kodu.

W obszarze **/azure-vote/azure-vote** katalogu, Znajdź plik o nazwie **config_file.cfg**. Zaktualizuj wartości głosu w tym pliku na coś, innym niż koty i psy.

Poniższy przykład pokazuje i zaktualizować **config_file.cfg** pliku.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Po zakończeniu zapisz plik, Zatwierdź zmiany i wypchnąć je do swojego rozwidlenia repozytorium GitHub... Po zakończeniu zatwierdzenie, element webhook usługi GitHub wyzwala nowej kompilacji narzędzia Jenkins, który aktualizuje obrazu kontenera i wdrożeniu usługi AKS. Monitorowanie procesu kompilacji w konsoli administracyjnej usługi Jenkins.

Po ukończeniu kompilacji Przejdź ponownie do punktu końcowego aplikacji można obserwować zmiany.

![Zaktualizowano głosowania na platformie Azure](media/aks-jenkins/azure-vote-updated-safari.png)

W tym momencie proces proste ciągłe Wdrażanie zostało ukończone. Kroki i konfiguracji przedstawionych w tym przykładzie może służyć do rozbudowy automatyzacji kompilacji ciągłej bardziej niezawodna i gotowa do użycia w środowisku produkcyjnym.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
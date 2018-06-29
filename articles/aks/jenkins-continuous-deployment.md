---
title: Ciągłe wdrażanie Wpięć z Kubernetes w usłudze Azure Kubernetes
description: Jak zautomatyzować proces ciągłego wdrażania z Wpięć do wdrożenia i uaktualnienia konteneryzowanych aplikacji na Kubernetes w usłudze Azure Kubernetes
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 84baf01ce6eeed8dc569d7a856189aefba788126
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096479"
---
# <a name="continuous-deployment-with-jenkins-and-azure-kubernetes-service"></a>Ciągłe wdrażanie w środowisku z Wpięć usługi Kubernetes Azure

Ten dokument pokazano, jak skonfigurować ciągłe wdrażanie podstawowych przepływ pracy między Wpięć a klastrem usługi Kubernetes Azure (AKS).

Przykładowy przepływ pracy obejmuje następujące kroki:

> [!div class="checklist"]
> * Wdróż aplikację Azure głos do klastra Kubernetes.
> * Zaktualizuj kod aplikacji Azure głosu i wypychania do repozytorium GitHub, który uruchamia proces ciągłego wdrażania.
> * Wpięć klonów repozytorium i tworzy nowy obraz kontenera z zaktualizowanego kodu.
> * Ten obraz jest przypisany do rejestru kontenera platformy Azure (ACR).
> * Aplikacja była uruchomiona w klastrze AKS został zaktualizowany o nowy obraz kontenera.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule potrzebne są następujące zasoby.

- Podstawową wiedzę na temat Kubernetes, Git CI/CD i rejestru kontenera platformy Azure (ACR).
- [Klastra usługi Kubernetes Azure (AKS)] [ aks-quickstart] i [skonfigurowane poświadczenia AKS] [ aks-credentials] w systemie deweloperskim.
- [Rejestru rejestru kontenera platformy Azure (ACR)][acr-quickstart], nazwa ACR logowania serwera i [poświadczenia ACR] [ acr-authentication] z dostępem do wypychania i ściągania.
- Azure CLI zainstalowane w systemie deweloperskim.
- Docker zainstalowane w systemie deweloperskim.
- Konto GitHub [GitHub osobisty token dostępu][git-access-token]i zainstalowane w systemie deweloperskim klienta Git.

## <a name="prepare-application"></a>Przygotowanie aplikacji

Aplikacja Azure głos w całym dokumencie zawiera interfejs sieci web hostowanych w stanowiskami co najmniej jednego i drugiego pod hosting Redis do przechowywania danych tymczasowych.

Przed zbudowaniem Wpięć / integracji AKS przygotować i wdrożyć aplikację Azure głos AKS klastra. Traktować jako wersja jednego z aplikacji.

Rozwidlenia następujące repozytorium GitHub.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

Po utworzeniu rozwidlenia ją sklonować systemie deweloperskim. Upewnij się, że używasz adresu URL rozwidlenia, w przypadku klonowania tym repozytorium.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Zmień katalogi, aby pracować w sklonowanym katalogu.

```bash
cd azure-voting-app-redis
```

Uruchom `docker-compose.yaml` pliku w celu utworzenia `azure-vote-front` obraz kontenera i uruchomienia aplikacji.

```bash
docker-compose up -d
```

Po zakończeniu użyj [obrazy usługi docker] [ docker-images] polecenie, aby wyświetlić utworzony obraz.

Zwróć uwagę, że zostały pobrane lub utworzone trzy obrazy. Obraz `azure-vote-front` zawiera aplikację i używa obrazu `nginx-flask` jako podstawy. Obraz `redis` jest używany do uruchomienia wystąpienia usługi Redis.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Pobierz ACR serwera logowania za pomocą [listy acr az] [ az-acr-list] polecenia. Upewnij się, że aktualizacja Nazwa grupy zasobów z grupą zasobów hosting rejestru ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Użyj [docker tag] [ docker-tag] polecenie, aby tag obrazu o nazwę logowania serwera i numer wersji `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Zaktualizuj wartość ACR logowania serwera o nazwę serwera ACR logowania i wypychania `azure-vote-front` obrazu w rejestrze.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Wdrażanie aplikacji w usłudze Kubernetes

Kubernetes, może to być plik manifestu znalezione w katalogu głównym repozytorium Azure głosu i może służyć do wdrożenia aplikacji w klastrze Kubernetes.

Najpierw należy zaktualizować **azure-vote-all-in-one-redis.yaml** pliku manifestu z lokalizacji rejestru ACR. Otwórz plik w dowolnym edytorze tekstu i Zastąp `microsoft` z nazwą serwera ACR logowania. Tę wartość można znaleźć w wierszu **47** pliku manifestu.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Następnie użyj [zastosować kubectl] [ kubectl-apply] polecenie do uruchomienia aplikacji. To polecenie analizuje plik manifestu i tworzy zdefiniowane obiekty usługi Kubernetes.

```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A [usługi Kubernetes] [ kubernetes-service] utworzeniu do udostępnienia aplikacji w Internecie. Ten proces może potrwać kilka minut.

Aby monitorować postęp, użyj polecenia [kubectl get-service][kubectl-get] z argumentem `--watch`.

```bash
kubectl get service azure-vote-front --watch
```

Początkowo adres *EXTERNAL-IP* dla usługi *azure-vote-front* pojawia się jako *oczekujący*.

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Po zmianie adresu *EXTERNAL-IP* z *oczekującego* na *adres IP*, zatrzymaj proces śledzenia narzędzia kubectl za pomocą polecenia `control+c`.

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

Aby wyświetlić aplikację, przejdź do zewnętrznego adresu IP.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>Wdrażanie Wpięć do maszyny Wirtualnej

Skrypt został wstępnie utworzone wdrożyć maszynę wirtualną, skonfigurować dostęp do sieci i utworzyć podstawową instalację Wpięć. Ponadto skrypt kopiuje Kubernetes pliku konfiguracji z systemu dla deweloperów systemu Wpięć. Ten plik jest używany do uwierzytelniania między Wpięć i AKS klastra.

Uruchom następujące polecenia, aby pobrać i uruchom skrypt. Poniżej adres URL może również służyć do Przejrzyj zawartość skryptu.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Po ukończeniu działania skryptu generuje adres serwera Wpięć jako również klucz do odblokowywania Wpięć. Przejdź do adresu URL, wprowadź klucz, a po wyświetlanymi na ekranie w celu ukończenia konfiguracji Wpięć.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Zmienne środowiskowe Wpięć

Zmienna środowiskowa Wpięć służy do przechowywania nazwy serwera logowania rejestru kontenera platformy Azure (ACR). Ta zmienna odwołuje się podczas wykonywania zadania Wpięć ciągłego wdrażania.

Znajduje się w portalu administracyjnym Wpięć kliknij **Zarządzanie Wpięć** > **skonfigurować System**.

W obszarze **globalnych właściwości**, wybierz pozycję **zmiennych środowiskowych**i Dodaj zmienna o nazwie `ACR_LOGINSERVER` i wartość ACR logowania serwera.

![Zmienne środowiskowe Wpięć](media/aks-jenkins/env-variables.png)

Po zakończeniu kliknij przycisk **zapisać** na stronie konfiguracji Wpięć.

## <a name="jenkins-credentials"></a>Poświadczenia Wpięć

Teraz przechowywane poświadczenia ACR w obiekt Wpięć poświadczeń. Te poświadczenia są istnieją odwołania podczas Wpięć zadania kompilacji.

Ponownie w portalu administracyjnym Wpięć kliknij **poświadczenia** > **Wpięć** > **poświadczeń globalnych (bez ograniczeń)**  >   **Dodawanie poświadczeń**.

Upewnij się, że typ poświadczeń jest **nazwy użytkownika z hasłem** , a następnie wprowadź następujące elementy:

- **Nazwa użytkownika** -ID użyj główną usługi dla uwierzytelniania za pomocą rejestru ACR.
- **Hasło** — klucz tajny klienta użyj główną usługi dla uwierzytelniania za pomocą rejestru ACR.
- **Identyfikator** — takie jak poświadczeń identyfikator `acr-credentials`.

Po zakończeniu formularza poświadczenia powinien wyglądać podobnie do tego obrazu:

![Poświadczenia ACR](media/aks-jenkins/acr-credentials.png)

Kliknij przycisk **OK** i wróć do portalu administratora Wpięć.

## <a name="create-jenkins-project"></a>Utwórz projekt Wpięć

W portalu administracyjnym Wpięć kliknij **nowy element**.

Nazwę projektu, na przykład `azure-vote`, wybierz pozycję **projektu dowolne**i kliknij przycisk **OK**.

![Wpięć projektu](media/aks-jenkins/jenkins-project.png)

W obszarze **ogólne**, wybierz pozycję **projektu GitHub** , a następnie wprowadź adres URL do rozwidlenia projektu GitHub głos platformy Azure.

![Projekt GitHub](media/aks-jenkins/github-project.png)

W obszarze **zarządzania kodem źródłowym**, wybierz pozycję **Git**, wprowadź adres URL do rozwidlenia repozytorium GitHub głos Azure.

Za pomocą poświadczeń kliknij i **Dodaj** > **Wpięć**. W obszarze **rodzaj**, wybierz pozycję **tajny tekst** , a następnie wprowadź Twojej [GitHub osobisty token dostępu] [ git-access-token] jako klucz tajny.

Wybierz **Dodaj** po zakończeniu.

![Poświadczenia usługi GitHub](media/aks-jenkins/github-creds.png)

W obszarze **kompilacji wyzwalaczy**, wybierz pozycję **wyzwalacza haku GitHub dla sondowania GITScm**.

![Wpięć kompilacji wyzwalacze](media/aks-jenkins/build-triggers.png)

W obszarze **Build Environment**, wybierz pozycję **tajny teksty lub plików użyć**.

![Wpięć środowisko kompilacji](media/aks-jenkins/build-environment.png)

W obszarze **powiązania**, wybierz pozycję **Dodaj** > **nazwy użytkownika i hasła (oddzielone)**.

Wprowadź `ACR_ID` dla **zmienna nazwy użytkownika**, i `ACR_PASSWORD` dla **zmiennej hasła**.

![Powiązania Wpięć](media/aks-jenkins/bindings.png)

Dodaj **krok kompilacji** typu **wykonywania powłoki** i skorzystaj z poniższego tekstu. Ten skrypt tworzy nowy obraz kontenera i wypchnięcia jej do rejestru ACR.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Dodaj inny **krok kompilacji** typu **wykonywania powłoki** i skorzystaj z poniższego tekstu. Ten skrypt aktualizacji wdrożenia Kubernetes.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

Po ukończeniu kliknij przycisk **zapisać**.

## <a name="test-the-jenkins-build"></a>Testowanie kompilacji Wpięć

Przed kontynuowaniem należy przetestować Wpięć kompilacji. Taki tryb testowania weryfikuje, czy zadania kompilacji został prawidłowo skonfigurowany, właściwy plik uwierzytelniania Kubernetes znajduje się w miejscu i że dostarczono prawidłowe poświadczenia ACR.

Kliknij przycisk **kompilacji teraz** w menu po lewej stronie projektu.

![Wpięć testów kompilacji](media/aks-jenkins/test-build.png)

W trakcie tego procesu repozytorium GitHub został sklonowany z Wpięć serwerem kompilacji. Nowy obraz kontenera jest wbudowana i przypisany do rejestru ACR. Na koniec głos Azure aplikacja była uruchomiona w klastrze AKS zostało zaktualizowane do użycia nowego obrazu. Ponieważ żadne zmiany nie zostały wprowadzone do kodu aplikacji, aplikacja nie zostanie zmieniona.

Po zakończeniu procesu kliknij **kompilacji #1** w obszarze Historia kompilacji i wybierz **dane wyjściowe konsoli** aby zobaczyć wszystkie dane wyjściowe z procesu kompilacji. Ostatnim wierszu powinna wskazywać pomyślnego utworzenia kompilacji.

## <a name="create-github-webhook"></a>Tworzenie elementu webhook GitHub

Następnie podłącz repozytorium aplikacji do serwera kompilacji Wpięć tak, aby w dowolnym zatwierdzeniu nowej kompilacji zostanie wywołany.

1. Przejdź do rozwidlonych repozytorium GitHub.
2. Wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Integracje i usługi** po lewej stronie.
3. Wybierz **Dodaj usługę**, wprowadź `Jenkins (GitHub plugin)` w polu filtru, a następnie wybierz wtyczki.
4. Dla Wpięć utworzenie punktu zaczepienia adres URL, wprowadź `http://<publicIp:8080>/github-webhook/` gdzie `publicIp` to adres IP serwera Wpięć. Pamiętaj wpisać kreskę końcową /.
5. Wybierz opcję Dodaj usługi.

![Element webhook GitHub](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Testowanie procesu CI/CD pełnego

Na komputerze deweloperskim otwarcie Sklonowana aplikacja za pomocą edytora kodu.

W obszarze **/azure-vote/azure-vote** katalogu, Znajdź plik o nazwie **config_file.cfg**. Zaktualizuj wartości głosowanie w tym pliku inny niż kotów i psów.

Poniższy przykład przedstawia i zaktualizować **config_file.cfg** pliku.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Po zakończeniu zapisz plik, Zatwierdź zmiany i wypychania do rozwidlenia repozytorium GitHub. Po zakończeniu zatwierdzenia element webhook GitHub wyzwala nową kompilację Wpięć, która aktualizuje kontener obrazu i wdrożenie AKS. Monitorowanie procesu kompilacji w konsoli administracyjnej Wpięć.

Po ukończeniu kompilacji, Wyszukaj ponownie punkt końcowy aplikacji można obserwować zmiany.

![Azure głos zaktualizowane](media/aks-jenkins/azure-vote-updated-safari.png)

W tym momencie proces proste ciągłe Wdrażanie zostało zakończone. Kroki i konfiguracji przedstawionych w tym przykładzie można budować automatyzacji kompilacji ciągłej bardziej niezawodny i gotowe do produkcji.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az_acr_list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
---
title: Rozwój zespołu na Kubernetes
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: Ten przewodnik Szybki start pokazuje, jak wykonać programowy kubernetes zespołu za pomocą kontenerów i mikrousług za pomocą usługi Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 0fe177db420913e5d68807dd803df791653c0914
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244943"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Szybki start: tworzenie zespołu w usłudze Kubernetes — usługi Azure Dev Spaces

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces w zarządzanym klastrze kubernetes na platformie Azure.
- Wdrażanie dużej aplikacji z wieloma mikrousługami w przestrzeni deweloperów.
- Przetestuj pojedyncze mikrousługi w izolowanej przestrzeni deweloperów w kontekście pełnej aplikacji.

![Tworzenie zespołu usługi Azure Dev Spaces](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 3 zainstalowany][helm-installed].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Kubernetes platformy Azure

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie *MyResourceGroup* i klaster AKS o nazwie *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie usług Azure Dev Spaces w klastrze usługi AKS

Użyj `use-dev-spaces` tego polecenia, aby włączyć miejsca dewelopera w klastrze AKS i postępować zgodnie z instrukcjami. Poniższe polecenie włącza miejsca dewelopera w klastrze *MyAKS* w grupie *MyResourceGroup* i tworzy przestrzeń deweloperów o nazwie *dev*.

> [!NOTE]
> Polecenie `use-dev-spaces` zainstaluje również interfejsu wiersza polecenia azure dev spaces, jeśli nie jest jeszcze zainstalowany. Nie można zainstalować interfejsu wiersza polecenia usługi Azure Dev Spaces w usłudze Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Pobierz przykładowy kod aplikacji

W tym artykule używasz [przykładowej aplikacji Azure Dev Spaces Bike Sharing,](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) aby zademonstrować użycie usługi Azure Dev Spaces.

Klonowanie aplikacji z usługi GitHub i przechodzenie do jej katalogu:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Pobieranie dodatku HostSuffix dla *deweloperów*

Użyj `azds show-context` polecenia, aby wyświetlić hostsuchix dla *deweloperów*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aktualizowanie wykresu Helm za pomocą dodatku HostSuffix

Otwórz [charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) i zastąp wszystkie wystąpienia `<REPLACE_ME_WITH_HOST_SUFFIX>` wartością HostSuffix pobraną wcześniej. Zapisz zmiany i zamknij plik.

## <a name="run-the-sample-application-in-kubernetes"></a>Uruchamianie przykładowej aplikacji w umięślice

Polecenia do uruchamiania przykładowej aplikacji w usłudze Kubernetes są częścią istniejącego procesu i nie mają zależności od narzędzi azure dev spaces. W takim przypadku Helm jest narzędziem używanym do uruchamiania tej przykładowej aplikacji, ale inne narzędzia mogą być używane do uruchamiania całej aplikacji w obszarze nazw w klastrze. Polecenia Helm są przeznaczone dla przestrzeni deweloperskiej o nazwie *dev* utworzone wcześniej, ale to miejsce dewelopera jest również obszarem nazw Kubernetes. W rezultacie przestrzenie deweloperów mogą być kierowane przez inne narzędzia takie same jak inne obszary nazw.

Usługi Azure Dev Spaces można używać do tworzenia zespołów po uruchomieniu aplikacji w klastrze, niezależnie od narzędzi używanych do jego wdrażania.

Użyj `helm install` polecenia, aby skonfigurować i zainstalować przykładową aplikację w klastrze.

```cmd
cd charts/
helm install bikesharingsampleappsampleapp . --dependency-update --namespace dev --atomic
```

Polecenie `helm install` może potrwać kilka minut. Po zainstalowaniu przykładowej aplikacji w klastrze i po włączeniu przestrzeni `azds list-uris` deweloperskich w klastrze użyj polecenia, aby wyświetlić adresy URL przykładowej aplikacji w *deweloperze,* który jest aktualnie wybrany.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Przejdź do usługi *bikesharingweb,* otwierając publiczny `azds list-uris` adres URL z polecenia. W powyższym przykładzie publiczny adres URL usługi *bikesharingweb* to `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wybierz *Aurelia Briggs (klient)* jako użytkownika. Sprawdź, czy widzisz tekst *Hi Aurelia Briggs | Wyloguj się* u góry.

![Przykładowa aplikacja Azure Dev Spaces Bike Sharing](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Tworzenie przestrzeni deweloperskich podrzędnych

Użyj `azds space select` polecenia, aby utworzyć dwa przestrzenie podrzędne w obszarze *dev:*

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Powyższe polecenia tworzą dwie przestrzenie podrzędne w obszarze *dev* o nazwie *azureuser1* i *azureuser2*. Te dwa przestrzenie podrzędne reprezentują różne przestrzenie deweloperów dla deweloperów *azureuser1* i *azureuser2* do użycia do wprowadzania zmian w przykładowej aplikacji.

Użyj `azds space list` polecenia, aby wyświetlić listę wszystkich spacji deweloperów i potwierdzić, że zaznaczona jest *opcja dev/azureuser2.*

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Użyj, `azds list-uris` aby wyświetlić adresy URL dla przykładowej aplikacji w aktualnie wybranym miejscu, które jest *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Upewnij się, że adresy `azds list-uris` URL wyświetlane przez polecenie mają prefiks *azureuser2.s.dev.* Ten prefiks potwierdza, że bieżąca wybrana przestrzeń to *azureuser2*, który jest podrzędnym *deweloperem.*

Przejdź do usługi *bikesharingweb* dla *dev/azureuser2* dev space, `azds list-uris` otwierając publiczny adres URL z polecenia. W powyższym przykładzie publiczny adres URL usługi *bikesharingweb* to `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wybierz *Aurelia Briggs (klient)* jako użytkownika. Sprawdź, czy widzisz tekst *Hi Aurelia Briggs | Wyloguj się* u góry.

## <a name="update-code"></a>Aktualizowanie kodu

Otwórz *BikeSharingWeb/components/Header.js* za pomocą edytora tekstu i zmień tekst w [elemencie zakresu za `userSignOut` pomocą className](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Zapisz zmiany i zamknij plik.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Tworzenie i uruchamianie zaktualizowanej usługi bikesharingweb w przestrzeni *deweloperów dev/azureuser2*

Przejdź do katalogu *BikeSharingWeb/* i `azds up` uruchom polecenie.

```cmd
$ cd ../BikeSharingWeb/
$ azds up

Using dev space 'dev/azureuser2' with target 'MyAKS'
Synchronizing files...2s
...
Service 'bikesharingweb' port 'http' is available at http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/
Service 'bikesharingweb' port 80 (http) is available at http://localhost:54256
...
```

To polecenie tworzy i uruchamia usługę *bikesharingweb* w przestrzeni *deweloperskiej dev/azureuser2.* Ta usługa działa oprócz usługi *bikesharingweb* uruchomionej w *programach deweloperskich* i jest używana tylko dla żądań z prefiksem adresu URL *azureuser2.s.* Aby uzyskać więcej informacji na temat działania routingu między przestrzeniami deweloperskimi nadrzędnymi i podrzędnymi, zobacz [Jak działa usługa Azure Dev Spaces i jest skonfigurowana.](how-dev-spaces-works.md)

Przejdź do usługi *bikesharingweb* dla *dev/azureuser2* dev space, otwierając publiczny adres `azds up` URL wyświetlany w danych wyjściowych polecenia. Wybierz *Aurelia Briggs (klient)* jako użytkownika. Sprawdź, czy zaktualizowany tekst jest widoczny w prawym górnym rogu. Jeśli nie zobaczysz tej zmiany, może być konieczne odświeżenie strony lub wyczyszczenie pamięci podręcznej przeglądarki.

![Zaktualizowano przykładową aplikację azure dev spaces bike sharing](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Po przejściu do usługi `azds up`podczas uruchamiania, ślady żądań HTTP `azds up` są również wyświetlane w danych wyjściowych polecenia. Te ślady mogą pomóc w rozwiązywaniu problemów i debugowaniu usługi. Można wyłączyć te ślady przy użyciu `--disable-http-traces` podczas uruchamiania `azds up`.

## <a name="verify-other-dev-spaces-are-unchanged"></a>Sprawdzanie, czy inne miejsca dewelopera pozostają niezmienione

Jeśli `azds up` polecenie jest nadal uruchomione, naciśnij *klawisze Ctrl+c*.

```cmd
$ azds list-uris --all
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser1.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser1.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://dev.bikesharingweb.fedcab0987.eus.azds.io/               Available
http://dev.gateway.fedcab0987.eus.azds.io/                      Available
```

Przejdź do wersji *deweloperskiej bikesharingweb* w przeglądarce, wybierz *Aurelia Briggs (klient)* jako użytkownika i sprawdź, czy oryginalny tekst jest widoczny w prawym górnym rogu. *dev* Powtórz te kroki z adresem URL *dev/azureuser1.* Zwróć uwagę, że zmiany są stosowane tylko do wersji *dev/azureuser2* *bikesharingweb*. Ta izolacja zmian *do dev/azureuser2* umożliwia *azureuser2,* aby wprowadzić zmiany bez wpływu *azureuser1*.

Aby te zmiany zostały odzwierciedlone w *dev* i *dev/azureuser1,* należy postępować zgodnie z istniejącym przepływem pracy zespołu lub potokiem ciągłej integracji/ciągłego wdrażania. Na przykład ten przepływ pracy może obejmować zatwierdzenie zmiany w systemie kontroli wersji i wdrażanie aktualizacji przy użyciu potoku ciągłej integracji/ciągłego wdrażania lub narzędzi, takich jak Helm.

## <a name="clean-up-your-azure-resources"></a>Oczyszczanie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć programowanie zespołowe przez pracę z różnymi wersjami lub gałęziami kodu w różnych obszarach.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-nodejs.md)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

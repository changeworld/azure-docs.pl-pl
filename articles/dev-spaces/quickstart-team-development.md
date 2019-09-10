---
title: Programowanie zespołowe na Kubernetes przy użyciu Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 04/25/2019
ms.topic: quickstart
description: Tworzenie zespołu Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
manager: gwallace
ms.openlocfilehash: 0abfe655e30b62829a7d353b6da85bb51e378f6d
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842592"
---
# <a name="quickstart-team-development-on-kubernetes-using-azure-dev-spaces"></a>Szybki start: Programowanie zespołowe na Kubernetes przy użyciu Azure Dev Spaces

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Skonfiguruj Azure Dev Spaces w zarządzanym klastrze Kubernetes na platformie Azure.
- Wdróż dużą aplikację z wieloma mikrousługami w miejscu dev.
- Przetestuj pojedynczą mikrousługę w izolowanym miejscu dev w kontekście pełnej aplikacji.

![Programowanie zespołowe Azure Dev Spaces](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 2,13 lub nowszy](https://github.com/helm/helm/blob/master/docs/install.md).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie Moja *zasobów* i klaster AKS o nazwie *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

Klaster *MyAKS* jest również tworzony z jednym węzłem, przy użyciu rozmiaru *Standard_DS2_v2* i z wyłączonymi RBAC.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie Azure Dev Spaces w klastrze AKS

`use-dev-spaces` Użyj polecenia, aby włączyć miejsca deweloperskie w klastrze AKS i postępuj zgodnie z monitami. Poniższe polecenie włącza miejsca deweloperskie w klastrze *MyAKS* w grupie Grupa *zasobów* i tworzy przestrzeń dev o nazwie *dev*.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Pobierz przykładowy kod aplikacji

W tym artykule użyto [przykładowej aplikacji do udostępniania Azure dev Spaces roweru](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) do zademonstrowania przy użyciu Azure dev Spaces.

Klonowanie aplikacji z usługi GitHub i przechodzenie do jej katalogu:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Pobierz HostSuffix dla *deweloperów*

Użyj polecenia, aby wyświetlić HostSuffix dla *deweloperów.* `azds show-context`

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aktualizowanie wykresu Helm za pomocą HostSuffix

Otwórz [wykresy/Values. YAML](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) i Zastąp wszystkie wystąpienia `<REPLACE_ME_WITH_HOST_SUFFIX>` z pobraną wcześniej wartością HostSuffix. Zapisz zmiany i zamknij plik.

## <a name="run-the-sample-application-in-kubernetes"></a>Uruchamianie przykładowej aplikacji w Kubernetes

Polecenia służące do uruchamiania przykładowej aplikacji na Kubernetes są częścią istniejącego procesu i nie są zależne od narzędzi Azure Dev Spaces. W takim przypadku Helm jest narzędziem służącym do uruchamiania tej przykładowej aplikacji, ale inne narzędzia mogą służyć do uruchamiania całej aplikacji w przestrzeni nazw w klastrze. Polecenia Helm są przeznaczone dla obszaru deweloperskiego o nazwie *dev* utworzonego wcześniej, ale ten obszar deweloperski jest również przestrzenią nazw Kubernetes. W związku z tym miejsca deweloperskie mogą być wskazywane przez inne narzędzia takie same jak inne przestrzenie nazw.

Azure Dev Spaces do programowania zespołowego można użyć po uruchomieniu aplikacji w klastrze, niezależnie od narzędzi użytych do jego wdrożenia.

Użyj poleceń `helm install` i, aby skonfigurować i zainstalować przykładową aplikację w klastrze. `helm init`

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```
> [!Note]
> W **przypadku korzystania z klastra z włączoną funkcją RBAC**należy pamiętać o skonfigurowaniu [konta usługi dla](https://helm.sh/docs/using_helm/#role-based-access-control)tego elementu. W przeciwnym razie polecenia zakończą się niepowodzeniem. `helm`

Wykonanie `helm install` polecenia może potrwać kilka minut. Dane wyjściowe polecenia pokazują stan wszystkich usług, które zostały wdrożone w klastrze po zakończeniu:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

Po zainstalowaniu przykładowej aplikacji w klastrze, ponieważ w klastrze są włączone spacje, użyj `azds list-uris` polecenia, aby wyświetlić adresy URL dla przykładowej aplikacji w *deweloperskim* , który jest aktualnie wybrany.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Przejdź do usługi *bikesharingweb* , otwierając publiczny adres URL z `azds list-uris` polecenia. W powyższym przykładzie publiczny adres URL usługi *bikesharingweb* to `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wybierz pozycję *Aurelia Briggs (Customer)* jako użytkownik. Upewnij się, że zobaczysz tekst *Hi Aurelia Briggs | Wyloguj się* u góry.

![Przykładowa aplikacja do udostępniania roweru Azure Dev Spaces](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Tworzenie podrzędnych miejsc dev

Użyj polecenia `azds space select` , aby utworzyć dwie spacje podrzędne w obszarze *dev*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Powyższe polecenia powodują utworzenie dwóch miejsc potomnych w obszarze *dev* o nazwie *azureuser1* i *azureuser2*. Te dwie przestrzenie podrzędne reprezentują różne miejsca deweloperskie dla *azureuser1* i *azureuser2* dla deweloperów, które mają być używane do wprowadzania zmian w aplikacji przykładowej.

Użyj polecenia, aby wyświetlić listę wszystkich miejsc dev i potwierdzić, że jest wybrana opcja *dev/azureuser2.* `azds space list`

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

Upewnij się, że adresy URL wyświetlane `azds list-uris` przez polecenie mają prefiks *azureuser2. s. dev* . Ten prefiks potwierdza, że bieżące wybrane miejsce to *azureuser2*, które jest elementem podrzędnym *dev*.

Przejdź do usługi *bikesharingweb* , aby uzyskać dostęp do deweloperów *dev/azureuser2* , otwierając publiczny `azds list-uris` adres URL za pomocą polecenia. W powyższym przykładzie publiczny adres URL usługi *bikesharingweb* to `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wybierz pozycję *Aurelia Briggs (Customer)* jako użytkownik. Upewnij się, że zobaczysz tekst *Hi Aurelia Briggs | Wyloguj się* u góry.

## <a name="update-code"></a>Aktualizowanie kodu

Otwórz *BikeSharingWeb/Components/header. js* z edytorem tekstu i Zmień tekst w [elemencie span na wartość `userSignOut` ClassName](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Zapisz zmiany i zamknij plik.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Kompilowanie i uruchamianie zaktualizowanej usługi bikesharingweb w obszarze deweloperów *dev/azureuser2*

Przejdź do katalogu *BikeSharingWeb/* i uruchom `azds up` polecenie.

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

To polecenie kompiluje i uruchamia usługę *bikesharingweb* w obszarze deweloperskim */azureuser2* dev. Ta usługa jest uruchamiana poza usługą *bikesharingweb* działającą w środowisku *deweloperskim* i jest używana tylko w przypadku żądań z prefiksem adresu URL *azureuser2. s* . Aby uzyskać więcej informacji na temat działania routingu między nadrzędną i podrzędną przestrzenią programistyczną, zobacz [jak działa Azure dev Spaces i jest skonfigurowany](how-dev-spaces-works.md).

Przejdź do usługi *bikesharingweb* dla obszaru tworzenia */azureuser2* dev, otwierając publiczny adres URL wyświetlany w `azds up` danych wyjściowych polecenia. Wybierz pozycję *Aurelia Briggs (Customer)* jako użytkownik. Sprawdź, czy w prawym górnym rogu zobaczysz zaktualizowany tekst. Może być konieczne odświeżenie strony lub wyczyszczenie pamięci podręcznej przeglądarki, jeśli ta zmiana nie zostanie natychmiast widoczna.

![Zaktualizowana aplikacja Przykładowa udostępniania Azure Dev Spaces roweru](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Gdy przejdziesz do usługi w trakcie `azds up`działania, w danych wyjściowych `azds up` polecenia są również wyświetlane dane śledzenia żądań HTTP. Te ślady mogą pomóc w rozwiązywaniu problemów i debugowaniu usługi. Te ślady można wyłączyć przy `--disable-http-traces` użyciu programu `azds up`podczas uruchamiania.

## <a name="verify-other-dev-spaces-are-unchanged"></a>Upewnij się, że inne miejsca deweloperskie nie zostały zmienione

Jeśli polecenie jest nadal uruchomione, naciśnij *klawisze CTRL + c.* `azds up`

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

Przejdź do wersji *deweloperskiej* *bikesharingweb* w przeglądarce, wybierz pozycję *Aurelia Briggs (klient)* jako użytkownik i sprawdź, czy w prawym górnym rogu zobaczysz oryginalny tekst. Powtórz te kroki przy użyciu adresu URL *dev/azureuser1* . Zauważ, że zmiany są stosowane tylko do *deweloperskich/azureuser2* wersji programu *bikesharingweb*. Ta izolacja zmian w *dev/azureuser2* umożliwia *azureuser2* wprowadzanie zmian bez wpływu na *azureuser1*.

Aby zmiany zostały odzwierciedlone w *deweloperskim* i *deweloperskim/azureuser1*, należy postępować zgodnie z istniejącym przepływem pracy lub potokiem ciągłej integracji/ciągłego wdrażania. Na przykład ten przepływ pracy może wymagać zatwierdzania zmian w systemie kontroli wersji i wdrażania aktualizacji przy użyciu potoku ciągłej integracji/ciągłego dostarczania lub narzędzi, takich jak Helm.

## <a name="clean-up-your-azure-resources"></a>Czyszczenie zasobów platformy Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć programowanie zespołowe przez pracę z różnymi wersjami lub gałęziami kodu w różnych obszarach.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations

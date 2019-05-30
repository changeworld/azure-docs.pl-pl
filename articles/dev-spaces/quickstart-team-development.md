---
title: Zespołowe programowanie na platformie Kubernetes przy użyciu usługi Azure Dev miejsca do magazynowania
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 04/25/2019
ms.topic: quickstart
description: Zespołowe programowanie usługi Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, usługi AKS, usłudze Azure Kubernetes Service, kontenerów, narzędzia Helm, usługa siatki, routing siatki usługi, narzędzia kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: 74ed1981ba6bf62413d7d7bfd51282eb04afd403
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393396"
---
# <a name="quickstart-team-development-on-kubernetes-using-azure-dev-spaces"></a>Szybki start: Zespołowe programowanie na platformie Kubernetes przy użyciu usługi Azure Dev miejsca do magazynowania

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev miejsca do magazynowania w zarządzanym klastrze Kubernetes na platformie Azure.
- Wdrażanie dużych aplikacji z wielu mikrousług na miejsce deweloperów.
- Testowanie pojedynczych mikrousług w przestrzeni izolowane dev w kontekście pełnej aplikacji.

![Programowanie zespołowe w usłudze Azure Dev miejsca do magazynowania](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Zainstalowane narzędzia Helm 2,13 lub nowszej](https://github.com/helm/helm/blob/master/docs/install.md).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Utwórz klaster Azure Kubernetes Service

Należy utworzyć klaster usługi AKS w [obsługiwany region][supported-regions]. Poniższe polecenia Utwórz grupę zasobów o nazwie *MyResourceGroup* i klaster AKS, o nazwie *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

*MyAKS* klastra jest tworzona z jednym węzłem, za pomocą *Standard_DS2_v2* rozmiaru, a także przy użyciu RBAC wyłączone.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie usługi Azure Dev miejsca do magazynowania w klastrze usługi AKS

Użyj `use-dev-spaces` polecenie, aby włączyć Dev miejsca do magazynowania w klastrze usługi AKS i postępuj zgodnie z monitami. Poniższe polecenie powoduje włączenie tworzenia miejsca do magazynowania na *MyAKS* klastra w systemie *MyResourceGroup* grupy i tworzy miejsce na deweloperów o nazwie *dev*.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Pobierz przykładowy kod aplikacji

W tym artykule używasz [udostępniania roweru miejsca do magazynowania Azure Dev przykładowej aplikacji](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) celu pokazanie sposobu używania usługi Azure Dev miejsca do magazynowania.

Klonowanie aplikacji z usługi GitHub i przechodzenie do jej katalogu:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Pobieranie HostSuffix dla *dev*

Użyj `azds show-context` polecenia w celu wyświetlenia HostSuffix dla *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aktualizowanie wykresu Helm za pomocą usługi HostSuffix

Otwórz [charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) i Zamień wszystkie wystąpienia elementu `<REPLACE_ME_WITH_HOST_SUFFIX>` wartością HostSuffix pobranym wcześniej. Zapisz zmiany i zamknij plik.

## <a name="run-the-sample-application-in-kubernetes"></a>Uruchamianie przykładowej aplikacji w usłudze Kubernetes

Polecenia dotyczące uruchamiania przykładowej aplikacji na platformie Kubernetes należą do istniejącego procesu i mają niezależne na narzędzi usługi Azure Dev miejsca do magazynowania. W tym przypadku Helm to narzędzia używane do uruchamiania tej przykładowej aplikacji, ale innych narzędzi, może służyć do uruchamiania całej aplikacji w przestrzeni nazw w ramach klastra. Polecenia narzędzia Helm przeznaczonych do miejsca deweloperów o nazwie *dev* została utworzona wcześniej, ale to miejsce deweloperów jest również przestrzeni nazw Kubernetes. W wyniku tworzenia miejsca do magazynowania może być kierowany przez inne narzędzia, takie same jak w innych przestrzeniach nazw.

Można użyć usługi Azure Dev miejsca do magazynowania dla programowania zespołowego, po aplikacja jest uruchomiona w klastrze, niezależnie od tego narzędzia używane w celu jego wdrożenia.

Użyj `helm init` i `helm install` polecenia, aby skonfigurować i zainstalować przykładową aplikację w klastrze.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic --wait
```

`helm install` Polecenia może potrwać kilka minut. Dane wyjściowe polecenia pokazuje stan wszystkich usług, które je wdrożyć w klastrze, po zakończeniu:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic --wait

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

Po przykładzie aplikacja zostanie zainstalowana w klastrze i ponieważ masz Dev miejsca do magazynowania, włączona w klastrze, należy użyć `azds list-uris` polecenie, aby wyświetlić adresy URL dla przykładowej aplikacji w *dev* który jest aktualnie wybrany.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Przejdź do *bikesharingweb* usługi przez otwarcie publicznego adresu URL z `azds list-uris` polecenia. W powyższym przykładzie, publiczny adres URL dla *bikesharingweb* usługa `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wybierz *Aurelia Briggs (klienta)* jako użytkownik. Sprawdź, zostanie wyświetlony tekst *Hi Aurelia Briggs | Wyrejestruj* u góry.

![Usługa Azure Dev miejsca do magazynowania roweru udostępnianie przykładowej aplikacji](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Tworzenie obiektu podrzędnego dev miejsca do magazynowania

Użyj `azds space select` polecenie, aby utworzyć dwie spacje podrzędne w ramach *dev*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Powyższe polecenia Utwórz dwie spacje podrzędne w ramach *dev* o nazwie *azureuser1* i *azureuser2*. Te miejsca do magazynowania z dwóch podrzędnych reprezentują dev odrębne miejsca do magazynowania dla deweloperów *azureuser1* i *azureuser2* do użycia w celu wprowadzenia zmian w przykładowej aplikacji.

Użyj `azds space list` polecenie, aby wyświetlić listę wszystkich spacji deweloperów i upewnij się, *dev/azureuser2* jest zaznaczone.

```cmd
$ azds space list
Name            Selected
--------------  --------
default         False
dev             False
dev/azureuser1  False
dev/azureuser2  True
```

Użyj `azds list-uris` do wyświetlenia w adresach URL dla przykładowej aplikacji w aktualnie wybranego miejsca, która jest *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Upewnij się, że adresy URL są wyświetlane przez `azds list-uris` polecenie ma *azureuser2.s.dev* prefiks. Ten prefiks potwierdza, że wybrane miejsce jest *azureuser2*, który jest elementem podrzędnym *dev*.

Przejdź do *bikesharingweb* usługi, aby *dev/azureuser2* miejsca dev, otwierając publiczny adres URL z `azds list-uris` polecenia. W powyższym przykładzie, publiczny adres URL dla *bikesharingweb* usługa `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wybierz *Aurelia Briggs (klienta)* jako użytkownik. Sprawdź, zostanie wyświetlony tekst *Hi Aurelia Briggs | Wyloguj się* u góry.

## <a name="update-code"></a>Aktualizowanie kodu

Otwórz *BikeSharingWeb/components/Header.js* za pomocą edytora tekstów i zastępowanie tekstu w [element span z `userSignOut` className](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Zapisz zmiany i zamknij plik.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Skompiluj i uruchom usługę bikesharingweb zaktualizowane *dev/azureuser2* miejsca dev

Przejdź do *BikeSharingWeb /* katalogu i uruchom `azds up` polecenia.

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

To polecenie kompiluje i uruchamia *bikesharingweb* w *dev/azureuser2* dev miejsca. Ta usługa działa w uzupełnieniu do *bikesharingweb* usługa jest uruchomiona *dev* i jest używana tylko dla żądań z *azureuser2.s* prefiks adresu URL. Aby uzyskać więcej informacji na temat jak działa routing między nadrzędnym a podrzędnym dev miejsca do magazynowania, zobacz [jak Azure Dev miejsca do magazynowania działa i jest skonfigurowany](how-dev-spaces-works.md).

Przejdź do *bikesharingweb* usługi, aby *dev/azureuser2* miejsca dev, otwierając publiczny adres URL wyświetlany w danych wyjściowych `azds up` polecenia. Wybierz *Aurelia Briggs (klienta)* jako użytkownik. Sprawdź, czy zostanie wyświetlony zaktualizowany tekst w prawym górnym rogu. Może być konieczne odświeżenie strony, lub wyczyść pamięć podręczną przeglądarki, jeśli użytkownik nie zostanie wyświetlona natychmiast tę zmianę.

![Usługa Azure Dev miejsca do magazynowania roweru aplikacji do udostępniania przykładowe zaktualizowane](media/quickstart-team-development/bikeshare-update.png)

## <a name="verify-other-dev-spaces-are-unchanged"></a>Sprawdź, czy innych deweloperów, miejsc do magazynowania są bez zmian

Jeśli `azds up` polecenie jest nadal uruchomiona, naciśnij *Ctrl + c*.

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

Przejdź do *dev* wersję *bikesharingweb* w przeglądarce, wybierz *Aurelia Briggs (klienta)* jako użytkownik i sprawdź, zobacz oryginalny tekst w prawym górnym rogu rogu. Powtórz te czynności przy użyciu *dev/azureuser1* adresu URL. Zwróć uwagę, zmiany zostaną zastosowane tylko do *dev/azureuser2* wersję *bikesharingweb*. Izolacja zmiany *dev/azureuser2* umożliwia *azureuser2* zmian bez wpływu na *azureuser1*.

Te zmiany zostaną uwzględnione w *dev* i *dev/azureuser1*, należy przestrzegać istniejącego przepływu pracy lub potoku ciągłej integracji/ciągłego wdrażania Twojego zespołu. Na przykład ten przepływ pracy może obejmować zatwierdzeniem zmiany do systemu kontroli wersji i wdrażania aktualizacji za pomocą potoku CI/CD lub narzędzi takich jak narzędzia Helm.

## <a name="clean-up-your-azure-resources"></a>Oczyszczanie zasobów platformy Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć programowanie zespołowe przez pracę z różnymi wersjami lub gałęziami kodu w różnych obszarach.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations

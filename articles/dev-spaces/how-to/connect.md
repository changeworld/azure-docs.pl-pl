---
title: Azure Dev Spaces Połącz
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Dowiedz się, jak używać połączenia Azure Dev Spaces
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Connect
ms.openlocfilehash: 2e1984b838e961239e0533179c34eccb118abd8e
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065875"
---
# <a name="azure-dev-spaces-connect-preview"></a>Azure Dev Spaces Connect (wersja zapoznawcza)

Azure Dev Spaces Connect umożliwia uruchamianie i debugowanie kodu z lub bez kontenera na komputerze deweloperskim, podczas gdy nadal nawiązuje połączenie z klastrem Kubernetes przy użyciu pozostałej części aplikacji lub usług. Połączenie komputera deweloperskiego z klastrem pomaga szybko opracowywać aplikację i wykonywać kompleksowe testy bez konieczności tworzenia żadnych konfiguracji platformy Docker lub Kubernetes. Możesz również nawiązać połączenie z klastrem AKS bez wpływu na inne obciążenia lub użytkowników, którzy mogą korzystać z tego samego klastra.

Azure Dev Spaces Connect przekierowuje ruch między podłączonym klastrem AKS a maszyną deweloperskią. To przekierowywanie ruchu umożliwia kod na komputerze deweloperskim i usługach uruchomionych w klastrze AKS, aby komunikować się tak, jakby znajdowały się w tym samym klastrze AKS. Ponieważ Twój kod jest uruchomiony na komputerze deweloperskim, masz również elastyczność w narzędziach programistycznych, które są używane do uruchamiania i debugowania tego kodu. Azure Dev Spaces Connect zapewnia również sposób replikowania zmiennych środowiskowych i zainstalowanych plików dostępnych dla zasobników w klastrze AKS na komputerze deweloperskim.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Skonfiguruj Azure Dev Spaces w zarządzanym klastrze Kubernetes na platformie Azure.
- Wdróż dużą aplikację z wieloma mikrousługami w miejscu dev.
- Użyj Azure Dev Spaces Połącz, aby przekierować ruch między klastrem AKS i kodem uruchomionym na komputerze deweloperskim.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten przewodnik korzysta z [przykładowej aplikacji do udostępniania Azure dev Spaces roweru](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) w celu przedstawienia Azure dev Spaces Connect. Jeśli masz własną aplikację w klastrze AKS, którego chcesz użyć, możesz zacząć od tego [miejsca](#use-azure-dev-spaces-connect).

### <a name="limitations"></a>Ograniczenia

* Protokół UDP nie jest obsługiwany w tym momencie Azure Dev Spaces Connect.

### <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure][azure-cli].
* [Helm 2,13 lub nowszy][helm-installed].
* [Visual Studio Code][vs-code] z rozszerzeniem [Azure dev Spaces][azds-vs-code] zainstalowanym i działającym w systemie MacOS lub Windows 10.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie Moja *zasobów* i klaster AKS o nazwie *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie Azure Dev Spaces w klastrze AKS

Użyj `use-dev-spaces` polecenia, aby włączyć miejsca deweloperskie w klastrze AKS i postępuj zgodnie z monitami. Poniższe polecenie włącza miejsca deweloperskie w klastrze *MyAKS* w grupie Grupa *zasobów* i tworzy przestrzeń dev o nazwie *dev*.

> [!NOTE]
> Polecenie `use-dev-spaces` zainstaluje również interfejs wiersza polecenia Azure Dev Spaces, jeśli nie został jeszcze zainstalowany. Nie można zainstalować interfejsu wiersza polecenia Azure Dev Spaces w Azure Cloud Shell.

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

Użyj `azds show-context` polecenia, aby wyświetlić HostSuffix dla *deweloperów*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aktualizowanie wykresu Helm za pomocą HostSuffix

Otwórz [wykresy/Values. YAML](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) i Zastąp wszystkie wystąpienia `<REPLACE_ME_WITH_HOST_SUFFIX>` wartością HostSuffix, która została pobrana wcześniej. Zapisz zmiany i zamknij plik.

## <a name="run-the-sample-application-in-kubernetes"></a>Uruchamianie przykładowej aplikacji w Kubernetes

Polecenia służące do uruchamiania przykładowej aplikacji na Kubernetes są częścią istniejącego procesu i nie są zależne od narzędzi Azure Dev Spaces. W takim przypadku Helm jest narzędziem służącym do uruchamiania tej przykładowej aplikacji, ale inne narzędzia mogą służyć do uruchamiania całej aplikacji w przestrzeni nazw w klastrze. Polecenia Helm są przeznaczone dla obszaru deweloperskiego o nazwie *dev* utworzonego wcześniej, ale ten obszar deweloperski jest również przestrzenią nazw Kubernetes. W związku z tym miejsca deweloperskie mogą być wskazywane przez inne narzędzia takie same jak inne przestrzenie nazw.

Azure Dev Spaces do programowania można użyć po uruchomieniu aplikacji w klastrze, niezależnie od narzędzi użytych do jego wdrożenia.

### <a name="use-helm-to-install-the-sample-application"></a>Instalowanie przykładowej aplikacji przy użyciu programu Helm

Użyj poleceń `helm init` i `helm install`, aby skonfigurować i zainstalować przykładową aplikację w klastrze.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```

> [!Note]
> W **przypadku korzystania z klastra z włączoną funkcją RBAC**należy pamiętać o skonfigurowaniu [konta usługi dla](https://helm.sh/docs/using_helm/#role-based-access-control)tego elementu. W przeciwnym razie polecenia `helm` zakończą się niepowodzeniem.

Wykonanie polecenia `helm install` może potrwać kilka minut. Dane wyjściowe polecenia pokazują stan wszystkich usług, które zostały wdrożone w klastrze po zakończeniu:

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

### <a name="navigate-to-your-sample-application"></a>Przejdź do aplikacji przykładowej

Po zainstalowaniu przykładowej aplikacji w klastrze, ponieważ w klastrze są włączone spacje, użyj polecenia `azds list-uris`, aby wyświetlić adresy URL dla przykładowej aplikacji w *deweloperskim* , który jest obecnie wybrany.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Przejdź do usługi *bikesharingweb* , otwierając publiczny adres URL z `azds list-uris` polecenia. W powyższym przykładzie publiczny adres URL dla usługi *bikesharingweb* jest `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wybierz pozycję *Aurelia Briggs (Customer)* jako użytkownik, a następnie wybierz rower do wynajęcia. Sprawdź, czy jest wyświetlany obraz symbolu zastępczego dla roweru.

## <a name="use-azure-dev-spaces-connect"></a>Użyj połączenia Azure Dev Spaces

Otwórz *przystawkę dev Spaces/Samples/BikeSharingApp/Bikes* in Visual Studio Code i Użyj rozszerzenia Connect Azure dev Spaces, aby połączyć komputer deweloperski z klastrem AKS.

Aby użyć rozszerzenia Connect Azure Dev Spaces, Otwórz paletę poleceń w Visual Studio Code, klikając pozycję *Widok* i *paleta poleceń*. Zacznij pisać `Azure Dev Spaces: Redirect` i kliknij `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`lub `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Polecenia Connect](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Wybierz opcję przekierowania

Jeśli uruchomisz `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, zostanie wyświetlony monit o wybranie istniejącej usługi Kubernetes:

![Połącz wybierz usługę](../media/how-to-connect/connect-choose-service.png)

Ta opcja przekierowuje cały ruch w klastrze AKS dla tej usługi do wersji aplikacji działającej na komputerze deweloperskim. Jeśli ta usługa ma wiele serwerów w klastrze AKS, cały ruch dla tej usługi jest kierowany tylko do komputera deweloperskiego. Azure Dev Spaces Connect również kieruje cały ruch wychodzący z aplikacji z powrotem do klastra AKS.

Jeśli uruchomisz `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, zostanie wyświetlony monit o wybranie określonego elementu pod:

![Połącz wybierz pod](../media/how-to-connect/connect-choose-pod.png)

Ta opcja łączy się z konkretnym pod. Ta opcja jest przydatna w przypadku współdziałania z identyfikatorami, które nie wysyłają ani nie odbierają ruchu i replikowania zakończonych zasobników. Jeśli na stronie jest wysyłany i odbierany ruch, ta opcja zachowuje się w podobny sposób, aby `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` i przekierowuje cały ruch w klastrze AKS dla wszystkich elementów, które są powiązane z usługą wybranej usługi.

W przypadku uruchomienia `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`nie zostanie wyświetlony monit o wybranie istniejącego elementu lub usługi. Ta opcja przekierowuje cały ruch wychodzący z aplikacji uruchomionej na komputerze deweloperskim do klastra AKS.

Na potrzeby tego przykładu wybierz `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` i wybierz usługę *Bikes* .

### <a name="select-a-connection-mode"></a>Wybierz tryb połączenia

Po wybraniu opcji przekierowania zostanie wyświetlony monit z pytaniem o wybranie trybu *zastępowanie* lub *klonowanie* .

![Połącz zastępowanie lub klonowanie](../media/how-to-connect/connect-replace-clone.png)

Opcja *replace* zastępuje bieżący pod lub usługę w klastrze AKS i przekierowuje cały ruch dla tej usługi do komputera deweloperskiego. Ta opcja może być nieprzerwana dla innych usług w klastrze AKS, które współdziałają z usługą przekierowania, mogą nie działać do momentu uruchomienia aplikacji na komputerze deweloperskim. Opcja *klonowania* pozwala wybrać istniejący obszar podrzędny lub utworzyć nowe miejsce w środowisku deweloperskim do przekierowywania ruchu dla usługi lub na komputer deweloperski. Ta opcja pozwala obsłużyć izolację i nie zakłóca innych usług, ponieważ tylko ruch do tego miejsca podrzędnego jest przekierowywany do komputera deweloperskiego. Opcja *klonowania* wymaga, aby klaster AKS miał włączony Azure dev Spaces.

Na potrzeby tego przykładu wybierz *Zastąp*.

> [!NOTE]
> Jeśli istniejąca usługa ma wiele kontenerów, zostanie również wyświetlony monit o wybranie kontenera aplikacji.

### <a name="select-a-port-for-your-application"></a>Wybierz port dla swojej aplikacji

Po wybraniu trybu połączenia zostanie wyświetlony monit o wprowadzenie portu TCP używanej przez lokalną aplikację. Jeśli aplikacja otwiera wiele portów, rozdziel je przecinkami na przykład *80, 81*. Jeśli aplikacja nie akceptuje żadnych żądań sieci, wprowadź *wartość 0*. Na potrzeby tego przykładu wprowadź *8080*.

![Połącz wybierz port](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Potwierdź, że masz połączenie

Po wybraniu portu TCP aplikacji Azure Dev Spaces Connect nawiąże połączenie z klastrem AKS. Azure Dev Spaces Connect wprowadza agenta do klastra AKS w celu przekierowania ruchu między klastrem AKS i komputerem deweloperskim. Ustanowienie tego połączenia może potrwać kilka minut. Azure Dev Spaces Connect także żąda dostępu administratora, aby można było zmodyfikować plik *hosts* na komputerze deweloperskim.

> [!IMPORTANT]
> Azure Dev Spaces po nawiązaniu połączenia z klastrem usługi AKS, inne usługi w klastrze AKS mogą działać nieprawidłowo, dopóki usługa nie zostanie uruchomiona na komputerze deweloperskim. Ponadto jeśli usługa ma zależność, która nie jest dostępna na komputerze deweloperskim, może być konieczne zmodyfikowanie aplikacji lub dostarczenie [dodatkowej konfiguracji](#additional-configuration)

Azure Dev Spaces Connect otwiera okno terminalu zatytułowane *AZDS Connect-Bikes* po ustanowieniu połączenia z klastrem AKS. To okno terminalu ma wszystkie zmienne środowiskowe i wpisy DNS skonfigurowane z klastra AKS. Każdy kod uruchamiany w tym oknie terminalu lub przy użyciu debugera Visual Studio Code jest połączony z klastrem AKS.

![Połącz Terminal](../media/how-to-connect/connect-terminal.png)

Ponadto Azure Dev Spaces Connect tworzy okno zatytułowane *miejsca deweloperskie Połącz* ze wszystkimi danymi wyjściowymi.

![Połącz dane wyjściowe](../media/how-to-connect/connect-output.png)

Azure Dev Spaces Connect ma także element paska stanu, w którym jest wyświetlany stan połączenia.

![Stan połączenia](../media/how-to-connect/connect-status.png)

Sprawdź, czy pasek stanu zawiera *spacje: połączony z dev/Bikes na porcie lokalnym 8080*.

### <a name="configure-your-application-on-your-development-machine"></a>Konfigurowanie aplikacji na komputerze deweloperskim

Otwórz okno terminalu *AZDS Connect-Bikes* i uruchom `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Otwórz [serwer Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L352) i w wierszu 352 zaktualizuj *port* do *8080*:

```javascript
var port = 8080;
var server = null;
```

Kliknij przycisk *Debuguj* , a następnie *Otwórz pozycję konfiguracje*. Spowoduje to utworzenie pliku `.vscode/launch.json`. Zastąp zawartość tego pliku następującym:

```json
{
    "configurations": [
        ...
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

Otwórz plik [Package. JSON](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) i Dodaj skrypt debugowania:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>Uruchom aplikację na komputerze deweloperskim

Kliknij ikonę *debugowania* po lewej stronie, a następnie kliknij przycisk Start obok pozycji *Uruchom za pomocą npm* w górnej części ekranu.

![Uruchom za pomocą NPM](../media/how-to-connect/launch-npm.png)

Aplikacja zostanie uruchomiona, a Azure Dev Spaces Connect przekierowuje ruch między klastrem AKS a maszyną deweloperskią. Komunikaty podobne do poniższych znajdują się w *konsoli debugowania*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 8080
```

Przejdź do usługi *bikesharingweb* , klikając pasek stanu Azure dev Spaces Connect i wybierając publiczny adres URL aplikacji. Publiczny adres URL można także znaleźć w poleceniu `azds list-uris` uruchomionego wcześniej. Jeśli nie używasz Azure Dev Spaces w klastrze, użyj adresu IP lub adresu URL aplikacji dla używanej przestrzeni nazw. W powyższym przykładzie publiczny adres URL dla usługi *bikesharingweb* jest `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wybierz pozycję *Aurelia Briggs (Customer)* jako użytkownik, a następnie wybierz rower do wynajęcia.

### <a name="set-a-break-point"></a>Ustaw punkt przerwania

Otwórz [serwer Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) i kliknij w dowolnym miejscu w wierszu 233, aby umieścić w nim kursor. Ustaw punkt przerwania, naciskając klawisz *F9* lub klikając polecenie *Debuguj* , a następnie *Przełącz punkt przerwania*.

Przejdź do usługi *bikesharingweb* , otwierając publiczny adres URL. Wybierz pozycję *Aurelia Briggs (Customer)* jako użytkownik, a następnie wybierz rower do wynajęcia. Zauważ, że obraz dla roweru nie zostanie załadowany. Wróć do Visual Studio Code i obserwuj wiersz 233. Ustawiony punkt przerwania został wstrzymany usługi w wierszu 233. Aby wznowić działanie usługi, naciśnij klawisz *F5* lub kliknij pozycję *Debuguj* i *Kontynuuj*. Wróć do przeglądarki i sprawdź, czy jest wyświetlany obraz symbolu zastępczego dla roweru.

Usuń punkt przerwania, umieszczając kursor w wierszu 233 w `server.js` i naciskając klawisz *F9*.

### <a name="update-your-application"></a>Aktualizowanie aplikacji

Edytuj `server.js`, aby usunąć wiersze 232 i 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Sekcja powinna teraz wyglądać następująco:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Zapisz zmiany, a następnie kliknij pozycję *Debuguj* , a następnie *ponownie uruchom debugowanie*. Odśwież przeglądarkę i sprawdź, czy nie widzisz już obrazu zastępczego dla roweru.

Kliknij pozycję *Debuguj* , a następnie *Zatrzymaj debugowanie* , aby zatrzymać debuger. Kliknij przycisk Azure Dev Spaces Połącz pasek stanu, aby rozłączyć się z klastrem AKS.

## <a name="additional-configuration"></a>Dodatkowa konfiguracja

Azure Dev Spaces Connect może obsługiwać ruch routingu i replikować zmienne środowiskowe bez żadnej dodatkowej konfiguracji. Jeśli konieczne jest pobranie plików zainstalowanych do kontenera w klastrze AKS, takich jak plik ConfigMap, można utworzyć `azds-local.env`, aby pobrać te pliki na komputer deweloperski.

Oto przykład `azds-local.env`:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak za pomocą akcji Azure Dev Spaces i GitHub przetestować zmiany z żądania ściągnięcia bezpośrednio w AKS przed scaleniem żądania ściągnięcia z gałęzią główną repozytorium.

> [!div class="nextstepaction"]
> [Akcje GitHub & usłudze Azure Kubernetes Service][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download
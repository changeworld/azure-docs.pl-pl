---
title: Łączenie komputera deweloperskiego z klastrem AKS
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Dowiedz się, jak połączyć maszynę deweloperskią z klastrem AKS przy użyciu Azure Dev Spaces
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: cba68702155475832930c791c3a7be014375ba04
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770683"
---
# <a name="connect-your-development-machine-to-an-aks-cluster-preview"></a>Łączenie komputera deweloperskiego z klastrem AKS (wersja zapoznawcza)

Azure Dev Spaces umożliwia uruchamianie i debugowanie kodu z lub bez kontenera na komputerze deweloperskim, przy jednoczesnym połączeniu z klastrem Kubernetes z pozostałą częścią aplikacji lub usług. Połączenie komputera deweloperskiego z klastrem pomaga szybko opracowywać aplikację i wykonywać kompleksowe testy bez konieczności tworzenia żadnych konfiguracji platformy Docker lub Kubernetes. Możesz również nawiązać połączenie z klastrem AKS bez wpływu na inne obciążenia lub użytkowników, którzy mogą korzystać z tego samego klastra.

Azure Dev Spaces przekierowuje ruch między podłączonym klastrem AKS a maszyną deweloperskią. To przekierowywanie ruchu umożliwia kod na komputerze deweloperskim i usługach uruchomionych w klastrze AKS, aby komunikować się tak, jakby znajdowały się w tym samym klastrze AKS. Ponieważ Twój kod jest uruchomiony na komputerze deweloperskim, masz również elastyczność w narzędziach programistycznych, które są używane do uruchamiania i debugowania tego kodu. Azure Dev Spaces zapewnia również sposób replikowania zmiennych środowiskowych i zainstalowanych plików dostępnych dla zasobników w klastrze AKS na komputerze deweloperskim.

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

* Skonfiguruj Azure Dev Spaces w zarządzanym klastrze Kubernetes na platformie Azure.
* Wdróż dużą aplikację z wieloma mikrousługami w miejscu dev.
* Użyj Azure Dev Spaces, aby przekierować ruch między klastrem AKS i kodem uruchomionym na komputerze deweloperskim.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten przewodnik korzysta z [przykładowej aplikacji do udostępniania Azure dev Spaces roweru](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) do zademonstrowania połączenia komputera deweloperskiego z klastrem AKS. Postępuj zgodnie z instrukcjami w [pliku Readme przykładowej aplikacji do udostępniania Azure dev Spaces roweru](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) , aby uruchomić przykładową aplikację. Alternatywnie, jeśli masz własną aplikację w klastrze AKS, możesz nadal wykonać poniższe kroki i używać nazw własnych usług i zasobników.

### <a name="limitations"></a>Ograniczenia

* Protokół UDP nie jest w tej chwili obsługiwany.

### <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure][azure-cli].
* [Visual Studio Code][vs-code] z rozszerzeniem [Azure dev Spaces][azds-vs-code] zainstalowanym i działającym w systemie MacOS lub Windows 10.
* [Aplikacja Przykładowa do udostępniania Azure dev Spaces roweru](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) lub własnej aplikacji uruchomionej w klastrze AKS.

## <a name="connect-your-development-machine"></a>Łączenie maszyny deweloperskiej

Otwórz *przystawkę dev Spaces/Samples/BikeSharingApp/Bikes* in Visual Studio Code i użyj rozszerzenia Azure dev Spaces, aby połączyć komputer deweloperski z klastrem AKS.

Aby użyć rozszerzenia Azure Dev Spaces, Otwórz paletę poleceń w Visual Studio Code, klikając pozycję *Widok* i *paleta poleceń*. Zacznij pisać `Azure Dev Spaces: Redirect` i kliknij `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`lub `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Polecenia](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Wybierz opcję przekierowania

Jeśli uruchomisz `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, zostanie wyświetlony monit o wybranie istniejącej usługi Kubernetes:

![Wybieranie usługi](../media/how-to-connect/connect-choose-service.png)

Ta opcja przekierowuje cały ruch w klastrze AKS dla tej usługi do wersji aplikacji działającej na komputerze deweloperskim. Jeśli ta usługa ma wiele serwerów w klastrze AKS, cały ruch dla tej usługi jest kierowany tylko do komputera deweloperskiego. Azure Dev Spaces również kieruje cały ruch wychodzący z aplikacji z powrotem do klastra AKS.

Jeśli uruchomisz `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, zostanie wyświetlony monit o wybranie określonego elementu pod:

![Wybierz pod](../media/how-to-connect/connect-choose-pod.png)

Ta opcja łączy się z konkretnym pod. Ta opcja jest przydatna w przypadku współdziałania z identyfikatorami, które nie wysyłają ani nie odbierają ruchu i replikowania zakończonych zasobników. Jeśli na stronie jest wysyłany i odbierany ruch, ta opcja zachowuje się w podobny sposób, aby `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` i przekierowuje cały ruch w klastrze AKS dla wszystkich elementów, które są powiązane z usługą wybranej usługi.

W przypadku uruchomienia `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`nie zostanie wyświetlony monit o wybranie istniejącego elementu lub usługi. Ta opcja przekierowuje cały ruch wychodzący z aplikacji uruchomionej na komputerze deweloperskim do klastra AKS.

Na potrzeby tego przykładu wybierz `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` i wybierz usługę *Bikes* .

### <a name="select-a-connection-mode"></a>Wybierz tryb połączenia

Po wybraniu opcji przekierowania zostanie wyświetlony monit z pytaniem o wybranie trybu *zastępowanie* lub *klonowanie* .

![Zastąp lub Klonuj](../media/how-to-connect/connect-replace-clone.png)

Opcja *replace* zastępuje bieżący pod lub usługę w klastrze AKS i przekierowuje cały ruch dla tej usługi do komputera deweloperskiego. Ta opcja może być nieprzerwana dla innych usług w klastrze AKS, które współdziałają z usługą przekierowania, mogą nie działać do momentu uruchomienia aplikacji na komputerze deweloperskim. Opcja *klonowania* pozwala wybrać istniejący obszar podrzędny lub utworzyć nowe miejsce w środowisku deweloperskim do przekierowywania ruchu dla usługi lub na komputer deweloperski. Ta opcja pozwala obsłużyć izolację i nie zakłóca innych usług, ponieważ tylko ruch do tego miejsca podrzędnego jest przekierowywany do komputera deweloperskiego. Opcja *klonowania* wymaga, aby klaster AKS miał włączony Azure dev Spaces.

Na potrzeby tego przykładu wybierz *Zastąp*.

> [!NOTE]
> Jeśli istniejąca usługa ma wiele kontenerów, zostanie również wyświetlony monit o wybranie kontenera aplikacji.

### <a name="select-a-port-for-your-application"></a>Wybierz port dla swojej aplikacji

Po wybraniu trybu połączenia zostanie wyświetlony monit o wprowadzenie portu TCP używanej przez lokalną aplikację. Jeśli aplikacja otwiera wiele portów, rozdziel je przecinkami na przykład *80, 81*. Jeśli aplikacja nie akceptuje żadnych żądań sieci, wprowadź *wartość 0*. Na potrzeby tego przykładu wprowadź *3000*.

![Połącz wybierz port](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Potwierdź, że masz połączenie

Po wybraniu portu TCP aplikacji Azure Dev Spaces nawiąże połączenie z klastrem AKS. Azure Dev Spaces wprowadza agenta do klastra AKS w celu przekierowania ruchu między klastrem AKS i komputerem deweloperskim. Ustanowienie tego połączenia może potrwać kilka minut. Azure Dev Spaces również poprosić o dostęp administratora w celu zmodyfikowania pliku *hosts* na komputerze deweloperskim.

> [!IMPORTANT]
> Po Azure Dev Spaces ustanowieniu połączenia z klastrem AKS inne usługi w klastrze AKS mogą działać nieprawidłowo, dopóki usługa nie zostanie uruchomiona na komputerze deweloperskim w przypadku wybrania opcji *Zamień* połączenie. Zamiast tego można wybrać tryb połączenia *klonowania* , aby utworzyć podrzędny obszar deweloperski dla przekierowania i uniknąć przerw w miejscu nadrzędnym. Ponadto jeśli usługa ma zależność, która nie jest dostępna na komputerze deweloperskim, może być konieczne zmodyfikowanie aplikacji lub dostarczenie [dodatkowej konfiguracji](#additional-configuration)

Azure Dev Spaces otwiera okno terminalu zatytułowane *AZDS Connect-Bikes* po ustanowieniu połączenia z klastrem AKS. To okno terminalu ma wszystkie zmienne środowiskowe i wpisy DNS skonfigurowane z klastra AKS. Każdy kod uruchamiany w tym oknie terminalu lub przy użyciu debugera Visual Studio Code jest połączony z klastrem AKS.

![Terminal](../media/how-to-connect/connect-terminal.png)

Ponadto Azure Dev Spaces tworzy okno zatytułowane *miejsca deweloperskie Połącz* ze wszystkimi danymi wyjściowymi.

![Dane wyjściowe](../media/how-to-connect/connect-output.png)

Azure Dev Spaces ma także element paska stanu z informacją o stanie połączenia.

![Stan](../media/how-to-connect/connect-status.png)

Sprawdź, czy pasek stanu zawiera *spacje: połączony z dev/Bikes na porcie lokalnym 3000*.

### <a name="configure-your-application-on-your-development-machine"></a>Konfigurowanie aplikacji na komputerze deweloperskim

Otwórz okno terminalu *AZDS Connect-Bikes* i uruchom `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Kliknij przycisk *Debuguj* , a następnie *Otwórz pozycję konfiguracje*. Jeśli zostanie wyświetlony monit o wybranie środowiska, wybierz pozycję *Node. js*. Spowoduje to utworzenie pliku `.vscode/launch.json`. Zastąp zawartość tego pliku następującym:

```json
{
    "configurations": [
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

Aplikacja zostanie uruchomiona i Azure Dev Spaces przekierowuje ruch między klastrem AKS a maszyną deweloperskią. Komunikaty podobne do poniższych znajdują się w *konsoli debugowania*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Przejdź do usługi *bikesharingweb* , klikając pasek stanu Azure dev Spaces i wybierając publiczny adres URL aplikacji. Publiczny adres URL można także znaleźć w poleceniu `azds list-uris` uruchomionego wcześniej. Jeśli nie używasz Azure Dev Spaces w klastrze, użyj adresu IP lub adresu URL aplikacji dla używanej przestrzeni nazw. W powyższym przykładzie publiczny adres URL dla usługi *bikesharingweb* jest `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wybierz pozycję *Aurelia Briggs (Customer)* jako użytkownik, a następnie wybierz rower do wynajęcia.

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

Kliknij pozycję *Debuguj* , a następnie *Zatrzymaj debugowanie* , aby zatrzymać debuger. Kliknij pasek stanu Azure Dev Spaces, aby rozłączyć się z klastrem AKS.

## <a name="additional-configuration"></a>Dodatkowa konfiguracja

Azure Dev Spaces może obsługiwać ruch routingu i replikowanie zmiennych środowiskowych bez żadnej dodatkowej konfiguracji. Jeśli konieczne jest pobranie plików zainstalowanych do kontenera w klastrze AKS, takich jak plik ConfigMap, można utworzyć `azds-local.env`, aby pobrać te pliki na komputer deweloperski.

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

## <a name="using-logging-and-diagnostics"></a>Korzystanie z funkcji rejestrowania i diagnostyki

Dane wyjściowe rejestrowania są zapisywane do okna *dev Spaces Connect* po nawiązaniu połączenia z maszyną deweloperskią z klastrem AKS.

![Dane wyjściowe](../media/how-to-connect/connect-output.png)

Kliknij pasek stanu Azure Dev Spaces a następnie wybierz pozycję *Pokaż informacje diagnostyczne*. To polecenie drukuje bieżące zmienne środowiskowe i cały system DNS w danych wyjściowych rejestrowania.

![Dane wyjściowe z diagnostyką](../media/how-to-connect/connect-output-diagnostics.png)

Ponadto można znaleźć dzienniki diagnostyczne w katalogu `Azure Dev Spaces` w [katalogu *tymczasowym* komputera deweloperskiego][azds-tmp-dir].

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak za pomocą akcji Azure Dev Spaces i GitHub przetestować zmiany z żądania ściągnięcia bezpośrednio w AKS przed scaleniem żądania ściągnięcia z gałęzią główną repozytorium.

> [!div class="nextstepaction"]
> [Akcje GitHub & usłudze Azure Kubernetes Service][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download
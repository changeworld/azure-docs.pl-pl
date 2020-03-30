---
title: Podłączanie komputera deweloperskiego do klastra AKS (wersja zapoznawcza)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Dowiedz się, jak połączyć komputer dewelopera z klastrem AKS za pomocą usługi Azure Dev Spaces
keywords: Miejsca deweloperów platformy Azure, przestrzenie deweloperów, platforma do dokowania, sieci Kubernetes, platforma Azure, usługa AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235005"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>Podłączanie komputera deweloperskiego do klastra AKS (wersja zapoznawcza)

Usługa Azure Dev Spaces umożliwia uruchamianie i debugowanie kodu z kontenerem lub bez kontenera na komputerze deweloperskim, podczas gdy nadal jest połączony z klastrem usługi Kubernetes z resztą aplikacji lub usług. Podłączenie komputera dewelopera do klastra pomaga szybko tworzyć aplikację i przeprowadzać kompleksowe testy bez konieczności tworzenia dowolnej konfiguracji platformy Docker lub Kubernetes. Można również połączyć się z klastrem AKS bez wpływu na inne obciążenia lub użytkowników, którzy mogą używać tego samego klastra.

Usługa Azure Dev Spaces przekierowuje ruch między połączonym klastrem AKS a komputerem deweloperskim. To przekierowanie ruchu umożliwia kod na komputerze deweloperskim i usług uruchomionych w klastrze AKS do komunikowania się tak, jakby były one w tym samym klastrze AKS. Ponieważ kod jest uruchomiony na komputerze deweloperskim, masz również elastyczność w narzędzia programistyczne, których używasz do uruchamiania i debugowania tego kodu. Usługa Azure Dev Spaces zapewnia również sposób replikowania zmiennych środowiskowych i zainstalowanych plików dostępnych dla zasobników w klastrze AKS na komputerze deweloperskim.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

* Konfigurowanie usługi Azure Dev Spaces w zarządzanym klastrze kubernetes na platformie Azure.
* Wdrażanie dużej aplikacji z wieloma mikrousługami w przestrzeni deweloperów.
* Użyj usługi Azure Dev Spaces, aby przekierować ruch między klastrem AKS a kodem uruchomionym na komputerze deweloperskim.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym przewodniku użyto [przykładowej aplikacji Azure Dev Spaces Bike Sharing,](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) aby zademonstrować podłączenie komputera deweloperskiego do klastra AKS. Postępuj zgodnie z instrukcjami w [przykładowej aplikacji AZURE Dev Spaces Bike Sharing README,](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) aby uruchomić przykładową aplikację. Alternatywnie, jeśli masz własną aplikację w klastrze AKS nadal można wykonać poniższe kroki i używać nazw własnych usług i zasobników.

### <a name="limitations"></a>Ograniczenia

* UDP nie jest obecnie obsługiwana.

### <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure][azure-cli].
* [Visual Studio Code][vs-code] z rozszerzeniem [Azure Dev Spaces][azds-vs-code] zainstalowanym i uruchomionym w systemie MacOS lub Windows 10.
* Przykładowa [aplikacja azure dev spaces bike sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) lub własna aplikacja działająca w klastrze AKS.

## <a name="connect-your-development-computer"></a>Podłączanie komputera dewelopera

Otwórz *dev-spaces/samples/BikeSharingApp/Bikes* w programie Visual Studio Code i użyj rozszerzenia Azure Dev Spaces, aby połączyć komputer dewelopera z klastrem AKS.

Aby użyć rozszerzenia Usługi Azure Dev Spaces, otwórz paletę poleceń w programie Visual Studio Code, klikając *pozycję Wyświetl,* a następnie *paletę poleceń*. Zacznij pisać `Azure Dev Spaces: Redirect` i kliknij `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`albo `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`, lub .

![Polecenia](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Wybierz opcję przekierowania

Jeśli uruchomisz `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`program , zostaniesz poproszony o wybranie istniejącej usługi Kubernetes:

![Wybierz usługę](../media/how-to-connect/connect-choose-service.png)

Ta opcja przekierowuje cały ruch w klastrze AKS dla tej usługi do wersji aplikacji uruchomionej na komputerze deweloperskim. Jeśli ta usługa ma wiele zasobników uruchomionych w klastrze AKS, cały ruch dla tej usługi jest kierowany tylko do komputera deweloperskiego. Usługa Azure Dev Spaces kieruje również cały ruch wychodzący z aplikacji z powrotem do klastra AKS.

Jeśli uruchomisz `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, zostaniesz poproszony o wybranie określonego zasobnika:

![Wybierz pozycję Pod](../media/how-to-connect/connect-choose-pod.png)

Ta opcja łączy się z określonym zasobnikiem. Ta opcja jest przydatna do interakcji z zasobnikami, które nie wysyłają ani nie odbierają ruchu i replikują zakończone zasobniki. Jeśli zasobnik wysyła i odbiera ruch, ta opcja `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` zachowuje się w podobny sposób i przekieruje cały ruch w klastrze AKS dla wszystkich zasobników związanych z usługą wybranego zasobnika.

Po uruchomieniu `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`nie zostanie wyświetlony monit o wybranie istniejącego zasobnika lub usługi. Ta opcja przekierowuje cały ruch wychodzący z aplikacji uruchomionej na komputerze deweloperskim do klastra AKS.

W tym przykładzie wybierz `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` i wybierz usługę *rowerów.*

### <a name="select-a-connection-mode"></a>Wybieranie trybu połączenia

Po wybraniu opcji przekierowania zostanie wyświetlony monit o wybranie trybu *zamień* lub *Klonuj* połączenie.

![Zastępowanie lub klonowanie](../media/how-to-connect/connect-replace-clone.png)

Opcja *Zamień* zastępuje bieżącą zasobnik lub usługę w klastrze AKS i przekierowuje cały ruch tej usługi do komputera deweloperskiego. Ta opcja może być uciążliwa dla innych usług w klastrze AKS, które współdziałają z przekierowaną usługą, może nie działać, dopóki nie uruchomisz aplikacji na komputerze deweloperskim. Opcja *Klonuj* umożliwia wybranie istniejącego miejsca deweloperskiego podrzędnego lub utworzenie nowego miejsca deweloperskiego podrzędnego do przekierowania ruchu dla zasobnika lub usługi do komputera deweloperskiego. Ta opcja umożliwia pracę w izolacji i nie zakłócać innych usług, ponieważ tylko ruch do tego miejsca dewelopera podrzędnego zostanie przekierowany do komputera dewelopera. Opcja *Klonuj* wymaga, aby klaster AKS miał włączoną usługę Azure Dev Spaces.

W tym przykładzie wybierz pozycję *Zamień*.

> [!NOTE]
> Jeśli zasobnik istniejącej usługi ma wiele kontenerów, zostanie wyświetlony monit o wybranie kontenera aplikacji.

### <a name="select-a-port-for-your-application"></a>Wybieranie portu dla aplikacji

Po wybraniu trybu połączenia zostanie wyświetlony monit o wprowadzenie portu TCP aplikacji lokalnej. Jeśli aplikacja otworzy wiele portów, oddziel je przecinkiem na przykład *80,81*. Jeśli aplikacja nie akceptuje żadnych żądań sieciowych, wprowadź *0*. W tym przykładzie wprowadź *3000*.

![Połącz wybierz port](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Potwierdź, że masz połączenie

Po wybraniu portu TCP aplikacji usługa Azure Dev Spaces ustanowi połączenie z klastrem AKS. Usługa Azure Dev Spaces wstrzykuje agenta do klastra AKS, aby przekierować ruch między klastrem AKS a komputerem deweloperskim. Ustanowienie tego połączenia może potrwać kilka minut. Usługa Azure Dev Spaces zażąda również dostępu administratora w celu zmodyfikowania pliku *hostów* na komputerze deweloperskim.

> [!IMPORTANT]
> Po utworzeniu usługi Azure Dev Spaces połączenie z klastrem AKS, inne usługi w klastrze AKS może nie działać poprawnie, dopóki nie uruchomisz usługi na komputerze deweloperskim, jeśli wybierzesz tryb *zamień* połączenie. Zamiast tego można wybrać tryb klonowania *połączenia,* aby utworzyć podrzędne miejsce dewelopera dla przekierowania i uniknąć zakłóceń w przestrzeni nadrzędnej. Ponadto jeśli usługa ma zależność, która nie jest dostępna na komputerze deweloperskim, może być konieczne zmodyfikowanie aplikacji lub podanie [dodatkowej konfiguracji](#additional-configuration)

Usługa Azure Dev Spaces otwiera okno terminalu o nazwie *AZDS Connect — Bikes* po nawiązaniu połączenia z klastrem AKS. To okno terminala ma wszystkie zmienne środowiskowe i wpisy DNS skonfigurowane z klastra AKS. Każdy kod uruchomiony w tym oknie terminala lub przy użyciu debugera kodu programu Visual Studio jest połączony z klastrem AKS.

![Terminal](../media/how-to-connect/connect-terminal.png)

Ponadto usługa Azure Dev Spaces tworzy okno *zatytułowane Dev Spaces Connect* ze wszystkimi jego wyjściami.

![Dane wyjściowe](../media/how-to-connect/connect-output.png)

Usługa Azure Dev Spaces ma również element paska stanu pokazujący stan połączenia.

![Stan](../media/how-to-connect/connect-status.png)

Sprawdź, czy na pasku stanu są wyświetlane *miejsca deweloperów: Połączone z deweloperami/rowerami na porcie lokalnym 3000*.

### <a name="configure-your-application-on-your-development-computer"></a>Konfigurowanie aplikacji na komputerze deweloperskim

Otwórz okno terminala *AZDS Connect - Bikes* i uruchom: `npm install`

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Kliknij *przycisk Debugowanie,* a następnie *pozycję Otwórz konfiguracje*. Jeśli zostanie wyświetlony monit o wybranie środowiska, wybierz *node.js*. Spowoduje to `.vscode/launch.json` utworzenie pliku. Zastąp zawartość tego pliku następującymi:

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

Otwórz [plik package.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) i dodaj skrypt debugowania:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Uruchamianie aplikacji na komputerze deweloperskim

Kliknij ikonę *debugowania* po lewej stronie i kliknij przycisk start obok *przycisku Uruchom za pośrednictwem NPM* u góry.

![Uruchamianie za pośrednictwem NPM](../media/how-to-connect/launch-npm.png)

Aplikacja zostanie uruchomiony i usługi Azure Dev Spaces przekierowuje ruch między klastrem AKS i komputera deweloperskiego. W *konsoli debugowania*zobaczysz komunikaty podobne do poniższych:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Przejdź do usługi *bikesharingweb,* klikając pasek stanu azure dev spaces i wybierając publiczny adres URL aplikacji. Publiczny adres URL można również `azds list-uris` znaleźć w poleceniu, które uruchomiono wcześniej. Jeśli nie używasz usługi Azure Dev Spaces w klastrze, użyj adresu IP lub adresu URL aplikacji dla używanego obszaru nazw. W powyższym przykładzie publiczny adres URL usługi *bikesharingweb* to `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wybierz *Aurelia Briggs (klienta)* jako użytkownika, a następnie wybierz rower do wynajęcia.

### <a name="set-a-break-point"></a>Ustawianie punktu przerwania

Otwórz [server.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) i kliknij gdzieś na wierszu 233, aby umieścić tam kursor. Ustaw punkt przerwania, naciskając *klawisz F9* lub klikając *przycisk Debug,* a następnie *przełącz punkt przerwania*.

Przejdź do usługi *bikesharingweb,* otwierając publiczny adres URL. Wybierz *Aurelia Briggs (klienta)* jako użytkownika, a następnie wybierz rower do wynajęcia. Zwróć uwagę, że obraz roweru nie ładuje się. Powrót do programu Visual Studio Code i obserwować wiersz 233 jest wyróżniony. Ustawiony punkt przerwania wstrzymał usługę w wierszu 233. Aby wznowić usługę, naciśnij *klawisz F5* lub kliknij przycisk *Debugowanie,* a następnie *kontynuuj*. Wróć do przeglądarki i sprawdź, czy widzisz obraz zastępczy roweru.

Usuń punkt przerwania, umieszczając kursor w wierszu `server.js` 233 i uderzając *F9*.

### <a name="update-your-application"></a>Aktualizowanie aplikacji

Edytuj, `server.js` aby usunąć linie 232 i 233:

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

Zapisz zmiany i kliknij przycisk *Debugowanie,* a następnie *uruchom ponownie debugowanie*. Odśwież przeglądarkę i sprawdź, czy nie widzisz już obrazu zastępczego roweru.

Kliknij *przycisk Debugowanie,* a następnie *pozycję Zatrzymaj debugowanie,* aby zatrzymać debuger. Kliknij pasek stanu usługi Azure Dev Spaces, aby odłączyć się od klastra AKS.

## <a name="additional-configuration"></a>Dodatkowa konfiguracja

Usługa Azure Dev Spaces może obsługiwać ruch routingu i replikowanie zmiennych środowiskowych bez dodatkowej konfiguracji. Jeśli chcesz pobrać wszystkie pliki, które są zainstalowane do kontenera w klastrze AKS, `azds-local.env` takie jak plik ConfigMap, można utworzyć, aby pobrać te pliki na komputer dewelopera.

Oto przykład: `azds-local.env`

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>Korzystanie z rejestrowania i diagnostyki

Dane wyjściowe rejestrowania są zapisywane w oknie *Dev Spaces Connect* po podłączeniu komputera deweloperskiego do klastra AKS.

![Dane wyjściowe](../media/how-to-connect/connect-output.png)

Kliknij pasek stanu usługi Azure Dev Spaces i wybierz pozycję *Pokaż informacje diagnostyczne*. To polecenie drukuje bieżące zmienne środowiskowe i całe DNS w danych wyjściowych rejestrowania.

![Wyjście z diagnostyką](../media/how-to-connect/connect-output-diagnostics.png)

Dodatkowo dzienniki diagnostyczne można znaleźć `Azure Dev Spaces` w katalogu w [katalogu *TEMP* komputera dewelopera.][azds-tmp-dir]

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać usługi Azure Dev Spaces i akcji GitHub do testowania zmian z żądania ściągania bezpośrednio w usłudze AKS, zanim żądanie ściągnięcia zostanie scalone z główną gałęzią repozytorium.

> [!div class="nextstepaction"]
> [Akcje usługi GitHub & usłudze Azure Kubernetes][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download
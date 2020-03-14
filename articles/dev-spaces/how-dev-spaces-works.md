---
title: Jak działa Azure Dev Spaces i jest skonfigurowany
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: Zawiera opis procesów Azure Dev Spaces i sposobu ich konfiguracji w pliku konfiguracji azds. YAML
keywords: azds. YAML, Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: e96541b0008dca9cbaeda92152f835c188036971
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259918"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Jak działa Azure Dev Spaces i jest skonfigurowany

Tworzenie aplikacji Kubernetes może być trudne. Wymagane są pliki konfiguracyjne Docker i Kubernetes. Należy ustalić sposób lokalnego testowania aplikacji i korzystania z innych usług zależnych. Być może musisz obsługiwać tworzenie i testowanie wielu usług jednocześnie i z zespołem deweloperów.

Azure Dev Spaces ułatwia tworzenie, wdrażanie i debugowanie aplikacji Kubernetes bezpośrednio w usłudze Azure Kubernetes Service (AKS). Azure Dev Spaces również umożliwia zespołowi udostępnianie obszaru dev. Udostępnianie przestrzeni deweloperskiej przez zespół pozwala indywidualnym członkom zespołu rozwijać się w izolacji bez konieczności replikowania lub makietowania zależności lub innych aplikacji w klastrze.

Azure Dev Spaces tworzy i używa pliku konfiguracji do wdrażania, uruchamiania i debugowania aplikacji Kubernetes w AKS. Ten plik konfiguracji znajduje się w kodzie aplikacji i można go dodać do systemu kontroli wersji.

W tym artykule opisano procesy Azure Dev Spaces i sposobu konfigurowania tych procesów w pliku konfiguracyjnym Azure Dev Spaces. Aby szybko uzyskać Azure Dev Spaces działania i zobaczyć, jak to działa, wykonaj jedną z przewodników szybki start:

* [Środowisko Java z interfejsem wiersza polecenia i Visual Studio Code](quickstart-java.md)
* [.NET Core z interfejsem wiersza polecenia i Visual Studio Code](quickstart-netcore.md)
* [.NET Core z programem Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js z interfejsem wiersza polecenia i Visual Studio Code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Jak działa usługa Azure Dev Spaces

Azure Dev Spaces ma dwa odrębne składniki, z którymi można korzystać: kontroler i narzędzia po stronie klienta.

![Składniki Azure Dev Spaces](media/how-dev-spaces-works/components.svg)

Kontroler wykonuje następujące akcje:

* Zarządza tworzeniem i wyborem obszaru dev.
* Instaluje wykres Helm aplikacji i tworzy obiekty Kubernetes.
* Kompiluje obraz kontenera aplikacji.
* Wdraża aplikację w usłudze AKS.
* Wykonuje przyrostowe kompilacje i ponowne uruchomienie po zmianie kodu źródłowego.
* Zarządza dziennikami i śladami HTTP.
* Przesyła strumieniowo stdout i stderr do narzędzi po stronie klienta.
* Umożliwia członkom zespołu tworzenie podrzędnych miejsc deweloperskich pochodzących z nadrzędnego obszaru deweloperskiego.
* Konfiguruje Routing dla aplikacji w przestrzeni, a także między spacjami nadrzędnymi i podrzędnymi.

Kontroler znajduje się poza AKS. Umożliwia ona zachowanie komunikacji między narzędziami po stronie klienta a klastrem AKS. Kontroler jest włączony przy użyciu interfejsu wiersza polecenia platformy Azure podczas przygotowywania klastra do korzystania z Azure Dev Spaces. Gdy ta funkcja jest włączona, można korzystać z niej przy użyciu narzędzi po stronie klienta.

Narzędzia po stronie klienta umożliwiają użytkownikowi:
* Wygeneruj wykres pliku dockerfile, Helm, a Azure Dev Spaces plik konfiguracyjny dla aplikacji.
* Utwórz nadrzędne i podrzędne miejsca deweloperskie.
* Poinformuj kontroler, aby skompilować i uruchomić aplikację.

Gdy aplikacja jest uruchomiona, narzędzia po stronie klienta:
* Odbiera i wyświetla stdout i stderr z aplikacji działającej w AKS.
* Używa [portu do przodu](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) w celu zezwalania na dostęp do aplikacji sieci Web za pomocą protokołu http:\//localhost.
* Dołącza debuger do uruchomionej aplikacji w AKS.
* Synchronizuje kod źródłowy do obszaru deweloperskiego, gdy zostanie wykryta zmiana dla kompilacji przyrostowych, co pozwala na szybką iterację.

Narzędzia po stronie klienta można użyć z wiersza polecenia w ramach polecenia `azds`. Możesz również użyć narzędzi po stronie klienta w programie:

* Visual Studio Code przy użyciu [rozszerzenia Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Program Visual Studio z [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools).

Poniżej przedstawiono podstawowy przepływ konfigurowania i używania Azure Dev Spaces:
1. Przygotowywanie klastra AKS do Azure Dev Spaces
1. Przygotuj swój kod do uruchomienia na Azure Dev Spaces
1. Uruchamianie kodu w obszarze dev
1. Debugowanie kodu w obszarze dev
1. Udostępnianie obszaru deweloperskiego

Szczegółowe informacje o tym, jak Azure Dev Spaces działa w każdej z poniższych sekcji.

## <a name="prepare-your-aks-cluster"></a>Przygotowywanie klastra AKS

Przygotowywanie klastra AKS obejmuje:
* Weryfikowanie, czy klaster AKS jest w regionie [obsługiwanym przez Azure dev Spaces][supported-regions].
* Weryfikowanie, czy korzystasz z programu Kubernetes 1.10.3 lub nowszego.
* Włączanie Azure Dev Spaces w klastrze przy użyciu `az aks use-dev-spaces`

Aby uzyskać więcej informacji na temat tworzenia i konfigurowania klastra AKS dla Azure Dev Spaces, zobacz jedną z przewodników wprowadzenie:
* [Wprowadzenie do Azure Dev Spaces przy użyciu języka Java](get-started-java.md)
* [Wprowadzenie do Azure Dev Spaces przy użyciu platformy .NET Core i programu Visual Studio](get-started-netcore-visualstudio.md)
* [Wprowadzenie do Azure Dev Spaces z platformą .NET Core](get-started-netcore.md)
* [Wprowadzenie do Azure Dev Spaces przy użyciu środowiska Node. js](get-started-nodejs.md)

Po włączeniu Azure Dev Spaces w klastrze AKS zainstaluje on kontroler dla klastra. Kontroler jest oddzielnym zasobem platformy Azure poza klastrem i wykonuje następujące czynności w przypadku zasobów w klastrze:

* Tworzy lub wyznacza przestrzeń nazw Kubernetes, która ma być używana jako przestrzeń dev.
* Usuwa wszystkie Kubernetes przestrzeń nazw o nazwie *azds*, jeśli istnieje, i tworzy nową.
* Wdraża konfigurację elementu webhook Kubernetes.
* Wdraża serwer przyjęcia elementu webhook.
    

Używa również tej samej nazwy głównej usługi, która jest używany przez klaster AKS do wykonywania wywołań usługi do innych składników Azure Dev Spaces.

![Azure Dev Spaces Przygotuj klaster](media/how-dev-spaces-works/prepare-cluster.svg)

Aby można było użyć Azure Dev Spaces, musi istnieć co najmniej jeden obszar deweloperski. Azure Dev Spaces używa Kubernetes przestrzenie nazw w klastrze AKS dla miejsc deweloperskich. Gdy kontroler jest instalowany, zostanie wyświetlony komunikat z prośbą o utworzenie nowej przestrzeni nazw Kubernetes lub wybranie istniejącej przestrzeni nazw, która ma być używana jako pierwsze miejsce dev. Gdy przestrzeń nazw jest oznaczona jako przestrzeń dev, kontroler dodaje etykietę *azds.IO/Space=true* do tej przestrzeni nazw, aby zidentyfikować ją jako przestrzeń dev. Początkowe miejsce tworzenia lub wyznaczania jest wybierane domyślnie po przygotowaniu klastra. Gdy jest zaznaczone miejsce, jest używane przez Azure Dev Spaces do tworzenia nowych obciążeń.

Domyślnie kontroler tworzy przestrzeń deweloperskią o nazwie *default* przez uaktualnienie istniejącej *domyślnej* przestrzeni nazw Kubernetes. Korzystając z narzędzi po stronie klienta, można tworzyć nowe miejsca deweloperskie i usuwać istniejące spacje. Ze względu na ograniczenie w Kubernetes, nie można usunąć *domyślnego* obszaru dev. Kontroler usuwa również wszystkie istniejące przestrzenie nazw Kubernetes o nazwie *azds* , aby uniknąć konfliktów z poleceniem `azds` używanym przez narzędzia po stronie klienta.

Serwer przyjęcia elementu webhook Kubernetes służy do iniekcji z trzech kontenerów podczas wdrażania Instrumentacji: kontenera devspaces-proxy, kontenera devspaces-proxy-init i kontenera devspaces-Build. **Wszystkie trzy z tych kontenerów działają z dostępem do katalogu głównego w klastrze AKS.** Używają one również tej samej nazwy głównej usługi, która jest używana przez klaster AKS do wykonywania wywołań usługi do innych składników Azure Dev Spaces.

![Azure Dev Spaces Kubernetes serwer przyjęcia elementu webhook](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Kontener devspaces-proxy jest kontenerem przyczepki, który obsługuje cały ruch TCP do i z kontenera aplikacji i ułatwia Routing. Kontener devspaces-proxy kieruje komunikaty HTTP, jeśli są używane określone spacje. Może na przykład ułatwić kierowanie komunikatów HTTP między aplikacjami w przestrzeniach nadrzędnych i podrzędnych. Cały ruch inny niż HTTP przechodzi przez devspaces-proxy unmodifiedd. Kontener devspaces-proxy rejestruje także wszystkie przychodzące i wychodzące komunikaty HTTP i wysyła je do narzędzi po stronie klienta jako śladów. Te ślady mogą być następnie przeglądane przez dewelopera w celu sprawdzenia zachowania aplikacji.

Kontener devspaces-proxy-init jest [kontenerem init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) , który dodaje dodatkowe reguły routingu na podstawie hierarchii Space do kontenera aplikacji. Dodaje reguły routingu, aktualizując plik */etc/resolv.conf* kontenera aplikacji i konfigurację dołączenie iptables przed rozpoczęciem. Aktualizacje */etc/resolv.conf* umożliwiają rozpoznawanie nazw DNS usług w przestrzeniach nadrzędnych. Aktualizacje konfiguracji dołączenie iptables zapewniają, że cały ruch TCP do i z kontenera aplikacji jest kierowany za pośrednictwem devspaces-proxy. Wszystkie aktualizacje z devspaces-proxy-init są wykonywane oprócz reguł, które Kubernetes dodaje.

Kontener devspaces-Build jest kontenerem init i ma zainstalowany kod źródłowy projektu i gniazdo Docker. Kod źródłowy projektu i dostęp do platformy Docker umożliwiają skompilowanie kontenera aplikacji bezpośrednio przez.

> [!NOTE]
> Azure Dev Spaces używa tego samego węzła do kompilowania kontenera aplikacji i uruchamiania go. W związku z tym Azure Dev Spaces nie potrzebuje zewnętrznego rejestru kontenerów do kompilowania i uruchamiania aplikacji.

Serwer przyjmowania elementu webhook Kubernetes nasłuchuje dla każdego nowego pod, który został utworzony w klastrze AKS. Jeśli ten element pod zostanie wdrożony w dowolnym obszarze nazw z etykietą *azds.IO/Space=true* , zostanie on dodany do tych dodatkowych kontenerów. Kontener devspaces-Build jest wstrzykiwany tylko wtedy, gdy kontener aplikacji jest uruchamiany przy użyciu narzędzi po stronie klienta.

Po przygotowaniu klastra AKS można użyć narzędzi po stronie klienta do przygotowania i uruchomienia kodu w obszarze dev.

## <a name="prepare-your-code"></a>Przygotowywanie kodu

Aby można było uruchomić aplikację w przestrzeni deweloperskiej, musi ona być zamieszczona w kontenerze i należy określić sposób jej wdrożenia w Kubernetes. Aby konteneryzowanie aplikację, potrzebujesz pliku dockerfile. Aby zdefiniować sposób wdrażania aplikacji w usłudze Kubernetes, potrzebny jest [Wykres Helm](https://docs.helm.sh/). Aby pomóc w tworzeniu wykresu pliku dockerfile i Helm dla aplikacji, narzędzia po stronie klienta udostępniają `prep` polecenie:

```cmd
azds prep --public
```

Polecenie `prep` będzie przeglądać pliki w projekcie i próbować utworzyć wykres pliku dockerfile i Helm na potrzeby uruchamiania aplikacji w Kubernetes. Obecnie polecenie `prep` generuje wykres pliku dockerfile i Helm z następującymi językami:

* Java
* Node.js
* .NET Core

*Należy* uruchomić `prep` polecenie z katalogu, który zawiera kod źródłowy. Uruchomienie polecenia `prep` z poprawnego katalogu pozwala narzędziom po stronie klienta identyfikować język i utworzyć odpowiednie pliku dockerfile do konteneryzowanie aplikacji. Możesz również uruchomić polecenie `prep` z katalogu, który zawiera plik *pliku pom. XML* dla projektów języka Java.

Jeśli uruchomisz polecenie `prep` z katalogu, który nie zawiera kodu źródłowego, narzędzia po stronie klienta nie będą generować pliku dockerfile. Zostanie również wyświetlony komunikat o błędzie mówiący: nie można *wygenerować pliku dockerfile z powodu nieobsługiwanego języka*. Ten błąd występuje również, gdy narzędzia po stronie klienta nie rozpoznają typu projektu.

Po uruchomieniu polecenia `prep` można określić flagę `--public`. Ta flaga nakazuje kontrolerowi utworzenie punktu końcowego dostępnego z Internetu dla tej usługi. Jeśli ta flaga nie zostanie określona, usługa będzie dostępna tylko z poziomu klastra lub przy użyciu tunelu localhost utworzonego przez narzędzia po stronie klienta. To zachowanie można włączyć lub wyłączyć po uruchomieniu polecenia `prep` przez zaktualizowanie wygenerowanego wykresu Helm.

Polecenie `prep` nie zastępuje żadnych istniejących wykresów wieloetapowe dockerfile lub Helm, które znajdują się w projekcie. Jeśli istniejący wykres pliku dockerfile lub Helm używa tej samej konwencji nazewnictwa jak pliki wygenerowane przez polecenie `prep`, polecenie `prep` spowoduje pominięcie generowania tych plików. W przeciwnym razie polecenie `prep` będzie generować własny wykres pliku dockerfile lub Helm po stronie istniejących plików.

`prep` polecenie spowoduje również wygenerowanie pliku `azds.yaml` w katalogu głównym projektu. Azure Dev Spaces używa tego pliku do kompilowania, instalowania, konfigurowania i uruchamiania aplikacji. Ten plik konfiguracyjny zawiera lokalizację wykresu pliku dockerfile i Helm, a także dodatkową konfigurację na podstawie tych artefaktów.

Oto przykład pliku azds. YAML utworzonego za pomocą [przykładowej aplikacji platformy .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

Plik `azds.yaml` generowany przez polecenie `prep` powinien współpracować z prostym scenariuszem tworzenia pojedynczego projektu. Jeśli konkretny projekt ma zwiększoną złożoność, może być konieczne zaktualizowanie tego pliku po uruchomieniu polecenia `prep`. Na przykład projekt może wymagać pewnych dostosowań do procesu kompilacji lub uruchamiania w zależności od potrzeb związanych z programowaniem lub debugowaniem. W projekcie może być również wiele aplikacji, które wymagają wielu procesów kompilacji lub innej zawartości kompilacji.

## <a name="run-your-code"></a>Uruchamianie kodu

Aby uruchomić kod w obszarze deweloperskim, wydaj polecenie `up` w tym samym katalogu, w którym znajduje się plik `azds.yaml`:

```cmd
azds up
```

`up` polecenie przekazuje pliki źródłowe aplikacji i inne artefakty, które są konieczne do kompilowania i uruchamiania projektu w obszarze dev. Z tego miejsca, kontroler w obszarze dev:

1. Tworzy obiekty Kubernetes do wdrożenia aplikacji.
1. Kompiluje kontener dla aplikacji.
1. Wdraża aplikację w obszarze dev.
1. Tworzy publicznie dostępną nazwę DNS dla punktu końcowego aplikacji, jeśli jest skonfigurowana.
1. Używa *portu do przodu* w celu zapewnienia dostępu do punktu końcowego aplikacji przy użyciu http://localhost.
1. Przesyła strumieniowo stdout i stderr do narzędzi po stronie klienta.


### <a name="starting-a-service"></a>Uruchamianie usługi

Po uruchomieniu usługi w obszarze dev narzędzia i kontroler po stronie klienta pracują w ramach koordynacji synchronizacji plików źródłowych, tworzenia kontenera i obiektów Kubernetes oraz uruchamiania aplikacji.

Na bardziej szczegółowym poziomie jest to, co się stanie po uruchomieniu `azds up`:

1. Pliki są synchronizowane z komputera użytkownika z magazynem plików platformy Azure, który jest unikatowy dla klastra AKS użytkownika. Przekazano kod źródłowy, wykres Helm i pliki konfiguracyjne. Więcej szczegółowych informacji o procesie synchronizacji można znaleźć w następnej sekcji.
1. Kontroler tworzy żądanie uruchomienia nowej sesji. To żądanie zawiera kilka właściwości, w tym unikatowy identyfikator, nazwę przestrzeni, ścieżkę do kodu źródłowego i flagę debugowania.
1. Kontroler zastępuje symbol zastępczy *$ (tag)* na wykresie Helm z UNIKATOWYm identyfikatorem sesji i instaluje wykres Helm dla usługi. Dodanie odwołania do unikatowego identyfikatora sesji do wykresu Helm umożliwia skojarzenie kontenera wdrożonego z klastrem AKS dla tej konkretnej sesji z powrotem do żądania sesji i skojarzonych informacji.
1. Podczas instalacji wykresu Helm serwer przyjęcia elementu webhook Kubernetes dodaje dodatkowe kontenery do aplikacji pod kątem Instrumentacji i dostępu do kodu źródłowego projektu. Kontenery devspaces-proxy i devspaces-proxy-init są dodawane w celu zapewnienia śledzenia HTTP i routingu miejsca. Kontener devspaces-Build został dodany, aby zapewnić mu dostęp do wystąpienia platformy Docker i kodu źródłowego projektu na potrzeby tworzenia kontenera aplikacji.
1. Gdy aplikacja jest uruchomiona, kontener devspaces-Build i devspaces-proxy-init jest używany do kompilowania kontenera aplikacji. Kontenery aplikacji i kontenery devspaces-proxy są następnie uruchamiane.
1. Po rozpoczęciu kontenera aplikacji funkcja po stronie klienta używa funkcji Kubernetes *port-forward* w celu zapewnienia dostępu HTTP do aplikacji za pośrednictwem http://localhost. Ten port przekazujący nawiązuje połączenie komputera deweloperskiego z usługą w obszarze dev.
1. Gdy wszystkie kontenery w obszarze zostały uruchomione, usługa jest uruchomiona. W tym momencie funkcja po stronie klienta zaczyna przesyłać strumieniowo dane śledzenia HTTP, stdout i stderr. Te informacje są wyświetlane przez funkcję po stronie klienta dla dewelopera.

### <a name="updating-a-running-service"></a>Aktualizowanie uruchomionej usługi

Gdy usługa jest uruchomiona, Azure Dev Spaces ma możliwość aktualizowania tej usługi, jeśli którykolwiek z plików źródłowych projektu zostanie zmieniony. Przestrzenie programistyczne obsługują również aktualizowanie usługi w różny sposób w zależności od typu pliku, który został zmieniony. Istnieją trzy sposoby aktualizowania uruchomionej usługi przez funkcję Spaces dev:

* Bezpośrednia aktualizacja pliku
* Odbudowywanie i ponowne uruchamianie procesu aplikacji wewnątrz kontenera działającej aplikacji
* Ponowne kompilowanie i wdrażanie kontenera aplikacji

![Azure Dev Spaces synchronizacji plików](media/how-dev-spaces-works/file-sync.svg)

Niektóre pliki projektu, które są statyczne elementy zawartości, takie jak pliki HTML, CSS i cshtml, można aktualizować bezpośrednio w kontenerze aplikacji bez konieczności ponownego uruchamiania. W przypadku zmiany statycznego zasobu nowy plik zostanie zsynchronizowany z miejscem deweloperskim, a następnie używany przez uruchomiony kontener.

Zmiany plików, takie jak kod źródłowy lub pliki konfiguracji aplikacji, można zastosować przez ponowne uruchomienie procesu aplikacji w działającym kontenerze. Po zsynchronizowaniu tych plików proces aplikacji jest uruchamiany ponownie w ramach uruchomionego kontenera przy użyciu procesu *devhostagent* . Podczas pierwszego tworzenia kontenera aplikacji kontroler zastępuje polecenie uruchamiania dla aplikacji innym procesem o nazwie *devhostagent*. Rzeczywisty proces aplikacji jest następnie uruchamiany jako proces podrzędny w obszarze *devhostagent*, a jego dane wyjściowe są przekazywane przy użyciu danych wyjściowych *devhostagent*. Proces *devhostagent* jest również częścią funkcji miejsca do magazynowania i może wykonywać polecenia w działającym kontenerze w imieniu miejsc deweloperskich. Po ponownym uruchomieniu *devhostagent*:

* Powoduje zatrzymanie bieżącego procesu lub procesów skojarzonych z aplikacją
* Ponownie kompiluje aplikację
* Uruchamia ponownie proces lub procesy skojarzone z aplikacją

Sposób, w jaki *devhostagent* wykonuje powyższe kroki, jest skonfigurowany w pliku konfiguracji `azds.yaml`. Ta konfiguracja jest szczegółowo opisana w sekcji w dalszej części.

Aktualizacje plików projektu, takie jak wieloetapowe dockerfile, csproj Files lub jakakolwiek część wykresu Helm, wymagają odbudowania i ponownego wdrożenia kontenera aplikacji. Po zsynchronizowaniu jednego z tych plików z miejscem deweloperskim kontroler uruchamia polecenie [uaktualnienia Helm](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure) , a kontener aplikacji jest ponownie kompilowany i wdrażany.

### <a name="file-synchronization"></a>Synchronizacja plików

Przy pierwszym uruchomieniu aplikacji w obszarze deweloperskim są przekazywane wszystkie pliki źródłowe aplikacji. Gdy aplikacja jest uruchomiona i przy późniejszym ponownym uruchomieniu, zostaną przekazane tylko zmienione pliki. Dwa pliki są używane do koordynowania tego procesu: plik po stronie klienta i plik po stronie kontrolera.

Plik po stronie klienta jest przechowywany w katalogu tymczasowym i nosi nazwę na podstawie skrótu katalogu projektu, który jest używany w miejscach programistycznych. Na przykład w systemie Windows istnieje plik, taki jak *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* dla projektu. W systemie Linux plik po stronie klienta jest przechowywany w katalogu */tmp* . Katalog można znaleźć w witrynie macOS, uruchamiając polecenie `echo $TMPDIR`.

Ten plik jest w formacie JSON i zawiera:

* Wpis dla każdego pliku projektu, który jest synchronizowany z przestrzenią dev
* Identyfikator synchronizacji
* Sygnatura czasowa ostatniej operacji synchronizacji

Każdy wpis pliku projektu zawiera ścieżkę do pliku i jego sygnaturę czasową.

Plik po stronie kontrolera jest przechowywany w klastrze AKS. Zawiera identyfikator synchronizacji oraz sygnaturę czasową ostatniej synchronizacji.

Synchronizacja występuje, gdy sygnatury czasowe synchronizacji nie są zgodne między plikami po stronie klienta i po stronie kontrolera. Podczas synchronizacji narzędzie po stronie klienta wykonuje iterację nad wpisami plików w pliku po stronie klienta. Jeśli sygnatura czasowa pliku jest po sygnaturze czasowej synchronizacji, ten plik jest synchronizowany do obszaru dev. Po zakończeniu synchronizacji sygnatury czasowe synchronizacji są aktualizowane zarówno po stronie klienta, jak i na plikach po stronie kontrolera.

Wszystkie pliki projektu są synchronizowane, jeśli plik po stronie klienta nie istnieje. Takie zachowanie umożliwia wymuszenie pełnej synchronizacji przez usunięcie pliku po stronie klienta.

### <a name="how-routing-works"></a>Jak działa Routing

Przestrzeń dev jest oparta na AKS i używa tych samych [koncepcji sieci](../aks/concepts-network.md). Azure Dev Spaces ma także scentralizowaną usługę *ingressmanager* i wdraża swój własny kontroler transferu danych w klastrze AKS. Usługa *ingressmanager* monitoruje klastry AKS z miejscami deweloperskimi i rozszerza Azure dev Spaces w klastrze za pomocą obiektów przychodzących do routingu do aplikacji. Kontener devspaces-proxy w każdym z nich dodaje `azds-route-as` nagłówek HTTP dla ruchu HTTP do obszaru dev na podstawie adresu URL. Na przykład żądanie do adresu URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* może uzyskać nagłówek HTTP z `azds-route-as: azureuser`. Kontener devspaces-proxy nie doda nagłówka `azds-route-as`, jeśli taki już istnieje.

Gdy żądanie HTTP zostanie wysłane do usługi spoza klastra, żądanie przechodzi do kontrolera transferu danych przychodzących. Kontroler transferu danych przychodzących kieruje żądanie bezpośrednio do odpowiedniego w oparciu o jego obiekty i reguły dotyczące transferu danych przychodzących. Kontener devspaces-proxy w obszarze pod odbiera żądanie, dodaje nagłówek `azds-route-as` na podstawie adresu URL, a następnie kieruje żądanie do kontenera aplikacji.

Gdy żądanie HTTP zostanie wysłane do usługi z innej usługi w ramach klastra, żądanie najpierw przechodzi przez kontener devspaces-proxy usługi wywołującej. Kontener devspaces-proxy sprawdza żądanie HTTP i sprawdza nagłówek `azds-route-as`. Na podstawie nagłówka devspaces-proxy kontener będzie wyszukiwać adres IP usługi skojarzonej z wartością nagłówka. W przypadku znalezienia adresu IP kontener devspaces-proxy kieruje żądanie do tego adresu IP. Jeśli adres IP nie zostanie znaleziony, kontener devspaces-proxy kieruje żądanie do kontenera aplikacji nadrzędnej.

Na przykład aplikacje *Service* i *serviceB* są wdrażane w nadrzędnym obszarze deweloperskim o nazwie *default*. *Usługa* jest zależna od *serviceB* i wysyła do niej wywołania http. Użytkownik platformy Azure tworzy przestrzeń podrzędną dev na podstawie *domyślnego* obszaru o nazwie *azureuser*. Użytkownik platformy Azure wdraża także własną wersję *usługi* do ich obszaru podrzędnego. Gdy zostanie wysłane żądanie *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Routing Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. Kontroler transferu danych przychodzących wyszukuje adres IP pod względem adresu URL, który jest *usługą. azureuser*.
1. Kontroler transferu danych przychodzących znajduje adres IP pod względem obszaru dev użytkownika platformy Azure i kieruje żądanie do *usługi Service. azureuser* pod.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod odbiera żądanie i dodaje `azds-route-as: azureuser` jako nagłówek HTTP.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod względem trasy żądania do kontenera aplikacji *usługi* Service. *azureuser* pod.
1. Aplikacja *usługi* w *usłudze Service. azureuser* pod wywołuje wywołanie *serviceB*. Aplikacja *usługi* również zawiera kod, który zachowuje istniejący nagłówek `azds-route-as`, co w tym przypadku jest `azds-route-as: azureuser`.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod odbiera żądanie i wyszukuje adres IP *serviceB* na podstawie wartości nagłówka `azds-route-as`.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod nie znalazł adresu IP dla *serviceB. azureuser*.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod Szukam adresu IP dla *serviceB* w obszarze nadrzędnym, który jest *serviceB. default*.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod znajduje adres IP dla *serviceB. default* i kieruje żądanie do *serviceB. default* .
1. Kontener devspaces-proxy w *serviceB. default* pod odbiera żądanie i kieruje żądanie do kontenera aplikacji *serviceB* w *serviceB. default* pod.
1. Aplikacja *serviceB* w *serviceB. default* pod zwraca odpowiedź do *usługi. azureuser* pod.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod otrzymuje odpowiedź i kieruje odpowiedź do kontenera aplikacji *usługi Service* *. azureuser* pod.
1. Aplikacja *Service* a otrzymuje odpowiedź, a następnie zwraca własną odpowiedź.
1. Kontener devspaces-proxy w usłudze a *. azureuser* ' otrzymuje odpowiedź od kontenera aplikacji *usługi* i kieruje odpowiedź do oryginalnego obiektu wywołującego poza klastrem.

Wszystkie inne ruchy TCP, które nie są przekazywane przez protokół HTTP, za pośrednictwem kontrolera transferu danych przychodzących i kontenerów devspaces-proxy bez modyfikacji.

### <a name="how-running-your-code-is-configured"></a>Jak jest konfigurowany kod

Azure Dev Spaces używa pliku `azds.yaml` do zainstalowania i skonfigurowania usługi. Kontroler używa właściwości `install` w pliku `azds.yaml` do instalowania wykresu Helm i tworzenia obiektów Kubernetes:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Domyślnie polecenie `prep` generuje wykres Helm. Ustawia również właściwość *install. Chart* na katalog wykresu Helm. Jeśli chcesz użyć wykresu Helm w innej lokalizacji, możesz zaktualizować tę właściwość, aby użyć tej lokalizacji.

Podczas instalowania wykresów Helm Azure Dev Spaces zapewnia sposób przesłaniania wartości na wykresie Helm. Wartości domyślne wykresu Helm są w `charts/APP_NAME/values.yaml`.

Za pomocą właściwości *install. Values* można wyświetlić jeden lub więcej plików, które definiują wartości, które mają zostać zastąpione na wykresie Helm. Na przykład w przypadku wybrania nazwy hosta lub bazy danych, która jest używana w specjalnym czasie podczas uruchamiania aplikacji w obszarze dev, można użyć tej funkcji przesłonięcia. Możesz również dodać *?* na końcu dowolnych nazw plików, aby ustawić je jako opcjonalne.

Właściwość *install. Set* umożliwia skonfigurowanie co najmniej jednej wartości, która ma zostać zastąpiona na wykresie Helm. Wszystkie wartości skonfigurowane w ramach *instalacji. Set* przesłonią wartości skonfigurowane w plikach wymienionych w pliku *install. Values*. Właściwości w obszarze *install. Set* są zależne od wartości na wykresie Helm i mogą się różnić w zależności od wygenerowanego wykresu Helm.

W powyższym przykładzie właściwość *install. Set. replicaCount* informuje kontroler, ile wystąpień aplikacji ma działać w miejscu dev. W zależności od danego scenariusza można zwiększyć tę wartość, ale będzie to miało wpływ na dołączenie debugera do poziomu aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł dotyczący rozwiązywania problemów](troubleshooting.md).

Na wygenerowanym wykresie Helm obraz kontenera jest ustawiany na *{{. Values. Image. Repository}}: {{. Values. Image. tag}}* . Plik `azds.yaml` definiuje domyślnie Właściwość *install. Set. Image. tag* jako *$ (tag)* , która jest używana jako wartość *{{. Values. Image. tag}}* . Ustawiając właściwość *install. Set. Image. tag* w ten sposób, umożliwia ona oznakowanie obrazu kontenera dla aplikacji w sposób odrębny podczas uruchamiania Azure dev Spaces. W tym konkretnym przypadku obraz jest otagowany jako *\<wartość z obrazu. repository >: $ (tag)* . Należy użyć zmiennej *$ (tag)* jako wartości *install. Set. Image. tag* , aby funkcja Spaces dev rozpoznaje i ZLOKALIZOWAĆ kontener w klastrze AKS.

W powyższym przykładzie `azds.yaml` definiuje *install. Set.* Transfers. Hosts. Właściwość *install. Set. Ingress. hosts* definiuje format nazwy hosta dla publicznych punktów końcowych. Ta właściwość używa również wartości *$ (spacePrefix)* , *$ (rootSpacePrefix)* i *$ (hostSuffix)* , które są wartościami dostarczonymi przez kontroler. 

*$ (SpacePrefix)* to nazwa podrzędnego miejsca dev, która przyjmuje postać *spacename. s*. *$ (RootSpacePrefix)* to nazwa przestrzeni nadrzędnej. Na przykład jeśli *azureuser* jest obszarem podrzędnym *domyślnym*, wartość *$ (rootSpacePrefix)* jest *Domyślna* , a wartość *$ (spacePrefix)* to *azureuser. s*. Jeśli przestrzeń nie jest przestrzenią podrzędną, *$ (spacePrefix)* jest pusty. Na przykład, jeśli *domyślne* miejsce nie ma miejsca nadrzędnego, wartość *$ (rootSpacePrefix)* jest *Domyślna* i wartość *$ (spacePrefix)* jest pusta. *$ (HostSuffix)* to sufiks DNS wskazujący Azure dev Spaces kontroler transferu danych przychodzących, który działa w klastrze AKS. Ten sufiks DNS odpowiada wpisowi DNS z symbolem wieloznacznym, na przykład *\*. RANDOM_VALUE. EUS. azds. IO*, który został utworzony, gdy kontroler Azure dev Spaces został dodany do klastra AKS.

W powyższym pliku `azds.yaml` można również zaktualizować *install. Set.* Transfers. hosts, aby zmienić nazwę hosta aplikacji. Na przykład jeśli chcesz uprościć nazwę hosta aplikacji z *$ (spacePrefix) $ (rootSpacePrefix) webfrontonu $ (hostSuffix)* do *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix)* .

Aby skompilować kontener dla aplikacji, kontroler używa poniższych sekcji pliku konfiguracji `azds.yaml`:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

Kontroler używa pliku dockerfile do kompilowania i uruchamiania aplikacji.

Właściwość *Build. Context* zawiera listę katalogów, w których istnieje wieloetapowe dockerfile. Właściwość *Build. pliku dockerfile* definiuje nazwę pliku dockerfile do tworzenia wersji produkcyjnej aplikacji. Właściwość *Configurations. Development. Build. pliku dockerfile* konfiguruje nazwę pliku dockerfile dla deweloperskiej wersji aplikacji.

Posiadanie różnych wieloetapowe dockerfile na potrzeby programowania i produkcji pozwala na włączenie pewnych rzeczy podczas opracowywania i wyłączenie tych elementów dla wdrożeń produkcyjnych. Na przykład można włączyć debugowanie lub więcej pełnych rejestrowania podczas opracowywania i wyłączyć w środowisku produkcyjnym. Możesz również zaktualizować te właściwości, jeśli wieloetapowe dockerfile są nazwane inaczej lub znajdują się w innej lokalizacji.

Aby ułatwić szybkie Iterowanie podczas opracowywania, Azure Dev Spaces zsynchronizuje zmiany z projektu lokalnego i przyrostowo aktualizuje aplikację. Poniższa sekcja w pliku konfiguracji `azds.yaml` służy do konfigurowania synchronizacji i aktualizacji:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

Pliki i katalogi, które będą synchronizowane ze zmianami, są wymienione w właściwości *Configurations. opracowywać. Container. Sync* . Te katalogi są wstępnie synchronizowane po uruchomieniu polecenia `up`, jak również w przypadku wykrycia zmian. Jeśli istnieją dodatkowe lub różne katalogi, które chcesz synchronizować z obszarem deweloperskim, możesz zmienić tę właściwość.

Właściwość *Configuration. Development. Container. ITER. buildCommands* określa sposób kompilowania aplikacji w scenariuszu programistycznym. Właściwość *Configurations. Development. Container. Command* udostępnia polecenie uruchamiania aplikacji w scenariuszu programistycznym. Możesz chcieć zaktualizować jedną z tych właściwości, jeśli istnieją dodatkowe flagi kompilacji lub lub parametry środowiska uruchomieniowego, które mają być używane podczas opracowywania.

*Konfiguracja. programowanie. Container. ITER. processesToKill* wyświetla listę procesów, które mają zostać zabicia, aby zatrzymać aplikację. Możesz chcieć zaktualizować tę właściwość, jeśli chcesz zmienić zachowanie ponownego uruchamiania aplikacji podczas opracowywania. Na przykład, jeśli zaktualizowano *konfiguracje. programowanie. Container. ITER. buildCommands* lub *Configurations. programowanie. Container. Command* właściwości aby zmienić sposób tworzenia lub uruchamiania aplikacji, może zajść potrzeba zmiany procesów, które są zatrzymane.

Podczas przygotowywania kodu za pomocą polecenia `azds prep` można dodać flagę `--public`. Dodanie flagi `--public` powoduje utworzenie publicznie dostępnego adresu URL dla aplikacji. Jeśli pominięto tę flagę, aplikacja jest dostępna tylko w ramach klastra lub przy użyciu tunelu localhost. Po uruchomieniu polecenia `azds prep` można zmienić to ustawienie, modyfikując właściwość transferal *. Enabled* w `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Debugowanie kodu

W przypadku aplikacji Java, .NET i Node. js można debugować aplikację uruchamianą bezpośrednio w obszarze deweloperskim przy użyciu Visual Studio Code lub programu Visual Studio. Visual Studio Code i program Visual Studio udostępniają narzędzia do łączenia się z obszarem deweloperskim, uruchamiania aplikacji i dołączania debugera. Po uruchomieniu `azds prep`można otworzyć projekt w Visual Studio Code lub Visual Studio. Visual Studio Code lub program Visual Studio wygeneruje własne pliki konfiguracji służące do nawiązywania połączenia, które nie są uruchamiane `azds prep`. Z poziomu Visual Studio Code lub programu Visual Studio można ustawić punkty przerwania i uruchomić aplikację w obszarze dev.

![Debugowanie kodu](media/get-started-node/debug-configuration-nodejs2.png)

Gdy uruchamiasz aplikację przy użyciu Visual Studio Code lub programu Visual Studio do debugowania, obsługują one uruchamianie i łączenie się z miejscem deweloperskim w taki sam sposób jak w przypadku `azds up`. Narzędzia po stronie klienta w Visual Studio Code i Visual Studio udostępniają również dodatkowy parametr z określonymi informacjami na potrzeby debugowania. Parametr zawiera nazwę obrazu debugera, lokalizację debugera w obrazie debugera i lokalizację docelową w kontenerze aplikacji do zainstalowania folderu debugera.

Obraz debugera jest automatycznie ustalany przez narzędzia po stronie klienta. Używa metody podobnej do używanej podczas pliku dockerfile i wykresu Helm generowanego podczas uruchamiania `azds prep`. Gdy debuger zostanie zainstalowany w obrazie aplikacji, jest uruchamiany przy użyciu `azds exec`.

## <a name="sharing-a-dev-space"></a>Udostępnianie obszaru deweloperskiego

Podczas pracy z zespołem możesz [udostępnić miejsce deweloperskie w całym zespole](how-to/share-dev-spaces.md) i utworzyć pochodne miejsca dev. Miejsce dev może być używane przez każdego, kto ma dostęp współautora do grupy zasobów obszaru dev.

Możesz również utworzyć nowe miejsce dev, które jest tworzone na podstawie innego miejsca dev. Gdy tworzysz pochodną przestrzeń dev, etykieta *azds.IO/Parent-Space=Parent-Space-Name* jest dodawana do przestrzeni nazw pochodnego obszaru dev. Ponadto wszystkie aplikacje z nadrzędnego miejsca deweloperskiego są udostępniane za pomocą pochodnego obszaru dev. W przypadku wdrożenia zaktualizowanej wersji aplikacji do pochodnego obszaru dev, będzie ona istniała tylko w pochodnym obszarze dev, a nadrzędna przestrzeń dev pozostanie bez zmian. Możesz mieć maksymalnie trzy poziomy *pochodnych lub miejsc do magazynowania.*

Pochodna przestrzeń dev również umożliwia inteligentne kierowanie żądań między własnymi aplikacjami i aplikacjami udostępnionymi z jego elementu nadrzędnego. Routing działa przez próbę skierowania żądania do aplikacji w pochodnym obszarze dev i powrocie do aplikacji udostępnionej z nadrzędnego obszaru dev. Routing powróci do aplikacji udostępnionej w obszarze nadrzędnym, jeśli aplikacja nie znajduje się w przestrzeni nadrzędnej.

Na przykład:
* *Domyślna* przestrzeń dev ma aplikacje *Service* i *serviceB* .
* Przestrzeń dev *azureuser* jest pochodną *domyślnego*.
* Zaktualizowana wersja *usługi Service* . została wdrożona w *azureuser*.

W przypadku korzystania z programu *azureuser*wszystkie żądania do *usługi Service* i będą kierowane do zaktualizowanej wersji w *azureuser*. Żądanie do *serviceB* najpierw będzie kierowane do *azureuser* wersji *serviceB*. Ponieważ nie istnieje, zostanie on rozesłany do *domyślnej* wersji *serviceB*. Jeśli *azureuser* wersja *usługi Service* of zostanie usunięta, wszystkie żądania do *usługi* będą powracać do korzystania z *domyślnej* wersji *usługi*.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z Azure Dev Spaces, zobacz następujące przewodniki szybki start:

* [Środowisko Java z interfejsem wiersza polecenia i Visual Studio Code](quickstart-java.md)
* [.NET Core z interfejsem wiersza polecenia i Visual Studio Code](quickstart-netcore.md)
* [.NET Core z programem Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js z interfejsem wiersza polecenia i Visual Studio Code](quickstart-nodejs.md)

Aby rozpocząć pracę z programowaniem zespołowym, zobacz następujące artykuły:

* [Programowanie zespołowe — Java za pomocą interfejsu wiersza polecenia i Visual Studio Code](team-development-java.md)
* [Programowanie zespołowe — .NET Core z interfejsem wiersza polecenia i Visual Studio Code](team-development-netcore.md)
* [Programowanie zespołowe — .NET Core za pomocą programu Visual Studio](team-development-netcore-visualstudio.md)
* [Programowanie zespołowe — Node. js z interfejsem wiersza polecenia i Visual Studio Code](team-development-nodejs.md)



[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

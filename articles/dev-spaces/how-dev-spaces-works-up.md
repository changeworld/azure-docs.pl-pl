---
title: Jak działa uruchamianie kodu za pomocą usługi Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: W tym artykule opisano procesy uruchamiania kodu w usłudze Azure Kubernetes za pomocą usługi Azure Dev Spaces
keywords: azds.yaml, Usługi Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241363"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Jak działa uruchamianie kodu za pomocą usługi Azure Dev Spaces

Usługa Azure Dev Spaces oferuje wiele sposobów szybkiego iteracji i debugowania aplikacji Kubernetes oraz współpracy z zespołem nad klastrem usługi Azure Kubernetes (AKS). Gdy [projekt jest przygotowany do uruchomienia w przestrzeni deweloperskiej,][how-it-works-prep]można użyć dev spaces do tworzenia i uruchamiania projektu w klastrze AKS.

W tym artykule opisano, co się dzieje uruchomić kod w uzywu AKS z Dev Spaces.

## <a name="run-your-code"></a>Uruchom swój kod

Aby uruchomić kod w przestrzeni deweloperskiej, wydaj `up` polecenie `azds.yaml` w tym samym katalogu co plik:

```cmd
azds up
```

Polecenie `up` przekazuje pliki źródłowe aplikacji i inne artefakty potrzebne do utworzenia i uruchomienia projektu do obszaru deweloperów. Stamtąd kontroler w przestrzeni deweloperów:

1. Tworzy Kubernetes obiektów do wdrożenia aplikacji.
1. Tworzy kontener dla aplikacji.
1. Wdraża aplikację w przestrzeni deweloperów.
1. Tworzy publicznie dostępną nazwę DNS dla punktu końcowego aplikacji, jeśli jest skonfigurowany.
1. Używa *portu do przodu,* aby zapewnić dostęp http://localhostdo punktu końcowego aplikacji przy użyciu .
1. Przekazuje stdout i stderr do narzędzi po stronie klienta.


## <a name="starting-a-service"></a>Uruchamianie usługi

Po uruchomieniu usługi w obszarze deweloperskim narzędzia po stronie klienta i kontroler pracy w koordynacji, aby zsynchronizować pliki źródłowe, utworzyć kontener i Kubernetes obiektów i uruchomić aplikację.

Na bardziej szczegółowym poziomie, oto co `azds up`się dzieje po uruchomieniu:

1. [Pliki są synchronizowane][sync-section] z komputera użytkownika do magazynu plików platformy Azure, który jest unikatowy dla klastra AKS użytkownika. Kod źródłowy, wykres helm i pliki konfiguracyjne są przekazywane.
1. Kontroler tworzy żądanie rozpoczęcia nowej sesji. To żądanie zawiera kilka właściwości, w tym unikatowy identyfikator, nazwę spacji, ścieżkę do kodu źródłowego i flagę debugowania.
1. Kontroler zastępuje symbol zastępczy *$(tag)* na wykresie Helm unikatowym identyfikatorem sesji i instaluje wykres Helm dla usługi. Dodanie odwołania do unikatowego identyfikatora sesji do wykresu Helm umożliwia kontenera wdrożonego w klastrze AKS dla tej konkretnej sesji, aby być powiązane z żądaniem sesji i skojarzone informacje.
1. Podczas instalacji wykresu Helm serwera dostępu do sieci Web programu Kubernetes dodaje dodatkowe kontenery do zasobnika aplikacji do instrumentacji i dostępu do kodu źródłowego projektu. Devspaces-proxy i devspaces-proxy-init kontenery są dodawane w celu zapewnienia śledzenia HTTP i routingu miejsca. Devspaces-build kontener jest dodawany w celu zapewnienia zasobnika z dostępem do wystąpienia platformy Docker i kodu źródłowego projektu do tworzenia kontenera aplikacji.
1. Po uruchomieniu zasobnika aplikacji kontener devspaces-build i devspaces-proxy-init kontenera są używane do tworzenia kontenera aplikacji. Następnie uruchamiany jest kontener aplikacji i kontenery devspaces-proxy.
1. Po uruchomieniu kontenera aplikacji funkcja po stronie klienta korzysta z funkcji *przesyłania dalej portu* Kubernetes, aby zapewnić dostęp HTTP do aplikacji za pośrednictwem http://localhost. To przekierowanie portów łączy komputer dewelopera z usługą w przestrzeni deweloperów.
1. Po uruchomieniu wszystkich kontenerów w zasobniku usługa jest uruchomiona. W tym momencie funkcje po stronie klienta zaczyna przesyłać strumieniowo ślady HTTP, stdout i stderr. Te informacje są wyświetlane przez funkcję po stronie klienta dla dewelopera.

## <a name="updating-a-running-service"></a>Aktualizowanie uruchomionej usługi

Gdy usługa jest uruchomiona, usługa Azure Dev Spaces ma możliwość aktualizacji tej usługi, jeśli którykolwiek z plików źródłowych projektu zmienić. Miejsca deweloperów obsługują również aktualizowanie usługi w różny sposób w zależności od typu pliku, który został zmieniony. Istnieją trzy sposoby aktualizacji uruchomionej usługi w przestrzeniach deweloperskich:

* Bezpośrednie aktualizowanie pliku
* Przebudowa i ponowne uruchomienie procesu aplikacji wewnątrz kontenera uruchomionej aplikacji
* Przebudowa i ponowne wdrożenie kontenera aplikacji

![Synchronizacja plików usługi Azure Dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Niektóre pliki projektu, które są zasobami statycznymi, takimi jak html, css i pliki cshtml, mogą być aktualizowane bezpośrednio w kontenerze aplikacji bez ponownego uruchamiania czegokolwiek. Jeśli zmienia się zasób statyczny, nowy plik jest synchronizowany z przestrzenią deweloperów, a następnie używany przez uruchomiony kontener.

Zmiany w plikach, takich jak kod źródłowy lub pliki konfiguracji aplikacji mogą być stosowane przez ponowne uruchomienie procesu aplikacji w uruchomionym kontenerze. Po zsynchronizowaniu tych plików proces aplikacji jest ponownie uruchamiany w uruchomionym kontenerze przy użyciu procesu *devhostagent.* Podczas początkowego tworzenia kontenera aplikacji kontroler zastępuje polecenie uruchamiania aplikacji innym procesem o nazwie *devhostagent*. Rzeczywisty proces aplikacji jest następnie uruchamiany jako proces podrzędny w ramach devhostagent , a jego dane wyjściowe są przesyłane potokiem przy użyciu danych wyjściowych devhostagent.The application's actual process is then run as a child process under *devhostagent*, and its output is piped out using *devhostagent's*output. Proces *devhostagent* jest również częścią dev spaces i można wykonywać polecenia w uruchomionym kontenerze w imieniu Dev Spaces. Podczas ponownego uruchamiania *devhostagent:*

* Zatrzymuje bieżący proces lub procesy skojarzone z aplikacją
* Przebudowuje aplikację
* Ponowne uruchamianie procesu lub procesów skojarzonych z aplikacją

Sposób, w jaki *devhostagent* wykonuje poprzednie kroki, jest [skonfigurowany w pliku `azds.yaml` ][azds-yaml-section].

Aktualizacje plików projektu, takich jak Dockerfiles, pliki csproj lub dowolna część wykresu Helm wymagają, aby kontener aplikacji został przebudowany i ponownie rozmieszczony. Gdy jeden z tych plików jest zsynchronizowany z przestrzenią deweloperów, kontroler uruchamia polecenie [uaktualnienia helm,][helm-upgrade] a kontener aplikacji jest przebudowywany i ponownie wdrażani.

## <a name="file-synchronization"></a>Synchronizacja plików

Przy pierwszym uruchomieniu aplikacji w przestrzeni deweloperów wszystkie pliki źródłowe aplikacji są przekazywane. Gdy aplikacja jest uruchomiona, a później ponownie uruchamia, tylko zmienione pliki są przekazywane. Do koordynowania tego procesu służą dwa pliki: plik po stronie klienta i plik po stronie kontrolera.

Plik po stronie klienta jest przechowywany w katalogu tymczasowym i jest nazwany na podstawie skrótu katalogu projektu uruchomionego w folderze dev spaces. Na przykład w systemie Windows można mieć plik, taki jak *Użytkownicy\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* dla projektu. W systemie Linux plik po stronie klienta jest przechowywany w katalogu */tmp.* Katalog można znaleźć w systemie macOS, `echo $TMPDIR` uruchamiając polecenie.

Ten plik jest w formacie JSON i zawiera:

* Wpis dla każdego pliku projektu, który jest synchronizowany z przestrzenią deweloperów
* Identyfikator synchronizacji
* Sygnatura czasowa ostatniej operacji synchronizacji

Każdy wpis pliku projektu zawiera ścieżkę do pliku i jego sygnaturę czasową.

Plik po stronie kontrolera jest przechowywany w klastrze AKS. Zawiera identyfikator synchronizacji i sygnaturę czasowa ostatniej synchronizacji.

Synchronizacja ma miejsce, gdy sygnatury czasowe synchronizacji nie są zgodne między plikami po stronie klienta i po stronie kontrolera. Podczas synchronizacji narzędzia po stronie klienta iteruje nad wpisami pliku w pliku po stronie klienta. Jeśli sygnatura czasowa pliku znajduje się po sygnatury czasowej synchronizacji, plik ten jest synchronizowany z miejscem deweloperskim. Po zakończeniu synchronizacji sygnatury czasowe synchronizacji są aktualizowane zarówno w plikach po stronie klienta, jak i po stronie kontrolera.

Wszystkie pliki projektu są synchronizowane, jeśli plik po stronie klienta nie jest obecny. To zachowanie umożliwia wymuszenie pełnej synchronizacji przez usunięcie pliku po stronie klienta.

## <a name="how-running-your-code-is-configured"></a>Sposób konfigurowania kodu

Usługa Azure Dev `azds.yaml` Spaces używa tego pliku do instalowania i konfigurowania usługi. Kontroler używa `install` właściwości w `azds.yaml` pliku, aby zainstalować wykres Helm i utworzyć Kubernetes obiektów:

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

Domyślnie polecenie `prep` wygeneruje wykres Helm. Ustawia również *właściwość install.chart* na katalog wykresu Helm. Jeśli chcesz użyć wykresu Helm w innej lokalizacji, można zaktualizować tę właściwość, aby użyć tej lokalizacji.

Podczas instalowania wykresów Helm, azure dev spaces umożliwia zastąpienie wartości na wykresie Helm. Wartości domyślne wykresu Helm `charts/APP_NAME/values.yaml`znajdują się w pliku .

Za pomocą właściwości *install.values* można wyświetlić listę co najmniej jednego pliku definiujące wartości, które mają zostać zastąpione na wykresie Helm. Na przykład jeśli chcesz konfiguracji nazwy hosta lub bazy danych specjalnie podczas uruchamiania aplikacji w przestrzeni deweloperów, można użyć tej funkcji zastępowania. Można również dodać *?* na końcu dowolnej nazwy pliku, aby ustawić ją jako opcjonalną.

Właściwość *install.set* umożliwia skonfigurowanie jednej lub więcej wartości, które mają zostać zastąpione na wykresie Helm. Wszystkie wartości skonfigurowane w *pliku install.set* zastąpią wartości skonfigurowane w plikach wymienionych w *pliku install.values*. Właściwości w obszarze *install.set* są zależne od wartości na wykresie Helm i mogą się różnić w zależności od wygenerowanego wykresu Helm.

W powyższym przykładzie *install.set.replicaCount* właściwość informuje kontrolera, ile wystąpień aplikacji do uruchomienia w przestrzeni deweloperów. W zależności od scenariusza można zwiększyć tę wartość, ale będzie to miało wpływ na dołączanie debugera do zasobnika aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł dotyczący rozwiązywania problemów][troubleshooting].

Na wygenerowanym wykresie Helm obraz kontenera jest ustawiony na *{{ . Values.image.repository }}:{{ . Values.image.tag }}*. Plik `azds.yaml` definiuje właściwość *install.set.image.tag* jako *$(tag)* domyślnie, która jest używana jako wartość *dla {{ . Values.image.tag }}*. Ustawiając *install.set.image.tag* właściwości w ten sposób, umożliwia obraz kontenera dla aplikacji, które mają być oznaczone w różny sposób podczas uruchamiania usługi Azure Dev Spaces. W tym konkretnym przypadku obraz jest oznaczany jako * \<wartość z pliku image.repository>:$(tag)*. Aby miejsca deweloperów rozpoznawały i lokalizowali kontener w klastrze AKS, należy użyć zmiennej *$(tag)* jako wartości *pliku install.set.image.tag.*

W powyższym `azds.yaml` przykładzie definiuje *plik install.set.ingress.hosts*. Właściwość *install.set.ingress.hosts* definiuje format nazwy hosta dla publicznych punktów końcowych. Ta właściwość używa również *$(spacePrefix)*, *$(rootSpacePrefix)* i *$(hostSuffix)*, które są wartościami dostarczonymi przez kontroler.

*$(spacePrefix)* to nazwa przestrzeni deweloperskiej podrzędnej, która ma postać *SPACENAME.s*. *$(rootSpacePrefix)* to nazwa obszaru nadrzędnego. Na przykład, jeśli *azureuser* jest przestrzenią podrzędną *domyślną,* wartość *$(rootSpacePrefix)* jest *domyślna,* a wartość *$(spacePrefix)* to *azureuser.s*. Jeśli spacja nie jest przestrzenią podrzędną, *$(spacePrefix)* jest puste. Na przykład, jeśli *miejsce domyślne* nie ma miejsca nadrzędnego, wartość *$(rootSpacePrefix)* jest *domyślna,* a wartość *$(spacePrefix)* jest pusta. *$(hostSuffix)* to sufiks DNS, który wskazuje kontroler transferu danych Azure Dev Spaces Ingress Controller działający w klastrze AKS. Ten sufiks DNS odpowiada na przykład * \*wpisowi DNS z symbolami wieloznacznym. RANDOM_VALUE.eus.azds.io*, który został utworzony po dodaniu kontrolera usługi Azure Dev Spaces do klastra AKS.

W powyższym `azds.yaml` pliku można również zaktualizować *plik install.set.ingress.hosts,* aby zmienić nazwę hosta aplikacji. Na przykład, jeśli chcesz uprościć nazwa hosta aplikacji z *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* do *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)*.

Aby utworzyć kontener dla aplikacji, kontroler używa poniższych `azds.yaml` sekcji pliku konfiguracyjnego:

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

Kontroler używa pliku Dockerfile do tworzenia i uruchamiania aplikacji.

Właściwość *build.context* wyświetla listę katalogu, w którym istnieją pliki Dockerfiles. Właściwość *build.dockerfile* definiuje nazwę pliku Dockerfile do tworzenia wersji produkcyjnej aplikacji. Właściwość *configurations.develop.build.dockerfile* konfiguruje nazwę pliku Dockerfile dla dewelopernej wersji aplikacji.

Posiadanie różnych dockerfiles dla rozwoju i produkcji pozwala włączyć pewne rzeczy podczas tworzenia i wyłączyć te elementy dla wdrożeń produkcyjnych. Na przykład można włączyć debugowanie lub więcej szczegółowego rejestrowania podczas programowania i wyłączyć w środowisku produkcyjnym. Można również zaktualizować te właściwości, jeśli pliki dockerfiles są nazwane inaczej lub znajdują się w innej lokalizacji.

Aby ułatwić szybką iterację podczas tworzenia, usługa Azure Dev Spaces zsynchronizuje zmiany z projektu lokalnego i stopniowo aktualizuje aplikację. Poniższa sekcja `azds.yaml` w pliku konfiguracyjnym służy do konfigurowania synchronizacji i aktualizacji:

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

Pliki i katalogi, które będą synchronizować zmiany są wymienione w *configurations.develop.container.sync* właściwości. Te katalogi są synchronizowane początkowo `up` po uruchomieniu polecenia, a także po wykryciu zmian. Jeśli istnieją dodatkowe lub różne katalogi, które chcesz zsynchronizować z przestrzenią deweloperów, możesz zmienić tę właściwość.

Właściwość *configurations.develop.container.iterate.buildCommands* określa sposób tworzenia aplikacji w scenariuszu rozwoju. Właściwość *configurations.develop.container.command* udostępnia polecenie uruchamiania aplikacji w scenariuszu rozwoju. Można zaktualizować jedną z tych właściwości, jeśli istnieją dodatkowe flagi kompilacji lub środowiska wykonawczego lub parametry, które mają być używane podczas tworzenia.

*Configurations.develop.container.iterate.processesToKill* wyświetla listę procesów do zabicia, aby zatrzymać aplikację. Można zaktualizować tę właściwość, jeśli chcesz zmienić zachowanie ponownego uruchamiania aplikacji podczas tworzenia. Na przykład jeśli zaktualizowano *configurations.develop.container.iterate.buildCommands* lub *configurations.develop.container.command* właściwości, aby zmienić sposób tworzenia lub uruchamiania aplikacji, może być konieczne zmienić, jakie procesy są zatrzymane.

Podczas przygotowywania kodu `azds prep` za pomocą polecenia, masz `--enable-ingress` możliwość dodania flagi. Dodanie `--enable-ingress` flagi tworzy publicznie dostępny adres URL aplikacji. Jeśli ta flaga zostanie pominięta, aplikacja będzie dostępna tylko w klastrze lub w tunelu localhost. Po uruchomieniu `azds prep` polecenia można zmienić to ustawienie modyfikując właściwość `charts/APPNAME/values.yaml` *ingress.enabled* w :

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sieci i sposobie kierowania żądań w usłudze Azure Dev Spaces, zobacz [Jak działa routing z usługą Azure Dev Spaces][how-it-works-routing].

Aby dowiedzieć się więcej na temat korzystania z usługi Azure Dev Spaces do szybkiego iteracji i tworzenia, zobacz [Jak działa łączenie komputera deweloperskiego z przestrzenią deweloperów][how-it-works-connect] i jak działa [zdalne debugowanie kodu za pomocą usługi Azure Dev Spaces.][how-it-works-remote-debugging]

Aby rozpocząć korzystanie z usługi Azure Dev Spaces do uruchamiania projektu, zobacz następujące przewodniki Szybki start:

* [Szybka iteracji i debugowania z Visual Studio Code i Java][quickstart-java]
* [Szybko iterować i debugować za pomocą programu Visual Studio Code i .NET][quickstart-netcore]
* [Szybka iteracji i debugowania z Visual Studio Code i Node.js][quickstart-node]
* [Szybkie iterowanie i debugowanie za pomocą programów Visual Studio i .NET Core][quickstart-vs]
* [Używanie interfejsu wiersza polecenia do tworzenia aplikacji w ud.][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md
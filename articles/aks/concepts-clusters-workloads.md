---
title: Pojęcia — podstawy platformy Kubernetes dla usługi Azure Kubernetes (AKS)
description: Poznaj podstawowe klastra i obciążenia składników platformy Kubernetes i ich wzajemnymi do funkcji w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: bababa723e70cdc5268fb04f1104cca9e254984d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467463"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Kubernetes podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)

Przemieszcza się tworzenia aplikacji na podejście oparte na kontenerach, ważne jest trzeba zorganizować zasoby i zarządzać nimi. Kubernetes to wiodąca platforma, zapewniająca zapewnia niezawodne planowania obciążeń odpornej na uszkodzenia aplikacji. Usługa Azure Kubernetes Service (AKS) jest zarządzane rozwiązanie Kubernetes ofertą, co dodatkowo upraszcza wdrażanie aplikacji opartych na kontenerach i zarządzanie nimi.

W tym artykule przedstawiono podstawowe składniki infrastruktury usługi Kubernetes *klastra głównego*, *węzłów*, i *pule węzłów*. Obciążenie zasobów, takich jak *zasobników*, *wdrożeń*, i *ustawia* również zostaną wprowadzone wraz z jak grupy zasobów do *przestrzenie nazw*.

## <a name="what-is-kubernetes"></a>Co to jest rozwiązanie Kubernetes?

Kubernetes to szybko zmieniających sie platforma, która zarządza aplikacjami opartymi na kontenerach i ich skojarzone składniki sieci i magazynu. Koncentruje się na obciążeń aplikacji, a nie podstawowych składników infrastruktury. Usługa Kubernetes zapewnia deklaratywne podejście do wdrożenia, wspierane przez niezawodny zestaw interfejsów API dla operacji zarządzania.

Można skompilować i uruchomić nowoczesnych przenośnych opartych na mikrousługach aplikacji korzystających z usługi Kubernetes, organizowanie i zarządzanie nimi dostępność tych składników aplikacji. Rozwiązanie Kubernetes obsługuje aplikacje stanowe i bezstanowe jako zespoły postęp wdrażania aplikacji opartych na mikrousługach.

Jako otwartej platformy Kubernetes pozwala na tworzenie aplikacji przy użyciu preferowanego języka programowania, systemu operacyjnego, bibliotek lub magistrali komunikatów. Istniejące ciągłej integracji i narzędzi do ciągłego dostarczania (CI/CD) można zintegrować z rozwiązania Kubernetes do planowania i wdrażania wydań.

Usługa Azure Kubernetes Service (AKS) zapewnia zarządzana usługa Kubernetes, która zmniejsza stopień złożoności wdrożenia i podstawowych zadaniach zarządzania znajdziesz w tym koordynowanie uaktualnienia. Wzorce klastra AKS są zarządzane przez platformę Azure i płacić tylko dla węzłów AKS, które uruchamiaj swoje aplikacje. AKS jest zbudowana na aparat typu open source usługi Azure Kubernetes usługi ([aparatu aks][aks-engine]).

## <a name="kubernetes-cluster-architecture"></a>Architektura klastra Kubernetes

Klaster Kubernetes jest podzielony na dwie części:

- *Klaster główny* węzłów zapewnia podstawowe usługi Kubernetes i organizowania obciążeń aplikacji.
- *Węzły* obciążenia aplikacji.

![Składniki głównego i węzeł klastra Kubernetes](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Wzorzec klastra

Podczas tworzenia klastra usługi AKS głównego klastra zostało automatycznie utworzone i skonfigurowane. Ten wzorzec klastra jest dostarczany jako zarządzanych zasobów platformy Azure wynosi co najmniej przez użytkownika. Nie ma żadnych kosztów, dla serwera głównego klastra, tylko węzły, które są częścią klastra AKS.

Głównego klastra obejmuje następujące podstawowe składniki usługi Kubernetes:

- *rozwiązanie kubernetes apiserver* — serwera interfejsu API jest jak podstawowych interfejsów API rozwiązania Kubernetes są widoczne. Ten składnik zapewnia interakcji dla narzędzia do zarządzania, takich jak `kubectl` lub pulpit nawigacyjny platformy Kubernetes.
- *etcd* — do zarządzania stanem klastra Kubernetes i konfiguracji o wysokiej dostępności *etcd* magazyn wartości klucza w ramach rozwiązania Kubernetes.
- *klastra kubernetes w usłudze scheduler* — podczas tworzenia lub aplikacji w skali, harmonogram Określa, jakie węzły można uruchamiać obciążenia i można je uruchomić.
- *Menedżera klastra kubernetes w usłudze kontrolera* -Menedżera kontrolera nadzoruje liczbę mniejszych kontrolerów, wykonujące akcje, takie jak replikowanie zasobników i obsługi operacji węzła.

AKS zapewnia wzorca pojedynczej dzierżawy klastra, za pomocą dedykowanego serwera interfejsu API usługi Scheduler, itp. Definiują liczbę i rozmiar węzłów, a platforma Azure umożliwia skonfigurowanie bezpiecznej komunikacji między klaster główny i węzły. Interakcja z poziomu głównego klastra odbywa się za pośrednictwem interfejsów API rozwiązania Kubernetes, takie jak `kubectl` lub pulpit nawigacyjny platformy Kubernetes.

Ten wzorzec zarządzanego klastra oznacza, że nie jest wymagane do konfigurowania składników, takich jak o wysokiej dostępności *etcd* magazynu, ale również oznacza, że nie masz dostępu do poziomu głównego klastra bezpośrednio. Uaktualnienia do rozwiązania Kubernetes są zarządzane za pośrednictwem wiersza polecenia platformy Azure lub w witrynie Azure portal i uaktualnia poziomu głównego klastra, a następnie węzły. Aby rozwiązać problemy, możesz przejrzeć dzienniki głównego klastra za pomocą dzienników usługi Azure Monitor.

Jeśli musisz skonfigurować główny klastra w określony sposób lub potrzebujesz bezpośredni dostęp do nich, możesz wdrożyć własnego klastra Kubernetes za pomocą [aparatu aks][aks-engine].

Najlepsze rozwiązania dotyczące skojarzone, zobacz [najlepsze rozwiązania dotyczące zabezpieczeń klastra i uaktualnień w usłudze AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Węzły i pule węzłów

Można uruchamiać aplikacje i usługi pomocnicze, potrzebujesz rozwiązania Kubernetes *węzła*. Klaster AKS ma co najmniej jeden węzeł, czyli maszynie wirtualnej platformy Azure (VM) składników węzła usługi Kubernetes i środowisko uruchomieniowe kontenera:

- `kubelet` Agent rozwiązania Kubernetes, który przetwarza żądania aranżacji z klastra głównego i planowania uruchomionych kontenerów żądanej.
- Praca w sieci wirtualnej jest obsługiwany przez *serwera proxy klastra kubernetes w usłudze* w każdym węźle. Tras serwera proxy ruchu sieciowego i zarządza adresowania IP dla usług i zasobników.
- *Kontener środowiska uruchomieniowego* jest składnikiem, który umożliwia konteneryzowanych aplikacji uruchomić i korzystać z dodatkowych zasobów, takich jak sieci wirtualnej i magazynu. W usłudze AKS Moby służy jako kontener środowiska uruchomieniowego.

![Maszyna wirtualna platformy Azure i zasoby pomocnicze dla węzła rozwiązania Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Rozmiar maszyny Wirtualnej platformy Azure dla węzłów definiuje liczbę procesorów CPU, ilości pamięci i rozmiar i typ magazynu (takich jak SSD o wysokiej wydajności lub regularnych HDD). Jeśli przewidujesz potrzebę aplikacje, które wymagają dużej ilości procesora CPU i pamięci lub magazynu o wysokiej wydajności, należy odpowiednio zaplanować rozmiar węzła. Możesz również skalować w górę liczby węzłów w klastrze usługi AKS w taki sposób, aby zaspokajać zapotrzebowanie.

W usłudze AKS obraz maszyny Wirtualnej dla węzłów w klastrze opiera się obecnie w systemie Ubuntu Linux. Podczas tworzenia klastra usługi AKS, lub skalować liczbę węzłów, platforma Azure tworzy żądana liczba maszyn wirtualnych i konfiguruje je. Brak ręcznej konfiguracji służących do wykonywania.

Jeśli musisz użyć innego hosta, system operacyjny, środowisko uruchomieniowe kontenera, lub Uwzględnij niestandardowe pakiety, możesz wdrożyć własnego klastra Kubernetes za pomocą [aparatu aks][aks-engine]. Poprzednie `aks-engine` wydania funkcji i udostępnia opcje konfiguracji, zanim są oficjalnie obsługiwane w klastrach usługi AKS. Na przykład, jeśli chcesz używać kontenerów Windows lub innej niż Moby kontener środowiska uruchomieniowego, można użyć `aks-engine` pozwalają skonfigurować i wdrożyć klaster usługi Kubernetes, który spełnia Twoje wymagania bieżącej.

### <a name="resource-reservations"></a>Rezerwacji zasobu

Nie trzeba zarządzać podstawowych składników platformy Kubernetes w każdym węźle, takich jak *agenta kubelet*, *serwera proxy klastra kubernetes w usłudze*, i *klastra kubernetes w usłudze dns*, ale niektóre z dostępnych używają oni zasoby obliczeniowe. Aby zachować węzeł wydajności i funkcjonalności, następujące zasoby obliczeniowe są zarezerwowane w każdym węźle:

- **Procesor CPU** — 60 ms
- **Pamięć** -20% maksymalnie 4 GiB

Te zastrzeżenia oznacza, że ilość dostępnych procesora CPU i pamięci dla aplikacji może pojawić się mniej niż sam węzeł zawiera. W przypadku ograniczeń zasobów z powodu liczby aplikacje, których te zastrzeżenia upewnij się, procesora CPU, a pamięć pozostają dostępne dla podstawowych składników platformy Kubernetes. Nie można zmienić rezerwacji zasobów.

Na przykład:

- **Standardowa DS2, wersja 2** rozmiar węzła zawiera 2 procesorów vCPU i 7 GiB pamięci
    - 20% 7 pamięci GiB = 1,4 GiB
    - Daje w sumie *(7-1.4) = 5.6 GiB* pamięci dostępnej dla węzła
    
- **Standardowa E4s v3** rozmiar węzła zawiera 4 procesory vCPU i 32 GiB pamięci
    - 20% pamięci 32 GiB = 6.4 GiB, ale AKS tylko rezerwuje maksymalnie 4 GiB
    - Daje w sumie *(32-4) = 28 GiB* jest dostępna dla węzła
    
Węzeł podstawowy system operacyjny wymaga również pewien stopień zasobów Procesora i pamięci, aby przeprowadzić jego własnej funkcji podstawowych.

Najlepsze rozwiązania dotyczące skojarzone, zobacz [najlepsze rozwiązania dotyczące harmonogramu podstawowe funkcje w usłudze AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Pule węzłów

Węzły o tej samej konfiguracji są grupowane w *pule węzłów*. Klaster usługi Kubernetes zawiera co najmniej jedną pulę węzłów. Początkowa liczba węzłów i rozmiar zdefiniowanych podczas tworzenia klastra usługi AKS, która tworzy *domyślnej puli węzeł*. Ta pula węzłów domyślną w usłudze AKS zawiera podstawowych maszyn wirtualnych, systemem węzły agenta.

Podczas uaktualniania klastra usługi AKS lub skalować wykonywania akcji względem domyślnej puli węzeł. Dla operacji uaktualniania uruchomione kontenery są planowane w innych węzłach w puli węzeł, do momentu uaktualnienia wszystkich węzłów pomyślnie.

## <a name="pods"></a>Zasobników

Korzysta z rozwiązania Kubernetes *zasobników* w celu uruchomienia wystąpienia aplikacji. Zasobnik reprezentuje pojedyncze wystąpienie aplikacji. Zasobników zazwyczaj mają mapowanie 1:1 z kontenerem, mimo że dostępne są zaawansowane scenariusze gdzie zasobnik może zawierać wiele kontenerów. Te zasobniki wielokontenerowych są planowane wspólnie w tym samym węźle i umożliwić kontenerów, aby udostępnić powiązane zasoby.

Kiedy tworzysz zasobnik, można zdefiniować *limity zasobów* żądania pewna ilość zasobów procesora CPU lub pamięci. Harmonogram Kubernetes próbuje zaplanować zasobników do uruchamiania w węźle z dostępnych zasobów w celu spełnienia żądania. Można również określić limity zasobów maksymalna, które uniemożliwiają pod danym zużywa zbyt dużej ilości zasobów obliczeniowych, z węzła podstawowego. Najlepszym rozwiązaniem jest uwzględnienie limity zasobów dla wszystkich zasobników ułatwiające harmonogramu Kubernetes, zrozumieć, jakie zasoby są potrzebne i dozwolonych.

Aby uzyskać więcej informacji, zobacz [zasobników] [ kubernetes-pods] i [zasobnika Kubernetes cyklu życia][kubernetes-pod-lifecycle].

Zasobnik jest zasobem logiczne, ale są kontenerów, których uruchamianie obciążeń aplikacji. Zasobników są zazwyczaj tymczasowych, możliwe do likwidacji zasobów i indywidualnie zaplanowane zasobników przeoczyć niektórych funkcji wysokiej dostępności i nadmiarowości udostępnia rozwiązania Kubernetes. Zamiast tego zasobników są zwykle wdrażane i zarządzane przez rozwiązania Kubernetes *kontrolerów*, takich jak kontroler wdrożenia.

## <a name="deployments-and-yaml-manifests"></a>Manifesty wdrożenia i YAML

A *wdrożenia* reprezentuje co najmniej jeden zasobników identyczne, zarządzana przez kontroler wdrożenia rozwiązania Kubernetes. Wdrożenie definiuje liczbę *replik* (zasobników), aby utworzyć, i harmonogram Kubernetes zapewnia, że w razie wystąpienia problemów zasobników albo przez węzły dodatkowe zasobniki zaplanowano na węzłów w dobrej kondycji.

Możesz zaktualizować wdrożeń, aby zmienić konfigurację zasobników, obraz kontenera używane lub pamięć masowa. Kontroler wdrożenia wstrzymanie i kończy się określoną liczbę replik, tworzy repliki z nową definicję wdrożenia i kontynuuje ten proces, dopóki wszystkie repliki we wdrożeniu są aktualizowane.

Większość aplikacji bezstanowych w usłudze AKS należy używać w modelu wdrażania, a nie poszczególnych zasobników planowania. Kubernetes można monitorować kondycję i stan wdrożenia, aby upewnić się, że wymagane liczby replik uruchamiane w ramach klastra. Jeśli określisz tylko poszczególne zasobników, zasobników nie są uruchamiane ponownie, jeśli wystąpi problem i nie są ponownie zaplanować na węzłów w dobrej kondycji, jeśli ich bieżącego węzła napotka problem.

Jeśli aplikacja wymaga kworum wystąpienia będą zawsze dostępne dla zarządzania decyzji w zakresie, które nie mają procesu aktualizacji w celu zakłócania tej możliwości. *Zasobnik budżetów przerw w działaniu* może służyć do definiowania, jak wiele replik w ramach wdrożenia mogą być podejmowane podczas uaktualniania aktualizacji lub węzeł w dół. Na przykład, jeśli masz *5* replik w danym wdrożeniu, można zdefiniować zakłócenia zasobnika *4* pozwalającą na tylko jedną replikę jako usunięte/zmienił czas w danym momencie. Jako z limitami zasobów zasobników, najlepszym rozwiązaniem jest określenie budżetów przerw w działaniu pod nad aplikacjami, które wymagają minimalna liczba replik zawsze być obecne.

Wdrożenia są zazwyczaj tworzone i zarządzane przy użyciu `kubectl create` lub `kubectl apply`. Aby utworzyć wdrożenie, należy zdefiniować plik manifestu w formacie YAML (YAML Ain't Markup Language). Poniższy przykład tworzy podstawowego wdrożenia serwera internetowego NGINX. Wdrożenie Określa *3* replik, które ma zostać utworzony, a ten port *80* otwartości w kontenerze. Żądania zasobów i limity również są definiowane dla procesora CPU i pamięci.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Bardziej złożone aplikacje mogą być tworzone przez dotyczy to również usług, takich jak moduły równoważenia obciążenia w manifeście YAML.

Aby uzyskać więcej informacji, zobacz [wdrożenia rozwiązania Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Zarządzanie pakietami za pomocą narzędzia Helm

Typowym podejściem do zarządzania aplikacjami w usłudze Kubernetes jest [Helm][helm]. Możesz tworzyć i użyć istniejącego publicznego Helm *wykresy* Kubernetes YAML manifestów wdrażać zasoby i zawierających spakowana utworzona wersja kodu aplikacji. Tych wykresów rozwiązania Helm mogą być przechowywane lokalnie lub często w repozytorium zdalnym, takich jak [repozytorium wykresu Helm rejestru kontenera platformy Azure][acr-helm].

Aby użyć narzędzia Helm, wywołuje składnik serwera *Tiller* jest zainstalowany w klastrze Kubernetes. Tiller służy do zarządzania instalacją wykresy w klastrze. Narzędzia Helm komputerze klienckim jest zainstalowany lokalnie na komputerze lub mogą być używane w [usługi Azure Cloud Shell][azure-cloud-shell]. Można wyszukać lub tworzenie wykresów rozwiązania Helm przy użyciu klienta, a następnie zainstaluj je z klastrem Kubernetes.

![Polecenie Helm zawiera składnik klienta, jak i składnika Tiller po stronie serwera, która tworzy zasoby znajdujące się w klastrze Kubernetes](media/concepts-clusters-workloads/use-helm.png)

Aby uzyskać więcej informacji, zobacz [instalowanie aplikacji za pomocą narzędzia Helm w usłudze Azure Kubernetes Service (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets i DaemonSets

Kontroler wdrożenie używa harmonogramu Kubernetes do uruchomienia danej liczby replik na dowolny dostępny węzeł z dostępnych zasobów. Przy użyciu wdrożeń to podejście może być wystarczający dla aplikacji bezstanowych, ale nie dla aplikacji, które wymagają stałe konwencją nazewnictwa lub magazyn. Dla aplikacji wymagających repliki ma ich na każdym węźle lub wybranych węzłów w klastrze kontroler wdrożenia nie Przyjrzyj się jak repliki są rozproszone na węzłach.

Istnieją dwa zasoby platformy Kubernetes, które umożliwiają zarządzanie tych typów aplikacji:

- *StatefulSets* — Obsługa stan aplikacji poza cyklu życia poszczególnych zasobników, takie jak magazyn.
- *DaemonSets* — upewnij się, w każdym węźle, wcześnie w procesie ładowania początkowego rozwiązania Kubernetes uruchomionego wystąpienia.

### <a name="statefulsets"></a>StatefulSets

Nowoczesnego projektowania aplikacji często ma dla aplikacji bezstanowych, ale *StatefulSets* może służyć do aplikacji stanowych, takich jak aplikacje, które zawierają składniki bazy danych. StatefulSet jest podobny do wdrożenia, w tym przynajmniej jednej identycznych zasobników są tworzone i zarządzane. Repliki w StatefulSet postępuj zgodnie z łagodne sekwencyjne podejścia do wdrożenia, skalowanie, uaktualnienia i zakończeń. Za pomocą StatefulSet konwencji nazewnictwa, nazwy sieci i magazynu są zachowywane jako repliki są zaplanowane ponownie.

Definiowanie aplikacji przy użyciu formatu YAML `kind: StatefulSet`, i kontroler StatefulSet obsługuje wdrażanie i zarządzanie nimi wymagane replik. Dane są zapisywane w magazynie trwałym, udostępniane przez usługę Azure Managed Disks lub usługi Azure Files. StatefulSets podstawowego magazynu trwałego nie będzie nawet gdy StatefulSet zostanie usunięty.

Aby uzyskać więcej informacji, zobacz [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliki w StatefulSet są zaplanowane i uruchamiane na dowolny dostępny węzeł w klastrze AKS. Jeśli potrzebujesz upewnić się, że co najmniej jeden zasobnik w twoim zestawie działa w węźle, należy użyć DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Dla kolekcji w określonym dzienniku lub potrzeby w zakresie monitorowania może być konieczne uruchomienie pod danym na wszystkich, lub wybrane węzły. A *DaemonSet* ponownie służy do wdrażania jednego lub więcej identycznych zasobników, ale kontroler DaemonSet sprawia, że każdy węzeł określony uruchomione jest wystąpienie zasobnik.

Kontroler DaemonSet można zaplanować zasobników w węzłach na wczesnym etapie procesu rozruchu klastra przed domyślne Kubernetes usługi scheduler została uruchomiona. Tę możliwość gwarantuje, że zasobników w DaemonSet są uruchamiane przed tradycyjnych zasobników we wdrożeniu lub StatefulSet są zaplanowane.

Podobnie jak StatefulSets, DaemonSet jest zdefiniowany jako część definicji YAML przy użyciu `kind: DaemonSet`.

Aby uzyskać więcej informacji, zobacz [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Jeśli przy użyciu [dodatek wirtualnych węzłów](virtual-nodes-cli.md#enable-virtual-nodes-addon), DaemonSets nie spowoduje utworzenie zasobników na wirtualny węzeł.

## <a name="namespaces"></a>Przestrzenie nazw

Zasoby platformy Kubernetes, takich jak zasobników i wdrożenia, są logicznie pogrupowane w *przestrzeni nazw*. Te grupy umożliwiają logicznie Podziel klaster AKS i ograniczanie dostępu do tworzenia, wyświetlania lub zarządzania zasobami. Można utworzyć przestrzeni nazw do oddzielania grup biznesowych, na przykład. Użytkownicy mogą komunikować się tylko z zasobów w ramach ich przypisanej przestrzenie nazw.

![Przestrzenie nazw Kubernetes, logicznego podziału zasobów i aplikacji](media/concepts-clusters-workloads/namespaces.png)

Podczas tworzenia klastra usługi AKS, dostępne są następujące przestrzenie nazw:

- *domyślne* — ta przestrzeń nazw jest, gdzie zasobników i wdrożenia są tworzone domyślnie w przypadku braku podania. W mniejszych środowiskach bez tworzenia dodatkowego odejścia logiczne można wdrożyć aplikacji bezpośrednio do domyślnej przestrzeni nazw. Podczas interakcji z interfejsem API rozwiązania Kubernetes, takich jak z `kubectl get pods`, domyślny obszar nazw jest używana, gdy nie jest określona.
- *rozwiązanie kubernetes — system* — ta przestrzeń nazw jest, jeśli istnieją podstawowych zasobów, takich jak funkcje sieciowe, takie jak DNS i serwera proxy lub pulpit nawigacyjny platformy Kubernetes. Zazwyczaj nie są wdrażane aplikacji do tej przestrzeni nazw.
- *publiczne klastra kubernetes w usłudze* — w tej przestrzeni nazw jest zwykle nie są używane, ale może być używana dla zasobów być widoczne w obrębie całego klastra i mogą zostać przejrzane przez dowolnego użytkownika.

Aby uzyskać więcej informacji, zobacz [przestrzeni nazw Kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono niektóre z podstawowych składników platformy Kubernetes i sposobów ich zastosowania w klastrach usługi AKS. Dodatkowe informacje na temat podstawowej platformy Kubernetes oraz pojęcia zostały przedstawione z usługi AKS zobacz następujące artykuły:

- [Kubernetes dostępu w usłudze AKS i zarządzania tożsamościami][aks-concepts-identity]
- [Kubernetes / zabezpieczeń usługi AKS][aks-concepts-security]
- [Kubernetes / sieci wirtualne w usłudze AKS][aks-concepts-network]
- [Kubernetes / magazynu usługi AKS][aks-concepts-storage]
- [Kubernetes / skalowanie usługi AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md

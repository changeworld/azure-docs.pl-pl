---
title: Pojęcia — podstawy kubernetes dla usług Kubernetes platformy Azure (AKS)
description: Poznaj podstawowe składniki klastra i obciążenia usługi Kubernetes oraz ich związek z funkcjami usługi Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: bcf56aa89a42d65fdb7bf03696faad13c64cbc8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259645"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Podstawowe pojęcia kubernetes dla usługi Azure Kubernetes Service (AKS)

W miarę jak tworzenie aplikacji przechodzi w kierunku podejścia opartego na kontenerach, ważna jest potrzeba organizowania i zarządzania zasobami. Kubernetes jest wiodącą platformą, która zapewnia możliwość zapewnienia niezawodnego planowania obciążeń aplikacji odpornych na uszkodzenia. Usługa Azure Kubernetes Service (AKS) to zarządzana usługa Kubernetes, która dodatkowo upraszcza wdrażanie aplikacji oparte na kontenerach i zarządzanie nimi.

W tym artykule przedstawiono podstawowe składniki infrastruktury kubernetes, takie jak *płaszczyzna sterowania,* *węzły*i *pule węzłów*. Zasoby obciążenia, takie jak *zasobniki,* *wdrożenia*i *zestawy,* są również wprowadzane, wraz z instrukcjami grupowanie zasobów w *przestrzenie nazw*.

## <a name="what-is-kubernetes"></a>Co to jest Kubernetes?

Kubernetes to szybko rozwijająca się platforma, która zarządza aplikacjami opartymi na kontenerach i skojarzonymi z nimi składnikami sieci i magazynu. Nacisk kładzie się na obciążeń aplikacji, a nie podstawowych składników infrastruktury. Usługa Kubernetes zapewnia deklaratywne podejście do wdrożeń, poparte niezawodnym zestawem interfejsów API dla operacji zarządzania.

Można tworzyć i uruchamiać nowoczesne, przenośne aplikacje oparte na mikrousługach, które korzystają z organizowania aplikacji Kubernetes i zarządzania dostępnością tych składników aplikacji. Usługa Kubernetes obsługuje aplikacje bezstanowe i stanowe w miarę postępów zespołów w zakresie wdrażania aplikacji opartych na mikrousługach.

Jako otwarta platforma, Kubernetes umożliwia tworzenie aplikacji przy ulubionym języku programowania, systemu operacyjnego, bibliotek lub magistrali obsługi wiadomości. Istniejące narzędzia ciągłej integracji i ciągłego dostarczania (CI/CD) mogą integrować się z programem Kubernetes w celu planowania i wdrażania wydań.

Usługa Azure Kubernetes Service (AKS) zapewnia zarządzaną usługę Kubernetes, która zmniejsza złożoność zadań wdrażania i podstawowych zadań zarządzania, w tym koordynowania uaktualnień. Płaszczyzna sterowania AKS jest zarządzana przez platformę Azure i płacisz tylko za węzły AKS, które uruchamiają aplikacje. AKS jest zbudowany na szczycie open-source Azure Kubernetes Service Engine[(aks-engine).][aks-engine]

## <a name="kubernetes-cluster-architecture"></a>Architektura klastra Kubernetes

Klaster Kubernetes jest podzielony na dwa składniki:

- *Węzły płaszczyzny sterowania* zapewniają podstawowe usługi Kubernetes i aranżację obciążeń aplikacji.
- *Węzły* uruchamiają obciążenia aplikacji.

![Komponenty płaszczyzny i węzła kubernetes](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Płaszczyzna sterowania

Podczas tworzenia klastra AKS płaszczyzna sterowania jest automatycznie tworzona i konfigurowana. Ta płaszczyzna sterowania jest dostarczana jako zarządzany zasób platformy Azure wyodrębniony od użytkownika. Nie ma żadnych kosztów dla płaszczyzny sterowania, tylko węzły, które są częścią klastra AKS.

Płaszczyzna sterowania zawiera następujące podstawowe komponenty Kubernetes:

- *kube-apiserver* — serwer interfejsu API jest jak podstawowe interfejsy API kubernetes są udostępniane. Ten składnik zapewnia interakcję dla narzędzi `kubectl` do zarządzania, takich jak lub pulpit nawigacyjny Kubernetes.
- *etcd* - Aby utrzymać stan klastra i konfiguracji kubernetes, wysoce dostępne *etcd* jest kluczem magazynu wartości w kubernetes.
- *kube-scheduler* — podczas tworzenia lub skalowania aplikacji harmonogram określa, które węzły można uruchomić obciążenia i uruchamia je.
- *kube-controller-manager* — Menedżer kontrolerów nadzoruje szereg mniejszych kontrolerów, które wykonują akcje, takie jak replikowanie zasobników i obsługa operacji węzłów.

Usługa AKS zapewnia płaszczyznę sterowania z jedną dzierżawą z dedykowanym serwerem interfejsu API, harmonogramem itp. Można zdefiniować liczbę i rozmiar węzłów, a platforma Azure konfiguruje bezpieczną komunikację między płaszczyzną sterowania a węzłami. Interakcja z płaszczyzną sterowania odbywa się za `kubectl` pośrednictwem interfejsów API kubernetes, takich jak lub pulpit nawigacyjny Kubernetes.

Ta zarządzana płaszczyzna sterowania oznacza, że nie trzeba konfigurować komponentów, takich jak magazyn *etcd* o wysokiej dostępności, ale oznacza to również, że nie można uzyskać bezpośredniego dostępu do płaszczyzny sterowania. Uaktualnienia do usługi Kubernetes są aranżowane za pośrednictwem interfejsu wiersza polecenia platformy Azure lub witryny Azure portal, która uaktualnia płaszczyznę sterowania, a następnie węzły. Aby rozwiązać możliwe problemy, można przejrzeć dzienniki płaszczyzny sterowania za pośrednictwem dzienników usługi Azure Monitor.

Jeśli chcesz skonfigurować płaszczyznę sterowania w określony sposób lub potrzebujesz do niej bezpośredniego dostępu, możesz wdrożyć własny klaster Kubernetes przy użyciu [aks-engine][aks-engine].

Aby uzyskać skojarzone najlepsze rozwiązania, zobacz [Najważniejsze wskazówki dotyczące zabezpieczeń klastra i uaktualnień w u. AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Węzły i pule węzłów

Do uruchamiania aplikacji i usług pomocniczych potrzebny jest *węzeł*Kubernetes . Klaster usługi AKS ma co najmniej jeden węzeł, czyli maszynę wirtualną platformy Azure , która uruchamia składniki węzła Kubernetes i środowisko wykonawcze kontenera:

- Jest `kubelet` agentem Kubernetes, który przetwarza żądania aranżacji z płaszczyzny sterowania i planowania uruchamiania żądanych kontenerów.
- Sieci wirtualne są obsługiwane przez *serwer proxy kube* w każdym węźle. Serwer proxy kieruje ruch sieciowy i zarządza adresem IP usług i zasobników.
- Środowisko *uruchomieniowe kontenera* to składnik, który umożliwia aplikacjom konteneryzowanym uruchamianie i interakcję z dodatkowymi zasobami, takimi jak sieć wirtualna i magazyn. W Usłudze AKS Moby jest używany jako środowisko uruchomieniowe kontenera.

![Maszyna wirtualna platformy Azure i zasoby pomocnicze dla węzła Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Rozmiar maszyny Wirtualnej platformy Azure dla węzłów definiuje liczbę procesorów, ilość pamięci oraz rozmiar i typ dostępnego magazynu (na przykład dysk SSD o wysokiej wydajności lub zwykły dysk twardy). Jeśli przewidujesz potrzebę aplikacji, które wymagają dużych ilości procesora CPU i pamięci lub magazynu o wysokiej wydajności, należy odpowiednio zaplanować rozmiar węzła. Można również skalować w górę liczbę węzłów w klastrze AKS, aby zaspokoić zapotrzebowanie.

W usłudze AKS obraz maszyny Wirtualnej dla węzłów w klastrze jest obecnie oparty na Ubuntu Linux lub Windows Server 2019. Podczas tworzenia klastra AKS lub skalowania w górę liczby węzłów platforma Azure tworzy żądaną liczbę maszyn wirtualnych i konfiguruje je. Nie ma ręcznej konfiguracji do wykonania. Węzły agenta są rozliczane jako standardowe maszyny wirtualne, więc wszelkie rabaty dotyczące używanego rozmiaru maszyny wirtualnej (w tym [rezerwacji platformy Azure)][reservation-discounts]są automatycznie stosowane.

Jeśli chcesz użyć innego systemu operacyjnego hosta, środowiska wykonawczego kontenera lub dołączyć pakiety niestandardowe, możesz wdrożyć własny klaster Kubernetes przy użyciu [aks-engine][aks-engine]. Nadrzędny `aks-engine` zwalnia funkcje i udostępnia opcje konfiguracji, zanim zostaną oficjalnie obsługiwane w klastrach AKS. Na przykład jeśli chcesz użyć środowiska uruchomieniowego kontenera innego `aks-engine` niż Moby, można użyć do skonfigurowania i wdrożenia klastra Kubernetes, który spełnia bieżące potrzeby.

### <a name="resource-reservations"></a>Rezerwacje zasobów

Zasoby węzłów są używane przez usługi AKS, aby funkcja węzła jako część klastra. Może to spowodować rozbieżność między całkowitymi zasobami węzła a zasobami allocatable, gdy są używane w uzywki AKS. Jest to ważne, aby pamiętać podczas ustawiania żądań i limitów dla użytkowników wdrożonych zasobników.

Aby znaleźć zasoby allocatable węzła uruchomić:
```kubectl
kubectl describe node [NODE_NAME]

```

Aby zachować wydajność i funkcjonalność węzła, zasoby są zarezerwowane w każdym węźle przez AKS. W miarę powiększania się węzła w zasobach rezerwacja zasobów rośnie z powodu większej ilości wdrożonych zasobników użytkowników wymagających zarządzania.

>[!NOTE]
> Za pomocą dodatków AKS, takich jak Container Insights (OMS) zużywają dodatkowe zasoby węzła.

- **CPU** - zarezerwowany procesor jest zależny od typu węzła i konfiguracji klastra, co może spowodować mniej allocatable CPU ze względu na uruchomienie dodatkowych funkcji

| Rdzenie procesora na hoście | 1 | 2 | 4 | 8 | 16 | 32|64|
|---|---|---|---|---|---|---|---|
|Kube zastrzeżone (millicores)|60|100|140|180|260|420|740|

- **Pamięć** — pamięć wykorzystywana przez AKS zawiera sumę dwóch wartości.

1. Demon kubelet jest zainstalowany na wszystkich węzłach agenta Kubernetes do zarządzania tworzeniem i zakończeniem kontenera. Domyślnie w przypadku AKS ten demon ma następującą regułę eksmisji: *memory.available<750Mi*, co oznacza, że węzeł musi zawsze mieć co najmniej 750 Mi allocatable przez cały czas.  Gdy host jest poniżej tego progu dostępnej pamięci, kubelet zakończy jeden z uruchomionych zasobników, aby zwolnić pamięć na komputerze hosta i chronić go. Jest to działanie reaktywne, gdy dostępna pamięć zmniejsza się powyżej progu 750Mi.

2. Drugą wartością jest progresywna szybkość rezerwacji pamięci dla demona kubelet poprawnie działać (kube-reserved).
    - 25% z pierwszych 4 GB pamięci
    - 20% z następnych 4 GB pamięci (do 8 GB)
    - 10% z następnych 8 GB pamięci (do 16 GB)
    - 6% z następnych 112 GB pamięci (do 128 GB)
    - 2% pamięci powyżej 128 GB

Powyższe reguły alokacji pamięci i procesora CPU są używane do utrzymania węzłów agenta w dobrej kondycji, w tym niektóre zasobniki systemu hostingu, które mają kluczowe znaczenie dla kondycji klastra. Te reguły alokacji również spowodować węzeł do raportu mniej allocatable pamięci i procesora CPU, niż byłoby, gdyby nie były częścią klastra Kubernetes. Powyższych rezerwacji zasobów nie można zmienić.

Na przykład jeśli węzeł oferuje 7 GB, zgłosi 34% pamięci nie allocatable na szczycie progu eksmisji twardej 750Mi.

`(0.25*4) + (0.20*3) = + 1 GB + 0.6GB = 1.6GB / 7GB = 22.86% reserved`

Oprócz rezerwacji dla samego kubernetes, podstawowy węzeł system operacyjny rezerwuje również ilość zasobów procesora CPU i pamięci do obsługi funkcji systemu operacyjnego.

Aby zapoznać się z skojarzonymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące podstawowych funkcji harmonogramu w ustroju AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Pule węzłów

Węzły tej samej konfiguracji są zgrupowane w *pulę węzłów*. Klaster kubernetes zawiera co najmniej jedną pulę węzłów. Początkowa liczba węzłów i rozmiar są definiowane podczas tworzenia klastra AKS, który tworzy *domyślną pulę węzłów*. Ta domyślna pula węzłów w uzywniu AKS zawiera podstawowe maszyny wirtualne, które uruchamiają węzły agenta.

> [!NOTE]
> Aby upewnić się, że klaster działa niezawodnie, należy uruchomić co najmniej 2 (dwa) węzły w domyślnej puli węzłów.

Podczas skalowania lub uaktualniania klastra AKS akcja jest wykonywana względem domyślnej puli węzłów. Można również skalować lub uaktualniać określoną pulę węzłów. W przypadku operacji uaktualniania uruchomione kontenery są planowane w innych węzłach w puli węzłów, dopóki wszystkie węzły nie zostaną pomyślnie uaktualnione.

Aby uzyskać więcej informacji na temat używania wielu pul węzłów w ukasza, zobacz [Tworzenie wielu pul węzłów dla klastra w ucho.][use-multiple-node-pools]

### <a name="node-selectors"></a>Selektory węzłów

W klastrze usługi AKS, który zawiera wiele pul węzłów, może być konieczne poinformowanie Harmonogramu kubernetes, która pula węzłów ma być używana dla danego zasobu. Na przykład kontrolery transferu danych przychodzących nie powinny być uruchamiane w węzłach systemu Windows Server (obecnie w wersji zapoznawczej w usłudze AKS). Selektory węzłów umożliwiają definiowanie różnych parametrów, takich jak węzeł system operacyjny, w celu kontrolowania, gdzie należy zaplanować zasobnik.

Poniższy podstawowy przykład planuje wystąpienie NGINX w węźle Systemu Linux przy użyciu selektora węzłów *"beta.kubernetes.io/os": linux:*

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Aby uzyskać więcej informacji na temat kontrolowania, gdzie zasobniki są zaplanowane, zobacz [Najważniejsze wskazówki dotyczące zaawansowanych funkcji harmonogramu w AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Strąków

Kubernetes używa *zasobników* do uruchomienia wystąpienia aplikacji. Zasobnik reprezentuje pojedyncze wystąpienie aplikacji. Zasobników zazwyczaj mają mapowanie 1:1 z kontenerem, chociaż istnieją zaawansowane scenariusze, w których zasobnik może zawierać wiele kontenerów. Te zasobniki wielu kontenerów są zaplanowane razem w tym samym węźle i umożliwiają kontenerom udostępnianie powiązanych zasobów.

Podczas tworzenia zasobnika można zdefiniować *żądania zasobów,* aby zażądać określonej ilości zasobów procesora CPU lub pamięci. Harmonogram Kubernetes próbuje zaplanować zasobników do uruchomienia w węźle z dostępnych zasobów, aby spełnić żądanie. Można również określić maksymalne limity zasobów, które uniemożliwiają danego zasobu obliczeniowego z węzła źródłowego. Najlepszym rozwiązaniem jest uwzględnienie limitów zasobów dla wszystkich zasobników, aby pomóc Harmonogramowi kubernetes zrozumieć, które zasoby są potrzebne i dozwolone.

Aby uzyskać więcej informacji, zobacz [Kubernetes zasobników][kubernetes-pods] i [Kubernetes pod cyklem życia][kubernetes-pod-lifecycle].

Zasobnik jest zasobem logicznym, ale kontenery są tam, gdzie uruchamiane są obciążenia aplikacji. Zasobniki są zazwyczaj efemeryczne, jednorazowe zasoby i indywidualnie zaplanowane zasobników pominąć niektóre z wysokiej dostępności i nadmiarowości funkcje Kubernetes zapewnia. Zamiast tego zasobników są zwykle wdrażane i zarządzane przez *kontrolery*Kubernetes , takich jak kontroler wdrażania.

## <a name="deployments-and-yaml-manifests"></a>Wdrożenia i manifesty YAML

*Wdrożenie* reprezentuje jeden lub więcej identycznych zasobników, zarządzanych przez kontroler wdrażania kubernetes. Wdrożenie definiuje liczbę *replik* (zasobników) do utworzenia, a Harmonogram kubernetes zapewnia, że jeśli zasobniki lub węzły napotkają problemy, dodatkowe zasobniki są planowane w węzłach w dobrej kondycji.

Można zaktualizować wdrożenia, aby zmienić konfigurację zasobników, obrazu kontenera używanego lub dołączonego magazynu. Kontroler wdrażania wypływa i kończy daną liczbę replik, tworzy repliki z nowej definicji wdrożenia i kontynuuje proces, dopóki wszystkie repliki we wdrożeniu nie zostaną zaktualizowane.

Większość aplikacji bezstanowych w uzywu usługi AKS należy używać modelu wdrażania, a nie planowania poszczególnych zasobników. Usługa Kubernetes może monitorować kondycję i stan wdrożeń, aby upewnić się, że wymagana liczba replik jest uruchamiana w klastrze. Podczas planowania tylko poszczególnych zasobników, zasobników nie są uruchamiane ponownie, jeśli napotkają problem i nie są przełożone na węzły w dobrej kondycji, jeśli ich bieżący węzeł napotka problem.

Jeśli aplikacja wymaga kworum wystąpień, które zawsze są dostępne dla decyzji zarządzania, które mają być podejmowane, nie chcesz, aby proces aktualizacji zakłócił tę zdolność. *Budżety związane z zakłóceniami zasobników* mogą służyć do definiowania, ile replik we wdrożeniu można zdjąć podczas aktualizacji lub uaktualnienia węzła. Na przykład jeśli masz *5* replik we wdrożeniu, można zdefiniować zakłócenia zasobnika *4,* aby zezwolić tylko na jedną replikę przed usunięciem/przełożonym naraz. Podobnie jak w przypadku limitów zasobów zasobu zasobu zasobu zasobu zasobu zasobu, najlepszym rozwiązaniem jest zdefiniowanie budżetów zakłóceń zasobnika w aplikacjach, które wymagają minimalnej liczby replik, które zawsze są obecne.

Wdrożenia są zazwyczaj tworzone i `kubectl create` `kubectl apply`zarządzane za pomocą programu . Aby utworzyć wdrożenie, należy zdefiniować plik manifestu w formacie YAML (YAML Ain't Markup Language). Poniższy przykład tworzy podstawowe wdrożenie serwera sieci web NGINX. Wdrożenie określa *3* repliki, które mają zostać utworzone, a port *80* będzie otwarty w kontenerze. Żądania zasobów i limity są również zdefiniowane dla procesora CPU i pamięci.

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

Bardziej złożone aplikacje mogą być tworzone przez również w tym usług, takich jak moduły równoważenia obciążenia w manifeście YAML.

Aby uzyskać więcej informacji, zobacz [Wdrożenia kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Zarządzanie pakietami z Helmem

Wspólne podejście do zarządzania aplikacjami w Kubernetes jest z [Helm][helm]. Można tworzyć i używać istniejących publicznych *wykresów* Helm, które zawierają spakowaną wersję kodu aplikacji i manifestów YAML Kubernetes do wdrażania zasobów. Te wykresy Helm mogą być przechowywane lokalnie lub często w zdalnym repozytorium, takim jak [repozytorium wykresu Helm rejestru kontenerów platformy Azure][acr-helm].

Aby użyć helm, składnik serwera o nazwie *Tiller* jest zainstalowany w klastrze Kubernetes. Kultywator zarządza instalacją wykresów w klastrze. Sam klient Helm jest instalowany lokalnie na komputerze lub może być używany w usłudze [Azure Cloud Shell.][azure-cloud-shell] Można wyszukiwać lub tworzyć wykresy Helm z klientem, a następnie zainstalować je w klastrze Kubernetes.

![Helm zawiera składnik klienta i składnik Tiller po stronie serwera, który tworzy zasoby wewnątrz klastra Kubernetes](media/concepts-clusters-workloads/use-helm.png)

Aby uzyskać więcej informacji, zobacz [Instalowanie aplikacji z helmem w usłudze Azure Kubernetes Service (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>Zestawy stanowe i demony

Kontroler wdrażania używa Harmonogramu kubernetes do uruchamiania danej liczby replik w dowolnym dostępnym węźle z dostępnymi zasobami. Takie podejście przy użyciu wdrożeń może być wystarczające dla aplikacji bezstanowych, ale nie dla aplikacji, które wymagają trwałej konwencji nazewnictwa lub magazynu. W przypadku aplikacji, które wymagają repliki istnieć w każdym węźle lub wybranych węzłów, w klastrze, Kontroler wdrażania nie patrzy na sposób dystrybucji replik między węzłami.

Istnieją dwa zasoby kubernetes, które umożliwiają zarządzanie tymi typami aplikacji:

- *StatefulSets* — obsługa stanu aplikacji poza poszczególnych pod cyklu życia, takich jak magazyn.
- *DaemonSets* — upewnij się, że uruchomione wystąpienie w każdym węźle, na początku procesu boottrap Kubernetes.

### <a name="statefulsets"></a>Zestawy stanowe

Nowoczesne tworzenie aplikacji często ma na celu aplikacje bezstanowe, ale *StatefulSets* może służyć do aplikacji stanowych, takich jak aplikacje, które zawierają składniki bazy danych. A StatefulSet jest podobny do wdrożenia w tym jeden lub więcej identycznych zasobników są tworzone i zarządzane. Repliki w StatefulSet wykonaj wdzięczne, sekwencyjne podejście do wdrażania, skalowania, uaktualnień i zakończeń. Z StatefulSet (jak repliki są ponownieplanowane) konwencja nazewnictwa, nazwy sieciowe i magazynu utrzymują.

Aplikacja jest definiowana w `kind: StatefulSet`formacie YAML za pomocą programu , a kontroler StatefulSet obsługuje następnie wdrażanie i zarządzanie wymaganymi replikami. Dane są zapisywane w magazynie trwałym, dostarczane przez dyski zarządzane platformy Azure lub usługi Azure Files. W przypadku statefulsets podstawowej trwałego magazynu pozostaje nawet wtedy, gdy StatefulSet jest usuwany.

Aby uzyskać więcej informacji, zobacz [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliki w StatefulSet są zaplanowane i uruchamiane przez dowolny dostępny węzeł w klastrze AKS. Jeśli musisz upewnić się, że co najmniej jeden zasobnik w zestawie działa w węźle, zamiast tego można użyć DaemonSet.

### <a name="daemonsets"></a>Zestawy demonów

Dla określonych potrzeb zbierania dzienników lub monitorowania może być konieczne uruchomienie danego zasobnika na wszystkich lub wybranych węzłach. Zestaw *demonów* jest ponownie używany do wdrażania jednego lub więcej identycznych zasobników, ale kontroler DaemonSet zapewnia, że każdy określony węzeł uruchamia wystąpienie zasobnika.

Kontroler DaemonSet może planować zasobników w węzłach na wczesnym etapie procesu rozruchu klastra, przed uruchomieniem domyślnego harmonogramu Kubernetes. Ta możliwość zapewnia, że zasobników w DemonSet są uruchamiane przed tradycyjnych zasobników w wdrożenie lub StatefulSet są zaplanowane.

Podobnie jak StatefulSets, DaemonSet jest zdefiniowany jako `kind: DaemonSet`część definicji YAML przy użyciu .

Aby uzyskać więcej informacji, zobacz [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Jeśli używasz [dodatku Węzły wirtualne,](virtual-nodes-cli.md#enable-virtual-nodes-addon)DaemonSets nie utworzy zasobników w węźle wirtualnym.

## <a name="namespaces"></a>Namespaces

Zasoby kubernetes, takie jak zasobniki i wdrożenia, są logicznie pogrupowane w *obszar nazw*. Te grupowania umożliwiają logiczne dzielenie klastra AKS i ograniczanie dostępu do tworzenia, wyświetlania lub zarządzania zasobami. Można na przykład tworzyć przestrzenie nazw w celu oddzielenia grup biznesowych. Użytkownicy mogą wchodzić w interakcje tylko z zasobami w przypisanych im obszarach nazw.

![Obszary nazw kubernetes logicznie dzielą zasoby i aplikacje](media/concepts-clusters-workloads/namespaces.png)

Podczas tworzenia klastra usługi AKS dostępne są następujące przestrzenie nazw:

- *domyślnie* — ta przestrzeń nazw jest miejscem, w którym zasobniki i wdrożenia są tworzone domyślnie, gdy nie podano żadnych. W mniejszych środowiskach można wdrażać aplikacje bezpośrednio w domyślnym obszarze nazw bez tworzenia dodatkowych rozbarwień logicznych. Podczas interakcji z interfejsem API kubernetes, na przykład z `kubectl get pods`, domyślny obszar nazw jest używany, gdy nie jest określony.
- *kube-system* — ta przestrzeń nazw to miejsce, w którym istnieją podstawowe zasoby, takie jak funkcje sieciowe, takie jak DNS i serwer proxy, lub pulpit nawigacyjny kubernetes. Zazwyczaj nie wdrażaj własnych aplikacji w tej przestrzeni nazw.
- *kube-public* — ten obszar nazw zazwyczaj nie jest używany, ale może służyć do zasobów, które mają być widoczne w całym klastrze i mogą być wyświetlane przez dowolnego użytkownika.

Aby uzyskać więcej informacji, zobacz [Obszary nazw kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano niektóre podstawowe składniki kubernetes i jak mają one zastosowanie do klastrów AKS. Aby uzyskać dodatkowe informacje na temat podstawowych pojęć kubernetów i usługi AKS, zobacz następujące artykuły:

- [Dostęp i tożsamość Kubernetes / AKS][aks-concepts-identity]
- [Zabezpieczenia Kubernetes / AKS][aks-concepts-security]
- [Sieci wirtualne Kubernetes / AKS][aks-concepts-network]
- [Kubernetes / AKS pamięci masowej][aks-concepts-storage]
- [Skala Kubernetes / AKS][aks-concepts-scale]

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
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md

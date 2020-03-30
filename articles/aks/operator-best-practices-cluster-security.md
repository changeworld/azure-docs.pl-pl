---
title: Najważniejsze rozwiązania dotyczące operatora — zabezpieczenia klastrów w usługach Kubernetes platformy Azure (AKS)
description: Zapoznaj się z najlepszymi rozwiązaniami operatorów klastra dotyczącymi zarządzania zabezpieczeniami i uaktualnieniami klastra w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f02f6588946e2b63a1a092aba15603d1685e8207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594808"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Najważniejsze wskazówki dotyczące zabezpieczeń klastra i uaktualnień w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) bezpieczeństwo obciążeń i danych jest kluczowym czynnikiem. Szczególnie podczas uruchamiania klastrów z wieloma dzierżawami przy użyciu izolacji logicznej, należy zabezpieczyć dostęp do zasobów i obciążeń. Aby zminimalizować ryzyko ataku, należy również upewnić się, że masz zastosowanie najnowszych aktualizacji zabezpieczeń kubernetes i węzła systemu operacyjnego.

W tym artykule skupiono się na tym, jak zabezpieczyć klaster AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Bezpieczne uzyskiwanie dostępu do serwera interfejsu API za pomocą usługi Azure Active Directory i formantów dostępu opartych na rolach
> * Bezpieczny dostęp kontenera do zasobów węzłów
> * Uaktualnianie klastra AKS do najnowszej wersji usługi Kubernetes
> * Aktualizowanie węzłów i automatyczne stosowanie poprawek zabezpieczeń

Można również zapoznać się z najlepszymi praktykami [w zakresie zarządzania obrazami kontenerów][best-practices-container-image-management] i [zabezpieczeń zasobników][best-practices-pod-security].

Można również użyć [integracji usługi Azure Kubernetes Services z usługą Security Center,][security-center-aks] aby ułatwić wykrywanie zagrożeń i wyświetlanie zaleceń dotyczących zabezpieczania klastrów usługi AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Bezpieczny dostęp do serwera interfejsu API i węzłów klastra

**Wskazówki dotyczące najlepszych rozwiązań** — zabezpieczanie dostępu do serwera API usługi Kubernetes jest jedną z najważniejszych czynności, które można wykonać w celu zabezpieczenia klastra. Zintegruj sterowanie dostępem oparte na rolach usługi Kubernetes (RBAC) z usługą Azure Active Directory w celu kontrolowania dostępu do serwera interfejsu API. Te formanty umożliwiają zabezpieczenie usługi AKS w taki sam sposób, w jaki można zabezpieczyć dostęp do subskrypcji platformy Azure.

Serwer interfejsu API kubernetes zapewnia pojedynczy punkt połączenia dla żądań do wykonywania akcji w klastrze. Aby zabezpieczyć i przeprowadzić inspekcję dostępu do serwera interfejsu API, należy ograniczyć dostęp i zapewnić najmniej uprzywilejowanych uprawnień dostępu wymaganych. To podejście nie jest unikatowe dla usługi Kubernetes, ale jest szczególnie ważne, gdy klaster AKS jest logicznie izolowane do użytku z wieloma dzierżawami.

Usługa Azure Active Directory (AD) zapewnia rozwiązanie do zarządzania tożsamościami gotowe do organizacji, które integruje się z klastrami AKS. Ponieważ kubernetes nie zapewnia rozwiązania do zarządzania tożsamościami, w przeciwnym razie może być trudno zapewnić szczegółowy sposób ograniczania dostępu do serwera interfejsu API. W przypadku klastrów zintegrowanych z usługą Azure AD w usłudze AKS można używać istniejących kont użytkowników i grup do uwierzytelniania użytkowników na serwerze interfejsu API.

![Integracja usługi Azure Active Directory dla klastrów AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Użyj funkcji Kubernetes RBAC i integracji usługi Azure AD, aby zabezpieczyć serwer interfejsu API i podać najmniejszą liczbę uprawnień wymaganych do zestawu zasobów o określonym zakresie, takich jak pojedynczy obszar nazw. Różnych użytkowników lub grup w usłudze Azure AD można przyznać różne role RBAC. Te szczegółowe uprawnienia umożliwiają ograniczenie dostępu do serwera interfejsu API i zapewniają przejrzystą ścieżkę inspekcji wykonywanych akcji.

Zalecaną najlepszą praktyką jest użycie grup w celu zapewnienia dostępu do plików i folderów w porównaniu z poszczególnymi tożsamościami, użyj członkostwa *w grupie* usługi Azure AD, aby powiązać użytkowników z rolami RBAC, a nie z poszczególnymi *użytkownikami.* W miarę zmiany członkostwa w grupie użytkownika ich uprawnienia dostępu do klastra AKS zostaną odpowiednio zmienione. Jeśli powiążesz użytkownika bezpośrednio z rolą, jego funkcja zadania może ulec zmianie. Członkostwa w grupach usługi Azure AD będą aktualizowane, ale uprawnienia do klastra AKS nie będą odzwierciedlać tego. W tym scenariuszu użytkownik kończy się przyznanie więcej uprawnień niż użytkownik wymaga.

Aby uzyskać więcej informacji na temat integracji usługi Azure AD i RBAC, zobacz [Najważniejsze wskazówki dotyczące uwierzytelniania i autoryzacji w usłudze AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Bezpieczny dostęp kontenera do zasobów

**Wskazówki dotyczące najlepszych praktyk** — ogranicz dostęp do akcji, które kontenery mogą wykonywać. Podaj najmniejszą liczbę uprawnień i unikaj używania eskalacji głównej / uprzywilejowanej.

W ten sam sposób, w jaki należy przyznać użytkownikom lub grupuje najmniejszą wymaganą liczbę uprawnień, kontenery powinny być również ograniczone tylko do akcji i procesów, których potrzebują. Aby zminimalizować ryzyko ataku, nie konfiguruj aplikacji i kontenerów, które wymagają eskalacji uprawnień lub dostępu administratora. Na przykład `allowPrivilegeEscalation: false` ustawić w manifeście zasobnika. Te *konteksty zabezpieczeń zasobników* są wbudowane w narzędzia Kubernetes i umożliwiają definiowanie dodatkowych uprawnień, takich jak użytkownik lub grupa do uruchomienia jako lub jakie możliwości systemu Linux do udostępnienia. Aby uzyskać więcej najlepszych rozwiązań, zobacz [Bezpieczny dostęp zasobnika do zasobów.][pod-security-contexts]

Aby uzyskać bardziej szczegółową kontrolę nad akcjami kontenera, można również użyć wbudowanych funkcji zabezpieczeń systemu Linux, takich jak *AppArmor* i *seccomp*. Te funkcje są definiowane na poziomie węzła, a następnie implementowane za pośrednictwem manifestu zasobnika. Wbudowane funkcje zabezpieczeń Systemu Linux są dostępne tylko w węzłach i zasobnikach systemu Linux.

> [!NOTE]
> Środowiska Kubernetes, w uzywu AKS lub gdzie indziej, nie są całkowicie bezpieczne dla wrogiego użycia wielu dzierżawców. Dodatkowe funkcje zabezpieczeń, takie jak *AppArmor*, *seccomp*, *Pod Security Policies*lub bardziej szczegółowe kontroli dostępu oparte na rolach (RBAC) dla węzłów utrudnić exploity. Jednak dla prawdziwego zabezpieczenia podczas uruchamiania wrogich obciążeń wielodostępnych hypervisor jest jedynym poziomem zabezpieczeń, który należy zaufać. Domena zabezpieczeń dla usługi Kubernetes staje się całym klastrem, a nie pojedynczym węzłem. Dla tych typów wrogich obciążeń wielodostępnych należy użyć fizycznie izolowane klastry.

### <a name="app-armor"></a>Zbroja aplikacji

Aby ograniczyć akcje, które kontenery mogą wykonywać, można użyć modułu zabezpieczeń jądra [AppArmor][k8s-apparmor] Linux. AppArmor jest dostępny jako część bazowego systemu operacyjnego węzła AKS i jest domyślnie włączony. Tworzenie profili AppArmor, które ograniczają akcje, takie jak odczyt, zapis lub wykonanie lub funkcje systemowe, takie jak systemy plików montażowych. Domyślne profile AppArmor ograniczają `/proc` `/sys` dostęp do różnych i lokalizacji i zapewniają środki logicznie izolować kontenery z węzła źródłowego. AppArmor działa dla każdej aplikacji, która działa na Linuksie, nie tylko Kubernetes zasobników.

![Profile AppArmor używane w klastrze AKS w celu ograniczenia akcji kontenera](media/operator-best-practices-container-security/apparmor.png)

Aby wyświetlić AppArmor w akcji, w poniższym przykładzie tworzy profil, który uniemożliwia zapisywanie do plików. [SSH][aks-ssh] do węzła AKS, a następnie utworzyć plik o nazwie *deny-write.profile* i wkleić następującą zawartość:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Profile AppArmor są dodawane za pomocą `apparmor_parser` polecenia. Dodaj profil do AppArmor i określ nazwę profilu utworzonego w poprzednim kroku:

```console
sudo apparmor_parser deny-write.profile
```

Nie ma żadnych danych wyjściowych zwróconych, jeśli profil jest poprawnie analizowany i stosowane do AppArmor. Zostaniesz przywrócony do wiersza polecenia.

Z komputera lokalnego teraz utwórz manifest zasobnika o nazwie *aks-apparmor.yaml* i wklej następującą zawartość. Ten manifest definiuje adnotację `container.apparmor.security.beta.kubernetes` dla dodawania odwołań do profilu *odmowy zapisu* utworzonego w poprzednich krokach:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

Wdrażanie zasobnika przykładu za pomocą polecenia [zastosuj kubectl:][kubectl-apply]

```console
kubectl apply -f aks-apparmor.yaml
```

Po wdrożeniu zasobnika użyj polecenia [kubectl exec,][kubectl-exec] aby zapisać do pliku. Nie można wykonać polecenia, jak pokazano w poniższym przykładzie danych wyjściowych:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Aby uzyskać więcej informacji na temat AppArmor, zobacz [Profile AppArmor w umięśnienia w uliczkach Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Bezpieczne przetwarzanie danych

Podczas gdy AppArmor działa dla dowolnej aplikacji systemu Linux, [seccomp *(sec*ure *comp*uting)][seccomp] działa na poziomie procesu. Seccomp jest również modułem zabezpieczeń jądra Linuksa i jest natywnie obsługiwany przez środowisko uruchomieniowe platformy Docker używane przez węzły AKS. Z seccomp wywołania procesu, które kontenery mogą wykonywać są ograniczone. Należy utworzyć filtry, które definiują, jakie akcje zezwalają lub odrzucać, a następnie używasz adnotacji w manifeście zasobnika YAML do skojarzenia z filtrem seccomp. To jest zgodne z najlepszym rozwiązaniem tylko przyznanie kontenera minimalne uprawnienia, które są potrzebne do uruchomienia i nie więcej.

Aby wyświetlić seccomp w akcji, należy utworzyć filtr, który uniemożliwia zmianę uprawnień do pliku. [SSH][aks-ssh] do węzła AKS, a następnie utworzyć filtr seccomp o nazwie */var/lib/kubelet/seccomp/prevent-chmod* i wkleić następującą zawartość:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Z komputera lokalnego teraz utwórz manifest zasobnika o nazwie *aks-seccomp.yaml* i wklej następującą zawartość. Ten manifest definiuje adnotację `seccomp.security.alpha.kubernetes.io` i odwołuje się do filtru *prevent-chmod* utworzonego w poprzednim kroku:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

Wdrażanie zasobnika przykładu za pomocą polecenia [zastosuj kubectl:][kubectl-apply]

```console
kubectl apply -f ./aks-seccomp.yaml
```

Służy do wyświetlania stanu zasobników za pomocą polecenia [kubectl get strąki.][kubectl-get] Zasobnik zgłasza błąd. Polecenie `chmod` nie może być uruchamiane przez filtr seccomp, jak pokazano w poniższym przykładzie danych wyjściowych:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Aby uzyskać więcej informacji na temat dostępnych filtrów, zobacz [Profile zabezpieczeń Seccomp dla platformy Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regularne aktualizowanie do najnowszej wersji Kubernetes

**Wskazówki dotyczące najlepszych praktyk** — aby być na bieżąco z nowymi funkcjami i poprawkami błędów, regularnie uaktualniaj do wersji Kubernetes w klastrze AKS.

Kubernetes udostępnia nowe funkcje w szybszym tempie niż bardziej tradycyjne platformy infrastruktury. Aktualizacje kubernetes zawierają nowe funkcje i poprawki błędów lub zabezpieczeń. Nowe funkcje zazwyczaj przechodzą przez *stan alfa,* a następnie *beta,* zanim staną się *stabilne* i są ogólnie dostępne i zalecane do użytku produkcyjnego. Ten cykl wersji powinien umożliwiać aktualizowanie aplikacji Kubernetes bez regularnego napotykania na wprowadzanie zmian lub dostosowywania wdrożeń i szablonów.

Usługa AKS obsługuje cztery pomocnicze wersje usługi Kubernetes. Oznacza to, że po wprowadzeniu nowej wersji poprawki pomocniczej najstarsza wersja pomocnicza i obsługiwane wersje poprawek są wycofywane. Drobne aktualizacje kubernetes się okresowo. Upewnij się, że masz proces nadzoru, aby sprawdzić i uaktualnić w razie potrzeby, aby nie wypaść z pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Obsługiwane wersje kubernetes AKS][aks-supported-versions]

Aby sprawdzić wersje dostępne dla klastra, użyj polecenia [az aks get-upgrades,][az-aks-get-upgrades] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Następnie można uaktualnić klaster AKS za pomocą polecenia [az aks upgrade.][az-aks-upgrade] Proces uaktualniania bezpiecznie kordony i opróżnia jeden węzeł naraz, planuje zasobników na pozostałych węzłach, a następnie wdraża nowy węzeł z systemem operacyjnym i wersji Kubernetes.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Aby uzyskać więcej informacji na temat uaktualnień w ukasza, zobacz [Obsługiwane wersje kubernetes w uzywu AKS][aks-supported-versions] i [uaktualnianie klastra AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Przetwarzaj aktualizacje i ponowne uruchamianie węzłów Systemu Linux za pomocą kured

**Wskazówki dotyczące najlepszych praktyk** — usługa AKS automatycznie pobiera i instaluje poprawki zabezpieczeń w każdym węzłach systemu Linux, ale w razie potrzeby nie uruchamia się automatycznie ponownie. Użyj, `kured` aby obserwować oczekujące ponowne uruchomienie, a następnie bezpiecznie kordon i opróżnić węzeł, aby umożliwić węzeł, aby ponownie uruchomić, zastosować aktualizacje i być tak bezpieczne, jak to możliwe w odniesieniu do systemu operacyjnego. W przypadku węzłów systemu Windows Server (obecnie w wersji zapoznawczej w systemie AKS) regularnie przeprowadzaj operację uaktualniania usługi AKS, aby bezpiecznie kordonować i opróżniać zasobniki oraz wdrażać zaktualizowane węzły.

Każdego wieczoru węzły Systemu Linux w AKS otrzymują poprawki zabezpieczeń dostępne za pośrednictwem kanału aktualizacji dystrybucji. To zachowanie jest konfigurowane automatycznie, gdy węzły są wdrażane w klastrze AKS. Aby zminimalizować zakłócenia i potencjalny wpływ na uruchomione obciążenia, węzły nie są automatycznie uruchamiane ponownie, jeśli wymaga tego poprawka zabezpieczeń lub aktualizacja jądra.

Projekt typu ["open-source kured" (KUbernetes REboot Daemon)][kured] firmy Weaveworks przewiduje oczekujące ponowne uruchomienie węzła. Gdy węzeł systemu Linux stosuje aktualizacje, które wymagają ponownego uruchomienia, węzeł jest bezpiecznie cordoned i opróżnione przenieść i zaplanować zasobników w innych węzłach w klastrze. Po ponownym uruchomieniu węzła jest dodawany z powrotem do klastra i Kubernetes wznawia planowania zasobników na nim. Aby zminimalizować zakłócenia, tylko jeden węzeł naraz może zostać `kured`ponownie uruchomiony przez plik .

![Proces ponownego uruchamiania węzła AKS przy użyciu](media/operator-best-practices-cluster-security/node-reboot-process.png)

Jeśli chcesz dokładniejszą kontrolę ziarna `kured` nad po ponownym uruchomieniu, można zintegrować z Prometheus, aby zapobiec ponownemu uruchomieniu, jeśli istnieją inne zdarzenia konserwacji lub problemy klastra w toku. Ta integracja minimalizuje dodatkowe komplikacje, uruchamiając ponownie węzły podczas aktywnego rozwiązywania innych problemów.

Aby uzyskać więcej informacji na temat obsługi ponownych rozruchów węzłów, zobacz [Stosowanie zabezpieczeń i aktualizacji jądra do węzłów w UKS][aks-kured].

## <a name="next-steps"></a>Następne kroki

W tym artykule skupiono się na tym, jak zabezpieczyć klaster AKS. Aby zaimplementować niektóre z tych obszarów, zobacz następujące artykuły:

* [Integracja usługi Azure Active Directory z usługą AKS][aks-aad]
* [Uaktualnianie klastra AKS do najnowszej wersji programu Kubernetes][aks-upgrade]
* [Przetwarzanie aktualizacji zabezpieczeń i ponowne uruchamianie węzłów za pomocą kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: /azure/security-center/azure-kubernetes-service-integration

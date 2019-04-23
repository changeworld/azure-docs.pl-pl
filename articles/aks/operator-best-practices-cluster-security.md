---
title: Operator najlepsze rozwiązania — zabezpieczenia klastra usługi Kubernetes w usłudze Azure (AKS)
description: Poznaj klastra operator najlepsze rozwiązania dotyczące sposobów zarządzania zabezpieczenia klastra i uaktualnień w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: bf794c6c4f73c4dd25849148aa2ea68b538372c4
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001970"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zabezpieczeń klastra i uaktualnień w usłudze Azure Kubernetes Service (AKS)

W przypadku zarządzania klastrów w usłudze Azure Kubernetes Service (AKS), zabezpieczenia obciążeń i danych stanowi klucza. Szczególnie w przypadku, gdy uruchamiasz klastrów wielodostępnych za pomocą izolacji logicznej, należy zabezpieczyć dostęp do zasobów i obciążeń. Aby zminimalizować ryzyko ataku, trzeba będzie również upewnij się, że stosowanie najnowszych Kubernetes i aktualizacje zabezpieczeń systemu operacyjnego węzła.

Ten artykuł koncentruje się na sposób zabezpieczania klastra usługi AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Bezpieczny dostęp do serwera interfejsu API za pomocą usługi Azure Active Directory i kontroli dostępu opartej na rolach
> * Zabezpieczanie dostępu do kontenera do węzła zasobów
> * Uaktualnianie klastra usługi AKS do najnowszej wersji platformy Kubernetes
> * Zachowaj aktualizacji węzłów do daty i automatyczne stosowanie poprawek zabezpieczeń

Można również przeczytać najlepsze rozwiązania dotyczące [zarządzanie obrazami kontenera] [ best-practices-container-image-management] i [zasobnika zabezpieczeń][best-practices-pod-security].

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Bezpieczny dostęp do interfejsu API serwera i węzłami klastra

**Najważniejsze wskazówki** — zabezpieczanie dostępu do interfejsu API rozwiązania Kubernetes-Server jest jednym z najważniejszych elementów, które można zrobić, aby zabezpieczyć klaster. Integracja kontroli dostępu opartej na rolach Kubernetes (RBAC) przy użyciu usługi Azure Active Directory w celu kontrolowania dostępu do serwera interfejsu API. Te kontrolki umożliwiają Zabezpieczanie usługi AKS w taki sam sposób, jak zabezpieczyć dostęp do subskrypcji platformy Azure.

Serwer interfejsu API rozwiązania Kubernetes zapewnia pojedynczy punkt połączenia dla żądań kierowanych do wykonywania akcji w ramach klastra. Do zabezpieczenia i inspekcja dostępu do serwera interfejsu API, ograniczyć dostęp i podaj najniższych uprawnieniach wymaganych uprawnień dostępu. To podejście nie jest unikatowy dla platformy Kubernetes, ale jest szczególnie ważne, gdy klaster AKS jest logicznie izolowana, do użytku z wieloma dzierżawami.

Usługa Azure Active Directory (AD) zapewnia rozwiązanie zarządzania tożsamościami obsługą przedsiębiorstw, która integruje się z klastrami usługi AKS. Jako rozwiązanie Kubernetes nie zapewnia rozwiązanie do zarządzania tożsamościami, w przeciwnym razie może być trudne zapewnić szczegółowy sposób, aby ograniczyć dostęp do serwera interfejsu API. Przy użyciu platformy Azure zintegrowanych z usługą AD klastrów w usłudze AKS korzystasz z istniejących kont użytkowników i grup do uwierzytelniania kont użytkowników do serwera interfejsu API.

![Integracja usługi Azure Active Directory w przypadku klastrów usługi AKS](media/operator-best-practices-cluster-security/aad-integration.png)

RBAC platformy Kubernetes i Azure AD integracji do zabezpieczania serwera interfejsu API i zapewnienia najmniejszej liczby uprawnień wymaganych do zakresu zestawu zasobów, takich jak jednej przestrzeni nazw. Różne role RBAC można udzielić różnych użytkowników lub grup w usłudze Azure AD. Te szczegółowe uprawnienia pozwalają ograniczyć dostęp do serwera interfejsu API i podaj szczegółowe rejestrowanie inspekcji wyczyść na akcje wykonywane.

Zalecanym najlepszym rozwiązaniem jest używanie grup do zapewnienia dostępu do plików i folderów w porównaniu z indywidualne tożsamości, należy użyć usługi Azure AD *grupy* członkostwa można powiązać użytkowników do ról RBAC, a nie poszczególnych *użytkowników*. Jako zmiany członkostwa grupy użytkownika zmieniłby się w związku z tym ich uprawnień dostępu do klastra usługi AKS. Jeśli użytkownik jest powiązana bezpośrednio do roli, ich funkcję zadania mogą ulec zmianie. Zaktualizować członkostwa w grupach usługi Azure AD, ale uprawnień w klastrze AKS nie będzie odzwierciedlać który. W tym scenariuszu użytkownik kończy się udzielenia więcej uprawnień niż użytkownik wymaga.

Aby uzyskać więcej informacji na temat integracji z usługą Azure AD i RBAC zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Kontener bezpiecznego dostępu do zasobów

**Najważniejsze wskazówki** — ograniczyć dostęp do akcji, które można wykonywać kontenerów. Podaj najmniejszej liczby uprawnień i Unikaj stosowania głównego / uprzywilejowany eskalacji.

W ten sam sposób, że należy udzielić użytkownikom lub grupom najmniejszej liczby uprawnienia wymagane, kontenery również powinny być ograniczone tylko do akcji i procesów, które są im niezbędne. Aby zminimalizować ryzyko ataku, nie należy konfigurować aplikacje i kontenery, które wymagają z podniesionymi uprawnieniami lub głównych dostępu. Na przykład ustawić `allowPrivilegeEscalation: false` w manifeście zasobników. Te *zasobnika konteksty zabezpieczeń* są wbudowane w platformę Kubernetes, dzięki czemu można zdefiniować dodatkowe uprawnienia, takie jak użytkownika lub grupy, aby Uruchom jako, lub jakie funkcje systemu Linux do udostępnienia. Aby uzyskać więcej o najlepszych praktykach, zobacz [zasobnika bezpiecznego dostępu do zasobów][pod-security-contexts].

Aby uzyskać większą kontrolę nad akcji kontenerów, umożliwia również wbudowane funkcje zabezpieczeń systemu Linux takie jak *AppArmor* i *funkcję seccomp*. Te funkcje są definiowane na poziomie węzła, a następnie wdrażane za pośrednictwem manifestu pod.

> [!NOTE]
> Środowisk Kubernetes w usłudze AKS, lub w innych miejscach, nie są całkowicie bezpieczne dla szkodliwy użycie wielu obcych dzierżaw. Funkcje dodatkowe zabezpieczenia, takie jak *AppArmor*, *funkcję seccomp*, *zasad zabezpieczeń zasobnika*, lub więcej kontroli dostępu w zakresie opartej na rolach (RBAC) dla węzłów luki w zabezpieczeniach trudniejsze. Wartość true, zabezpieczeń przy uruchamianiu obciążeń liczonych w szkodliwy wielodostępne, funkcja hypervisor to tylko poziom zabezpieczeń, które należy ufać. Domeny zabezpieczeń dla rozwiązania Kubernetes staje się całego klastra, a nie oddzielnego węzła. Dla tych typów szkodliwy obciążenia z wieloma dzierżawami należy użyć fizycznie izolowane klastrów.

### <a name="app-armor"></a>Moto aplikacji

Aby ograniczyć akcje, które można wykonywać kontenerów, można użyć [AppArmor] [ k8s-apparmor] modułu zabezpieczeń jądra systemu Linux. AppArmor jest dostępny jako część podstawowego węzłów AKS system operacyjny i jest domyślnie włączona. Można tworzyć AppArmor profilów, które ograniczenie akcji takich jak odczytu, zapisu lub wykonania lub funkcji systemu, takich jak instalowanie systemów plików. Domyślne profile AppArmor ograniczyć dostęp do różnych `/proc` i `/sys` lokalizacji i umożliwiają izolowanie logicznie kontenerów z węzła podstawowego. AppArmor działa w przypadku dowolnej aplikacji uruchomionej w systemie Linux, a nie tylko zasobników.

![Profile AppArmor używany w klastrze AKS do ograniczenia akcji kontenerów](media/operator-best-practices-container-security/apparmor.png)

Aby zobaczyć AppArmor w działaniu, poniższy przykład tworzy profil, który uniemożliwia zapisywanie do plików. [SSH] [ aks-ssh] do węzła usługi AKS, a następnie utwórz plik o nazwie *Odmów write.profile* i wklej następującą zawartością:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Profile AppArmor są dodawane przy użyciu `apparmor_parser` polecenia. Dodać profil do AppArmor i określ nazwę profilu utworzonego w poprzednim kroku:

```console
sudo apparmor_parser deny-write.profile
```

Nie ma żadnych danych wyjściowych jest zwracana, jeśli profil jest poprawnie przeanalizować i zastosowane do AppArmor. Następuje powrót do wiersza polecenia.

Na maszynie lokalnej, a teraz tworzenie manifestu pod o nazwie *aks apparmor.yaml* i wklej następującą zawartością. Tego manifestu definiuje adnotacji dla `container.apparmor.security.beta.kubernetes` Dodaj odwołania *Odmów zapisu* profil został utworzony w poprzednich krokach:

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

Wdrażanie przy użyciu zasobnika przykładowe [zastosować kubectl] [ kubectl-apply] polecenia:

```console
kubectl apply -f aks-apparmor.yaml
```

Za pomocą zasobnika wdrożone, należy użyć [kubectl exec] [ kubectl-exec] polecenie, aby zapisać do pliku. Nie można wykonać tego polecenia, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Aby uzyskać więcej informacji na temat AppArmor zobacz [AppArmor profilów w usłudze Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Bezpiecznego przetwarzania danych

Natomiast AppArmor działa dla dowolnej aplikacji systemu Linux [funkcję seccomp (*s*uruj *comp*uty)] [ seccomp] działa na poziomie procesu. Funkcję Seccomp jest również moduł zabezpieczeń jądra systemu Linux i obsługiwane przez środowisko uruchomieniowe platformy Docker, używane przez węzłów AKS. Za pomocą funkcję seccomp przetwarzają wywołania, które mogą wykonywać kontenery są ograniczone. Tworzenie filtrów, które określają, jakie akcje, aby udzielić lub odmówić, a następnie korzystanie z adnotacji w manifeście YAML zasobnika do skojarzenia z filtrem funkcję seccomp. Wyrównuje to najlepsze rozwiązanie polegające na tylko udzielanie kontenera minimalne uprawnienia, które są wymagane do uruchomienia i nie ma więcej.

Aby zobaczyć funkcję seccomp w akcji, należy utworzyć filtr, który uniemożliwia zmienianie uprawnień do pliku. [SSH] [ aks-ssh] do węzła usługi AKS, a następnie utwórz filtr funkcję seccomp o nazwie */var/lib/kubelet/seccomp/prevent-chmod* i wklej następującą zawartością:

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

Na maszynie lokalnej, a teraz tworzenie manifestu pod o nazwie *aks seccomp.yaml* i wklej następującą zawartością. Tego manifestu definiuje adnotacji dla `seccomp.security.alpha.kubernetes.io` i zawiera odwołania do *zapobiec chmod* Filtr utworzony w poprzednim kroku:

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

Wdrażanie przy użyciu zasobnika przykładowe [zastosować kubectl] [ kubectl-apply] polecenia:

```console
kubectl apply -f ./aks-seccomp.yaml
```

Wyświetl stan zasobników, za pomocą [kubectl get pods-] [ kubectl-get] polecenia. Zasobnik zgłasza błąd. `chmod` Polecenia jest mogły być uruchamiane przez filtr funkcję seccomp, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Aby uzyskać więcej informacji na temat dostępnych filtrów, zobacz [funkcję Seccomp profile zabezpieczeń dla platformy Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regularne aktualizowanie do najnowszej wersji platformy Kubernetes

**Najważniejsze wskazówki** — aby bieżące informacje na temat nowych funkcji i poprawek błędów, regularnie uaktualnienia do wersji rozwiązania Kubernetes w klastrze AKS.

Nowe funkcje w tempie szybciej niż bardziej tradycyjny różnych platformach infrastruktury z wersjami usługi Kubernetes. Kubernetes aktualizacje obejmują nowe funkcje i poprawki błędów lub zabezpieczeń. Nowe funkcje, ale zazwyczaj poruszać *alfa* a następnie *beta* stanu, zanim staną się one *stabilne* i są ogólnie dostępne i zalecane do użytku produkcyjnego. Ten cykl wersji powinno umożliwić zaktualizowanie Kubernetes bez regularnie występują istotne zmiany lub dostosowywanie wdrożeń i szablony.

AKS obsługuje cztery pomocnicze wersje rozwiązania Kubernetes. Oznacza to, gdy wprowadzono nową wersję pomocniczą poprawki, najstarsze pomocniczej wersji i poprawki wersji obsługiwane zostały wycofane. Niewielkie aktualizacje Kubernetes się tak zdarzyć w regularnych odstępach czasu. Upewnij się, że proces nadzoru Sprawdź i uaktualniania, zgodnie z potrzebami, dzięki czemu nie utracą pomocy technicznej. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje rozwiązania Kubernetes usługi AKS][aks-supported-versions]

Aby sprawdzić wersje, które są dostępne dla klastra, użyj [az aks get uaktualnienia] [ az-aks-get-upgrades] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Następnie można uaktualnić usługi AKS klastra przy użyciu [uaktualnienia az aks] [ az-aks-upgrade] polecenia. Proces uaktualniania bezpiecznie cordons i opróżnia jednym węźle naraz, harmonogramy zasobników w pozostałych węzłach, a następnie wdraża nowy węzeł uruchomiony w najnowszych wersjach systemu operacyjnego i platformy Kubernetes.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.8
```

Aby uzyskać więcej informacji na temat uaktualnienia w usłudze AKS, zobacz [obsługiwane wersje rozwiązania Kubernetes w usłudze AKS] [ aks-supported-versions] i [Uaktualnianie klastra usługi AKS][aks-upgrade].

## <a name="process-node-updates-and-reboots-using-kured"></a>Węzeł procesu aktualizacji i wykonuje ponowny rozruch przy użyciu kured

**Najważniejsze wskazówki** — AKS automatycznie pobiera i instaluje zabezpieczeń naprawia na wszystkich węzłach procesu roboczego, ale nie automatyczny ponowny rozruch w razie potrzeby. Użyj `kured` poszukaj do czasu ponownego uruchomienia, a następnie bezpiecznie odizolowywanie i opróżnianie węzła, aby zezwolić na węzeł, aby ponownie uruchomić, należy zastosować aktualizacje i być tak samo bezpieczna, jak to możliwe w odniesieniu do systemu operacyjnego.

Każdy wieczór węzłów AKS. Uzyskaj poprawek zabezpieczeń jest dostępne za pośrednictwem ich kanał aktualizacji dostępnych dla określonych dystrybucji. To zachowanie jest konfigurowana automatycznie podczas wdrażania węzłów w klastrze AKS. Aby zminimalizować przerwy w działaniu i potencjalnego wpływu na działające zadania, węzły są nie automatycznie ponownie uruchomiony, jeśli poprawka zabezpieczeń lub aktualizacji jądra go wymaga.

Open source [kured (KUbernetes ponownie uruchomić demona)] [ kured] projektu, Weaveworks obserwuje do czasu ponownego uruchomienia węzła. Gdy węzeł stosujący aktualizacje wymagające ponownego uruchomienia systemu, węzeł jest bezpiecznie odizolowywane i opróżniane można przenosić i zaplanować zasobników w innych węzłach w klastrze. Gdy węzeł jest uruchamiany ponownie, zostanie on dodany do klastra i wznawia Kubernetes planowania zasobników na nim. Aby zminimalizować zakłócenia, tylko jeden węzeł w danym momencie może zostać przeprowadzony ponowny rozruch `kured`.

![Proces ponownego uruchomienia węzłów AKS, korzystając z kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Jeśli chcesz, aby bardziej precyzyjną kontrolę ziarna po ponownym uruchomieniu i tak się stanie `kured` można zintegrować z Prometheus, aby uniknąć ponownego uruchomienia, jeśli istnieją inne zdarzenia konserwacji lub problemów z klastrem w toku. Ta integracja minimalizuje komplikacji dodatkowe węzły ponownie uruchamiany, gdy są aktywnie Rozwiązywanie innych problemów.

Aby uzyskać więcej informacji na temat obsługi ponownego uruchomienia węzła, zobacz [stosowania aktualizacji zabezpieczeń i jądra dla węzłów w usłudze AKS][aks-kured].

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł koncentruje się na sposób zabezpieczania klastra usługi AKS. Aby zaimplementować, niektóre z tych obszarów, zobacz następujące artykuły:

* [Integrowanie usługi Azure Active Directory za pomocą usługi AKS][aks-aad]
* [Uaktualnianie klastra usługi AKS do najnowszej wersji platformy Kubernetes][aks-upgrade]
* [Za pomocą kured ponowne uruchomienie procesu aktualizacji zabezpieczeń i języka node][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://docs.docker.com/engine/security/seccomp/
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

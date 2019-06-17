---
title: Użycie zasad zabezpieczeń zasobnik w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak kontrolować przyjęć odpowiadające ilości zasobnik przy użyciu PodSecurityPolicy w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 881a16501574dc7309eede6b58e270a97bed977a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235742"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>W wersji zapoznawczej — Zabezpieczanie klastra za pomocą zasad zabezpieczeń zasobnik w usłudze Azure Kubernetes Service (AKS)

Aby zwiększyć bezpieczeństwo klastra usługi AKS, można ograniczyć zasobników, które można zaplanowane. Zasobników, które zasoby, które nie zezwalaj na żądania nie można uruchomić w klastrze AKS. Należy zdefiniować ten dostęp, za pomocą zasad zabezpieczeń zasobników. W tym artykule dowiesz się, jak ograniczyć wdrożenia zasobników w usłudze AKS za pomocą zasad zabezpieczeń pod.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi, uczestnictwo. Są one udostępniane do zbierania opinii i błędy z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w wersji zapoznawczej objęte "starań" pomocy technicznej. Pomoc od zespołów pomocy technicznej usługi AKS jest dostępna w godzinach pracy i Pacyfik, część strefy czasowej (PST) tylko. Aby uzyskać więcej informacji zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej usługi AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.61 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia w wersji zapoznawczej usługi aks

Klastry usługi AKS są aktualizowane, aby włączyć zasady zabezpieczeń zasobnik przy użyciu *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia. Zainstaluj *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [Dodaj rozszerzenie az] [ az-extension-add] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Jeśli wcześniej zainstalowano *podglądu usługi aks* rozszerzenia, zainstaluj dostępne aktualizacje, przy użyciu `az extension update --name aks-preview` polecenia.

### <a name="register-pod-security-policy-feature-provider"></a>Zarejestruj dostawcę funkcji zasad zabezpieczeń zasobników

Aby utworzyć lub zaktualizować klastra usługi AKS, stosowanie zasad zabezpieczeń zasobnik, należy najpierw włączyć flagi funkcji w ramach Twojej subskrypcji. Aby zarejestrować *PodSecurityPolicyPreview* flagę funkcji, należy użyć [az feature register] [ az-feature-register] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Zajmuje kilka minut, zanim stan wyświetlany *zarejestrowanej*. Można sprawdzić stan rejestracji przy użyciu [lista funkcji az] [ az-feature-list] polecenia:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestracji *Microsoft.ContainerService* dostawcę zasobów przy użyciu [az provider register] [ az-provider-register] polecenia:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Omówienie zasad zabezpieczeń zasobników

W klastrze Kubernetes kontroler czasowej jest używany do przechwycenia żądań do serwera interfejsu API, gdy zasób ma zostać utworzony. Kontroler czasowej mogą następnie *zweryfikować* żądania zasobu z zestawem reguł, lub *mutować* zasobów w celu zmiany parametrów wdrożenia.

*PodSecurityPolicy* jest kontroler czasowej, która weryfikuje specyfikacji zasobnika spełnia wymagania zdefiniowane. Te wymagania mogą ograniczać wykorzystanie uprzywilejowanego kontenerów, dostęp do niektórych typów magazynu, lub użytkownika lub grupę, którą można uruchomić kontener. Podczas wdrażania zasobów, gdzie specyfikacje zasobnika nie spełniają wymagań opisanych w zasadach zabezpieczeń pod, żądanie zostanie odrzucone. Ta możliwość kontrolowania zasobników, które można zaplanowane w usłudze AKS klastra zapobiega kilka możliwych luk w zabezpieczeniach lub podwyższeń poziomu uprawnień.

Po włączeniu zasady zabezpieczeń zasobnik w klastrze AKS, niektóre domyślne zasady są stosowane. Te zasady domyślne zapewniają środowiska out-of--box do definiowania zasobników co można uwzględnić w harmonogramie. Jednak użytkownicy klastra mogą występować problemy wdrażanie zasobników, dopóki nie można zdefiniować własne zasady. Podejście zalecane jest:

* Tworzenie klastra AKS
* Definiowanie własnych zasad zabezpieczeń zasobników
* Włącz funkcję zasad zabezpieczeń zasobników

Aby wyświetlić jak domyślne zasady ograniczania zasobnika wdrożeń, w tym artykule możemy najpierw włączyć funkcję zasad zabezpieczeń pod, a następnie utwórz zasadę niestandardową.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Włącz zasady zabezpieczeń zasobnik w klastrze usługi AKS

Można włączyć lub wyłączyć za pomocą zasad zabezpieczeń zasobnika [aktualizacji az aks] [ az-aks-update] polecenia. Poniższy przykład umożliwia zasobnika zasad zabezpieczeń na nazwę klastra *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*.

> [!NOTE]
> Do użytku w rzeczywistych warunkach nie włączaj zasobnika zasady zabezpieczeń, dopóki nie zdefiniowano własne zasady niestandardowe. W tym artykule, włączyć zasady zabezpieczeń pod jako pierwszy krok, aby zobaczyć, jak domyślne zasady ograniczyć pod wdrożeń.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Domyślne zasady usługi AKS

Po włączeniu zasobnika zasad zabezpieczeń usługa AKS tworzy dwie domyślne zasady o nazwie *uprzywilejowanych* i *ograniczeniami*. Nie Edytuj lub usuń te domyślne zasady. Zamiast tego utwórz własne zasady, które definiują ustawienia, które mają do formantu. Pierwsze spojrzenie na te zasady domyślne są umożliwia ich wpływ na zasobnik wdrożeń.

Aby wyświetlić dostępne zasady, użyj [kubectl get-psp] [ kubectl-get] polecenia, jak pokazano w poniższym przykładzie. Wartość domyślna w ramach *ograniczeniami* zasad, użytkownik otrzyma odmowę *PRIV* używane dla uprzywilejowanych zasobnika eskalacji oraz użytkownika *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*Ograniczeniami* zasobnika zabezpieczeń zasady są stosowane do dowolnej uwierzytelnionego użytkownika w klastrze AKS. To przypisanie jest kontrolowana przez ClusterRoles i ClusterRoleBindings. Użyj [kubectl get-clusterrolebindings] [ kubectl-get] polecenia, a następnie wyszukaj *domyślne: ograniczone:* powiązania:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Jak pokazano na następujące skrócone dane wyjściowe *psp: ograniczone* ClusterRole jest przypisany do żadnej *systemu: uwierzytelniony* użytkowników. Ta możliwość udostępnia podstawowy poziom ograniczeń bez własne zasady, które zostały zdefiniowane.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

Należy zrozumieć, jak te domyślne zasady wchodzić w interakcje z żądaniami użytkowników, aby zaplanować zasobników, przed przystąpieniem do tworzenia własnych zasobnika zasad zabezpieczeń. W kolejnych sekcjach możemy zaplanować pewne zasobników, aby wyświetlić te domyślne zasady w działaniu.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Tworzenie użytkownika testowego w klastrze AKS

Domyślnie, gdy używasz [az aks get-credentials] [ az-aks-get-credentials] polecenia *administratora* poświadczenia dla klastra usługi AKS i dodane do Twojego `kubectl` konfiguracji. Administrator pomija wymuszanie zasad zabezpieczeń zasobników. Korzystając z integracji usługi Azure Active Directory w przypadku klastrów usługi AKS, można zalogować się przy użyciu poświadczeń użytkownika bez uprawnień administratora, wymuszania zasad na liście akcji wyświetlić. Utwórz testowe konto użytkownika w klastrze AKS, którego można używać w tym artykule.

Tworzenie przestrzeni nazw próbki o nazwie *psp aks* dla zasobów testowych za pomocą [kubectl tworzenie przestrzeni nazw] [ kubectl-create] polecenia. Następnie należy utworzyć konto usługi o nazwie *tekst nonadmin użytkownika* przy użyciu [kubectl tworzenie serviceaccount] [ kubectl-create] polecenia:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Następnie należy utworzyć RoleBinding dla *tekst nonadmin użytkownika* wykonywać podstawowe działania w przestrzeni nazw, używając [kubectl tworzenie rolebinding] [ kubectl-create] polecenia:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Tworzenie polecenia aliasu dla użytkowników bez uprawnień administratora i administratora

Aby wyróżnić różnica między użytkownika administratora regularne, korzystając z `kubectl` i użytkowników bez uprawnień administratora, utworzony w poprzednich krokach, Utwórz dwa aliasy wiersza polecenia:

* **Kubectl-admin** alias jest regularne administratora i jest ograniczone do *psp aks* przestrzeni nazw.
* **Kubectl nonadminuser** aliasu jest *tekst nonadmin użytkownika* utworzony w poprzednim kroku i jest ograniczone do *psp aks* przestrzeni nazw.

Utwórz następujące dwa aliasy, jak pokazano w poniższych poleceniach:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testowanie tworzenia zasobnik uprzywilejowanych

Możemy najpierw sprawdza, co się stanie po zaplanowaniu zasobnik za pomocą kontekstu zabezpieczeń `privileged: true`. Ten kontekst zabezpieczeń Eskalowanie uprawnień zasobników. W poprzedniej sekcji, pokazujący zasobnika AKS domyślne zasady zabezpieczeń *ograniczeniami* zasad należy odrzucić to żądanie.

Utwórz plik o nazwie `nginx-privileged.yaml` i wklej następujące manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Utwórz zasobnik przy użyciu [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Zasobnik nie można zaplanować, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Zasobnik nie dociera do etapu planowania, więc nie ma żadnych zasobów, aby usunąć przed przejściem.

## <a name="test-creation-of-an-unprivileged-pod"></a>Tworzenie testów nieuprzywilejowanego zasobników

W poprzednim przykładzie specyfikacji zasobnika zażądał podwyższeniem poziomu uprawnień. To żądanie zostało odrzucone przez domyślne *ograniczeniami* zasobnika zasady zabezpieczeń, więc zasobnik zakończy się niepowodzeniem do zaplanowania. Wypróbujmy teraz uruchomione tego samego pod NGINX bez żądania podwyższenie poziomu uprawnień.

Utwórz plik o nazwie `nginx-unprivileged.yaml` i wklej następujące manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Utwórz zasobnik przy użyciu [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Harmonogram Kubernetes akceptuje żądania zasobników. Jednak jeśli przyjrzymy się stan zasobnik przy użyciu `kubectl get pods`, występuje błąd:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Użyj [kubectl opisują zasobnika] [ kubectl-describe] polecenie, aby przejrzeć zdarzenia dla zasobnik. Poniższego, skróconego przykładu pokazuje kontenera i obraz wymaga uprawnień głównych, mimo że firma Microsoft nie wysłano żądania je:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Mimo że firma Microsoft nie żądań uprzywilejowanego dostępu, obrazu kontenera dla kontenera NGINX musi utworzyć powiązanie portu *80*. Aby powiązać porty *1024* i starsze, *głównego* użytkownika jest wymagana. Podczas uruchomienia, Zasobnik *ograniczeniami* zasobnika zasady zabezpieczeń nie zezwala na to żądanie.

Ten przykład pokazuje, że domyślne zasady zabezpieczeń zasobnika utworzone przez usługę AKS są stosowane i ograniczenie akcji, którą użytkownik może wykonywać. Ważne jest zrozumienie zachowania tych domyślnych zasad jako nie może oczekiwać podstawowe zasobnik NGINX niedozwolone.

Przed przejściem do następnego kroku Usuń ten test zasobnik przy użyciu [kubectl usunąć zasobnik] [ kubectl-delete] polecenia:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Tworzenie testów zasobnik za pomocą kontekstu określonego użytkownika

W poprzednim przykładzie obraz kontenera jest automatycznie dotarła do głównego można powiązać serwer NGINX na porcie 80. To żądanie zostało odrzucone przez domyślne *ograniczeniami* zasobnika zasady zabezpieczeń, więc zasobnik uruchomienie nie powiedzie się. Wypróbujmy teraz uruchomione tego samego zasobnika NGINX z kontekstem określonego użytkownika, takie jak `runAsUser: 2000`.

Utwórz plik o nazwie `nginx-unprivileged-nonroot.yaml` i wklej następujące manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Utwórz zasobnik przy użyciu [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Harmonogram Kubernetes akceptuje żądania zasobników. Jednak jeśli przyjrzymy się stan zasobnik przy użyciu `kubectl get pods`, występuje błąd inny niż w poprzednim przykładzie:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Użyj [kubectl opisują zasobnika] [ kubectl-describe] polecenie, aby przejrzeć zdarzenia dla zasobnik. Poniższego, skróconego przykładu przedstawia zdarzenia zasobnika:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

Zdarzenia wskazują, że kontener została utworzona i uruchomiona. Nie ma nic od razu widoczne, dlaczego zasobnika ustawiany jest w stanie niepowodzenia. Przyjrzyjmy się dzienników zasobnik przy użyciu [Dzienniki narzędzia kubectl] [ kubectl-logs] polecenia:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

Następujące przykładowe dane wyjściowe przez dziennik umożliwia wskazanie, w ramach konfiguracji serwera NGINX, samego, występuje błąd uprawnień podczas uruchomienia usługi. Przyczyną tego błędu jest ponownie konieczności powiązania z portem 80. Mimo że specyfikacji zasobnika zdefiniowane kontem zwykłego użytkownika, to konto użytkownika nie jest wystarczające w poziomie systemu operacyjnego — usługi NGINX do uruchamiania i powiązania z portem ograniczone.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Ponownie ważne jest zrozumienie zachowania domyślne zasady zabezpieczeń zasobników. Ten błąd był nieco trudniejsze do znalezienia i ponownie, nie może oczekiwać podstawowe zasobnik NGINX niedozwolone.

Przed przejściem do następnego kroku Usuń ten test zasobnik przy użyciu [kubectl usunąć zasobnik] [ kubectl-delete] polecenia:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Utwórz niestandardowy zasobnik zasady zabezpieczeń

Teraz, gdy zachowanie domyślne zasady zabezpieczeń zasobników, możemy umożliwiają *tekst nonadmin użytkownika* można pomyślnie zaplanować zasobników.

Utwórz zasadę do odrzucenia zasobników, które wysyłania żądań uprzywilejowanego dostępu. Inne opcje, takie jak *nazwa_użytkownika* lub niedozwolone *woluminów*, nie są wyraźnie zabronione. Ten typ zasad odrzuca żądanie uprzywilejowanego dostępu, ale w przeciwnym razie umożliwia uruchomienia żądanej zasobników klastra.

Utwórz plik o nazwie `psp-deny-privileged.yaml` i wklej następujące manifest YAML:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Tworzenie zasad przy użyciu [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Aby wyświetlić dostępne zasady, użyj [kubectl get-psp] [ kubectl-get] polecenia, jak pokazano w poniższym przykładzie. Porównaj *psp Odmów — uprzywilejowane* zasad przy użyciu domyślnego *ograniczeniami* zasady, które zostało wymuszone w poprzednich przykładach, aby utworzyć zasobnik. Korzystanie z *PRIV* eskalacji zostanie odrzucona przez zasady. Nie ma żadnych ograniczeń na użytkownika lub grupę *psp Odmów — uprzywilejowane* zasad.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Zezwalaj na konto użytkownika używane do zasobnika niestandardowych zasad zabezpieczeń

W poprzednim kroku utworzono pod zasady zabezpieczeń, aby odrzucić zasobników tego żądania uprzywilejowany dostęp. Aby zezwolić na zasadach, które ma być używany, należy utworzyć *roli* lub *ClusterRole*. Następnie należy skojarzyć jedną z tych ról przy użyciu *RoleBinding* lub *ClusterRoleBinding*.

W tym przykładzie należy utworzyć ClusterRole, która pozwala na *użyj* *psp Odmów — uprzywilejowane* zasady utworzone w poprzednim kroku. Utwórz plik o nazwie `psp-deny-privileged-clusterrole.yaml` i wklej następujące manifest YAML:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Tworzenie za pomocą ClusterRole [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Teraz Utwórz ClusterRoleBinding używać ClusterRole utworzony w poprzednim kroku. Utwórz plik o nazwie `psp-deny-privileged-clusterrolebinding.yaml` i wklej następujące manifest YAML:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Tworzenie przy użyciu ClusterRoleBinding [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> W pierwszym kroku w tym artykule funkcja zasad zabezpieczeń zasobnika został włączony w klastrze AKS. Zalecaną praktyką było umożliwienie tylko funkcja zasad zabezpieczeń zasobnika po zdefiniowaniu własnych zasad. Jest na etapie, gdzie umożliwiłby zasobnika funkcję zasad zabezpieczeń. Zdefiniowano co najmniej jedne zasady niestandardowe, a konta użytkowników zostały skojarzone z tymi zasadami. Teraz możesz bezpiecznie zasobnika zasad zabezpieczeń funkcji i zminimalizować problemy spowodowane przez domyślne zasady.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Tworzenie nieuprzywilejowanego zasobnika ponownie test

Niestandardowy zasobnik stosowane zasady zabezpieczeń i wiązania dla konta użytkownika, aby korzystać z zasad spróbujmy ponownie utworzyć zasobnika nieuprzywilejowanego. Użyto tych samych `nginx-privileged.yaml` manifestu do utworzenia zasobnik przy użyciu [zastosować kubectl] [ kubectl-apply] polecenia:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pomyślnie zaplanowano zasobnik. Sprawdzając stan zasobnik przy użyciu [kubectl get pods-] [ kubectl-get] polecenia zasobnika ustawiany jest *systemem*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Ten przykład pokazuje, jak utworzyć niestandardowy zasobnik zasad zabezpieczeń definiują dostęp użytkownika do klastra usługi AKS dla różnych użytkowników lub grup. Domyślne zasady AKS zapewniają, że ścisłej kontroli zasobników, jakie można uruchamiać, więc Utwórz własne zasady niestandardowe, aby poprawnie zdefiniować ograniczenia, które są potrzebne.

Usunąć zasobnik nieuprzywilejowane NGINX przy użyciu [Usuń kubectl] [ kubectl-delete] polecenia i podaj nazwę manifeście YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyłączyć zasady zabezpieczeń pod, użyj [aktualizacji az aks] [ az-aks-update] ponownie polecenie. Poniższy przykład wyłącza zasobnika zasad zabezpieczeń na nazwę klastra *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Następnie usuń ClusterRole i ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Usunąć za pomocą zasad sieciowych [Usuń kubectl] [ kubectl-delete] polecenia i podaj nazwę manifeście YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Na koniec usunąć *psp aks* przestrzeni nazw:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule pokazano, jak utworzyć zasady zabezpieczeń zasobników, aby uniemożliwić korzystanie z dostępem uprzywilejowanym. Dostępnych jest wiele funkcji, które mogą wymusić zasady, takie jak typ woluminu lub użytkownika Uruchom jako. Aby uzyskać więcej informacji o dostępnych opcjach, zobacz [zasobnika Kubernetes dokumenty referencyjne zasad zabezpieczeń][kubernetes-policy-reference].

Aby uzyskać więcej informacji na temat ograniczania ruchu sieciowego zasobników, zobacz [bezpieczny ruch między zasobników w usłudze AKS za pomocą zasad sieciowych][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md

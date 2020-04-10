---
title: Używanie zasad zabezpieczeń zasobników w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak kontrolować przyjęć zasobników przy użyciu PodSecurityPolicy w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 9e3a17e4775150247ef7924dffec68cc86a0bcac
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998356"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>W wersji zapoznawczej — zabezpieczyć klaster przy użyciu zasad zabezpieczeń zasobników w usłudze Azure Kubernetes (AKS)

Aby zwiększyć bezpieczeństwo klastra usługi AKS, można ograniczyć, jakie zasobniki można zaplanować. Zasobników, które żądają zasobów, na które nie zezwalasz, nie można uruchomić w klastrze AKS. Ten dostęp można zdefiniować przy użyciu zasad zabezpieczeń zasobnika. W tym artykule pokazano, jak używać zasad zabezpieczeń zasobników, aby ograniczyć wdrażanie zasobników w ucho.

> [!IMPORTANT]
> Funkcje podglądu usługi AKS są samoobsługowe. Wersje zapoznawcza są dostarczane w stanie "tak jak jest" i "w miarę dostępności" i są wyłączone z umów o gwarantowanym poziomie usług i ograniczonej gwarancji. Podglądy AKS są częściowo objęte obsługą klienta na podstawie najlepszych starań. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby uzyskać dodatkowe informacje, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady wsparcia AKS][aks-support-policies]
> * [Często zadawane pytania dotyczące pomocy technicznej platformy Azure][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby korzystać z zasad zabezpieczeń zasobników, potrzebujesz rozszerzenia interfejsu wiersza polecenia *aks-preview* w wersji 0.4.1 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure *aks-preview* przy użyciu polecenia [dodawania rozszerzenia az,][az-extension-add] a następnie sprawdź dostępność dostępnych aktualizacji za pomocą polecenia [aktualizacji rozszerzenia az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Dostawca funkcji zasad zabezpieczeń zasobnika rejestru

Aby utworzyć lub zaktualizować klaster AKS do używania zasad zabezpieczeń zasobników, najpierw włącz flagę funkcji w ramach subskrypcji. Aby zarejestrować flagę funkcji *PodSecurityPolicyPreview,* użyj polecenia [az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

> [!CAUTION]
> Podczas rejestrowania funkcji w ramach subskrypcji nie można obecnie odeszło od rejestracji tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej ustawienia domyślne mogą być używane dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w subskrypcjach produkcyjnych. Użyj oddzielnej subskrypcji, aby przetestować funkcje w wersji zapoznawczej i zebrać opinie.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Trwa kilka minut, aby stan pokazał *zarejestrowany*. Stan rejestracji można sprawdzić za pomocą polecenia [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Gdy będzie to gotowe, odśwież rejestrację dostawcy zasobów *Microsoft.ContainerService* za pomocą polecenia [rejestru dostawcy az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Omówienie zasad zabezpieczeń zasobników

W klastrze Kubernetes kontroler wstępu jest używany do przechwytywania żądań do serwera interfejsu API, gdy ma zostać utworzony zasób. Kontroler dostępu można następnie *sprawdzić poprawność* żądania zasobu względem zestawu reguł lub *mutować* zasób, aby zmienić parametry wdrażania.

*PodSecurityPolicy* jest kontrolerem wstępu, który sprawdza poprawność specyfikacji zasobnika spełnia zdefiniowane wymagania. Te wymagania mogą ograniczać korzystanie z kontenerów uprzywilejowanych, dostęp do niektórych typów magazynu lub użytkownika lub grupy kontenera można uruchomić jako. Podczas próby wdrożenia zasobu, w którym specyfikacje zasobu nie spełniają wymagań określonych w zasadach zabezpieczeń zasobnika, żądanie jest odrzucane. Ta możliwość kontrolowania, jakie zasobniki można zaplanować w klastrze AKS, zapobiega niektórym możliwym lukom w zabezpieczeniach lub eskalacjom uprawnień.

Po włączeniu zasad zabezpieczeń zasobników w klastrze AKS stosowane są niektóre domyślne zasady. Te domyślne zasady zapewniają gotowe środowisko do definiowania, jakie zasobniki można zaplanować. Jednak użytkownicy klastra mogą napotkać problemy z wdrażaniem zasobników, dopóki nie zdefiniujesz własnych zasad. Zalecanym podejściem jest:

* Tworzenie klastra AKS
* Definiowanie własnych zasad zabezpieczeń zasobników
* Włączanie funkcji zasad zabezpieczeń zasobnika

Aby pokazać, jak domyślne zasady ograniczają wdrożenia zasobników, w tym artykule najpierw włączamy funkcję zasad zabezpieczeń zasobnika, a następnie tworzymy zasady niestandardowe.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Włączanie zasad zabezpieczeń zasobników w klastrze usługi AKS

Za pomocą polecenia [az aks update][az-aks-update] można włączyć lub wyłączyć zasady zabezpieczeń zasobnika. Poniższy przykład umożliwia zasad zabezpieczeń zasobnika na nazwę klastra *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*.

> [!NOTE]
> W przypadku rzeczywistych zastosowań nie włączaj zasad zabezpieczeń zasobników, dopóki nie zdefiniowano własnych zasad niestandardowych. W tym artykule włącz zasady zabezpieczeń zasobników jako pierwszy krok, aby zobaczyć, jak domyślne zasady ograniczają wdrożenia zasobników.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Domyślne zasady AKS

Po włączeniu zasad zabezpieczeń zasobników usługa AKS tworzy jedną domyślną zasadę o nazwie *uprzywilejowana*. Nie edytuj ani nie usuwaj zasad domyślnych. Zamiast tego utwórz własne zasady, które definiują ustawienia, które chcesz kontrolować. Przyjrzyjmy się najpierw, jakie są te domyślne zasady, jak wpływają one na wdrożenia zasobników.

Aby wyświetlić dostępne zasady, użyj polecenia [kubectl get psp,][kubectl-get] jak pokazano w poniższym przykładzie

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Zasady zabezpieczeń zasobnika *uprzywilejowanego* są stosowane do dowolnego uwierzytelnionego użytkownika w klastrze AKS. To przypisanie jest kontrolowane przez clusterroles i ClusterRoleBindings. Użyj [polecenia kubectl get rolebindings][kubectl-get] i wyszukaj *powiązanie default:privileged:* w obszarze nazw *systemu kube:*

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Jak pokazano na poniższym skondensowanym wyjściu, *psp:restricted* ClusterRole jest przypisany do wszystkich *użytkowników systemowych:uwierzytelnionych.* Ta możliwość zapewnia podstawowy poziom ograniczeń bez definiowania własnych zasad.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

Ważne jest, aby zrozumieć, jak te domyślne zasady współdziałają z żądaniami użytkowników, aby zaplanować zasobników przed rozpoczęciem tworzenia własnych zasad zabezpieczeń zasobnika. W następnych kilku sekcjach zaplanujmy niektóre zasobników, aby zobaczyć te domyślne zasady w działaniu.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Tworzenie użytkownika testowego w klastrze AKS

Domyślnie podczas korzystania z polecenia [az aks get-credentials][az-aks-get-credentials] poświadczenia, poświadczenia *administratora* dla `kubectl` klastra AKS są dodawane do konfiguracji. Użytkownik administratora pomija wymuszanie zasad zabezpieczeń zasobników. Jeśli używasz integracji usługi Azure Active Directory dla klastrów AKS, możesz zalogować się przy użyciu poświadczeń użytkownika niebędącego administratorem, aby wyświetlić wymuszanie zasad w działaniu. W tym artykule utwórzmy konto użytkownika testowego w klastrze AKS, którego można użyć.

Utwórz przykładową przestrzeń nazw o nazwie *psp-aks* dla zasobów testowych za pomocą polecenia [kubectl create namespace.][kubectl-create] Następnie utwórz konto usługi o nazwie *nonadmin-user* za pomocą polecenia [kubectl create serviceaccount:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Następnie utwórz rolebinding dla *nienależącego do użytkownika,* aby wykonać podstawowe akcje w obszarze nazw za pomocą [polecenia kubectl create rolebinding:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Tworzenie poleceń aliasu dla administratora i użytkownika niebędącego administratorem

Aby wyróżnić różnicę między zwykłym `kubectl` użytkownikiem administratora podczas korzystania z niego a użytkownikiem niebędącym administratorem utworzonym w poprzednich krokach, utwórz dwa aliasy wiersza polecenia:

* Alias **kubectl-admin** jest przeznaczony dla zwykłego użytkownika administratora i jest objęty zakresem obszaru nazw *psp-aks.*
* **Identyfikator kubectl-nonadminuser** jest przeznaczony dla *nienadmin-user* utworzonego w poprzednim kroku i jest objęty zakresem obszaru nazw *psp-aks.*

Utwórz te dwa aliasy, jak pokazano w następujących poleceniach:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testowanie tworzenia uprzywilejowanej zasobnika

Najpierw przetestujmy, co się dzieje, gdy `privileged: true`planujesz zasobnik z kontekstem zabezpieczeń . Ten kontekst zabezpieczeń eskaluje uprawnienia zasobnika. W poprzedniej sekcji, która pokazywała domyślne zasady zabezpieczeń zasobników usługi AKS, *zasady z ograniczeniami* powinny odmówić tego żądania.

Utwórz plik `nginx-privileged.yaml` o nazwie i wklej następujący manifest YAML:

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

Utwórz zasobnik za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Zasobnik nie może być zaplanowane, jak pokazano w poniższym przykładzie danych wyjściowych:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Zasobnik nie dociera do etapu planowania, więc nie ma żadnych zasobów do usunięcia przed przejściem dalej.

## <a name="test-creation-of-an-unprivileged-pod"></a>Przetestuj tworzenie nieuprzywilejowanej kapsuły

W poprzednim przykładzie specyfikacji zasobnika wymagane eskalacji uprzywilejowanej. To żądanie jest odrzucane przez domyślne zasady zabezpieczeń *zasobników ograniczone,* więc zasobnik nie powiedzie się zaplanować. Spróbujmy teraz uruchomić tę samą zasobnik NGINX bez żądania eskalacji uprawnień.

Utwórz plik `nginx-unprivileged.yaml` o nazwie i wklej następujący manifest YAML:

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

Utwórz zasobnik za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Zasobnik nie może być zaplanowane, jak pokazano w poniższym przykładzie danych wyjściowych:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Zasobnik nie dociera do etapu planowania, więc nie ma żadnych zasobów do usunięcia przed przejściem dalej.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testowanie tworzenia zasobnika z określonym kontekstem użytkownika

W poprzednim przykładzie obraz kontenera automatycznie próbował użyć roota do powiązania NGINX z portem 80. To żądanie zostało odrzucone przez domyślne zasady zabezpieczeń *zasobnika ograniczonego,* więc nie można uruchomić zasobnika. Spróbujmy teraz uruchomić tę samą kapsułę NGINX z `runAsUser: 2000`określonym kontekstem użytkownika, takim jak .

Utwórz plik `nginx-unprivileged-nonroot.yaml` o nazwie i wklej następujący manifest YAML:

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

Utwórz zasobnik za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Zasobnik nie może być zaplanowane, jak pokazano w poniższym przykładzie danych wyjściowych:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Zasobnik nie dociera do etapu planowania, więc nie ma żadnych zasobów do usunięcia przed przejściem dalej.

## <a name="create-a-custom-pod-security-policy"></a>Tworzenie niestandardowej zasady zabezpieczeń zasobników

Teraz, gdy widziałeś zachowanie domyślnych zasad zabezpieczeń zasobnika, zapewnijmy sposób dla *nienawidzonych użytkowników,* aby pomyślnie zaplanować zasobników.

Utwórzmy zasadę odrzucania zasobników, które żądają uprzywilejowanego dostępu. Inne opcje, takie jak *runAsUser* lub dozwolone *woluminy,* nie są jawnie ograniczone. Ten typ zasad odrzuca żądanie dostępu uprzywilejowanego, ale w przeciwnym razie umożliwia klastrowi uruchomienie żądanych zasobników.

Utwórz plik `psp-deny-privileged.yaml` o nazwie i wklej następujący manifest YAML:

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

Utwórz zasady za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Aby wyświetlić dostępne zasady, użyj [polecenia kubectl get psp,][kubectl-get] jak pokazano w poniższym przykładzie. Porównaj zasady *uprzywilejowane psp-deny* z *domyślną zasadą z ograniczeniami,* która została wymuszana w poprzednich przykładach, aby utworzyć zasobnik. Tylko korzystanie z *priv* eskalacji jest odrzucana przez zasady. Nie ma żadnych ograniczeń dla użytkownika lub grupy dla *zasad uprzywilejowanych psp-deny.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Zezwalaj kontu użytkownika na używanie niestandardowych zasad zabezpieczeń zasobników

W poprzednim kroku utworzono zasadę zabezpieczeń zasobnika, aby odrzucić zasobników, które żądają uprzywilejowanego dostępu. Aby zezwolić na użycie zasad, należy utworzyć *rolę* lub *rolę klastra*. Następnie należy skojarzyć jedną z tych ról przy użyciu *funkcji RoleBinding* lub *ClusterRoleBinding*.

W tym przykładzie należy utworzyć clusterrole, która umożliwia *korzystanie z* zasad *uprzywilejowanych psp-deny* utworzonych w poprzednim kroku. Utwórz plik `psp-deny-privileged-clusterrole.yaml` o nazwie i wklej następujący manifest YAML:

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

Utwórz grupę ClusterRole za pomocą polecenia [kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Teraz utwórz narzędzie ClusterRoleBinding, aby użyć roli klastra utworzonej w poprzednim kroku. Utwórz plik `psp-deny-privileged-clusterrolebinding.yaml` o nazwie i wklej następujący manifest YAML:

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

Utwórz element ClusterRoleBinding za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> W pierwszym kroku tego artykułu funkcja zasad zabezpieczeń zasobnika została włączona w klastrze AKS. Zalecaną praktyką było włączenie funkcji zasad zabezpieczeń zasobnika tylko po zdefiniowaniu własnych zasad. Jest to etap, na którym można włączyć funkcję zasad zabezpieczeń zasobnika. Zdefiniowano co najmniej jedną zasadę niestandardową, a konta użytkowników zostały skojarzone z tymi zasadami. Teraz można bezpiecznie włączyć funkcję zasad zabezpieczeń zasobnika i zminimalizować problemy spowodowane przez domyślne zasady.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Ponownie przetestuj utworzenie nieuprzywilejowanej kapsuły

Z niestandardowe zasady zabezpieczeń zasobnika stosowane i powiązanie dla konta użytkownika do korzystania z zasad, spróbujmy utworzyć nieuprzywilejowanych zasobnika ponownie. Użyj tego `nginx-privileged.yaml` samego manifestu, aby utworzyć zasobnik za pomocą [polecenia zastosuj kubectl:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Zasobnik jest pomyślnie zaplanowane. Po sprawdzeniu stanu zasobnika za pomocą [polecenia kubectl get strąki,][kubectl-get] zasobnik jest *uruchomiony:*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

W tym przykładzie pokazano, jak można utworzyć niestandardowe zasady zabezpieczeń zasobników, aby zdefiniować dostęp do klastra AKS dla różnych użytkowników lub grup. Domyślne zasady usługi AKS zapewniają ścisłe kontrole, jakie zasobniki mogą działać, więc utwórz własne zasady niestandardowe, aby następnie poprawnie zdefiniować potrzebne ograniczenia.

Usuń nieuprzywilejowaną zasobnik NGINX za pomocą polecenia [kubectl delete][kubectl-delete] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyłączyć zasady zabezpieczeń zasobnika, należy ponownie użyć polecenia [az aks update.][az-aks-update] Poniższy przykład wyłącza zasady zabezpieczeń zasobników na nazwie klastra *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Następnie usuń narzędzie ClusterRole i ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Usuń zasady zabezpieczeń za pomocą polecenia [kubectl delete][kubectl-delete] i określ nazwę manifestu YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Na koniec usuń obszar nazw *psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak utworzyć zasady zabezpieczeń zasobników, aby zapobiec użyciu dostępu uprzywilejowanego. Istnieje wiele funkcji, które zasady można wymusić, takich jak typ woluminu lub runas użytkownika. Aby uzyskać więcej informacji na temat dostępnych opcji, zobacz [dokumenty dotyczące odwołań do zasad zabezpieczeń kubernetes pod][kubernetes-policy-reference].

Aby uzyskać więcej informacji na temat ograniczania ruchu sieciowego, zobacz [Zabezpieczanie ruchu między zasobnikami przy użyciu zasad sieciowych w uzywce AKS][network-policies].

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

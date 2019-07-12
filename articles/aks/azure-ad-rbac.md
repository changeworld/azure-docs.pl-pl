---
title: Zasoby klastra kontroli RBAC i usługi Azure AD w usłudze Azure Kubernetes Service
description: Dowiedz się, jak ograniczyć dostęp do zasobów klastra przy użyciu kontroli dostępu opartej na rolach (RBAC) w usłudze Azure Kubernetes Service (AKS) za pomocą członkostwa w grupie usługi Azure Active Directory
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: mlearned
ms.openlocfilehash: fba54fd23fefbe0029b9a809b23568490f05b23e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616167"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Kontrola dostępu do zasobów klastra przy użyciu tożsamości usługi Azure Active Directory i kontroli dostępu opartej na rolach w usłudze Azure Kubernetes Service

Usługa Azure Kubernetes Service (AKS) można skonfigurować do uwierzytelniania użytkowników usługi Azure Active Directory (AD). W tej konfiguracji należy zarejestrować się do klastra usługi AKS przy użyciu tokenu uwierzytelniania usługi Azure AD. Można również skonfigurować Kubernetes kontroli dostępu opartej na rolach (RBAC), aby ograniczyć dostęp do zasobów klastra na podstawie członkostwa użytkownika tożsamości lub grupy.

W tym artykule pokazano, jak kontrolować dostęp do przestrzeni nazw i zasoby przy użyciu RBAC platformy Kubernetes w klastrze AKS klastra za pomocą członkostwa w grupie usługi Azure AD. Przykład grupy i użytkownicy są tworzone w usłudze Azure AD, a następnie role i RoleBindings są tworzone w klastrze AKS, aby przyznać odpowiednie uprawnienia do tworzenia i wyświetlania zasobów.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster AKS włączyć za pomocą integracji z usługą Azure AD. Jeśli potrzebujesz klastra AKS, zobacz [integracji usługi Azure Active Directory za pomocą usługi AKS][azure-ad-aks-cli].

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.61 lub później zainstalowane i skonfigurowane. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Tworzenie wersji demonstracyjnej grup w usłudze Azure AD

W tym artykule utworzymy dwie role użytkowników, których można użyć, aby pokazać, jak RBAC platformy Kubernetes i usługi Azure AD, kontrolować dostęp do zasobów klastra. Są używane następujące role dwa przykładowe:

* **Deweloper aplikacji**
    * Użytkownik o nazwie *aksdev* który jest częścią *appdev* grupy.
* **Inżynier niezawodność lokacji**
    * Użytkownik o nazwie *akssre* który jest częścią *opssre* grupy.

W środowiskach produkcyjnych można użyć istniejących użytkowników i grup w ramach dzierżawy usługi Azure AD.

Najpierw Pobierz identyfikator zasobu usługi AKS klastra przy użyciu [az aks show][az-aks-show] polecenia. Przypisz identyfikator zasobu do zmiennej o nazwie *AKS_ID* , dzięki czemu mogą być przywoływane w dodatkowych poleceń.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Tworzenie pierwszej grupy przykładu w usłudze Azure AD dla deweloperów aplikacji przy użyciu [utworzyć grupy usługi ad az][az-ad-group-create] polecenia. Poniższy przykład tworzy grupę o nazwie *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Teraz Utwórz przypisania ról platformy Azure dla *appdev* grupy za pomocą [utworzenia przypisania roli az][az-role-assignment-create] polecenia. To przypisanie umożliwia dowolny członek grupy, użyj `kubectl` do interakcji z klastrem usługi AKS, przyznając *roli użytkownika dla klastra usługi Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Jeśli zostanie wyświetlony błąd taki jak `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, odczekaj kilka sekund, aby uzyskać identyfikator obiektu grupy usługi Azure AD propagowane do katalogu, a następnie spróbuj `az role assignment create` ponownie polecenie.

Tworzenie drugiej grupy przykład, ten jeden dla SREs o nazwie *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Ponownie Utwórz przypisanie roli platformy Azure w taki sposób, aby udzielić członkom grupy *roli użytkownika dla klastra usługi Azure Kubernetes*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Tworzenie wersji demonstracyjnej użytkowników w usłudze Azure AD

Przy użyciu dwóch przykład grup utworzonych w usłudze Azure AD dla naszych deweloperów aplikacji i SREs teraz Utwórzmy dwóch przykładowych użytkowników. Aby przetestować integrację RBAC na końcu tego artykułu, zaloguj się do klastra usługi AKS przy użyciu tych kont.

Tworzenie pierwszego konta użytkownika w usłudze Azure AD przy użyciu [Utwórz użytkownika usługi ad az][az-ad-user-create] polecenia.

Poniższy przykład tworzy użytkownika z nazwą wyświetlaną *AKS Dev* i główną nazwę użytkownika (UPN) `aksdev@contoso.com`. Zaktualizuj nazwę UPN, aby uwzględnić zweryfikowanej domeny dla dzierżawy usługi Azure AD (Zastąp *contoso.com* z własnej domeny) i podaj własne bezpieczne `--password` poświadczeń:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Teraz Dodaj użytkownika do *appdev* grupę utworzoną w poprzedniej sekcji przy użyciu [dodać członka grupy ad az][az-ad-group-member-add] polecenia:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Tworzenie drugiego konta użytkownika. Poniższy przykład tworzy użytkownika z nazwą wyświetlaną *AKS SRE* i główną nazwę użytkownika (UPN) `akssre@contoso.com`. Ponownie, zaktualizuj nazwę UPN, aby uwzględnić zweryfikowanej domeny dla dzierżawy usługi Azure AD (Zastąp *contoso.com* z własnej domeny) i podaj własne bezpieczne `--password` poświadczeń:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Tworzenie zasobów klastra usługi AKS dla deweloperów aplikacji

Tworzone są teraz grup usługi Azure AD i użytkowników. Przypisania ról platformy Azure zostały utworzone dla członków grupy nawiązać połączenie z klastrem AKS jako zwykły użytkownik. Teraz Skonfigurujmy klastra AKS, aby umożliwić dostęp tych różnych grup, do określonych zasobów.

Najpierw pobierz klastra poświadczeń administratora przy użyciu [az aks get-credentials][az-aks-get-credentials] polecenia. W jednym z następujących sekcji, możesz uzyskać zwykłych *użytkownika* poświadczeń, aby zobaczyć uwierzytelniania usługi Azure AD klastra przepływu w akcji.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Tworzenie przestrzeni nazw w klastrze usługi AKS przy użyciu [kubectl tworzenie przestrzeni nazw][kubectl-create] polecenia. Poniższy przykład tworzy nazwę przestrzeni nazw *dev*:

```console
kubectl create namespace dev
```

W usłudze Kubernetes *role* zdefiniować uprawnienia, aby przyznać użytkownikom, i *RoleBindings* zastosować je do odpowiednich użytkowników lub grup. Te przypisania można zastosować do danej przestrzeni nazw lub dla całego klastra. Aby uzyskać więcej informacji, zobacz [autoryzacji RBAC przy użyciu][rbac-authorization].

Najpierw Utwórz rolę dla *dev* przestrzeni nazw. Ta rola przyznaje pełnych uprawnień do przestrzeni nazw. W środowiskach produkcyjnych można określić bardziej szczegółowych uprawnień dla różnych użytkowników lub grup.

Utwórz plik o nazwie `role-dev-namespace.yaml` i wklej następujące manifest YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Tworzenie przy użyciu roli [zastosować kubectl][kubectl-apply] polecenia i podaj nazwę pliku manifestu usługi YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Następnie Pobierz identyfikator zasobu dla *appdev* grupy za pomocą [Pokaż grupy ad az][az-ad-group-show] polecenia. Ta grupa jest ustawiana jako podmiot RoleBinding w następnym kroku.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Teraz Utwórz RoleBinding dla *appdev* grupę, aby uzyskać dostęp do przestrzeni nazw za pomocą wcześniej utworzonej roli. Utwórz plik o nazwie `rolebinding-dev-namespace.yaml` i wklej następujące manifest YAML. W ostatnim wierszu, Zastąp *groupObjectId* z danymi wyjściowymi identyfikator obiektu grupy z poprzedniego polecenia:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Tworzenie za pomocą RoleBinding [zastosować kubectl][kubectl-apply] polecenia i podaj nazwę pliku manifestu usługi YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Tworzenie zasobów klastra usługi AKS dla SREs

Teraz Powtórz poprzednie kroki, aby utworzyć przestrzeń nazw, roli i RoleBinding dla SREs.

Najpierw utworzyć przestrzeń nazw dla *sre* przy użyciu [kubectl tworzenie przestrzeni nazw][kubectl-create] polecenia:

```console
kubectl create namespace sre
```

Utwórz plik o nazwie `role-sre-namespace.yaml` i wklej następujące manifest YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Tworzenie przy użyciu roli [zastosować kubectl][kubectl-apply] polecenia i podaj nazwę pliku manifestu usługi YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Pobierz identyfikator zasobu dla *opssre* grupy za pomocą [Pokaż grupy ad az][az-ad-group-show] polecenia:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Utwórz RoleBinding dla *opssre* grupę, aby uzyskać dostęp do przestrzeni nazw za pomocą wcześniej utworzonej roli. Utwórz plik o nazwie `rolebinding-sre-namespace.yaml` i wklej następujące manifest YAML. W ostatnim wierszu, Zastąp *groupObjectId* z danymi wyjściowymi identyfikator obiektu grupy z poprzedniego polecenia:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Tworzenie za pomocą RoleBinding [zastosować kubectl][kubectl-apply] polecenia i podaj nazwę pliku manifestu usługi YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Korzystać z zasobów klastra przy użyciu tożsamości usługi Azure AD

Teraz możemy przetestować pracy oczekiwanych uprawnień podczas tworzenia i zarządzania zasobami w klastrze AKS. W tych przykładach możesz zaplanować i wyświetlić zasobników w przestrzeni nazw przypisanych przez użytkownika. Następnie próbujesz harmonogram i widoku zasobników poza przypisane przestrzeni nazw.

Najpierw należy zresetować *plik kubeconfig* przy użyciu kontekstu [az aks get-credentials][az-aks-get-credentials] polecenia. W poprzedniej sekcji można ustawić kontekstu przy użyciu poświadczeń administratora klastra. Administrator pomija logowania usługi Azure AD monity. Bez `--admin` parametru kontekstu użytkownika jest stosowana wymagającego wszystkich żądań uwierzytelnienia przy użyciu usługi Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Harmonogram podstawowe zasobnik NGINX, przy użyciu [kubectl Uruchom][kubectl-run] polecenia w pliku *dev* przestrzeni nazw:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Jako znak w wierszu polecenia wprowadź poświadczenia dla własnego `appdev@contoso.com` konto utworzone na początku tego artykułu. Po pomyślnym zalogowaniu się, token konta są buforowane w przyszłości `kubectl` poleceń. NGINX jest pomyślnie zaplanować, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Teraz za pomocą [kubectl get pods-][kubectl-get] polecenie, aby wyświetlić zasobników w *dev* przestrzeni nazw.

```console
kubectl get pods --namespace dev
```

Jak pokazano w następujących przykładowych danych wyjściowych, NGINX pod jest pomyślnie *systemem*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Można tworzyć i wyświetlać zasoby klastra poza przypisane przestrzeni nazw

Teraz próby wyświetlenia zasobników poza *dev* przestrzeni nazw. Użyj [kubectl get pods-][kubectl-get] polecenie ponownie, ten czas, aby zobaczyć `--all-namespaces` w następujący sposób:

```console
kubectl get pods --all-namespaces
```

Członkostwa w grupie użytkowników nie ma roli Kubernetes, która zezwala na tę akcję, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

W ten sam sposób, spróbuj zaplanować zasobnik w innej przestrzeni nazw, takich jak *sre* przestrzeni nazw. Członkostwo w grupie użytkownika nie jest zgodny z roli usługi Kubernetes i RoleBinding przyznać te uprawnienia, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testowanie SRE dostęp do zasobów klastra usługi AKS

Aby upewnić się, że naszych członkostwa w grupie usługi Azure AD i kontroli RBAC usługi Kubernetes działała prawidłowo w różnych użytkowników i grup, spróbuj wykonać poprzednie polecenia, po zalogowaniu się jako *opssre* użytkownika.

Resetuj *plik kubeconfig* przy użyciu kontekstu [az aks get-credentials][az-aks-get-credentials] polecenie usuwa token uwierzytelniania wcześniej w pamięci podręcznej dla *aksdev* użytkownika:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Spróbuj harmonogram i widoku zasobników w przypisanych *sre* przestrzeni nazw. Po wyświetleniu monitu zaloguj się przy użyciu własnych `opssre@contoso.com` poświadczeń utworzonych na początku tego artykułu:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Jak pokazano w następujących przykładowych danych wyjściowych, można pomyślnie utworzyć i wyświetlić zasobników:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Wypróbuj teraz wyświetlić lub zaplanować zasobników poza przypisane SRE przestrzeni nazw:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Te `kubectl` polecenia nie powiedzie się, jak pokazano w następujących przykładowych danych wyjściowych. Członkostwo w grupie użytkownika i roli usługi Kubernetes i RoleBindings nie udzielić uprawnień do utworzenia lub Menedżera zasobów w innych przestrzeniach nazw:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule utworzono zasobów w klastrze usługi AKS i użytkowników i grup w usłudze Azure AD. Aby wyczyścić te zasoby, uruchom następujące polecenia:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o tym, jak zabezpieczyć klastry Kubernetes, zobacz [opcje dostęp i tożsamość dla usługi AKS)][rbac-authorization].

Aby uzyskać najlepsze rozwiązania dotyczące kontrolowanie tożsamości i zasobów, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md

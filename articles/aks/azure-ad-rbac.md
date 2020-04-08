---
title: Używanie usługi Azure AD i RBAC dla klastrów
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak korzystać z członkostwa w grupie usługi Azure Active Directory w celu ograniczenia dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach (RBAC) w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: ad195085c049776bf0db418c57f2c72830f1adff
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803573"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Kontroluj dostęp do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości usługi Azure Active Directory w usłudze Azure Kubernetes

Usługę Azure Kubernetes Service (AKS) można skonfigurować do używania usługi Azure Active Directory (AD) do uwierzytelniania użytkowników. W tej konfiguracji logujesz się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD. Można również skonfigurować kontrolę dostępu opartą na rolach kubernetes (RBAC), aby ograniczyć dostęp do zasobów klastra na podstawie tożsamości użytkownika lub członkostwa w grupie.

W tym artykule pokazano, jak używać członkostwa w grupie usługi Azure AD do kontrolowania dostępu do obszarów nazw i zasobów klastra przy użyciu narzędzia Kubernetes RBAC w klastrze AKS. Przykładowe grupy i użytkownicy są tworzone w usłudze Azure AD, a następnie role i rolebindings są tworzone w klastrze AKS, aby udzielić odpowiednich uprawnień do tworzenia i wyświetlania zasobów.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS włączony z integracją usługi Azure AD. Jeśli potrzebujesz klastra AKS, zobacz [Integrowanie usługi Azure Active Directory z usługą AKS][azure-ad-aks-cli].

Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Tworzenie grup demonstracyjnych w usłudze Azure AD

W tym artykule utwórzmy dwie role użytkownika, które mogą służyć do pokazywalenia, jak Kubernetes RBAC i Azure AD kontrolują dostęp do zasobów klastra. Używane są następujące dwie przykładowe role:

* **Deweloper aplikacji**
    * Użytkownik o nazwie *aksdev,* który jest częścią grupy *appdev.*
* **Inżynier niezawodności zakładu**
    * Użytkownik o nazwie *akssre,* który jest częścią grupy *opssre.*

W środowiskach produkcyjnych można używać istniejących użytkowników i grup w dzierżawie usługi Azure AD.

Najpierw pobierz identyfikator zasobu klastra AKS za pomocą polecenia [az aks show.][az-aks-show] Przypisz identyfikator zasobu do zmiennej o nazwie *AKS_ID,* aby można było odwoływać się do niej w dodatkowych poleceniach.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Utwórz pierwszą przykładową grupę w usłudze Azure AD dla deweloperów aplikacji za pomocą polecenia [tworzenie grupy reklam az.][az-ad-group-create] Poniższy przykład tworzy grupę o nazwie *appdev:*

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Teraz utwórz przypisanie roli platformy Azure dla grupy *appdev* za pomocą [polecenia tworzenia przypisania roli az.][az-role-assignment-create] To przypisanie umożliwia każdemu `kubectl` członkowi grupy używanie do interakcji z klastrem AKS, przyznając im *rolę użytkownika klastra usługi Azure Kubernetes.*

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Jeśli zostanie wyświetlony błąd, taki jak `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, poczekaj kilka sekund na identyfikator obiektu grupy usługi `az role assignment create` Azure AD do propagacji za pośrednictwem katalogu, a następnie spróbuj ponownie.

Utwórz drugą przykładową grupę, tę dla SRE o nazwie *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Ponownie utwórz przypisanie roli platformy Azure, aby przyznać członkom grupy *rolę użytkownika klastra usługi Azure Kubernetes:*

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Tworzenie użytkowników demonstracyjnych w usłudze Azure AD

Z dwóch przykładowych grup utworzonych w usłudze Azure AD dla naszych deweloperów aplikacji i SREs, teraz pozwala utworzyć dwóch przykładowych użytkowników. Aby przetestować integrację RBAC na końcu artykułu, należy zalogować się do klastra AKS przy tych kontach.

Utwórz pierwsze konto użytkownika w usłudze Azure AD za pomocą polecenia [utwórz az ad.][az-ad-user-create]

Poniższy przykład tworzy użytkownika o wyświetlanej nazwie *AKS Dev* i `aksdev@contoso.com`głównej nazwie użytkownika (UPN) . Zaktualizuj nazwę UPN, aby uwzględnić *contoso.com* zweryfikowaną domenę dzierżawy usługi Azure AD `--password` (zastąp contoso.com własną domeną) i podaj własne bezpieczne poświadczenia:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Teraz dodaj użytkownika do grupy *appdev* utworzonej w poprzedniej sekcji za pomocą [polecenia dodaj grupę reklam az:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Utwórz drugie konto użytkownika. Poniższy przykład tworzy użytkownika o wyświetlanej nazwie *AKS SRE* i `akssre@contoso.com`głównej nazwie użytkownika (UPN) z . Ponownie zaktualizuj nazwę UPN, aby uwzględnić *contoso.com* zweryfikowaną domenę dzierżawy usługi Azure AD `--password` (zastąp contoso.com własną domeną) i podaj własne bezpieczne poświadczenia:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Tworzenie zasobów klastra AKS dla deweloperów aplikacji

Grupy usługi Azure AD i użytkownicy są teraz tworzone. Przypisania ról platformy Azure zostały utworzone dla członków grupy, aby połączyć się z klastrem AKS jako zwykły użytkownik. Teraz skonfigurujmy klaster AKS, aby umożliwić tym różnym grupom dostęp do określonych zasobów.

Najpierw pobierz poświadczenia administratora klastra za pomocą polecenia [az aks get-credentials.][az-aks-get-credentials] W jednej z poniższych sekcji otrzymasz poświadczenia klastra zwykłych *użytkowników,* aby wyświetlić przepływ uwierzytelniania usługi Azure AD w akcji.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Utwórz obszar nazw w klastrze AKS za pomocą polecenia [kubectl create namespace.][kubectl-create] Poniższy przykład tworzy *dev*nazwy obszaru nazw:

```console
kubectl create namespace dev
```

W umięsień *ról role* definiują uprawnienia do udzielania, a *rolebindings* stosuje je do żądanych użytkowników lub grup. Te przypisania mogą być stosowane do danego obszaru nazw lub w całym klastrze. Aby uzyskać więcej informacji, zobacz [Korzystanie z autoryzacji RBAC][rbac-authorization].

Najpierw utwórz rolę dla obszaru nazw *deweloperów.* Ta rola udziela pełnych uprawnień do obszaru nazw. W środowiskach produkcyjnych można określić bardziej szczegółowe uprawnienia dla różnych użytkowników lub grup.

Utwórz plik `role-dev-namespace.yaml` o nazwie i wklej następujący manifest YAML:

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

Utwórz rolę za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Następnie uzyskaj identyfikator zasobu dla grupy *appdev* za pomocą polecenia [az ad group show.][az-ad-group-show] Ta grupa jest ustawiona jako temat RoleBinding w następnym kroku.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Teraz utwórz RoleBinding dla grupy *appdev* używać wcześniej utworzonej roli dostępu do obszaru nazw. Utwórz plik `rolebinding-dev-namespace.yaml` o nazwie i wklej następujący manifest YAML. W ostatnim wierszu zastąp *identyfikator groupObjectId* dane wyjściowe identyfikatora obiektu grupy z poprzedniego polecenia:

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

Utwórz rolebinding za pomocą [polecenia zastosuj kubectl][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Tworzenie zasobów klastra AKS dla SFR

Teraz powtórz poprzednie kroki, aby utworzyć obszar nazw, role i rolebinding dla SRC.

Najpierw utwórz obszar nazw dla *sre* za pomocą [polecenia kubectl create obszar nazw:][kubectl-create]

```console
kubectl create namespace sre
```

Utwórz plik `role-sre-namespace.yaml` o nazwie i wklej następujący manifest YAML:

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

Utwórz rolę za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Pobierz identyfikator zasobu dla grupy *opssre* za pomocą polecenia [az ad group show:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Utwórz rolebinding dla grupy *opssre* używać wcześniej utworzonej roli dostępu do obszaru nazw. Utwórz plik `rolebinding-sre-namespace.yaml` o nazwie i wklej następujący manifest YAML. W ostatnim wierszu zastąp *identyfikator groupObjectId* dane wyjściowe identyfikatora obiektu grupy z poprzedniego polecenia:

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

Utwórz rolebinding za pomocą [polecenia zastosuj kubectl][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interakcja z zasobami klastra przy użyciu tożsamości usługi Azure AD

Teraz przetestujmy oczekiwane uprawnienia działają podczas tworzenia zasobów i zarządzania nimi w klastrze AKS. W tych przykładach można zaplanować i wyświetlić zasobników w obszarze nazw użytkownika przypisane. Następnie spróbuj zaplanować i wyświetlić zasobników poza przypisaną przestrzenią nazw.

Najpierw zresetuj kontekst *kubeconfig* za pomocą polecenia [az aks get-credentials.][az-aks-get-credentials] W poprzedniej sekcji można ustawić kontekst przy użyciu poświadczeń administratora klastra. Użytkownik administratora pomija monity logowania usługi Azure AD. Bez `--admin` parametru kontekst użytkownika jest stosowany, który wymaga wszystkich żądań do uwierzytelnienia przy użyciu usługi Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Zaplanuj podstawową zasobnik NGINX za pomocą polecenia [kubectl run][kubectl-run] w obszarze nazw *deweloperów:*

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Jako monit logowania wprowadź poświadczenia dla własnego `appdev@contoso.com` konta utworzonego na początku artykułu. Po pomyślnym zalogowaniu token konta jest buforowany dla przyszłych `kubectl` poleceń. NGINX jest pomyślnie harmonogram, jak pokazano w poniższym przykładzie danych wyjściowych:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Teraz użyj [polecenia kubectl get zasobników,][kubectl-get] aby wyświetlić zasobników w obszarze nazw *deweloperów.*

```console
kubectl get pods --namespace dev
```

Jak pokazano w poniższym przykładzie danych wyjściowych, zasobnik NGINX jest pomyślnie *uruchomiony:*

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Tworzenie i wyświetlanie zasobów klastra poza przypisaną przestrzenią nazw

Teraz spróbuj wyświetlić zasobników poza obszarem nazw *deweloperów.* Użyj [polecenia kubectl get strąki][kubectl-get] ponownie, tym razem, aby zobaczyć `--all-namespaces` w następujący sposób:

```console
kubectl get pods --all-namespaces
```

Członkostwo w grupie użytkownika nie ma roli Kubernetes, która umożliwia tę akcję, jak pokazano w poniższym przykładzie danych wyjściowych:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

W ten sam sposób spróbuj zaplanować zasobnik w różnych przestrzeni nazw, takich jak obszar nazw *sre.* Członkostwo w grupie użytkownika nie jest zgodne z rolą kubernetes i rolebinding, aby udzielić tych uprawnień, jak pokazano w poniższym przykładzie danych wyjściowych:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testowanie dostępu SRE do zasobów klastra AKS

Aby potwierdzić, że nasze członkostwo w grupie usługi Azure AD i usługi Kubernetes RBAC działają poprawnie między różnymi użytkownikami i grupami, spróbuj poprzednich poleceń po zalogowaniu się jako użytkownik *opssre.*

Zresetuj kontekst *kubeconfig* przy użyciu polecenia [az aks get-credentials,][az-aks-get-credentials] które czyści wcześniej buforowany token uwierzytelniania dla użytkownika *aksdev:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Spróbuj zaplanować i wyświetlić zasobników w przypisanej przestrzeni nazw *sre.* Po wyświetleniu monitu zaloguj `opssre@contoso.com` się przy użyciu własnych poświadczeń utworzonych na początku artykułu:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Jak pokazano w poniższym przykładzie danych wyjściowych, można pomyślnie utworzyć i wyświetlić zasobników:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Teraz spróbuj wyświetlić lub zaplanować zasobników poza przypisaną przestrzenią nazw SRE:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Polecenia `kubectl` te nie powiodą się, jak pokazano w poniższym przykładzie danych wyjściowych. Członkostwo w grupie użytkownika i rola i role kubernetes i rolebindings nie udzielają uprawnień do tworzenia lub menedżerać się zasobów w innych obszarach nazw:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule utworzono zasoby w klastrze AKS oraz użytkowników i grupy w usłudze Azure AD. Aby wyczyścić wszystkie te zasoby, uruchom następujące polecenia:

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zabezpieczania klastrów kubernetes, zobacz [Opcje dostępu i tożsamości dla usługi AKS).][rbac-authorization]

Aby uzyskać najlepsze rozwiązania dotyczące kontroli tożsamości i zasobów, zobacz [Najważniejsze wskazówki dotyczące uwierzytelniania i autoryzacji w uzywce AKS][operator-best-practices-identity].

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

---
title: Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia klastra usługi Azure Kubernetes (AKS) z włączoną usługą Azure i usługą Azure Directory
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253054"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integruj usługę Azure Active Directory z usługą Azure Kubernetes Service przy użyciu interfejsu wiersza polecenia platformy Azure

Usługę Azure Kubernetes Service (AKS) można skonfigurować do używania usługi Azure Active Directory (AD) do uwierzytelniania użytkowników. W tej konfiguracji można zalogować się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD. Operatorzy klastrów mogą również konfigurować kontrolę dostępu opartą na rolach kubernetes (RBAC) na podstawie tożsamości użytkownika lub członkostwa w grupie katalogów.

W tym artykule pokazano, jak utworzyć wymagane składniki usługi Azure AD, a następnie wdrożyć klaster obsługujący usługę Azure AD i utworzyć podstawową rolę RBAC w klastrze AKS. Można również [wykonać te kroki za pomocą witryny Azure portal][azure-ad-portal].

Aby uzyskać pełny przykładowy skrypt użyty w tym artykule, zobacz [przykłady interfejsu wiersza polecenia platformy Azure — integracja AKS z usługą Azure AD][complete-script].

Obowiązują następujące ograniczenia:

- Usługę Azure AD można włączyć tylko podczas tworzenia nowego klastra z włączoną funkcją RBAC. Nie można włączyć usługi Azure AD w istniejącym klastrze usługi AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Przejdź [https://shell.azure.com](https://shell.azure.com) do, aby otworzyć Cloud Shell w przeglądarce.

Aby uzyskać spójność i ułatwić uruchamianie poleceń w tym artykule, utwórz zmienną dla żądanej nazwy klastra AKS. W poniższym przykładzie użyto nazwy *myakscluster:*

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Omówienie uwierzytelniania usługi Azure AD

Uwierzytelnianie usługi Azure AD jest dostarczane do klastrów AKS z OpenID Connect. OpenID Connect to warstwa tożsamości zbudowana na szczycie protokołu OAuth 2.0. Aby uzyskać więcej informacji na temat OpenID Connect, zobacz [dokumentację połączenia z otwartym identyfikatorem][open-id-connect].

Wewnątrz klastra Kubernetes uwierzytelnianie tokenu elementu webhook służy do weryfikacji tokenów uwierzytelniania. Uwierzytelnianie tokenu elementu webhook jest skonfigurowane i zarządzane jako część klastra AKS. Aby uzyskać więcej informacji na temat uwierzytelniania tokenów elementu webhook, zobacz [dokumentację uwierzytelniania elementu webhook][kubernetes-webhook].

> [!NOTE]
> Podczas konfigurowania usługi Azure AD dla uwierzytelniania AKS skonfigurowano dwie aplikacje usługi Azure AD. Ta operacja musi zostać ukończona przez administratora dzierżawy platformy Azure.

## <a name="create-azure-ad-server-component"></a>Tworzenie składnika serwera usługi Azure AD

Aby zintegrować z usługą AKS, należy utworzyć i używać aplikacji usługi Azure AD, która działa jako punkt końcowy dla żądań tożsamości. Pierwsza aplikacja usługi Azure AD, której potrzebujesz, uzyskuje członkostwo w grupie usługi Azure AD dla użytkownika.

Utwórz składnik aplikacji serwera za pomocą polecenia [tworzenie aplikacji reklamowej az,][az-ad-app-create] a następnie zaktualizuj oświadczenia o członkostwie grupy za pomocą polecenia [az ad app update.][az-ad-app-update] W poniższym przykładzie użyto *zmiennej aksname* zdefiniowanej w sekcji [Przed rozpoczęciem](#before-you-begin) i tworzy zmienną

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Teraz utwórz jednostkę usługi dla aplikacji serwera za pomocą polecenia [az ad sp create.][az-ad-sp-create] Ten podmiot zabezpieczeń usługi jest używany do uwierzytelniania się w ramach platformy Azure. Następnie pobierz klucz tajny jednostki usługi za pomocą polecenia [resetowania poświadczeń az ad sp][az-ad-sp-credential-reset] i przypisz do zmiennej o nazwie *serverApplicationSecret* do użycia w jednym z następujących kroków:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Usługa Azure AD potrzebuje uprawnień do wykonywania następujących akcji:

* Odczytywanie danych katalogu
* Logowanie i odczyt profilu użytkownika

Przypisz te uprawnienia za pomocą [polecenia dodaj aplikację reklamową az:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Na koniec przyznaj uprawnienia przypisane w poprzednim kroku dla aplikacji serwera za pomocą polecenia [udzielania uprawnień aplikacji reklamowej az.][az-ad-app-permission-grant] Ten krok kończy się niepowodzeniem, jeśli bieżące konto nie jest administratorem dzierżawy. Należy również dodać uprawnienia dla aplikacji usługi Azure AD, aby zażądać informacji, które w przeciwnym razie mogą wymagać zgody administracyjnej przy użyciu [zgody administratora uprawnień aplikacji reklamowej az:][az-ad-app-permission-admin-consent]

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Tworzenie składnika klienta usługi Azure AD

Druga aplikacja usługi Azure AD jest używana, gdy użytkownik loguje się do`kubectl`klastra AKS za pomocą interfejsu wiersza polecenia Kubernetes ( ). Ta aplikacja kliencka pobiera żądanie uwierzytelnienia od użytkownika i weryfikuje ich poświadczenia i uprawnienia. Utwórz aplikację usługi Azure AD dla składnika klienta za pomocą polecenia [tworzenia aplikacji reklamowej az:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Utwórz jednostkę usługi dla aplikacji klienckiej za pomocą polecenia [az ad sp create:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Pobierz identyfikator oAuth2 dla aplikacji serwera, aby umożliwić przepływ uwierzytelniania między dwoma składnikami aplikacji za pomocą polecenia [az ad app show.][az-ad-app-show] Ten identyfikator oAuth2 jest używany w następnym kroku.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Dodaj uprawnienia dla składników aplikacji klienckiej i aplikacji serwera, aby użyć przepływu komunikacji oAuth2 za pomocą [polecenia dodaj aplikację reklamową az.][az-ad-app-permission-add] Następnie udziel uprawnień aplikacji klienckiej do komunikacji z aplikacją serwera za pomocą polecenia [udzielania uprawnień aplikacji reklamowej az:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Wdrażanie klastra

Po utworzeniu dwóch aplikacji usługi Azure AD utwórz teraz sam klaster AKS. Najpierw utwórz grupę zasobów za pomocą polecenia [utwórz grupę AZ.][az-group-create] Poniższy przykład tworzy grupę zasobów w regionie *EastUS:*

Tworzenie grupy zasobów dla klastra:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Pobierz identyfikator dzierżawy subskrypcji platformy Azure przy użyciu polecenia [show konta az.][az-account-show] Następnie utwórz klaster AKS za pomocą polecenia [az aks create.][az-aks-create] Polecenie utworzenia klastra AKS udostępnia identyfikatory serwerów i aplikacji klienckich, główny klucz tajny usługi aplikacji serwera oraz identyfikator dzierżawy:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Na koniec pobierz poświadczenia administratora klastra za pomocą polecenia [az aks get-credentials.][az-aks-get-credentials] W jednym z następujących kroków otrzymasz poświadczenia klastra zwykłych *użytkowników,* aby wyświetlić przepływ uwierzytelniania usługi Azure AD w akcji.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Tworzenie powiązania RBAC

Przed kontem usługi Azure Active Directory można używać z klastrem AKS, należy utworzyć powiązanie roli lub powiązanie roli klastra. *Role* definiują uprawnienia do udzielania, a *powiązania stosują* je do żądanych użytkowników. Te przypisania mogą być stosowane do danego obszaru nazw lub w całym klastrze. Aby uzyskać więcej informacji, zobacz [Korzystanie z autoryzacji RBAC][rbac-authorization].

Pobierz nazwę głównej użytkownika (UPN) dla użytkownika aktualnie zalogowanego za pomocą polecenia [az ad signed-in-user show.][az-ad-signed-in-user-show] To konto użytkownika jest włączone dla integracji usługi Azure AD w następnym kroku.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Jeśli użytkownik, dla którego udzielasz powiązania RBAC, znajduje się w tej samej dzierżawie usługi Azure AD, przypisz uprawnienia na podstawie *nazwy użytkownikaPrincipalName*. Jeśli użytkownik znajduje się w innej dzierżawie usługi Azure AD, kwerendy i używać *objectId* właściwości zamiast.

Utwórz manifest YAML o nazwie `basic-azure-ad-binding.yaml` i wklej następującą zawartość. W ostatnim wierszu zastąp *userPrincipalName_or_objectId* wyjściem z owanie UPN lub identyfikatorem obiektu z poprzedniego polecenia:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Utwórz narzędzie ClusterRoleBinding za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Dostęp do klastra za pomocą usługi Azure AD

Teraz przetestujmy integrację uwierzytelniania usługi Azure AD dla klastra AKS. Ustaw `kubectl` kontekst konfiguracji, aby używał poświadczeń zwykłych użytkowników. Ten kontekst przekazuje wszystkie żądania uwierzytelniania z powrotem za pośrednictwem usługi Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Teraz użyj [polecenia kubectl get strąki,][kubectl-get] aby wyświetlić zasobników we wszystkich obszarach nazw:

```console
kubectl get pods --all-namespaces
```

Otrzymasz monit logowania do uwierzytelniania przy użyciu poświadczeń usługi Azure AD przy użyciu przeglądarki sieci web. Po pomyślnym uwierzytelnieniu `kubectl` polecenie wyświetla zasobniki w klastrze AKS, jak pokazano w poniższym przykładzie danych wyjściowych:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Token uwierzytelniania `kubectl` odebrany dla jest buforowany. Zostanie wyświetlony monit o zalogowanie się tylko wtedy, gdy token wygasł lub plik konfiguracji Kubernetes zostanie ponownie utworzony.

Jeśli po pomyślnym zalogowaniu się przy użyciu przeglądarki sieci Web zostanie wyświetlony komunikat o błędzie autoryzacji, sprawdź następujące możliwe problemy:

```output
error: You must be logged in to the server (Unauthorized)
```

* Zdefiniowano odpowiedni identyfikator obiektu lub numer UPN, w zależności od tego, czy konto użytkownika znajduje się w tej samej dzierżawie usługi Azure AD, czy nie.
* Użytkownik nie jest członkiem więcej niż 200 grup.
* Klucz tajny zdefiniowany w rejestracji aplikacji dla serwera odpowiada wartości skonfigurowanej przy użyciu`--aad-server-app-secret`

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełny skrypt zawierający polecenia pokazane w tym artykule, zobacz [skrypt integracji usługi Azure AD w repozytorium próbek usługi AKS][complete-script].

Aby używać użytkowników i grup usługi Azure AD do kontrolowania dostępu do zasobów klastra, zobacz [Kontrolowanie dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości usługi Azure AD w usłudze AKS.][azure-ad-rbac]

Aby uzyskać więcej informacji na temat zabezpieczania klastrów kubernetes, zobacz [Opcje dostępu i tożsamości dla usługi AKS).][rbac-authorization]

Aby uzyskać najlepsze rozwiązania dotyczące kontroli tożsamości i zasobów, zobacz [Najważniejsze wskazówki dotyczące uwierzytelniania i autoryzacji w uzywce AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md

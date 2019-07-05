---
title: Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service
description: Dowiedz się, jak korzystać z wiersza polecenia platformy Azure, aby utworzyć i klastrów z obsługą usługi Azure Active Directory Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: afb554307fd255d1863fc1508cef3703d4dc9f9e
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561160"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service przy użyciu wiersza polecenia platformy Azure

Usługa Azure Kubernetes Service (AKS) można skonfigurować do uwierzytelniania użytkowników usługi Azure Active Directory (AD). W tej konfiguracji możesz zalogować się do klastra usługi AKS przy użyciu tokenu uwierzytelniania usługi Azure AD. Operatorów klastra można również skonfigurować kontroli dostępu opartej na rolach Kubernetes (RBAC) na podstawie członkostwa grupy użytkownika tożsamości lub katalogu.

W tym artykule pokazano, jak utworzyć wymaganych składników usługi Azure AD, następnie wdrożyć klastrze platformy Azure z obsługą usługi AD i utworzyć rolę RBAC podstawowa w klastrze AKS. Możesz również [wykonaj następujące kroki w witrynie Azure portal][azure-ad-portal].

Aby uzyskać kompletny przykładowy skrypt używane w tym artykule, zobacz [przykłady interfejsu wiersza polecenia platformy Azure — usłudze AKS integracji z usługą Azure AD][complete-script].

Obowiązują następujące ograniczenia:

- Usługa Azure AD można włączyć tylko podczas tworzenia klastra nowe, włączone RBAC. Nie można włączyć usługi Azure AD w istniejącym klastrze usługi AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.61 lub później zainstalowane i skonfigurowane. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

W celu zachowania spójności i pomoc, Uruchom polecenia w tym artykule należy utworzyć zmienną z odpowiednią nazwę klastra AKS. W poniższym przykładzie użyto nazwy *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Omówienie uwierzytelniania usługi Azure AD

Uwierzytelnianie usługi Azure AD jest udostępniane na klastry usługi AKS przy użyciu protokołu OpenID Connect. OpenID Connect jest warstwy tożsamości korzystających z protokołu OAuth 2.0. Aby uzyskać więcej informacji na temat protokołu OpenID Connect, zobacz [Open ID connect dokumentacji][open-id-connect].

Z wewnątrz klastra Kubernetes uwierzytelnianie przy użyciu elementu Webhook tokenów służy do Sprawdź tokeny uwierzytelniania. Uwierzytelnianie przy użyciu elementu Webhook tokenów jest konfigurowane i zarządzane w ramach klastra AKS. Aby uzyskać więcej informacji na temat uwierzytelniania tokenu elementu Webhook, zobacz [dokumentacji uwierzytelniania elementu webhook][kubernetes-webhook].

> [!NOTE]
> Podczas konfigurowania usługi Azure AD do uwierzytelniania usługi AKS, są skonfigurowane dwie aplikacje usługi Azure AD. Musi być zakończona przez administratora dzierżawy usługi Azure.

## <a name="create-azure-ad-server-component"></a>Utwórz składnik serwera usługi Azure AD

Aby zintegrować z usługą AKS, tworzenie i korzystanie z aplikacji usługi Azure AD, który działa jako punkt końcowy dla żądania tożsamości. Pierwszą aplikację usługi Azure AD, których potrzebujesz pobiera członkostwa w grupie usługi Azure AD dla użytkownika.

Tworzenie serwera aplikacji składnika przy użyciu [az ad app Utwórz][az-ad-app-create] command, then update the group membership claims using the [az ad app update][az-ad-app-update] polecenia. W poniższym przykładzie użyto *aksname* zmiennej zdefiniowanej w [przed rozpoczęciem](#before-you-begin) sekcji, a następnie tworzy zmienną

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Teraz Utwórz jednostkę usługi dla aplikacji serwera za pomocą [tworzenie az ad sp][az-ad-sp-create] command. This service principal is used to authenticate itself within the Azure platform. Then, get the service principal secret using the [az ad sp credential reset][az-ad-sp-credential-reset] poleceń i przypisz ją do zmiennej o nazwie *serverApplicationSecret* do użytku w jednym z następujących czynności:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Usługa Azure AD wymaga uprawnień do wykonania następujących czynności:

* Czytaj dane katalogu
* Zaloguj się i odczytuj profil użytkownika

Tych uprawnień za pomocą [az ad app uprawnienia dodawania][az-ad-app-permission-add] polecenia:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Na koniec udzielić uprawnień przypisana w poprzednim kroku dla aplikacji serwera przy użyciu [az ad app uprawnienia grant][az-ad-app-permission-grant] command. This step fails if the current account is not a tenant admin. You also need to add permissions for Azure AD application to request information that may otherwise require administrative consent using the [az ad app permission admin-consent][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Utwórz składnik klienta usługi Azure AD

Druga aplikacja usługi Azure AD jest używany, gdy użytkownik zaloguje się do klastra usługi AKS przy użyciu interfejsu wiersza polecenia Kubernetes (`kubectl`). Ta aplikacja kliencka przyjmuje żądania uwierzytelniania od użytkownika i weryfikuje ich poświadczeń i uprawnień. Tworzenie aplikacji usługi Azure AD za pomocą składnika klienta [az ad app Utwórz][az-ad-app-create] polecenia:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Tworzenie jednostki usługi dla aplikacji klienckich przy użyciu [tworzenie az ad sp][az-ad-sp-create] polecenia:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Uzyskać identyfikator oAuth2 dla aplikacji serwera, aby umożliwić przepływ uwierzytelniania między składnikami dwóch aplikacji przy użyciu [az ad app show][az-ad-app-show] polecenia. Ten identyfikator oAuth2 jest używany w następnym kroku.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Dodaj uprawnienia do aplikacji klienckiej i składniki aplikacji serwera do komunikacji oAuth2 przy użyciu przepływu przy użyciu [az ad app uprawnienia dodawania][az-ad-app-permission-add] command. Then, grant permissions for the client application to communication with the server application using the [az ad app permission grant][az-ad-app-permission-grant] polecenia:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Wdrożenie klastra

Za pomocą utworzone dwie aplikacje usługi Azure AD teraz utworzyć samego klastra AKS. Najpierw utwórz grupę zasobów za pomocą [Tworzenie grupy az][az-group-create] polecenia. Poniższy przykład obejmuje tworzenie grupy zasobów w *EastUS* regionu:

Utwórz grupę zasobów dla klastra:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Pobieranie Identyfikatora dzierżawy w swojej subskrypcji platformy Azure za pomocą [Pokaż konta az][az-account-show] command. Then, create the AKS cluster using the [az aks create][az-aks-create] polecenia. Polecenie, aby utworzyć klaster AKS zapewnia serwera i klienta identyfikatorów aplikacji, klucz tajny z nazwy głównej usługi serwera aplikacji i identyfikator dzierżawy:

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

Na koniec Uzyskaj klastra poświadczeń administratora przy użyciu [az aks get-credentials][az-aks-get-credentials] polecenia. W jednym z następujących czynności, Uzyskaj zwykłych *użytkownika* poświadczeń, aby zobaczyć uwierzytelniania usługi Azure AD klastra przepływu w akcji.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Utwórz powiązanie RBAC

Konto usługi Azure Active Directory można było korzystać z klastrem usługi AKS, powiązanie roli lub powiązania roli klastra musi zostać utworzona. *Role* zdefiniować uprawnienia, aby przyznać użytkownikom, i *powiązania* zastosować je do odpowiednich użytkowników. Te przypisania można zastosować do danej przestrzeni nazw lub dla całego klastra. Aby uzyskać więcej informacji, zobacz [autoryzacji RBAC przy użyciu][rbac-authorization].

Pobierz nazwę główną użytkownika (UPN) dla użytkownika aktualnie zalogowany przy użyciu [az ad podpisany w użytkowników show][az-ad-signed-in-user-show] polecenia. To konto użytkownika jest włączona dla integracji z usługą Azure AD w następnym kroku.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> W przypadku użytkowników, udzielanie powiązania funkcji RBAC dla tej samej dzierżawy usługi Azure AD, przypisywać uprawnienia na podstawie *userPrincipalName*. Jeśli użytkownik znajduje się w innej usłudze Azure AD dzierżawy, kwerendy i użyć *objectId* właściwości zamiast tego.

Tworzenie manifestu YAML, o nazwie `basic-azure-ad-binding.yaml` i Wklej poniższą zawartość. W ostatnim wierszu, Zastąp *userPrincipalName_or_objectId* z danymi wyjściowymi identyfikator nazwy UPN lub obiekt w poprzednim poleceniu:

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

Tworzenie za pomocą ClusterRoleBinding [zastosować kubectl][kubectl-apply] polecenia i podaj nazwę pliku manifestu usługi YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Dostęp do klastra za pomocą usługi Azure AD

Teraz możemy przetestować integracja uwierzytelniania usługi Azure AD dla klastra usługi AKS. Ustaw `kubectl` kontekstu konfiguracji przy użyciu poświadczeń zwykłych użytkowników. Ten kontekst przekazuje wszystkie żądania uwierzytelniania za pośrednictwem usługi Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Teraz za pomocą [kubectl get pods-][kubectl-get] polecenie, aby wyświetlić zasobników we wszystkich przestrzeni nazw:

```console
kubectl get pods --all-namespaces
```

Pojawi się znakiem w wierszu polecenia do uwierzytelniania za pomocą poświadczeń usługi Azure AD za pomocą przeglądarki sieci web. Po użytkownik został pomyślnie uwierzytelniony, `kubectl` polecenie wyświetla zasobników w klastrze AKS, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl get pods --all-namespaces

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

Odebrano token uwierzytelniania `kubectl` są buforowane. Można tylko ponownie monitowany do logowania, gdy token wygasł lub plik konfiguracji Kubernetes jest utworzony ponownie.

Jeśli po pomyślnym zalogowaniu przy użyciu przeglądarki sieci web, tak jak w następujących przykładowych danych wyjściowych zostanie wyświetlony komunikat o błędzie autoryzacji, sprawdź następujące potencjalne problemy:

```console
error: You must be logged in to the server (Unauthorized)
```

* Zdefiniowana jest odpowiedni obiekt o identyfikatorze lub główną nazwę użytkownika, w zależności od Jeśli konto użytkownika jest w tej samej dzierżawie usługi Azure AD, czy nie.
* Użytkownik nie jest członkiem więcej niż 200 grup.
* Klucz tajny zdefiniowane w rejestracji aplikacji dla serwera jest zgodna z wartości ustawionej za pomocą `--aad-server-app-secret`

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać kompletny skrypt, który zawiera polecenia przedstawionych w tym artykule, zobacz [skrypt integracji usługi Azure AD w usłudze AKS przykłady repozytorium][complete-script].

Aby użyć usługi Azure AD użytkownicy i grupy do kontrolowania dostępu do zasobów klastra, zobacz [kontrolować dostęp do zasobów klastra przy użyciu tożsamości usługi Azure AD i kontroli dostępu opartej na rolach w usłudze AKS][azure-ad-rbac].

Aby uzyskać więcej informacji o tym, jak zabezpieczyć klastry Kubernetes, zobacz [opcje dostęp i tożsamość dla usługi AKS)][rbac-authorization].

Aby uzyskać najlepsze rozwiązania dotyczące kontrolowanie tożsamości i zasobów, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
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

---
title: Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service
description: Jak utworzyć klastry z włączoną obsługą usługi Azure Active Directory Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: c2ed053479b11bada4cfc0ec808ad148f024dee6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803252"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service

Usługa Azure Kubernetes Service (AKS) można skonfigurować do uwierzytelniania użytkowników usługi Azure Active Directory (AD). W tej konfiguracji możesz zalogować się do klastra usługi AKS przy użyciu tokenu uwierzytelniania usługi Azure Active Directory. Ponadto administratorzy klastra mogą skonfigurować Kubernetes kontroli dostępu opartej na rolach (RBAC) oparte na tożsamości lub katalog członkostwem w grupach użytkowników.

W tym artykule dowiesz się, jak wdrażanie wstępnie wymaganych składników dla usługi AKS i Azure AD, a następnie jak wdrożyć klaster usługi platformy Azure z obsługą usługi AD i utworzyć prosty rolę RBAC w klastrze AKS.

Obowiązują następujące ograniczenia:

- Usługa Azure AD można włączyć tylko podczas tworzenia klastra nowe, włączone RBAC. Nie można włączyć usługi Azure AD w istniejącym klastrze usługi AKS.
- *Gość* użytkowników w usłudze Azure AD, takie jak, jeśli używasz federacyjnego logowania z innego katalogu, nie są obsługiwane.

## <a name="authentication-details"></a>Szczegóły uwierzytelniania

Uwierzytelnianie usługi Azure AD jest udostępniane na klastry usługi AKS przy użyciu protokołu OpenID Connect. OpenID Connect jest warstwy tożsamości korzystających z protokołu OAuth 2.0. Aby uzyskać więcej informacji na temat protokołu OpenID Connect, zobacz [Open ID connect dokumentacji][open-id-connect].

Z wewnątrz klastra Kubernetes uwierzytelnianie przy użyciu elementu Webhook tokenów służy do Sprawdź tokeny uwierzytelniania. Uwierzytelnianie przy użyciu elementu Webhook tokenów jest konfigurowane i zarządzane w ramach klastra AKS. Aby uzyskać więcej informacji na temat uwierzytelniania tokenu elementu Webhook, zobacz [dokumentacji uwierzytelniania elementu webhook][kubernetes-webhook].

> [!NOTE]
> Podczas konfigurowania usługi Azure AD do uwierzytelniania usługi AKS, dwóch aplikacji usługi Azure AD są skonfigurowane. Musi być zakończona przez administratora dzierżawy usługi Azure.

## <a name="create-server-application"></a>Tworzenie aplikacji serwera

Pierwszą aplikację usługi Azure AD umożliwia uzyskiwanie członkostwa grupy użytkowników usługi Azure AD.

1. Wybierz pozycję **Azure Active Directory** > **Rejestracje aplikacji** > **Rejestrowanie nowej aplikacji**.

   Nadaj aplikacji nazwę, wybierz opcję **aplikacji sieci Web / interfejs API** dla typu aplikacji i wprowadzić dowolną wartość identyfikatora URI sformatowane dla **adres URL logowania**. Wybierz **Utwórz** po zakończeniu.

   ![Utwórz rejestrację w usłudze Azure AD](media/aad-integration/app-registration.png)

2. Wybierz **manifestu** i edytować `groupMembershipClaims` wartość `"All"`.

   Zapisz aktualizacje po wykonaniu tych czynności.

   ![Członkostwo w grupie aktualizacji do wszystkich](media/aad-integration/edit-manifest.png)

3. Po powrocie na aplikację usługi Azure AD wybierz **ustawienia** > **klucze**.

   Dodaj opis klucza, wybierz termin wygaśnięcia i wybierz **Zapisz**. Zanotuj wartość klucza. Podczas wdrażania usługi Azure AD włączone klastra AKS, ta wartość jest określany jako `Server application secret`.

   ![Pobieranie klucza prywatnego aplikacji](media/aad-integration/application-key.png)

4. Wróć do aplikacji usługi Azure AD, wybierz pozycję **ustawienia** > **wymagane uprawnienia** > **Dodaj**  >   **Wybierz interfejs API** > **programu Microsoft Graph** > **wybierz**.

   ![Wybierz interfejs API programu graph](media/aad-integration/graph-api.png)

5. W obszarze **uprawnienia aplikacji** należy zaznaczyć **Odczyt danych katalogu**.

   ![Ustaw uprawnienia aplikacji do wykresu](media/aad-integration/read-directory.png)

6. W obszarze **DELEGOWANE uprawnienia**, należy zaznaczyć **Zaloguj się i odczytuj profil użytkownika** i **Odczyt danych katalogu**. Zapisz aktualizacje po zakończeniu.

   ![Ustaw uprawnienia aplikacji do wykresu](media/aad-integration/delegated-permissions.png)

   Wybierz pozycję **Done** (Gotowe).

7. Wybierz *programu Microsoft Graph* wybierz z listy interfejsów API, następnie **Udziel uprawnień**. Ten krok zakończy się niepowodzeniem, jeśli bieżące konto nie jest administratorem dzierżawy.

   ![Ustaw uprawnienia aplikacji do wykresu](media/aad-integration/grant-permissions.png)

   Po pomyślnym przyznano uprawnienia, w portalu zostanie wyświetlone następujące powiadomienie:

   ![Powiadomienie o pomyślnym uprawnienia przyznane](media/aad-integration/permissions-granted.png)

8. Wróć do aplikacji i zwróć uwagę na **identyfikator aplikacji**. W przypadku wdrażania klastra usługi AKS z obsługą usługi AD systemu Azure, ta wartość jest określany jako `Server application ID`.

   ![Uzyskiwanie Identyfikatora aplikacji](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Tworzenie aplikacji klienckiej

Druga aplikacja usługi Azure AD jest używany podczas logowania się przy użyciu interfejsu wiersza polecenia Kubernetes (kubectl).

1. Wybierz pozycję **Azure Active Directory** > **Rejestracje aplikacji** > **Rejestrowanie nowej aplikacji**.

   Nadaj aplikacji nazwę, wybierz opcję **natywnych** dla typu aplikacji i wprowadzić dowolną wartość identyfikatora URI sformatowane dla **identyfikator URI przekierowania**. Wybierz **Utwórz** po zakończeniu.

   ![Tworzenie rejestracji usługi AAD](media/aad-integration/app-registration-client.png)

2. W aplikacji Azure AD wybierz **ustawienia** > **wymagane uprawnienia** > **Dodaj** > **wybierz Interfejs API** i wyszukaj nazwę aplikacji serwera, utworzony w poprzednim kroku w tym dokumencie.

   ![Skonfiguruj uprawnienia aplikacji](media/aad-integration/select-api.png)

3. Umieść znacznik wyboru obok aplikacji i kliknij przycisk **wybierz**.

   ![Wybierz punkt końcowy aplikacji serwera usługi AAD usługi AKS](media/aad-integration/select-server-app.png)

   Wybierz **gotowe**

4. Wybierz swój serwer interfejsu API z listy, a następnie wybierz **Udziel uprawnień**:

   ![Udzielenie uprawnień](media/aad-integration/grant-permissions-client.png)

5. Wstecz w aplikacji usługi AD, zwróć uwagę na **identyfikator aplikacji**. W przypadku wdrażania klastra usługi AKS z obsługą usługi AD systemu Azure, ta wartość jest określany jako `Client application ID`.

   ![Uzyskiwanie Identyfikatora aplikacji](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Pobieranie identyfikatora dzierżawy

Na koniec Uzyskaj identyfikator dzierżawy platformy Azure. Ta wartość jest również używana w przypadku wdrażania klastra AKS.

W witrynie Azure portal, wybierz **usługi Azure Active Directory** > **właściwości** i zwróć uwagę na **identyfikator katalogu**. W przypadku wdrażania klastra usługi AKS z obsługą usługi AD systemu Azure, ta wartość jest określany jako `Tenant ID`.

![Uzyskaj identyfikator dzierżawy platformy Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Wdrażanie klastra

Użyj [Tworzenie grupy az] [ az-group-create] polecenie, aby utworzyć grupę zasobów dla klastra AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Wdrażanie klastra przy użyciu [tworzenie az aks] [ az-aks-create] polecenia. Zastąp wartości w poniższym poleceniu przykładowe wartości zebrane podczas tworzenia aplikacji usługi Azure AD.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Utwórz powiązanie RBAC

Konto usługi Azure Active Directory można było korzystać z klastrem usługi AKS, powiązanie roli lub powiązania roli klastra musi zostać utworzona. *Role* zdefiniować uprawnienia, aby przyznać użytkownikom, i *powiązania* zastosować je do odpowiednich użytkowników. Te przypisania można zastosować do danej przestrzeni nazw lub dla całego klastra. Aby uzyskać więcej informacji, zobacz [autoryzacji RBAC przy użyciu][rbac-authorization].

Najpierw za pomocą [az aks get-credentials] [ az-aks-get-credentials] polecenia `--admin` argumentu, aby zalogować się do klastra z prawami dostępu administratora.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Następnie użyj następujących manifestu, aby utworzyć ClusterRoleBinding dla konta usługi Azure AD. W tym przykładzie daje pełny dostęp konta do wszystkie obszary nazw klastra. 

Pobierz *objectId* użytkownika wymagane konta przy użyciu [az ad użytkownika show] [ az-ad-user-show] polecenia. Podaj główną nazwę użytkownika (UPN) konta wymagane:

```azurecli-interactive
az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
```

Utwórz plik, takich jak *rbac-aad-user.yaml*i Wklej poniższą zawartość. Zaktualizuj nazwę użytkownika o identyfikatorze obiektu konta użytkownika z usługi Azure AD uzyskanego w poprzednim kroku:

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
  name: "947026ec-9463-4193-c08d-4c516e1f9f52"
```

Stosowanie użyciu powiązania [zastosować kubectl] [ kubectl-apply] polecenia, jak pokazano w poniższym przykładzie:

```console
kubectl apply -f rbac-aad-user.yaml
```

Powiązanie roli można również tworzyć dla wszystkich członków grupy usługi Azure AD. Grupy usługi Azure AD są określane za pomocą identyfikator obiektu grupy, jak pokazano w poniższym przykładzie. Utwórz plik, takich jak *rbac-aad-group.yaml*i Wklej poniższą zawartość. Zaktualizuj identyfikator obiektu grupy przy użyciu jednego z dzierżawą usługi Azure AD:

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Stosowanie użyciu powiązania [zastosować kubectl] [ kubectl-apply] polecenia, jak pokazano w poniższym przykładzie:

```console
kubectl apply -f rbac-aad-group.yaml
```

Aby uzyskać więcej informacji na temat zabezpieczania klastra Kubernetes za pomocą funkcji RBAC, zobacz [autoryzacji RBAC przy użyciu][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Dostęp do klastra za pomocą usługi Azure AD

Następnie ściągnąć kontekst dla użytkownika bez uprawnień administratora za pomocą [az aks get-credentials] [ az-aks-get-credentials] polecenia.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Po uruchomieniu dowolnego polecenia kubectl wyświetli monit o uwierzytelnianie za pomocą platformy Azure. Postępuj zgodnie z wyświetlanymi instrukcjami.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

Po wykonaniu tych czynności, jest buforowany token uwierzytelniania. Są tylko ponownie monitowany, aby zalogować się po token wygasł lub ponowne utworzenie pliku konfiguracji platformy Kubernetes.

Jeśli widzisz komunikat o błędzie autoryzacji po zalogowaniu się pomyślnie, sprawdź, czy:
1. Użytkownik logujesz się nie gościa w wystąpieniu usługi Azure AD (jest to często w przypadku korzystania z logowania federacyjnego z innego katalogu).
2. Użytkownik nie jest członkiem więcej niż 200 grup.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zabezpieczania klastrów Kubernetes z funkcją RBAC przy użyciu [autoryzacji RBAC przy użyciu] [ rbac-authorization] dokumentacji.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show

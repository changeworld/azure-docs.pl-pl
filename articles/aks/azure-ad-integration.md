---
title: Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service
description: Jak utworzyć klastry z włączoną obsługą usługi Azure Active Directory Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: 2a218a48223c81e009b83cb1f129601a8035e18e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138528"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service

Usługa Azure Kubernetes Service (AKS) można skonfigurować do uwierzytelniania użytkowników usługi Azure Active Directory (AD). W tej konfiguracji należy zalogować się do klastra usługi AKS przy użyciu tokenu uwierzytelniania usługi Azure Active Directory. Ponadto administratorzy klastra mogą Konfigurowanie kontroli dostępu opartej na rolach Kubernetes (RBAC) na podstawie członkostwa grupy użytkownika tożsamości lub katalogu.

W tym artykule dowiesz się, jak wdrażanie wstępnie wymaganych składników dla usługi AKS i Azure AD, a następnie wdrożyć klaster usługi platformy Azure z obsługą usługi AD i tworzeniu Podstawowa rola RBAC w klastrze usługi AKS przy użyciu witryny Azure portal. Możesz również [wykonaj te kroki przy użyciu wiersza polecenia platformy Azure][azure-ad-cli].

Obowiązują następujące ograniczenia:

- Usługa Azure AD można włączyć tylko podczas tworzenia klastra nowe, włączone RBAC. Nie można włączyć usługi Azure AD w istniejącym klastrze usługi AKS.
- *Gość* użytkowników w usłudze Azure AD, takie jak, jeśli używasz federacyjnego logowania z innego katalogu, nie są obsługiwane.

## <a name="authentication-details"></a>Szczegóły uwierzytelniania

Uwierzytelnianie usługi Azure AD jest udostępniane na klastry usługi AKS przy użyciu protokołu OpenID Connect. OpenID Connect jest warstwy tożsamości korzystających z protokołu OAuth 2.0. Aby uzyskać więcej informacji na temat protokołu OpenID Connect, zobacz [Open ID connect dokumentacji][open-id-connect].

Z wewnątrz klastra Kubernetes uwierzytelnianie przy użyciu elementu Webhook tokenów służy do Sprawdź tokeny uwierzytelniania. Uwierzytelnianie przy użyciu elementu Webhook tokenów jest konfigurowane i zarządzane w ramach klastra AKS. Aby uzyskać więcej informacji na temat uwierzytelniania tokenu elementu Webhook, zobacz [dokumentacji uwierzytelniania elementu webhook][kubernetes-webhook].

Aby zapewnić uwierzytelnianie usługi Azure AD dla klastra usługi AKS, są tworzone dwie aplikacje usługi Azure AD. Pierwsza aplikacja jest składnik serwera, który przeprowadza uwierzytelnianie użytkownika. Druga aplikacja jest składnik klienta, który jest używany po wyświetleniu monitu przez interfejs wiersza polecenia do uwierzytelniania. Ta aplikacja kliencka używa aplikacji serwera dla rzeczywistego uwierzytelniania poświadczeń dostarczonych przez klienta.

> [!NOTE]
> Podczas konfigurowania usługi Azure AD do uwierzytelniania usługi AKS, dwóch aplikacji usługi Azure AD są skonfigurowane. Należy wykonać kroki, aby delegować uprawnienia dla każdej z aplikacji przez administratora dzierżawy usługi Azure.

## <a name="create-server-application"></a>Tworzenie aplikacji serwera

Pierwszą aplikację usługi Azure AD umożliwia uzyskiwanie członkostwa grupy użytkowników usługi Azure AD. Utwórz tę aplikację w witrynie Azure portal.

1. Wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **nowej rejestracji**.

    * Nadaj aplikacji nazwę, taką jak *AKSAzureADServer*.
    * Aby uzyskać **obsługiwane typy kont**, wybierz *kont w tym katalogu organizacji tylko*.
    * Wybierz *Web* dla **identyfikator URI przekierowania** wpisz i wprowadź dowolną wartość w formacie identyfikatora URI, taki jak *https://aksazureadserver*.
    * Wybierz **zarejestrować** po zakończeniu.

1. Wybierz **manifestu** i edytować `groupMembershipClaims` wartość `"All"`.

    ![Członkostwo w grupie aktualizacji do wszystkich](media/aad-integration/edit-manifest.png)

    **Zapisz** aktualizacji po wykonaniu tych czynności.

1. W lewym obszarze nawigacji aplikacji usługi Azure AD, wybierz **certyfikaty i klucze tajne**.

    * Wybierz **+ nowy wpis tajny klienta**.
    * Dodaj opis klucza, takie jak *serwera usługi Azure AD w usłudze AKS*. Wybierz czas wygaśnięcia, a następnie wybierz **Dodaj**.
    * Zanotuj wartość klucza. Jest wyświetlane tylko ten czas wstępnej. Podczas wdrażania klastra usługi AKS z obsługą usługi AD systemu Azure, ta wartość jest określany jako `Server application secret`.

1. W lewym obszarze nawigacji aplikacji usługi Azure AD, wybierz **uprawnienia do interfejsu API**, następnie **+ Dodaj uprawnienia**.

    * W obszarze **Microsoft APIs**, wybierz *programu Microsoft Graph*.
    * Wybierz **delegowane uprawnienia**, należy zaznaczyć w polu **katalogu > Directory.Read.All (Czytaj dane katalogu)**.
        * Jeśli domyślny delegować uprawnienia dla **użytkownika > User.Read (Zaloguj się i odczytuj profil użytkownika)** nie istnieje, należy zaznaczyć to uprawnienie.
    * Wybierz **uprawnienia aplikacji**, należy zaznaczyć w polu **katalogu > Directory.Read.All (Czytaj dane katalogu)**.

        ![Ustaw uprawnienia do wykresu](media/aad-integration/graph-permissions.png)

    * Wybierz **Dodaj uprawnienia** Aby zapisać aktualizacje.

    * W obszarze **wyrazić zgody** sekcji, możliwość **udzielić zgody administratora**. Ten przycisk jest nieaktywny i jest niedostępna, jeśli bieżące konto nie jest administratorem dzierżawy.

        Po pomyślnym przyznano uprawnienia, w portalu zostanie wyświetlone następujące powiadomienie:

        ![Powiadomienie o pomyślnym uprawnienia przyznane](media/aad-integration/permissions-granted.png)

1. W lewym obszarze nawigacji aplikacji usługi Azure AD, wybierz **uwidaczniania interfejsu API**, następnie **+ Dodaj zakres**.
    
    * Ustaw *nazwa zakresu*, *nazwę wyświetlaną zgody administratora*, i *opis zgody administratora*, takich jak *AKSAzureADServer*.
    * Upewnij się, że **stanu** ustawiono *włączone*.

        ![Udostępnianie aplikacji serwera jako interfejsu API do użycia z innymi usługami](media/aad-integration/expose-api.png)

    * Wybierz **Dodaj zakres**.

1. Wróć do aplikacji **Przegląd** strony i zwróć uwagę na **identyfikator aplikacji (klienta)**. Podczas wdrażania klastra usługi AKS z obsługą usługi AD systemu Azure, ta wartość jest określany jako `Server application ID`.

   ![Uzyskiwanie Identyfikatora aplikacji](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Tworzenie aplikacji klienckiej

Druga aplikacja usługi Azure AD jest używany podczas logowania się przy użyciu interfejsu wiersza polecenia Kubernetes (`kubectl`).

1. Wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **nowej rejestracji**.

    * Nadaj aplikacji nazwę, taką jak *AKSAzureADClient*.
    * Aby uzyskać **obsługiwane typy kont**, wybierz *kont w tym katalogu organizacji tylko*.
    * Wybierz *Web* dla **identyfikator URI przekierowania** wpisz i wprowadź dowolną wartość w formacie identyfikatora URI, taki jak *https://aksazureadclient*.
    * Wybierz **zarejestrować** po zakończeniu.

1. W lewym obszarze nawigacji aplikacji usługi Azure AD, wybierz **uprawnienia do interfejsu API**, następnie **+ Dodaj uprawnienia**.

    * Wybierz **Moje interfejsy API**, następnie wybierz utworzony w poprzednim kroku, takie jak aplikacja serwera usługi Azure AD *AKSAzureADServer*.
    * Wybierz **delegowane uprawnienia**, następnie zaznaczyć pole obok aplikacji serwera usługi Azure AD.

        ![Skonfiguruj uprawnienia aplikacji](media/aad-integration/select-api.png)

    * Wybierz **Dodaj uprawnienia**.

    * W obszarze **wyrazić zgody** sekcji, możliwość **udzielić zgody administratora**. Ten przycisk jest nieaktywny i jest niedostępna, jeśli bieżące konto nie jest administratorem dzierżawy.

        Po pomyślnym przyznano uprawnienia, w portalu zostanie wyświetlone następujące powiadomienie:

        ![Powiadomienie o pomyślnym uprawnienia przyznane](media/aad-integration/permissions-granted.png)

1. W okienku nawigacji po lewej stronie aplikacji usługi Azure AD, zwróć uwagę na **identyfikator aplikacji**. W przypadku wdrażania klastra usługi AKS z obsługą usługi AD systemu Azure, ta wartość jest określany jako `Client application ID`.

   ![Uzyskiwanie Identyfikatora aplikacji](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Pobieranie identyfikatora dzierżawy

Na koniec Uzyskaj identyfikator dzierżawy platformy Azure. Ta wartość jest używana podczas tworzenia klastra usługi AKS.

W witrynie Azure portal, wybierz **usługi Azure Active Directory** > **właściwości** i zwróć uwagę na **identyfikator katalogu**. Podczas tworzenia klastra usługi AKS z obsługą usługi AD systemu Azure, ta wartość jest określany jako `Tenant ID`.

![Uzyskaj identyfikator dzierżawy platformy Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Wdrażanie klastra

Użyj [Tworzenie grupy az] [ az-group-create] polecenie, aby utworzyć grupę zasobów dla klastra AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Wdrażanie klastra przy użyciu [tworzenie az aks] [ az-aks-create] polecenia. Zastąp wartości w poniższym poleceniu przykładowe wartości zebrane podczas tworzenia aplikacji usługi Azure AD, aby uzyskać identyfikator aplikacji serwera i klucz tajny, Identyfikatora aplikacji klienckiej i identyfikator dzierżawy:

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

Trwa kilka minut, aby utworzyć klaster usługi AKS.

## <a name="create-rbac-binding"></a>Utwórz powiązanie RBAC

Konto usługi Azure Active Directory można było korzystać z klastrem usługi AKS, powiązanie roli lub powiązania roli klastra musi zostać utworzona. *Role* zdefiniować uprawnienia, aby przyznać użytkownikom, i *powiązania* zastosować je do odpowiednich użytkowników. Te przypisania można zastosować do danej przestrzeni nazw lub dla całego klastra. Aby uzyskać więcej informacji, zobacz [autoryzacji RBAC przy użyciu][rbac-authorization].

Najpierw za pomocą [az aks get-credentials] [ az-aks-get-credentials] polecenia `--admin` argument do logowania do klastra z prawami dostępu administratora.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Następnie należy utworzyć ClusterRoleBinding dla konta usługi Azure AD, które chcesz udzielić dostępu do klastra usługi AKS. Poniższy przykład daje pełny dostęp konta do wszystkich przestrzeni nazw w klastrze.

- Jeśli użytkownika można przyznać, że powiązanie funkcji RBAC dla znajduje się w tej samej dzierżawie usługi Azure AD, należy przypisać uprawnienia, w oparciu o nazwę główną użytkownika (UPN). Przejdź do kroku do tworzenia manifestu YAML ClusterRuleBinding.

- Jeśli użytkownik znajduje się w innej usłudze Azure AD dzierżawy, kwerendy i użyć *objectId* właściwości zamiast tego. Jeśli to konieczne, uzyskać *objectId* użytkownika wymagane konta przy użyciu [az ad użytkownika show] [ az-ad-user-show] polecenia. Podaj główną nazwę użytkownika (UPN) konta wymagane:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Utwórz plik, takich jak *rbac-aad-user.yaml*i Wklej poniższą zawartość. W ostatnim wierszu, Zastąp *userPrincipalName_or_objectId* przy użyciu nazwy UPN lub obiektu o identyfikatorze zależności od tego, jeśli użytkownik jest tą samą dzierżawą usługi Azure AD, czy nie.

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

Po uruchomieniu `kubectl` polecenia zostanie wyświetlony monit o uwierzytelnianie za pomocą platformy Azure. Postępuj zgodnie z wyświetlanymi instrukcjami, aby ukończyć proces, jak pokazano w poniższym przykładzie:

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Po zakończeniu jest buforowany token uwierzytelniania. Są tylko ponownie monitowany, aby zalogować się po token wygasł lub ponowne utworzenie pliku konfiguracji platformy Kubernetes.

Jeśli widzisz komunikat o błędzie autoryzacji po zalogowaniu się pomyślnie, sprawdź, czy:
1. Użytkownik logujesz się nie gościa w wystąpieniu usługi Azure AD (w tym scenariuszu jest często tak w przypadku używania kont federacyjnych z innego katalogu).
2. Użytkownik nie jest członkiem więcej niż 200 grup.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Kolejne kroki

Aby użyć usługi Azure AD użytkownicy i grupy do kontrolowania dostępu do zasobów klastra, zobacz [kontrolować dostęp do zasobów klastra przy użyciu tożsamości usługi Azure AD i kontroli dostępu opartej na rolach w usłudze AKS][azure-ad-rbac].

Aby uzyskać więcej informacji o tym, jak zabezpieczyć klastry Kubernetes, zobacz [opcje dostęp i tożsamość dla usługi AKS)][rbac-authorization].

Aby uzyskać najlepsze rozwiązania dotyczące kontrolowanie tożsamości i zasobów, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

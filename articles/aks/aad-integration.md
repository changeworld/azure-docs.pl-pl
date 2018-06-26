---
title: Integrowanie usługi Azure Active Directory z usługą Kubernetes Azure
description: Jak utworzyć klastry z włączoną usługą Azure Active Directory z usługą Azure Kubernetes.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7d157d50bbcd25edd9cd6693a71fb04535cbeb79
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937385"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Integrowanie usługi Azure Active Directory z AKS - Preview

Usługa Kubernetes Azure (AKS) można skonfigurować do używania usługi Azure Active Directory do uwierzytelniania użytkowników. W tej konfiguracji możesz zalogować się do Kubernetes klastra usługi platformy Azure przy użyciu token uwierzytelniania usługi Azure Active Directory. Ponadto administratorzy klastra mogą skonfigurować kontroli dostępu opartej na rolach Kubernetes na podstawie członkostwa grupy użytkowników, jak tożsamość lub katalogu.

Ten dokument zawiera szczegóły tworzenie wszystkie niezbędne wymagania wstępne dotyczące AKS i Azure AD, wdrażanie klastra usług Azure z obsługą usługi AD i tworzenie prostego roli RBAC w klastrze AKS.

> [!IMPORTANT]
> Integracja Azure RBAC Kubernetes usługi (AKS) i Azure AD jest obecnie w **Podgląd**. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

## <a name="authentication-details"></a>Szczegóły okna uwierzytelnianie

Usługa Azure AD authentication zapewnia Azure Kubernetes klastrów z OpenID Connect. OpenID Connect to warstwa tożsamości, rozszerzający protokołu OAuth 2.0. Więcej informacji na temat protokołu OpenID Connect można znaleźć w [Open ID connect dokumentacji][open-id-connect].

Z wewnątrz klastra Kubernetes uwierzytelnianie Token elementu Webhook jest używane do Sprawdź tokeny uwierzytelniania. Uwierzytelnianie token elementu Webhook jest skonfigurowany i zarządzane w ramach klastra AKS. Więcej informacji na temat elementu Webhook token uwierzytelniania można znaleźć w [dokumentacji uwierzytelniania elementu webhook][kubernetes-webhook].

> [!NOTE]
> Podczas konfigurowania usługi Azure AD na potrzeby uwierzytelniania AKS, są skonfigurowane dwie aplikacji usługi Azure AD. Administrator dzierżawy Azure musi wykonać tę operację.

## <a name="create-server-application"></a>Tworzenie aplikacji serwera

Pierwszą aplikację usługi Azure AD jest używany do pobierania członkostwa w grupie użytkowników usługi Azure AD.

1. Wybierz pozycję **Azure Active Directory** > **Rejestracje aplikacji** > **Rejestrowanie nowej aplikacji**.

  Nadaj nazwę, wybierz aplikację **aplikacji sieci Web / interfejs API** dla typu aplikacji i wprowadź wszystkie wartości identyfikatora URI sformatowany **adres URL logowania**. Wybierz **Utwórz** po zakończeniu.

  ![Tworzenie rejestracji usługi Azure AD](media/aad-integration/app-registration.png)

2. Wybierz **manifestu** i edytować `groupMembershipClaims` do wartości `"All"`.

  Zapisz aktualizacji po wykonaniu tych czynności.

  ![Członkostwo w grupie aktualizacji do wszystkich](media/aad-integration/edit-manifest.png)

3. Do aplikacji usługi Azure AD, wybierz **ustawienia** > **klucze**.

  Dodaj opis klucza, wybierz termin wygaśnięcia i wybierz **zapisać**. Zanotuj wartość klucza. Podczas wdrażania usługi Azure AD włączono klaster AKS, ta wartość jest określana jako `Server application secret`.

  ![Pobierz klucz prywatny aplikacji](media/aad-integration/application-key.png)

4. Powrót do aplikacji usługi Azure AD, wybierz pozycję **ustawienia** > **wymagane uprawnienia** > **Dodaj**  >   **Wybierz interfejs API** > **Microsoft Graph** > **wybierz**.

  W obszarze **uprawnienia aplikacji** dalej, aby zaznaczyć **odczytuj dane katalogu**.

  ![Ustaw uprawnienia wykres aplikacji](media/aad-integration/read-directory.png)

5. W obszarze **DELEGOWANE uprawnienia**, zaznacz obok **Zaloguj się i odczytuj profil użytkownika** i **odczytuj dane katalogu**. Zapisz aktualizacje wykonywane raz.

  ![Ustaw uprawnienia wykres aplikacji](media/aad-integration/delegated-permissions.png)

6. Wybierz **gotowe** i **udzielanie uprawnień** do ukończenia tego kroku. Ten krok zakończy się niepowodzeniem, jeśli nie jest z bieżącego konta administratora dzierżawy.

  ![Ustaw uprawnienia wykres aplikacji](media/aad-integration/grant-permissions.png)

7. Wróć do aplikacji, a następnie zanotuj **identyfikator aplikacji**. Podczas wdrażania klastra usługi Azure AD, włączone AKS, ta wartość jest określana jako `Server application ID`.

  ![Uzyskiwanie Identyfikatora aplikacji](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Tworzenie aplikacji klienckich

Drugi aplikacji usługi Azure AD będzie używane podczas logowania się przy użyciu interfejsu wiersza polecenia Kubernetes (kubectl).

1. Wybierz pozycję **Azure Active Directory** > **Rejestracje aplikacji** > **Rejestrowanie nowej aplikacji**.

  Nadaj nazwę, wybierz aplikację **natywnego** dla typu aplikacji i wprowadź wszystkie wartości identyfikatora URI sformatowany **identyfikator URI przekierowania**. Wybierz **Utwórz** po zakończeniu.

  ![Tworzenie rejestracji usługi AAD](media/aad-integration/app-registration-client.png)

2. Wybierz z aplikacji usługi Azure AD **ustawienia** > **wymagane uprawnienia** > **Dodaj** > **wybierz Interfejs API** i wyszukaj nazwę aplikacji serwera, utworzony w ostatnim kroku tego dokumentu.

  ![Konfigurowanie uprawnień aplikacji](media/aad-integration/select-api.png)

3. Zaznacz pole wyboru obok aplikacji i kliknij przycisk **wybierz**.

  ![Wybierz punkt końcowy usługi AAD AKS serwera aplikacji](media/aad-integration/select-server-app.png)

4. Wybierz **gotowe** i **udzielanie uprawnień** do ukończenia tego kroku.

  ![Udzielenie uprawnień](media/aad-integration/grant-permissions-client.png)

5. Wróć na aplikacji usługi AD, zwróć uwagę na **identyfikator aplikacji**. Podczas wdrażania klastra usługi Azure AD, włączone AKS, ta wartość jest określana jako `Client application ID`.

  ![Uzyskiwanie Identyfikatora aplikacji](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Pobieranie identyfikatora dzierżawy

Na koniec można pobrać Identyfikatora dzierżawy usługi Azure. Ta wartość jest również używana w przypadku wdrażania klastra AKS.

W portalu Azure wybierz **usługi Azure Active Directory** > **właściwości** i zwróć uwagę na **identyfikator katalogu**. Podczas wdrażania klastra usługi Azure AD, włączone AKS, ta wartość jest określana jako `Tenant ID`.

![Uzyskanie Identyfikatora dzierżawy usługi Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Wdrażanie klastra

Użyj [Tworzenie grupy az] [ az-group-create] polecenie, aby utworzyć grupę zasobów klastra AKS.

```azurecli
az group create --name myAKSCluster --location eastus
```

Wdrażanie klastra przy użyciu [utworzyć az aks] [ az-aks-create] polecenia. Zastąp wartości w poleceniu próbki poniżej wartości zbierane podczas tworzenia aplikacji usługi Azure AD.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Utwórz powiązanie RBAC

Konto usługi Azure Active Directory można było korzystać z klastrem AKS, powiązania roli lub powiązanie roli klastra musi zostać utworzona.

Najpierw użyj [aks az get poświadczenia] [ az-aks-get-credentials] z `--admin` argument do logowania się w klastrze o dostępu z uprawnieniami administratora.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Następnie użyj następujących manifestu, aby utworzyć ClusterRoleBinding dla konta usługi Azure AD. Aktualizacja nazwy użytkownika z jednym z dzierżawy usługi Azure AD. W tym przykładzie daje pełny dostęp do wszystkich obszarów nazw klastra.

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
  name: "user@contoso.com"
```

Można również tworzyć powiązania roli dla wszystkich członków grupy w usłudze Azure AD. Następujące manifestu zawiera wszystkie elementy członkowskie `kubernetes-admin` grupy dostępu administratora do klastra.

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
   name: "kubernetes-admin"
```

Aby uzyskać więcej informacji na temat zabezpieczenia klastra Kubernetes o RBAC zobacz [przy użyciu autoryzacji RBAC][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Dostęp do klastra z usługą Azure AD

Następnie ściągnięcia w kontekście użytkownika niebędącego administratorem za pomocą [aks az get poświadczenia] [ az-aks-get-credentials] polecenia.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

Po uruchomieniu polecenia kubectl, pojawi się monit do uwierzytelniania w usłudze Azure. Postępuj zgodnie z wyświetlanymi instrukcjami.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

Po wykonaniu tych czynności token uwierzytelniania są buforowane. Są tylko ponownie monitowany, aby zalogować się po wygaśnięciu tokena lub pliku konfiguracyjnym Kubernetes utworzony ponownie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zabezpieczania Kubernetes klastrów z RBAC z [przy użyciu autoryzacji RBAC] [ rbac-authorization] dokumentacji.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md

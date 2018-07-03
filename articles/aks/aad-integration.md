---
title: Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service
description: Jak utworzyć klastry z obsługą usługi Azure Active Directory Azure Kubernetes Service.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7ae3818795cddf5dfbb93ca6cc8dfff9d1c44c03
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341246"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Integrowanie usługi Azure Active Directory za pomocą usługi AKS — wersja zapoznawcza

Usługa Azure Kubernetes Service (AKS) można skonfigurować do użycia usługi Azure Active Directory do uwierzytelniania użytkowników. W tej konfiguracji możesz zalogować się do klastra usługi Azure Kubernetes Service przy użyciu tokenu uwierzytelniania usługi Azure Active Directory. Ponadto administratorzy klastra mogą Konfigurowanie kontroli dostępu opartej na rolach platformy Kubernetes, oparte na tożsamości lub katalog członkostwem w grupach użytkowników.

W tym dokumencie przedstawiono tworzenie wszystkie niezbędne wymagania wstępne dotyczące usługi AKS i Azure AD, wdrażanie klastra z obsługą usługi AD systemu Azure i tworzenie prostego rolę RBAC w klastrze AKS.

> [!IMPORTANT]
> Integracja z platformą Azure Kubernetes Service (AKS) RBAC a usługą Azure AD jest obecnie w **Podgląd**. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

## <a name="authentication-details"></a>Szczegóły uwierzytelniania

Uwierzytelnianie usługi Azure AD znajduje się w klastrach usługi Azure Kubernetes za pomocą protokołu OpenID Connect. OpenID Connect jest warstwy tożsamości korzystających z protokołu OAuth 2.0. Więcej informacji na temat protokołu OpenID Connect można znaleźć w [Open ID connect dokumentacji][open-id-connect].

Z wewnątrz klastra Kubernetes uwierzytelnianie przy użyciu elementu Webhook tokenów służy do Sprawdź tokeny uwierzytelniania. Uwierzytelnianie przy użyciu elementu Webhook tokenów jest konfigurowane i zarządzane w ramach klastra AKS. Więcej informacji na temat elementu Webhook, uwierzytelnianie przy użyciu tokenów można znaleźć w [dokumentacji uwierzytelniania elementu webhook][kubernetes-webhook].

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

7. Wybierz **gotowe**, wybierz *programu Microsoft Graph* wybierz z listy interfejsów API, następnie **Udziel uprawnień**. Ten krok zakończy się niepowodzeniem, jeśli bieżące konto nie jest administratorem dzierżawy.

  ![Ustaw uprawnienia aplikacji do wykresu](media/aad-integration/grant-permissions.png)

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

4. Wybierz **gotowe** i **Udziel uprawnień** do ukończenia tego kroku.

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
az group create --name myAKSCluster --location eastus
```

Wdrażanie klastra przy użyciu [tworzenie az aks] [ az-aks-create] polecenia. Zastąp wartości w poniższym poleceniu przykładowe wartości zebrane podczas tworzenia aplikacji usługi Azure AD.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Utwórz powiązanie RBAC

Konto usługi Azure Active Directory można było korzystać z klastrem usługi AKS, powiązanie roli lub powiązania roli klastra musi zostać utworzona.

Najpierw za pomocą [az aks get-credentials] [ az-aks-get-credentials] polecenia `--admin` argumentu, aby zalogować się do klastra z prawami dostępu administratora.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Następnie użyj następujących manifestu, aby utworzyć ClusterRoleBinding dla konta usługi Azure AD. Zaktualizuj nazwę użytkownika przy użyciu jednego z dzierżawą usługi Azure AD. W tym przykładzie daje pełny dostęp konta do wszystkie obszary nazw klastra.

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

Powiązanie roli można również tworzyć dla wszystkich członków grupy usługi Azure AD. Grupy usługi Azure AD są określane za pomocą identyfikatora grupy obiektów.

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

Aby uzyskać więcej informacji na temat zabezpieczania klastra Kubernetes za pomocą funkcji RBAC, zobacz [autoryzacji RBAC przy użyciu][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Dostęp do klastra za pomocą usługi Azure AD

Następnie ściągnąć kontekst dla użytkownika bez uprawnień administratora za pomocą [az aks get-credentials] [ az-aks-get-credentials] polecenia.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

Po uruchomieniu dowolnego polecenia kubectl wyświetli monit o uwierzytelnianie za pomocą platformy Azure. Postępuj zgodnie z wyświetlanymi instrukcjami.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

Po wykonaniu tych czynności, jest buforowany token uwierzytelniania. Są tylko ponownie monitowany, aby zalogować się po token wygasł lub ponowne utworzenie pliku konfiguracji platformy Kubernetes.

Jeśli widzisz komunikat o błędzie autoryzacji, po pomyślnym zalogowaniu, sprawdź, czy użytkownik logujesz się nie Gość w usłudze Azure AD (jest to często w przypadku korzystania z logowania federacyjnego z innego katalogu).
```console
error: You must be logged in to the server (Unauthorized)
```


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zabezpieczania klastrów Kubernetes z funkcją RBAC przy użyciu [autoryzacji RBAC przy użyciu] [ rbac-authorization] dokumentacji.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md

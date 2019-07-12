---
title: Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service
description: Sposób tworzenia klastrów z obsługą usługi Azure Active Directory Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 80137023643630e8472a70fcca6cb656aeba7123
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616388"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service

Usługa Azure Kubernetes Service (AKS) można skonfigurować do użycia usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników. W tej konfiguracji można Zaloguj się do klastra usługi AKS przy użyciu token uwierzytelniania usługi Azure AD.

Administratorzy klastra mogą skonfigurować kontroli dostępu opartej na rolach Kubernetes (RBAC) na podstawie członkostwa grupy użytkownika tożsamości lub katalogu.

W tym artykule opisano sposób:

- Wdrażanie wstępnie wymaganych składników dla usługi AKS i Azure AD.
- Wdrażanie klastra platformy Azure z obsługą usługi AD.
- Utwórz podstawowe rolę RBAC w klastrze usługi AKS przy użyciu witryny Azure portal.

Mogą również wykonać te kroki przy użyciu [wiersza polecenia platformy Azure][azure-ad-cli].

> [!NOTE]
> Usługa Azure AD można włączyć tylko podczas tworzenia nowego klastra z włączoną funkcją RBAC. Nie można włączyć usługi Azure AD w istniejącym klastrze usługi AKS.

## <a name="authentication-details"></a>Szczegóły uwierzytelniania

Uwierzytelnianie usługi Azure AD znajduje się w klastrach usługi AKS, które mają OpenID Connect. OpenID Connect jest warstwy tożsamości korzystających z protokołu OAuth 2.0.

Aby uzyskać więcej informacji na temat protokołu OpenID Connect, zobacz [Autoryzowanie dostępu do aplikacji sieci web przy użyciu protokołu OpenID Connect a usługą Azure AD][open-id-connect].

Wewnątrz klastra Kubernetes element webhook, uwierzytelnianie tokenu jest używany do tokenów uwierzytelniania. Uwierzytelnianie przy użyciu elementu Webhook tokenów jest konfigurowane i zarządzane w ramach klastra AKS.

Aby uzyskać więcej informacji o uwierzytelnianiu token elementu webhook, zobacz [uwierzytelnianie przy użyciu elementu Webhook tokenów][kubernetes-webhook] sekcji dokumentacją rozwiązania Kubernetes.

Aby zapewnić uwierzytelnianie usługi Azure AD dla klastra usługi AKS, są tworzone dwie aplikacje usługi Azure AD. Pierwsza aplikacja jest składnik serwera, który zapewnia uwierzytelnianie użytkownika. Druga aplikacja jest składnik klienta, który jest używany po wyświetleniu monitu przez interfejs wiersza polecenia do uwierzytelniania. Ta aplikacja kliencka używa aplikacji serwera dla rzeczywistego uwierzytelniania poświadczeń dostarczonych przez klienta.

> [!NOTE]
> Po skonfigurowaniu usługi Azure AD do uwierzytelniania usługi AKS, są skonfigurowane dwie aplikacje usługi Azure AD. Kroki, aby delegować uprawnienia dla każdej aplikacji muszą być wykonywane przez administratora dzierżawy usługi Azure.

## <a name="create-the-server-application"></a>Tworzenie aplikacji serwera

Pierwszą aplikację usługi Azure AD jest stosowany do uzyskać członkostwo w grupie usługi Azure AD przez użytkownika. Aby utworzyć aplikację w witrynie Azure portal:

1. Wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **nowej rejestracji**.

    a. Nadaj aplikacji nazwę, taką jak *AKSAzureADServer*.

    b. Aby uzyskać **obsługiwane typy kont**, wybierz opcję **kont w tym katalogu organizacji tylko**.
    
    c. Wybierz **Web** dla identyfikatora URI przekierowania typu, a następnie wprowadź dowolną wartość sformatowany identyfikator URI, takich jak *https://aksazureadserver* .

    d. Wybierz **zarejestrować** po zakończeniu.

2. Wybierz **manifestu**, a następnie Edytuj **groupMembershipClaims:** wartość jako **wszystkich**. Po zakończeniu aktualizacji, wybierz **Zapisz**.

    ![Członkostwo w grupie aktualizacji do wszystkich](media/aad-integration/edit-manifest.png)

3. W okienku po lewej stronie aplikacji usługi Azure AD wybierz **certyfikaty i klucze tajne**.

    a. Wybierz **+ nowy wpis tajny klienta**.

    b. Dodaj opis klucza, takie jak *serwera usługi Azure AD w usłudze AKS*. Wybierz czas wygaśnięcia, a następnie wybierz pozycję **Dodaj**.

    c. Należy zauważyć wartość klucza, który jest wyświetlany tylko w tej chwili. Podczas wdrażania klastra usługi AKS z obsługą usługi AD systemu Azure, wartość ta nosi nazwę wpisu tajnego aplikacji serwera.

4. W okienku po lewej stronie aplikacji usługi Azure AD wybierz **uprawnienia do interfejsu API**, a następnie wybierz pozycję **+ Dodaj uprawnienia**.

    a. W obszarze **Microsoft APIs**, wybierz opcję **programu Microsoft Graph**.

    b. Wybierz **delegowane uprawnienia**, a następnie zaznacz pole wyboru obok pozycji **katalogu > Directory.Read.All (Czytaj dane katalogu)** .

    c. Jeśli domyślny delegować uprawnienia dla **użytkownika > User.Read (Zaloguj się i odczytuj profil użytkownika)** nie istnieje, zaznacz pole wyboru obok niego.

    d. Wybierz **uprawnienia aplikacji**, a następnie zaznacz pole wyboru obok pozycji **katalogu > Directory.Read.All (Czytaj dane katalogu)** .

    ![Ustaw uprawnienia do wykresu](media/aad-integration/graph-permissions.png)

    e. Wybierz **Dodaj uprawnienia** Aby zapisać aktualizacje.

    f. W obszarze **wyrazić zgody**, wybierz opcję **udzielić zgody administratora**. Ten przycisk jest niedostępny, jeśli bieżące konto nie jest administratorem dzierżawy.

    Po pomyślnym udzieleniu uprawnień, w portalu zostanie wyświetlone następujące powiadomienie:

   ![Powiadomienie o pomyślnym uprawnienia przyznane](media/aad-integration/permissions-granted.png)

5. W okienku po lewej stronie aplikacji usługi Azure AD wybierz **uwidaczniania interfejsu API**, a następnie wybierz pozycję **+ Dodaj zakres**.
    
    a. Wprowadź **nazwa zakresu**, **nazwę wyświetlaną zgody administratora**, a następnie **opis zgody administratora** takich jak *AKSAzureADServer*.

    b. Upewnij się, że **stanu** ustawiono **włączone**.

    ![Udostępnianie aplikacji serwera jako interfejsu API do użycia z innymi usługami](media/aad-integration/expose-api.png)

    c. Wybierz **Dodaj zakres**.

6. Wróć do aplikacji **Przegląd** strony i zanotuj **identyfikator aplikacji (klienta)** . Podczas wdrażania klastra usługi AKS z obsługą usługi AD systemu Azure, ta wartość jest nazywana identyfikator serwera aplikacji.

    ![Uzyskiwanie Identyfikatora aplikacji](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Tworzenie aplikacji klienckiej

Druga aplikacja usługi Azure AD jest używany, gdy logują się przy użyciu interfejsu wiersza polecenia Kubernetes (kubectl).

1. Wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **nowej rejestracji**.

    a. Nadaj aplikacji nazwę, taką jak *AKSAzureADClient*.

    b. Aby uzyskać **obsługiwane typy kont**, wybierz opcję **kont w tym katalogu organizacji tylko**.

    c. Wybierz **Web** dla identyfikatora URI przekierowania typu, a następnie wprowadź dowolną wartość sformatowany identyfikator URI takich jak *https://aksazureadclient* .

    d. Wybierz **zarejestrować** po zakończeniu.

2. W okienku po lewej stronie aplikacji usługi Azure AD wybierz **uprawnienia do interfejsu API**, a następnie wybierz pozycję **+ Dodaj uprawnienia**.

    a. Wybierz **Moje interfejsy API**, a następnie wybierz utworzony w poprzednim kroku, takie jak aplikacja serwera usługi Azure AD *AKSAzureADServer*.

    b. Wybierz **delegowane uprawnienia**, a następnie zaznacz pole wyboru obok aplikacji serwera usługi Azure AD.

    ![Skonfiguruj uprawnienia aplikacji](media/aad-integration/select-api.png)

    c. Wybierz **Dodaj uprawnienia**.

    d. W obszarze **wyrazić zgody**, wybierz opcję **udzielić zgody administratora**. Ten przycisk jest niedostępny, jeśli bieżące konto nie jest administratorem dzierżawy. Po udzieleniu uprawnień są następujące powiadomienie jest wyświetlane w portalu:

    ![Powiadomienie o pomyślnym uprawnienia przyznane](media/aad-integration/permissions-granted.png)

3. W okienku po lewej stronie aplikacji usługi Azure AD wybierz **uwierzytelniania**.

    - W obszarze **domyślny typ klienta**, wybierz opcję **tak** do **traktować klienta jako publicznych klienta**.

5. W okienku po lewej stronie aplikacji usługi Azure AD Zanotuj identyfikator aplikacji. Podczas wdrażania klastra usługi AKS z obsługą usługi AD systemu Azure, ta wartość jest nazywana identyfikator klienta aplikacji.

   ![Uzyskiwanie Identyfikatora aplikacji](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Pobieranie Identyfikatora dzierżawy

Następnie Uzyskaj identyfikator dzierżawy platformy Azure. Ta wartość jest używana podczas tworzenia klastra usługi AKS.

W witrynie Azure portal, wybierz **usługi Azure Active Directory** > **właściwości** i zanotuj **identyfikator katalogu**. Podczas tworzenia klastra usługi AKS z obsługą usługi AD systemu Azure, wartość ta jest nazywana identyfikatorem dzierżawy.

![Uzyskaj identyfikator dzierżawy platformy Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Wdrażanie klastra AKS

Użyj [Tworzenie grupy az][az-group-create] polecenie, aby utworzyć grupę zasobów dla klastra AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Użyj [tworzenie az aks][az-aks-create] polecenie, aby wdrożyć klaster AKS. Następnie zastąp wartości w następujące przykładowe polecenie. Użyj wartości zebrane podczas tworzenia aplikacji usługi Azure AD dla Identyfikatora aplikacji serwera, klucz tajny aplikacji, Identyfikatora aplikacji klienckiej i identyfikator dzierżawy.

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

Klaster AKS zajmuje kilka minut, aby utworzyć.

## <a name="create-an-rbac-binding"></a>Utwórz powiązanie RBAC

Przed skorzystaniem z konta usługi Azure Active Directory z klastrem usługi AKS, należy utworzyć powiązania roli lub powiązania roli klastra. Role definiują uprawnienia, aby udzielić i powiązania zastosować je do odpowiednich użytkowników. Te przypisania można zastosować do danej przestrzeni nazw lub dla całego klastra. Aby uzyskać więcej informacji, zobacz [autoryzacji RBAC przy użyciu][rbac-authorization].

Najpierw za pomocą [az aks get-credentials][az-aks-get-credentials] polecenia `--admin` argument do logowania do klastra z prawami dostępu administratora.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Następnie należy utworzyć ClusterRoleBinding dla konta usługi Azure AD, które chcesz udzielić dostępu do klastra usługi AKS. Poniższy przykład zapewnia konta o pełny dostęp do wszystkich przestrzeni nazw w klastrze:

- Jeśli użytkownika można przyznać, że powiązanie funkcji RBAC dla znajduje się w tej samej dzierżawie usługi Azure AD, należy przypisać uprawnienia, w oparciu o nazwę główną użytkownika (UPN). Przejdź do kroku do tworzenia manifestu YAML ClusterRoleBinding.

- Jeśli użytkownik znajduje się w innej usłudze Azure AD dzierżawy, kwerendy i użyć **objectId** właściwości zamiast tego. Jeśli to konieczne, uzyskać identyfikator obiektu konta użytkowników wymagane za pomocą [az ad użytkownika show][az-ad-user-show] polecenia. Podaj główną nazwę użytkownika (UPN) konta wymagane:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Utwórz plik, takich jak *rbac-aad-user.yaml*, a następnie wklej poniższą zawartość. W ostatnim wierszu, Zastąp **userPrincipalName_or_objectId** o identyfikatorze głównej nazwy użytkownika lub obiekt. Wybór zależy od tego, czy użytkownik jest tą samą dzierżawą usługi Azure AD, czy nie.

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

Zastosuj powiązanie, używając [zastosować kubectl][kubectl-apply] polecenia, jak pokazano w poniższym przykładzie:

```console
kubectl apply -f rbac-aad-user.yaml
```

Powiązanie roli można również tworzyć dla wszystkich członków grupy usługi Azure AD. Określone grupy usługi Azure AD za pomocą identyfikator obiektu grupy, jak pokazano w poniższym przykładzie.

Utwórz plik, takich jak *rbac-aad-group.yaml*, a następnie wklej poniższą zawartość. Zaktualizuj identyfikator obiektu grupy przy użyciu jednego z dzierżawą usługi Azure AD:

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

Zastosuj powiązanie, używając [zastosować kubectl][kubectl-apply] polecenia, jak pokazano w poniższym przykładzie:

```console
kubectl apply -f rbac-aad-group.yaml
```

Aby uzyskać więcej informacji na temat zabezpieczania klastra Kubernetes za pomocą funkcji RBAC, zobacz [autoryzacji RBAC przy użyciu][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Dostęp do klastra z usługą Azure AD

Ściągnij kontekst dla użytkowników bez uprawnień administratora przy użyciu [az aks get-credentials][az-aks-get-credentials] polecenia.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Po uruchomieniu `kubectl` polecenia zostanie wyświetlony monit do uwierzytelnienia za pomocą platformy Azure. Postępuj zgodnie z wyświetlanymi instrukcjami, aby zakończyć proces, jak pokazano w poniższym przykładzie:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Po zakończeniu procesu jest buforowany token uwierzytelniania. Zostanie wyświetlony tylko monit Zaloguj się ponownie po wygaśnięciu ważności tokenu lub plik konfiguracji Kubernetes jest utworzony ponownie.

Jeśli po pomyślnym zalogowaniu się zostanie wyświetlony komunikat o błędzie autoryzacji, sprawdź następujące kryteria:

```console
error: You must be logged in to the server (Unauthorized)
```


- Zdefiniowana jest odpowiedni obiekt o identyfikatorze lub główną nazwę użytkownika, w zależności od Jeśli konto użytkownika jest w tej samej dzierżawie usługi Azure AD, czy nie.
- Użytkownik nie jest członkiem więcej niż 200 grup.
- Zdefiniowane podczas rejestracji aplikacji serwera jest zgodna z wartości ustawionej za pomocą klucza tajnego `--aad-server-app-secret`.

## <a name="next-steps"></a>Następne kroki

Aby użyć usługi Azure AD użytkownicy i grupy do kontrolowania dostępu do zasobów klastra, zobacz [kontrolować dostęp do zasobów klastra przy użyciu tożsamości usługi Azure AD i kontroli dostępu opartej na rolach w usłudze AKS][azure-ad-rbac].

Aby uzyskać więcej informacji na temat zabezpieczonych klastrów usługi Kubernetes, zobacz [opcje dostęp i tożsamość dla usługi AKS][rbac-authorization].

Aby dowiedzieć się więcej na temat kontrolowanie tożsamości i zasobów, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze AKS][operator-best-practices-identity].

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

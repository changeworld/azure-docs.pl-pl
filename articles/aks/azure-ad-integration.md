---
title: Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service
description: Jak utworzyć klastry usługi Azure Kubernetes (Azure Kubernetes) z obsługą usługi Azure (Azure U).
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: de57a46f92fab2486aa7722daf8745a01be1f4f6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617593"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service

Usługę Azure Kubernetes Service (AKS) można skonfigurować do używania usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników. W tej konfiguracji można zalogować się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD.

Administratorzy klastrów mogą konfigurować kontrolę dostępu opartą na rolach kubernetes (RBAC) na podstawie tożsamości użytkownika lub członkostwa w grupie katalogowej.

W tym artykule wyjaśniono, jak:

- Wdrażanie wymagań wstępnych dla usługi AKS i usługi Azure AD.
- Wdrażanie klastra obsługującego usługę Azure AD.
- Utwórz podstawową rolę RBAC w klastrze AKS przy użyciu witryny Azure portal.

Można również wykonać te kroki przy użyciu [interfejsu wiersza polecenia platformy Azure][azure-ad-cli].

> [!NOTE]
> Usługę Azure AD można włączyć tylko podczas tworzenia nowego klastra z włączoną funkcją RBAC. Nie można włączyć usługi Azure AD w istniejącym klastrze usługi AKS.

## <a name="authentication-details"></a>Szczegóły uwierzytelniania

Uwierzytelnianie usługi Azure AD jest dostarczane do klastrów AKS, które mają OpenID Connect. OpenID Connect to warstwa tożsamości zbudowana na szczycie protokołu OAuth 2.0.

Aby uzyskać więcej informacji na temat OpenID Connect, zobacz [Autoryzowanie dostępu do aplikacji sieci Web przy użyciu openid connect i usługi Azure AD][open-id-connect].

Wewnątrz klastra Kubernetes uwierzytelnianie tokenu elementu webhook jest używane do tokenów uwierzytelniania. Uwierzytelnianie tokenu elementu webhook jest skonfigurowane i zarządzane jako część klastra AKS.

Aby uzyskać więcej informacji na temat uwierzytelniania tokenu elementu webhook, zobacz sekcję [Uwierzytelnianie tokenów elementu webhook][kubernetes-webhook] w dokumentacji usługi Kubernetes.

Aby zapewnić uwierzytelnianie usługi Azure AD dla klastra AKS, tworzone są dwie aplikacje usługi Azure AD. Pierwsza aplikacja jest składnikiem serwera, który zapewnia uwierzytelnianie użytkowników. Druga aplikacja jest składnikiem klienta, który jest używany, gdy zostanie wyświetlony monit przez interfejs wiersza polecenia do uwierzytelniania. Ta aplikacja kliencka używa aplikacji serwera do rzeczywistego uwierzytelniania poświadczeń dostarczonych przez klienta.

> [!NOTE]
> Podczas konfigurowania usługi Azure AD dla uwierzytelniania AKS skonfigurowane są dwie aplikacje usługi Azure AD. Kroki delegowania uprawnień dla każdej aplikacji muszą być wykonywane przez administratora dzierżawy platformy Azure.

## <a name="create-the-server-application"></a>Tworzenie aplikacji serwera

Pierwsza aplikacja usługi Azure AD jest stosowana w celu uzyskania członkostwa użytkownika w grupie usługi Azure AD. Aby utworzyć tę aplikację w witrynie Azure portal:

1. Wybierz**rejestracje** > aplikacji **usługi Azure Active Directory** > Nowa**rejestracja**.

    a. Nadaj aplikacji nazwę, taką jak *AKSAzureADServer*.

    b. W przypadku **obsługiwanych typów kont**wybierz pozycję **Konta tylko w tym katalogu organizacyjnym**.
    
    d. Wybierz **pozycję Web** dla typu URI przekierowania, a następnie wprowadź *https://aksazureadserver*dowolną wartość w formacie URI, taką jak .

    d. Po zakończeniu wybierz **pozycję Zarejestruj** się.

2. Wybierz **opcję Manifest**, a następnie edytuj **grupęMembershipClaims:** wartość jako **Wszystkie**. Po zakończeniu aktualizacji wybierz pozycję **Zapisz**.

    ![Aktualizowanie członkostwa w grupie do wszystkich](media/aad-integration/edit-manifest.png)

3. W lewym okienku aplikacji usługi Azure AD wybierz pozycję **Certyfikaty & wpisy tajne**.

    a. Wybierz **+ Nowy klucz tajny klienta**.

    b. Dodaj opis klucza, taki jak *serwer usługi AD usługi AKS.* Wybierz godzinę wygaśnięcia, a następnie wybierz pozycję **Dodaj**.

    d. Zwróć uwagę na wartość klucza, która jest wyświetlana tylko w tej chwili. Podczas wdrażania klastra AKS z obsługą usługi Azure AD ta wartość jest nazywana kluczem tajnym aplikacji serwera.

4. W lewym okienku aplikacji usługi Azure AD wybierz pozycję **Uprawnienia interfejsu API**, a następnie wybierz pozycję + Dodaj **uprawnienie**.

    a. W obszarze **Interfejsy API firmy Microsoft**wybierz pozycję **Microsoft Graph**.

    b. Wybierz pozycję **Uprawnienia delegowane**, a następnie zaznacz pole wyboru obok **pozycji Katalog > Directory.Read.All (Odczyt danych katalogu)**.

    d. Jeśli domyślne uprawnienie delegowane dla **użytkownika > User.Read (Zaloguj się i przeczytaj profil użytkownika)** nie istnieje, zaznacz pole wyboru obok niego.

    d. Wybierz **pozycję Uprawnienia aplikacji**, a następnie zaznacz pole wyboru obok pozycji Katalog > **Directory.Read.All (Odczyt danych katalogu)**.

    ![Ustawianie uprawnień do wykresu](media/aad-integration/graph-permissions.png)

    e. Wybierz **pozycję Dodaj uprawnienia** do zapisywania aktualizacji.

    f. W obszarze **Zgoda na udzielenie dotacji**wybierz pozycję **Udzielaj zgody administratora**. Ten przycisk nie będzie dostępny, a używane bieżące konto nie jest wyświetlane jako administrator dzierżawy.

    Po pomyślnym przyznaniu uprawnień w portalu wyświetlane jest następujące powiadomienie:

   ![Powiadomienie o pomyślnych przyznanych uprawnieniach](media/aad-integration/permissions-granted.png)

5. W lewym okienku aplikacji usługi Azure AD wybierz pozycję **Udostępnij interfejs API**, a następnie wybierz pozycję **+ Dodaj zakres**.
    
    a. Wprowadź **nazwę zakresu**, **nazwę wyświetlaną zgody administratora,** a następnie **opis zgody administratora,** taki jak *AKSAzureADServer*.

    b. Upewnij się, że **stan** jest ustawiony na **Włączone**.

    ![Uwidacznianie aplikacji serwera jako interfejsu API do użytku z innymi usługami](media/aad-integration/expose-api.png)

    d. Wybierz **pozycję Dodaj zakres**.

6. Wróć do strony **Przegląd** aplikacji i zanotuj **identyfikator aplikacji (klienta).** Podczas wdrażania klastra AKS z obsługą usługi Azure AD ta wartość jest nazywana identyfikatorem aplikacji serwera.

    ![Pobierz identyfikator aplikacji](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Tworzenie aplikacji klienckiej

Druga aplikacja usługi Azure AD jest używana podczas logowania się za pomocą interfejsu wiersza polecenia Kubernetes (kubectl).

1. Wybierz**rejestracje** > aplikacji **usługi Azure Active Directory** > Nowa**rejestracja**.

    a. Nadaj aplikacji nazwę, taką jak *AKSAzureADClient*.

    b. W przypadku **obsługiwanych typów kont**wybierz pozycję **Konta tylko w tym katalogu organizacyjnym**.

    d. Wybierz **pozycję Web** dla typu Przekierowanie identyfikatora URI, a *https://aksazureadclient*następnie wprowadź dowolną wartość w formacie URI, taką jak .

    >[!NOTE]
    >Jeśli tworzysz nowy klaster z włączoną funkcją RBAC do obsługi usługi Azure Monitor dla kontenerów, dodaj następujące dwa dodatkowe adresy URL przekierowania do tej listy jako typy aplikacji **sieci Web.** Pierwsza podstawowa wartość `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` adresu URL powinna być, a druga podstawowa wartość adresu URL powinna być`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Jeśli korzystasz z tej funkcji w Chinach platformy Azure, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` pierwszą podstawową wartością adresu URL powinna być i druga podstawowa wartość adresu URL powinna być`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Aby uzyskać więcej informacji, zobacz [Jak skonfigurować funkcję Live Data (wersja zapoznawcza)](../azure-monitor/insights/container-insights-livedata-setup.md) dla usługi Azure Monitor dla kontenerów oraz kroki konfigurowania uwierzytelniania w sekcji [Konfigurowanie zintegrowanego uwierzytelniania usługi AD.](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)

    d. Po zakończeniu wybierz **pozycję Zarejestruj** się.

2. W lewym okienku aplikacji usługi Azure AD wybierz pozycję **Uprawnienia interfejsu API**, a następnie wybierz pozycję + Dodaj **uprawnienie**.

    a. Wybierz **pozycję Moje interfejsy API**, a następnie wybierz aplikację serwera usługi Azure AD utworzoną w poprzednim kroku, taką jak *AKSAzureADServer*.

    b. Wybierz pozycję **Uprawnienia delegowane**, a następnie zaznacz pole wyboru obok aplikacji serwera usługi Azure AD.

    ![Konfigurowanie uprawnień aplikacji](media/aad-integration/select-api.png)

    d. Wybierz pozycję **Dodaj uprawnienia**.

    d. W obszarze **Zgoda na udzielenie dotacji**wybierz pozycję **Udzielaj zgody administratora**. Ten przycisk nie jest dostępny, jeśli bieżące konto nie jest administratorem dzierżawy. Po przyznaniu uprawnień w portalu wyświetlane jest następujące powiadomienie:

    ![Powiadomienie o pomyślnych przyznanych uprawnieniach](media/aad-integration/permissions-granted.png)

3. W lewym okienku aplikacji usługi Azure AD wybierz pozycję **Uwierzytelnianie**.

    - W obszarze **Domyślny typ klienta**wybierz pozycję **Tak,** aby **traktować klienta jako klienta publicznego**.

5. W lewym okienku aplikacji usługi Azure AD należy zwrócić uwagę na identyfikator aplikacji. Podczas wdrażania klastra AKS z obsługą usługi Azure AD ta wartość jest nazywana identyfikatorem aplikacji klienckiej.

   ![Pobierz identyfikator aplikacji](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Uzyskaj identyfikator dzierżawy

Następnie uzyskaj identyfikator dzierżawy platformy Azure. Ta wartość jest używana podczas tworzenia klastra AKS.

W witrynie Azure Portal wybierz pozycję**Właściwości** **usługi Azure Active Directory** > i zanotuj **identyfikator katalogu**. Podczas tworzenia klastra AKS z obsługą usługi Azure AD ta wartość jest nazywana identyfikatorem dzierżawy.

![Uzyskaj identyfikator dzierżawy platformy Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Wdrażanie klastra AKS

Polecenie [utwórz grupę AZ][az-group-create] służy do tworzenia grupy zasobów dla klastra AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Użyj polecenia [az aks create,][az-aks-create] aby wdrożyć klaster AKS. Następnie zastąp wartości w poniższym przykładowym poleceniu. Użyj wartości zebranych podczas tworzenia aplikacji usługi Azure AD dla identyfikatora aplikacji serwera, klucza tajnego aplikacji, identyfikatora aplikacji klienckiej i identyfikatora dzierżawy.

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

Utworzenie klastra usługi AKS zajmuje kilka minut.

## <a name="create-an-rbac-binding"></a>Tworzenie powiązania RBAC

> [!NOTE]
> W nazwie powiązanej roli klastra jest rozróżniana wielkość liter.

Przed użyciem konta usługi Azure Active Directory z klastrem AKS należy utworzyć powiązanie ról lub powiązanie ról klastra. Role definiują uprawnienia do udzielania, a powiązania stosują je do żądanych użytkowników. Te przypisania mogą być stosowane do danego obszaru nazw lub w całym klastrze. Aby uzyskać więcej informacji, zobacz [Korzystanie z autoryzacji RBAC][rbac-authorization].

Najpierw użyj polecenia [az aks get-credentials][az-aks-get-credentials] z argumentem, `--admin` aby zalogować się do klastra z dostępem administratora.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Następnie utwórz clusterrolebinding dla konta usługi Azure AD, które chcesz udzielić dostępu do klastra AKS. Poniższy przykład daje konto pełny dostęp do wszystkich obszarów nazw w klastrze:

- Jeśli użytkownik, dla którego udzielasz powiązania RBAC, znajduje się w tej samej dzierżawie usługi Azure AD, przypisz uprawnienia na podstawie głównej nazwy użytkownika (UPN). Przejdź do kroku, aby utworzyć manifest YAML dla ClusterRoleBinding.

- Jeśli użytkownik znajduje się w innej dzierżawie usługi Azure AD, kwerendy i używać **objectId** właściwości zamiast. W razie potrzeby pobierz identyfikator objectId wymaganego konta użytkownika za pomocą polecenia [az ad user show.][az-ad-user-show] Podaj nazwę główną użytkownika (UPN) wymaganego konta:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Utwórz plik, taki jak *rbac-aad-user.yaml,* a następnie wklej następującą zawartość. W ostatnim wierszu zastąp **userPrincipalName_or_objectId** identyfikatorem UPN lub obiektu. Wybór zależy od tego, czy użytkownik jest taką samą dzierżawą usługi Azure AD, czy nie.

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

Zastosuj powiązanie za pomocą [polecenia kubectl apply,][kubectl-apply] jak pokazano w poniższym przykładzie:

```console
kubectl apply -f rbac-aad-user.yaml
```

Powiązanie roli można również utworzyć dla wszystkich członków grupy usługi Azure AD. Grupy usługi Azure AD są określane przy użyciu identyfikatora obiektu grupy, jak pokazano w poniższym przykładzie.

Utwórz plik, na przykład *rbac-aad-group.yaml,* a następnie wklej następującą zawartość. Zaktualizuj identyfikator obiektu grupy o jeden z dzierżawy usługi Azure AD:

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

Zastosuj powiązanie za pomocą [polecenia kubectl apply,][kubectl-apply] jak pokazano w poniższym przykładzie:

```console
kubectl apply -f rbac-aad-group.yaml
```

Aby uzyskać więcej informacji na temat zabezpieczania klastra Kubernetes za pomocą funkcji RBAC, zobacz [Korzystanie z autoryzacji RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Uzyskiwanie dostępu do klastra za pomocą usługi Azure AD

Pobierz kontekst dla użytkownika niebędącego administratorem przy użyciu polecenia [az aks get-credentials.][az-aks-get-credentials]

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Po uruchomieniu `kubectl` polecenia zostanie wyświetlony monit o uwierzytelnienie przy użyciu platformy Azure. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby zakończyć proces, jak pokazano w poniższym przykładzie:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Po zakończeniu procesu token uwierzytelniania jest buforowany. Zostanie wyświetlony monit o ponowne zalogowanie się po wygaśnięciu tokenu lub ponowne utworzenie pliku konfiguracji Kubernetes.

Jeśli po pomyślnym zalogowaniu się zostanie wyświetlony komunikat o błędzie autoryzacji, sprawdź następujące kryteria:

```console
error: You must be logged in to the server (Unauthorized)
```


- Zdefiniowano odpowiedni identyfikator obiektu lub numer UPN, w zależności od tego, czy konto użytkownika znajduje się w tej samej dzierżawie usługi Azure AD, czy nie.
- Użytkownik nie jest członkiem więcej niż 200 grup.
- Klucz tajny zdefiniowany w rejestracji aplikacji dla serwera `--aad-server-app-secret`odpowiada wartości skonfigurowanej przy użyciu programu .

## <a name="next-steps"></a>Następne kroki

Aby używać użytkowników i grup usługi Azure AD do kontrolowania dostępu do zasobów klastra, zobacz [Kontrolowanie dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości usługi Azure AD w usłudze AKS.][azure-ad-rbac]

Aby uzyskać więcej informacji na temat zabezpieczania klastrów kubernetes, zobacz [Opcje dostępu i tożsamości dla usługi AKS][rbac-authorization].

Aby dowiedzieć się więcej na temat kontroli tożsamości i zasobów, zobacz [Najważniejsze wskazówki dotyczące uwierzytelniania i autoryzacji w uzywce AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

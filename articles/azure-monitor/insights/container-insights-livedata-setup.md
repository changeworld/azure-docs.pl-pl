---
title: Azure Monitor Instalatora dla kontenerów dane dynamiczne (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania widoku w czasie rzeczywistym dzienników kontenerów (stdout/stderr) i zdarzeń bez używania polecenia kubectl z Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 5a3d020132e3c93eab7fec46d1ffe45d00b5ed43
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404698"
---
# <a name="how-to-setup-the-live-data-preview-feature"></a>Jak skonfigurować funkcję Live Data (wersja zapoznawcza)

Aby wyświetlić dane na żywo (wersja zapoznawcza) przy użyciu Azure Monitor kontenerów z klastrów usługi Azure Kubernetes Service (AKS), należy skonfigurować uwierzytelnianie, aby udzielić uprawnień dostępu do danych Kubernetes. Ta konfiguracja zabezpieczeń umożliwia dostęp do danych w czasie rzeczywistym za pomocą interfejsu API Kubernetes bezpośrednio w Azure Portal.

Ta funkcja obsługuje trzy różne metody kontroli dostępu do dzienników, zdarzeń i metryk:

- AKS, bez autoryzacji Kubernetes RBAC włączone
- Włączone z autoryzacji RBAC platformy Kubernetes w usłudze AKS
- AKS włączone z logowaniem jednokrotnym opartym na protokole SAML Azure Active Directory (AD)

Te instrukcje wymagają zarówno dostępu administracyjnego do klastra Kubernetes, jak i w przypadku konfigurowania do korzystania z Azure Active Directory (AD) do uwierzytelniania użytkowników, dostępu administracyjnego do usługi Azure AD.  

W tym artykule wyjaśniono, jak skonfigurować uwierzytelnianie, aby kontrolować dostęp do funkcji danych na żywo (wersja zapoznawcza) z klastra:

- Klaster AKS z włączoną kontrolą dostępu opartą na rolach (RBAC)
- Azure Active Directory zintegrowany klaster AKS. 

>[!NOTE]
>Klastry AKS włączone jako [klastry prywatne](https://azure.microsoft.com/updates/aks-private-cluster/) są nieobsługiwane w przypadku tej funkcji. Ta funkcja wykorzystuje bezpośrednio dostęp do interfejsu API Kubernetes za pośrednictwem serwera proxy z przeglądarki. Włączenie zabezpieczeń sieci w celu blokowania interfejsu API Kubernetes z tego serwera proxy spowoduje zablokowanie tego ruchu. 

>[!NOTE]
>Ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, w tym na platformie Azure (Chiny). Nie jest ona obecnie dostępna w systemie Azure dla instytucji rządowych USA.

## <a name="authentication-model"></a>Model uwierzytelniania

Funkcja dane dynamiczne (wersja zapoznawcza) korzysta z interfejsu API Kubernetes, identycznego z `kubectl` narzędziem wiersza polecenia. Punkty końcowe interfejsu API Kubernetes używają certyfikatu z podpisem własnym, którego przeglądarka nie będzie mogła zweryfikować. Ta funkcja wykorzystuje wewnętrzny serwer proxy do weryfikowania certyfikatu za pomocą usługi AKS, co zapewnia, że ruch jest zaufany.

Azure Portal poprosi o zweryfikowanie poświadczeń logowania do klastra Azure Active Directory i przekieruje użytkownika do konfiguracji rejestracji klienta podczas tworzenia klastra (i ponownie skonfigurowany w tym artykule). To zachowanie jest podobne do procesu uwierzytelniania wymaganego przez `kubectl`. 

>[!NOTE]
>Autoryzacja klastra jest zarządzana przez program Kubernetes oraz model zabezpieczeń, z którym jest on skonfigurowany. Użytkownicy uzyskujący dostęp do tej funkcji wymagają uprawnień do pobrania konfiguracji Kubernetes (*kubeconfig*), podobnie jak w przypadku uruchamiania `az aks get-credentials -n {your cluster name} -g {your resource group}`. Ten plik konfiguracji zawiera token autoryzacji i uwierzytelniania dla **roli użytkownika klastra usługi Azure Kubernetes Service**, w przypadku klastrów z obsługą kontroli RBAC platformy Azure i AKS bez włączonej autoryzacji RBAC. Zawiera informacje o rejestracji w usłudze Azure AD i klientach, gdy AKS jest włączona z logowaniem jednokrotnym opartym na protokole SAML Azure Active Directory (AD).

>[!IMPORTANT]
>Aby możliwe było pobranie `kubeconfig` i użycie tej funkcji, użytkownicy tych funkcji muszą mieć [rolę użytkownika klastra usługi Azure Kubernetes](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) w klastrze. Użytkownicy **nie** wymagają dostępu współautora do klastra w celu korzystania z tej funkcji. 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Klaster Kubernetes bez RBAC włączone

Jeśli masz klaster Kubernetes, nie jest skonfigurowany z autoryzacji Kubernetes RBAC lub zintegrowana z usługą Azure AD logowania jednokrotnego, nie musisz wykonaj następujące kroki. Wynika to z faktu, że uprawnienia administracyjne są domyślnie dostępne w konfiguracji bez kontroli RBAC.

## <a name="configure-kubernetes-rbac-authentication"></a>Konfigurowanie uwierzytelniania RBAC Kubernetes

Po włączeniu autoryzacji RBAC Kubernetes są wykorzystywane dwa użytkownicy: **clusterUser** i **clusterAdmin** , aby uzyskać dostęp do interfejsu API Kubernetes. Jest to podobne do uruchamiania `az aks get-credentials -n {cluster_name} -g {rg_name}` bez opcji administracyjnych. Oznacza to, że **clusterUser** musi mieć przyznane dostęp do punktów końcowych w interfejsie API Kubernetes.

Krokach w poniższym przykładzie pokazano sposób konfigurowania powiązania rolę klastra za pomocą tego szablonu konfiguracji yaml.

1. Skopiuj i wklej plik yaml i zapisz go jako LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
          verbs: ["get", "list"] 
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
    subjects: 
    - kind: User 
      name: clusterUser 
      apiGroup: rbac.authorization.k8s.io 
    ```

2. Aby zaktualizować konfigurację, uruchom następujące polecenie: `kubectl apply -f LogReaderRBAC.yaml`.

>[!NOTE] 
> Jeśli w klastrze zastosowano poprzednią wersję pliku `LogReaderRBAC.yaml`, zaktualizuj ją, kopiując i wklejając nowy kod przedstawiony w kroku 1 powyżej, a następnie uruchom polecenie pokazane w kroku 2, aby zastosować je do klastra.

## <a name="configure-ad-integrated-authentication"></a>Konfigurowanie uwierzytelniania zintegrowanego usługi AD 

Klaster AKS skonfigurowany do korzystania z Azure Active Directory (AD) do uwierzytelniania użytkowników korzysta z poświadczeń logowania osoby, która uzyskuje dostęp do tej funkcji. W tej konfiguracji możesz zalogować się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD.

Należy ponownie skonfigurować rejestrację klienta usługi Azure AD, aby umożliwić Azure Portal przekierowywanie stron autoryzacji jako zaufanego adresu URL przekierowania. Użytkownicy z usługi Azure AD uzyskują dostęp bezpośrednio do tych samych punktów końcowych interfejsu API Kubernetes za pośrednictwem **ClusterRoles** i **ClusterRoleBindings**. 

Aby uzyskać więcej informacji na temat zaawansowanej konfiguracji zabezpieczeń w programie Kubernetes, zapoznaj się z [dokumentacją Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Jeśli tworzysz nowy klaster z obsługą kontroli dostępu do sieci, zobacz [integrowanie Azure Active Directory z usługą Azure Kubernetes](../../aks/azure-ad-integration.md) i postępuj zgodnie z instrukcjami, aby skonfigurować uwierzytelnianie w usłudze Azure AD. Podczas wykonywania kroków w celu utworzenia aplikacji klienckiej w tej sekcji wyróżnia się dwa adresy URL przekierowania, które należy utworzyć dla Azure Monitor kontenerów. 

### <a name="client-registration-reconfiguration"></a>Ponowna konfiguracja rejestracji klienta

1. Zlokalizuj rejestrację klienta dla klastra Kubernetes w usłudze Azure AD w obszarze **Azure Active Directory > rejestracje aplikacji** w Azure Portal.

2. Wybierz pozycję **uwierzytelnianie** w okienku po lewej stronie. 

3. Dodaj dwa adresy URL przekierowania do tej listy jako typy aplikacji **sieci Web** . Wartość pierwszego podstawowego adresu URL powinna być `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`, a druga podstawowa wartość adresu URL powinna być `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

    >[!NOTE]
    >Jeśli używasz tej funkcji w Chinach platformy Azure, najpierw należy `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` pierwszą podstawową wartość adresu URL, a druga podstawowa wartość adresu URL powinna być `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`. 
    
4. Po zarejestrowaniu adresów URL przekierowania w obszarze **Ustawienia zaawansowane**wybierz pozycję Opcje **dostępu tokeny** i **identyfikatory** , a następnie Zapisz zmiany.

>[!NOTE]
>Konfigurowanie uwierzytelniania przy użyciu Azure Active Directory logowania jednokrotnego można wykonać tylko podczas początkowego wdrażania nowego klastra AKS. Nie można skonfigurować logowania jednokrotnego na dla klastra usługi AKS już wdrożone.
  
>[!IMPORTANT]
>W przypadku zmiany konfiguracji usługi Azure AD na potrzeby uwierzytelniania użytkowników przy użyciu zaktualizowanego identyfikatora URI Wyczyść pamięć podręczną przeglądarki, aby upewnić się, że zaktualizowany token uwierzytelniania zostanie pobrany i zastosowany.

## <a name="grant-permission"></a>Udziel uprawnienia

Każde konto usługi Azure AD musi mieć uprawnienia do odpowiednich interfejsów API w programie Kubernetes, aby można było uzyskać dostęp do funkcji dane dynamiczne (wersja zapoznawcza). Kroki służące do przyznania konta Azure Active Directory są podobne do kroków opisanych w sekcji [KUBERNETES RBAC Authentication](#configure-kubernetes-rbac-authentication) . Przed zastosowaniem szablonu konfiguracji YAML do klastra Zastąp wartość **clusterUser** w obszarze **ClusterRoleBinding** wybranym użytkownikiem. 

>[!IMPORTANT]
>Jeśli użytkownik, któremu przyznano powiązanie RBAC, jest w tej samej dzierżawie usługi Azure AD, przypisz uprawnienia na podstawie elementu userPrincipalName. Jeśli użytkownik znajduje się w innej dzierżawie usługi Azure AD, zapytaj o i Użyj właściwości objectId.

Aby uzyskać dodatkową pomoc dotyczącą konfigurowania klastra AKS **ClusterRoleBinding**, zobacz [Tworzenie powiązania RBAC](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu uwierzytelniania można wyświetlić [metryki](container-insights-livedata-metrics.md), [wdrożenia](container-insights-livedata-deployments.md)oraz [zdarzenia i dzienniki](container-insights-livedata-overview.md) w czasie rzeczywistym z klastra.

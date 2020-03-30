---
title: Konfigurowanie usługi Azure Monitor dla kontenerów Dane na żywo (wersja zapoznawcza) | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania widoku w czasie rzeczywistym dzienników kontenerów (stdout/stderr) i zdarzeń bez użycia kubectl z usługą Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275375"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Jak skonfigurować funkcję Live Data (wersja zapoznawcza)

Aby wyświetlić dane na żywo (wersja zapoznawcza) w usłudze Azure Monitor dla kontenerów z klastrów usługi Azure Kubernetes (AKS), należy skonfigurować uwierzytelnianie w celu udzielenia uprawnień dostępu do danych usługi Kubernetes. Ta konfiguracja zabezpieczeń umożliwia dostęp w czasie rzeczywistym do danych za pośrednictwem interfejsu API usługi Kubernetes bezpośrednio w witrynie Azure portal.

Ta funkcja obsługuje następujące metody kontrolowania dostępu do dzienników, zdarzeń i metryk:

- AKS bez włączonej autoryzacji RBAC kubernetes
- AKS włączone z autoryzacją Kubernetes RBAC
    - Usługa AKS skonfigurowana z klastrem monitorowania roli ** [klastra klastra klastra](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- Usługa AKS włączona z jednokrotnym znakiem opartym na usłudze Azure Active Directory (AD)

Te instrukcje wymagają zarówno dostępu administracyjnego do klastra usługi Kubernetes, jak i konfigurowania do używania usługi Azure Active Directory (AD) do uwierzytelniania użytkowników, dostępu administracyjnego do usługi Azure AD.  

W tym artykule wyjaśniono, jak skonfigurować uwierzytelnianie w celu kontrolowania dostępu do funkcji Live Data (wersja zapoznawcza) z klastra:

- Klaster AKS oparty na rolach (RBAC)
- Zintegrowany klaster AKS usługi Azure Active Directory. 

>[!NOTE]
>Klastry AKS włączone jako [klastry prywatne](https://azure.microsoft.com/updates/aks-private-cluster/) nie są obsługiwane za pomocą tej funkcji. Ta funkcja polega na bezpośrednim uzyskiwaniu dostępu do interfejsu API kubernetes za pośrednictwem serwera proxy z przeglądarki. Włączenie zabezpieczeń sieciowych w celu zablokowania interfejsu API usługi Kubernetes z tego serwera proxy spowoduje zablokowanie tego ruchu. 

>[!NOTE]
>Ta funkcja jest dostępna we wszystkich regionach platformy Azure, w tym w chinach platformy Azure. Obecnie nie jest dostępna w usłudze Azure US Government.

## <a name="authentication-model"></a>Model uwierzytelniania

Funkcje Live Data (wersja zapoznawcza) wykorzystują interfejs `kubectl` API kubernetes, identyczny z narzędziem wiersza polecenia. Punkty końcowe interfejsu API usługi Kubernetes wykorzystują certyfikat z podpisem własnym, którego przeglądarka nie będzie mogła zweryfikować. Ta funkcja korzysta z wewnętrznego serwera proxy do sprawdzania poprawności certyfikatu za pomocą usługi AKS, zapewniając, że ruch jest zaufany.

Portal Azure monituje o sprawdzenie poprawności poświadczeń logowania dla klastra usługi Azure Active Directory i przekierowuje do konfiguracji rejestracji klienta podczas tworzenia klastra (i ponownie skonfigurowany w tym artykule). To zachowanie jest podobne do procesu `kubectl`uwierzytelniania wymaganego przez program . 

>[!NOTE]
>Autoryzacja do klastra jest zarządzana przez program Kubernetes i model zabezpieczeń, za pomocą który jest skonfigurowany. Użytkownicy uzyskujący dostęp do tej funkcji wymagają uprawnień do pobrania konfiguracji `az aks get-credentials -n {your cluster name} -g {your resource group}`Kubernetes (*kubeconfig*), podobnej do uruchomionej . Ten plik konfiguracyjny zawiera token autoryzacji i uwierzytelniania dla **roli użytkownika klastra usługi Azure Kubernetes**, w przypadku klastrów obsługujących usługę Azure RBAC i AKS bez włączonej autoryzacji RBAC. Zawiera informacje o usłudze Azure AD i szczegóły rejestracji klienta, gdy usługa AKS jest włączona z azure active directory (AD) saml oparte na pojedynczym logacie.

>[!IMPORTANT]
>Użytkownicy tej funkcji wymaga [roli użytkownika klastra klastra platformy Azure Kubernetes](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) do klastra w celu pobrania `kubeconfig` i korzystania z tej funkcji. Użytkownicy **nie** wymagają dostępu współautora do klastra, aby korzystać z tej funkcji. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>Korzystanie z clusterMonitoringUser z klastrami obsługującymi rbac

Aby wyeliminować konieczność stosowania dodatkowych zmian konfiguracji w celu umożliwienia klastrowi wiązania roli użytkownika **Kubernetesuuuuuuuuużyt** do funkcji Live Data (wersja zapoznawcza) po włączeniu autoryzacji [RBAC,](#configure-kubernetes-rbac-authorization) AKS dodał nowe powiązanie roli klastra Kubernetes o nazwie **clusterMonitoringUser**. To powiązanie roli klastra ma wszystkie niezbędne uprawnienia out-of-the-box, aby uzyskać dostęp do interfejsu API kubernetes i punktów końcowych do korzystania z funkcji Live Data (wersja zapoznawcza).

Aby korzystać z funkcji Live Data (wersja zapoznawcza) z tym nowym użytkownikiem, musisz być członkiem roli [współautora](../../role-based-access-control/built-in-roles.md#contributor) w zasobie klastra AKS. Usługa Azure Monitor dla kontenerów, gdy jest włączona, jest skonfigurowana do uwierzytelniania przy użyciu tego użytkownika domyślnie. Jeśli powiązanie roli clusterMonitoringUser nie istnieje w klastrze, **clusterUser** jest używany do uwierzytelniania.

Usługa AKS wydała to nowe powiązanie roli w styczniu 2020 r., więc klastry utworzone przed styczniem 2020 r. nie mają go. Jeśli masz klaster, który został utworzony przed styczniem 2020 r., nowy **clusterMonitoringUser** można dodać do istniejącego klastra, wykonując operację PUT w klastrze lub wykonując inne operacje w klastrze tha wykonuje operację PUT w klastrze, takich jak aktualizowanie wersji klastra.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Klaster Kubernetes bez włączonego rbac

Jeśli masz klaster Kubernetes, który nie jest skonfigurowany z autoryzacją RBAC kubernetes lub zintegrowany z azure ad jednokrotnego logowania, nie trzeba wykonać następujące kroki. Dzieje się tak, ponieważ masz uprawnienia administracyjne domyślnie w konfiguracji innych niż RBAC.

## <a name="configure-kubernetes-rbac-authorization"></a>Konfigurowanie autoryzacji RBAC usługi Kubernetes

Po włączeniu autoryzacji rbas kubernetes, dwóch użytkowników są wykorzystywane: **clusterUser** i **clusterAdmin,** aby uzyskać dostęp do interfejsu API Kubernetes. Jest to podobne `az aks get-credentials -n {cluster_name} -g {rg_name}` do uruchamiania bez opcji administracyjnej. Oznacza to, że **clusterUser** musi uzyskać dostęp do punktów końcowych w interfejsie API usługi Kubernetes.

Poniższe przykładowe kroki pokazują, jak skonfigurować powiązanie roli klastra z tego szablonu konfiguracji yaml.

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

2. Aby zaktualizować konfigurację, uruchom `kubectl apply -f LogReaderRBAC.yaml`następujące polecenie: .

>[!NOTE] 
> Jeśli do klastra zastosowano poprzednią wersję `LogReaderRBAC.yaml` pliku, zaktualizuj ją, kopiując i wklejając nowy kod pokazany w kroku 1 powyżej, a następnie uruchom polecenie pokazane w kroku 2, aby zastosować go do klastra.

## <a name="configure-ad-integrated-authentication"></a>Konfigurowanie uwierzytelniania zintegrowanego z usługą AD 

Klaster usługi AKS skonfigurowany do używania usługi Azure Active Directory (AD) do uwierzytelniania użytkowników wykorzystuje poświadczenia logowania osoby uzyskującej dostęp do tej funkcji. W tej konfiguracji można zalogować się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD.

Rejestracja klienta usługi Azure AD musi zostać ponownie skonfigurowana, aby umożliwić witrynie Azure Portal przekierowanie stron autoryzacji jako zaufanego adresu URL przekierowania. Użytkownicy z usługi Azure AD są następnie udzielane dostęp bezpośrednio do tych samych punktów końcowych interfejsu API Kubernetes za pośrednictwem **ClusterRoles** i **ClusterRoleBindings**. 

Aby uzyskać więcej informacji na temat zaawansowanej konfiguracji zabezpieczeń w programie Kubernetes, zapoznaj się z [dokumentacją programu Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Jeśli tworzysz nowy klaster z włączoną funkcją RBAC, zobacz [Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes](../../aks/azure-ad-integration.md) i postępuj zgodnie z instrukcjami, aby skonfigurować uwierzytelnianie usługi Azure AD. Podczas kroków tworzenia aplikacji klienckiej notatka w tej sekcji wyróżnia dwa adresy URL przekierowania, które należy utworzyć dla usługi Azure Monitor dla kontenerów pasujących do kontenerów określonych w kroku 3 poniżej.

### <a name="client-registration-reconfiguration"></a>Ponowna konfiguracja rejestracji klienta

1. Znajdź rejestrację klienta dla klastra kubernetes w usłudze Azure AD w obszarze **Rejestracji > aplikacji usługi Azure Active Directory** w witrynie Azure portal.

2. Wybierz **pozycję Uwierzytelnianie** z lewego okienka. 

3. Dodaj dwa adresy URL przekierowania do tej listy jako typy aplikacji **sieci Web.** Pierwsza podstawowa wartość `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` adresu URL powinna być, `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`a drugą podstawową wartością adresu URL .

    >[!NOTE]
    >Jeśli używasz tej funkcji w Chinach platformy Azure, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` pierwszą podstawową wartością `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`adresu URL powinna być i drugą podstawową wartością adresu URL. 
    
4. Po zarejestrowaniu adresów URL przekierowania w obszarze **Niejawne przyznanie**wybierz opcje **Tokeny dostępu** i **identyfikatory,** a następnie zapisz zmiany.

>[!NOTE]
>Konfigurowanie uwierzytelniania za pomocą usługi Azure Active Directory do logowania jednokrotnego można wykonać tylko podczas początkowego wdrażania nowego klastra AKS. Nie można skonfigurować logowania jednokrotnego dla już wdrożonego klastra AKS.
  
>[!IMPORTANT]
>Jeśli ponownie skonfigurowano usługę Azure AD do uwierzytelniania użytkowników przy użyciu zaktualizowanego identyfikatora URI, wyczyść pamięć podręczną przeglądarki, aby upewnić się, że zaktualizowany token uwierzytelniania jest pobierany i stosowany.

## <a name="grant-permission"></a>Udzielanie uprawnień

Każde konto usługi Azure AD musi mieć uprawnienia do odpowiednich interfejsów API w usłudze Kubernetes, aby uzyskać dostęp do funkcji Live Data (preview). Kroki przyznawania konta usługi Azure Active Directory są podobne do czynności opisanych w sekcji [uwierzytelniania RBAC usługi Kubernetes.](#configure-kubernetes-rbac-authorization) Przed zastosowaniem szablonu konfiguracji yaml do klastra zastąp **użytkownika klastra** w obszarze **ClusterRoleBinding** żądanym użytkownikiem. 

>[!IMPORTANT]
>Jeśli użytkownik, dla którego udzielasz powiązania RBAC, znajduje się w tej samej dzierżawie usługi Azure AD, przypisz uprawnienia na podstawie nazwy użytkownikaPrincipalName. Jeśli użytkownik znajduje się w innej dzierżawie usługi Azure AD, kwerendy i używać objectId właściwości.

Aby uzyskać dodatkową pomoc dotyczącą konfigurowania klastra AKS **ClusterRoleBinding**, zobacz [Tworzenie powiązania RBAC](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz uwierzytelnianie konfiguracji, można wyświetlić [metryki](container-insights-livedata-metrics.md), [wdrożenia](container-insights-livedata-deployments.md)i [zdarzenia i dzienniki](container-insights-livedata-overview.md) w czasie rzeczywistym z klastra.

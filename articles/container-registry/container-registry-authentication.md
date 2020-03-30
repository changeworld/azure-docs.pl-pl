---
title: Opcje uwierzytelniania rejestru
description: Opcje uwierzytelniania prywatnego rejestru kontenerów platformy Azure, w tym logowanie się za pomocą tożsamości usługi Azure Active Directory przy użyciu podmiotów usługi i przy użyciu opcjonalnych poświadczeń administratora.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247048"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Uwierzytelnij się przy użyciu rejestru kontenerów platformy Azure

Istnieje kilka sposobów uwierzytelniania za pomocą rejestru kontenerów platformy Azure, z których każdy ma zastosowanie do jednego lub więcej scenariuszy użycia rejestru.

Zalecane sposoby obejmują uwierzytelnianie w rejestrze bezpośrednio za pomocą [indywidualnego logowania](#individual-login-with-azure-ad)lub aplikacje i koordynatorzy kontenerów mogą wykonywać uwierzytelnianie bez nadzoru lub "bez głowy" przy użyciu [jednostki usługi](#service-principal)Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Opcje uwierzytelniania

W poniższej tabeli wymieniono dostępne metody uwierzytelniania i zalecane scenariusze. Szczegółowe informacje można znaleźć w połączonej zawartości.

| Metoda                               | Jak uwierzytelnić                                           | Scenariusze                                                            | Kontrola dostępu oparta na rolach                             | Ograniczenia                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Indywidualna tożsamość ad](#individual-login-with-azure-ad)                | `az acr login` w usłudze Azure CLI                             | Interaktywne push /pull przez programistów, testerów                                    | Tak                              | Token usługi AD musi być odnawiany co 3 godziny     |
| [Podmiot obsługujący usługę AD](#service-principal)                  | `docker login`<br/><br/>`az acr login`w usłudze Azure CLI<br/><br/> Ustawienia logowania rejestru w interfejsach API lub narzędziach<br/><br/> [Kubernetes wyciągnąć tajemnicę](container-registry-auth-kubernetes.md)                                           | Wypychanie bez nadzoru z potoku ciągłej integracji/ciągłego wdrażania<br/><br/> Pobieranie nienadzorowane na platformę Azure lub usługi zewnętrzne  | Tak                              | Domyślny wygaśnięcie hasła SP wynosi 1 rok       |                                                           
| [Integracja z AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Dołączanie rejestru podczas tworzenia lub aktualizowania klastra AKS  | Ściąganie nienadzorowane do klastra AKS                                                  | Nie, tylko dostęp ściągany             | Dostępne tylko w klastrze AKS            |
| [Tożsamość zarządzana dla zasobów platformy Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` w usłudze Azure CLI                                       | Wypychanie nienadzorowane z potoku ciągłej integracji/dysku CD platformy Azure<br/><br/> Ściąganie nienadzorowane do usług platformy Azure<br/><br/>   | Tak                              | Używaj tylko z usług platformy Azure [obsługujących tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Użytkownik administracyjny](#admin-account)                            | `docker login`                                          | Interaktywne wypychanie/ciągnięcie przez indywidualnego dewelopera lub testera                           | Nie, zawsze ściągaj i naciskaj dostęp  | Pojedyncze konto na rejestr, nie zalecane dla wielu użytkowników         |
| [Token dostępu o zakresie repozytorium](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`w usłudze Azure CLI   | Interaktywne wypychanie/ściąganie do repozytorium przez indywidualnego dewelopera lub testera<br/><br/> Nienadzorowane wypychanie/ściąganie do repozytorium przez indywidualny system lub urządzenie zewnętrzne                  | Tak                              | Obecnie nie jest zintegrowany z tożsamością usługi AD  |

## <a name="individual-login-with-azure-ad"></a>Indywidualne logowanie za pomocą usługi Azure AD

Podczas bezpośredniej pracy z rejestrem, na przykład ściągania obrazów do i wypychania obrazów ze stacji roboczej dewelopera, uwierzytelnij się za pomocą polecenia [logowania az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-login) w [interfejsie wiersza polecenia platformy Azure:](/cli/azure/install-azure-cli)

```azurecli
az acr login --name <acrName>
```

Po zalogowaniu `az acr login`się za pomocą interfejsu wiersza polecenia używa tokenu utworzonego podczas wykonywania [logowania az,](/cli/azure/reference-index#az-login) aby bezproblemowo uwierzytelnić sesję w rejestrze. Aby zakończyć przepływ uwierzytelniania, program Docker musi być zainstalowany i uruchomiony w twoim środowisku. `az acr login`Używa klienta platformy Docker, aby ustawić token `docker.config` usługi Azure Active Directory w pliku. Po zalogowaniu się w ten sposób poświadczenia są buforowane, a kolejne `docker` polecenia w sesji nie wymagają nazwy użytkownika ani hasła.

> [!TIP]
> Służy `az acr login` również do uwierzytelniania tożsamości indywidualnej, gdy chcesz wypychać lub pobierać artefakty inne niż obrazy platformy Docker do rejestru, takie jak [artefakty OCI](container-registry-oci-artifacts.md).  


W przypadku dostępu do `az acr login` rejestru token używany przez ten token jest ważny przez **3 godziny,** dlatego zaleca się, aby zawsze logować się do rejestru przed uruchomieniem `docker` polecenia. Jeśli token wygaśnie, można go `az acr login` odświeżyć za pomocą polecenia ponownie uwierzytelnić. 

Korzystanie `az acr login` z tożsamości platformy Azure zapewnia [dostęp oparty na rolach.](../role-based-access-control/role-assignments-portal.md) W niektórych scenariuszach można zalogować się do rejestru przy użyciu własnej tożsamości w usłudze Azure AD. W przypadku scenariuszy międzyusługowych lub obsługi potrzeb grupy roboczej lub przepływu pracy deweloperskiego, w którym nie chcesz zarządzać indywidualnym dostępem, można również zalogować się przy [użyciu tożsamości zarządzanej dla zasobów platformy Azure.](container-registry-authentication-managed-identity.md)

## <a name="service-principal"></a>Jednostka usługi

Jeśli do rejestru zostanie [przypisana jednostka usługi,](../active-directory/develop/app-objects-and-service-principals.md) aplikacja lub usługa może używać go do uwierzytelniania bezgłowego. Jednostki usługi zezwalają na dostęp oparty [na rolach](../role-based-access-control/role-assignments-portal.md) do rejestru i można przypisać wiele podmiotów usługi do rejestru. Wiele podmiotów usługi umożliwiają definiowanie innego dostępu dla różnych aplikacji.

Dostępne role rejestru kontenerów obejmują:

* **AcrPull**: ciągnąć

* **AcrPush**: ciągnąć i pchać

* **Właściciel**: ściąganie, wypychanie i przypisywanie ról innym użytkownikom

Aby uzyskać pełną listę ról, zobacz [Role i uprawnienia rejestru kontenerów platformy Azure](container-registry-roles.md).

Aby skrypty interfejsu wiersza polecenia utworzyć jednostkę usługi do uwierzytelniania za pomocą rejestru kontenerów platformy Azure i więcej wskazówek, zobacz [uwierzytelnianie rejestru kontenerów platformy Azure z jednostkami usługi.](container-registry-auth-service-principal.md)

## <a name="admin-account"></a>Konto administratora

Każdy rejestr kontenerów zawiera konto użytkownika administratora, które jest domyślnie wyłączone. Użytkownik administratora i zarządzanie jego poświadczeniami można włączyć w witrynie Azure portal lub za pomocą interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure.

> [!IMPORTANT]
> Konto administratora jest przeznaczone dla pojedynczego użytkownika, aby uzyskać dostęp do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora wielu użytkownikom. Wszyscy użytkownicy uwierzytelniający się za pomocą konta administratora są wyświetlane jako pojedynczy użytkownik z wypychaniem i ściąganiem dostępu do rejestru. Zmiana lub wyłączenie tego konta powoduje wyłączenie dostępu do rejestru dla wszystkich użytkowników korzystających z jego poświadczeń. Indywidualna tożsamość jest zalecana dla użytkowników i podmiotów usługi dla scenariuszy bezgłowy.
>

Konto administratora jest wyposażone w dwa hasła, z których oba można ponownie wygenerować. Dwa hasła umożliwiają utrzymanie połączenia z rejestrem przy użyciu jednego hasła podczas ponownego generowania drugiego. Jeśli konto administratora jest włączone, możesz przekazać nazwę użytkownika `docker login` i hasło do polecenia po wyświetleniu monitu o uwierzytelnienie podstawowe do rejestru. Przykład:

```
docker login myregistry.azurecr.io 
```

Aby uzyskać najlepsze rozwiązania dotyczące zarządzania poświadczeniami logowania, zobacz odwołanie do polecenia [logowania docker.](https://docs.docker.com/engine/reference/commandline/login/)

Aby włączyć użytkownika administratora dla istniejącego `--admin-enabled` rejestru, można użyć parametru [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) polecenia w interfejsie wiersza polecenia platformy Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Użytkownik administratora w portalu Azure można włączyć, nawigując po rejestrze, wybierając **pozycję Klawisze dostępu** w obszarze **USTAWIENIA**, a następnie **Włącz** w obszarze **Użytkownik administratora**.

![Włączanie interfejsu użytkownika administratora w witrynie Azure portal][auth-portal-01]

## <a name="next-steps"></a>Następne kroki

* [Wypychanie pierwszego obrazu przy użyciu interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png

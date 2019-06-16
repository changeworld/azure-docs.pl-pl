---
title: Uwierzytelnianie za pomocą usługi Azure container registry
description: Opcje uwierzytelniania dla usługi Azure container registry, takich jak logowanie się przy użyciu tożsamości usługi Azure Active Directory, za pomocą jednostki usługi i przy użyciu poświadczeń administratora opcjonalne.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9682b9b832a8fd7374cb84e8cc6faad69df15945
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61333881"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Uwierzytelnianie przy użyciu prywatnego rejestru kontenerów platformy Docker

Istnieje kilka sposobów uwierzytelniania za pomocą usługi Azure container registry, z których każdy ma zastosowanie do jednego lub więcej scenariuszy użycia rejestru.

Możesz zalogować się do rejestru bezpośrednio za pośrednictwem [poszczególnych logowania](#individual-login-with-azure-ad), lub aplikacje i koordynatorów kontenerów, można wykonać instalacji nienadzorowanej lub "bezobsługowe" uwierzytelnianie przy użyciu usługi Azure Active Directory (Azure AD) [ Nazwa główna usługi](#service-principal).

## <a name="individual-login-with-azure-ad"></a>Poszczególne Zaloguj się przy użyciu usługi Azure AD

Podczas pracy z rejestrem bezpośrednio, takie jak ściąganie obrazów i wypychający z deweloperskiej stacji roboczej, Uwierzytelnij się przy użyciu [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) polecenia w pliku [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Po zalogowaniu się przy użyciu `az acr login`, token utworzony, jeśli został wykonany korzysta z interfejsu wiersza polecenia [az login](/cli/azure/reference-index#az-login) bezproblemowe uwierzytelnianie sesji z rejestrem. Gdy logujesz się w ten sposób, poświadczenia są buforowane i kolejne `docker` polecenia w sesji nie wymagają nazwy użytkownika i hasła. 

Aby uzyskać dostęp do rejestru, token używany przez `az acr login` nadaje się do 1 godziny, tak więc zaleca się, że należy zawsze zalogować się do rejestru przed uruchomieniem `docker` polecenia. Token wygasa, można odświeżyć go za pomocą `az acr login` polecenie ponownie, aby ponownie uwierzytelniać. 

Za pomocą `az acr login` tożsamości platformy Azure zapewnia [dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md). W niektórych scenariuszach możesz zalogować się do rejestru za pomocą indywidualne tożsamości w usłudze Azure AD. W scenariuszach między usługami lub aby sprostać wymaganiom grupy roboczej, w których nie chcesz zarządzać dostępem do poszczególnych możesz również zalogować się przy użyciu [tożsamości zarządzanej dla zasobów platformy Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Nazwa główna usługi

Jeśli przypiszesz [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md) do rejestru, Twoja aplikacja lub usługa może być użyty do bezobsługowego uwierzytelniania. Zezwalaj na nazwy główne usług [dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md) do rejestru, i może przypisywać wiele jednostek usługi do rejestru. Wiele jednostek usługi umożliwiają definiowanie różny dostęp do różnych aplikacji.

Dostępne role dla rejestru kontenerów uwzględnić:

* **AcrPull**: ściągania

* **AcrPush**: ściągać i wypychać

* **Właściciel**: ściągać, wypychać i przypisać role do innych użytkowników

Aby uzyskać pełną listę ról, zobacz [uprawnienia i role usługi Azure Container Registry](container-registry-roles.md).

Aby uzyskać skryptami interfejsu wiersza polecenia, aby utworzyć identyfikator aplikacji nazwy głównej usługi i hasło do uwierzytelniania za pomocą usługi Azure container registry lub użyć istniejącej jednostki usługi, zobacz [uwierzytelniania usługi Azure Container Registry za pomocą jednostki usługi](container-registry-auth-service-principal.md).

Nazwy główne usług włączyć bezobsługowego łączność z rejestru w scenariuszach zarówno ściąganie i wypychanie, jak pokazano poniżej:

  * *Ściągnij*: Wdrażanie kontenerów z rejestru, aby systemy organizowania, w tym Kubernetes, DC/OS i Docker Swarm. Możesz również ściągnąć z rejestrów kontenerów pokrewne usługi platformy takie jak [usługi Azure Kubernetes Service](container-registry-auth-aks.md), [usługi Azure Container Instances](container-registry-auth-aci.md), [usługi App Service](../app-service/index.yml), [Partii](../batch/index.yml), [usługi Service Fabric](/azure/service-fabric/)i innym osobom.

  * *Wypychanie*: Kompilowanie obrazów kontenerów i odesłać je do rejestru za pomocą ciągłej rozwiązań integracji i ciągłego wdrażania, takie jak potoki usługi Azure lub usługi Jenkins.

Możesz również zalogować się bezpośrednio za pomocą jednostki usługi. Po uruchomieniu następującego polecenia, podaj interaktywnie appID nazwy głównej usługi (nazwa użytkownika) i hasło po wyświetleniu monitu. Aby uzyskać najlepsze rozwiązania do zarządzania poświadczeń logowania, zobacz [docker login](https://docs.docker.com/engine/reference/commandline/login/) poleceń:

```
docker login myregistry.azurecr.io
```

Po zalogowaniu Docker buforuje poświadczeń, więc nie trzeba pamiętać identyfikator aplikacji.

> [!TIP]
> Można ponownie wygenerować hasło jednostki usługi, uruchamiając [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest) polecenia.
>

## <a name="admin-account"></a>Konto administratora

Każdego rejestru kontenera obejmuje konta administratora, który jest domyślnie wyłączona. Można włączyć użytkownika administracyjnego i zarządzać jego poświadczeń w witrynie Azure portal lub przy użyciu wiersza polecenia platformy Azure lub innych narzędziach platformy Azure.

> [!IMPORTANT]
> Konto administratora jest przeznaczony dla jednego użytkownika, dostęp do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora z wieloma użytkownikami. Wszyscy użytkownicy uwierzytelniania przy użyciu konta administratora są traktowane jako pojedynczego użytkownika za pomocą wypychania i ściągania dostępu do rejestru. Zmiana lub wyłączanie tego konta powoduje wyłączenie dostępu do rejestru dla wszystkich użytkowników, którzy korzystają z jego poświadczeń. Indywidualne tożsamości jest zalecana dla użytkowników i nazwy główne usług dla bezobsługowego scenariuszy.
>

Konto administratora jest udostępniane z dwa hasła, które może być generowany ponownie. Dwa hasła pozwala utrzymać połączenia w rejestrze za pomocą jednego hasła podczas ponownego generowania drugiego. Jeśli konto administratora jest włączona, można przekazać nazwę użytkownika i hasło albo `docker login` polecenia, gdy zostanie wyświetlony monit o uwierzytelnianie podstawowe w rejestrze. Na przykład:

```
docker login myregistry.azurecr.io 
```


Aby włączyć użytkownika administratora dla istniejącego rejestru, można użyć `--admin-enabled` parametru [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) polecenia w interfejsie wiersza polecenia platformy Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Można włączyć użytkownika administratora w witrynie Azure portal, przechodząc do rejestru, wybierając **klucze dostępu** w obszarze **ustawienia**, następnie **Włącz** w obszarze **administratora Użytkownik**.

![Włączanie użytkownika administratora interfejsu użytkownika w witrynie Azure portal][auth-portal-01]

## <a name="next-steps"></a>Kolejne kroki

* [Wypchnij swój pierwszy obraz przy użyciu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png

---
title: Uwierzytelnianie przy użyciu usługi Azure Container Registry
description: Opcje uwierzytelniania dla usługi Azure Container Registry, w tym logowanie za pomocą tożsamości Azure Active Directory, korzystanie z nazw głównych usługi i używanie opcjonalnych poświadczeń administratora.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 82fe80e098ee95c09c4a1400068ab813910e0e1a
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309830"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Uwierzytelnianie za pomocą prywatnego rejestru kontenerów platformy Docker

Istnieje kilka sposobów uwierzytelniania przy użyciu usługi Azure Container Registry, z których każdy ma zastosowanie do co najmniej jednego scenariusza użycia rejestru.

Możesz zalogować się do rejestru bezpośrednio przy użyciu [poszczególnych logowań](#individual-login-with-azure-ad), a aplikacje i Koordynatory kontenerów mogą przeprowadzić nienadzorowane lub bezobsługowe uwierzytelnianie przy użyciu [nazwy głównej usługi](#service-principal)Azure Active Directory (Azure AD).

## <a name="individual-login-with-azure-ad"></a>Indywidualne logowanie za pomocą usługi Azure AD

W przypadku bezpośredniej pracy z rejestrem, na przykład ściągania obrazów do i wypychania obrazów z stacji roboczej deweloperskiej, należy uwierzytelnić się za pomocą polecenia [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) w [interfejsie wiersza poleceń platformy Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Gdy logujesz się za `az acr login`pomocą programu, interfejs wiersza polecenia używa tokenu utworzonego, gdy wykonasz [AZ login](/cli/azure/reference-index#az-login) , aby bezproblemowo uwierzytelniać sesję z rejestrem. Po zalogowaniu się w ten sposób poświadczenia są buforowane, a kolejne `docker` polecenia w sesji nie wymagają nazwy użytkownika ani hasła. 

W przypadku dostępu do rejestru token używany przez `az acr login` program jest ważny przez 1 godzinę, dlatego zalecamy, aby zawsze logować się do rejestru przed `docker` uruchomieniem polecenia. Jeśli token wygaśnie, możesz go odświeżyć za pomocą `az acr login` polecenia ponownie, aby przeprowadzić ponowną próbę uwierzytelnienia. 

Używanie `az acr login` z tożsamościami platformy Azure zapewnia [dostęp oparty na rolach](../role-based-access-control/role-assignments-portal.md). W przypadku niektórych scenariuszy możesz zalogować się do rejestru przy użyciu indywidualnej tożsamości w usłudze Azure AD. W przypadku scenariuszy obejmujących wiele usług lub do obsługi potrzeb grupy roboczej, w której nie chcesz zarządzać indywidualnym dostępem, możesz również zalogować się za pomocą [tożsamości zarządzanej dla zasobów platformy Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Nazwa główna usługi

Jeśli przypiszesz jednostkę [usługi](../active-directory/develop/app-objects-and-service-principals.md) do rejestru, aplikacja lub usługa może jej używać do bezobsługowego uwierzytelniania. Jednostki usług umożliwiają [dostęp oparty na rolach](../role-based-access-control/role-assignments-portal.md) do rejestru i można przypisać wiele jednostek usługi do rejestru. Wiele jednostek usługi pozwala definiować różne prawa dostępu dla różnych aplikacji.

Dostępne role dla rejestru kontenerów obejmują:

* **AcrPull**: ściąganie

* **AcrPush**: ściąganie i wypychanie

* **Właściciel**: ściąganie, wypychanie i przypisywanie ról innym użytkownikom

Aby uzyskać pełną listę ról, zobacz [Azure Container Registry ról i uprawnień](container-registry-roles.md).

Skrypty interfejsu wiersza polecenia do tworzenia identyfikatora aplikacji głównej usługi i hasła do uwierzytelniania za pomocą usługi Azure Container Registry lub do korzystania z istniejącej jednostki usługi znajdują się w temacie [Azure Container Registry Authentication with Service Principals](container-registry-auth-service-principal.md).

Nazwy główne usług umożliwiają bezobsługowe łączenie z rejestrem w scenariuszach ściągania i wypychania, takich jak następujące:

  * *Ściąganie*: Wdrażaj kontenery z rejestru w systemach aranżacji, takich jak Kubernetes, DC/OS i Docker Swarm. Możesz również ściągnąć z rejestrów kontenerów do pokrewnych usług platformy Azure, takich jak [Azure Kubernetes Service](container-registry-auth-aks.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)i innych.

  * *Wypchnij*: Tworzenie obrazów kontenerów i wypychanie ich do rejestru przy użyciu rozwiązań ciągłej integracji i wdrażania, takich jak Azure Pipelines lub Jenkins.

Możesz też zalogować się bezpośrednio przy użyciu nazwy głównej usługi. Po uruchomieniu poniższego polecenia, interaktywnie Podaj nazwę (username) jednostki usługi i hasło po wyświetleniu monitu. Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi zarządzania poświadczeniami logowania, zobacz informacje dotyczące poleceń [logowania Docker](https://docs.docker.com/engine/reference/commandline/login/) :

```
docker login myregistry.azurecr.io
```

Po zalogowaniu się program Docker buforuje poświadczenia, więc nie trzeba pamiętać identyfikatora aplikacji.

> [!TIP]
> Możesz ponownie wygenerować hasło jednostki usługi, uruchamiając polecenie [AZ AD Sp Reset-Credentials](/cli/azure/ad/sp?view=azure-cli-latest) .
>

## <a name="admin-account"></a>Konto administratora

Każdy rejestr kontenerów zawiera konto użytkownika administratora, które jest domyślnie wyłączone. Możesz włączyć administratora i zarządzać jego poświadczeniami w Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure.

> [!IMPORTANT]
> Konto administratora jest przeznaczone dla jednego użytkownika, aby uzyskać dostęp do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora wielu użytkownikom. Wszyscy użytkownicy uwierzytelniani przy użyciu konta administratora są wyświetlani jako pojedynczy użytkownik z dostępem wypychanym i ściąganiem do rejestru. Zmiana lub wyłączenie tego konta powoduje wyłączenie dostępu do rejestru dla wszystkich użytkowników korzystających z jego poświadczeń. Indywidualna tożsamość jest zalecana dla użytkowników i jednostek usługi dla scenariuszy bezobsługowych.
>

Konto administratora jest dostarczane z dwoma hasłami, które mogą być ponownie generowane. Dwa hasła umożliwiają zachowanie połączenia z rejestrem przy użyciu jednego hasła podczas ponownego generowania drugiego. Jeśli konto administratora jest włączone, można przekazać nazwę użytkownika i hasło do `docker login` polecenia po wyświetleniu monitu o uwierzytelnienie podstawowe w rejestrze. Na przykład:

```
docker login myregistry.azurecr.io 
```


Aby umożliwić administratorowi istniejący rejestr, można użyć `--admin-enabled` parametru [AZ ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) w interfejsie wiersza polecenia platformy Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Możesz włączyć administratora w Azure Portal, przechodząc do rejestru, wybierając pozycję **klucze dostępu** w obszarze **Ustawienia**, a następnie pozycję **Włącz** w obszarze **administrator**.

![Włącz interfejs użytkownika administratora w Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Następne kroki

* [Wypchnij swój pierwszy obraz przy użyciu interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png

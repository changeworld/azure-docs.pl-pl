---
title: Opcje uwierzytelniania rejestru
description: Opcje uwierzytelniania dla usługi Azure Container Registry, w tym logowanie za pomocą tożsamości Azure Active Directory, korzystanie z nazw głównych usługi i używanie opcjonalnych poświadczeń administratora.
ms.topic: article
ms.date: 12/21/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 29e23f6a983ccc2197e609511aee2ce13726ed0f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455392"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Uwierzytelnianie za pomocą prywatnego rejestru kontenerów platformy Docker

Istnieje kilka sposobów uwierzytelniania przy użyciu usługi Azure Container Registry, z których każdy ma zastosowanie do co najmniej jednego scenariusza użycia rejestru.

Zalecane sposoby: uwierzytelnianie do rejestru bezpośrednio za pośrednictwem [pojedynczej nazwy logowania](#individual-login-with-azure-ad)lub aplikacje oraz koordynatorów kontenerów mogą wykonywać nienadzorowane lub bezobsługowe uwierzytelnianie przy użyciu [nazwy głównej usługi](#service-principal)Azure Active Directory (Azure AD).

## <a name="individual-login-with-azure-ad"></a>Indywidualne logowanie za pomocą usługi Azure AD

W przypadku bezpośredniej pracy z rejestrem, na przykład ściągania obrazów do i wypychania obrazów z stacji roboczej deweloperskiej, należy uwierzytelnić się za pomocą polecenia [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) w [interfejsie wiersza poleceń platformy Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Gdy logujesz się za pomocą `az acr login`, interfejs wiersza polecenia używa tokenu utworzonego podczas wykonywania [AZ login](/cli/azure/reference-index#az-login) , aby bezproblemowo uwierzytelniać sesję w rejestrze. Po zalogowaniu się w ten sposób poświadczenia są buforowane, a kolejne `docker` polecenia w sesji nie wymagają nazwy użytkownika ani hasła. 

W przypadku dostępu do rejestru token używany przez `az acr login` jest ważny przez 1 godzinę, dlatego zalecamy, aby zawsze logować się do rejestru przed uruchomieniem polecenia `docker`. Jeśli token wygaśnie, można go odświeżyć za pomocą polecenia `az acr login` ponownie w celu ponownego uwierzytelnienia. 

Używanie `az acr login` z tożsamościami platformy Azure zapewnia [dostęp oparty na rolach](../role-based-access-control/role-assignments-portal.md). W przypadku niektórych scenariuszy możesz zalogować się do rejestru przy użyciu indywidualnej tożsamości w usłudze Azure AD. W przypadku scenariuszy obejmujących wiele usług lub do obsługi potrzeb grupy roboczej, w której nie chcesz zarządzać indywidualnym dostępem, możesz również zalogować się za pomocą [tożsamości zarządzanej dla zasobów platformy Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Nazwa główna usługi

Jeśli przypiszesz jednostkę [usługi](../active-directory/develop/app-objects-and-service-principals.md) do rejestru, aplikacja lub usługa może jej używać do bezobsługowego uwierzytelniania. Jednostki usług umożliwiają [dostęp oparty na rolach](../role-based-access-control/role-assignments-portal.md) do rejestru i można przypisać wiele jednostek usługi do rejestru. Wiele jednostek usługi pozwala definiować różne prawa dostępu dla różnych aplikacji.

Dostępne role dla rejestru kontenerów obejmują:

* **AcrPull**: ściąganie

* **AcrPush**: ściąganie i wypychanie

* **Właściciel**: ściąganie, wypychanie i przypisywanie ról innym użytkownikom

Aby uzyskać pełną listę ról, zobacz [Azure Container Registry ról i uprawnień](container-registry-roles.md).

Skrypty interfejsu wiersza polecenia do tworzenia jednostki usługi do uwierzytelniania przy użyciu usługi Azure Container Registry i wskazówki dotyczące korzystania z nazwy głównej usługi znajdują się w temacie [Azure Container Registry Authentication with Service Principals](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Konto administratora

Każdy rejestr kontenerów zawiera konto użytkownika administratora, które jest domyślnie wyłączone. Możesz włączyć administratora i zarządzać jego poświadczeniami w Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure.

> [!IMPORTANT]
> Konto administratora jest przeznaczone dla jednego użytkownika, aby uzyskać dostęp do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora między wieloma użytkownikami. Wszyscy użytkownicy uwierzytelniani przy użyciu konta administratora są wyświetlani jako pojedynczy użytkownik z dostępem wypychanym i ściąganiem do rejestru. Zmiana lub wyłączenie tego konta powoduje wyłączenie dostępu do rejestru dla wszystkich użytkowników korzystających z jego poświadczeń. Indywidualna tożsamość jest zalecana dla użytkowników i jednostek usługi dla scenariuszy bezobsługowych.
>

Konto administratora jest dostarczane z dwoma hasłami, które mogą być ponownie generowane. Dwa hasła umożliwiają zachowanie połączenia z rejestrem przy użyciu jednego hasła podczas ponownego generowania drugiego. Jeśli konto administratora jest włączone, można przekazać nazwę użytkownika i hasło do polecenia `docker login` po wyświetleniu monitu o uwierzytelnienie podstawowe w rejestrze. Na przykład:

```
docker login myregistry.azurecr.io 
```

Najlepsze rozwiązania dotyczące zarządzania poświadczeniami logowania można znaleźć w dokumentacji polecenia [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

Aby umożliwić administratorowi istniejący rejestr, można użyć `--admin-enabled` parametru polecenia [AZ ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) w interfejsie użytkownika platformy Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Możesz włączyć administratora w Azure Portal, przechodząc do rejestru, wybierając pozycję **klucze dostępu** w obszarze **Ustawienia**, a następnie pozycję **Włącz** w obszarze **administrator**.

![Włącz interfejs użytkownika administratora w Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Następne kroki

* [Wypchnij swój pierwszy obraz przy użyciu interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png

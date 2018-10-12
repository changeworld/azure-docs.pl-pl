---
title: Uwierzytelnianie za pomocą usługi Azure container registry
description: Opcje uwierzytelniania dla usługi Azure container registry, w tym usługi Active Directory usługi podmiotów zabezpieczeń logowania bezpośrednie i rejestru.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c0c2323d1864be24edbf6005d634ae1d08bba8ea
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116610"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Uwierzytelnianie przy użyciu prywatnego rejestru kontenerów platformy Docker

Istnieje kilka sposobów uwierzytelniania za pomocą usługi Azure container registry, z których każdy ma zastosowanie do jednego lub więcej scenariuszy użycia rejestru.

Możesz zalogować się do rejestru bezpośrednio za pośrednictwem [poszczególnych logowania](#individual-login-with-azure-ad), oraz aplikacje i koordynatorów kontenerów, można wykonać instalacji nienadzorowanej lub "bezobsługowe" uwierzytelnianie przy użyciu usługi Azure Active Directory (Azure AD) [ Nazwa główna usługi](#service-principal).

Usługa Azure Container Registry nie obsługuje nieuwierzytelnione operacje platformy Docker lub dostęp anonimowy. Obrazy publiczne, możesz użyć [usługi Docker Hub](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Poszczególne Zaloguj się przy użyciu usługi Azure AD

Podczas pracy z rejestrem bezpośrednio, takie jak ściąganie obrazów i wypychający z deweloperskiej stacji roboczej, Uwierzytelnij się przy użyciu [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) polecenia w pliku [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Po zalogowaniu się przy użyciu `az acr login`, token utworzony, jeśli został wykonany korzysta z interfejsu wiersza polecenia `az login` bezproblemowe uwierzytelnianie sesji z rejestrem. Gdy logujesz się w ten sposób, poświadczenia są buforowane i kolejne `docker` polecenia nie wymagają nazwy użytkownika i hasła. Token wygasa, można odświeżyć go za pomocą `az acr login` polecenie ponownie, aby ponownie uwierzytelniać. Za pomocą `az acr login` tożsamości platformy Azure zapewnia [dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md).

## <a name="service-principal"></a>Jednostka usługi

Możesz przypisać [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md) do rejestru, a Twoja aplikacja lub usługa może używać go do bezobsługowego uwierzytelniania. Zezwalaj na nazwy główne usług [dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md) do rejestru, i może przypisywać wiele jednostek usługi do rejestru. Wiele jednostek usługi umożliwiają definiowanie różny dostęp do różnych aplikacji.

Dostępne role to:

  * **Czytnik**: ściągania
  * **Współautor**: ściągać i wypychać
  * **Właściciel**: ściągać, wypychać i przypisać role do innych użytkowników

Nazwy główne usług włączyć bezobsługowego łączność z rejestru w scenariuszach wypychania i ściągania, jak pokazano poniżej:

  * *Czytnik*: wdrożeń kontenera z rejestru do aranżacji systemy, w tym Kubernetes, DC/OS i Docker Swarm. Możesz również ściągnąć z rejestrów kontenerów pokrewne usługi platformy takie jak [AKS](../aks/index.yml), [usługi App Service](../app-service/index.yml), [partii](../batch/index.yml), [usługi Service Fabric](/azure/service-fabric/), i inne osoby.

  * *Współautor*: ciągłej integracji i ciągłego wdrażania rozwiązania, takie jak potoki usługi Azure lub usługi Jenkins, który kompilowanie obrazów kontenerów i odesłać je do rejestru.

> [!TIP]
> Można ponownie wygenerować hasło jednostki usługi, uruchamiając [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-reset-credentials) polecenia.
>

Możesz również zalogować się bezpośrednio za pomocą jednostki usługi. Podaj identyfikator aplikacji i hasło jednostki do usługi `docker login` polecenia:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Po zalogowaniu Docker buforuje poświadczeń, więc nie trzeba pamiętać identyfikator aplikacji.

W zależności od wersji platformy Docker został zainstalowany, może być wyświetlone ostrzeżenie o zabezpieczeniach zalecające użycie `--password-stdin` parametru. Użycie tego parametru wykracza poza zakres tego artykułu, jednak zalecamy zastosowanie tego najlepszego rozwiązania. Aby uzyskać więcej informacji, zobacz [docker login](https://docs.docker.com/engine/reference/commandline/login/) poleceń.

Aby uzyskać więcej informacji na temat bezobsługowego uwierzytelniania do usługi ACR przy użyciu nazwy głównej usługi, zobacz [uwierzytelniania usługi Azure Container Registry za pomocą jednostki usługi](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Konto administratora

Każdego rejestru kontenera obejmuje konta administratora, który jest domyślnie wyłączona. Można włączyć użytkownika administracyjnego i zarządzać jego poświadczeń w [witryny Azure portal](container-registry-get-started-portal.md#create-a-container-registry), lub za pomocą wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Konto administratora jest przeznaczony dla jednego użytkownika, dostęp do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora z wieloma użytkownikami. Wszyscy użytkownicy uwierzytelniania przy użyciu konta administratora są traktowane jako pojedynczego użytkownika za pomocą wypychania i ściągania dostępu do rejestru. Zmiana lub wyłączanie tego konta powoduje wyłączenie dostępu do rejestru dla wszystkich użytkowników, którzy korzystają z jego poświadczeń. Indywidualne tożsamości jest zalecana dla użytkowników i nazwy główne usług dla bezobsługowego scenariuszy.
>

Konto administratora jest udostępniane z dwa hasła, które może być generowany ponownie. Dwa hasła pozwala utrzymać połączenia w rejestrze za pomocą jednego hasła podczas ponownego generowania drugiego. Jeśli konto administratora jest włączona, można przekazać nazwę użytkownika i hasło albo `docker login` polecenia dla uwierzytelniania podstawowego w rejestrze. Na przykład:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Ponownie Docker zaleca używanie `--password-stdin` parametru zamiast podawania go w wierszu polecenia w celu zwiększenia bezpieczeństwa. Można również określić tylko swoją nazwę użytkownika, bez `-p`, a następnie wprowadź hasło po wyświetleniu monitu.

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

---
title: Uwierzytelniania za pomocą rejestru kontenera platformy Azure
description: Opcje uwierzytelniania dla rejestru kontenera platformy Azure, w tym usługi Azure Active Directory usługi podmiotów bezpośrednio i rejestru do logowania.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8fcebae21d73db75e19bd1091faa8f389f0ba40
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165521"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Uwierzytelniania za pomocą prywatnego rejestru kontenera Docker

Istnieje kilka sposobów uwierzytelniania za pomocą rejestru kontenera platformy Azure, z których każdy ma zastosowanie do co najmniej jeden scenariusze użycia rejestru.

Możesz zalogować się w rejestrze bezpośrednio za pomocą [poszczególnych logowania](#individual-login-with-azure-ad), a aplikacje i orchestrators kontener może wykonywać uwierzytelnianie bez nadzoru lub "bezobsługowe" za pomocą usługi Azure Active Directory (Azure AD) [ nazwy głównej usługi](#service-principal).

Rejestru kontenera platformy Azure nie obsługuje nieuwierzytelnione operacje Docker lub dostęp anonimowy. Publiczny obrazów, można użyć [Centrum Docker](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Poszczególne Zaloguj się za pomocą usługi Azure AD

Podczas pracy z rejestrem bezpośrednio, takie jak ściąganie obrazów i wypychanie obrazów z deweloperskiej stacji roboczej, Uwierzytelnij się przy użyciu [logowania acr az](/cli/azure/acr?view=azure-cli-latest#az_acr_login) w [interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Podczas logowania przy użyciu `az acr login`, interfejsu wiersza polecenia używa tokenu tworzone podczas wykonania `az login` bezproblemowe uwierzytelnianie sesji z rejestru. Po logujesz się w ten sposób, poświadczenia są buforowane, a kolejne `docker` polecenia nie wymagają nazwy użytkownika i hasła. Jeśli token wygaśnie, można go odświeżyć przy użyciu `az acr login` polecenia ponownie w celu ponownego uwierzytelnienia. Przy użyciu `az acr login` z tożsamościami Azure udostępnia [dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md).

## <a name="service-principal"></a>Jednostka usługi

Można przypisać [nazwy głównej usługi](../active-directory/develop/active-directory-application-objects.md) do rejestru, a usługi lub aplikacji może być używany do uwierzytelniania bezobsługowe. Zezwalaj na nazwy główne usług [dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md) do rejestru, i wiele podmiotów usługi można przypisać do rejestru. Wiele podmiotów usługi umożliwiają definiowanie różnych dostępu dla różnych aplikacji.

Dostępne role są:

  * **Czytnik**: ściągania
  * **Współautor**: ściągania i wypychania
  * **Właściciel**: ściągnięcia, wypychania i przypisać role do innych użytkowników

Nazwy główne usług umożliwić bezobsługowe łączność z rejestru w scenariuszach wypychania i ściągania podobne do poniższych:

  * *Czytnik*: wdrożenia kontenera z rejestru na systemy orchestration, w tym Kubernetes DC/OS i Docker Swarm. Można również umieścić z rejestrów kontenera do powiązanych usług platformy Azure takich jak [AKS](../aks/index.yml), [usługi aplikacji](../app-service/index.yml), [partii](../batch/index.yml), [sieci szkieletowej usług](/azure/service-fabric/), i inne osoby.

  * *Współautor*: ciągłej integracji i wdrażania rozwiązań, takich jak Visual Studio Team Services (VSTS) lub Wpięć tworzyć obrazy kontenera i wypychanie ich do rejestru.

> [!TIP]
> Hasło nazwy głównej usługi można ponownie wygenerować uruchamiając [sp ad az resetowania — poświadczenia](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_reset_credentials) polecenia.
>

Możesz także zalogować się bezpośrednio z nazwy głównej usługi. Podaj identyfikator aplikacji i hasło do głównej usługi `docker login` polecenia:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Po zalogowaniu, Docker buforuje poświadczeń, więc nie trzeba pamiętać identyfikator aplikacji.

W zależności od wersji Docker został zainstalowany, może być wyświetlone ostrzeżenie rekomendowania stosowania `--password-stdin` parametru. Użycie tego parametru wykracza poza zakres tego artykułu, jednak zalecamy zastosowanie tego najlepszego rozwiązania. Aby uzyskać więcej informacji, zobacz [logowania docker](https://docs.docker.com/engine/reference/commandline/login/) poleceń.

Aby uzyskać więcej informacji na przy użyciu nazwy głównej usługi uwierzytelniania bezobsługowe ACR, zobacz [uwierzytelniania rejestru kontenera Azure za pomocą nazwy główne usług](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Konto administratora

Rejestr każdego kontenera zawiera konto użytkownika administratora jest domyślnie wyłączona. Można włączyć dla użytkownika administracyjnego i zarządzać jego poświadczeń w [portalu Azure](container-registry-get-started-portal.md#create-a-container-registry), lub za pomocą wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Konto administratora jest przeznaczony dla jednego użytkownika do dostępu do rejestru, głównie do celów testowych. Nie zaleca się udostępniania wielu użytkownikom za pomocą poświadczeń konta administratora. Wszyscy użytkownicy uwierzytelniania przy użyciu konta administratora są wyświetlane jako pojedynczego użytkownika z dostępem do wypychania i ściągania w rejestrze. Zmiana lub wyłączenie tego konta spowoduje wyłączenie dostępu do rejestru dla wszystkich użytkowników, którzy użyć jego poświadczeń. Poszczególne tożsamości jest zalecana dla użytkowników i nazwy główne usług dla scenariuszy bezobsługowe.
>

Konto administratora jest udostępniane z dwóch haseł, które może być generowany ponownie. Dwa hasła umożliwiają zachowanie połączenia w rejestrze za pomocą jednego hasła podczas ponownego generowania drugiego. Jeśli konto administratora jest włączone, można przekazać nazwę użytkownika i hasło albo `docker login` polecenia dla uwierzytelniania podstawowego w rejestrze. Na przykład:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Ponownie Docker zaleca użycie `--password-stdin` parametru zamiast dostarczenie go w wierszu polecenia, aby zwiększyć bezpieczeństwo. Można również określić tylko nazwy użytkownika, bez `-p`, a następnie wprowadź hasło po wyświetleniu monitu.

Aby włączyć dla użytkownika administracyjnego dla istniejącego rejestru, można użyć `--admin-enabled` parametr [aktualizacji acr az](/cli/azure/acr?view=azure-cli-latest#az_acr_update) w wiersza polecenia platformy Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Można włączyć dla użytkownika administracyjnego, w portalu Azure, przechodząc rejestru, wybierając **klucze dostępu** w obszarze **ustawienia**, następnie **włączyć** w obszarze **administratora Użytkownik**.

![Włącz administrator interfejsu użytkownika w portalu Azure][auth-portal-01]

## <a name="next-steps"></a>Kolejne kroki

* [Wypychanie pierwszy obraz przy użyciu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png

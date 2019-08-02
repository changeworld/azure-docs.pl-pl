---
title: Używanie tożsamości zarządzanej z zadaniami Azure Container Registry
description: Włącz zarządzaną tożsamość zasobów platformy Azure w zadaniu Azure Container Registry, aby umożliwić zadanie dostępu do innych zasobów platformy Azure, w tym innych prywatnych rejestrów kontenerów.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: 9f7c083a079e42172a9e2865f90293fa4d6813d8
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640410"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Korzystanie z tożsamości zarządzanej przez platformę Azure w zadaniach ACR 

Włącz [zarządzaną tożsamość zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w zadaniu [ACR](container-registry-tasks-overview.md), aby zadanie miało dostęp do innych zasobów platformy Azure bez konieczności udostępniania poświadczeń ani zarządzania nimi. Na przykład użyj tożsamości zarządzanej, aby umożliwić krokowi zadania ściąganie lub wypychanie obrazów kontenera do innego rejestru.

W tym artykule dowiesz się, jak korzystać z interfejsu wiersza polecenia platformy Azure w celu włączenia zarządzanej tożsamości przypisanej przez użytkownika lub przypisanej do systemu w ramach zadania ACR. Możesz użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.68 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

Scenariusze umożliwiające dostęp do zabezpieczonych zasobów z zadania ACR przy użyciu tożsamości zarządzanej można znaleźć w temacie:

* [Uwierzytelnianie między rejestrami](container-registry-tasks-cross-registry-authentication.md)
* [Dostęp do zasobów zewnętrznych przy użyciu wpisów tajnych przechowywanych w Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Zarządzana tożsamość zasobów platformy Azure umożliwia wybranie usług platformy Azure z automatyczną tożsamością zarządzaną w Azure Active Directory (Azure AD). Zadanie ACR można skonfigurować przy użyciu tożsamości zarządzanej, aby zadanie miało dostęp do innych zabezpieczonych zasobów platformy Azure bez przekazywania poświadczeń w krokach zadania.

Zarządzane tożsamości są dwa typy:

* *Tożsamości przypisane przez użytkownika*, które można przypisać do wielu zasobów i utrzymywać je tak długo, jak chcesz. Tożsamości przypisane przez użytkownika są obecnie w wersji zapoznawczej.

* *Tożsamość przypisana przez system*, która jest unikatowa dla określonego zasobu, takiego jak zadanie ACR i obowiązuje w okresie istnienia tego zasobu.

W zadaniu ACR można włączyć albo oba typy tożsamości. Przyznaj tożsamości dostęp do innego zasobu, podobnie jak każdy podmiot zabezpieczeń. Gdy zadanie zostanie uruchomione, używa tożsamości w celu uzyskania dostępu do zasobu w dowolnym kroku zadania, który wymaga dostępu.

## <a name="steps-to-use-a-managed-identity"></a>Procedura używania tożsamości zarządzanej

Wykonaj te czynności wyższego poziomu, aby użyć zarządzanej tożsamości z zadaniem ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. Obowiązkowe Tworzenie tożsamości przypisanej do użytkownika

Jeśli planujesz użycie tożsamości przypisanej do użytkownika, możesz użyć istniejącej tożsamości. Lub Utwórz tożsamość przy użyciu interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure. Na przykład użyj polecenia [AZ Identity Create][az-identity-create] . 

Jeśli planujesz używać tylko tożsamości przypisanej do systemu, Pomiń ten krok. Tożsamość przypisaną przez system można utworzyć podczas tworzenia zadania ACR.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Włącz tożsamość w ACR zadaniu

Podczas tworzenia zadania ACR opcjonalnie należy włączyć tożsamość przypisaną przez użytkownika, tożsamość przypisaną do systemu lub obie te funkcje. Na przykład, należy przekazać `--assign-identity` parametr, gdy uruchamiasz polecenie [AZ ACR Task Create][az-acr-task-create] w interfejsie wiersza polecenia platformy Azure.

Aby włączyć tożsamość przypisaną do systemu, należy `--assign-identity` przekazać bez wartości lub `assign-identity [system]`. Następujące polecenie tworzy zadanie systemu Linux na podstawie publicznego repozytorium GitHub, które kompiluje `hello-world` obraz z wyzwalaczem zatwierdzenia git i z tożsamością zarządzaną przez system:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity
```

Aby włączyć tożsamość przypisaną przez użytkownika, należy `--assign-identity` przekazać wartość *identyfikatora zasobu* tożsamości. Następujące polecenie tworzy zadanie systemu Linux na podstawie publicznego repozytorium GitHub, które kompiluje `hello-world` obraz z wyzwalaczem zatwierdzenia git i z tożsamością zarządzaną przez użytkownika:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity <resourceID>
```

Identyfikator zasobu tożsamości można uzyskać, uruchamiając polecenie [AZ Identity show][az-identity-show] . Identyfikator zasobu dla identyfikatora *myUserAssignedIdentity* *w grupie zasobów* ma postać. 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Przyznaj tożsamości dostęp do innych zasobów platformy Azure

W zależności od wymagań zadania Przyznaj tożsamości uprawnienia dostępu do innych zasobów platformy Azure. Przykłady:

* Przypisywanie tożsamości zarządzanej roli za pomocą ściągania, wypychania i ściągania lub innych uprawnień do rejestru kontenerów docelowych na platformie Azure. Pełną listę ról rejestru można znaleźć w temacie [Azure Container Registry role i uprawnienia](container-registry-roles.md). 
* Przypisanie tożsamości zarządzanej roli do odczytu wpisów tajnych w magazynie kluczy platformy Azure.

Użyj [interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md) lub innych narzędzi platformy Azure, aby zarządzać dostępem opartym na rolach do zasobów. Na przykład uruchom polecenie [AZ role Assign Create][az-role-assignment-create] , aby przypisać tożsamość roli do tożsamości. 

Poniższy przykład przypisuje zarządzanej tożsamości uprawnienia do ściągania z rejestru kontenerów. Polecenie określa identyfikator jednostki *usługi* tożsamości i *Identyfikator zasobu* docelowego rejestru.


```azurecli
az role assignment create --assignee <servicePrincipalID> --scope <registryID> --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. Obowiązkowe Dodawanie poświadczeń do zadania

Jeśli zadanie ściąga lub wypycha obrazy do innego rejestru kontenerów platformy Azure, Dodaj do zadania poświadczenia dotyczące tożsamości do uwierzytelnienia. Aby dodać poświadczenia tożsamości do zadania, uruchom polecenie [AZ ACR Task Credential Add][az-acr-task-credential-add] i przekaż `--use-identity` parametr. 

Na przykład aby dodać poświadczenia dla tożsamości przypisanej do systemu w celu uwierzytelnienia za pomocą rejestru *targetregistry*, `use-identity [system]`Pass:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Aby dodać poświadczenia dla tożsamości przypisanej do użytkownika w celu uwierzytelnienia zapomocą rejestru targetregistry `use-identity` , przekaż wartość *identyfikatora klienta* tożsamości. Na przykład:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Identyfikator klienta tożsamości można uzyskać, uruchamiając polecenie [AZ Identity show][az-identity-show] . Identyfikator klienta jest identyfikatorem GUID formularza `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób włączania i używania tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej do systemu w ramach zadania ACR. Scenariusze umożliwiające dostęp do zabezpieczonych zasobów z zadania ACR przy użyciu tożsamości zarządzanej można znaleźć w temacie:

* [Uwierzytelnianie między rejestrami](container-registry-tasks-cross-registry-authentication.md)
* [Dostęp do zasobów zewnętrznych przy użyciu wpisów tajnych przechowywanych w Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli

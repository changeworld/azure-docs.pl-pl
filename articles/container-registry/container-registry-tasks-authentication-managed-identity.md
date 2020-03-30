---
title: Tożsamość zarządzana w zadaniu ACR
description: Włącz tożsamość zarządzaną dla zasobów platformy Azure w zadaniu rejestru kontenerów platformy Azure, aby umożliwić zadaniu dostęp do innych zasobów platformy Azure, w tym innych rejestrów kontenerów prywatnych.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111771"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Używanie tożsamości zarządzanej przez platformę Azure w zadaniach usługi ACR 

Włącz [tożsamość zarządzaną zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w [zadaniu usługi ACR,](container-registry-tasks-overview.md)aby zadanie uzyskiwać dostęp do innych zasobów platformy Azure bez konieczności podawania poświadczeń ani zarządzania nimi. Na przykład użyj tożsamości zarządzanej, aby włączyć krok zadania, aby wyciągnąć lub wypchnąć obrazy kontenerów do innego rejestru.

W tym artykule dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure, aby włączyć tożsamość zarządzaną przypisaną przez użytkownika lub przypisaną do systemu w zadaniu usługi ACR. Można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.68 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

Na potrzeby ilustracji przykładowe polecenia w tym artykule używają [az acr task create][az-acr-task-create] to create a basic image build task that enables a managed identity. Przykładowe scenariusze uzyskiwania dostępu do zabezpieczonych zasobów z zadania usługi ACR przy użyciu tożsamości zarządzanej można znaleźć na przykład:

* [Uwierzytelnianie między rejestrami](container-registry-tasks-cross-registry-authentication.md)
* [Uzyskiwanie dostępu do zasobów zewnętrznych za pomocą wpisów tajnych przechowywanych w usłudze Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Tożsamość zarządzana zasobów platformy Azure zapewnia wybrane usługi platformy Azure z automatycznie zarządzaną tożsamością w usłudze Azure Active Directory. Można skonfigurować zadanie usługi ACR z tożsamością zarządzaną, aby zadanie miało dostęp do innych zabezpieczonych zasobów platformy Azure bez podawania poświadczeń w krokach zadania.

Tożsamości zarządzane są dwa typy:

* *Tożsamości przypisane przez użytkownika*, które można przypisać do wielu zasobów i utrzymują się tak długo, jak chcesz. Tożsamości przypisane przez użytkownika są obecnie w wersji zapoznawczej.

* *Tożsamość przypisana do systemu*, która jest unikatowa dla określonego zasobu, takiego jak zadanie ACR i trwa przez cały okres istnienia tego zasobu.

Można włączyć jeden lub oba typy tożsamości w zadaniu ACR. Udziel tożsamości dostępu do innego zasobu, podobnie jak każdy podmiot zabezpieczeń. Po uruchomieniu zadania używa tożsamości, aby uzyskać dostęp do zasobu w dowolnych krokach zadania, które wymagają dostępu.

## <a name="steps-to-use-a-managed-identity"></a>Kroki użycia tożsamości zarządzanej

Wykonaj te kroki wysokiego poziomu, aby użyć tożsamości zarządzanej z zadaniem usługi ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Opcjonalnie) Tworzenie tożsamości przypisanej przez użytkownika

Jeśli planujesz używać tożsamości przypisanej przez użytkownika, użyj istniejącej tożsamości lub utwórz tożsamość przy użyciu interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure. Na przykład użyj polecenia [tworzenia tożsamości az.][az-identity-create] 

Jeśli planujesz używać tylko tożsamości przypisanej do systemu, pomiń ten krok. Podczas tworzenia zadania ACR tworzę tożsamość przypisaną do systemu.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Włącz tożsamość w zadaniu ACR

Podczas tworzenia zadania ACR opcjonalnie włącz tożsamość przypisaną przez użytkownika, tożsamość przypisaną do systemu lub obie te te informacje. Na przykład przekazać `--assign-identity` parametr po uruchomieniu [az acr zadanie utworzyć][az-acr-task-create] polecenie w usłudze Azure CLI.

Aby włączyć tożsamość przypisaną `--assign-identity` do systemu, `assign-identity [system]`należy przejść bez wartości lub . Poniższe przykładowe polecenie tworzy zadanie systemu Linux z publicznego repozytorium GitHub, które tworzy `hello-world` obraz i umożliwia tożsamości zarządzanej przypisanej przez system:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Aby włączyć tożsamość przypisaną `--assign-identity` przez użytkownika, przekaż z wartością *identyfikatora zasobu* tożsamości. Poniższe przykładowe polecenie tworzy zadanie systemu Linux z publicznego repozytorium GitHub, które tworzy `hello-world` obraz i umożliwia tożsamości zarządzanej przypisanej przez użytkownika:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Identyfikator zasobu tożsamości można uzyskać, uruchamiając polecenie [az identity show.][az-identity-show] Identyfikator zasobu identyfikatora *myUserAssignedIdentity* w grupie zasobów *myResourceGroup* jest w formularzu: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Udzielanie uprawnień tożsamości w celu uzyskania dostępu do innych zasobów platformy Azure

W zależności od wymagań zadania przyznaj uprawnienia tożsamości, aby uzyskać dostęp do innych zasobów platformy Azure. Przykłady:

* Przypisz tożsamość zarządzaną rolę z ściąganiem, wypychaniem i ściąganiem lub innymi uprawnieniami do docelowego rejestru kontenerów na platformie Azure. Aby uzyskać pełną listę ról rejestru, zobacz [Role i uprawnienia rejestru kontenerów platformy Azure](container-registry-roles.md). 
* Przypisz tożsamość zarządzaną rolę do odczytu wpisów tajnych w magazynie kluczy platformy Azure.

Użyj [interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md) lub innych narzędzi platformy Azure do zarządzania dostępem do zasobów opartych na rolach. Na przykład uruchom [polecenie tworzenia przypisania roli az,][az-role-assignment-create] aby przypisać tożsamość roli do zasobu. 

Poniższy przykład przypisuje tożsamości zarządzanej uprawnienia do ściągania z rejestru kontenerów. Polecenie określa *główny identyfikator* tożsamości zadania i *identyfikator zasobu* rejestru docelowego.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Opcjonalnie) Dodawanie poświadczeń do zadania

Jeśli zadanie wymaga poświadczeń, aby pobierać lub wypychać obrazy do innego rejestru niestandardowego lub uzyskać dostęp do innych zasobów, dodaj poświadczenia do zadania. Uruchom [polecenie dodawania az acr,][az-acr-task-credential-add] aby dodać `--use-identity` poświadczenia, i przekaż parametr, aby wskazać, że tożsamość może uzyskać dostęp do poświadczeń. 

Na przykład, aby dodać poświadczenia dla tożsamości przypisanej do systemu do uwierzytelniania za `use-identity [system]`pomocą *docelowych*rejestrów kontenerów platformy Azure , przekaż:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Aby dodać poświadczenia dla tożsamości przypisanej przez użytkownika do uwierzytelniania za `use-identity` pomocą *rejestru docelowego,* przekaż z wartością *identyfikatora klienta* tożsamości. Przykład:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Identyfikator klienta tożsamości można uzyskać, uruchamiając polecenie [az identity show.][az-identity-show] Identyfikator klienta jest identyfikatorem GUID `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`formularza .

### <a name="5-run-the-task"></a>5. Uruchom zadanie

Po skonfigurowaniu zadania z tożsamością zarządzaną uruchom zadanie. Na przykład, aby przetestować jedno z zadań utworzonych w tym artykule, ręcznie wyzwolić go za pomocą polecenia [az acr uruchamiania zadania.][az-acr-task-run] Jeśli skonfigurowano dodatkowe, zautomatyzowane wyzwalacze zadań, zadanie zostanie uruchomione po automatycznym wyzwoleniu.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak włączyć i używać tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej do systemu w zadaniu usługi ACR. Scenariusze dostępu do zabezpieczonych zasobów z zadania usługi ACR przy użyciu tożsamości zarządzanej można znaleźć na przykład:

* [Uwierzytelnianie między rejestrami](container-registry-tasks-cross-registry-authentication.md)
* [Uzyskiwanie dostępu do zasobów zewnętrznych za pomocą wpisów tajnych przechowywanych w usłudze Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli

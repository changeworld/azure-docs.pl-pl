---
title: Zarządzanie usługą Azure Blockchain za pomocą interfejsu wiersza polecenia platformy Azure
description: Jak zarządzać usługą Azure Blockchain service za pomocą interfejsu wiersza polecenia platformy Azure
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455580"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Zarządzanie usługą Azure Blockchain za pomocą interfejsu wiersza polecenia platformy Azure

Oprócz witryny Azure portal można używać interfejsu wiersza polecenia platformy Azure do zarządzania członkami łańcucha bloków i węzłami transakcji dla usługi Azure Blockchain.

Upewnij się, że zainstalowano najnowszą [platformę Interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i zalogowano się do konta platformy Azure za pomocą `az login`programu .

W poniższych przykładach `<parameter names>` zastąp przykład własnymi wartościami.

## <a name="create-blockchain-member"></a>Tworzenie członka łańcucha bloków

Przykład tworzy członka łańcucha bloków w usłudze Azure Blockchain, który uruchamia protokół księgi kworum w nowym konsorcjum.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w której tworzone są zasoby usługi Azure Blockchain Service. |
| **Nazwa** | Unikatowa nazwa identyfikująca członka łańcucha bloków usługi Azure Blockchain. Nazwa jest używana dla adresu publicznego punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`. |
| **Lokalizacji** | Region platformy Azure, w którym jest tworzony członek łańcucha bloków. Na przykład `eastus`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. |
| **hasło** | Hasło konta członkowskiego. Hasło konta członkowskiego służy do uwierzytelniania w publicznym punkcie końcowym członka łańcucha bloków przy użyciu uwierzytelniania podstawowego. Hasło musi spełniać trzy z następujących czterech wymagań: długość musi wynosić od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 liczba i 1 znak specjalny, który\`nie jest znakiem liczby(#), percent(%), przecinek(,), gwiazdką(*), cytatem tylnym(), podwójnym cudzysłowem("), pojedynczym cudzysłowem('), kreską(-) i średnikiem(;)|
| **Protokół** | Publiczna wersja zapoznawcza obsługuje Kworum. |
| **Konsorcjum** | Nazwa konsorcjum do przyłączenia lub utworzenia. |
| **konsorcjumManagementAccountPassword** | Hasło do zarządzania konsorcjum. Hasło służy do dołączania do konsorcjum. |
| **Rulename** | Nazwa reguły umieszczania na białej liście zakresu adresów IP. Opcjonalny parametr dla reguł zapory.|
| **startIpAddress (startIpAddress)** | Początek zakresu adresów IP do umieszczenia na białej liście. Opcjonalny parametr dla reguł zapory. |
| **endIpAddress (adres endIpAddress)** | Koniec zakresu adresów IP dla białej listy. Opcjonalny parametr dla reguł zapory. |
| **skuName (nazwa skuname)** | Typ warstwy. Użyj S0 dla standardu i B0 dla basic. |

## <a name="change-blockchain-member-password"></a>Zmienianie hasła członka łańcucha bloków

Przykład zmienia hasło członka łańcucha bloków.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w której tworzone są zasoby usługi Azure Blockchain Service. |
| **Nazwa** | Nazwa identyfikująca członka usługi Azure Blockchain. |
| **hasło** | Hasło konta członkowskiego. Hasło musi spełniać trzy z następujących czterech wymagań: długość musi wynosić od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 liczba i 1 znak specjalny, który\`nie jest znakiem liczby(#), percent(%), przecinek(,), gwiazdką(*), cytatem tylnym(), podwójnym cudzysłowem("), pojedynczym cudzysłowem('), kreską(-) i średnikiem(;). |

## <a name="create-transaction-node"></a>Tworzenie węzła transakcji

Utwórz węzeł transakcji wewnątrz istniejącego elementu członkowskiego łańcucha bloków. Dodając węzły transakcji, można zwiększyć izolację zabezpieczeń i rozłożyć obciążenia. Na przykład może mieć punkt końcowy węzła transakcji dla różnych aplikacji klienckich.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w której tworzone są zasoby usługi Azure Blockchain Service. |
| **Nazwa** | Nazwa członka łańcucha bloków usługi Azure Blockchain, który zawiera również nową nazwę węzła transakcji. |
| **Lokalizacji** | Region platformy Azure, w którym jest tworzony członek łańcucha bloków. Na przykład `eastus`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. |
| **hasło** | Hasło węzła transakcji. Hasło musi spełniać trzy z następujących czterech wymagań: długość musi wynosić od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 liczba i 1 znak specjalny, który\`nie jest znakiem liczby(#), percent(%), przecinek(,), gwiazdką(*), cytatem tylnym(), podwójnym cudzysłowem("), pojedynczym cudzysłowem('), kreską(-) i średnikiem(;). |
| **Rulename** | Nazwa reguły umieszczania na białej liście zakresu adresów IP. Opcjonalny parametr dla reguł zapory. |
| **startIpAddress (startIpAddress)** | Początek zakresu adresów IP do umieszczenia na białej liście. Opcjonalny parametr dla reguł zapory. |
| **endIpAddress (adres endIpAddress)** | Koniec zakresu adresów IP dla białej listy. Opcjonalny parametr dla reguł zapory.|

## <a name="change-transaction-node-password"></a>Zmienianie hasła węzła transakcji

Przykład zmienia hasło węzła transakcji.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w której istnieją zasoby usługi Azure Blockchain Service. |
| **Nazwa** | Nazwa członka łańcucha bloków usługi Azure Blockchain, który zawiera również nową nazwę węzła transakcji. |
| **hasło** | Hasło węzła transakcji. Hasło musi spełniać trzy z następujących czterech wymagań: długość musi wynosić od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 liczba i 1 znak specjalny, który\`nie jest znakiem liczby(#), percent(%), przecinek(,), gwiazdką(*), cytatem tylnym(), podwójnym cudzysłowem("), pojedynczym cudzysłowem('), kreską(-) i średnikiem(;). |

## <a name="change-consortium-management-account-password"></a>Zmienianie hasła do konta zarządzania konsorcjum

Konto zarządzania konsorcjum jest używane do zarządzania członkostwem konsorcjum. Każdy członek jest jednoznacznie identyfikowany przez konto zarządzania konsorcjum i można zmienić hasło tego konta za pomocą następującego polecenia.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w której tworzone są zasoby usługi Azure Blockchain Service. |
| **Nazwa** | Nazwa identyfikująca członka usługi Azure Blockchain. |
| **konsorcjumManagementAccountPassword** | Hasło konta zarządzania konsorcjum. Hasło musi spełniać trzy z następujących czterech wymagań: długość musi wynosić od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 liczba i 1 znak specjalny, który\`nie jest znakiem liczby(#), percent(%), przecinek(,), gwiazdką(*), cytatem tylnym(), podwójnym cudzysłowem("), pojedynczym cudzysłowem('), kreską(-) i średnikiem(;). |
  
## <a name="update-firewall-rules"></a>Aktualizowanie reguł zapory

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w której istnieją zasoby usługi Azure Blockchain Service. |
| **Nazwa** | Nazwa członka łańcucha bloków usługi Azure Blockchain. |
| **Rulename** | Nazwa reguły umieszczania na białej liście zakresu adresów IP. Opcjonalny parametr dla reguł zapory.|
| **startIpAddress (startIpAddress)** | Początek zakresu adresów IP do umieszczenia na białej liście. Opcjonalny parametr dla reguł zapory.|
| **endIpAddress (adres endIpAddress)** | Koniec zakresu adresów IP dla białej listy. Opcjonalny parametr dla reguł zapory.|

## <a name="list-api-keys"></a>Lista kluczy interfejsu API

Klucze interfejsu API mogą być używane do dostępu do węzła podobne do nazwy użytkownika i hasła. Istnieją dwa klucze interfejsu API do obsługi rotacji kluczy. Użyj następującego polecenia, aby wyświetlić listę kluczy interfejsu API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w której istnieją zasoby usługi Azure Blockchain Service. |
| **Nazwa** | Nazwa członka łańcucha bloków usługi Azure Blockchain, który zawiera również nową nazwę węzła transakcji. |

## <a name="regenerate-api-keys"></a>Ponowne generowanie kluczy interfejsu API

Użyj następującego polecenia, aby ponownie wygenerować klucze interfejsu API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w której istnieją zasoby usługi Azure Blockchain Service. |
| **Nazwa** | Nazwa członka łańcucha bloków usługi Azure Blockchain, który zawiera również nową nazwę węzła transakcji. |
| **keyName** | Zamień \<keyValue\> na key1 lub key2. |

## <a name="delete-a-transaction-node"></a>Usuwanie węzła transakcji

Przykład usuwa węzeł transakcji elementu członkowskiego łańcucha bloków.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w której istnieją zasoby usługi Azure Blockchain Service. |
| **Nazwa** | Nazwa członka łańcucha bloków usługi Azure Blockchain, który zawiera również nazwę węzła transakcji do usunięcia. |

## <a name="delete-a-blockchain-member"></a>Usuwanie członka łańcucha bloków

Przykład usuwa członka łańcucha bloków.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w której istnieją zasoby usługi Azure Blockchain Service. |
| **Nazwa** | Nazwa członka łańcucha bloków usługi Azure Blockchain do usunięcia. |

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Udzielanie dostępu użytkownikom usługi Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametr | Opis |
|---------|-------------|
| **role (rola)** | Nazwa roli usługi Azure AD. |
| **Cesjonariusza** | Identyfikator użytkownika usługi Azure AD. Na przykład: `user@contoso.com` |
| **Zakres** | Zakres przypisania roli. Może być członkiem łańcucha bloków lub węzłem transakcji. |

**Przykład:**

Udziel dostępu do węzła dla użytkownika usługi Azure AD **członkowi**łańcucha bloków:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Przykład:**

Udziel dostępu do węzła dla użytkownika usługi Azure AD do **węzła transakcji**łańcucha bloków:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Udziel dostępu do węzła dla grupy lub roli aplikacji usługi Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parametr | Opis |
|---------|-------------|
| **role (rola)** | Nazwa roli usługi Azure AD. |
| **identyfikator cesjonariusza-obiekt** | Identyfikator grupy usługi Azure AD lub identyfikator aplikacji. |
| **Zakres** | Zakres przypisania roli. Może być członkiem łańcucha bloków lub węzłem transakcji. |

**Przykład:**

Udziel dostępu do węzła dla **roli aplikacji**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Usuwanie dostępu do węzła usługi Azure AD

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametr | Opis |
|---------|-------------|
| **role (rola)** | Nazwa roli usługi Azure AD. |
| **Cesjonariusza** | Identyfikator użytkownika usługi Azure AD. Na przykład: `user@contoso.com` |
| **Zakres** | Zakres przypisania roli. Może być członkiem łańcucha bloków lub węzłem transakcji. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować węzły transakcji usługi Azure Blockchain Service za pomocą portalu Azure.](configure-transaction-nodes.md)

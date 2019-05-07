---
title: Zarządzanie usługą łańcucha bloków platformy Azure przy użyciu wiersza polecenia platformy Azure
description: Jak tworzyć i zarządzać nimi w usłudze Azure Service łańcucha bloków przy użyciu wiersza polecenia platformy Azure
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154453"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Zarządzanie usługa łańcucha bloków przy użyciu wiersza polecenia platformy Azure

Oprócz witryny Azure portal można użyć wiersza polecenia platformy Azure na szybkie tworzenie i zarządzanie członkami łańcucha bloków i węzły transakcji dla usługi Microsoft Azure Blockchain.

Upewnij się, że zainstalowano najnowszy [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i zalogowano się przy użyciu konta platformy Azure `az login`.

W poniższych przykładach, Zastąp przykład `<parameter names>` własnymi wartościami.

## <a name="create-blockchain-member"></a>Utwórz członka łańcucha bloków

Przykład tworzy członka łańcucha bloków w usłudze Azure Service łańcucha bloków, działający protokół księgi kworum w nowych konsorcjum.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, których są tworzone zasoby platformy Azure Blockchain usługi. |
| **name** | Unikatową nazwę, która identyfikuje elementu członkowskiego usługi Azure Blockchain Service łańcucha bloków. Nazwa jest używana dla adresu publicznego punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`. |
| **location** | Region platformy Azure, w której tworzona jest łańcuch bloków elementu członkowskiego. Na przykład `eastus`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. |
| **Hasło** | Hasło konta członkowskiego. Element członkowski hasło do konta jest używany do uwierzytelniania przy użyciu uwierzytelniania podstawowego punktu końcowego publicznego elementu członkowskiego łańcucha bloków. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 mała litera, 1 Wielka litera, 1 cyfra i 1 znak specjalny inny sign(#) nie liczb, percent(%), przecinka (,), star(*), utworzyć kopię oferty (\`), podwójny quote("), pojedynczych cudzysłowów, myślnik (-) i semicolumn(;)|
| **protocol** | Publiczna wersja zapoznawcza obsługuje kworum. |
| **consortium** | Nazwa konsorcjum, które chcesz dołączyć, lub utworzyć. |
| **consortiumManagementAccountPassword** | Hasło zarządzania konsorcjum. Hasło jest używane do przyłączania konsorcjum. |
| **ruleName** | Nazwa reguły umieszczania na białej liście zakres adresów IP. Opcjonalny parametr dla reguły zapory.|
| **startIpAddress** | Początek zakresu adresów IP do listy dozwolonych. Opcjonalny parametr dla reguły zapory. |
| **endIpAddress** | Koniec zakresu adresów IP do listy dozwolonych. Opcjonalny parametr dla reguły zapory. |
| **skuName** | Typ warstwy. Na użytek S0 Standard i B0 Basic. |

## <a name="change-blockchain-member-password"></a>Zmienianie hasła do elementu członkowskiego łańcucha bloków

Przykład zmienia hasło członka łańcucha bloków.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, których są tworzone zasoby platformy Azure Blockchain usługi. |
| **name** | Nazwa identyfikująca Azure Blockchain Service elementów członkowskich. |
| **Hasło** | Hasło konta członkowskiego. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 mała litera, 1 Wielka litera, 1 cyfra i 1 znak specjalny inny sign(#) nie liczb, percent(%), przecinka (,), star(*), utworzyć kopię oferty (\`), podwójny quote("), pojedynczych cudzysłowów, myślnik (-) i semicolon(;). |


## <a name="create-transaction-node"></a>Utwórz węzeł transakcji

Utwórz węzeł transakcji wewnątrz istniejącego posiadacza łańcucha bloków. Dodając węzły transakcji, można zwiększyć zabezpieczeń, izolacji i rozłożenie obciążenia. Na przykład można mieć punktem końcowym węzła transakcji dla różnych aplikacji klienckich.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, których są tworzone zasoby platformy Azure Blockchain usługi. |
| **name** | Nazwa elementu członkowskiego łańcucha bloków Azure Blockchain Service, która zawiera również nazwę węzła transakcji. |
| **location** | Region platformy Azure, w której tworzona jest łańcuch bloków elementu członkowskiego. Na przykład `eastus`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. |
| **Hasło** | Hasło węzła transakcji. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 mała litera, 1 Wielka litera, 1 cyfra i 1 znak specjalny inny sign(#) nie liczb, percent(%), przecinka (,), star(*), utworzyć kopię oferty (\`), podwójny quote("), pojedynczych cudzysłowów, myślnik (-) i semicolon(;). |
| **ruleName** | Nazwa reguły umieszczania na białej liście zakres adresów IP. Opcjonalny parametr dla reguły zapory. |
| **startIpAddress** | Początek zakresu adresów IP do listy dozwolonych. Opcjonalny parametr dla reguły zapory. |
| **endIpAddress** | Koniec zakresu adresów IP do listy dozwolonych. Opcjonalny parametr dla reguły zapory.|

## <a name="change-transaction-node-password"></a>Zmienianie hasła do węzła transakcji

Przykład zmienia hasło węzła transakcji.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, gdy istnieją zasobów Azure Blockchain usługi. |
| **name** | Nazwa elementu członkowskiego łańcucha bloków Azure Blockchain Service, która zawiera również nazwę węzła transakcji. |
| **Hasło** | Hasło węzła transakcji. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 mała litera, 1 Wielka litera, 1 cyfra i 1 znak specjalny inny sign(#) nie liczb, percent(%), przecinka (,), star(*), utworzyć kopię oferty (\`), podwójny quote("), pojedynczych cudzysłowów, myślnik (-) i semicolon(;). |

## <a name="change-consortium-management-account-password"></a>Zmień hasło do konta zarządzania konsorcjum

Konto zarządzania konsorcjum służy do zarządzania członkostwa konsorcjum. Każdy element członkowski jest unikatowo identyfikowana przez konsorcjum konta zarządzania, a następnie można zmienić hasło tego konta za pomocą następującego polecenia.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, których są tworzone zasoby platformy Azure Blockchain usługi. |
| **name** | Nazwa identyfikująca Azure Blockchain Service elementów członkowskich. |
| **consortiumManagementAccountPassword** | Hasło konta zarządzania konsorcjum. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 mała litera, 1 Wielka litera, 1 cyfra i 1 znak specjalny inny sign(#) nie liczb, percent(%), przecinka (,), star(*), utworzyć kopię oferty (\`), podwójny quote("), pojedynczych cudzysłowów, myślnik (-) i semicolon(;). |
  
## <a name="update-firewall-rules"></a>Aktualizuj reguły zapory

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, gdy istnieją zasobów Azure Blockchain usługi. |
| **name** | Nazwa elementu członkowskiego łańcucha bloków Azure Blockchain Service. |
| **ruleName** | Nazwa reguły umieszczania na białej liście zakres adresów IP. Opcjonalny parametr dla reguły zapory.|
| **startIpAddress** | Początek zakresu adresów IP do listy dozwolonych. Opcjonalny parametr dla reguły zapory.|
| **endIpAddress** | Koniec zakresu adresów IP do listy dozwolonych. Opcjonalny parametr dla reguły zapory.|

## <a name="list-api-keys"></a>Interfejs API listy kluczy

Klucze interfejsu API umożliwia dostęp do węzła podobne do nazwy użytkownika i hasła. Istnieją dwa klucze interfejsu API do obsługi wymiany kluczy. Użyj następującego polecenia, aby wyświetlić listę kluczy interfejsu API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, gdy istnieją zasobów Azure Blockchain usługi. |
| **name** | Nazwa elementu członkowskiego łańcucha bloków Azure Blockchain Service, która zawiera również nazwę węzła transakcji. |

## <a name="regenerate-api-keys"></a>Wygeneruj ponownie klucze interfejsu API

Użyj następującego polecenia, aby ponownie wygenerować klucze interfejsu API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, gdy istnieją zasobów Azure Blockchain usługi. |
| **name** | Nazwa elementu członkowskiego łańcucha bloków Azure Blockchain Service, która zawiera również nazwę węzła transakcji. |
| **keyName** | Zastąp \<par klucz-wartość\> klucz1 lub klucz2. |

## <a name="delete-a-transaction-node"></a>Usuń węzeł transakcji

Przykład usuwa transakcji węzeł elementu członkowskiego łańcucha bloków.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, gdy istnieją zasobów Azure Blockchain usługi. |
| **name** | Nazwa składowej łańcucha bloków Azure Blockchain Service, która obejmuje również nową nazwę węzła transakcji do usunięcia. |

## <a name="delete-a-blockchain-member"></a>Usuń element członkowski łańcucha bloków

Przykład usuwa element członkowski łańcucha bloków.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, gdy istnieją zasobów Azure Blockchain usługi. |
| **name** | Nazwa elementu członkowskiego łańcucha bloków Azure Blockchain usługi do usunięcia. |

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Udzielanie dostępu dla użytkownika usługi Azure AD

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametr | Opis |
|---------|-------------|
| **Rola** | Nazwa roli usługi Azure AD. |
| **osoby przypisanej** | Identyfikator usługi Azure AD użytkownika. Na przykład: `user@contoso.com` |
| **Zakres** | Zakres przypisania roli. Może być Członkowskim łańcucha bloków węzła transakcji. |

**Przykład:**

Udzielanie dostępu węzła dla użytkownika usługi Azure AD do łańcucha bloków **elementu członkowskiego**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Przykład:**

Udzielanie dostępu węzła dla użytkownika usługi Azure AD do łańcucha bloków **węzła transakcji**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Przyznaj dostęp do węzła dla grupy usługi Azure AD lub aplikacji roli

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Parametr | Opis |
|---------|-------------|
| **Rola** | Nazwa roli usługi Azure AD. |
| **Identyfikator osoby przydzielonej — obiekt** | Identyfikator grupy usługi Azure AD lub aplikacji identyfikatora. |
| **Zakres** | Zakres przypisania roli. Może być Członkowskim łańcucha bloków węzła transakcji. |

**Przykład:**

Przyznaj dostęp do węzła dla **ról aplikacji**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Usuń dostęp do węzła usługi Azure AD

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametr | Opis |
|---------|-------------|
| **Rola** | Nazwa roli usługi Azure AD. |
| **osoby przypisanej** | Identyfikator usługi Azure AD użytkownika. Na przykład: `user@contoso.com` |
| **Zakres** | Zakres przypisania roli. Może być Członkowskim łańcucha bloków węzła transakcji. |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie węzłów transakcji usługi Azure Service łańcucha bloków w witrynie Azure portal](configure-transaction-nodes.md)

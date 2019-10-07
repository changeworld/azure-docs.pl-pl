---
title: Zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji Azure Key Vault | Microsoft Docs
description: Dowiedz się, jak przełączyć identyfikator dzierżawy magazynu kluczy po przeniesieniu subskrypcji do innej dzierżawy
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 3819742e82fe6877b6a1aa58e52eec01b6b05515
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001245"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Po utworzeniu nowego magazynu kluczy w ramach subskrypcji zostanie on automatycznie powiązany z domyślnym IDENTYFIKATORem dzierżawy Azure Active Directory dla tej subskrypcji. Wszystkie wpisy zasad dostępu są również powiązane z tym IDENTYFIKATORem dzierżawy. 

Jeśli przeniesiesz subskrypcję platformy Azure z dzierżawy A do dzierżawy B, istniejące magazyny kluczy są niedostępne dla podmiotów zabezpieczeń (użytkowników i aplikacji) w dzierżawie B. Aby rozwiązać ten problem, należy:

* Zmień identyfikator dzierżawy skojarzony ze wszystkimi istniejącymi magazynami kluczy w subskrypcji na dzierżawcę B.
* Usuń wszystkie istniejące wpisy zasad dostępu.
* Dodaj nowe wpisy zasad dostępu skojarzone z dzierżawcą B.

Jeśli na przykład masz Magazyn kluczy "Moja magazyn" w subskrypcji, która została przeniesiona z dzierżawy A do dzierżawy B, możesz użyć Azure PowerShell do zmiany identyfikatora dzierżawy i usunięcia starych zasad dostępu.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

Można też użyć interfejsu wiersza polecenia platformy Azure.

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Po skojarzeniu magazynu z prawidłowym IDENTYFIKATORem dzierżawy i usunięciu starych wpisów zasad dostępu Ustaw nowe wpisy zasad dostępu za pomocą polecenia cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](https://powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) lub interfejsu wiersza polecenia platformy Azure [AZ datamagazyn Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

Jeśli używasz zarządzanej tożsamości dla zasobów platformy Azure, musisz zaktualizować ją również do nowej dzierżawy usługi Azure AD. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [zapewnianie uwierzytelniania Key Vault przy użyciu tożsamości zarządzanej](managed-identity.md).


Jeśli używasz pliku MSI, musisz również zaktualizować tożsamość MSI, ponieważ stara tożsamość nie będzie już w poprawnej dzierżawie usługi AAD.

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania dotyczące Azure Key Vault, odwiedź [fora Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

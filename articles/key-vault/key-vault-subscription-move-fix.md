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
ms.openlocfilehash: 87025767725142cc2f861ff8b390d6ea916f8e38
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947733"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>Pytanie: Moja subskrypcja została przeniesiona z dzierżawy A do dzierżawy B. Jak zmienić identyfikator dzierżawy dla istniejącego magazynu kluczy i ustawić prawidłowe listy ACL dla nazw głównych w dzierżawie B?

Po utworzeniu nowego magazynu kluczy w subskrypcji zostaje on automatycznie powiązany z domyślnym identyfikatorem dzierżawy usługi Azure Active Directory dla tej subskrypcji. Wszystkie wpisy zasad dostępu również zostają powiązane z tym identyfikatorem dzierżawy. Po przeniesieniu subskrypcji platformy Azure z dzierżawy A do dzierżawy B istniejące magazyny kluczy są niedostępne za pomocą nazw głównych (użytkowników i aplikacji) w dzierżawie B. Aby rozwiązać ten problem, należy:

* zmienić identyfikator dzierżawy skojarzony ze wszystkimi istniejącymi magazynami kluczy w tej subskrypcji na dzierżawę B,
* usunąć wszystkie istniejące wpisy zasad dostępu,
* dodać nowe wpisy zasad dostępu skojarzone z dzierżawą B.

Jeśli na przykład masz magazyn kluczy „myvault” w subskrypcji przeniesionej z dzierżawy a do dzierżawy B, oto jak zmienić identyfikator dzierżawy dla tego magazynu kluczy i usunąć stare zasady dostępu.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID                   # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your Keyvault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your Keyvault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your Keyvault resides in
$vault.Properties.AccessPolicies = @()                                     # Accesspolicies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                                                                              # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the kevault's properties.
</pre>

Ponieważ ten magazyn był w dzierżawie A przed przeniesieniem, oryginalna wartość **$Vault. Właściwość. TenantId** jest dzierżawcą A, podczas gdy **(Get-AzContext). Dzierżawca. TenantId** jest dzierżawcą B.

Po skojarzeniu magazynu z prawidłowym IDENTYFIKATORem dzierżawy i usunięciu starych wpisów zasad dostępu Ustaw nowe wpisy zasad dostępu za pomocą [opcji Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy).

Jeśli używasz pliku MSI, musisz również zaktualizować tożsamość MSI, ponieważ stara tożsamość nie będzie już w poprawnej dzierżawie usługi AAD.

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania dotyczące usługi Azure Key Vault, odwiedź [forum usługi Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

---
title: Zmiana Identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przełączyć identyfikator dzierżawy magazynu kluczy po przeniesieniu subskrypcji do innej dzierżawy
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: ea6fc4b155075084150d5bb732f3f8a08846974f
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074312"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>PYT.: Moja subskrypcja została przeniesiona z dzierżawy A do dzierżawy B. Jak zmienić identyfikator dzierżawy dla istniejącego magazynu kluczy i ustawić prawidłowe listy ACL dla nazw głównych w dzierżawie B?

Po utworzeniu nowego magazynu kluczy w subskrypcji zostaje on automatycznie powiązany z domyślnym identyfikatorem dzierżawy usługi Azure Active Directory dla tej subskrypcji. Wszystkie wpisy zasad dostępu również zostają powiązane z tym identyfikatorem dzierżawy. Po przeniesieniu subskrypcji platformy Azure z dzierżawy A do dzierżawy B istniejące magazyny kluczy są niedostępne za pomocą nazw głównych (użytkowników i aplikacji) w dzierżawie B. Aby rozwiązać ten problem, należy:

* zmienić identyfikator dzierżawy skojarzony ze wszystkimi istniejącymi magazynami kluczy w tej subskrypcji na dzierżawę B,
* usunąć wszystkie istniejące wpisy zasad dostępu,
* dodać nowe wpisy zasad dostępu skojarzone z dzierżawą B.

Jeśli na przykład masz magazyn kluczy „myvault” w subskrypcji przeniesionej z dzierżawy a do dzierżawy B, oto jak zmienić identyfikator dzierżawy dla tego magazynu kluczy i usunąć stare zasady dostępu.

<pre>
Select-AzureRmSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Ponieważ ten magazyn przed przeniesieniem był w dzierżawie A, pierwotna wartość właściwości **$vault.Properties.TenantId** to dzierżawa A, podczas gdy wartość właściwości **(Get-AzureRmContext).Tenant.TenantId** to dzierżawa B.

Po skojarzeniu magazynu z poprawnym identyfikatorem dzierżawy i usunięciu starych wpisów zasad dostępu ustaw nowe wpisy zasad dostępu za pomocą polecenia [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy).

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz pytania dotyczące usługi Azure Key Vault, odwiedź [forum usługi Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

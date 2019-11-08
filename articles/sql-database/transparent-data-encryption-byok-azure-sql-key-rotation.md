---
title: Obróć funkcję ochrony TDE — PowerShell
description: Dowiedz się, jak obrócić funkcję ochrony Transparent Data Encryption (TDE) dla serwera SQL platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 5bfcacb9348940e0b36947f6e4e0d27839de35bb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824679"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Obróć funkcję ochrony Transparent Data Encryption (TDE) przy użyciu programu PowerShell

W tym artykule opisano rotację kluczy dla serwera SQL Azure przy użyciu funkcji ochrony TDE z Azure Key Vault. Obrócenie funkcji ochrony TDE w programie Azure SQL Server oznacza przełączenie do nowego klucza asymetrycznego chroniącego bazy danych na serwerze. Rotacja kluczy jest operacją online i należy wykonać zaledwie kilka sekund, ponieważ spowoduje to odszyfrowanie i ponowne zaszyfrowanie klucza szyfrowania danych bazy danych, a nie całej bazy danych.

W tym przewodniku omówiono dwie opcje rotacji funkcji ochrony TDE na serwerze.

> [!NOTE]
> Wstrzymanie SQL Data Warehouse musi zostać wznowione przed rotacją kluczy.
>

> [!IMPORTANT]
> **Nie usuwaj** poprzednich wersji klucza po przejściu.  Gdy klucze są zestawiane, niektóre dane są nadal szyfrowane przy użyciu poprzednich kluczy, takich jak starsze kopie zapasowe bazy danych. 
>

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

- W tym przewodniku założono, że korzystasz już z klucza z Azure Key Vault jako funkcji ochrony TDE dla Azure SQL Database lub magazynu danych. Zobacz [transparent Data Encryption z obsługą BYOK](transparent-data-encryption-byok-azure-sql.md).
- Musisz mieć Azure PowerShell zainstalowane i uruchomione.
- [Zalecane, ale opcjonalne] Najpierw Utwórz materiał klucza dla ochrony TDE w sprzętowym module zabezpieczeń (HSM) lub lokalnym magazynie kluczy, a następnie zaimportuj kluczowy materiał do Azure Key Vault. Postępuj zgodnie z [instrukcjami dotyczącymi używania sprzętowego modułu zabezpieczeń (HSM) i Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) , aby dowiedzieć się więcej.

## <a name="manual-key-rotation"></a>Ręczny obrót klucza

Ręczne rotacja kluczy używa poleceń cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)i [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) w celu dodania zupełnie nowego klucza, który może znajdować się w nowej nazwie klucza lub nawet w innym kluczu archiwizowan. Użycie tej metody umożliwia dodanie tego samego klucza do różnych magazynów kluczy w celu obsługi scenariuszy wysokiej dostępności i odzyskiwania geograficznego.

>[!NOTE]
>Łączna długość nazwy magazynu kluczy i nazwy klucza nie może przekraczać 94 znaków.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>Inne przydatne polecenia cmdlet programu PowerShell

- Aby przełączyć funkcję ochrony TDE z trybu zarządzanego przez firmę Microsoft do BYOK, należy użyć polecenia cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Aby przełączyć funkcję ochrony TDE z trybu BYOK do zarządzanego przez firmę Microsoft, należy użyć polecenia cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Następne kroki

- W przypadku zagrożenia bezpieczeństwa należy dowiedzieć się, jak usunąć potencjalnie naruszoną ochronę TDE: [Usuń potencjalnie złamany klucz](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Rozpocznij pracę z integracją Azure Key Vault i wsparcie Bring Your Own Key dla TDE: [Włącz TDE przy użyciu własnego klucza z Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)

---
title: PowerShell — funkcja ochrony TDE Obróć — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wymienić ochrony przezroczystego szyfrowania danych (TDE) dla serwera Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 03/12/2019
ms.openlocfilehash: 760b292e75b4cc64b85eaf51ffad0521b721dabf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60330860"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Obróć ochrony przezroczystego szyfrowania danych (TDE) przy użyciu programu PowerShell

W tym artykule opisano rotacji kluczy dla serwera Azure SQL przy użyciu funkcji ochrony TDE z usługi Azure Key Vault. Obracanie serwera Azure SQL TDE ochrony oznacza, że przełączenie do nowego klucza asymetrycznego, który zapewnia ochronę baz danych na serwerze. Wymiana kluczy jest operacji w trybie online i trwa tylko kilka sekund, aby zakończyć, ponieważ to tylko odszyfrowywane i ponownie szyfruje klucz szyfrowania danych bazy danych, nie całą bazę danych.

Ten przewodnik w tym artykule omówiono dwa sposoby Obróć ochrony TDE na serwerze.

> [!NOTE]
> Wstrzymanej usłudze SQL Data Warehouse musi zostać wznowiony przed wymianą klucza.
>

> [!IMPORTANT]
> **Czy usunąć nie** poprzedniej wersji klucza po przerzucania.  Jeśli klucze są przenoszone, niektóre dane nadal są szyfrowane przy użyciu poprzednich kluczy, takie jak starsze kopie zapasowe bazy danych. 
>

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

- W tym przewodniku przyjęto założenie, że korzystasz już klucza z usługi Azure Key Vault jako funkcja ochrony TDE dla usługi Azure SQL Database lub magazynu danych. Zobacz [Transparent Data Encryption z obsługą funkcji BYOK](transparent-data-encryption-byok-azure-sql.md).
- Konieczne jest posiadanie programu Azure PowerShell zainstalowany i uruchomiony.
- [Zalecane, ale opcjonalny] Utworzyć materiału klucza dla funkcji ochrony TDE w sprzętowego modułu zabezpieczeń (HSM) lub klucza lokalnego przechowywania pierwszy i zaimportować materiału klucza do usługi Azure Key Vault. Postępuj zgodnie z [instrukcje dotyczące używania sprzętowego modułu zabezpieczeń (HSM) oraz usługi Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) Aby dowiedzieć się więcej.

## <a name="manual-key-rotation"></a>Ręczna wymiana kluczy

Ręczna wymiana kluczy używa [AzKeyVaultKey Dodaj](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [AzSqlServerKeyVaultKey Dodaj](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), i [AzSqlServerTransparentDataEncryptionProtector zestaw](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) poleceń cmdlet, aby dodać zupełnie nowy klucz, który może być pod nową nazwą klucza lub nawet inny magazyn kluczy. To podejście obsługuje dodawanie tego samego klucza do różnych magazynów kluczy w celu obsługi scenariuszy wysokiej dostępności i geo-dr.

>[!NOTE]
>Łączna długość nazwy magazynu kluczy i nazwę klucza nie może przekraczać 94 znaki.

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

## <a name="option-2-manual-rotation"></a>Opcja 2: Ręczne obrotu

Ta opcja używa [AzKeyVaultKey Dodaj](/powershell/module/az.keyvault/add-azkeyvaultkey), [AzSqlServerKeyVaultKey Dodaj](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), i [AzSqlServerTransparentDataEncryptionProtector zestaw](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) poleceń cmdlet, aby dodać całkowicie nowy klucz, który może być pod nową nazwą klucza lub nawet inny magazyn kluczy. 

>[!NOTE]
>Łączna długość nazwy magazynu kluczy i nazwę klucza nie może przekraczać 94 znaki.
>

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
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Inne przydatne poleceń cmdlet programu PowerShell

- Aby przełączyć ochrony TDE z zarządzanych przez firmę Microsoft do trybu BYOK, użyj [AzSqlServerTransparentDataEncryptionProtector zestaw](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) polecenia cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Aby przełączyć ochrony TDE z trybu BYOK na zarządzany przez firmę Microsoft, należy użyć [AzSqlServerTransparentDataEncryptionProtector zestaw](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) polecenia cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Kolejne kroki

- W przypadku zagrożenie bezpieczeństwa Dowiedz się, jak usunąć mogą mieć złamane zabezpieczenia ochrony TDE: [Usuń klucz mogą mieć złamane zabezpieczenia](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Rozpocznij pracę dzięki integracji usługi Azure Key Vault i obsłudze własnego klucza dla funkcji TDE: [Włączanie funkcji TDE przy użyciu własnego klucza z usługi Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)

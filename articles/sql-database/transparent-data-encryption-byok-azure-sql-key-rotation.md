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
ms.date: 12/06/2018
ms.openlocfilehash: 14a39d283d9ec4f8d5267e6a6628609ac79879ee
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567506"
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

- W tym przewodniku przyjęto założenie, że korzystasz już klucza z usługi Azure Key Vault jako funkcja ochrony TDE dla usługi Azure SQL Database lub magazynu danych. Zobacz [Transparent Data Encryption z obsługą funkcji BYOK](transparent-data-encryption-byok-azure-sql.md).
- Konieczne jest posiadanie programu Azure PowerShell w wersji numer 3.7.0 lub nowszej zainstalowany i uruchomiony. 
- [Zalecane, ale opcjonalny] Utworzyć materiału klucza dla funkcji ochrony TDE w sprzętowego modułu zabezpieczeń (HSM) lub klucza lokalnego przechowywania pierwszy i zaimportować materiału klucza do usługi Azure Key Vault. Postępuj zgodnie z [instrukcje dotyczące używania sprzętowego modułu zabezpieczeń (HSM) oraz usługi Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) Aby dowiedzieć się więcej.

## <a name="option-1-auto-rotation"></a>Opcja 1: Automatyczne obracanie

Wygeneruj nową wersję istniejącego klucza funkcji ochrony TDE w usłudze Key Vault w ramach tej samej nazwy kluczy i kluczy. Usługa Azure SQL jest uruchamiana przy użyciu tej nowej wersji w ciągu 24 godzin. 

Aby utworzyć nową wersję TDE ochrony za pomocą [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) polecenia cmdlet:

   ```powershell
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>
   ```

## <a name="option-2-manual-rotation"></a>Opcja 2: Ręczne obrotu

Ta opcja używa [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey), [AzureRmSqlServerKeyVaultKey Dodaj](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey), i [polecenia Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) poleceń cmdlet, aby dodać zupełnie nowy klucz, który może być pod nową nazwą klucza lub nawet inny magazyn kluczy. 

>[!NOTE]
>Łączna długość nazwy magazynu kluczy i nazwę klucza nie może przekraczać 94 znaki.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Inne przydatne poleceń cmdlet programu PowerShell

- Aby przełączyć ochrony TDE z zarządzanych przez firmę Microsoft do trybu BYOK, użyj [polecenia Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) polecenia cmdlet.

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Aby przełączyć ochrony TDE z trybu BYOK na zarządzany przez firmę Microsoft, należy użyć [polecenia Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) polecenia cmdlet.

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Kolejne kroki

- W przypadku zagrożenie bezpieczeństwa Dowiedz się, jak usunąć mogą mieć złamane zabezpieczenia ochrony TDE: [Usuń klucz mogą mieć złamane zabezpieczenia](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Rozpoczynanie pracy z usługą Bring Your Own Key pomocy technicznej dla funkcji TDE: [Włączanie funkcji TDE przy użyciu własnego klucza z usługi Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)

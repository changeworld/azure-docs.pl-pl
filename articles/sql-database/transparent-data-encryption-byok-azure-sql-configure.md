---
title: 'Program PowerShell i interfejs wiersza polecenia: Włączanie funkcji SQL TDE - key - usługi Azure SQL Database | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure SQL Database i Data Warehouse, aby rozpocząć korzystanie z przezroczystego szyfrowania danych (TDE) do szyfrowania podczas spoczynku przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 0fad0cd32e8df38c5a9c06ecf01a14340f1bc9ef
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165079"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-using-your-own-key-from-azure-key-vault"></a>Program PowerShell i interfejs wiersza polecenia: Włączanie funkcji Transparent Data Encryption przy użyciu własnego klucza z usługi Azure Key Vault

W tym artykule przedstawiono sposób użycia klucza z usługi Azure Key Vault dla przezroczystego szyfrowania danych (TDE) bazy danych SQL lub magazynu danych. Aby dowiedzieć się więcej na temat funkcji TDE z obsługą Bring Your Own Key (BYOK), odwiedź stronę [TDE własnego klucza do bazy danych SQL Azure](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Wymagania wstępne dotyczące programu PowerShell

- Musisz mieć subskrypcję platformy Azure i mieć uprawnienia administratora na tę subskrypcję.
- [Zalecane, ale opcjonalny] Mieć sprzętowego modułu zabezpieczeń (HSM) lub klucza lokalnego magazynu dla tworzenia kopii lokalnej materiału klucza funkcji ochrony TDE.
- Konieczne jest posiadanie programu Azure PowerShell w wersji 4.2.0 lub nowszej zainstalowany i uruchomiony. 
- Utwórz usługę Azure Key Vault i klucz do użycia dla funkcji TDE.
   - [Instrukcje dotyczące programu PowerShell z usługi Key Vault](../key-vault/key-vault-get-started.md)
   - [Instrukcje dotyczące używania sprzętowego modułu zabezpieczeń (HSM) oraz usługi Key Vault](../key-vault/key-vault-get-started.md#HSM)
 - Usługi key vault musi mieć następującą właściwość, która ma być używany dla funkcji TDE:
   - [opcji soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
   - [Jak używać usuwania nietrwałego w usłudze Key Vault z programem PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- Klucz musi mieć następujące atrybuty, które ma być używany dla funkcji TDE:
   - Bez daty wygaśnięcia
   - Nie jest wyłączona
   - Możliwość wykonania *uzyskać*, *opakuj klucz*, *odpakuj klucz* operacji

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Krok 1. Przypisz tożsamości usługi Azure AD do serwera 

Jeśli masz istniejącego serwera, umożliwia dodawanie tożsamości usługi Azure AD do serwera następujące czynności:

   ```powershell
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Jeśli tworzysz serwera, użyj [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) polecenia cmdlet z tagiem — tożsamości, aby dodać tożsamości usługi Azure AD podczas tworzenia serwera:

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Krok 2. Udzielanie uprawnień usługi Key Vault do serwera

Użyj [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) polecenia cmdlet, aby udzielić dostępu serwera do klucza magazynu przed rozpoczęciem korzystania z klucza z niego dla funkcji TDE.

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Krok 3. Dodaj klucz usługi Key Vault do serwera i ustaw ochrony TDE

- Użyj [AzureRmSqlServerKeyVaultKey Dodaj](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) polecenia cmdlet, aby dodać klucz z usługi Key Vault do serwera.
- Użyj [polecenia Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) polecenia cmdlet, aby ustawić klucz jako funkcji ochrony TDE dla wszystkich zasobów serwera.
- Użyj [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) polecenia cmdlet, aby upewnić się, że funkcji ochrony TDE została skonfigurowana zgodnie z oczekiwaniami.

> [!Note]
> Łączna długość nazwy magazynu kluczy i nazwę klucza nie może przekraczać 94 znaki.
> 

>[!Tip]
>Przykład KeyId z usługi Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Krok 4. Włączanie funkcji TDE 

Użyj [AzureRMSqlDatabaseTransparentDataEncryption zestaw](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) polecenia cmdlet, aby włączyć funkcję TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Bazy danych ani na magazyn danych ma teraz funkcja TDE jest włączona przy użyciu klucza szyfrowania w usłudze Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Krok 5. Sprawdź stan szyfrowania i działania szyfrowania

Użyj [Get AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) można pobrać stanu szyfrowania i [Get AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) Aby sprawdzić postęp szyfrowania bazy danych ani na magazyn danych.

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Inne przydatne poleceń cmdlet programu PowerShell

- Użyj [AzureRMSqlDatabaseTransparentDataEncryption zestaw](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) polecenia cmdlet, aby wyłączyć funkcję TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Użyj [Get AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) polecenia cmdlet, aby zwrócić listę kluczy magazynu kluczy, dodane do serwera.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Użyj [AzureRmSqlServerKeyVaultKey Usuń](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) usunąć klucz usługi Key Vault z serwera.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli problem występuje, sprawdź następujące kwestie:
- Jeśli nie można odnaleźć usługi key vault, upewnij się, znajdujesz się w prawej subskrypcji za pomocą [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) polecenia cmdlet.

   ```powershell
   Get-AzureRmSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Jeśli nowy klucz nie można dodać do serwera lub nowy klucz nie można zaktualizować jako funkcja ochrony TDE, sprawdź następujące informacje:
   - Klucz nie powinny mieć datę wygaśnięcia
   - Klucz musi mieć *uzyskać*, *opakuj klucz*, i *odpakuj klucz* włączone czynności.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak obrócić ochrony TDE serwera, aby spełnić wymagania dotyczące zabezpieczeń: [Obróć programu PowerShell przy użyciu funkcji ochrony Transparent Data Encryption](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- W przypadku zagrożenie bezpieczeństwa, Dowiedz się, jak usunąć mogą mieć złamane zabezpieczenia ochrony TDE: [Usuń klucz mogą mieć złamane zabezpieczenia](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia

- Musisz mieć subskrypcję platformy Azure i mieć uprawnienia administratora na tę subskrypcję.
- [Zalecane, ale opcjonalny] Mieć sprzętowego modułu zabezpieczeń (HSM) lub klucza lokalnego magazynu dla tworzenia kopii lokalnej materiału klucza funkcji ochrony TDE.
- Interfejs wiersza polecenia w wersji 2.0 lub nowszej. Aby zainstalować najnowszą wersję i nawiązać połączenie z subskrypcją platformy Azure, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia dla wielu Platform Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
- Utwórz usługę Azure Key Vault i klucz do użycia dla funkcji TDE.
   - [Zarządzanie przy użyciu interfejsu wiersza polecenia 2.0 w usłudze Key Vault](../key-vault/key-vault-manage-with-cli2.md)
   - [Instrukcje dotyczące używania sprzętowego modułu zabezpieczeń (HSM) oraz usługi Key Vault](../key-vault/key-vault-get-started.md#HSM)
 - Usługi key vault musi mieć następującą właściwość, która ma być używany dla funkcji TDE:
   - [opcji soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
   - [Jak używać usuwania nietrwałego w usłudze Key Vault z interfejsem wiersza polecenia](../key-vault/key-vault-soft-delete-cli.md) 
- Klucz musi mieć następujące atrybuty, które ma być używany dla funkcji TDE:
   - Bez daty wygaśnięcia
   - Nie jest wyłączona
   - Możliwość wykonania *uzyskać*, *opakuj klucz*, *odpakuj klucz* operacji
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>Krok 1. Tworzenie serwera i przypisz tożsamości usługi Azure AD do serwera
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -I 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Krok 2. Udzielanie uprawnień usługi Key Vault do serwera
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Krok 3. Dodaj klucz usługi Key Vault do serwera i ustaw ochrony TDE
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> Łączna długość nazwy magazynu kluczy i nazwę klucza nie może przekraczać 94 znaki.
> 

>[!Tip]
>Przykład KeyId z usługi Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>Krok 4. Włączanie funkcji TDE 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

Bazy danych ani na magazyn danych ma teraz funkcja TDE jest włączona przy użyciu klucza szyfrowania w usłudze Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Krok 5. Sprawdź stan szyfrowania i działania szyfrowania

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>Odwołania do interfejsu wiersza polecenia SQL

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 


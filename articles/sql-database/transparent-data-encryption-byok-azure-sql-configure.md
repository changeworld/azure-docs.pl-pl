---
title: 'PowerShell i interfejs wiersza polecenia: Włącz TDE SQL — z Azure Key Vault — Przenieś własny klucz Azure SQL Database | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure SQL Database i magazyn danych, aby rozpocząć korzystanie z Transparent Data Encryption (TDE) na potrzeby szyfrowania w środowisku REST przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: beeb5fa4f979ac457db8a779dd8f8f2e94ef87f5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163853"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell i interfejs wiersza polecenia: Włącz Transparent Data Encryption z kluczem zarządzanym przez klienta w programie Azure Key Vault

W tym artykule przedstawiono sposób użycia klucza z Azure Key Vault dla Transparent Data Encryption (TDE) na SQL Database lub magazynie danych. Aby dowiedzieć się więcej na temat obsługi TDE Azure Key Vault z integracją Bring Your Own Key (BYOK), odwiedź [TDE z kluczami zarządzanymi przez klienta w Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Wymagania wstępne dotyczące programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

- Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.
- [Zalecane, ale opcjonalne] Posiadanie sprzętowego modułu zabezpieczeń (HSM) lub lokalnego magazynu kluczy na potrzeby tworzenia lokalnej kopii materiału klucza ochrony TDE.
- Musisz mieć Azure PowerShell zainstalowane i uruchomione. 
- Utwórz Azure Key Vault i klucz do użycia dla TDE.
  - [Instrukcje programu PowerShell z Key Vault](../key-vault/quick-create-powershell.md)
  - [Instrukcje dotyczące korzystania z sprzętowego modułu zabezpieczeń (HSM) i Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Magazyn kluczy musi mieć następującą właściwość, która ma być używana dla TDE:
  - Ochrona przed [usuwaniem](../key-vault/key-vault-ovw-soft-delete.md) i przeczyszczaniem
  - [Jak używać usuwania nietrwałego w usłudze Key Vault z programem PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- Klucz musi mieć następujące atrybuty, aby można było używać go dla TDE:
   - Brak daty wygaśnięcia
   - Niewyłączone
   - Możliwość wykonania operacji *Get*, *zawijania klucza*, *odpakowania klucza*

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Krok 1. Przypisywanie tożsamości usługi Azure AD do serwera 

Jeśli masz istniejący serwer, użyj następującego elementu, aby dodać tożsamość usługi Azure AD do serwera:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

W przypadku tworzenia serwera należy użyć polecenia cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) ze znacznikiem Identity, aby dodać tożsamość usługi Azure AD podczas tworzenia serwera:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Krok 2. Przyznaj Key Vault uprawnienia do serwera

Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , aby udzielić serwerowi dostępu do magazynu kluczy przed użyciem klucza z niego dla TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Krok 3. Dodaj klucz Key Vault do serwera i ustaw funkcję ochrony TDE


- Użyj polecenia cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) , aby pobrać identyfikator klucza z magazynu kluczy
- Użyj polecenia cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) , aby dodać klucz z Key Vault do serwera programu.
- Użyj polecenia cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) , aby ustawić klucz jako funkcję ochrony TDE dla wszystkich zasobów serwera.
- Użyj polecenia cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) , aby upewnić się, że funkcja ochrony TDE została skonfigurowana zgodnie z oczekiwaniami.

> [!Note]
> Łączna długość nazwy magazynu kluczy i nazwy klucza nie może przekraczać 94 znaków.
> 

>[!Tip]
>Przykład KeyId z Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Krok 4. Włącz TDE 

Aby włączyć TDE, należy użyć polecenia cmdlet [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) .

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Teraz baza danych lub magazyn danych ma TDE z kluczem szyfrowania w Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Krok 5. Sprawdź stan szyfrowania i aktywność szyfrowania

Użyj elementu [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) , aby pobrać stan szyfrowania i [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) w celu sprawdzenia postępu szyfrowania bazy danych lub magazynu danych.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Inne przydatne polecenia cmdlet programu PowerShell

- Aby wyłączyć TDE, należy użyć polecenia cmdlet [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) .

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled"
   ```
 
- Użyj polecenia cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) , aby zwrócić listę kluczy Key Vault dodanych do serwera.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Aby usunąć klucz Key Vault z serwera, użyj elementu [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) .

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi problem, sprawdź następujące kwestie:
- Jeśli nie można znaleźć magazynu kluczy, upewnij się, że jesteś w odpowiedniej subskrypcji za pomocą polecenia cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) .

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Jeśli nie można dodać nowego klucza do serwera lub nie można zaktualizować nowego klucza jako funkcji ochrony TDE, sprawdź następujące kwestie:
   - Klucz nie powinien mieć daty wygaśnięcia
   - Klucz musi mieć włączone operacje *Get*, *zawijania*klucza i *dezawijania kluczy* .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak obrócić funkcję ochrony TDE na serwerze, aby zachować zgodność z wymaganiami dotyczącymi zabezpieczeń: [obróć transparent Data Encryption funkcję ochrony przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- W przypadku zagrożenia bezpieczeństwa należy dowiedzieć się, jak usunąć potencjalnie naruszoną ochronę TDE: [Usuń potencjalnie złamany klucz](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia

- Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.
- [Zalecane, ale opcjonalne] Posiadanie sprzętowego modułu zabezpieczeń (HSM) lub lokalnego magazynu kluczy na potrzeby tworzenia lokalnej kopii materiału klucza ochrony TDE.
- Interfejs wiersza polecenia w wersji 2,0 lub nowszej. Aby zainstalować najnowszą wersję i nawiązać połączenie z subskrypcją platformy Azure, zobacz [Instalowanie i Konfigurowanie międzyplatformowego interfejsu wiersza polecenia platformy azure 2,0](https://docs.microsoft.com/cli/azure/install-azure-cli). 
- Utwórz Azure Key Vault i klucz do użycia dla TDE.
  - [Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia 2,0](../key-vault/key-vault-manage-with-cli2.md)
  - [Instrukcje dotyczące korzystania z sprzętowego modułu zabezpieczeń (HSM) i Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Magazyn kluczy musi mieć następującą właściwość, która ma być używana dla TDE:
  - [usuwanie nietrwałe](../key-vault/key-vault-ovw-soft-delete.md)
  - [Jak używać usuwania nietrwałego w usłudze Key Vault z interfejsem wiersza polecenia](../key-vault/key-vault-soft-delete-cli.md) 
- Klucz musi mieć następujące atrybuty, aby można było używać go dla TDE:
   - Brak daty wygaśnięcia
   - Niewyłączone
   - Możliwość wykonania operacji *Get*, *zawijania klucza*, *odpakowania klucza*
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>Krok 1. Tworzenie serwera przy użyciu tożsamości usługi Azure AD
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Nie można utworzyć serwera "principalID", który jest identyfikatorem obiektu używanym do przypisywania uprawnień magazynu kluczy w następnym kroku
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>Krok 2. Przyznaj Key Vault uprawnienia do serwera logicznego SQL
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Zachowaj klucz URI lub keyID nowego klucza dla następnego kroku, na przykład: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Krok 3. Dodaj klucz Key Vault do serwera i ustaw funkcję ochrony TDE
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> Łączna długość nazwy magazynu kluczy i nazwy klucza nie może przekraczać 94 znaków.
> 

  
## <a name="step-4-turn-on-tde"></a>Krok 4. Włącz TDE 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Teraz baza danych lub magazyn danych ma TDE włączony przy użyciu klucza szyfrowania zarządzanego przez klienta w Azure Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Krok 5. Sprawdź stan szyfrowania i aktywność szyfrowania

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>Dokumentacja interfejsu wiersza polecenia SQL

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 


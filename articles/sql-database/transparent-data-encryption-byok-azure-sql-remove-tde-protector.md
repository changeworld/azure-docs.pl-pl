---
title: -Usuń ochrony TDE — program PowerShell usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Przewodnik reagowania na mogą mieć złamane zabezpieczenia ochrony TDE dla usługi Azure SQL Database lub magazynu danych przy użyciu funkcji TDE z obsługą Bring YOur Own Key (BYOK).
keywords: ''
services: sql-database
documentationcenter: ''
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: ''
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: rebeccaz
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: cc52b9ee290ca362c51f7a30cc09056e66df3c55
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719821"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Usuwanie ochrony przezroczystego szyfrowania danych (TDE), przy użyciu programu PowerShell
## <a name="prerequisites"></a>Wymagania wstępne
- Konieczne jest posiadanie subskrypcji platformy Azure oraz mieć uprawnienia administratora na tę subskrypcję
- Konieczne jest posiadanie programu Azure PowerShell w wersji 4.2.0 lub nowszej zainstalowany i uruchomiony. 
- W tym przewodniku przyjęto założenie, że korzystasz już klucza z usługi Azure Key Vault jako funkcja ochrony TDE dla usługi Azure SQL Database lub magazynu danych. Zobacz [funkcji Transparent Data Encryption z obsługą funkcji BYOK](transparent-data-encryption-byok-azure-sql.md) Aby dowiedzieć się więcej.

## <a name="overview"></a>Przegląd
W tym przewodniku opisano sposób reagowania na mogą mieć złamane zabezpieczenia ochrony TDE dla usługi Azure SQL Database lub magazynu danych, który używa funkcji TDE z obsługą Bring Your Own Key (BYOK). Aby dowiedzieć się więcej na temat obsługi funkcji BYOK dla funkcji TDE, zobacz [strony Przegląd](transparent-data-encryption-byok-azure-sql.md). 

Poniższych procedur należy to robić tylko w ekstremalnych przypadkach lub w środowiskach testowych. Należy uważnie przeczytać Przewodnik z instrukcjami dotyczącymi, ponieważ usunięcie aktywnie używanych funkcji TDE funkcje ochrony kluczy z usługi Azure Key Vault może spowodować **utraty danych**. 

Jeśli klucz nigdy nie podejrzewa się być narażone na ataki, takie, że usługa lub użytkownik miał nieautoryzowany dostęp do klucza, najlepiej jest usunąć klucza.

Należy pamiętać, kwotę ochrony TDE zostanie usunięty z usługi Key Vault **wszystkie połączenia z szyfrowanymi bazami danych na tym serwerze są blokowane, a tymi bazami danych przejdą w tryb offline oraz są porzucane w ciągu 24 godzin**. Stare kopie zapasowe szyfrowane za pomocą których bezpieczeństwo zostało naruszone klucza nie są już dostępne.

Ten poradnik przechodzi przez dwie metody w zależności od żądanego wyniku po reagowania na zdarzenia:
- Baz danych Azure SQL / Data Warehouses **dostępne**
- Zapewnienie baz danych Azure SQL / Data Warehouses **niedostępny**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Aby zachować dostępne zaszyfrowanych zasobów
1. Tworzenie [nowy klucz w usłudze Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey?view=azurermps-4.1.0). Upewnij się, że ten nowy klucz jest tworzony w osobnym magazynie kluczy z mogą mieć złamane zabezpieczenia ochrony TDE, ponieważ Kontrola dostępu jest inicjowana na poziomie magazynu. 
2. Dodaj nowy klucz na serwer za pomocą [AzureRmSqlServerKeyVaultKey Dodaj](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) i [polecenia Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) poleceń cmdlet i zaktualizować go jako funkcja ochrony TDE nowego serwera.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>
   
   # Set the key as the TDE protector for all resources under the server
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Sprawdź, czy serwer i wszystkie repliki zostały zaktualizowane do nowej funkcji ochrony TDE przy użyciu [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) polecenia cmdlet. 

   >[!NOTE]
   > Może upłynąć kilka minut, zanim nowe funkcji ochrony TDE zostanie rozpropagowany do wszystkich baz danych i pomocniczych baz danych na tym serwerze.
   >

   ```powershell
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Wykonaj [kopii zapasowej z nowego klucza](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey) w usłudze Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Usuwanie złamany klucz usługi Key Vault przy użyciu [Remove-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey) polecenia cmdlet. 

   ```powershell
   Remove-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Aby przywrócić klucz usługi Key Vault w przyszłości za pomocą [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey) polecenia cmdlet:
   ```powershell
   Restore-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```
 
## <a name="to-make-the-encrypted-resources-inaccessible"></a>Zapewnienie zaszyfrowanych zasobów niedostępny
1. Usuwanie bazy danych zaszyfrowanych za pomocą klucza mogą mieć złamane zabezpieczenia.
Pliki dziennika i bazy danych są automatycznie do kopii zapasowej, dzięki czemu w momencie przywracania bazy danych może odbywać się w dowolnym momencie (tak długo, jak należy podać klucz). Przed usunięciem aktywnego ochrony TDE, aby zapobiec utracie danych z maksymalnie 10 minut najnowszych transakcji, należy porzucić bazy danych. 
2. Utwórz kopię zapasową klucza materiał ochrony TDE w usłudze Key Vault.
3. Usuń klucz mogą mieć złamane zabezpieczenia z usługi Key Vault

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak obrócić ochrony TDE serwera, aby spełnić wymagania dotyczące zabezpieczeń: [Obróć programu PowerShell przy użyciu funkcji ochrony Transparent Data Encryption](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- Rozpoczynanie pracy z usługą Bring Your Own Key pomocy technicznej dla funkcji TDE: [włączyć funkcję TDE, przy użyciu własnego klucza z usługi Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)

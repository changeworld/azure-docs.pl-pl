---
title: PowerShell — Usuń ochronę TDE Azure SQL Database | Microsoft Docs
description: Przewodnik, w którym można odpowiedzieć na potencjalnie złamaną ochronę TDE dla Azure SQL Database lub magazynu danych przy użyciu TDE z obsługą własnego klucza (BYOK).
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
ms.openlocfilehash: dc117dd844a3a47cafa1b37170c95fe852bb82ef
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566057"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Usuwanie funkcji ochrony Transparent Data Encryption (TDE) przy użyciu programu PowerShell

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

- Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji
- Musisz mieć Azure PowerShell zainstalowane i uruchomione. 
- W tym przewodniku założono, że korzystasz już z klucza z Azure Key Vault jako funkcji ochrony TDE dla Azure SQL Database lub magazynu danych. Aby dowiedzieć się więcej, zobacz [transparent Data Encryption z obsługą BYOK](transparent-data-encryption-byok-azure-sql.md) .

## <a name="overview"></a>Omówienie

W tym przewodniku opisano, jak odpowiedzieć na potencjalnie złamaną ochronę TDE dla Azure SQL Database lub magazynu danych korzystającego z TDE z kluczami zarządzanymi przez klienta w ramach obsługi Azure Key Vault Bring Your Own Key (BYOK). Aby dowiedzieć się więcej o obsłudze BYOK dla TDE, zobacz [stronę przegląd](transparent-data-encryption-byok-azure-sql.md). 

Poniższe procedury powinny być wykonywane tylko w sytuacjach skrajnych lub w środowiskach testowych. Uważnie zapoznaj się z przewodnikiem krok po kroku, ponieważ usunięcie aktywnie używanych funkcji ochrony TDE z Azure Key Vault może spowodować **utratę danych**. 

Jeśli klucz jest kiedykolwiek zagrożony, w taki sposób, aby usługa lub użytkownik miał nieautoryzowany dostęp do klucza, najlepszym rozwiązaniem jest usunięcie klucza.

Należy pamiętać, że po usunięciu funkcji ochrony TDE w Key Vault **wszystkie połączenia z zaszyfrowanymi bazami danych na serwerze są blokowane, a te bazy danych przechodzą w tryb offline i zostaną usunięte w ciągu 24 godzin**. Stare kopie zapasowe zaszyfrowane za pomocą klucza złamanego nie są już dostępne.

Poniższe kroki przedstawiają sposób sprawdzania odcisków palca funkcji TDE, które są nadal używane przez wirtualne pliki dzienników (VLF) danej bazy danych. Odcisk palca bieżącej funkcji ochrony TDE bazy danych i identyfikator bazy danych można znaleźć, uruchamiając polecenie: SELECT [database_id],       [encryption_state], [encryptor_type],/*klucz asymetryczny oznacza AKV, certyfikat oznacza klucze zarządzane przez usługę*/[encryptor_thumbprint] z [sys]. [ dm_database_encryption_keys] 
 
Następujące zapytanie zwraca VLFs i szyfrujący odpowiednie odciski palców w użyciu. Każdy inny odcisk palca odwołuje się do innego klucza w Azure Key Vault (AKV): SELECT * FROM sys. DM _db_log_info (database_id) 

Polecenie programu PowerShell Get-AzureRmSqlServerKeyVaultKey udostępnia odcisk palca funkcji ochrony TDE używanej w zapytaniu, dzięki czemu można zobaczyć, które klucze mają być zachowane, oraz klucze, które mają zostać usunięte w AKV. Tylko klucze, które nie są już używane przez bazę danych, można bezpiecznie usunąć z Azure Key Vault.

Ten przewodnik przedstawia dwa podejścia w zależności od pożądanego wyniku po reakcji na zdarzenie:

- Aby zachować **dostęp** do baz danych Azure SQL Database/magazynów z danymi
- Aby uniemożliwić **dostęp** do baz danych Azure SQL Database/magazynów z danymi

## <a name="to-keep-the-encrypted-resources-accessible"></a>Aby zapewnić dostęp do zaszyfrowanych zasobów

1. Utwórz [nowy klucz w Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Upewnij się, że ten nowy klucz jest tworzony w osobnym magazynie kluczy od potencjalnie naruszonej ochrony TDE, ponieważ kontrola dostępu jest inicjowana na poziomie magazynu.
2. Dodaj nowy klucz do serwera za pomocą poleceń cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) i [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) i zaktualizuj go jako nową funkcję ochrony TDE na serwerze.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Upewnij się, że serwer i wszystkie repliki zostały zaktualizowane do nowej funkcji ochrony TDE przy użyciu polecenia cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) . 

   >[!NOTE]
   > Nowe zabezpieczenie TDE można propagować do wszystkich baz danych i pomocniczych baz danych na serwerze za kilka minut.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Utwórz [kopię zapasową nowego klucza](/powershell/module/az.keyvault/backup-azkeyvaultkey) w Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Usuń złamany klucz z Key Vault przy użyciu polecenia cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) . 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Aby przywrócić klucz do Key Vault w przyszłości przy użyciu polecenia cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Aby uniemożliwić dostęp do zaszyfrowanych zasobów

1. Porzuć bazy danych, które są szyfrowane przez potencjalnie złamany klucz.

   Kopie zapasowe bazy danych i plików dziennika są tworzone automatycznie, dzięki czemu można wykonać przywracanie do punktu w czasie w dowolnym momencie (o ile podano klucz). Przed usunięciem aktywnej funkcji ochrony TDE należy porzucić bazy danych, aby zapobiec utracie danych do 10 minut od najnowszych transakcji. 
2. Wykonaj kopię zapasową najważniejszych materiałów ochrony TDE w Key Vault.
3. Usuń potencjalnie złamany klucz z Key Vault

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak obrócić funkcję ochrony TDE na serwerze w celu zachowania zgodności z wymaganiami dotyczącymi zabezpieczeń: [Obróć funkcję ochrony Transparent Data Encryption przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Wprowadzenie do obsługi Bring Your Own Key TDE: [Włącz TDE przy użyciu własnego klucza z Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)

---
title: Usuń osłonę TDE - PowerShell
description: Przewodnik instrukcje dotyczące odpowiadania na potencjalnie naruszone ochrony TDE dla usługi Azure SQL Database lub hurtowni danych przy użyciu TDE z Bring YOur Own Key (BYOK) wsparcie.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 5a89c3f7d52c5717b902a69e9c64b3fcc422c481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067211"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Usuwanie ochrony przezroczystego szyfrowania danych (TDE) przy użyciu programu PowerShell

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji
- Musisz mieć zainstalowany i uruchomiony program Azure PowerShell.
- W tym przewodniku przyjęto założenie, że już używasz klucza z usługi Azure Key Vault jako ochrony TDE dla bazy danych SQL platformy Azure lub magazynu danych. Aby dowiedzieć się więcej, zobacz [Transparent Data Encryption with BYOK Support.](transparent-data-encryption-byok-azure-sql.md)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

 Aby uzyskać instrukcje dotyczące instalacji modułu Az, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps). Aby zapoznać się z określonymi poleceniami cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów co najmniej do grudnia 2020.  Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Przedstawianie nowego modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać instalację, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Omówienie

W tym przewodniku opisano sposób reagowania na potencjalnie naruszone zabezpieczenia TDE dla bazy danych SQL azure lub hurtowni danych, który używa TDE z kluczami zarządzanymi przez klienta w usłudze Azure Key Vault — Bring Your Own Key (BYOK) pomocy technicznej. Aby dowiedzieć się więcej o obsłudze firmy BYOK dla TDE, zobacz [stronę przeglądu](transparent-data-encryption-byok-azure-sql.md).

Poniższe procedury powinny być wykonywane tylko w skrajnych przypadkach lub w środowiskach testowych. Przejrzyj uważnie przewodnik dotyczący sposobu prowadzenia, ponieważ usunięcie aktywnie używanych osłon TDE z usługi Azure Key Vault spowoduje **niedostępność bazy danych.**

Jeśli kiedykolwiek podejrzewa się, że klucz został naruszony, tak że usługa lub użytkownik miał nieautoryzowany dostęp do klucza, najlepiej usunąć klucz.

Należy pamiętać, że po usunięciu ochrony TDE w magazynie kluczy, w ciągu maksymalnie 10 minut wszystkie zaszyfrowane bazy danych zaczną odrzucać wszystkie połączenia z odpowiednim komunikatem o błędzie i zmienić jego stan na [Niedostępny](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector).

W poniższych krokach opisano sposób sprawdzania odcisków palców ochrony TDE nadal używanych przez pliki dziennika wirtualnego (VLF) danej bazy danych.
Odcisk palca bieżącego ochrony TDE bazy danych i identyfikator bazy danych można znaleźć, uruchamiając:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

Następująca kwerenda zwraca VLFs i szyfrator odpowiednich odcisków palców w użyciu. Każdy inny odcisk palca odnosi się do innego klucza w usłudze Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Polecenie Programu PowerShell **Get-AzureRmSqlServerKeyVaultKey** udostępnia odcisk palca ochrony TDE używane w kwerendzie, dzięki czemu można zobaczyć, które klucze do przechowywania i które klucze do usunięcia w AKV. Tylko klucze, które nie są już używane przez bazę danych, można bezpiecznie usunąć z usługi Azure Key Vault.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenie Programu PowerShell **az sql server key show** zapewnia odcisk palca ochrony TDE używane w kwerendzie, dzięki czemu można zobaczyć, które klucze do przechowywania i które klucze do usunięcia w AKV. Tylko klucze, które nie są już używane przez bazę danych, można bezpiecznie usunąć z usługi Azure Key Vault.

* * *

Ten przewodnik instrukcja przechodzi przez dwa podejścia w zależności od pożądanego wyniku po reakcji zdarzenia:

- Aby zapewnić **dostępność** baz danych SQL platformy Azure / magazynów danych
- Aby niedostępne bazy danych SQL platformy Azure / **magazyny** danych

## <a name="to-keep-the-encrypted-resources-accessible"></a>Aby zachować dostęp zaszyfrowanych zasobów

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Utwórz [nowy klucz w przechowalni kluczy](/powershell/module/az.keyvault/add-azkeyvaultkey). Upewnij się, że ten nowy klucz jest tworzony w oddzielnej przechowalni kluczy z potencjalnie naruszone TDE ochrony, ponieważ kontrola dostępu jest aprowizowana na poziomie przechowalni.

2. Dodaj nowy klucz do serwera za pomocą poleceń cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) i [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) i zaktualizuj go jako nowy ochraniacz TDE serwera.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Upewnij się, że serwer i wszystkie repliki zostały zaktualizowane do nowego zabezpieczenia TDE przy użyciu polecenia cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector)

   > [!NOTE]
   > Może upłynąć kilka minut dla nowego ochrony TDE do propagacji do wszystkich baz danych i pomocniczych baz danych na serwerze.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Wykonaj [kopię zapasową nowego klucza](/powershell/module/az.keyvault/backup-azkeyvaultkey) w magazynie kluczy.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Usuń klucz, który został naruszony z usługi Key Vault za pomocą polecenia cmdlet [Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Aby przywrócić klucz do usługi Key Vault w przyszłości za pomocą polecenia cmdlet [Restore-AzKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać informacje o poleceniach, zobacz [keyvault interfejsu wiersza polecenia platformy Azure](/cli/azure/keyvault/key).

1. Utwórz [nowy klucz w przechowalni kluczy](/cli/azure/keyvault/key#az-keyvault-key-create). Upewnij się, że ten nowy klucz jest tworzony w oddzielnej przechowalni kluczy z potencjalnie naruszone TDE ochrony, ponieważ kontrola dostępu jest aprowizowana na poziomie przechowalni.

2. Dodaj nowy klucz do serwera i zaktualizuj go jako nowy obrońca TDE serwera.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Upewnij się, że serwer i wszystkie repliki zostały zaktualizowane do nowego ochrony TDE.

   > [!NOTE]
   > Może upłynąć kilka minut dla nowego ochrony TDE do propagacji do wszystkich baz danych i pomocniczych baz danych na serwerze.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Wykonaj kopię zapasową nowego klucza w magazynie kluczy.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Usuń klucz, który został naruszony z magazynu kluczy.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Aby przywrócić klucz do magazynu kluczy w przyszłości.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Aby szyfrowane zasoby były niedostępne

1. Upuść bazy danych, które są szyfrowane przez klucz potencjalnie naruszone.

   Pliki bazy danych i dziennika są automatycznie archiwizowane, więc przywracanie bazy danych w czasie można wykonać w dowolnym momencie (o ile podasz klucz). Bazy danych muszą zostać usunięte przed usunięciem aktywnego ochrony TDE, aby zapobiec potencjalnej utracie danych do 10 minut najnowszych transakcji.

2. Zabezpiecz kluczowy materiał ochrony TDE w magazynie kluczy.
3. Usuwanie potencjalnie zagrożonego klucza z magazynu kluczy

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak obrócić ochronę TDE serwera w celu spełnienia wymagań dotyczących zabezpieczeń: [Obracanie ochrony transparentnego szyfrowania danych za pomocą programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Wprowadzenie do obsługi funkcji Bring Your Own Key dla TDE: [Włącz TDE przy użyciu własnego klucza z usługi Key Vault za pomocą programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)

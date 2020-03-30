---
title: Obróć ochraniacz TDE - PowerShell
description: Dowiedz się, jak obrócić ochronę przezroczystego szyfrowania danych (TDE) dla serwera SQL platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067165"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Obracanie ochrony przezroczystego szyfrowania danych (TDE) za pomocą programu PowerShell

W tym artykule opisano rotację kluczy dla serwera SQL platformy Azure przy użyciu ochrony TDE z usługi Azure Key Vault. Obracanie ochrony TDE serwera SQL platformy Azure oznacza przełączenie się na nowy klucz asymetryczny, który chroni bazy danych na serwerze. Obrót klucza jest operacją online i powinna potrwać tylko kilka sekund, ponieważ to tylko odszyfrowuje i ponownie szyfruje klucz szyfrowania danych bazy danych, a nie całą bazę danych.

W tym przewodniku omówiono dwie opcje obracania ochrony TDE na serwerze.

> [!NOTE]
> Wstrzymany magazyn danych SQL musi zostać wznowiony przed rotacją kluczy.

> [!IMPORTANT]
> Nie należy usuwać poprzednich wersji klucza po nawróceniu. Gdy klucze są przerzucone, niektóre dane są nadal szyfrowane przy poprzednich kluczach, takich jak starsze kopie zapasowe bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

- W tym przewodniku przyjęto założenie, że już używasz klucza z usługi Azure Key Vault jako ochrony TDE dla bazy danych SQL platformy Azure lub magazynu danych. Zobacz [Przezroczyste szyfrowanie danych z pomocą techniczną BYOK](transparent-data-encryption-byok-azure-sql.md).
- Musisz mieć zainstalowany i uruchomiony program Azure PowerShell.
- [Zalecane, ale opcjonalne] Najpierw utwórz materiał klucza dla ochrony TDE w sprzętowym module zabezpieczeń (HSM) lub lokalnym magazynie kluczy i zaimportuj materiał klucza do usługi Azure Key Vault. Aby dowiedzieć się więcej, postępuj zgodnie z [instrukcjami dotyczącymi korzystania ze sprzętowego modułu zabezpieczeń (HSM) i usługi Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aby uzyskać instrukcje dotyczące instalacji modułu Az, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps). Aby zapoznać się z określonymi poleceniami cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów co najmniej do grudnia 2020.  Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Przedstawianie nowego modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać instalację, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Ręczne obracanie klawiszy

Ręczne obracanie klawiszy używa następujących poleceń, aby dodać zupełnie nowy klucz, który może znajdować się pod nową nazwą klucza lub nawet innym magazynem kluczy. Przy użyciu tej metody obsługuje dodawanie tego samego klucza do różnych magazynów kluczy do obsługi scenariuszy wysokiej dostępności i geo-dr.

> [!NOTE]
> Łączna długość nazwy magazynu kluczy i nazwy klucza nie może przekraczać 94 znaków.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Użyj poleceń cmdlet [Add-AzKeyVaultkey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)i [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj [klawisza az keyvault create](/cli/azure/keyvault/key#az-keyvault-key-create), [az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create)i [az sql server tde-key set.](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set)

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Przydatne polecenia cmdlet programu PowerShell

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

- Aby przełączyć ochronę TDE z trybu BYOK zarządzanego przez firmę Microsoft, należy użyć polecenia cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Aby przełączyć osłonę TDE z trybu BYOK na zarządzane przez firmę Microsoft, należy użyć polecenia cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W poniższych przykładach użyto [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Aby przełączyć ochronę TDE z trybu BYOK zarządzanego przez firmę Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Aby przełączyć ochronę TDE z trybu BYOK na zarządzane przez firmę Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Następne kroki

- W przypadku zagrożenia bezpieczeństwa dowiedz się, jak usunąć potencjalnie naruszoną ochronę TDE: [Usuń potencjalnie naruszony klucz](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Wprowadzenie do integracji usługi Azure Key Vault i obsługi kluczy własnych dla usługi TDE: [włącz funkcję TDE przy użyciu własnego klucza z usługi Key Vault za pomocą programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)

---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 19be449528481b4e35cad4418f82f2250917966b
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
## <a name="prepare-for-akv-integration"></a>Przygotowanie do Integracja
Integracja magazynu kluczy Azure umożliwia skonfigurowanie maszyną Wirtualną programu SQL Server, istnieje kilka wymagań wstępnych: 

1. [Instalowanie programu Azure Powershell](#install)
2. [Tworzenie usługi Azure Active Directory](#register)
3. [Tworzenie magazynu kluczy](#createkeyvault)

W poniższych sekcjach opisano te warunki wstępne i informacje, które należy zebrać później uruchomić polecenia cmdlet programu PowerShell.

### <a id="install"></a> Instalowanie programu Azure PowerShell
Upewnij się, że zainstalowano najnowszy zestaw SDK usługi Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a id="register"></a> Rejestrowanie aplikacji w usłudze Azure Active Directory

Najpierw musisz mieć [usługi Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) w ramach subskrypcji. Między wiele korzyści dzięki temu można udzielić uprawnienia do magazynu kluczy dla niektórych użytkowników i aplikacje.

Następnie należy zarejestrować aplikację przy użyciu usługi AAD. Zapewni to konto nazwy głównej usługi, które ma dostęp do magazynu kluczy, który należy maszyny Wirtualnej. W artykule usługi Azure Key Vault możesz znaleźć następujące kroki w [zarejestrować aplikację w usłudze Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register) sekcji, lub można zobaczyć kroki z zrzuty ekranu w **uzyskać tożsamości dla sekcji aplikacji**  z [ten wpis w blogu](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Przed wykonaniem tych kroków, które należy zebrać następujące informacje w tej rejestracji, który będzie później potrzebny po włączeniu integracji magazynu kluczy Azure na maszynie Wirtualnej SQL.

* Po dodaniu aplikacji Znajdź **identyfikator aplikacji** na **aplikacji zarejestrowanej** bloku.
    Identyfikator aplikacji jest przypisany później do **$spName** parametr (nazwę główną usługi) w skrypt programu PowerShell, aby włączyć integracji magazynu kluczy Azure.

   ![Identyfikator aplikacji](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Podczas tych czynności podczas tworzenia klucza, skopiować klucza tajnego klucza pokazany na poniższym zrzucie ekranu. Przypisano później do tego klucza klucz tajny **$spSecret** parametr (klucz tajny nazwy głównej usługi) w skrypcie programu PowerShell.

   ![Klucz tajny usługi AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Identyfikator i klucz tajny będzie można również używać do tworzenia poświadczeń w programie SQL Server.

* Musisz autoryzować tego nowego Identyfikatora klienta, są następujące uprawnienia dostępu: **szyfrowania**, **odszyfrować**, **wrapKey**, **unwrapKey**, **znak**, i **Sprawdź**. Jest to zrobić za pomocą [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [zezwolić aplikacji na używanie klucza lub klucza tajnego](../articles/key-vault/key-vault-get-started.md#authorize).

### <a id="createkeyvault"></a> Tworzenie magazynu kluczy
Aby można było używać usługi Azure Key Vault do przechowywania kluczy, który będzie używany do szyfrowania maszyny Wirtualnej, potrzebujesz dostępu do magazynu kluczy. Jeśli magazynu kluczy nie zostały już skonfigurowane, utwórz go, wykonując kroki opisane w [wprowadzenie do korzystania z usługi Azure Key Vault](../articles/key-vault/key-vault-get-started.md) artykułu. Przed wykonaniem tych kroków, ma niektórych informacji, które należy zebrać podczas tego zestawu w górę potrzebne później podczas włączania integracji magazynu kluczy Azure na maszynie Wirtualnej SQL.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Po wyświetleniu okna Tworzenie krok magazynu kluczy, należy pamiętać, zwracana **vaultUri** właściwość, która jest adres URL magazynu kluczy. W przykładzie przedstawionym w tym kroku przedstawiony poniżej, nazwa magazynu kluczy jest ContosoKeyVault, w związku z tym magazynu kluczy adres URL może być https://contosokeyvault.vault.azure.net/.

Adres URL magazynu kluczy jest przypisany później do **$akvURL** parametru w skrypt programu PowerShell, aby włączyć integracji magazynu kluczy Azure.

Po utworzeniu magazynu kluczy, należy dodać klucz do magazynu kluczy, będzie ona nazywana tego klucza, gdy utworzymy klucza asymetrycznego później utworzyć w programie SQL Server.

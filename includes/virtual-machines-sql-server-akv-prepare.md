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
ms.openlocfilehash: c1a6a53e6db883c63164a6367012faf32ed75519
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673455"
---
## <a name="prepare-for-akv-integration"></a>Przygotowanie do Integracja
Aby korzystać z integracji magazynu kluczy Azure do skonfigurowania maszyny Wirtualnej programu SQL Server, istnieje kilka wymagań wstępnych: 

1. [Instalowanie programu Azure Powershell](#install)
2. [Tworzenie usługi Azure Active Directory](#register)
3. [Tworzenie magazynu kluczy](#createkeyvault)

W poniższych sekcjach opisano te warunki wstępne i informacje, które należy zebrać, aby później uruchomić poleceń cmdlet programu PowerShell.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a id="install"></a> Instalowanie programu Azure PowerShell
Upewnij się, że zainstalowano najnowszy moduł Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-az-ps).

### <a id="register"></a> Zarejestrować aplikację w usłudze Azure Active Directory

Po pierwsze, musisz mieć [usługi Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) w ramach subskrypcji. Wśród wielu korzyści dzięki temu można udzielić uprawnień do magazynu kluczy dla niektórych użytkowników i aplikacji.

Następnie należy zarejestrować aplikację za pomocą usługi AAD. Zapewni to konto nazwy głównej usługi, które ma dostęp do magazynu kluczy, którego należy maszyna wirtualna. W artykule usługi Azure Key Vault, możesz znaleźć te kroki w [rejestrowanie aplikacji w usłudze Azure Active Directory](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) sekcji, lub można wyświetlić czynności, które ze zrzutami ekranu w **uzyskać tożsamości w sekcji aplikacji**  z [ten wpis w blogu](https://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Przed wykonaniem tych kroków, należy zebrać następujące informacje w trakcie tej rejestracji, który jest potrzebny później, po włączeniu integracji magazynu kluczy Azure na maszynie Wirtualnej programu SQL.

* Po dodaniu aplikacji Znajdź **identyfikator aplikacji** na **zarejestrowana aplikacja** bloku.
    Identyfikator aplikacji jest przypisywany później do **$spName** parametru (główna nazwa usługi) w skrypcie programu PowerShell, aby włączyć integracji magazynu kluczy Azure.

   ![Identyfikator aplikacji](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* W ramach tej procedury podczas tworzenia klucza, skopiować klucza tajnego klucza pokazany na poniższym zrzucie ekranu. Tego klucza wpisu tajnego jest przypisywany później do **$spSecret** parametru (klucz tajny jednostki usługi) w skrypcie programu PowerShell.

   ![Klucz tajny usługi AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Identyfikator i klucz tajny będą również tworzenie poświadczeń w programie SQL Server.

* Musisz autoryzować się tego nowego Identyfikatora klienta musi mieć następujące uprawnienia dostępu: **uzyskać**, **wrapKey**, **unwrapKey**. Jest to zrobić za pomocą [AzKeyVaultAccessPolicy zestaw](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Key Vault](../articles/key-vault/key-vault-overview.md).

### <a id="createkeyvault"></a> Tworzenie magazynu kluczy
Aby można było używać usługi Azure Key Vault do przechowywania kluczy, który będzie używany do szyfrowania maszyny Wirtualnej, potrzebujesz dostępu do magazynu kluczy. Jeśli Twój magazyn kluczy nie mają już skonfigurowany, utworzyć, wykonując czynności opisane w [wprowadzenie do usługi Azure Key Vault](../articles/key-vault/key-vault-overview.md) artykułu. Przed wykonaniem tych kroków, niektóre informacje są potrzebne do zbierania podczas tego zestawu w górę, która jest potrzebna później, po włączeniu integracji magazynu kluczy Azure na maszynie Wirtualnej programu SQL.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Po wyświetleniu okna Tworzenie kroku magazynu kluczy, należy pamiętać, zwrócony **vaultUri** właściwość, która jest adres URL magazynu kluczy. W przykładzie przedstawionym w tym kroku pokazano poniżej, nazwa usługi key vault jest ContosoKeyVault, w związku z tym magazynie kluczy adres URL będzie https://contosokeyvault.vault.azure.net/.

Adres URL magazynu kluczy jest przypisywany później do **$akvURL** parametru w skrypcie programu PowerShell, aby włączyć integracji magazynu kluczy Azure.

Po utworzeniu magazynu kluczy, należy dodać klucz do magazynu kluczy, ten klucz będzie określana podczas tworzenia klucza asymetrycznego później utworzyć w programie SQL Server.

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
ms.openlocfilehash: 2c7d312910c6d38c54b291da34bfb827246c7dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504309"
---
## <a name="prepare-for-akv-integration"></a>Przygotuj się na integrację AKV
Aby skonfigurować maszynę wirtualną programu SQL Server za pomocą usługi Azure Key Vault, istnieje kilka wymagań wstępnych: 

1. [Instalowanie programu Azure Powershell](#install)
2. [Tworzenie usługi Azure Active Directory](#register)
3. [Tworzenie magazynu kluczy](#createkeyvault)

W poniższych sekcjach opisano te wymagania wstępne i informacje potrzebne do zbierania, aby później uruchomić polecenia cmdlet programu PowerShell.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a>Instalowanie programu Azure PowerShell
Upewnij się, że zainstalowano najnowszy moduł programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a>Rejestrowanie aplikacji w usłudze Azure Active Directory

Najpierw musisz mieć [usługę Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) w ramach subskrypcji. Wśród wielu korzyści, to pozwala udzielić uprawnień do magazynu kluczy dla niektórych użytkowników i aplikacji.

Następnie zarejestruj aplikację w urzędzie zgłoszenia. Spowoduje to zapewnienie konta jednostki usługi, który ma dostęp do magazynu kluczy, które maszyny Wirtualnej będzie potrzebne. W artykule Usługi Azure Key Vault można znaleźć te kroki w sekcji [Rejestrowanie aplikacji za pomocą usługi Azure Active Directory](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) lub w sekcji **Uzyskaj tożsamość aplikacji** [w tym wpisie](https://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx)w blogu. Przed wykonaniem tych kroków należy zebrać następujące informacje podczas tej rejestracji, które są potrzebne później po włączeniu integracji usługi Azure Key Vault na maszynie Wirtualnej SQL.

* Po dodaniu aplikacji znajdź **identyfikator aplikacji** (znany również jako AAD ClientID lub AppID) w bloku **zarejestrowana aplikacja.**
    Identyfikator aplikacji jest przypisywany później do parametru **$spName** (nazwa głównej usługi) w skrypcie programu PowerShell, aby włączyć integrację usługi Azure Key Vault.

   ![Identyfikator aplikacji](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Podczas tych kroków podczas tworzenia klucza skopiuj klucz tajny klucza, jak pokazano na poniższym zrzucie ekranu. Ten klucz tajny jest przypisany później do **parametru $spSecret** (Service Principal secret) w skrypcie programu PowerShell.

   ![Tajemnica AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Identyfikator aplikacji i klucz tajny będą również używane do tworzenia poświadczeń w programie SQL Server.

* Aby mieć następujące uprawnienia dostępu, należy autoryzować ten nowy identyfikator aplikacji (lub identyfikator klienta), aby mieć następujące uprawnienia dostępu: **get**, **wrapKey**, **unwrapKey**. Odbywa się to za pomocą polecenia cmdlet [Set-AzKeyVaultAccessPolicy.](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Aby uzyskać więcej informacji, zobacz [omówienie usługi Azure Key Vault](../articles/key-vault/key-vault-overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a>Tworzenie magazynu kluczy
Aby używać usługi Azure Key Vault do przechowywania kluczy, które będą używane do szyfrowania na maszynie Wirtualnej, potrzebujesz dostępu do magazynu kluczy. Jeśli magazyn kluczy nie został jeszcze skonfigurowany, utwórz go, wykonując kroki opisane w artykule [Wprowadzenie do usługi Azure Key Vault.](../articles/key-vault/key-vault-overview.md) Przed wykonaniem tych kroków, istnieje kilka informacji, które należy zebrać podczas tej konfiguracji, która jest potrzebna później po włączeniu integracji usługi Azure Key Vault na maszynie Wirtualnej SQL.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Po dojście do kroku Utwórz magazyn kluczy, zanotuj zwróconą właściwość **vaultUri,** która jest adresem URL magazynu kluczy. W przykładzie podanym w tym kroku, pokazanym poniżej, nazwa magazynu kluczy to https://contosokeyvault.vault.azure.net/ContosoKeyVault, dlatego adres URL magazynu kluczy będzie .

Adres URL magazynu kluczy jest przypisywany później do parametru **$akvURL** w skrypcie programu PowerShell, aby włączyć integrację usługi Azure Key Vault.

Po utworzeniu magazynu kluczy musimy dodać klucz do magazynu kluczy, ten klucz zostanie odsyłany podczas tworzenia asymetrycznego klucza utworzonego w programie SQL Server później.

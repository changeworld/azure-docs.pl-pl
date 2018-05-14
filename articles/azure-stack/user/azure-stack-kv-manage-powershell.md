---
title: Zarządzanie Key Vault w stosie Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać Key Vault w stosie Azure przy użyciu programu PowerShell
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 5e9de401f64a835c286c226bfac88caf5168b96e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Zarządzanie Key Vault w stosie Azure przy użyciu programu PowerShell

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Możesz zarządzać Key Vault w stosie Azure przy użyciu programu PowerShell. Dowiedz się, jak używać poleceń cmdlet programu PowerShell magazynu kluczy:

* Tworzenie magazynu kluczy.
* Przechowywanie i zarządzanie nimi kluczy kryptograficznych i kluczy tajnych.
* Zezwalanie użytkownikom lub aplikacjom wywoływać operacje w magazynie.

>[!NOTE]
>Descibed poleceń cmdlet programu PowerShell magazynu kluczy, w tym artykule są udostępniane w zestawie SDK Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

* Należy zasubskrybować ofertę, która obejmuje usługę Azure Key Vault.
* [Instalowanie programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md).
* [Konfigurowanie środowiska PowerShell użytkownika stosu Azure](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Włącz subskrypcji dzierżawy dla usługi Key Vault operacji

Przed możesz wykonywać żadnych operacji dla magazynu kluczy, należy się upewnić, że subskrypcji dzierżawcy jest włączony dla operacji magazynu. Aby sprawdzić, czy są włączone operacje magazynu, uruchom następujące polecenie:

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Dane wyjściowe**

Jeśli subskrypcja jest włączony dla operacji magazynu, dane wyjściowe zawierają "RegistrationState" jest "zarejestrowano" dla wszystkich typów zasobów magazynu kluczy.

![Stan rejestracji magazynu kluczy](media/azure-stack-kv-manage-powershell/image1.png)

Jeśli operacje magazynu nie są włączone, wywołaj następujące polecenie, aby zarejestrować usługi Key Vault w Twojej subskrypcji:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Dane wyjściowe**

Jeśli rejestracja zakończy się pomyślnie, zwracany jest następujące dane wyjściowe:

![Zarejestruj](media/azure-stack-kv-manage-powershell/image2.png) po wywołaniu polecenia magazyn kluczy, może być wyświetlany komunikat o błędzie, takie jak "subskrypcji nie jest zarejestrowany do korzystania z przestrzeni nazw"Microsoft.KeyVault"." Jeśli wystąpi błąd, upewnij się, że masz [włączone dostawcy zasobów usługi Key Vault](#enable-your-tenant-subscription-for-vault-operations) zgodnie z instrukcjami, które zostały wymienione wcześniej.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Przed utworzeniem magazyn kluczy, Utwórz grupę zasobów, dzięki czemu wszystkie zasoby związane z magazynu kluczy istnieje w grupie zasobów. Aby utworzyć nową grupę zasobów, użyj następującego polecenia:

```PowerShell
New-AzureRmResourceGroup -Name “VaultRG” -Location local -verbose -Force

```

**Dane wyjściowe**

![nową grupę zasobów](media/azure-stack-kv-manage-powershell/image3.png)

Teraz użyj **New-AzureRMKeyVault** polecenie, aby utworzyć magazyn kluczy w grupie zasobów, który został utworzony wcześniej. To polecenie odczytuje trzy obowiązkowe parametry: Nazwa grupy zasobów, nazwa magazynu kluczy i lokalizacji geograficznej.

Uruchom następujące polecenie, aby utworzyć magazyn kluczy:

```PowerShell
New-AzureRmKeyVault -VaultName “Vault01” -ResourceGroupName “VaultRG” -Location local -verbose
```

**Dane wyjściowe**

![Nowy magazyn kluczy](media/azure-stack-kv-manage-powershell/image4.png)

Dane wyjściowe tego polecenia są wyświetlane właściwości magazynu kluczy, który został utworzony. Gdy aplikacja uzyskuje dostęp do tego magazynu, należy użyć **identyfikator URI magazynu** właściwość, która jest "https://vault01.vault.local.azurestack.external" w tym przykładzie.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Wdrażanie usługi Active Directory Federation Services (AD FS)

We wdrożeniu usług AD FS może spowodować, że to ostrzeżenie: "nie ustawiono zasad dostępu. Nie użytkownika lub aplikacji ma uprawnienia dostępu do użycia w tym magazynie." Aby rozwiązać ten problem, należy ustawić zasady dostępu dla magazynu przy użyciu [Set-AzureRmKeyVaultAccessPolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) polecenia:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Zarządzanie kluczy i kluczy tajnych

Po utworzeniu magazynu następujące kroki umożliwiają tworzenie i zarządzanie nimi kluczy i kluczy tajnych w magazynie.

### <a name="create-a-key"></a>Utwórz klucz

Użyj **Add-AzureKeyVaultKey** polecenie, aby utworzyć lub zaimportować klucza chronionego przez oprogramowanie, w magazynie kluczy.

```PowerShell
Add-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01” -verbose -Destination Software
```

**Docelowego** parametr jest używany do określenia, czy dany klucz jest chroniony oprogramowania. Po pomyślnym utworzeniu klucz polecenie wyświetla szczegóły utworzony klucz.

**Dane wyjściowe**

![Nowy klucz](media/azure-stack-kv-manage-powershell/image5.png)

Teraz możesz odwoływać utworzony klucz za pomocą jego identyfikatora URI. Tworzenie lub Importowanie klucza, takiej samej nazwie jak istniejący klucz oryginalny klucz został zaktualizowany o wartości podanych w nowego klucza. Za pomocą identyfikatora URI określonej wersji klucza można uzyskać dostępu do poprzedniej wersji. Na przykład:

* Użyj "https://vault10.vault.local.azurestack.external:443/keys/key01" Aby zawsze uzyskać bieżącą wersję.
* Użyj "https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a" Aby uzyskać tę konkretną wersję.

### <a name="get-a-key"></a>Uzyskać klucz

Użyj **Get-AzureKeyVaultKey** polecenia można odczytać klucza i jego szczegóły.

```PowerShell
Get-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01”
```

### <a name="create-a-secret"></a>Utwórz klucz tajny

Użyj **AzureKeyVaultSecret zestaw** polecenie, aby utworzyć lub zaktualizować klucza tajnego w magazynie. Klucz tajny jest tworzony, jeśli jeszcze nie istnieje. Nowa wersja klucz tajny jest tworzony, jeśli już istnieje.

```PowerShell
$secretvalue = ConvertTo-SecureString “User@123” -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01” -SecretValue $secretvalue
```

**Dane wyjściowe**

![Utwórz klucz tajny](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Pobierz klucz tajny

Użyj **Get-AzureKeyVaultSecret** polecenia można odczytać klucza tajnego w magazynie kluczy. To polecenie może zwrócić wszystkich lub określonych wersji klucza tajnego.

```PowerShell
Get-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01”
```

Po utworzeniu kluczy i kluczy tajnych, można zezwolić aplikacji zewnętrznych z nich korzystać.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Zezwolić aplikacji na używanie klucza lub klucza tajnego

Użyj **Set-AzureRmKeyVaultAccessPolicy** polecenie, aby zezwolić aplikacji na dostęp do klucza lub klucza tajnego w magazynie kluczy.
W poniższym przykładzie nazwa magazynu jest *ContosoKeyVault* i identyfikator klienta aplikacji, którą chcesz autoryzować *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Aby przyznać aplikacji, uruchom następujące polecenie. Opcjonalnie można określić **PermissionsToKeys** parametru można ustawić uprawnień dla użytkownika, aplikacji lub grupy zabezpieczeń.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Jeśli chcesz zezwolić tej samej aplikacji na odczyt kluczy tajnych w magazynie, uruchom następujące polecenie cmdlet:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Kolejne kroki

* [Wdrożenie maszyny Wirtualnej przy użyciu hasła zapisane w magazynie kluczy](azure-stack-kv-deploy-vm-with-secret.md)
* [Wdrożenie maszyny Wirtualnej przy użyciu certyfikatu przechowywane w magazynie kluczy](azure-stack-kv-push-secret-into-vm.md)

---
title: Przewodnik Szybki start platformy Azure — Tworzenie usługi Key Vault w programie PowerShell | Microsoft Docs
description: ''
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/18/2018
ms.author: barclayn
ms.openlocfilehash: e980eafc5bd70a15de11239c7543a7f1dde4ef64
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-an-azure-key-vault-using-powershell"></a>Szybki start: Tworzenie usługi Azure Key Vault przy użyciu programu PowerShell

Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na temat usługi Key Vault, możesz zapoznać się z [omówieniem](key-vault-overview.md). W tym przewodniku Szybki start opisano tworzenie magazynu kluczy przy użyciu programu PowerShell, a następnie umieszczanie wpisu tajnego w nowo utworzonym magazynie.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.1.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

```azurepowershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

```azurepowershell
New-AzureRmResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie utworzysz magazyn kluczy. Podczas wykonywania tego kroku potrzebne będą pewne informacje:

Mimo iż w tym przewodniku Szybki start używany nazwy „Contoso KeyVault2” jako nazwy usługi Key Vault, musisz użyć unikatowej nazwy.

- **Nazwa magazynu**: Contoso Vault2.
- **Nazwa grupy zasobów**: ContosoResourceGroup.
- **Lokalizacja**: Wschodnie stany USA.

```azurepowershell
New-AzureRmKeyVault -VaultName 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Dane wyjściowe tego polecenia cmdlet pokazują właściwości nowo utworzonej usługi Key Vault. Zanotuj dwie poniższe właściwości:

* **Nazwa magazynu**: w tym przykładzie jest to **Contoso-Vault2**. Ta nazwa będzie używana do innych poleceń cmdlet usługi Key Vault.
* **Identyfikator URI magazynu**: w tym przykładzie jest to https://contosokeyvault.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Po utworzeniu magazynu Twoje konto platformy Azure będzie jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

![Dane wyjściowe po ukończeniu wykonywania polecenia tworzenia magazynu kluczy](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać wpis tajny do magazynu, wystarczy tylko wykonać kilka czynności. W tym przypadku dodaj hasło, którego będzie mogła używać aplikacja. Hasło ma nazwę **ExamplePassword** i przechowywana jest w nim wartość **Pa$$w0rd**.

Najpierw przekonwertuj wartość Pa$$w0rd na bezpieczny ciąg, wpisując:

```azurepowershell
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Następnie wpisz poniższe polecenia programu PowerShell, aby utworzyć wpis tajny w usłudze Key Vault o nazwie **ExamplePassword** z wartością **Pa$$w0rd**:

```azurepowershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Aby wyświetlić wartość zawartą we wpisie tajnym jako zwykły tekst:

```azurepowershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Utworzono usługę Key Vault, umieszczono w niej wpis tajny i pobrano go.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

 Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz korzystać z innych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.

Gdy grupa zasobów, usługa Key Vault i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell
Remove-AzureRmResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano tworzenie usługi Key Vault i umieszczanie w niej klucza programowego. Aby dowiedzieć się więcej na temat usługi Key Vault i sposobu jej używania z aplikacjami, przejdź do samouczka dla aplikacji internetowych współdziałających z usługą Key Vault.

> [!div class="nextstepaction"]
> [Używanie usługi Azure Key Vault z poziomu aplikacji internetowej](key-vault-use-from-web-application.md)

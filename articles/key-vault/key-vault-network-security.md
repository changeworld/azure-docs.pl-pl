---
ms.assetid: ''
title: Konfigurowanie zapór usługi Azure Key Vault i sieciami wirtualnymi
description: Instrukcje krok po kroku, aby skonfigurować usługi Key Vault zapory i sieci wirtualne
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: c58fc56742c0a11771bdd84e4195e6f476622a3b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345173"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie zapór usługi Azure Key Vault i sieciami wirtualnymi

W tym przewodniku opisano krok po kroku instrukcje dotyczące konfigurowania usługi Key Vault zapory i sieci wirtualne, aby ograniczyć dostęp do magazynu kluczy. [Usługi punkty końcowe sieci wirtualnej dla usługi Key Vault](key-vault-overview-vnet-service-endpoints.md) pozwalają ograniczyć dostęp do magazynu kluczy do określonej sieci wirtualnej i/lub zestaw zakresów adresów IPv4 (Internet Protocol wersja 4).

> [!IMPORTANT]
> Gdy Zapora i reguły sieci wirtualnej są stosowane, wszystkie usługi Key Vault [płaszczyzny danych](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) operacje mogą być wykonywane, gdy obiekt wywołujący żądania pochodzą z dozwolonych sieci wirtualnych lub zakres adresów IPV4. Dotyczy to również do uzyskiwania dostępu do magazynu kluczy z witryny Azure portal. Gdy użytkownik może przeglądarki do magazynu kluczy z witryny Azure portal, ich może nie móc listę kluczy/wpisów tajnych/certyfikatów Jeśli swoim komputerze klienckim nie ma na liście dozwolonych. Dotyczy to również selektor klucza — magazynu przez inne usługi platformy Azure. Użytkownicy mogą mieć możliwości wyświetlić listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory na swoim komputerze klienckim.

## <a name="azure-portal"></a>Azure Portal

1. Przejdź do magazynu kluczy, które chcesz zabezpieczyć.
2. Kliknij pozycję **zapory i sieci wirtualne**.
3. Kliknij pozycję **wybrane sieci** w obszarze **dostęp z**.
4. Aby dodać istniejące sieci wirtualne zapory i reguł sieci wirtualnej, kliknij przycisk **+ Dodaj istniejące sieci wirtualne**.
5. W nowym bloku, które się pojawi Wybierz subskrypcję, sieci wirtualnych i podsieci, który chcesz zezwolić na dostęp do tego magazynu kluczy. Jeśli sieci wirtualne i podsieci, którą wybierzesz, nie mają włączonych punktów końcowych usługi zostanie wyświetlony komunikat, "następujące sieci nie mają... enavled punkty końcowe usługi". Kliknij przycisk **Włącz** po potwierdzeniu, że chcesz włączyć punkty końcowe usługi wymienione na liście sieci wirtualnych i podsieci. Może upłynąć do 15 minut, aby zastosować zmiany.
6. Możesz również dodawanie nowych sieci wirtualnych i podsieci, a następnie włączyć punktów końcowych usługi dla nowo utworzonej sieci wirtualnych i podsieci, klikając **+ Dodaj nową sieć wirtualną** i monitów.
7. W obszarze **sieci IP**, można dodać zakresy adresów IPv4, wpisując zakresy adresów IPv4 [notacji CIDR (Classless Inter-Domain Routing)](https://tools.ietf.org/html/rfc4632) lub poszczególne adresy IP.
8. Kliknij pozycję **Zapisz**.

## <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure w wersji 2.0

1. [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) i [logowania](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Lista dostępnych sieci wirtualnej reguły, jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
```azurecli
az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
```

3. Włączanie punktu końcowego usługi dla usługi Key Vault w istniejącej sieci wirtualnej i podsieci
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
```

4. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
```

5. Dodaj zakres adresów IP, aby zezwolić na ruch z
```azurecli
az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
```

6. Jeśli tego magazynu kluczy musi być dostępny za pomocą dowolnego z zaufanych usług, ustaw "Obejście" do elementu AzureServices
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. Teraz krok ostateczne i ważne, włącz sieć reguł dalej przez ustawienie akcji domyślnej na "Odmów"
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Program Azure PowerShell

1. Zainstaluj najnowszą wersję [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) i [logowania](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Lista dostępnych sieci wirtualnej reguły, jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. Włączanie punktu końcowego usługi dla usługi Key Vault w istniejącej sieci wirtualnej i podsieci
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. Dodaj zakres adresów IP, aby zezwolić na ruch z
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. Jeśli tego magazynu kluczy musi być dostępny za pomocą dowolnego z zaufanych usług, ustaw "Obejście" do elementu AzureServices
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. Teraz krok ostateczne i ważne, włącz sieć reguł dalej przez ustawienie akcji domyślnej na "Odmów"
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>Informacje

* Poleceń interfejsu wiersza polecenia 2.0 platformy Azure — [reguły sieciowej az keyvault](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Polecenia cmdlet programu PowerShell systemu Azure — [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [AzureRmKeyVaultNetworkRule Dodaj](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [AzureRmKeyVaultNetworkRule Usuń](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [ Aktualizacja AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Kolejne kroki

* [Punkty końcowe usługi sieci wirtualnej dla usługi Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Zabezpieczanie własnego magazynu kluczy](key-vault-secure-your-key-vault.md)
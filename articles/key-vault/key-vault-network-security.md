---
ms.assetid: ''
title: Konfigurowanie zapór usługi Azure Key Vault i sieci wirtualnych — usługi Azure Key Vault
description: Instrukcje krok po kroku, aby skonfigurować usługi Key Vault zapory i sieci wirtualne
services: key-vault
author: amitbapat
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: c54b78a24068758fabb0918cfeb7d6516fd1bce5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487247"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie zapór usługi Azure Key Vault i sieciami wirtualnymi

Ten artykuł zawiera instrukcje krok po kroku, aby skonfigurować usługę Azure Key Vault zapory i sieci wirtualne, aby ograniczyć dostęp do magazynu kluczy. [Punkty końcowe usługi sieci wirtualnej dla usługi Key Vault](key-vault-overview-vnet-service-endpoints.md) umożliwiają ograniczanie dostępu do określonej sieci wirtualnej i zestaw zakresów adresów (internet protocol w wersji 4) IPv4.

> [!IMPORTANT]
> Po reguły zapory są stosowane, użytkownicy usługi Key Vault można wykonać tylko [płaszczyzny danych](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) operacje podczas ich żądania pochodzą z dozwolone sieci wirtualnych lub zakresy adresów IPv4. Dotyczy to również do uzyskiwania dostępu do usługi Key Vault w witrynie Azure portal. Mimo że użytkownicy mogą przeglądać do magazynu kluczy w witrynie Azure portal, ich może nie móc Wyświetl listę kluczy, wpisów tajnych lub certyfikatów Jeśli swoim komputerze klienckim nie ma na liście dozwolonych. Dotyczy to również selektor klucza magazynu przez inne usługi platformy Azure. Użytkowników można wyświetlić listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory na swoim komputerze klienckim.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Poniżej przedstawiono sposób konfigurowania usługi Key Vault zapór i sieci wirtualnych za pomocą witryny Azure portal:

1. Przejdź do magazynu kluczy, które chcesz zabezpieczyć.
2. Wybierz **zapory i sieci wirtualne**.
3. W obszarze **dostęp z**, wybierz opcję **wybrane sieci**.
4. Aby dodać istniejące sieci wirtualne zapory i reguł sieci wirtualnej, wybierz **+ Dodaj istniejące sieci wirtualne**.
5. W nowym bloku, który zostanie otwarty Wybierz subskrypcję, sieci wirtualnych i podsieci, które chcesz zezwolić na dostęp do tego magazynu kluczy. Jeśli sieci wirtualne i podsieci z wybraniu nie mają włączonych punktów końcowych usługi, upewnij się, że chcesz włączyć punkty końcowe usługi, a następnie wybierz pozycję **Włącz**. Może upłynąć do 15 minut, aby zastosować zmiany.
6. W obszarze **sieci IP**, dodać zakresy adresów IPv4, wpisując zakresy adresów IPv4 [notacji CIDR (Classless Inter-Domain Routing)](https://tools.ietf.org/html/rfc4632) lub poszczególne adresy IP.
7. Wybierz pozycję **Zapisz**.

Również dodawanie nowych sieci wirtualnych i podsieci, a następnie włączyć punktów końcowych usługi dla nowo utworzonej sieci wirtualne i podsieci, wybierając **+ Dodaj nową sieć wirtualną**. Następnie postępuj zgodnie z monitami.

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure 

Poniżej przedstawiono sposób konfigurowania usługi Key Vault zapór i sieci wirtualnych przy użyciu wiersza polecenia platformy Azure

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i [Zaloguj](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Lista reguł dostępnych sieci wirtualnej. Jeśli nie zdefiniowano żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Włącz punkt końcowy usługi dla usługi Key Vault w istniejącej sieci wirtualnej lub podsieci.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Dodaj zakres adresów IP, z którego można zezwolić na ruch.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Jeśli ten magazyn kluczy powinna być dostępna dla zaufanych usług, ustaw `bypass` do `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Należy włączyć reguły sieci przez ustawienie domyślne działanie `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniżej przedstawiono sposób konfigurowania usługi Key Vault zapór i sieci wirtualnych przy użyciu programu PowerShell:

1. Zainstaluj najnowszą wersję [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps), i [Zaloguj](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Lista reguł dostępnych sieci wirtualnej. Jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Włącz punkt końcowy usługi dla usługi Key Vault w istniejącej sieci wirtualnej lub podsieci.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Dodaj zakres adresów IP, z którego można zezwolić na ruch.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Jeśli ten magazyn kluczy powinna być dostępna dla zaufanych usług, ustaw `bypass` do `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Należy włączyć reguły sieci przez ustawienie domyślne działanie `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Dokumentacja

* Poleceń interfejsu wiersza polecenia platformy Azure: [reguły sieciowej az keyvault](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Polecenia cmdlet programu PowerShell systemu Azure: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Kolejne kroki

* [Punkty końcowe usługi sieci wirtualnej dla usługi Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Zabezpieczanie własnego magazynu kluczy](key-vault-secure-your-key-vault.md)

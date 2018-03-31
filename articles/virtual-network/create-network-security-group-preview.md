---
title: "Filtrowanie ruchu sieciowego w systemie Azure grup zabezpieczeń sieci i aplikacji (wersja zapoznawcza) | Dokumentacja firmy Microsoft"
description: "Informacje o sposobie tworzenia grup zabezpieczeń sieci i aplikacji (wersja zapoznawcza) w celu ograniczenia typu ruchu sieciowego do i z maszyn wirtualnych."
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ac9a1a8c59a26393d32f9c543e630c302b7ced9d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2018
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>Filtrowanie ruchu sieciowego z grup zabezpieczeń sieci i aplikacji (wersja zapoznawcza)

W tym samouczku informacje o sposobie tworzenia grup zabezpieczeń sieci do filtrowania ruchu sieciowego do i z grupy maszyn wirtualnych z grup zabezpieczeń aplikacji. Aby dowiedzieć się więcej na temat grup zabezpieczeń sieci i grup zabezpieczeń aplikacji, zobacz [Omówienie zabezpieczeń](security-overview.md). 

W następujących sekcjach opisano kroki, które można utworzyć sieciowej grupy zabezpieczeń przy użyciu interfejsu wiersza polecenia platformy Azure ([interfejsu wiersza polecenia Azure](#azure-cli)) i [programu Azure PowerShell](#powershell). Wynik jest taki sam, niezależnie od tego, które narzędzie umożliwia tworzenie grup zabezpieczeń sieci. Kliknij łącze narzędzia, aby przejść do tej części samouczka. 

Ten artykuł zawiera kroki, aby utworzyć sieciowej grupy zabezpieczeń, za pomocą modelu wdrażania usługi Resource Manager, który jest modelem wdrożenia, które firma Microsoft zaleca używanie podczas tworzenia grup zabezpieczeń sieci. Jeśli musisz utworzyć grupę zabezpieczeń sieci (klasyczne), zobacz [Utwórz grupę zabezpieczeń sieci (klasyczne)](virtual-networks-create-nsg-classic-ps.md). Jeśli nie masz doświadczenia w obsłudze modele wdrażania platformy Azure, zobacz [modele wdrażania zrozumieć Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!NOTE]
> W tym samouczku korzysta z funkcji grupy zabezpieczeń sieci, które są obecnie w wersji zapoznawczej. Funkcje w wersji zapoznawczej nie mają tej samej dostępność i niezawodność jako funkcje ogólnie wersji. Aby zaimplementować zabezpieczenia sieci grup ogólnie przy użyciu tylko funkcje wersji, zobacz [Utwórz grupę zabezpieczeń sieci](virtual-networks-create-nsg-arm-pportal.md). 

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Polecenia interfejsu wiersza polecenia platformy Azure są takie same, czy można wykonywać polecenia w systemie Windows, Linux lub macOS. Jednak ma różnic skryptów powłoki systemu operacyjnego. Wykonanie skryptów w poniższych krokach w powłoki Bash. 

1. [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Upewnij się, czy używasz wersji 2.0.18 lub wyższej z wiersza polecenia platformy Azure, wprowadzając `az --version` polecenia. Jeśli nie masz, zainstaluj najnowszą wersję.
3. Logowanie do platformy Azure z `az login` polecenia.
4. Zarejestruj w wersji zapoznawczej, wprowadzając następujące polecenia:
    
    ```azurecli
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    az provider register --namespace Microsoft.Network
    ``` 

5. Upewnij się, że są zarejestrowane dla wersji zapoznawczej, wprowadzając następujące polecenie:

    ```azurecli
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    > [!WARNING]
    > Rejestracja może potrwać do godziny do wykonania. Nie wykonuj pozostałych kroków do *zarejestrowanej* występuje **stanu** w danych wyjściowych zwrócone przez poprzednie polecenie. Jeśli będziesz kontynuować, zanim zostanie zarejestrowany, nie pozostałe kroki.

6. Uruchom poniższy skrypt bash, aby utworzyć grupę zasobów:

    ```azurecli
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. Utwórz trzy grupy zabezpieczeń aplikacji, po jednej dla każdego typu serwera:

    ```azurecli
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. Utwórz grupę zabezpieczeń sieci:

    ```azurecli
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. Utwórz zasady zabezpieczeń w ramach grupy NSG, ustawienia grupy zabezpieczeń aplikacji jako miejsce docelowe:
    
    ```azurecli    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. Utwórz sieć wirtualną: 
    
    ```azurecli
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. Skojarz sieciową grupę zabezpieczeń do podsieci w sieci wirtualnej:

    ```azurecli
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. Utwórz trzy interfejsów sieciowych, po jednej dla każdego typu serwera: 

    ```azurecli
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    Dotyczy tylko odpowiadająca im zasada zabezpieczeń, który został utworzony w kroku 9 interfejsu sieciowego, na podstawie interfejsu sieciowego jest członkiem grupy zabezpieczeń aplikacji. Na przykład tylko *AppRule* reguła ma zastosowanie w przypadku *myNic2*, ponieważ interfejs sieciowy jest elementem członkowskim *AppServers* grupy zabezpieczeń aplikacji i zasady Określa *AppServers* grupy zabezpieczeń aplikacji jako miejsca docelowego. *WebRule* i *DatabaseRule* reguły nie są stosowane do *myNic2*, ponieważ interfejs sieci nie jest elementem członkowskim *serwery sieci Web*i *DatabaseServers* grup zabezpieczeń aplikacji. Zarówno *WebRule* i *AppRule* obowiązują dla *myNic1* , ponieważ *myNic1* interfejsu sieciowego jest elementem członkowskim zarówno *serwery sieci Web* i *AppServers* grup zabezpieczeń aplikacji i zasady określ *serwery sieci Web* i *AppServers* grup zabezpieczeń aplikacji jako ich miejsc docelowych. 

13. Tworzenie maszyn wirtualnych dla każdego typu serwera, dołączanie odpowiedniego interfejsu sieciowego do każdej maszyny wirtualnej. W tym przykładzie tworzy maszyny wirtualne systemu Windows, ale można zmienić *win2016datacenter* do *UbuntuLTS* zamiast tego utworzyć maszyn wirtualnych systemu Linux.

    ```azurecli
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **Opcjonalne**: usunięcie zasobów, utworzonych w tym samouczku, wykonując kroki opisane w [zasoby zostaną usunięte](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Instalowanie i konfigurowanie [PowerShell](/powershell/azure/install-azurerm-ps).
2. Upewnij się, wersja 4.4.0 lub wyższej modułu AzureRm zainstalowane. Sprawdź aktualnie zainstalowanej wersji, wprowadzając `Get-Module -ListAvailable AzureRM` polecenia. Aby zainstalować lub uaktualnić należy zainstalować najnowszą wersję modułu AzureRM z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureRM).
3. W sesji programu PowerShell, zaloguj się do platformy Azure z Twojej [konta Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) przy użyciu `login-azurermaccount` polecenia.
4. Zarejestruj w wersji zapoznawczej, wprowadzając następujące polecenia:
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. Upewnij się, że są zarejestrowane dla wersji zapoznawczej, wprowadzając następujące polecenie:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    > [!WARNING]
    > Rejestracja może potrwać do godziny do wykonania. Nie wykonuj pozostałych kroków do *zarejestrowanej* występuje **RegistrationState** w danych wyjściowych zwrócone przez poprzednie polecenie. Jeśli będziesz kontynuować, zanim zostanie zarejestrowany, nie pozostałe kroki.
        
6. Utwórz grupę zasobów:

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. Utwórz trzy grupy zabezpieczeń aplikacji, po jednej dla każdego typu serwera:

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. Tworzenie reguł zabezpieczeń dla każdego typu serwera.
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80
    
    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443
      
    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336
    ``` 

9. Utwórz grupę zabezpieczeń sieci:

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. Tworzenie konfiguracji podsieci i skojarzyć grupę zabezpieczeń sieci, aby go:
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. Utwórz sieć wirtualną: 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. Utwórz trzy interfejsów sieciowych, po jednej dla każdego typu serwera. 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $databaseAsg
    ```

    Dotyczy tylko odpowiadająca im zasada zabezpieczeń, który został utworzony w kroku 8 interfejsu sieciowego, na podstawie interfejsu sieciowego jest członkiem grupy zabezpieczeń aplikacji. Na przykład tylko *AppRule* reguła ma zastosowanie w przypadku *myNic2*, ponieważ interfejs sieciowy jest elementem członkowskim *AppServers* grupy zabezpieczeń aplikacji i zasady Określa *AppServers* grupy zabezpieczeń aplikacji jako miejsca docelowego. *WebRule* i *DatabaseRule* reguły nie są stosowane do *myNic2*, ponieważ interfejs sieci nie jest elementem członkowskim *serwery sieci Web*i *DatabaseServers* grup zabezpieczeń aplikacji. Zarówno *WebRule* i *AppRule* obowiązują dla *myNic1* , ponieważ *myNic1* interfejsu sieciowego jest elementem członkowskim zarówno *serwery sieci Web* i *AppServers* grup zabezpieczeń aplikacji i zasady określ *serwery sieci Web* i *AppServers* grup zabezpieczeń aplikacji jako ich miejsc docelowych. 

13. Tworzenie maszyn wirtualnych dla każdego typu serwera, dołączanie odpowiedniego interfejsu sieciowego do każdej maszyny wirtualnej. W tym przykładzie tworzy maszyny wirtualne systemu Windows, ale przed wykonaniem skryptu, można zmienić *-Windows* do *- Linux*, *MicrosoftWindowsServer* do *Canonical*, *Windows Server* do *UbuntuServer* i *2016 Datacenter* do *14.04.2-LTS*zamiast tego utworzyć maszyn wirtualnych systemu Linux.

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **Opcjonalne**: usunięcie zasobów, utworzonych w tym samouczku, wykonując kroki opisane w [zasoby zostaną usunięte](#delete-cli).

## <a name="remove-a-nic-from-an-asg"></a>Usuń karty Sieciowej z ASG
Po usunięciu interfejsu sieciowego z grupy zabezpieczeń aplikacji, Brak reguł, które określają grupy zabezpieczeń aplikacji są stosowane do interfejsu sieciowego, który należy usunąć.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby usunąć *myNic3* ze wszystkich grup zabezpieczeń aplikacji, wprowadź następujące polecenie:

```azurecli
az network nic update \
  --name myNic3 \
  --resource-group myResourceGroup \
  --remove ipConfigurations[0].applicationSecurityGroups
```

### <a name="powershell"></a>PowerShell

Aby usunąć *myNic3* ze wszystkich grup zabezpieczeń aplikacji, wprowadź następujące polecenia:

```powershell
$nic=Get-AzureRmNetworkInterface `
  -Name myNic3 `
  -ResourceGroupName myResourceGroup

$nic.IpConfigurations[0].ApplicationSecurityGroups = $null
$nic | Set-AzureRmNetworkInterface 
```

## <a name="delete"></a>Usuwanie zasobów

Po zakończeniu tego samouczka można usunąć zasoby, które zostały utworzone, tak aby nie wiąże się z opłatami użycia. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów, które znajdują się w grupie zasobów.

### <a name="delete-portal"></a>Azure portal

1. W polu wyszukiwania portalu wprowadź **myResourceGroup**. W wynikach wyszukiwania kliknij **myResourceGroup**.
2. Na **myResourceGroup** bloku, kliknij przycisk **usunąć** ikony.
3. Aby potwierdzić decyzję, w **typu nazwa grupy zasobów** wprowadź **myResourceGroup**, a następnie kliknij przycisk **usunąć**.

### <a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

W sesji interfejsu wiersza polecenia wprowadź następujące polecenie:

```azurecli
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>Środowiska PowerShell

W sesji programu PowerShell wpisz następujące polecenie:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```


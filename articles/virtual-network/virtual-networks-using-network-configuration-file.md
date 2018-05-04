---
title: Konfigurowanie sieci wirtualnej platformy Azure (klasyczne) — plik konfiguracji sieci | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i modyfikować sieci wirtualnych (klasyczne) przez eksportowanie, zmienianie i importowanie pliku konfiguracji sieci.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: ed47a5d1449ba634f90e93a82f15daf6e44a553e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Skonfiguruj sieć wirtualną (klasyczne) przy użyciu pliku konfiguracji sieci
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby większości nowych wdrożeń korzystać modelu wdrażania usługi Resource Manager.

Można tworzyć i konfigurować sieć wirtualna (klasyczna) z pliku konfiguracji sieci przy użyciu interfejsu wiersza polecenia platformy Azure (CLI) 1.0 lub Azure PowerShell. Nie można utworzyć lub zmodyfikować sieć wirtualną przy użyciu modelu wdrażania usługi Azure Resource Manager przy użyciu pliku konfiguracji sieci. Nie można użyć portalu Azure do tworzenia lub modyfikowania sieci wirtualnej (klasyczne) przy użyciu pliku konfiguracji sieci, jednak można utworzyć sieć wirtualną (klasyczne), portalu Azure, bez użycia pliku konfiguracji sieci.

Tworzenie i konfigurowanie sieci wirtualnej (klasyczne) przy użyciu pliku konfiguracji sieci wymaga eksportowania, zmienianie i importowanie pliku.

## <a name="export"></a>Eksportowanie plików konfiguracji sieci

Aby wyeksportować plik konfiguracji sieci, można użyć programu PowerShell lub wiersza polecenia platformy Azure. PowerShell eksportuje pliku XML podczas interfejsu wiersza polecenia Azure eksportuje pliku json.

### <a name="powershell"></a>PowerShell
 
1. [Instalowanie programu Azure PowerShell i logowanie do platformy Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Zmień katalog (i upewnij się, istnieje) i nazwę pliku w poniższym poleceniu zgodnie z potrzebami, następnie uruchom polecenie, aby wyeksportować plik konfiguracji sieci:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

1. [Zainstaluj Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wykonaj pozostałe kroki w wierszu polecenia, Azure CLI w wersji 1.0.
2. Logowanie do platformy Azure, wprowadzając `azure login` polecenia.
3. Upewnij się, jest w trybie asm wprowadzając `azure config mode asm` polecenia.
4. Zmień katalog (i upewnij się, istnieje) i nazwę pliku w poniższym poleceniu zgodnie z potrzebami, następnie uruchom polecenie, aby wyeksportować plik konfiguracji sieci:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Utwórz lub zmodyfikuj plik konfiguracji sieci

Plik konfiguracji sieci jest plik XML (w przypadku używania programu PowerShell) lub plik json (w przypadku używania interfejsu wiersza polecenia Azure). Można edytować plik w dowolnym tekstu lub edytora XML/json. [Ustawienia schematu pliku konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100.aspx) artykuł zawiera szczegółowe informacje o wszystkich ustawieniach. Opis dodatkowych ustawień, zobacz [wyświetlić sieci wirtualnych i ustawienia](manage-virtual-network.md#view-virtual-networks-and-settings). Zmiany wprowadzone w pliku:

- Musi być zgodne z schematu lub importowanie niepowodzenia pliku konfiguracji sieci.
- Zastąp wszystkie istniejące ustawienia sieci dla Twojej subskrypcji, dlatego należy zachować wyjątkową ostrożność podczas wprowadzania zmian. Na przykład odwołanie pliki konfiguracyjne przykład sieci, które należy wykonać. Powiedz oryginalny plik zawiera dwa **VirtualNetworkSite** wystąpienia, a uległ zmianie, jak przedstawiono w przykładach. Podczas importowania pliku Azure usuwa sieć wirtualną **VirtualNetworkSite** wystąpienia usunięte w pliku. W tym scenariuszu uproszczony przyjęto zostały żadnych zasobów w sieci wirtualnej, jak w przypadku, nie można usunąć sieci wirtualnej, a import nie powiedzie się.

> [!IMPORTANT]
> Azure uwzględnia podsieć, która ma coś wdrożone w niej jako **używany**. Gdy używany jest podsieci, nie można modyfikować. Przed zmodyfikowaniem informacji o podsieci w pliku konfiguracji sieci, należy przenieść wszystkie elementy, które zostały wdrożone do podsieci do innej podsieci, która nie jest modyfikowana. Zobacz [przenieść do innej podsieci maszyny Wirtualnej lub wystąpienia roli](virtual-networks-move-vm-role-to-subnet.md) szczegółowe informacje.

### <a name="example-xml-for-use-with-powershell"></a>Przykład XML do użycia przy użyciu programu PowerShell

Następujący przykładowy plik konfiguracji sieci tworzy sieć wirtualną o nazwie *myVirtualNetwork* się na przestrzeń adresową z *10.0.0.0/16* w *wschodnie stany USA* Azure region. Sieć wirtualna zawiera jedną podsieć o nazwie *mySubnet* z prefiksem adresu o *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Wyeksportowany plik konfiguracji sieci zawiera zawartość nie, można skopiować kod XML w poprzednim przykładzie i wklej go do nowego pliku.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>Przykład JSON do użytku z interfejsu wiersza polecenia platformy Azure w wersji 1.0

Następujący przykładowy plik konfiguracji sieci tworzy sieć wirtualną o nazwie *myVirtualNetwork* się na przestrzeń adresową z *10.0.0.0/16* w *wschodnie stany USA* Azure region. Sieć wirtualna zawiera jedną podsieć o nazwie *mySubnet* z prefiksem adresu o *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Wyeksportowany plik konfiguracji sieci zawiera zawartość nie, można skopiować dane json w poprzednim przykładzie i wklej go do nowego pliku.

## <a name="import"></a>Importowanie pliku konfiguracji sieci

Aby zaimportować plik konfiguracji sieci, można użyć programu PowerShell lub interfejsu wiersza polecenia Azure. PowerShell importuje plik XML podczas interfejsu wiersza polecenia Azure importuje plik json. Jeśli import zakończy się niepowodzeniem, upewnij się, że plik spełnia [schemat konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Instalowanie programu Azure PowerShell i logowanie do platformy Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Zmień katalog i nazwę pliku w poniższym poleceniu w razie potrzeby, następnie uruchom polecenie, aby zaimportować plik konfiguracji sieci:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

1. [Zainstaluj Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wykonaj pozostałe kroki w wierszu polecenia, Azure CLI w wersji 1.0.
2. Logowanie do platformy Azure, wprowadzając `azure login` polecenia.
3. Upewnij się, jest w trybie asm wprowadzając `azure config mode asm` polecenia.
4. Zmień katalog i nazwę pliku w poniższym poleceniu w razie potrzeby, następnie uruchom polecenie, aby zaimportować plik konfiguracji sieci:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```

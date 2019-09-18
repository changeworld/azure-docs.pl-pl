---
title: Konfigurowanie systemu Azure Virtual Network (klasyczny) — plik konfiguracji sieci | Microsoft Docs
description: Dowiedz się, jak tworzyć i modyfikować sieci wirtualne (klasyczne), eksportując, zmieniając i importując plik konfiguracji sieci.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: ab4faa0f727469e27eb30af54f24036292ec3118
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058697"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Konfigurowanie sieci wirtualnej (klasycznej) przy użyciu pliku konfiguracji sieci
> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby większość nowych wdrożeń korzystała z modelu wdrażania Menedżer zasobów.

Można utworzyć i skonfigurować sieć wirtualną (klasyczną) z plikiem konfiguracji sieci przy użyciu klasycznego interfejsu wiersza polecenia (CLI) platformy Azure lub Azure PowerShell. Nie można utworzyć ani zmodyfikować sieci wirtualnej za pomocą modelu wdrażania Azure Resource Manager przy użyciu pliku konfiguracji sieci. Nie można użyć Azure Portal do tworzenia lub modyfikowania sieci wirtualnej (klasycznej) przy użyciu pliku konfiguracji sieci, jednak można użyć Azure Portal do utworzenia sieci wirtualnej (klasycznej) bez użycia pliku konfiguracji sieci.

Tworzenie i Konfigurowanie sieci wirtualnej (klasycznej) z plikiem konfiguracji sieci wymaga eksportowania, zmiany i importowania pliku.

## <a name="export"></a>Eksportowanie pliku konfiguracji sieci

Aby wyeksportować plik konfiguracji sieci, można użyć programu PowerShell lub klasycznego interfejsu wiersza polecenia platformy Azure. Program PowerShell eksportuje plik XML, podczas gdy klasyczny interfejs wiersza polecenia platformy Azure eksportuje plik JSON.

### <a name="powershell"></a>PowerShell
 
1. [Zainstaluj Azure PowerShell i zaloguj się na platformie Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Zmień katalog (i upewnij się, że istnieje) i nazwę pliku w następującym poleceniu, a następnie uruchom polecenie, aby wyeksportować plik konfiguracji sieci:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

1. [Zainstaluj klasyczny interfejs wiersza polecenia platformy Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wykonaj pozostałe kroki z klasycznego wiersza polecenia CLI.
2. Zaloguj się do platformy Azure, wprowadzając `azure login` polecenie.
3. Upewnij się, że jesteś w trybie ASM, `azure config mode asm` wprowadzając polecenie.
4. Zmień katalog (i upewnij się, że istnieje) i nazwę pliku w następującym poleceniu, a następnie uruchom polecenie, aby wyeksportować plik konfiguracji sieci:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Utwórz lub zmodyfikuj plik konfiguracji sieciowej

Plik konfiguracji sieci to plik XML (w przypadku korzystania z programu PowerShell) lub plik JSON (w przypadku korzystania z klasycznego interfejsu wiersza polecenia). Plik można edytować w dowolnym tekście lub edytorze XML/JSON. Artykuł [Ustawienia schematu pliku konfiguracji sieciowej](https://msdn.microsoft.com/library/azure/jj157100.aspx) zawiera szczegółowe informacje dotyczące wszystkich ustawień. Aby uzyskać dodatkowe wyjaśnienie ustawień, zobacz [Wyświetlanie sieci wirtualnych i ustawień](manage-virtual-network.md#view-virtual-networks-and-settings). Zmiany wprowadzane do pliku:

- Musi być zgodna ze schematem lub importowanie pliku konfiguracji sieci zakończy się niepowodzeniem.
- Zastąp wszystkie istniejące ustawienia sieci dla Twojej subskrypcji, dlatego podczas wprowadzania modyfikacji należy zachować wyjątkową ostrożność. Na przykład odwołują się do przykładowych plików konfiguracji sieci, które obserwują. Załóżmy, że oryginalny plik zawiera dwa wystąpienia **VirtualNetworkSite** i został zmieniony, jak pokazano w przykładach. Podczas importowania pliku platforma Azure usuwa sieć wirtualną dla wystąpienia **VirtualNetworkSite** usuniętego w pliku. Ten uproszczony scenariusz polega na tym, że żadne zasoby nie znajdowały się w sieci wirtualnej, tak jak w przypadku, nie można było usunąć sieci wirtualnej, a Importowanie nie powiedzie się.

> [!IMPORTANT]
> Platforma Azure traktuje podsieć, w której jest wdrożony. Jeśli podsieć jest używana, nie można jej modyfikować. Przed zmodyfikowaniem informacji o podsieci w pliku konfiguracji sieci należy przenieść wszystkie elementy wdrożone w podsieci do innej podsieci, która nie jest modyfikowana. Aby uzyskać szczegółowe informacje [, zobacz Przenoszenie maszyny wirtualnej lub wystąpienia roli do innej podsieci](virtual-networks-move-vm-role-to-subnet.md) .

### <a name="example-xml-for-use-with-powershell"></a>Przykładowy kod XML do użycia z programem PowerShell

Poniższy przykładowy plik konfiguracji sieciowej tworzy sieć wirtualną o nazwie *myVirtualNetwork* z przestrzenią adresową *10.0.0.0/16* w regionie *Wschodnie stany USA* platformy Azure. Sieć wirtualna zawiera jedną podsieć o nazwie Moja podsieć z prefiksem adresu *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

Jeśli wyeksportowany plik konfiguracji sieci nie zawiera zawartości, można skopiować kod XML w poprzednim przykładzie i wkleić go do nowego pliku.

### <a name="example-json-for-use-with-the-classic-cli"></a>Przykładowy kod JSON do użycia z klasycznym interfejsem wiersza polecenia

Poniższy przykładowy plik konfiguracji sieciowej tworzy sieć wirtualną o nazwie *myVirtualNetwork* z przestrzenią adresową *10.0.0.0/16* w regionie *Wschodnie stany USA* platformy Azure. Sieć wirtualna zawiera jedną podsieć o nazwie Moja podsieć z prefiksem adresu *10.0.0.0/24*.

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

Jeśli wyeksportowany plik konfiguracji sieci nie zawiera zawartości, można skopiować kod JSON w poprzednim przykładzie i wkleić go do nowego pliku.

## <a name="import"></a>Importuj plik konfiguracji sieciowej

Do zaimportowania pliku konfiguracji sieci można użyć programu PowerShell lub klasycznego interfejsu wiersza polecenia. Program PowerShell importuje plik XML, podczas gdy klasyczny interfejs wiersza polecenia importuje plik JSON. Jeśli importowanie nie powiedzie się, upewnij się, że plik jest zgodny ze [schematem konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Zainstaluj Azure PowerShell i zaloguj się na platformie Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. W razie potrzeby zmień katalog i nazwę pliku w następującym poleceniu, a następnie uruchom polecenie, aby zaimportować plik konfiguracji sieci:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

1. [Zainstaluj klasyczny interfejs wiersza polecenia platformy Azure](/cli/azure/install-classic-cli). Wykonaj pozostałe kroki z klasycznego wiersza polecenia CLI.
2. Zaloguj się do platformy Azure, wprowadzając `azure login` polecenie.
3. Upewnij się, że jesteś w trybie ASM, `azure config mode asm` wprowadzając polecenie.
4. W razie potrzeby zmień katalog i nazwę pliku w następującym poleceniu, a następnie uruchom polecenie, aby zaimportować plik konfiguracji sieci:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```

---
title: Konfigurowanie usługi Azure Virtual Network (klasyczna) — plik konfiguracji sieci | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i modyfikować sieci wirtualne (klasyczne) eksportowanie, zmieniając i importowanie pliku konfiguracji sieci.
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
ms.openlocfilehash: e26ec4d268b9bd8852ef8cd2c522995902e15923
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108015"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Skonfiguruj sieć wirtualna (klasyczna) przy użyciu pliku konfiguracji sieci
> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca się, że większości nowych wdrożeń korzystać z modelu wdrażania usługi Resource Manager.

Możesz można tworzyć i konfigurować sieci wirtualne (klasyczne) przy użyciu pliku konfiguracji sieci przy użyciu platformy Azure klasyczny interfejs wiersza polecenia (CLI) lub programu Azure PowerShell. Nie można utworzyć lub zmodyfikować sieci wirtualnej za pomocą modelu wdrażania usługi Azure Resource Manager przy użyciu pliku konfiguracji sieci. Nie można użyć witryny Azure portal do tworzenia lub modyfikowania sieć wirtualna (klasyczna) przy użyciu pliku konfiguracji sieci, jednak można użyć witryny Azure portal można utworzyć sieci wirtualnej (wersja klasyczna), bez użycia pliku konfiguracyjnego sieci.

Tworzenie i konfigurowanie sieci wirtualnej (klasycznej) z plikiem konfiguracyjnym sieci wymaga eksportowania, zmieniania i importowania pliku.

## <a name="export"></a>Eksportuj plik konfiguracji sieci

Aby wyeksportować plik konfiguracji sieci, można użyć programu PowerShell lub Azure klasyczny interfejs wiersza polecenia. Środowiska PowerShell eksportuje plik XML, natomiast Azure klasyczny interfejs wiersza polecenia eksportuje plik w formacie json.

### <a name="powershell"></a>PowerShell
 
1. [Zainstaluj program Azure PowerShell i zaloguj się do platformy Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Zmień katalog (i upewnij się, istnieje) i nazwę pliku w następującym poleceniu zgodnie z potrzebami, następnie uruchom polecenie, aby wyeksportować plik konfiguracji sieci:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

1. [Instalowanie platformy Azure klasyczny interfejs wiersza polecenia](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wykonaj pozostałe kroki z klasycznego wiersza polecenia.
2. Zaloguj się do platformy Azure, wprowadzając `azure login` polecenia.
3. Upewnij się, znajdujesz się w trybie asm, wprowadzając `azure config mode asm` polecenia.
4. Zmień katalog (i upewnij się, istnieje) i nazwę pliku w następującym poleceniu zgodnie z potrzebami, następnie uruchom polecenie, aby wyeksportować plik konfiguracji sieci:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Utwórz lub zmodyfikuj plik konfiguracji sieci

Plik konfiguracji sieci jest plik XML (w przypadku używania programu PowerShell) lub plik w formacie json (w przypadku używania klasyczny interfejs wiersza polecenia). Możesz edytować plik w dowolnej tekstu lub edytorze XML/json. [Ustawienia schemat pliku konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100.aspx) artykułu zawiera szczegółowe informacje dla wszystkich ustawień. Aby uzyskać dodatkowe opisy ustawień, zobacz [wyświetlić sieci wirtualnych i ustawienia](manage-virtual-network.md#view-virtual-networks-and-settings). Zmiany wprowadzone do pliku:

- Muszą być zgodne z schemat lub importowania, który plik konfiguracji sieci zakończy się niepowodzeniem.
- Zastąp wszystkie istniejące ustawienia sieci dla Twojej subskrypcji, a więc należy zachować wyjątkową ostrożność podczas wprowadzania zmian. Na przykład odwołanie przykład pliki konfiguracji sieci, które należy wykonać. Powiedz, oryginalnym plik zawiera dwa **VirtualNetworkSite** wystąpień i możesz zmienić, jak pokazano w przykładach. Podczas importowania pliku Azure usuwa sieć wirtualną dla **VirtualNetworkSite** wystąpienia, które zostały usunięte w pliku. W tym scenariuszu uproszczone przyjmuje, nie zasoby były w sieci wirtualnej, jak w przypadku, nie można usunąć sieci wirtualnej i importowania może zakończyć się niepowodzeniem.

> [!IMPORTANT]
> Azure uznaje podsieci, która ma coś wdrożyć go jako **używana**. Jeśli podsieć jest używana, nie można modyfikować. Przed zmodyfikowaniem informacje o podsieci w pliku konfiguracji sieci, przesuń wszystko, co została wdrożona w podsieci do innej podsieci, która nie jest modyfikowana. Zobacz [przenieść maszynę Wirtualną lub wystąpieniem roli do innej podsieci](virtual-networks-move-vm-role-to-subnet.md) Aby uzyskać szczegółowe informacje.

### <a name="example-xml-for-use-with-powershell"></a>Przykładowy kod XML do użycia przy użyciu programu PowerShell

Poniższy przykładowy plik konfiguracji sieci tworzy sieć wirtualną o nazwie *myVirtualNetwork* z przestrzeni adresowej *10.0.0.0/16* w *wschodnie stany USA* platformy Azure region. Sieć wirtualna zawiera jedną podsieć o nazwie *mySubnet* z prefiksem adresu *10.0.0.0/24*.

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

Plik konfiguracji sieci, który został wyeksportowany zawiera zawartość nie, można skopiować kod XML w poprzednim przykładzie i wkleić go do nowego pliku.

### <a name="example-json-for-use-with-the-classic-cli"></a>Przykładowy plik JSON do użytku z programem klasyczny interfejs wiersza polecenia

Poniższy przykładowy plik konfiguracji sieci tworzy sieć wirtualną o nazwie *myVirtualNetwork* z przestrzeni adresowej *10.0.0.0/16* w *wschodnie stany USA* platformy Azure region. Sieć wirtualna zawiera jedną podsieć o nazwie *mySubnet* z prefiksem adresu *10.0.0.0/24*.

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

Plik konfiguracji sieci, który został wyeksportowany zawiera zawartość nie, można skopiować dane json w poprzednim przykładzie i wkleić go do nowego pliku.

## <a name="import"></a>Importuj plik konfiguracji sieci

Aby zaimportować plik konfiguracji sieci, można użyć programu PowerShell lub klasyczny interfejs wiersza polecenia. PowerShell importuje plik XML, gdy klasyczny interfejs wiersza polecenia importuje plik w formacie json. Jeśli import zakończy się niepowodzeniem, upewnij się, że plik spełnia [schemat konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Zainstaluj program Azure PowerShell i zaloguj się do platformy Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Zmień katalog i nazwę pliku, w poniższym poleceniu zgodnie z potrzebami, następnie uruchom polecenie, aby zaimportować plik konfiguracji sieci:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

1. [Instalowanie platformy Azure klasyczny interfejs wiersza polecenia](/cli/azure/install-classic-cli). Wykonaj pozostałe kroki z klasycznego wiersza polecenia.
2. Zaloguj się do platformy Azure, wprowadzając `azure login` polecenia.
3. Upewnij się, znajdujesz się w trybie asm, wprowadzając `azure config mode asm` polecenia.
4. Zmień katalog i nazwę pliku, w poniższym poleceniu zgodnie z potrzebami, następnie uruchom polecenie, aby zaimportować plik konfiguracji sieci:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```

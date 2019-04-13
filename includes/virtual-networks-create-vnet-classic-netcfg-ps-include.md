---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59532782"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Jak utworzyć sieć wirtualną przy użyciu pliku konfiguracji sieci za pomocą programu PowerShell
Platforma Azure używa pliku xml do definiowania wszystkich sieci wirtualnych dostępnych na subskrypcję. Możesz pobrać ten plik, edytuj go, aby zmodyfikować lub usunąć istniejące sieci wirtualne i tworzenie nowych sieci wirtualnych. W tym samouczku, Dowiedz się, jak pobrać ten plik, określanego jako plik konfiguracji (lub netcfg) sieci i edytuj go, aby utworzyć nową sieć wirtualną. Aby dowiedzieć się więcej na temat pliku konfiguracji sieci, zobacz [schemat konfiguracji sieci wirtualnej platformy Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Aby utworzyć sieć wirtualną przy użyciu pliku netcfg przy użyciu programu PowerShell, wykonaj następujące czynności:

1. Jeśli po raz pierwszy używasz programu Azure PowerShell, wykonaj kroki opisane w [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs) artykuł, a następnie zaloguj się do platformy Azure i wybierz swoją subskrypcję.
2. W konsoli programu Azure PowerShell, użyj **Get AzureVnetConfig** polecenia cmdlet, aby pobrać plik konfiguracji sieci do katalogu na komputerze, uruchamiając następujące polecenie: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Oczekiwane dane wyjściowe:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Otwórz plik został zapisany w kroku 2, za pomocą dowolnej aplikacji edytora XML lub tekstu i poszukaj  **\<VirtualNetworkSites >** elementu. Jeśli masz już utworzone sieci, każdą sieć jest wyświetlana jako swój własny  **\<VirtualNetworkSite >** elementu.
4. Aby utworzyć sieć wirtualną, opisane w tym scenariuszu, Dodaj następujący kod XML zaraz pod  **\<VirtualNetworkSites >** elementu:

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. Zapisz plik konfiguracji sieci.
6. W konsoli programu Azure PowerShell, użyj **AzureVnetConfig zestaw** polecenia cmdlet, aby przekazać plik konfiguracji sieci, uruchamiając następujące polecenie: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Zwrócone dane wyjściowe:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Jeśli **OperationStatus** nie *Powodzenie* w dane wyjściowe, sprawdź ponownie plik xml dla błędów i wykonaj krok 6.

7. W konsoli programu Azure PowerShell, użyj **Get AzureVnetSite** polecenia cmdlet, aby sprawdzić, czy nowej sieci został dodany, uruchamiając następujące polecenie: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   Zwrócone dane wyjściowe (skrócona) zawiera następujący tekst:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```

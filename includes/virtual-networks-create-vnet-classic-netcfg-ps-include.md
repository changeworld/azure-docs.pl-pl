---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: a84b31d64f8f9900d342e245ca66cde3c0395be1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Jak utworzyć sieć wirtualną przy użyciu pliku konfiguracji sieci z programu PowerShell
Azure używa pliku xml definiującego wszystkie sieci wirtualne dostępne do subskrypcji. Można pobrać tego pliku, edytować go, aby zmodyfikować lub usunąć istniejące sieci wirtualne i utworzyć nowe sieci wirtualnej. W tym samouczku Dowiedz się, jak pobrać ten plik, określonych w pliku konfiguracji (lub netcfg) sieci i edytowanie go w celu utworzenia nowej sieci wirtualnej. Aby dowiedzieć się więcej na temat pliku konfiguracji sieci, zobacz [schemat konfiguracji sieci wirtualnej platformy Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Aby utworzyć sieć wirtualną przy użyciu pliku netcfg przy użyciu programu PowerShell, wykonaj następujące kroki:

1. Jeśli nie znasz programu Azure PowerShell, wykonaj kroki [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs) artykuł, a następnie zaloguj się usłudze Azure i wyboru subskrypcji.
2. Z poziomu konsoli programu Azure PowerShell, użyj **Get-AzureVnetConfig** polecenia cmdlet, aby pobrać plik konfiguracji sieci do katalogu na komputerze, uruchamiając następujące polecenie: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Oczekiwane dane wyjściowe:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Otwórz plik zapisany w kroku 2, za pomocą dowolnej aplikacji edytora XML lub tekst i poszukaj **<VirtualNetworkSites>** elementu. Jeśli masz już utworzone sieci każda sieć jest wyświetlana jako własnego **<VirtualNetworkSite>** elementu.
4. Aby utworzyć sieć wirtualną opisane w tym scenariuszu, Dodaj następujący kod XML tylko w obszarze **<VirtualNetworkSites>** elementu:

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
6. Z poziomu konsoli programu Azure PowerShell, użyj **AzureVnetConfig zestaw** polecenia cmdlet, aby przekazać plik konfiguracji sieci, uruchamiając następujące polecenie: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Zwrócone dane wyjściowe:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Jeśli **OperationStatus** nie jest *zakończyło się pomyślnie* w dane wyjściowe, sprawdź ponownie plik xml dla błędów i wykonaj krok 6.

7. Z poziomu konsoli programu Azure PowerShell, użyj **Get-AzureVnetSite** polecenia cmdlet, aby sprawdzić, czy nowa sieć został dodany, uruchamiając następujące polecenie: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   Zwrócone dane wyjściowe (skracania) zawiera następujący tekst:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
